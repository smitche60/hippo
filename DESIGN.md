# Hippo — design

Hippo is a personal brain: a small git repo of markdown pages that an AI assistant reads
before answering questions about your life and writes to only when you say so. It borrows
its conventions from Garry Tan's [GBrain](https://github.com/garrytan/gbrain), leaves out
nearly all of GBrain's machinery, and adds a few things GBrain does not have. This document
says what was kept, what was dropped, what was added, and why — so you can change any of it
knowing what it was for.

Hippo assumes an AI assistant that can read and write files in a folder on your machine, run
on a schedule, and load **skills** — short instruction documents that tell it how to file,
dream, and answer. It was built on one such assistant; anything with those three abilities will do. Its rule
file is `AGENTS.md`, per the cross-tool convention; `CLAUDE.md` is a pointer to it. The rules the assistant actually follows live in `AGENTS.md`, `RESOLVER.md`,
`ONTOLOGY.md`, `REDACTOR.md`, and `CONTEXT.md`. Where those point at this document by section name, the
section is here.

## The shape of it

Every page has two layers separated by a horizontal rule. Above the line is **compiled
truth**: a one-paragraph summary, a `## State` section of as-of-now facts, and `## Open
Threads` — loose ends. It is rewritten whenever the facts change. Below the line is the
**timeline**: dated, append-only, newest first, every entry stamped with the id of the
source it came from. To know what is true now, read above the line; to know what happened,
read below it.

Pages live in directories, and the directory is the type. The kit ships seven — decisions,
projects, events, places, products, people, topics — but the list is not this document's to
state: `ONTOLOGY.md` declares each one —
what goes there, what does not, what `status:` values it allows — and `tools/lint.py` reads
that file rather than carrying its own list. Two root pages sit outside the set: `ME.md`, the
page about you, and `VOICE.md`, a profile of how you write.

Content enters through `capture/`, a gitignored staging directory, and passes through the
**redactor** before anything is stored. The **resolver** then routes each fact to exactly one
page. A weekly **dream** run drains capture, sweeps any working folders you have listed in
`WATCHED.md`, refreshes the pages it touched, tidies the board, and writes a report. A
weekday **morning brief** reads the board and the research queue and tells you what needs
you. `TODO.md` is the board: tasks, not pages.

## Borrowed from GBrain

GBrain's page-level conventions are the part of it that works at any scale, and Hippo takes
them nearly verbatim:

- **Two-layer pages** — the `---` separator, State and Open Threads above, the append-only
  timeline below, and the rule that a resolved thread moves to the timeline with its
  resolution rather than being deleted. One departure: GBrain keeps empty sections marked
  "[No data yet]" because the structure is a prompt; Hippo omits empty sections, and lint
  rejects them, because at personal scale a page should be readable at a glance.
- **Read the resolver before you write.** GBrain's phrase is "this is not optional." Hippo's
  `RESOLVER.md` is short enough to read every time.
- **Filename is identity.** Lowercase hyphenated slugs; frontmatter `aliases` for variants;
  before creating any page, search names and aliases, and update if there is a match. The
  merge protocol when a duplicate slips through — pick the more complete page, merge
  timelines chronologically, merge aliases, repoint links — is GBrain's.
- **No catch-all directory.** When nothing fits, the capture stays in `capture/` and the
  dream report nags about it. Persistent residue is the signal to add a type, not to force a
  bad fit.
- **Date-hash ids.** A capture is `YYYY-MM-DD-<hash8>` of its text; every timeline entry
  carries that stamp, which is what makes re-running a filing a no-op.
- **Scheduled runs that are silent when nothing happened,** keep a small state file, and
  commit in batches. GBrain's rule is that noisy crons get disabled; Hippo's dream writes
  no report when nothing happened, and the brief's research watches stay silent unless they
  found something.
- **Brain-first lookup.** For any question about your own life: search the brain first, read
  the page, answer with citations by path, offer to write back what the brain lacked. The
  user's direct statements outrank the brain; the brain outranks the assistant's recollection
  and outside sources.
- **Capped volunteering.** GBrain's push-context guide argues that "push noise never becomes
  worse than pull silence" by confidence-gating and hard-capping what the brain volunteers.
  Hippo's version caps volunteered pages per session and suggested filings per session, and
  never writes silently; `AGENTS.md` carries the numbers.
- **Sourcing labels.** GBrain labels every claim observed / self-described / inferred with a
  confidence. Hippo keeps two: `stated` (you said it) and `inferred` (concluded from
  patterns, a guess until you confirm). Unlabeled means stated.

## Left out, and why

GBrain runs at 146,000 pages with a team. Nearly all of its machinery exists to keep
retrieval and hygiene working at that scale. A personal brain is a few hundred entities at
most, and grep does not miss at a few hundred entities — GBrain's own brain-first protocol
puts keyword search first even with the full stack behind it.

- **No database, embeddings, hybrid search, typed-edge graph, job queue, or daemon.**
  Retrieval is grep plus an assistant reading files. Vector search is deferred until grep
  demonstrably fails on a real question, not adopted in advance. "No daemon" means no
  self-hosted always-on process you have to keep alive; the platform's scheduler runs the
  dream and the brief, and a run that fires while your machine is closed skips and defers.
- **No enrichment.** GBrain pulls from APIs and keeps `.raw/` sidecars for provenance. Hippo
  has no external stream; the only raw material is what you capture.
- **No email, calendar, or social ingestion — ever.** This is a hard constraint, not a
  setting. It rejects GBrain's highest-volume sources in exchange for a privacy boundary and
  a small blast radius. The landing directory is called `capture/` rather than `inbox/` to
  kill even the connotation.
- **No per-directory README resolvers.** GBrain puts a "what goes here / what does not"
  resolver in every directory; at scale, a rule next to the thing it governs is worth the
  duplication. For one person, seven files drift. `ONTOLOGY.md` carries both halves for
  every type in one file, in precedence order; lint enforces the set and the resolver reads
  both halves — GBrain's doctrine without the file layout.
- **No typed taxonomy on top of directories.** The directory is the type. Frontmatter is
  four fields: `type`, `status`, `aliases`, `updated`.
- **No confidence scores.** Two labels are enough; counting interactions is not.
- **No private fences.** GBrain's privacy primitive stores content but never surfaces it.
  Hippo's redactor never stores it. Keeping both would blur the guarantee, and storing is
  the thing being guarded against.
- **No signal detector on every message.** The equivalent is the habit of saying "file this,"
  plus the one suggested filing per session.
- **No salience scoring, backlink maintenance, cross-page timelines, orphan reports, eval
  framework, or team machinery.** Patterns the dream notices go in its report as prose,
  capped at three. Backlinks are a grep. Unlinked pages are normal at this scale.

## Added

Four things Hippo does that GBrain does not, and the choices behind each.

### The redactor, and why its guarantee is never-stored

Everything in `capture/` is raw. Before any of it is written anywhere, each piece is judged
against the table in `REDACTOR.md` — the single source of what may be stored — and one of
four verbs applies: **pass**, **strip** (the content is removed), **abstract** (the fact that
something happened survives; the payload does not), or **flag** (held for your call). Anything
uncertain is not written — the capture is held whole in `capture/.quarantine/`, and the
dream report shows counts by category, never the text.

The guarantee is *never stored*, not *never transits*. The material is conversation history
and the pipeline is an assistant running in the cloud, so the content has already transited;
the promise that can actually be kept is that redacted categories never land in the repo, in
git history, in an archived capture, or in a report. Git history is itself a permanent
archive, which is why redaction precedes the first commit, always, and why `capture/` is
gitignored.

### Decisions as pages

GBrain keeps decisions inside the thing they served — a section on a meeting page. Hippo
gives a decision its own page in `decisions/` with a `status:` of `active`, `superseded`, or
`reversed`, so the reasoning outlives its context. The rule that goes with it is in
`AGENTS.md`: **the owner outranks the brain.** When you change your mind, the page records
that you did, and the assistant cites the decision as reversed instead of arguing the old
case back at you. The entry test keeps it from becoming a diary: a real choice between real
alternatives that you might revisit.

### The ontology as one file

The directory set — with each type's routing test, its negative test, and its allowed
statuses — is declared once, in `ONTOLOGY.md`, in precedence order. `RESOLVER.md` routes
against it and does not restate it; `tools/lint.py` parses it and errors on a page whose
`type:` disagrees with its directory, a `status:` outside the declared values, or a directory
holding pages that the file does not declare. Adding or renaming a type is an edit to that
file plus a `git mv`. This is the seam you customise: a brain for a different context edits
one file and lint follows.

The defaults the kit ships came out of testing candidates against real material. (If you have
changed the set, the list here is the one you inherited, not the one you run — `ONTOLOGY.md`
is always current.) `events`
replaced an earlier `trips`: an event is anything that ends by occurring — a race, a wedding
weekend — and travel was too narrow. `products` replaced `gear`: the test is anything you
buy, own, or maintain, which covers repeat supplies like firewood and furnace filters, not
just equipment. `topics` was added because ongoing interests and lines of thinking had no
home, and two-layer pages serve them unusually well — the current take above the line,
how it evolved below.

### Working folders that feed the brain

`WATCHED.md` lists folders whose churn you want distilled. Every dream sweeps them: new or
changed text files pass through the redactor and are distilled into durable material —
milestones, decisions, preferences, facts about you — with source stamps. Raw working files
are never copied into pages. The principle: **churn flows through the brain; only distillate
settles in it.** A gitignored `.dream-state.json` holds per-file hashes so unchanged files
are skipped; losing it costs one re-sweep, which the stamps make harmless.

## Smaller choices worth knowing about

- **Capture, archive, quarantine.** After a capture is filed, its post-redaction text is kept
  in gitignored `capture/.archive/` for 30 days so a mis-filing can be replayed, then purged.
  Refusals wait raw in `capture/.quarantine/` until you rule, because you cannot override
  content that was already destroyed. Captures that became only a board line are not
  archived — the board line is their record.
- **What the dream checks besides filing.** A new fact that contradicts an existing State
  line is flagged in the report with both visible on the page — you arbitrate, the brain does
  not pick. Open Threads and Waiting For items untouched for 30 days get a nag. On the first
  dream of each month, pages untouched for 90 days are listed with one question each: still
  true, still cared about? Every dream also starts by replaying the last seven days of
  archived captures through the pipeline; the stamps make completed filings no-ops, so a run
  that crashed halfway finishes itself.
- **The board is not memory.** Tasks churn daily; the brain compounds weekly. `TODO.md` is
  current-state only — Inbox / Next Action / Waiting For / Done — with stable `[#N]` ids so
  you can say "do #2." The dream expires Done items after a week, moving meaningful ones to
  page timelines first, and reconciles the board against pages' Open Threads so nothing is
  tracked twice.
- **The morning brief and the research queue.** A weekday run delivers research findings,
  decisions that need you (in the question format `AGENTS.md` describes), the board, and
  anything dated or stale. `RESEARCH.md` is its queue: one-shot items, at most one deep one
  per morning, and recurring watches that stay silent unless they find something.
- **Questions come in a fixed shape.** Numbered, with lettered options and a recommendation on
  its own line, so you can answer "Q1 b" from a phone. The morning brief and the dream report
  use the same shape.
- **`VOICE.md` starts in alpha.** The assistant records observations about how you write but
  does not imitate you until you flip the status — a thin profile imitates badly, and bad
  imitation in your name is worse than none. Only text you actually wrote enters it.
- **Local git, no remote.** Your brain stays on your machine. Scheduled runs need it open;
  they skip silently otherwise, and skipped work is deferred, not lost.
- **Conversation history comes in by curation, not bulk.** There is no API for it; the brain
  grows as you file things. A one-time backfill from a data export is a bounded project you
  run deliberately, picking conversations by hand — a full import would mint pages for things
  you no longer care about. The known risk, accepted: the habit of saying "file this" is the
  system's most likely point of failure. Watched folders exist partly to hedge it.

## This kit never holds content

The repo you are reading is the skeleton: rules, lint, skills, this document, and empty
directories. It holds no page about anyone and never will. Your brain is a **separate** clone
with **no remote**. Git history keeps what you delete, so a brain that was ever pushed with
content in it has published that content; and a kit forked from a live brain would carry the
brain's whole history with it. This kit was assembled fresh for that reason, and any second
instance you stand up should be cloned from it, not from a brain.

## Changing it

A rule lives in exactly one file: behaviour in `AGENTS.md`, routing and page anatomy in
`RESOLVER.md`, types in `ONTOLOGY.md`, redaction in `REDACTOR.md`, vocabulary in
`CONTEXT.md`. Any edit to a rule ends with a search for its key terms across the repo and the
skills, and is done when every hit agrees.

One rule is deliberately stated more than once, because it governs everything else: **the
brain's scope.** The kit ships as a personal brain and says so in five places: `AGENTS.md`'s
opening paragraph (which both names it a personal brain and states the scope), the first line
of `AGENTS.md`'s "Brain-first", `RESOLVER.md`'s step 3 ("the brain stores the owner's life"),
`CONTEXT.md`'s opening paragraph ("Personal scope only"), and `REDACTOR.md`'s row for work
content. If you are adapting this for a different scope — a work brain, a research brain, a
brain for one project — change all five together, or the rules will contradict each other:
the redactor will store what the rulebook says belongs somewhere else. `README.md` and this
document describe the kit rather than govern it, so they can lag; the five above cannot.

`AGENTS.md` says when lint must pass. If you keep a record of your own
decisions about your instance, a folder of short dated notes works well; that is how Hippo's
own were kept before they were distilled into this document.

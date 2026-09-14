# Hippo

A personal brain in markdown and git, run by an AI assistant. Two-layer pages (compiled truth on
top, append-only timeline below), a redaction stage that sees raw input before anything is
stored, a resolver that routes every fact to exactly one page, a board, and a weekly
"dream" that distills the day's captures and watched folders into durable pages. No
database, no embeddings, no daemon — conventions and a linter.

This repo is the **skeleton**: the rules, the design reasoning, the linter, the skills, and
empty directories. It holds no content and never will.

Credit: the conventions are adapted from Garry Tan's GBrain — two-layer pages, a resolver
you read before you write, brain-first lookup, date-hash capture ids, a dream. What Hippo
kept, dropped, and added — and why — is in `DESIGN.md`, along with Hippo's own additions:
decisions as first-class pages, the redactor, the ontology as one file.

## Rule zero

This repo never holds a page about anyone. Your brain is a **separate** clone with **no
remote**. If you push a brain with content in it, every fact in it is on the internet, and
git history keeps what you delete. `DESIGN.md`, "This kit never holds content."

## Starting one

Hand your assistant the prompt in `INSTALL.md`. It clones this skeleton into your folder
with no history and no remote, reads the rules, fills in your page with you, walks you
through the types, installs the three skills, sets up the two scheduled runs, and lints. If
you'd rather do it by hand, the prompt is also the checklist.

The rules say "the owner" throughout — that's you. They are the actual rules of an actual
brain, not an abstraction. `AGENTS.md`'s "Asking the owner" section in particular is how the
author wants to be asked questions; keep it or rewrite it, but keep the idea.

## What's here

- `INSTALL.md` — the prompt that sets this up.
- `AGENTS.md` — behaviour and operating rules. Start here. (`CLAUDE.md` is a pointer to
  it, for assistants that load that filename automatically.)
- `RESOLVER.md` — routing and page anatomy; read before any write.
- `ONTOLOGY.md` — the entity types, in precedence order. The customisation seam.
- `REDACTOR.md` — what may never be stored, and what happens to it instead.
- `CONTEXT.md` — the glossary.
- `WATCHED.md`, `RESEARCH.md`, `TODO.md` — watched folders, the research queue, the board.
- `ME.md`, `VOICE.md` — the owner's page and voice profile, empty.
- `tools/lint.py` — strict lint; the gate on dream commits and multi-page filings.
- `skills/` — the three skills: `brain-file`, `brain-dream`, `brain-ask`.
- `DESIGN.md` — what Hippo borrows from GBrain, leaves out, and adds, and the reasoning
  behind every choice you might want to change. Read it second.
- `capture/` is deliberately absent; it's gitignored and gets created by the first filing.

## License

MIT — see `LICENSE`. Use it however you like, keep the notice, and nothing here comes with
a warranty.

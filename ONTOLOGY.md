# ONTOLOGY — the entity types this brain holds

The single source of the directory set. RESOLVER.md routes against the tests below;
`tools/lint.py` reads this file for the directory list, the expected `type:` value, and any
allowed `status:` values. Nothing else states the set — change it here and the rest follows.

**Order is precedence.** RESOLVER works the blocks top to bottom and the first test that
passes claims the content.

A directory block is a `##` heading naming the directory, followed by its fields, each on
one line in the shape `` - **field:** value ``. `type:`
and `test:` are required; `status:`, `entry:` and `not:` are optional. `not:` names the nearest
neighbours and the boundary — the half of a resolver that says what does not go here.

## decisions
- **type:** decision
- **status:** active | superseded | reversed
- **test:** The owner chose between real alternatives, and their future self might re-litigate it. ("Went with the second bid", "not renewing the gym" — yes. "Picked the closer trailhead" — no; that's at most a timeline line elsewhere.)
- **not:** A choice with no real alternative, or one nobody would revisit — that is a timeline line on the page it affected. A plan or a build is a project, even when it embeds choices.

## projects
- **type:** project
- **status:** active | complete | abandoned
- **test:** Ends by producing an outcome — a plan shipped, a renovation finished, a repair completed. A finished one keeps its page with `status: complete`.
- **not:** Something with no end — a topic. An occasion that ends by happening — an event. The thing a project produced, if it is kept and maintained afterwards — a product, with the job's own page marked complete.

## events
- **type:** event
- **status:** active | archived
- **test:** Ends by occurring: a date range the owner plans, attends, and remembers as a unit (trip, race, wedding weekend).
- **not:** The location it happened at — a place. The ongoing interest it belongs to — a topic. A recurring thing with no end date is not an event.

## places
- **type:** place
- **status:** active | archived
- **test:** A physical location that accrues visits or events across time (restaurant, town, birding spot). The owner's own house lives here as the anchor for structural facts, with individual jobs on their own project pages.
- **not:** A single occasion at a location — an event. Work done on the house — a project. A place mentioned once lives as a timeline line elsewhere, not a page.

## products
- **type:** product
- **status:** active | archived
- **test:** A thing the owner buys, owns, or maintains — equipment and repeat supplies alike (bike, furnace filter, firewood, chimney). A job that merely produced a thing is a project, not a product.
- **not:** The job that bought, built, or installed it — a project. A category of interest rather than a thing owned — a topic. Something sold or discarded keeps its page, marked archived.

## people
- **type:** person
- **status:** active | archived
- **entry:** A page when there is something to say about the person beyond their relationship to the owner and the job they turned up in — their own facts, preferences, history, threads. Until then they live as text in other pages' timelines, however often they appear: frequency alone never earns a page, and neither does being family. The owner's call overrides the test in both directions.
- **test:** A named human who clears the entry criteria above.
- **not:** Anyone who fails the entry criteria — a timeline line on the page they appeared in. A vendor known only by one transaction — the project page carries them.

## topics
- **type:** topic
- **status:** active | archived
- **test:** An ongoing interest or line of thinking with no end date (AI platform shifts, birdwatching, movie taste). Media taste is one page per medium (topics/movies.md), never per-title pages.
- **not:** Anything with an end date — a project or an event. A single fact or opinion — a State line on the page it belongs to. Media taste per title — never; one page per medium.

# Root pages

Single pages outside the directory set, linted like any other page but exempt from the
directory/type match. lint reads this section: every list item must be exactly
`` - `FILE.md` — type `value` — status `a | b` `` (backticked filename, em dash, the word type,
backticked value, em dash, the word status, backticked allowed values). All three parts are
required. Directory names and type values are lowercase, and may contain digits, hyphens and
underscores. Both this section and
the next must exist; lint errors if either heading is missing. A list item here in any other shape is a lint error, so a reworded line can't
silently drop a page from linting.

- `ME.md` — type `me` — status `active | archived`
- `VOICE.md` — type `voice` — status `alpha | active`

# Non-content directories

Not part of the ontology; never linted as pages. lint reads this section too: every list
item must start `` - `name/` `` (backticked directory name with trailing slash). A
directory holding pages that appears in neither section is a lint error.

- `capture/` — pre-redaction landing pad, gitignored
- `reports/` — dream reports, research digests, audits
- `docs/` — your own notes about the system, if you keep any
- `tools/` — lint and any other scripts
- `skills/` — the assistant's skills, when a kit ships them

# How to change this

Adding, removing, or renaming a type is an edit to this file plus a `git mv` of the
directory: RESOLVER.md points here rather than restating the set, and lint.py derives its
list from here. Three things inside a rename are easy to miss — the block's own `type:`
value, the singular name where other blocks' `not:` lines cross-reference it, and any prose
elsewhere that lists the set. Lint catches a wrong `type:` only once a page of that type
exists, so on an empty brain, check it by eye or write a throwaway page and lint that. Write down why — the set is a design
decision, and the reasons matter more than the list.

No catch-all directory. What fits nowhere stays in `capture/`, and persistent residue is
the signal to add a type rather than to force a bad fit.

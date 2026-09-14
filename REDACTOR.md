# REDACTOR — rules for what the brain may store

The sole stage that sees raw input. Stateless: each capture judged on its own text.
Guarantee (DESIGN.md “The redactor”): stripped content is never stored — not in pages, git history, the
archive, or reports. This table is the single source of the rules; loosening or tightening
it is an edit here, nothing more.

## Verbs

- **pass** — store as-is.
- **strip** — remove the content; what remains is written without it.
- **abstract** — keep that the thing happened, drop the payload ("caught up with J —
  she shared some personal news" survives; the news does not).
- **flag** — hold for the owner's call before storing.

## Categories

| Content | Verb |
|---|---|
| Healthcare — anyone's (conditions, diagnoses, meds, appointments' reasons) | strip |
| Credentials & secrets (passwords, keys, codes) | strip |
| Government identifiers (SSN, passport, license numbers) | strip |
| Financial account details (account/routing numbers, logins) | strip |
| Home-security details (alarm codes, key locations) | strip |
| Legal matters (anything the owner would want privileged) | strip |
| Third-party confidences ("X told me privately…") | abstract |
| Other people's private situations (a friend's divorce, a neighbor's money trouble) | abstract |
| The owner's income, comp, and financial-planning content | pass |
| Financial facts (prices paid, costs, budgets) | pass |
| Work content | flag — held for the owner's call; belongs in a separate instance by default |

## Procedure

1. Judge each distinct piece of a capture against the table; apply the matched verb.
2. Uncertain whether a category applies → fail closed: the piece is not written. A capture
   with any uncertain piece is held whole in `capture/.quarantine/` (clean pieces wait with
   it) and counted. A watched-source file is never moved or copied — an uncertain piece
   there is simply skipped, listed in the report by folder and category only — the exact
   filename is told to the owner in conversation, never written to tracked files when the
   category is sensitive.
3. Flags likewise wait in `capture/.quarantine/` for the owner.
4. Report only category counts — never the redacted text.

## Quarantine

`capture/.quarantine/` holds raw refusals and flags until the owner rules (gitignored, like all
of capture/). Every dream report shows quarantine counts by category — counts only in
anything *stored*. When asking the owner to rule, show them the raw text in conversation: a
ruling requires seeing the item, and conversation display is not storage. Their override —
"store it" — is deliberate and final: re-run the item with the override noted. Rising counts in one category mean this table is too conservative there;
the fix is a one-line edit above.

# Installing Hippo

Hippo is set up by an AI assistant, not by hand. Give the prompt below to whatever you use —
Claude Cowork, Claude Code, Codex, or any harness that can read and write files in a folder
on your machine, run on a schedule, and load skill files. Paste it as your first message in a
fresh session that has your chosen folder connected.

Two things to decide before you paste: **which folder** on your machine the brain will live
in (it must be one the assistant can reach), and **what to call it** — the prompt says
`hippo`; change it if you like.

---

```
I want to set up Hippo, a personal brain in markdown and git, from the skeleton at
https://github.com/smitche60/hippo. Work through these steps in order. Stop and ask me
whenever a step needs a decision from me; never guess at facts about my life.

1. Get the skeleton. Clone the repo into my connected folder as a new folder named `hippo`.
   Delete the clone's `.git` directory and run `git init` inside the folder, so my brain
   starts with no history and no remote. Never add a remote to this repo — it stays on this
   machine. Check that `git config user.name` and `user.email` are set; if not, ask me for
   them and set them locally in this repo.

2. Read before touching anything: `README.md`, then `DESIGN.md`, then the five rule files —
   `AGENTS.md`, `RESOLVER.md`, `ONTOLOGY.md`, `REDACTOR.md`, `CONTEXT.md` — in full. From
   now on, those rules govern how you work in this folder. In particular: every write to the
   brain is explicit (I asked, or I accepted a suggestion), and every question you ask me
   uses the format in AGENTS.md's "Asking the owner" section.

3. Fill in `ME.md` with me. Ask me, a few at a time, for the facts that have no other page
   to live on: where I am, who I live with, what I do, what I'm working toward, how I like
   to work with you. Write only what I tell you, label anything you inferred as `inferred`,
   and omit any State section that would be empty.

4. Walk me through `ONTOLOGY.md`. Read me the seven default types and their tests, one at a
   time, and ask whether each fits my life. If I want a type added, removed, or renamed:
   edit that one file, `git mv` the directory, and ask me where a new type belongs in the
   order — the order is routing precedence, and the first test that passes wins.

   A rename has four parts and three are easy to miss. Change the `##` heading; change the
   block's own `type:` value to the new singular; then grep the repo for BOTH the old plural
   and the old singular and fix every hit — other blocks' `not:` lines cross-reference types
   in the singular, and `DESIGN.md` lists the defaults in two places. Verify by writing a
   throwaway page in the renamed directory and linting it, then delete it: `lint.py` cannot
   catch a wrong `type:` on an empty brain, because there are no pages of that type yet, so
   a clean lint proves nothing here. Finally, write down why we changed it — make `docs/`
   and put a short dated note there.

5. Ask me which working folders, if any, I want the weekly dream to sweep, and put them in
   `WATCHED.md`, replacing the example line. If none, delete the example line.

6. Install the three skills in `skills/` — `brain-file`, `brain-dream`, `brain-ask`. First
   edit step 1 of each to the real path of this brain. Then put them where this harness
   looks for skills: for Claude Code that is `~/.claude/skills/<name>/SKILL.md`; for Claude
   Cowork, skills are saved to my account, so hand me each one and tell me to save it; for
   anything else, find the equivalent and tell me what you did. Verify: where the harness loads skills from disk, invoke `brain-file` and see it resolve;
   where skills are saved to an account mid-session, confirm I saved all three and read the
   files back instead — a skill saved now may not load until my next session. If this harness has no skills mechanism at all, say
   so plainly and leave them in `skills/` — then you read the relevant file yourself before
   filing, dreaming, or answering, and step 7's task prompts must point at
   `skills/brain-dream/SKILL.md` by path rather than naming a skill.

7. If this harness can run scheduled tasks, set up two: a weekly dream that runs
   `brain-dream` against this folder (AGENTS.md's "Dream duties" is its checklist), and a
   weekday morning brief per AGENTS.md's "Morning brief duties". Ask me what day and time
   for each, and in what timezone — suggest Sunday evening for the dream and 7am on weekdays
   for the brief. Both must skip silently when this machine is unreachable. If it can't
   schedule, tell me and I'll run them by hand.

8. `VOICE.md` still ships with instructions to the installer as its content — replace that
   prose with a one-line summary saying nothing is recorded yet. (`ME.md` and `WATCHED.md`
   were handled in steps 3 and 5.) Never leave an empty `##` section on a page; lint
   rejects it.

9. Run `python3 tools/lint.py`. It must pass. Then commit everything with the message `init: hippo`.

10. Show me, in three short examples, how to file something ("file this: …") and how to ask
    something the brain knows, then describe what the weekly dream will do — it has nothing
    to drain yet, so there is nothing to demonstrate. Then stop.
```

---

After setup, the three things you'll say most are **"file this"**, a question about your own
life, and **"run the dream"**. Everything else is in `AGENTS.md`.

---
name: release-setup
description: >-
  Put a project on the debrief → release → follow-up → announce process. Interviews the user about
  how the project ships, what a release may do on production without asking, where it can test, and
  where announcements go; then writes RELEASING.md, NEXT-RELEASE.md and NEXT-ANNOUNCEMENT.md, adds
  the mandatory debrief rule to AGENTS.md / CLAUDE.md, and retires project-local copies of the
  skills. Reach for this when `/debrief` or `/release` finds no RELEASING.md, or when asked to set
  a project up for releases and announcements. Also re-run it to revise an existing RELEASING.md.
---
<Args>$ARGUMENTS</Args>

# Set a project up for releases

The four process skills (`debrief`, `release`, `follow-up`, `announce`) carry no project facts.
Everything specific — how the project ships, how far an agent may go on production, where it may
test, where announcements go — lives in one file in the repository root, `RELEASING.md`. This
skill writes it, together with the two worksheets and the rule that makes agents debrief.

Templates for every file are in this skill's `templates/` directory.

## 1. Learn the project before asking

Answer as much as you can yourself; the user is the last resort.

- `AGENTS.md`, `CLAUDE.md`, any playbooks or local skills about shipping, deploying or releasing
  (`.claude/skills/`, `.agents/skills/`, `.ai/`, `docs/`). Existing local `debrief` / `release` /
  `announce` skills are the richest source — the project-specific half of them becomes
  `RELEASING.md`.
- CI config: what a push to which branch does, whether tags trigger anything, what deploys.
- `git ls-remote --tags origin` — versioned or not.
- Existing `NEXT-RELEASE.md` / `NEXT-ANNOUNCEMENT.md` and their archives.
- Past announcements in the channel, if the user names one — they show the form and language.

## 2. Interview for what the code cannot say

One question at a time, each with your recommendation and what you already found (the
`interviewer` skill's discipline). Cover, skipping whatever you already know:

1. **Versioning** — semver tags or none? (None: releases are named by date and cut commit.)
2. **Shipping** — what exactly delivers a release (a push, a tag, a pin bump elsewhere), its gates,
   how to prove production runs it, what makes a rollback unsafe.
3. **Pushing** — may debrief/bookkeeping commits be pushed right away, or would a push deploy?
4. **Autonomy on production** — the central question. What may an agent do on production without
   asking during verification? Push for concrete lines: which systems, which accounts or servers are
   test objects that may be changed freely, what counts as harming a user, what must never be
   touched (real money, legal records like a ledger, other users' things), which events must be
   observed instead of provoked. A vague answer here produces exactly the hesitant agent this file
   exists to prevent — keep asking until every line is something an agent can apply literally.
5. **Test surfaces** — the logged-in browser session, test channels in Slack/Discord (links), test
   accounts, test servers or workbenches.
6. **Debrief rules** — plans, companion repos, version bumps, generated files that must stay current.
7. **Announcements** — platform and channel (link), form, language, the style spec under
   `~/communication-styles/` (if none fits, note it and draft from the channel's recent posts until
   one exists), whose account posts.

## 3. Write the files

- **`RELEASING.md`** from `templates/RELEASING.md`. Keep the section headings. Write the shipping
  procedure as concrete steps with complete commands (contexts, namespaces, ids), so a release agent
  can run it without guessing. Move the project-specific half of any old local release skill here
  rather than paraphrasing it away — its hard-won warnings are the valuable part.
- **`NEXT-RELEASE.md`** from `templates/NEXT-RELEASE.md` — for a versioned project, set the target
  header to the last release plus a patch. If one already exists, keep its entries and bring its
  header and sections to the template's shape (`## Themes` included).
- **`NEXT-ANNOUNCEMENT.md`** from `templates/NEXT-ANNOUNCEMENT.md`, with the language filled in. Keep
  existing entries.
- **The release archive**: existing archived worksheets get frontmatter — `status: closed` if every
  box is ticked, `status: open` if any is not (and the boxes stay where they are).
- **Open work that lives elsewhere** (notes, memory, tickets listing post-deploy steps nobody did):
  if the user agrees, collect it into one release file `status: open`, named for today, one entry
  per item with its theme, so `/follow-up` works it.
- **`AGENTS.md`** (or whichever file the project's agents read first): add
  `templates/agents-section.md`, and rewrite any existing rule that contradicts it — "push when
  done", "always deploy after a change" — rather than leaving two rules. If `CLAUDE.md` only imports
  `AGENTS.md`, that is enough.
- **Retire local copies**: delete project-local `debrief` / `release` / `announce` / `follow-up`
  skills (and their mirrors) once their facts are in `RELEASING.md`; a local copy shadows the global
  one. Update references to them.

## 4. Check and hand over

- Read `RELEASING.md` once as the release agent would: could you run the shipping procedure from it
  alone? Is every autonomy line applicable without a question?
- Run the project's own checks if the files are linted (a docs or plans lint).
- Commit with an explicit pathspec, pushed only if the answer to question 3 allows it.
- Report what you wrote, what you moved out of old skills, what you decided yourself, and anything
  left for the user (a style spec still to write, a test account still to create).

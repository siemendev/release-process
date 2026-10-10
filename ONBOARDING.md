# Onboarding a project

Instructions for an agent putting a project on the release process. The user starts this with the
onboarding prompt from the README; you are now reading what that prompt pointed you at. Work
through the steps in order. Talk to the user in their language; everything you write into files is
English unless they say otherwise.

The four skills (`debrief`, `release`, `follow-up`, `announce`) carry no project facts. Everything
specific — how the project ships, how far an agent may go on production, where it may test, where
announcements go — lives in one file in the project's root, `RELEASING.md`. This onboarding installs
the skills if they are missing and writes that file, the releases directory and the rule that makes
agents debrief.

Every change gets its own debrief file in `<releases>/next/`, written on its feature branch and
merged with its code, so any number of people can debrief in parallel without conflicts. A release
moves those files into its own directory, `<releases>/<release name>/`, next to a `RELEASE.md` that
carries its status.

## 1. Install the skills (once per machine)

Check whether the skills are already installed: look for `debrief`, `release`, `follow-up` and
`announce` in the skill directories your harness reads (Claude Code: `~/.claude/skills/`; Codex:
`~/.agents/skills/`). If all four are there and point into a clone of this repository, skip to
step 2.

Otherwise:

1. Ask where the clone should live (suggest `~/Documents/private/release-process` or wherever the
   user keeps repositories) and clone it:
   `git clone git@github.com:siemendev/release-process.git <path>` — or pull it if it exists.
2. Link each directory under `<path>/skills/` into every skill directory a harness on this machine
   reads. Symlink, do not copy — a `git pull` in the clone then updates every project at once. If a
   skill directory is itself a symlink to another (e.g. `~/.agents/skills` → `~/.claude/skills`),
   one link is enough.
3. **Name clashes**: if a skill of the same name already exists globally and is not ours, stop and
   ask — never overwrite it. Project-local copies (`<project>/.claude/skills/debrief` …) are handled
   in step 4; they shadow the global ones, so they must go.

The templates for step 4 are in `<path>/templates/`.

## 2. Learn the project before asking

Answer as much as you can yourself; the user is the last resort.

- `AGENTS.md`, `CLAUDE.md`, playbooks or local skills about shipping, deploying or releasing
  (`.claude/skills/`, `.agents/skills/`, `.ai/`, `docs/`). Existing local `debrief` / `release` /
  `announce` skills are the richest source — their project-specific half becomes `RELEASING.md`.
- CI config: what a push to which branch does, whether tags trigger anything, what deploys.
- `git ls-remote --tags origin` — versioned or not.
- An existing worksheet or release log (`NEXT-RELEASE.md`, a changelog, past release notes) and
  its archives; a public changelog or release-notes page, if any.
- Past announcements in the channel, if the user names one — they show the form and language.
- Where the project keeps deployment-specific values (ids, channel ids) if it does not commit them.

## 3. Interview for what the code cannot say

One question at a time, each with your recommendation and what you already found. Skip whatever you
already know. Cover:

1. **Versioning** — semver tags, or none (releases named by date and cut commit)?
2. **Shipping** — what exactly delivers a release (a push, a tag, a pin bump elsewhere), its gates,
   how to prove production runs it, what makes a rollback unsafe.
3. **Pushing** — may bookkeeping commits (cut, ticks, close, announcements) be pushed to the main
   branch right away, or would a push deploy? Is the main branch protected? If it is, suggest
   exempting the releases directory (or giving the people who release a push right for it) — a
   release has to push its cut commit atomically. Where several people work on the project, point
   out that bookkeeping kept local is invisible to everyone else. Debriefs travel with their feature
   branch either way.
4. **Autonomy on production** — the central question. What may an agent do on production without
   asking during verification? Push for concrete lines: which systems, which accounts, servers or
   workbenches are test objects that may be changed freely, what counts as harming a user, what must
   never be touched (real money, legal records like a ledger, other users' things), which events
   must be observed instead of provoked. A vague answer here produces exactly the hesitant agent this
   file exists to prevent — keep asking until every line is something an agent can apply literally.
5. **Test surfaces** — the logged-in browser session, test channels in Slack/Discord (links), test
   accounts, test servers or workbenches. For each: how an agent proves it has access, and what the
   user does when it has not — release and follow-up check all of them before the user leaves.
6. **Debrief rules** — plans, companion repos, version bumps, generated files, access rights — what
   a debrief in this project must additionally write or check.
7. **Release notes** — does the project want a complete, public page per release (a changelog in
   its docs or on its site)? If so: where, in which languages, and should the user see them before
   each release (`ask` / `always` / `never`)? Without them, announcements are built straight from
   the entries.
8. **Announcements** — platform and channel (link), the source (release notes or entries), what a
   draft preselects (e.g. headlines and anything the reader must act on), form, language, the style
   spec under `~/communication-styles/` if the user has one (otherwise: draft from the channel's
   recent posts), whose account posts.

## 4. Write the files

- **`RELEASING.md`** from `templates/RELEASING.md`. Keep the section headings — the skills look them
  up by name. Write the shipping procedure as concrete steps with complete commands (contexts,
  namespaces, ids), so a release agent can run it without guessing. Move the project-specific half of
  any old local release skill or playbook here rather than paraphrasing it away — its hard-won
  warnings are the valuable part.
- **The releases directory** with an empty `next/` (holding a `.gitkeep`, so it exists). Entries
  from an existing worksheet move into `next/`, one file per theme in the shape of
  `templates/entry.md` (`bump:` and `covers:` filled in as far as the old entry tells), and the
  worksheet is deleted.
- **Past releases**: each archived worksheet or release file becomes `<releases>/<name>/RELEASE.md`,
  unchanged in content, with frontmatter — `status: closed` when every box is ticked, `status: open`
  when not, and `announced: n/a` (nothing old is waiting for a post unless the user says
  otherwise). The skills read every file in a release directory, so one old file holding all themes
  works as it is.
- **Release notes**, if wanted: whatever the site needs to render them (a docs plugin, a nav entry),
  so the first release's page builds. Many old files with stale open boxes would flood
  `/follow-up`; ask the user whether to mark old releases `closed` wholesale.
- **Open work that lives elsewhere** (notes, memory files, tickets listing post-deploy steps nobody
  did): offer to collect it into one release directory with `status: open`, one debrief file per theme
  naming each item's source, so `/follow-up` works it.
- **`AGENTS.md`** (or whichever file the project's agents read first): add
  `templates/agents-section.md`, and rewrite any existing rule that contradicts it ("push when done",
  "always deploy after a change", anything naming `NEXT-RELEASE.md`) rather than leaving two rules.
  If `CLAUDE.md` only imports `AGENTS.md`, that is enough.
- **Retire local copies**: delete project-local `debrief` / `release` / `announce` / `follow-up`
  skills and their mirrors once their facts are in `RELEASING.md`, and update references to them.

## 5. Check and hand over

- Read `RELEASING.md` once as the release agent would: could you run the shipping procedure from it
  alone? Is every autonomy line applicable without a question?
- Run the project's own checks if these files are linted.
- Commit with an explicit pathspec; push only if the answer to question 3 allows it.
- Report briefly: what you wrote, what you moved out of old skills, what you decided yourself, and
  what is left for the user (a style spec still to write, a test account still to create).

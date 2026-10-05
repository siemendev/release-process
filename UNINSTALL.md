# Uninstalling

Instructions for an agent removing the release process. The user starts this with the uninstall
prompt from the README. Show the user what you found before deleting anything, and ask once per
project — release archives are history someone may still want.

## 1. Find everything

- **Skills**: entries named `debrief`, `release`, `follow-up`, `announce` (and a leftover
  `release-setup`) in every skill directory a harness on this machine reads (Claude Code:
  `~/.claude/skills/`; Codex: `~/.agents/skills/`). Only those that are symlinks into a clone of
  `github.com/siemendev/release-process` (or copies of its skills) are ours — leave any other skill
  of the same name alone.
- **The clone** they point to.
- **Projects** on the process: ask the user which ones, and/or search their usual repository folders
  for a `RELEASING.md` that names this process.

## 2. Per project, ask, then clean up

List what the project has and ask which of these to remove:

- `RELEASING.md`, `NEXT-RELEASE.md`, `NEXT-ANNOUNCEMENT.md` (repo root).
- The release archive and the announcement archive named in `RELEASING.md`'s *Files* section.
  Recommend **keeping** them — they record what shipped, what was verified and what people were told.
  Warn if any release file is still `status: open`: those items are unfinished and disappear with it.
- The debrief section in `AGENTS.md` / `CLAUDE.md` ("Finishing a change: debrief, then ask about a
  release") and any reference to `RELEASING.md` or the four skills elsewhere in the project's agent
  instructions. If the project shipped differently before (a playbook the onboarding replaced), say
  so — the user may want it back from git history.

Commit the removal in that project with an explicit pathspec. Push only where `RELEASING.md` said
bookkeeping commits are pushed — in a project where a push deploys, leave the commit local and say so.

## 3. Remove the skills and the clone

- Delete our symlinks (or copies) from every skill directory found in step 1.
- Ask before deleting the clone itself — the user may have local changes in it (`git status` there
  first and show them).

## 4. Report

What was removed where, what was kept on purpose, any open release items that were lost, and any
commits left unpushed.

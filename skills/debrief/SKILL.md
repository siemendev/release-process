---
name: debrief
description: >-
  Close out the change you just built: record what it takes to ship it and what the people who use
  the project should be told about it. Reach for this at the end of any session that produced
  something real — a feature, a fix, a behaviour change — before handing off, ideally on the feature
  branch before its merge request is merged. Writes one file for your change into the releases
  directory (`next/`, or the in-flight release if your commits are already part of a running
  release) — both what shipping it needs and what users notice — commits it, and on the main
  branch asks whether a release should follow. Needs the project's RELEASING.md; points to the
  onboarding if it is missing.
---
<Args>$ARGUMENTS</Args>
If args exist, treat them as the theme name for your entry (e.g. `terminal-proxy`).
If empty, derive a short kebab-case theme from what you built.

# Debrief your change

You came back from building something. Two groups need to hear about it, and they need different
things:

- **Whoever releases it** needs to know what to *do* so it works in production — a companion
  change, a grant, a backfill, a check that only makes sense against the live system.
- **Whoever uses the project** needs to know what *changed for them*. That goes under
  `## Users notice` in the same file. It travels with the release, becomes the release's public
  release notes where the project has them, and waits there until an announcement covers it.

Both go into **one file of your own**: `<releases>/next/<theme>.md`. Nobody else writes into it, so
any number of people can debrief in parallel without touching each other's work. A release moves
every file in `next/` into its own directory when it is cut.

**Run this even when the release needs nothing from you.** A pure UI change with no ops steps still
has to be told to somebody, and only you can tell it: by the time anyone writes release notes, the
meaning of `feat(x): gate the pre-pass` is gone. The only session that debriefs nothing is one whose
work nobody outside the repo would notice: dependency bumps, refactors, tests, internal docs.

**Debrief on the feature branch.** The file belongs in the same merge request as the code, so the
code never lands without it and the reviewer reads both. A debrief after the merge still works (§2),
it is just the late path.

**Describe only YOUR change.** Ignore every other file in the directory and every other change in
the repo. You are not writing a changelog of the release.

**Stay on the hot path.** A debrief closes a session that was usually long already. It writes, it
does not verify, release or follow up on anything. Everything below that sounds like checking is
reading — of your own commits and of the files you are about to edit.

## 0. Read the project's release facts

```bash
cat RELEASING.md
```

It names the releases directory, how versions or release names work, whether bookkeeping commits
get pushed here, the `Users notice` language, and any project-specific debrief rules (plans to
update, companion changes, version bumps a template needs). Those rules are part of this skill for
this project — follow them where the steps below say "the project's debrief rules".

If `RELEASING.md` does not exist, the project is not on this process yet. Say so and offer the
onboarding: `ONBOARDING.md` in the repository this skill is linked from (two directories above this
file). Do not improvise the files.

## 1. Establish what "your change" is

Read your own commits, not the whole unreleased range:

```bash
git log --oneline -15
git show --stat <your-commits>
git status --porcelain
```

On a feature branch, your commits are usually the branch's own: `git log --oneline <main>..HEAD`.

If your own work is still uncommitted, **say so and stop**: an entry describing code that isn't in
the release is worse than no entry. Offer the `git-commit` skill first. Foreign dirt from a
parallel session in this worktree is not yours — leave it alone and never commit it.

## 2. Find where your file goes

- **Your commits are not on the main branch yet** (a feature branch) → `<releases>/next/<theme>.md`.
  The file merges together with the code.
- **Your commits are already on the main branch** (you work on it directly, or the debrief was
  forgotten before the merge) → check whether a release already contains them. Each release
  directory's `RELEASE.md` records a `cut:` commit — the last commit that release contains. Find the
  oldest release whose cut contains each of your commits:

  ```bash
  git merge-base --is-ancestor <your-commit> <cut-sha> && echo "in that release"
  ```

  - **None are in a release** → `<releases>/next/<theme>.md`.
  - **All are, in a running one** (`status: in-flight`) → `<releases>/<that release>/<theme>.md`
    (§2a).
  - **All are, in a finished one** (`open` or `closed`) → the change is live without its debrief.
    Write the file into that release's directory all the same — its gates are history now, so say
    in Pre-release what should have happened and whether it did — and update that release's
    `RELEASE.md`: `status: open` if your file has open boxes, `announced: no` if it was `n/a` and you
    wrote `Users notice`. Say plainly in your report that the change shipped undebriefed.
  - **Split** (some before the cut, some after) → the release ships a partial state of your work.
    One file in the release directory for what it carries, one in `next/` for the rest, same
    theme name, and say plainly in your report that half of your change is live without the other
    half.

**Your theme name is your file name.** If `<theme>.md` already exists — in your checkout or on the
remote main branch (`git ls-tree origin/<main> <releases>/next/`) — and is not yours (its `covers:`
names other commits), pick a more specific name; never write into somebody else's file. If it is
yours — you debriefed this branch earlier and built more since — update it, but only while it is
still in `next/` on the remote main branch too: a release may have moved it since, and git would
carry your edit into a release that already shipped. Moved → write a new file for what is new.

### 2a. Writing into an in-flight release

The release agent is working that directory. Rules, so you never collide with it:

- **Only add your own file.** Never tick, untick, reword or reorder anything in the other files or
  in `RELEASE.md` — the boxes are the release agent's record.
- **Push it** where `RELEASING.md` says bookkeeping is pushed — the release may run on another
  machine and only sees what reaches the remote.
- **Tell the release agent.** It re-reads its directory at every phase boundary, so it will find
  your file eventually — but a phase it already finished will not be revisited on its own.
  `RELEASE.md` may name the release session (`session:`) and who runs it (`by:`). Look for the
  session with whatever your harness offers to find and message other agent sessions on this
  machine, and send one short message: which theme you added, which stages, and anything that
  belongs to a phase it may already have passed (a pre-release step after the push is the dangerous
  one; `Users notice` entries after the release notes were written are another — they reach the
  public notes only if it adds them).
- **If you cannot reach it** — no session found, it runs on someone else's machine, or your harness
  cannot message other sessions — say so, name the person in `by:`, and offer to put the message on
  the user's clipboard so they can pass it on. Never assume it saw your file.

## 3. Start the file

Take the shape of `templates/entry.md` from the repository this skill is linked from (two
directories above this file): frontmatter, a `# <theme>` title with one sentence on what the change
is, the six stages and `## Users notice` with its four sub-sections. Keep every heading, even
empty ones — the release reads them by name. Create `<releases>/next/` if it does not exist (a
release just emptied it).

**`covers:`** lists your commits as a block, one `<short-sha> <subject>` per line. The release
checks every `feat` and `fix` it ships against these lines and against which commits brought a
debrief file along; a commit nobody covers gets flagged as never debriefed. A rebase at merge
changes the SHAs but keeps the subjects; a squash commit is covered because it carries the file.

## 4. Claim the version (versioned projects only)

If `RELEASING.md` says the project releases without versions, delete the `bump:` line — the release
is named by its date and cut commit instead.

Otherwise set `bump:` to what your change needs, with a one-clause reason: a `!` suffix or
`BREAKING CHANGE` footer → `major`; any `feat(...)` → `minor`; only fix/chore/docs/deps → `patch`.
Judge the result, don't just trust the prefix — a `feat` that narrows an interface other code
compiles against is breaking whatever its prefix says. The release takes the highest claim of all
files it ships; the reason lets it tell whether a claim is justified.

**This is not the same judgement as "Need to know" in §7.** Semver breakage is about what other
code runs against; the announcement is about what a person's hands do tomorrow. Decide them
separately.

An in-flight release has a fixed version. If your change needs more than it carries, say so loudly
in your message to the release agent and in your report — it is not yours to change.

## 5. Write the stages — what shipping this needs

Put each point where its *timing* belongs, not where its topic feels at home:

- **Pre-release** — must be true before some specific step of the release. Companion changes in
  other repos (as a link to the ready change, see the project's debrief rules), config that must
  exist before the new code boots, anything whose absence makes the delivery fail. **Always state
  why, and name the step it has to precede** — the reason lets the release agent verify instead of
  trust, the step lets it schedule. If landing it early has a cost of its own, say so.
- **Sequencing & timing** — constraints on the run itself: component order, fleet-wide effects,
  "not mid-workday", failure modes that don't look like failures. An order dependency that the
  release satisfies by itself belongs here; something a human must go and *do* first is
  Pre-release.
- **After the release — required** — hands-on steps without which the shipped feature is
  **broken**. Granting a new permission is the archetype: a new access key nobody holds hides the
  feature from everyone. The test: *if nobody does this, is the feature visibly dead?* If yes, it
  goes here, never in cleanup.
- **Verification** — what to check on production after the rollout, as concrete steps. The release
  agent runs these **autonomously** within `RELEASING.md`'s autonomy rules, so write them to be
  run, not read: the page, the account or test surface to use, what a pass looks like, what a bad
  result means and where to look first. Mark what you could not measure locally. If your change
  fixes a reported bug, put its verification here — nobody tells the reporter before it passes.
  - **A check that can only pass once something real happens** — the first real payment after the
    change, the next nightly job, a player doing X — is a watch: write it as
    `- [ ] [watch] <what to wait for> — <how to see it happened, as a read-only query or page>`.
    Never fake the event to make it pass (no test bookings in a real ledger, no synthetic traffic
    against a customer); `RELEASING.md`'s autonomy rules say where that line is.
- **Backfills & migrations** — data and existing state: migrations, backfill commands, existing
  records affected. Name who or what is affected. Write "no backfill" explicitly.
- **Cleanup & risks** — orphans, optional deletions, unknowns. Nothing here blocks the release.

If your change needs nothing in a stage, say so where the absence is worth stating: every stage
may carry a `Checked, nothing needed:` block. "Checked, nothing needed" and "nobody thought about
it" must be distinguishable.

Format rules, because the release agent acts on them:

- `- [ ]` for anything someone must do; plain bullets for information only. The release ticks
  `[x]` what it completed; `[~]` marks an item carried to a later release by the user's decision.
- **A fenced command block means "safe to run as-is".** Only a command that is complete,
  non-interactive and idempotent, including every flag, context and credential it needs. If a step
  needs judgement or a click, write it as prose, so the agent stops and asks rather than improvising.
- Be specific over brief: change numbers, names, selectors, affected records and owners. This gets
  read once, possibly weeks from now, by someone under time pressure.

Then apply **the project's debrief rules** from `RELEASING.md` (plans, companion changes, version
bumps a template needs).

## 6. Open releases from earlier

List the release directories and count the unticked boxes in those whose `RELEASE.md` says
`status: open`. Do not work them — that is `/follow-up`'s job, not the hot path's. Just carry the
number into your report: "Release 3.6.0 still has 2 open items (1 watch) — run `/follow-up` to
check them." No open items → nothing to report; don't mention it.

## 7. Write `Users notice` — what it means to the reader

Now the last section, and a different frame of mind: forget the mechanism entirely and say what a
user notices. "The addressing gate now runs as a pre-pass" is worthless to them; "the assistant no
longer replies to every message in a channel it is in" is the same change, told.

- **Write content, not an announcement.** Plain, factual, in the `Users notice` language
  `RELEASING.md` names, no greeting, no voice, no polish. The release notes and `/announce` apply
  the voice later.
- **Pick a sub-section, don't decide importance.** What makes it into an announcement is decided at
  posting time, with the user.
- **Link the docs.** Where the project's docs have a page for it, add `Docs: <url>` under the entry
  — release notes and posts link it.

The sub-sections:

- **Headline** — a new capability worth a paragraph. What it is, and a sentence or two on how
  someone starts using it. Write that now, while it is fresh.
- **Need to know** — behaviour changed under the reader, or they must act. Mark `` `action` `` when
  they have to do something (grant a new right, change a setting) and say exactly what and where;
  mark `` `breaking` `` if something they do today stops working.
- **Also shipped** — one line, enough that a reader can spot it and ask.
- **Fixed** — a bug that is fixed. When somebody reported it: an open `- [ ]` and
  `Reported: <permalink>` — the release replies once the fix verifies. A permalink into a direct
  message is not a reply address the release agent can use — write
  `Reported: <permalink> (DM — only <name> can reply)` and put the reply text in the entry. If the
  report reached you without a link, say so.

```
### Headline

- One plain sentence about what a user now sees,
  continued on an indented line if it needs one.
  Docs: <url>

### Need to know

- `action` — Grant `some.right` under Groups, or the page stays hidden.

### Fixed

- [ ] What works again.
  Reported: <permalink>
```

Other lines an entry may carry later: `Heads-up: <permalink>` (announced before it shipped),
`Replied: <permalink>`. Internal work goes nowhere in `Users notice`. If your change is purely
internal, leave it empty and say so in your report.

## 8. Commit

Commit **only the files you wrote** — your theme file, and whatever the project's debrief rules had
you touch — as one commit with an explicit pathspec:

```bash
git add <files>
git commit -m "docs(release): debrief <theme>" -- <files>
git show --stat HEAD     # nothing else may be in it — parallel sessions share this worktree
```

- **On a feature branch** the commit travels with the merge request. Push it if the branch is
  already pushed, so the reviewer sees it.
- **On the main branch**, push only if `RELEASING.md` says bookkeeping commits are pushed. In a
  project where every push deploys, the commit stays local and rides along with the next release.
  A rejected push: `git pull --rebase` and push again — your file is yours alone, so there is
  nothing to resolve.

## 9. Report, then ask about the release

Two or three lines: which file you wrote (and, for an in-flight release, whether the release agent
got your message), which stages, which companion changes you prepared, which `Users notice`
sub-section you picked, which `bump:` you claimed, whether the commit is pushed or local, and open
items from earlier releases if there are any (§6). A check that turned up nothing is not news —
leave it out. If you wrote no `Users notice` (or no release stages), say so and why — silence there
reads as an oversight.

Then, only where your work is already on the main branch, **ask whether a release should follow
now** (`/release`). Never start one unasked. On a feature branch there is nothing to ask — the
entry ships with whichever release follows the merge. If your file went into an in-flight release,
it is already shipping.

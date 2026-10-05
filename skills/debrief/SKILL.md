---
name: debrief
description: >-
  Close out the change you just built: record what it takes to ship it and what the people who use
  the project should be told about it. Reach for this at the end of any session that produced
  something real — a feature, a fix, a behaviour change — before handing off. Writes your entry
  into the release worksheet (NEXT-RELEASE.md, or the in-flight release file if your commits are
  already part of a running release) and the announcement backlog (NEXT-ANNOUNCEMENT.md), then
  commits them and asks whether a release should follow. Needs the project's RELEASING.md; points
  to the onboarding if it is missing.
---
<Args>$ARGUMENTS</Args>
If args exist, treat them as the theme name for your entry (e.g. `terminal-proxy`).
If empty, derive a short kebab-case theme from what you built.

# Debrief your change

You came back from building something. Two groups need to hear about it, and they need different
things:

- **Whoever releases it** needs to know what to *do* so it works in production — a companion
  change, a grant, a backfill, a check that only makes sense against the live system. That goes in
  the release worksheet, which is cut and archived by every release.
- **Whoever uses the project** needs to know what *changed for them*. That goes in
  `NEXT-ANNOUNCEMENT.md`, which accumulates across releases until an announcement is worth posting.

**Run this even when the release needs nothing from you.** A pure UI change with no ops steps still
has to be told to somebody, and only you can tell it: by the time anyone writes release notes, the
meaning of `feat(x): gate the pre-pass` is gone. The only session that debriefs nothing is one whose
work nobody outside the repo would notice: dependency bumps, refactors, tests, internal docs.

**Describe only YOUR change.** Ignore every other feature in the files and in the repo. You are not
writing a changelog of the release.

**Stay on the hot path.** A debrief closes a session that was usually long already. It writes, it
does not verify, release or follow up on anything. Everything below that sounds like checking is
reading — of your own commits and of the files you are about to edit.

## 0. Read the project's release facts

```bash
cat RELEASING.md
```

It names the files (worksheet, backlog, release archive), how versions or stamps work, whether
debrief commits get pushed here, and any project-specific debrief rules (plans to update,
companion changes, version bumps a template needs). Those rules are part of this skill for this
project — follow them where the steps below say "the project's debrief rules".

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

If your own work is still uncommitted, **say so and stop**: an entry describing code that isn't in
the release is worse than no entry. Offer the `git-commit` skill first. Foreign dirt from a
parallel session in this worktree is not yours — leave it alone and never commit it.

## 2. Find where your entry goes

Your commits belong to exactly one release. Usually that is the next one — but a release may be
running right now, and if it was cut after your commits landed, your work is already shipping in
it and your entry must go **there**, or the release agent ships your change without the steps it
needs.

List the release files in the archive directory `RELEASING.md` names and read the frontmatter of
any whose `status` is `in-flight`. Each records a `cut:` commit — the last commit that release
contains. For each of your commits:

```bash
git merge-base --is-ancestor <your-commit> <cut-sha> && echo "in that release"
```

- **All your commits are in an in-flight release** → write into that release file (§2a).
- **None are** → write into `NEXT-RELEASE.md`.
- **Split** (some before the cut, some after) → the release ships a partial state of your work.
  Write the entry into the in-flight file for what it carries, a second entry into
  `NEXT-RELEASE.md` for the rest, and say plainly in your report that a half of your change is
  live without the other half.

### 2a. Writing into an in-flight release

The release agent is working that file. Rules, so you never collide with it:

- **Only append.** Add your entries under your theme in the stages they belong to. Never tick,
  untick, reword or reorder anything else — the boxes are the release agent's record.
- **Tell the release agent.** It reads its file at every phase boundary, so it will find your
  entries eventually — but a phase it already finished will not be revisited on its own. The file's
  frontmatter may name the release session (`session:`). Look for it with whatever your harness
  offers to find and message other agent sessions on this machine, and send one short message:
  which theme you added, which stages, and anything that belongs to a phase it may already have
  passed (a pre-release step after the push is the dangerous one).
- **If you cannot reach it** — no session named, none found, or your harness cannot message other
  sessions — say so and offer to put the message on the user's clipboard so they can paste it into
  the release session themselves. Never assume it saw your entry.

## 3. Read the files before writing

Read the target worksheet (or release file) and `NEXT-ANNOUNCEMENT.md` in full. Another theme may
already have claimed what you were about to write, or raised the version past yours. Never
rewrite either file wholesale — **edit only the sections you touch**, so parallel writers can only
collide with themselves.

If `NEXT-RELEASE.md` or `NEXT-ANNOUNCEMENT.md` does not exist (a release or an announcement just
consumed it), recreate it from the shape its header describes in the most recent archived copy, or
from the `templates/` directory of the repository this skill is linked from.

## 4. Negotiate the version (versioned projects only)

If `RELEASING.md` says the project releases without versions, skip this section — the release
stamps entries with its date and commit instead.

Otherwise the worksheet header names the version that document will become. It only ever moves
**up**. Derive what your change needs: a `!` suffix or `BREAKING CHANGE` footer → `major`; any
`feat(...)` → `minor`; only fix/chore/docs/deps → `patch`. Judge the result, don't just trust the
prefix — a `feat` that narrows an interface other code compiles against is breaking whatever its
prefix says.

**This is not the same judgement as "Need to know" in §7.** Semver breakage is about what other
code runs against; the announcement is about what a person's hands do tomorrow. Decide them
separately.

Read the last release where `RELEASING.md` says it lives (usually the origin's tags — never trust a
local tag list blindly if the project warns about foreign tags). If the header is already at or
above what you need, leave it and add your claim line; if you need more, raise the header and add
your claim with a one-clause reason. Claim lines let a later reader tell whether the version is
still justified once a theme drops out.

An in-flight release has a fixed version. If your change needs more than it carries, say so loudly
in your message to the release agent and in your report — it is not yours to change.

## 5. Write the worksheet entry — what shipping this needs

First add your theme to `## Themes`: `` - **[<theme>]** — <one clause> (`<first-sha>`..`<last-sha>`) ``.
This list is how the release knows which announcement entries it ships.

Then the six stages, fixed headings. Put each point where its *timing* belongs, not where its
topic feels at home:

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
    `- [ ] [watch] **[<theme>]** <what to wait for> — <how to see it happened, as a read-only query
    or page>`. Never fake the event to make it pass (no test bookings in a real ledger, no
    synthetic traffic against a customer); `RELEASING.md`'s autonomy rules say where that line is.
- **Backfills & migrations** — data and existing state: migrations, backfill commands, existing
  records affected. Name who or what is affected. Write "no backfill" explicitly.
- **Cleanup & risks** — orphans, optional deletions, unknowns. Nothing here blocks the release.

If your change needs nothing in a stage, say so where the absence is worth stating: every stage
may carry a `Checked, nothing needed:` block. "Checked, nothing needed" and "nobody thought about
it" must be distinguishable.

Format rules, because the release agent acts on them:

- `- [ ]` for anything someone must do; plain bullets for information only.
- **A fenced command block means "safe to run as-is".** Only a command that is complete,
  non-interactive and idempotent, including every flag, context and credential it needs. If a step
  needs judgement or a click, write it as prose, so the agent stops and asks rather than improvising.
- Prefix every entry with your theme in bold brackets — `**[terminal-proxy]**`.
- Be specific over brief: change numbers, names, selectors, affected records and owners. This gets
  read once, possibly weeks from now, by someone under time pressure.

Then apply **the project's debrief rules** from `RELEASING.md` (plans, companion changes, version
bumps a template needs).

## 6. Open releases from earlier

While you have the archive directory listed, count the unticked boxes in release files whose
`status` is `open`. Do not work them — that is `/follow-up`'s job, not the hot path's. Just carry
the number into your report: "Release 3.6.0 still has 2 open items (1 watch) — run `/follow-up` to
check them."

## 7. Write the announcement entry — what it means to the reader

Now the other file, and a different frame of mind: forget the mechanism entirely and say what a
user notices. "The addressing gate now runs as a pre-pass" is worthless to them; "the assistant no
longer replies to every message in a channel it is in" is the same change, told.

Follow `NEXT-ANNOUNCEMENT.md`'s own conventions. Three things to hold on to:

- **Write content, not an announcement.** Plain, factual, in the language the file's header names,
  no greeting, no voice, no polish. Several sessions write here; `/announce` applies the voice once.
- **Propose a section, don't decide importance.** Headline, Need to know, Also shipped, Fixed — pick
  the one that fits and move on. The bar moves with what else ships; that call is made once, at
  posting time.
- **Leave the stamp alone.** New entries are written `*(unreleased)*`; the release stamps them.

In short — the file has the long version:

- **Headline** — a new capability. A paragraph, and a sentence or two on how someone starts using
  it. Write that now, while it is fresh.
- **Need to know** — the reader's habits change, or they must act (grant a new right, change a
  setting). Mark it `breaking` if something they do today stops working.
- **Also shipped** — one line.
- **Fixed** — a bug somebody reported. Include `Reported: <permalink>` and leave its `- [ ]` open;
  the release replies once the fix verifies. A permalink into a direct message is not a reply
  address the release agent can use — write `Reported: <permalink> (DM — only <name> can reply)` and
  put the reply text in the entry. If the report reached you without a link, say so.

Internal work goes in neither file's announcement sections. If your change is purely internal,
write nothing here and say so in your report.

## 8. Commit

Commit **only the files you edited** — worksheet or release file, `NEXT-ANNOUNCEMENT.md`, and
whatever the project's debrief rules had you touch — as one commit with an explicit pathspec:

```bash
git add <files>
git commit -m "docs(release): debrief <theme>" -- <files>
git show --stat HEAD     # nothing else may be in it — parallel sessions share this worktree
```

**Push only if `RELEASING.md` says debrief commits are pushed.** In a project where every push
deploys, pushing a debrief would release everything on the branch — there the commit stays local
and rides along with the next release. Where pushing is wanted and gets rejected, `git pull
--rebase` and resolve conflicts by meaning: keep *both* entries, and for the header keep the
**higher** version with both claim lines.

## 9. Report, then ask about the release

Two or three lines: which file you wrote into (and, for an in-flight release, whether the release
agent got your message), which stages, which companion changes you prepared, which announcement
section you proposed, whether you raised the version, whether the commit is pushed or local, and
open items from earlier releases (§6). If you wrote nothing into one of the two files, say which
and why — silence there reads as an oversight.

Then **ask whether a release should follow now** (`/release`). Never start one unasked. If your
entry went into an in-flight release, there is nothing to ask — it is already shipping.

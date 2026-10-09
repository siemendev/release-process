---
name: release
description: >-
  Release a project end-to-end: cut the worksheet (NEXT-RELEASE.md) into an in-flight release file
  so parallel work can keep debriefing, work the pre-release gates, deliver exactly the cut through
  the project's own shipping procedure (RELEASING.md), confirm it is live, then do the required
  post-release steps and run every verification that needs no human — autonomously, in the
  browser and on the test surfaces RELEASING.md allows — write the release notes into the cut where
  the project has them, answer bug reporters, and close the release only when nothing is left. Determines where it stands
  before acting, so it can be resumed; stops before every irreversible step whose gate is not
  green.
---
<Args>$ARGUMENTS</Args>

`<Args>` may carry, in any combination:
- an explicit version (`2.1.0`) or a bump level (`major` / `minor` / `patch`) — versioned
  projects only; overrides the worksheet header
- `no-deploy` — stop before the delivery's first irreversible step
- `resume` — a hint that a previous attempt was interrupted; step 0 runs either way
- `autonomous` — publish the release notes without showing them first, whatever `RELEASING.md`'s
  review setting says, and do not ask about it

# Release

One release is: the state read → the cut taken → the pre-release gates worked → exactly the cut
delivered → confirmed live → the required steps done → **every verification that needs no human
run** → the reporters told → the release closed, or left open with only what needs a human. Where
the project has release notes, they are written before the cut and ship inside it.

## How to run it

**Do not stop to ask for confirmation.** The version or name was negotiated in the worksheet, the
gates decide the rest. Announce each decision as you take it and narrate progress — whoever asked
cannot see the pipeline. Long waits are normal; going quiet during them looks stalled.

Stop only when there is genuinely nothing sensible to do without an answer: a gate is red, a box
needs a human, the state check contradicts the plan, or a decision only the user can make (carrying
items to the next release, a major version nobody wrote down). Anything else — announce and
proceed.

**A green rollout is half the release.** The second half — verification — is the reason the
worksheet exists, and it is yours to do, not to hand back. The user typically lets a release run
unattended; a report that says "deployed, verification is up to you" makes them send you back to do
it. Run everything the autonomy rules in `RELEASING.md` allow, so the only items left at the end
are the ones that really need them.

**Resolve apparent contradictions yourself.** Two notes that seem to disagree about *when*
something must happen are usually one constraint seen from two sides. Read the **reason** each
gives: it names the real deadline, almost always a specific step of this process.

## The project's facts

```bash
cat RELEASING.md
```

Everything project-specific comes from there: the files and the archive directory, versioning or
naming, **the shipping procedure** (preflight, how the cut is delivered, its gates, how to
confirm it is live, rollback notes), whether bookkeeping commits are pushed, **the autonomy rules**
and **the test surfaces**, **the release notes** (optional — no section, no notes) and the
announcement channel. Where this skill says "the shipping
procedure", it means that section — follow it step by step, including its gates.

A `NEXT-ANNOUNCEMENT.md` in the repo means the project is still on the old backlog model: stop
before the cut and point to the migration section of `ONBOARDING.md` — releasing now would strand
its entries.

If `RELEASING.md` is missing, stop and offer the onboarding: `ONBOARDING.md` in the repository this
skill is linked from (two directories above this file).

## 0. Establish where you stand

Do this first, every time — not only when resuming. A release is a sequence of published facts;
read them from the world, never from memory.

- Release files in the archive directory with `status: in-flight` — a release already running (or
  interrupted). With `session:` naming another live session, that release is not yours: stop and say
  so. Otherwise resume it.
- For an in-flight release, read off the shipping procedure how far it got: is the cut delivered,
  are its gates done, is it live? Which boxes are ticked?
- Release files with `status: open` — earlier releases with unfinished verifications or watches.
  They are not this release's work; mention them at the end and point to `/follow-up`.

Enter at the first missing fact and **say what you found** — "the cut is already delivered and its
pipeline is green, so I'm resuming at the rollout check" is the sentence that prevents a double
release. A published irreversible fact (a pushed tag, a deployed commit) is never moved or deleted
to redo a release; a fix ships as the next release.

# Phase A — Prepare (nothing irreversible)

## 1. Preflight

Run the shipping procedure's preflight: the tools, contexts and companion checkouts later steps
need, so a missing one fails now rather than after the first irreversible step. Then:

- Confirm the repo and the release branch. `git fetch`; integrate incoming work first (the
  `git-pull` skill reconciles local work with what arrived).
- Dirty tree: uncommitted work will not ship. Ask whether to commit it (`git-commit` isolates a
  commit) or release without it. Never sweep up a parallel session's changes.

## 2. Know what ships

List the range: from the last release (where `RELEASING.md` says to read it) to `HEAD`. An empty
range means there is nothing to release — report and stop.

Match the range against the worksheet's `## Themes`. Commits no theme covers are either internal
(deps, refactors, tests — fine, they ship silently) or **somebody's change that was never
debriefed**. For the latter, say which commits and ask: wait for the debrief, or release without
the steps it may need? A `feat` or `fix` nobody debriefed is the case worth that one question.

Apply any project rule that must hold for the range (a template version that must have been
bumped, a generated file that must be current) as `RELEASING.md` describes. Flag a violation before
cutting and offer to fix it as a commit first.

## 3. Secure access for everything after the rollout

The user starts a release and walks away; verification runs an hour later. A login that fails then
turns an unattended release into one that waits for nobody. So find out now, while the user is still
here, what Phase C will need — and make sure it works.

1. **List what Phase C will touch.** Go through the worksheet's `After the release — required`,
   `Verification` and `Backfills & migrations` boxes, and the Fixed entries in `Users notice` whose
   reporters this release will answer. For each, name the surface it needs: the production app in
   the browser (as which account, with which role), Slack or Discord (which workspace, which
   channel, in the browser or through a tool), a test server, a cluster context, an API token, an
   admin panel. `RELEASING.md`'s test surfaces say how each is reached.
2. **Probe each surface once, read-only.** Open the app and see that you are logged in as the
   expected account with the rights the check needs (staff in that guild, admin in that panel); read
   the channel through the Slack/Discord tool; run a harmless `get` against the context. Where
   `RELEASING.md` names a probe for a surface, use that one. A probe proves access, nothing more —
   never act on production here.
3. **Collect every gap, then ask once.** One message listing everything that failed and exactly what
   the user has to do about it (log in to the app in the browser window that is open now, add the
   Slack app to the channel, `! gcloud auth login`). Wait, re-probe what they fixed, repeat until all
   is green. **Put the release-notes question into the same message** where it applies (step 5a).
   If the user says to go on without a surface, the boxes that need it wait for them — say
   so now and list them in the report.
4. **Say the result** in one line: "Access for verification: prod app (logged in as test-admin),
   Slack #release-test, kube context prod — all set, you can leave."

Access that will not last until Phase C (a session that expires within minutes, a one-shot token) is
a gap too: say so, and ask for the longer-lived variant where one exists. Nothing in Phase C needs
more than the shipping procedure's tools → go on without mentioning it. Resuming past this step →
run it before Phase C anyway.

## 4. Work the pre-release gates

Two stages act now.

**Pre-release** — verify each open `- [ ]` yourself wherever it is checkable rather than asking: a
companion change's merge state, a value in a config, a line in a script. Tick a box only once you
confirmed it. A companion change the worksheet only *describes*, with nothing prepared, is
unfinished work from that theme — stop and raise it rather than writing it yourself mid-release.

**A box's deadline is its reason, not its stage name.** Work out which step of the shipping
procedure the reason names, and act on it then. Where landing early has a cost, prefer the latest
moment its reason allows and say which moment you chose. A box whose deadline you reach and cannot
satisfy **stops the release**, like a red pipeline.

**Sequencing & timing** — surface it now, before the version, because it can decide whether now
is the moment at all.

An empty worksheet is fine. Several `feat` commits with an empty worksheet is worth one question.

## 5. Settle the version or name

**Versioned project:** the worksheet header names the version — take it. Derive the bump from the
commits as a cross-check (`!` / `BREAKING CHANGE` → major, `feat` → minor, else patch) against the
last release: at or below the header, the header wins; above it, take the higher and say why. The
one case worth a question: the commits derive **major** and the header claims less.

**Unversioned project:** the release is named by its date and the cut commit —
`YYYY-MM-DD-<short-sha>` as file name, `YYYY-MM-DD · <short-sha>` in prose.

State what you are releasing, what ships, and anything from Sequencing & timing that bears on doing
it now.

## 5a. Draft the release notes (only where `RELEASING.md` has a `## Release notes` section)

The notes ship **inside the cut**, so they go live exactly when the release does. Write them now,
before the cut, from the worksheet's `## Users notice`.

**Review.** Decide once, up front, whether the user sees the notes before they ship:

- `autonomous` in the args → publish without review; never ask.
- `RELEASING.md` says `Review: never` → publish without review.
- `Review: always` → show them and wait for a go before the cut.
- `Review: ask`, or nothing said → ask in the access message of step 3: *"Shall I show you the
  release notes once before they ship, or publish them without review?"* No answer because the user
  already left → publish without review and say so in the report.

**Write one page per release, in every language `RELEASING.md` names**, where it says, with the
front matter it says. Unless `RELEASING.md` gives its own page shape:

1. **TL;DR** — one or two sentences on the scale and kind of the release: only fixes, a few
   improvements, or a big update and what it is mainly about. Never a list. This sentence decides
   whether the reader reads on, so it is honest about a quiet release.
2. **Action needed** — every Need to know marked `action` or `breaking`: what to do and where. Left
   out when empty.
3. **The big things** — one real paragraph per Headline: what it is, how to start using it.
4. **Good to know** — the remaining Need to know and Also shipped, one short line each.
5. **Fixed** — a plain bullet list.

Every entry with a `Docs:` line links it. Translate faithfully — the same facts in every language,
nothing added. Never mention internals: theme names, commits, `Reported:` links. A release whose
`Users notice` is empty still gets a page: the TL;DR says it is maintenance and internal work.

If the user asked to review: show every language, take edits until they say go. The files then wait
uncommitted for the cut commit.

## 6. Take the cut

The cut freezes what this release is, so parallel work never has to wait for you:

1. Record the cut: the current `HEAD`. Everything up to and including it ships in this release;
   anything committed later belongs to the next.
2. Move the worksheet to its archive file — `git mv NEXT-RELEASE.md <archive-dir>/<name>.md` —
   and give it frontmatter:
   ```yaml
   ---
   status: in-flight        # in-flight → open → closed
   announced: no            # no → <announcement name>; n/a when `Users notice` is empty
   release: 3.7.0           # or 2026-10-05 · 3f9a1c2
   cut: <full sha of HEAD before this commit>
   session: <your session's name, if your harness has one — so a late debrief can message you>
   started: 2026-10-05T09:12Z
   ---
   ```
3. Write a fresh `NEXT-RELEASE.md` from the archived file's shape: the same header text and
   conventions, empty stages, empty `## Themes` and `## Users notice` sub-sections, and for a versioned project a target of this
   version bumped by a patch (a floor) with this version as "last release".
4. Apply the project's cut rules from `RELEASING.md` (for example: stamp plans listed in the
   worksheet with this version now, so a lint on archived releases stays green).
5. Commit those files alone, plus the release notes from 5a — `docs(release): cut <name>` — and push it if `RELEASING.md` says
   bookkeeping commits are pushed. **This commit is the cut commit** you deliver: it sits directly on
   `cut:`, so it carries exactly the release plus its own archived file and notes. For an unversioned release,
   `<name>` uses the short SHA of `cut:`.

From here on, parallel sessions debrief into the fresh worksheet — or, if their commits are inside
your cut, append to **your** file and message you.

**Re-read your release file at every phase boundary** (before delivery, before the required steps,
before verification, before closing). Pick up new entries into the phase you are entering. An entry
for a phase you already passed — a pre-release step that appears after delivery — is not skipped
silently: report it at once and handle it as the late gate it is.

# Phase B — Deliver (irreversible from here)

## 7. Follow the shipping procedure

Do exactly what `RELEASING.md`'s shipping procedure says, in its order, with its gates. Universal
rules on top of it:

- **Deliver the cut commit, not the branch tip.** Push/tag the `docs(release): cut` commit, never
  whatever `HEAD` became while you worked.
- **A gate that is not green stops the release.** Never work around one, never force-push, never
  move or delete a published tag. Report the failing job with enough detail to act on.
- Capture a baseline of "normal" first where the procedure says so (logs, pod list), so after the
  rollout you can tell a new error from an old one by measuring instead of remembering.
- `no-deploy` stops before the first irreversible step.

## 8. Confirm it is live

Confirm the way the procedure says that production really runs the cut — the deployed image tag or
commit compared against the cut, pods ready and not restarting, the error classes in the logs
compared against the baseline. Verifying the previous release by accident is the classic failure
here; compare the identifier first.

# Phase C — Finish

Access was secured in step 3. A surface that fails anyway — a session expired, an entry appended
after the check needs something new — parks only the boxes that need it: keep going with everything
else and list in the report exactly what the user has to restore. Never stall the run on a login.

## 9. Required post-release steps

`After the release — required`. Without these the shipped feature is broken but silent.

- Run every fenced command block as written. Carry anything that needs a click or a judgement
  **loudly** to the user now — never let it drift into the final report.
- **Verify the effect; do not accept "done".** Read the result back where it can be read.
- Never report the release complete while a required box is open.

## 10. Verification — autonomously

This is the step releases skip, and the one the user cares about most. Go through every
`Verification` box and **do it**:

- **Within the autonomy rules of `RELEASING.md`, act without asking.** Use the test surfaces it
  lists: the browser session the user is logged in to (via Playwright or whatever browser tool you
  have), the test channels in Slack/Discord where you may post and trigger integrations, the test
  accounts, servers or workbenches you may change freely. Restarting, toggling and creating on those
  is allowed by definition — that is what they are for.
- **Outside them, stop at that one box only.** Something the rules forbid (real money, other
  people's things, irreversible changes to production data) waits for the user — note exactly what
  you would do and keep going with everything else.
- **Never fake an event to make a check pass.** If a check can only pass once something real
  happens, it is a `[watch]`: run its read-only check once. Happened → tick it with what you saw.
  Not yet → leave it open with a note of when you looked. The release stays `open` for it, and
  `/follow-up` checks it later.
- **Report every result**: tick what passed with a one-line note of what you observed; for a
  failure, use the box's own detail (they usually say what a bad result means and where to look
  first), investigate as far as the autonomy rules allow, and say what you found.

## 11. Backfills & migrations

Run what the worksheet lists that is not self-executing, as written, within the autonomy rules.

## 12. Answer the reporters

Every **Fixed** entry in your release file's `## Users notice` with a `Reported:` permalink and an
open box. **The gate is verification**: reply only where step 10 confirmed the fix (or the worksheet
said no verification was needed). For each: reply in the reported thread (one or two sentences, in
the report's language, saying what works now and that it is live), react with a checkmark, tick the
box, add `Replied: <permalink>`. An entry marked as a DM someone else must answer: hand the user the
reply text instead. A fix you could not verify keeps its box open.

**Late `Users notice` entries.** A debrief that appended to your file after the cut added entries the
release notes do not have yet. Update the notes page now — it goes live with the next release; say
so in the report. If `announced:` was `n/a`, it becomes `no`.

## 13. Close the release — or leave it open

**A release stays with its items.** Never move an open box into `NEXT-RELEASE.md` on your own.

- Every box ticked, the Fixed boxes in `Users notice` included → `status: closed`, `closed: <date>`.
  `announced:` is a separate axis: a closed release can still wait for its announcement.
- Boxes still open (an unmet watch, a step that needs the user, a failed check) →
  `status: open`. The release file is where they live until `/follow-up` or the user finishes them.
- **Carrying an item to the next release is the user's decision**, never yours. If they decide it,
  mark the box `- [~]` with `→ carried to NEXT-RELEASE.md: <reason>` and add the item there under
  its theme.

Write a short outcome block under the header — when it went live, the pipelines, what was verified
and how, what is still open and who owes it. Commit the release file
(`docs(release): <name> — <closed|open, n items left>`), pushed if bookkeeping is pushed.

## 14. Report

- What shipped (name, commit list or themes), how long delivery and rollout took, links.
- What you verified and how — briefly.
- **What needs the user**, as the last and clearest block: each open item, why it needs them, and
  what exactly they would do. Nothing else may be buried below it.
- Open earlier releases, if any: "3.6.0 still has 2 open items — `/follow-up`".

**Then: is an announcement due?** Read `Users notice` of every release file with `announced: no` —
this one and earlier ones nobody announced yet — and recommend rather than ask:

- **A `Need to know` entry sets a deadline.** People's habits already changed under them — post
  soon, however thin the rest is. A `breaking` one live for a release or two without a word is the
  strongest case.
- **A `Headline` entry sets a threshold.** Two or three good ones are a post; one half-interesting
  one usually is not.

Say it concretely ("1 headline, 2 need-to-know, 7 also-shipped — announce now, the rights note has
been live since Tuesday"). If agreed, run `/announce`.

# When something goes wrong

- **A gate is red.** Stop at the gate, report the failure with its trace. Fix forward.
- **Live but broken.** Prefer a forward fix. A rollback is not automatically safe after a migration
  or a narrowed interface — the worksheet's Backfills & migrations stage says which case you are in;
  if it does not, ask the change's author before rolling back. Say which you chose and why.
- **Interrupted.** Back to step 0. Never restart from the top, never re-tag.
- **A worksheet entry is wrong or impossible.** Report it to its author; do not rewrite it to fit.
  Ticking a box you verified, and the notes beside it, are your only edits to other themes' entries.

# Safety

- Never force-push. Never delete or move a published tag or deployment.
- Never cross a gate that is not green, never deliver past an open Pre-release box whose deadline
  has come, never call a release done with an open required box.
- Never act outside `RELEASING.md`'s autonomy rules without asking — and never stay inside them so
  timidly that the verification is left to the user.
- Never tell a reporter a fix is live without a passed verification; never put an entry into the
  release notes whose theme did not ship.
- Never carry an open item to the next release without the user deciding it.

---
name: follow-up
description: >-
  Finish what earlier releases left open: go through every release whose status is `open`,
  check its watches (verifications that wait for something real to happen — the first real
  payment, the next nightly run), run any verification that has become possible, and close each
  release once nothing is left. Reach for this between work sessions, when `/debrief` or `/release`
  reports open items, or when asked whether last week's change has proven itself. Never ships
  anything.
---
<Args>$ARGUMENTS</Args>
`<Args>` may name one release (`3.6.0`, `2026-10-05-3f9a1c2`) to limit the run to it.

# Follow up on open releases

A release stays `open` while any of its boxes are unticked — mostly watches, sometimes a
verification that needed a moment the release could not wait for. This skill is the only place
they get worked, so the debrief stays on the hot path and the release can end.

It runs on its own: **within the autonomy rules of `RELEASING.md`, check and tick without asking**.
Stop only at items that need the user, and collect them for the end.

## 1. Read the ground

```bash
cat RELEASING.md
```

From it: the releases directory, the autonomy rules, the test surfaces, and whether bookkeeping
commits are pushed. `git pull` first where bookkeeping is pushed — others release and follow up
too. Then list the release directories whose `RELEASE.md` says `status: open` (and `in-flight` ones
only to skip them — a running release belongs to its release session). A release's open boxes are
in the files of its directory — the theme files, and `RELEASE.md` itself for releases from before
there were theme files.

Nothing open → say so in one line and stop.

## 2. Secure access before working

The user starts a follow-up and moves on, so every login has to be in place before the first item.
List the surfaces the open items will need — the production app in the browser (as which account),
Slack or Discord channels, test servers, cluster contexts, tokens; `RELEASING.md`'s test surfaces
say how each is reached — and probe each once, read-only (logged in as the expected account, with the rights the check needs? channel
readable? `get` works?), using the probe `RELEASING.md` names where it names one. Collect every gap
into **one** message with exactly what the user has to do, wait, re-probe, repeat until all is
green. A surface the user waives leaves the items that need it for them. Then say in one line that
access is set and the rest runs without them.

## 3. Work each open item

Per release, oldest first, per debrief file, per unticked box:

- **A watch** (`[watch]`): run its read-only check.
  - The event happened → check what the watch actually verifies (the booking has the new field, the
    job ran with the new behaviour), tick it with a one-line note of what you saw and when.
  - Not yet → leave it open; add `Checked <date>: not yet — <what you saw>`. If it has been open
    long enough that the event should have happened by now (the first payment after a change usually
    comes within a day or two), say so: a watch that never fires may mean the change broke the path
    that would trigger it.
  - **Never cause the event yourself to make it fire** — no test payment into a real ledger, no
    synthetic action against real users. A watch exists precisely because that would be wrong.
- **A verification** left open: run it now, within the autonomy rules, on the test surfaces
  `RELEASING.md` lists. Tick what passes, note what fails.
- **A box that needs the user** (a click, a decision, something the autonomy rules forbid): leave
  it, collect it for the report.
- **A Fixed entry** in `Users notice` whose verification is now ticked: reply to the reporter as the
  `release` skill describes (thread reply, checkmark reaction, tick, `Replied:`) — the reply was
  waiting for exactly this.

## 4. Close what is done

A release whose every box in every file is now ticked (or `[~]` carried by the user's decision) gets
`status: closed` and `closed: <date>` in its `RELEASE.md`. `announced:` does not hold a release open — announcing is
`/announce`'s business, not a box. **Never carry an item into `next/` on your own** —
if an item looks like it will never be satisfiable as written, propose carrying or dropping it and
let the user decide. Carrying follows step 13 of the `release` skill; a dropped item becomes
`- [~]` with `→ dropped: <reason>`.

Commit the files you changed (`docs(release): follow up <names>`), with an explicit
pathspec, pushed only if `RELEASING.md` says bookkeeping commits are pushed.

## 5. Report

Per release: what you ticked and what you saw, what is still open and why (not yet happened /
needs you / failed), which releases you closed. End with the items that need the user — each with
what exactly they would do.

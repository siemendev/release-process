# Next release

<!-- versioned projects: replace the line below with
**Target version: `X.Y.Z`** — last release was `X.Y.W`.

Claims on the version (highest wins; raise it if your change needs more, never lower it):

- `patch` — floor set by the release; the first author who needs more raises it.
-->
Unversioned — the release is named by its date and cut commit when it is cut.

---

This is the worksheet for whatever ships next. If your change needs a hand to go live — a companion
change, a grant, a backfill, a check that only makes sense on production — it belongs here, added
with `/debrief` so every entry has the same shape. What the change means for the people who *use*
the project goes in `NEXT-ANNOUNCEMENT.md` instead — same skill, different clock.

`/release` cuts this file into the release archive (see `RELEASING.md`) the moment it starts, and
opens a fresh one, so later work never waits for a running release. Debriefs whose commits are
already inside a running release's cut append to that release's file instead.

Conventions: `[ ]` is an open action, `[x]` was completed during the release, `[~]` was carried to
a later release by an explicit decision. `[watch]` marks a verification that can only pass once
something real happens; it stays open in its release until `/follow-up` sees it happen. A fenced
command block may be run as-is by the release agent; prose is for a human. Every entry names its
theme in brackets. "Nothing to do" is written down rather than left out. State **why** each
pre-release box exists and **which step it has to precede**.

## Themes

## Pre-release

## Sequencing & timing

## After the release — required

## Verification

## Backfills & migrations

## Cleanup & risks

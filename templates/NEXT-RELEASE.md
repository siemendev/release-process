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
the project goes in the same file, under `## Users notice` — written for them, not for the release.

`/release` cuts this file into the release archive (see `RELEASING.md`) the moment it starts, and
opens a fresh one, so later work never waits for a running release. Debriefs whose commits are
already inside a running release's cut append to that release's file instead.

Conventions: `[ ]` is an open action, `[x]` was completed during the release, `[~]` was carried to
a later release by an explicit decision. `[watch]` marks a verification that can only pass once
something real happens; it stays open in its release until `/follow-up` sees it happen. A fenced
command block may be run as-is by the release agent; prose is for a human. Every entry names its
theme in brackets. "Nothing to do" is written down rather than left out. State **why** each
pre-release box exists and **which step it has to precede**.

`## Users notice` is content, not publication: plain factual statements in <LANGUAGE> about what
changed for the reader, no announcement voice. Its sub-sections are an internal sorting —
release notes and announcements are built from them (see `RELEASING.md`):

- **Headline** — a new capability worth a paragraph. What it is and how to start using it.
- **Need to know** — behaviour changed under the reader, or they must act. Mark `` `action` `` when
  they have to do something (grant a right, change a setting), `` `breaking` `` when something they
  do today stops working.
- **Also shipped** — one line, enough that a reader can spot it and ask.
- **Fixed** — a bug that is fixed. When somebody reported it: an open `- [ ]` and
  `Reported: <permalink>` — the reply is owed until the fix is verified live.

Optional lines under an entry: `Docs: <url>`, `Heads-up: <permalink>` (announced before it shipped),
`Reported:` / `Replied: <permalink>`. Internal work (deps, refactors, tests) does not belong here.

```
- **[example-theme]** — One plain sentence about what a user now sees,
  continued on an indented line if it needs one.
  Docs: <url>
- **[example-theme]** `action` — Grant `some.right` under Groups, or the page stays hidden.
- [ ] **[example-theme]** — What works again (Fixed only).
  Reported: <permalink>
```

## Themes

## Pre-release

## Sequencing & timing

## After the release — required

## Verification

## Backfills & migrations

## Cleanup & risks

## Users notice

### Headline

### Need to know

### Also shipped

### Fixed

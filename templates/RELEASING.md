# Releasing <project>

The project facts the `debrief`, `release`, `follow-up` and `announce` skills work from. The skills
carry the process; this file carries everything that is specific to this project. Keep the section
headings — the skills look them up by name.

## Files

- **Worksheet:** `NEXT-RELEASE.md` (repo root).
- **Release archive:** `<dir>/` — one file per release, frontmatter `status: in-flight | open | closed`
  and `announced: no | <announcement name> | n/a`.
- **Announcement archive:** `<dir>/` — one file per post, exactly as posted.

## Versioning

<!-- One of:
- Semver tags `X.Y.Z` on origin, no `v` prefix. Read the last release with
  `git ls-remote --tags origin | sed 's#.*refs/tags/##' | grep -E '^[0-9]+\.[0-9]+\.[0-9]+$' | sort -V | tail -1`.
- None. A release is named `YYYY-MM-DD-<short-sha>` of its cut (`YYYY-MM-DD · <short-sha>` in prose).
  The last release is the newest file in the release archive. -->

## Pushing bookkeeping

<!-- Whether debrief / release / follow-up / announce commits are pushed right away, or stay local
because a push would deploy. -->

## Shipping procedure

<!-- The project's own delivery, in order. The release skill follows this step by step.
### Preflight      — tools, contexts, checkouts later steps need
### Deliver the cut — how exactly the cut commit goes out (push, tag, pin bump) and its gates
### Confirm live    — how to prove production runs the cut, what a baseline looks like
### Rollback notes  — what makes rollback unsafe here
### Cut rules       — anything to do at the cut (e.g. stamp plans) -->

## Autonomy on production

What a release, a follow-up or a verification may do **without asking**, and where it must stop.
The skills act on this literally: anything allowed here is done without a question, anything
forbidden waits for the user.

**Allowed without asking:**

- Reading everything: pages, data, logs, metrics.
- <test surfaces and what may be changed there>

**Never without the user:**

- <what must not be touched>

**Observe instead of provoking:** <events that must happen for real — write their verification as a
`[watch]`>.

## Test surfaces

<!-- Where verification can act: the browser session the user is logged in to, test channels in
Slack/Discord (with links/ids), test accounts, test servers, test workbenches. For each: a read-only
probe that proves the agent has access (a page that shows the logged-in account, a channel read, a
`get`), and what the user does when it fails. Release and follow-up run every probe before the user
leaves. -->

## Debrief rules

<!-- Project-specific additions to the debrief: plans to update, companion changes in other repos
that must exist as a ready change, version bumps a template needs. "None." if there are none. -->

## Release notes

<!-- Optional. Delete this section and release notes are off: announcements are built straight from
the release files' `Users notice`. With it, `/release` writes a public release-notes page per
release into the cut, so the notes go live with the release itself.

- **Where:** <path per language, e.g. `docs/changelog/<version>.md` and `docs/de/changelog/<version>.md`>
- **URL:** <public URL per release and of the overview>
- **Languages:** <English | English and German, …>
- **Review:** ask | always | never — whether the user sees the notes before they ship. `ask` (the
  default) asks once at the start of every release; `/release autonomous` never shows them.
- **Page shape:** <only where it differs from the skill's default: TL;DR, Action needed, big things,
  good to know, Fixed>
- **Build:** <anything a page needs to render: front matter, a sidebar entry, a build to run> -->

## Announcements

- **Platform & channel:** <Slack / Discord> — <link and id>
- **Source:** <release notes — the post picks from them and links them | entries — the post is built
  from the release files' `Users notice`>
- **Draft includes:** <which kinds are preselected, e.g. Headline + `action` + `breaking`; the rest
  is offered as numbered candidates>
- **Form:** <main message + thread reply with the full list | a single message, split at 2000 chars>
- **Language:** <English>
- **Voice:** `~/communication-styles/<spec>.md`
- **Posted as:** <whose account; write as them, not about them>

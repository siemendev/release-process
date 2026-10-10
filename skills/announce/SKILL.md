---
name: announce
description: >-
  Tell the project's users what changed. Collects every release not announced yet, drafts a post
  for the channel RELEASING.md names — from the release notes or straight from the releases'
  `Users notice`, as the project chooses — and shapes it with the user in quick rounds ("drop 2,
  add 7") until it is right. Posts only on an explicit go, archives exactly what was posted and
  marks the covered releases announced. Also does heads-up posts: flagging something important
  BEFORE it ships. Reach for this when a release just landed and enough has piled up, when somebody
  asks for release notes, or when a change needs warning ahead of time.
---
<Args>$ARGUMENTS</Args>
`<Args>` may carry:
- `heads-up` — pre-release mode (§7). Anything after it names what to flag.
- nothing — the normal announcement.

# Announce what changed

An announcement is: the unannounced releases collected → a draft with the defaults preselected and
every other candidate offered → shaped with the user, round by round → **explicitly approved** →
posted → archived → the covered releases marked announced.

**Nothing gets posted without an explicit go.** It reaches everyone in the channel and cannot be
quietly corrected afterwards. That is the one hard rule in this skill — it holds even inside an
otherwise autonomous run.

## Facts come from RELEASING.md

```bash
cat RELEASING.md
```

Its **Announcements** section names: the platform and channel, the **source** (the release notes,
which the post links — or the releases' `Users notice` entries directly), what the **draft
includes** by default, the **form** (a single message, or a main message plus a thread reply…), the
language, the voice or style spec to read in full before drafting, and who posts. **Files** names
the releases directory and the announcement archive; **Release notes** (if present) says where the
notes live and their public URL.

## 1. Collect what is unannounced

List the release directories and read the frontmatter of every `RELEASE.md`. The candidates are
all releases with `announced: no` whose `status` is not `in-flight` (a running release is not live
yet). Skip `n/a`.

None → say so and stop. Then read:

- the `## Users notice` of every file in each candidate's directory, `RELEASE.md` included (the
  internal sorting: Headline, Need to know with its
  `action` / `breaking` marks, Also shipped, Fixed) and, where the source is release notes, its
  published notes page;
- the previous archived announcement (the newest file in the announcement archive) — its shape and
  voice;
- the channel's recent history (about ten messages), if a tool can read it — an open bug report you
  are about to call fixed, a discussion the post should acknowledge.

Thin material — only a few Also shipped lines, no Headline, no `action`/`breaking` — say so and
recommend waiting; a post that says nothing teaches people to skip the next. The exception is an
`action` or `breaking` entry: that has a deadline, not a threshold. Recommend posting it even alone.

## 2. Number every candidate

Build one numbered list of everything that could go into the post: every Headline, every Need to
know, every Also shipped, and Fixed entries if the form lists them. One line each — the release it
shipped in, its sub-section and marks, a short gist. Numbers stay stable for the whole conversation,
so "add 7" means the same thing three rounds later.

## 3. Draft

- **Preselect what `Draft includes` names** (for example Headline + `action` + `breaking`). The rest
  stays a candidate.
- **Shape follows the form and voice in `RELEASING.md`.** Several headlines at once are not all
  equal — the biggest leads, others shrink to a line; one headline in a thin release carries the
  post. `action` and `breaking` items say exactly what the reader must do and where.
- **Source = release notes:** the post points at the notes for the details — a link to the notes
  page or overview, as `RELEASING.md` says. Each item is a teaser, not the full text.
- **Source = entries:** the form decides where the rest goes (e.g. a thread reply listing every
  entry, one line each).
- **Already flagged** (`Heads-up:` under the entry): one line, "now live: …", linking that post — do
  not repeat what people already read.
- **Platform markdown.** Slack: `*bold*`, `<url|label>`. Discord: `**bold**`, `[label](<url>)` (the
  angle brackets suppress the preview), 2000 characters per message — split at an item boundary,
  never mid-item. Wrong markup shows raw syntax to everyone.
- **Language:** the one `RELEASING.md` names, whatever language the conversation is in.

## 4. Shape it with the user

Present, every round:

1. the full text of every message, exactly as it would be posted, with its character count where
   the platform has a limit;
2. **"In the post"** — the numbers currently included;
3. **"Not in the post"** — the remaining candidates by number, grouped by sub-section, so the user
   can pick from them;
4. one line on any call you made (what leads, what you shortened and why).

Then wait. The user answers in shorthand — "drop 2, add 7 and 12, first paragraph shorter, no emoji"
— and you redraft and present again. Repeat as often as it takes. Offer the obvious moves when they
help ("7 and 9 are the same area — one line?"), never make them silently. Only an explicit go
("post it", "passt, raus damit") ends the loop; changes after that are a new round.

## 5. Post

Post in the form's order (main message first, then its thread reply or follow-up messages), keeping
the identifiers each post returns, and build the permalinks. **If a first message posted and a
follow-up failed, say so immediately and loudly** — a post promising a list that never arrived is
worse than no post. Retry before anything else.

No tool can post to the channel → put the text on the user's clipboard if you can, ask them to post
it, and wait for the permalink before §6. Never mark releases announced on a post you cannot point to.

## 6. Archive and mark

Only after every message is up.

1. Write `<announcement-archive-dir>/<newest release covered>.md`: the date, the permalink(s), the
   releases covered, and every text **exactly as posted**.
2. Set `announced: <that archive name>` in the `RELEASE.md` of **every** release the post covered —
   also those none of whose items made it into the post: their details are in the notes the post
   links (or in the list it carried), and they are not offered again.
3. Commit both (`docs(announce): announce <name>`), with an explicit pathspec, pushed only if
   `RELEASING.md` says bookkeeping commits are pushed.

Reporters of Fixed entries are not this skill's job — the release and `/follow-up` answer them once
the fix is verified.

## 7. Heads-up mode

`/announce heads-up <what>` flags something **before** it ships — the one deliberate exception to
"only released work". It posts about **only** what you name (from a debrief file in `next/` or in an
in-flight release, or from what the user tells you), marks nothing announced, archives
nothing, and addresses the future: what is coming, when, and what the reader should do before it
lands. Shape it with the user the same way (§4); post only on a go.

After posting, add `Heads-up: <permalink>` under each `Users notice` entry it covered — in the
debrief file, wherever it is — and commit just those files. A file that exists only on a feature
branch gets the line on that branch (or hand the line to its author) — never create it on the main
branch, that collides with the merge. The
line travels with the entry into its release, and stops the real announcement from repeating the
paragraph. Then stop.

## 8. Report

The permalink(s), the releases covered, what led and what was left to the notes or the list, and
the archive path. Any release still `announced: no` (left out on purpose, or in flight) — say which.

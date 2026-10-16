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
- `heads-up` — pre-release mode (§8). Anything after it names what to flag.
- nothing — the normal announcement.

# Announce what changed

An announcement is: the unannounced releases collected → their entries gathered into clusters → a
draft with the defaults preselected and every other candidate offered → shaped with the user, round
by round → **explicitly approved** → posted → archived next to its first draft → the covered
releases marked announced → a look at what the user changed, so the next draft starts closer.

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
language, the voice or style spec to read in full before drafting, the **audience**, and who posts.
**Files** names the releases directory and the announcement archive; **Debrief rules** may list the
project's **areas**; **Release notes** (if present) says where the notes live and their public URL.

## 1. Collect what is unannounced

List the release directories and read the frontmatter of every `RELEASE.md`. The candidates are
all releases with `announced: no` whose `status` is not `in-flight` (a running release is not live
yet). Skip `n/a`.

None → say so and stop. Then read:

- the `## Users notice` of every file in each candidate's directory, `RELEASE.md` included (the
  internal sorting: Headline, Need to know with its
  `action` / `breaking` marks, Also shipped, Fixed), each file's `area:` and the entries' `Hook:`,
  `Affects:` and `Area:` lines where they have them, and, where the source is release notes, its
  published notes page;
- the previous archived announcement (the newest file in the announcement archive) — its shape and
  voice, and what it already told: an entry that post announced ahead of time is not news again;
- the `## What moved` of the last three archives that have one — what the user changed in earlier
  drafts. A move they made twice (dropped the same kind of entry, put the same kind of thing first)
  is applied to this draft from the start, and said in the first round's call line;
- the channel's recent history (about ten messages), if a tool can read it — an open bug report you
  are about to call fixed, a discussion the post should acknowledge.

Thin material — only a few Also shipped lines, no Headline, no `action`/`breaking` — say so and
recommend waiting; a post that says nothing teaches people to skip the next. The exception is an
`action` or `breaking` entry that affects most readers: that has a deadline, not a threshold.
Recommend posting it even alone.

## 2. Cluster, then number every candidate

**Posts are told by story, not by debrief file.** Gather the entries of all candidate releases by
area — the file's `area:`, an entry's own `Area:` overriding it. The map changes of three releases
are one cluster. Entries without an area (older files) join the cluster their text plainly belongs
to; near-identical names (`map`, `maps`) are one cluster. Then read across the `Hook:` lines: several
clusters tied to the same outside event (a game update, a launch day) can make one lead together.

Build one numbered list of everything that could go into the post, grouped by cluster: every
Headline, every Need to know, every Also shipped, and Fixed entries if the form lists them. One line
each — the release it shipped in, its sub-section and marks, `Affects:` where it narrows the
audience, ★ where it has a hook, a short gist. Numbers stay stable for the whole conversation, so
"add 7" means the same thing three rounds later.

## 3. Draft

- **Preselect what `Draft includes` names** (for example clusters with a Headline + `action` +
  `breaking`). An `action` or `breaking` reaches the draft when its `Affects:` is everyone or most of
  the audience; one that concerns a few stays a candidate, flagged with whom it concerns. No
  `Affects:` line (older files) → treat it as everyone. The rest stays a candidate.
- **One paragraph or line per cluster, not per entry.** The cluster's Headlines become its
  sentences; its other entries are supporting clauses or are left to the notes.
- **The hook decides what leads, and how a paragraph opens.** Order the clusters by their strongest
  hook — first or only one, an outside event happening now, a known pain answered, visibly new —
  then by how many Headlines they hold. Open a paragraph with its angle, not its feature list.
  Never post a hook's evidence, and name every "first", "only" or "nobody else" claim in the call
  line with the debrief's evidence: it goes out under the user's name.
- **Ask for the angle the debriefs could not have.** A record on launch day, a reaction in the
  channel, a competitor's silence — the user often knows the best hook. One question in the call
  line when no cluster has a strong one.
- **Shape follows the form and voice in `RELEASING.md`.** Several clusters at once are not all
  equal — the strongest leads, others shrink to a line; one headline in a thin release carries the
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

Keep the first draft exactly as you present it — it is archived next to the posted text (§6).

## 4. Shape it with the user

Present, every round:

1. the full text of every message, exactly as it would be posted, with its character count where
   the platform has a limit;
2. **"In the post"** — the numbers currently included;
3. **"Not in the post"** — the remaining candidates by number, grouped by cluster, so the user
   can pick from them;
4. one line on any call you made (what leads and on which hook, what you shortened and why, any
   lesson from earlier archives you applied, any "first" or "only" claim and its evidence).

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
   releases covered, then three sections —
   - `## Text as posted` — every text **exactly as posted**;
   - `## First draft` — the first draft exactly as you presented it in round one;
   - `## What moved` — at most six lines of fact, no commentary: what led in the draft and in the
     post; what was dropped and what added (number, gist, sub-section, area); the angles the user
     brought in, quoted; clusters merged or split; a narrow `action` kept or dropped. Nothing moved
     → say so in one line — that is evidence too.
2. Set `announced: <that archive name>` in the `RELEASE.md` of **every** release the post covered —
   also those none of whose items made it into the post: their details are in the notes the post
   links (or in the list it carried), and they are not offered again.
3. Commit both (`docs(announce): announce <name>`), with an explicit pathspec, pushed only if
   `RELEASING.md` says bookkeeping commits are pushed — after §7, so an accepted proposal rides
   along.

Reporters of Fixed entries are not this skill's job — the release and `/follow-up` answer them once
the fix is verified.

## 7. Learn from what moved

Read the `## What moved` of this archive and of the two before it that have one. A move that
repeats in at least two of them — the same kind of entry dropped, the same kind of angle added, a
lead the draft keeps missing — is a lesson. Propose **one** concrete change, as the exact line,
where it belongs:

- **In `RELEASING.md`** when it is about this project: `Draft includes`, the `Areas:` list, the
  debrief rules' `Not a Headline here:`, the voice. Apply it on a yes and commit it with the
  archive.
- **In the `debrief` or `announce` skill** when it would hold for any project. Say so to the user as
  a suggestion for the skills' repository; do not edit the skills from here.

Nothing repeats → say nothing. A single move is an anecdote, not a rule.

## 8. Heads-up mode

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

## 9. Report

The permalink(s), the releases covered, what led and what was left to the notes or the list, the
archive path, and the lesson you proposed in §7 if there was one. Any release still
`announced: no` (left out on purpose, or in flight) — say which.

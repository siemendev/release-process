---
name: announce
description: >-
  Tell the project's users what changed. Drains the stamped entries of NEXT-ANNOUNCEMENT.md into a
  post on the channel RELEASING.md names (Slack, Discord, …) in the form and voice it prescribes,
  then archives exactly what was posted. Also does heads-up posts: flagging something important
  BEFORE it ships, without draining anything. Reach for this when a release just landed and the
  backlog looks worth posting, when somebody asks for release notes, or when a change needs
  warning ahead of time. Never posts without an explicit go.
---
<Args>$ARGUMENTS</Args>
`<Args>` may carry:
- `heads-up` — pre-release mode (§6). Anything after it names what to flag.
- nothing — the normal announcement.

# Announce what changed

An announcement is: the backlog read → coverage established → the post drafted → shown and
**explicitly approved** → posted → permalinks written back → the backlog drained and archived.

**Nothing gets posted without an explicit go.** It reaches everyone in the channel and cannot be
quietly corrected afterwards. Draft, show, wait. That is the one hard rule in this skill — it holds
even inside an otherwise autonomous run.

Everything else you can decide yourself: which entries lead, how to phrase them, whether the
backlog is worth posting today. Say what you decided as you go.

## Facts come from RELEASING.md

```bash
cat RELEASING.md
```

Its announcements section names: **the platform and channel**, **the form** (for example a main
message plus a thread reply with the full list, or a single message), **the language**, **the
style spec** to read for the voice, **the archive directory**, and who the post goes out as. Read
the style spec it names in full before drafting.

## 1. Read the ground

```bash
cat NEXT-ANNOUNCEMENT.md
ls <announcement-archive-dir> | sort | tail -3
```

Then read the channel's recent history (about ten messages) and the previous archived
announcement. The channel tells you what the backlog cannot: an open bug report you are about to
call fixed, a discussion the post should acknowledge, the shape of the last post. The newest
archive file — not a date, not a search — is where the previous announcement stopped.

## 2. Establish coverage

**Only stamped entries are announceable.** An entry still reading `*(unreleased)*` is not live;
mentioning it sends people looking for something that isn't there. It stays for the next
announcement.

Collect every stamped entry across all sections. The stamps you find are the range this post
covers; the **latest** one names the archive file (a version like `3.6.0`, or a release name like
`2026-10-05-3f9a1c2`).

Nothing stamped → say so and stop. Only a few thin `Also shipped` lines → say so and recommend
waiting; a post that says nothing teaches people to skip the next. The exception is a stamped
`Need to know`, especially a `breaking` one or a right users must grant: that has a deadline, not a
threshold. Post it even if it is alone.

## 3. Decide what goes where

The sections in the file are proposals written by sessions that each saw only their own change.
Now you see all of them, and **the bar moves with the size of the release**. Four headlines in one
post is three too many — the biggest leads, the others become bullets. A single headline in a thin
release carries the whole post.

- **Up front**: the headlines and every `Need to know` — those earn their place by being needed,
  not by being big. Breaking items first among them. Anything the reader must *do* (grant a right,
  change a setting) is said explicitly, with where to do it.
- **The complete list**: every stamped entry, one line each, where the form puts it (the thread
  reply, or the rest of a single message).
- **Already flagged** (`Heads-up:` under the entry): one line linking that post — "now live: …".
  Do not repeat a paragraph people already read.

Say which entries you promoted or demoted and why, one line each, when you present the draft.

## 4. Draft

Follow the form and the style spec from `RELEASING.md`. Two things the spec may not tell you:

- **Each platform has its own markdown.** Slack: `*bold*` (one asterisk), `<url|label>` links.
  Discord: `**bold**`, bare URLs or `[label](url)`, `<url>` to suppress the preview, a 2000-character
  limit per message (split at an entry boundary, never mid-entry). Get this wrong and the post shows
  raw syntax to everyone.
- **Write in the language `RELEASING.md` names**, whatever language the conversation around it is.

## 5. Show it, then post it

Present the full text of every message and wait for an explicit go. Offer the obvious edits.
Rework and show again as often as needed.

On approval, post in the form's order (main message first, then its thread reply or follow-up
messages), keeping the identifiers each post returns, and build the permalinks. **If a first message
posted and a follow-up failed, say so immediately and loudly** — a post promising a list that never
arrived is worse than no post. Retry before anything else.

## 6. Heads-up mode

`/announce heads-up <what>` flags something **before** it ships — the one deliberate exception to
"only stamped entries". It posts about **only** what you name (from the backlog even if unstamped,
or from what the user tells you), **drains nothing, archives nothing**, and addresses the future:
what is coming, when, and what the reader should do before it lands.

After posting, add `Heads-up: <permalink>` under each entry it covered and commit just that file.
That marker stops the real announcement from repeating the paragraph. Then stop.

## 7. Drain and archive

Normal mode only, and only after every message is up.

Write `<announcement-archive-dir>/<latest stamp covered>.md`: the date, the permalink(s), the range
covered, and every text **exactly as posted** — the record of what people were actually told.

Then rewrite `NEXT-ANNOUNCEMENT.md`: remove every entry you posted, keep everything unstamped, and
keep any stamped `Fixed` entry **whose reply box is still open** — that reporter has not been told
and still needs a reply. Keep the header and all section headings, with `Nothing yet.` under empty
ones.

Commit both (`docs(announce): announce <latest stamp>`), pushed only if `RELEASING.md` says
bookkeeping commits are pushed.

## 8. Report

The permalink, the range covered, what led and what went to the list, what stayed in the backlog
and why (unstamped, or an unanswered report), and the archive path.

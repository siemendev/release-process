# Next announcement

What the people who use this project get told about what changed. `/debrief` writes an entry here
at the end of a session, `/release` stamps it when its theme ships, `/announce` drains the file
into a post and archives what it posted (see `RELEASING.md` for the channel and the archive).

**Not every release is announced.** Entries live here across several releases and only leave when
an announcement actually goes out.

**This file is content, not publication.** Write plain factual statements in <LANGUAGE> about what
changed for the reader. No announcement voice, no greeting, no polish — several sessions write
here; `/announce` applies the voice once, at the end.

## How an entry works

One entry is one line, plus continuation lines indented under it. Keep this shape exactly — the
release finds unstamped entries by it:

```
- **[example-theme]** *(unreleased)* — One plain sentence about what a user now sees,
  continued on an indented line if it needs one.

- [ ] **[example-theme]** *(unreleased)* — What works again, in the Fixed section only.
      Reported: <permalink to the report>
```

Only **Fixed** entries carry the `- [ ]` checkbox. Every entry names its theme in bold brackets and
carries a stamp:

- `*(unreleased)*` — written, not shipped yet. A release announcement never mentions it (the one
  exception is a heads-up).
- `*(<release>)*` — the release that shipped it, set by `/release` for the themes in its cut.
  Nobody sets this by hand.

Optional lines under an entry: `Heads-up: <permalink>` (already announced before it shipped),
`Reported: <permalink>` / `Replied: <permalink>` (see **Fixed**).

## Which section

The section is a *proposal* about where the entry lands in the post; `/announce` makes the real
call across all entries at once.

- **Headline** — a new capability worth a paragraph. Say what it is and how to start using it.
- **Need to know** — behaviour or UI changed under the reader, or they must act (grant a right,
  change a setting). Add `breaking` if something they do today stops working.
- **Also shipped** — one line, enough that a reader can spot it and ask.
- **Fixed** — a bug somebody reported. Carries `Reported: <permalink>` and an open `- [ ]`: the
  reply is owed until the fix is verified live.

Internal work — dependency bumps, refactors, tests, docs — does not belong here.

## Headline

Nothing yet.

## Need to know

Nothing yet.

## Also shipped

Nothing yet.

## Fixed

Nothing yet.

# release-process

Agent skills that ship changes the same way in every project — and keep track of what each change
still needs after it is live.

Coding agents are good at building a change and bad at remembering what it needs afterwards: the
right a group has to be granted, the backfill, the check that only makes sense on production, the
sentence that tells users what changed. That knowledge exists only at the end of the session that
built the change, and it is gone by release day. This process writes it down at exactly that moment
— one small file per change, so a whole team can do it in parallel — and works it off at release
time, including the verification on production, which agents usually hand back to you.

## The process

```
  build ──▶ /debrief ──▶ merge ──▶ /release ──▶ /follow-up ──▶ /announce
              │                       │              │              │
              ▼                       ▼              ▼              ▼
   releases/next/<theme>.md   releases/<name>/   ticks watches   Slack/Discord
   (ship it + users notice)   RELEASE.md + the                   post + archive
   in the feature branch      theme files                        announced: <name>
                              in-flight → open → closed
                              + release notes (optional)
```

**`/debrief`** — at the end of every session that produced something real, on the feature branch
before its merge request is merged. It writes one file for the change, `releases/next/<theme>.md`:
what the release has to *do* to ship it (pre-release steps, required post-release steps like grants,
verifications, backfills, cleanup), what users *notice* (`Users notice`: headline, need to know,
also shipped, fixed), the version bump it claims and the commits it covers. Every change has its own
file, so ten people debriefing at once never touch each other's work. On the main branch it then
asks whether to release. It never verifies or releases anything itself — it closes long sessions
and stays out of the way.

**`/release`** — releases everything that is ready, together. It:

1. **cuts** the release: flags every `feat` and `fix` in the range that nobody debriefed, takes the
   highest claimed bump as the version, and moves every file from `next/` into
   `releases/<name>/` next to a `RELEASE.md` (`status: in-flight`, the cut commit, who runs it) —
   parallel work keeps debriefing into the empty `next/` instead of waiting;
2. checks up front every access the verification will need (browser logins, Slack/Discord,
   clusters) and asks you once for whatever is missing — so you can leave before the rollout;
3. writes the **release notes** into the cut where the project has them — a public page per release
   that goes live with it (shown to you first, or not, as you choose);
4. works the pre-release gates and delivers **exactly the cut** through the project's own shipping
   procedure, stopping at every gate that is not green;
5. confirms production runs the cut;
6. does the required post-release steps and **runs every verification it may run on its own** — in
   your logged-in browser, in the test channels, on test servers and accounts — within the autonomy
   rules the project defines;
7. answers the people who reported the fixed bugs;
8. closes the release — or leaves it `open` with only what really needs you, listed last in its
   report.

**`/follow-up`** — works what releases left open: mostly **watches**, verifications that can only
pass once something real happens (the first real payment after a change, the next nightly run). It
never fakes the event; it secures its access first, then looks, ticks what happened, and closes
releases that are done.

**`/announce`** — collects every release not announced yet and drafts the post in the project's
voice and form — from the release notes (and linking them) or straight from the entries. You shape it
in quick rounds ("drop 2, add 7") with every candidate in view; it posts only after your explicit
go, archives exactly what was posted and marks the releases `announced`. `/announce heads-up` warns about
something before it ships.

### Rules the skills keep

- **A release keeps its items.** Open verifications stay in the release they belong to. Carrying one
  to the next release is always your decision, never automatic.
- **Parallel work never waits, and never collides.** Each change writes only its own file. A debrief
  whose commits are already inside a running release's cut adds its file to that release's directory
  and messages the release session (or hands you the message for whoever runs it). The release
  re-reads its directory at every phase.
- **Nothing is released unasked**, and nothing is posted without an explicit go.

## One file per project: `RELEASING.md`

The skills carry the process; each project's `RELEASING.md` carries its facts:

| Section | What it says |
| --- | --- |
| Files | releases directory (`next/` + one directory per release), announcement archive |
| Versioning | semver tags — or none, releases named `YYYY-MM-DD-<sha>` |
| Pushing bookkeeping | whether release bookkeeping is pushed, or stays local because a push deploys |
| Shipping procedure | preflight, how the cut is delivered and gated, how to confirm it is live, rollback notes |
| **Autonomy on production** | what an agent may do without asking, what never, what to observe instead |
| Test surfaces | test channels, test servers, test accounts, the logged-in browser |
| Debrief rules | `Users notice` language; project-specific additions: plans, companion MRs, access rights |
| Release notes | optional: where the pages go, languages, whether you review them first |
| Announcements | platform, channel, source (release notes or entries), what the draft preselects, form, language, voice, posted as whom |

*Autonomy on production* is the section that matters most. Write it so every line can be applied
literally — "anything on the test server; on the main server only what no player notices, cleaned
up afterwards; never a booking in the real ledger, wait for the first real one instead". A vague
section produces an agent that asks about everything, or one that asks about nothing.

## Onboarding

Start a session in the project and paste this prompt:

```
Put this project on the release process from github.com/siemendev/release-process.
Read its ONBOARDING.md and follow it — fetch it with
`gh api repos/siemendev/release-process/contents/ONBOARDING.md -H "Accept: application/vnd.github.raw"`
(or from a local clone of the repository if there is one).
```

The agent installs the skills if this machine does not have them yet (clone + symlinks into
`~/.claude/skills` / `~/.agents/skills`), reads the project, interviews you about what the code
cannot tell it — mostly how far it may go on production — and writes `RELEASING.md`, the
releases directory and the debrief rule in `AGENTS.md`. Existing local release skills are folded in and
removed. Templates are in [`templates/`](templates/).

To update the skills later: `git pull` in the clone — every project uses the new version at once.

## Uninstalling

Paste this prompt:

```
Remove the release process from github.com/siemendev/release-process from this machine.
Read its UNINSTALL.md and follow it — fetch it with
`gh api repos/siemendev/release-process/contents/UNINSTALL.md -H "Accept: application/vnd.github.raw"`
(or from the local clone the skills are linked from).
```

It finds the linked skills and the projects on the process, shows you what it would remove, asks per
project (it recommends keeping the release and announcement archives), and cleans up skills,
project files and the instructions in `AGENTS.md`.

## Layout

```
skills/debrief/    skills/release/    skills/follow-up/    skills/announce/
templates/         RELEASING.md, entry.md (one debrief file), agents-section.md
ONBOARDING.md      what the onboarding prompt points at
UNINSTALL.md       what the uninstall prompt points at
```

## Contributing

Issues and pull requests are welcome. For bigger changes, open an issue first so we can talk it through.

## License

[MIT](LICENSE)

## Finishing a change: debrief, then ask about a release

When you finish something real — a feature, a fix, a behaviour change — run **`/debrief`** before
handing off, even when shipping it needs nothing: it records what the release has to *do*
(`NEXT-RELEASE.md`) and what users *notice* (`NEXT-ANNOUNCEMENT.md`), and only you can still say
either. The only session that debriefs nothing is one nobody outside the repo would notice:
dependency bumps, refactors, tests, internal docs.

After the debrief, **ask whether to release** (`/release`). Never start a release unasked.
How this project ships, what a release may do on production without asking, and where announcements
go is in [`RELEASING.md`](RELEASING.md).

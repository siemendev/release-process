## Finishing a change: debrief, then ask about a release

When you finish something real — a feature, a fix, a behaviour change — run **`/debrief`** before
handing off, even when shipping it needs nothing: it writes one file for your change into the
releases directory with what the release has to *do* and what users *notice*, and only you can still
say either. On a feature branch, debrief before the merge request is merged — the file belongs in
it. The only session that debriefs nothing is one nobody outside the repo would notice: dependency
bumps, refactors, tests, internal docs.

Once the change is on the main branch, **ask whether to release** (`/release`). Never start a
release unasked. How this project ships, what a release may do on production without asking, and
where announcements go is in [`RELEASING.md`](RELEASING.md).

# release-process

Agent skills for shipping changes the same way in every project: each finished change is
**debriefed**, changes are **released** together, open verifications are **followed up**, and users
are told in an **announcement**.

| Skill | What it does |
| --- | --- |
| `debrief` | End of every real change: writes what shipping it needs into `NEXT-RELEASE.md` and what users notice into `NEXT-ANNOUNCEMENT.md`, then asks whether to release. |
| `release` | Cuts the worksheet into an in-flight release file (parallel work keeps debriefing), delivers exactly the cut through the project's procedure, runs every verification an agent may run on its own, stamps the announcement backlog, and closes the release only when nothing is left. |
| `follow-up` | Works what earlier releases left open — mostly watches, verifications that wait for something real to happen. |
| `announce` | Drafts the post from the stamped backlog, posts it after an explicit go, archives what was posted. |
| `release-setup` | Interviews you about a project and writes its `RELEASING.md`, the two worksheets and the debrief rule in `AGENTS.md`. |

The skills carry the process. Everything project-specific — how it ships, **what an agent may do on
production without asking**, where it can test, where announcements go — lives in the project's
`RELEASING.md`.

## Lifecycle of a release file

```
NEXT-RELEASE.md ──/release cuts──▶ <archive>/<release>.md
                                    status: in-flight ─▶ open ─▶ closed
                                                          ▲        │
                                                 /follow-up ───────┘
```

- **in-flight** — being delivered. Debriefs whose commits are inside its `cut:` append here and
  message the release session.
- **open** — live, but a verification or watch is still open. Items stay in the release they
  belong to; carrying one to the next release is always an explicit decision.
- **closed** — every box ticked or deliberately carried.

## Install

```bash
git clone git@github.com:siemendev/release-process.git ~/Documents/private/release-process
for s in ~/Documents/private/release-process/skills/*/; do ln -s "$s" ~/.claude/skills/"$(basename "$s")"; done
```

Codex reads `~/.agents/skills`; link the same directories there (or point `~/.agents/skills` at
`~/.claude/skills`). Then run `/release-setup` in a project.

# Max's T3 Code fork

This fork uses upstream T3 Code as its foundation and adds Max's personal customizations.
It is not maintained for contributing those changes upstream.

- Fork / `origin`: <https://github.com/muxihale/t3code>
- Upstream / `upstream`: <https://github.com/pingdotgg/t3code>
- `main`: the fork's working version, including personal changes and upstream updates.
- Starting upstream commit: `962428df104d1958ff58a3b205e64a07117e7781` (2026-10-20).

## Record each customization's intent

Git records what changed. Intent records explain why the change exists and what an agent must
preserve when upstream evolves. Keep these records with the code so other machines, worktrees,
and agents receive them too.

For each new customization, copy [the template](docs/fork/changes/TEMPLATE.md) to
`docs/fork/changes/FNNN-short-description.md`, using the next unused number. Update the index below.
Check existing records first: a follow-up to the same customization should update its record rather
than create a duplicate. Cover every intentional fork departure, including documentation and
configuration changes; do not create a record for each imported upstream commit or mechanical edit.

Before implementation, capture Max's stated intention and concrete acceptance criteria. Distinguish
explicit requirements from implementation assumptions. Update the affected locations and actual
verification results before handing work back. Include code and its record together when committing,
and put the record ID in the commit message; `git log --oneline --grep=F001` can then find its history.

Keep each record concise and current. Explain the desired outcome, important constraints, and
conflict-resolution guidance; use Git for line-by-line history. Never claim a check passed unless it
ran. Do not include credentials, pairing URLs, or private conversation transcripts.

## Integrate upstream updates

Use merges to preserve the fork's history. Do not reset the fork to upstream, force-push, or resolve
conflicts wholesale with `--ours` / `--theirs`. The customized `main` is expected to differ from upstream.

Start with a clean working tree and create an integration branch:

```sh
git switch main
git fetch upstream
git switch -c sync/upstream-YYYY-MM-DD
git merge --no-commit --no-ff upstream/main
```

Replace the date with the actual date and use an unused branch name. If Git reports that it is already
up to date, there is no merge to complete. Otherwise:

1. Read every active intent record and compare the upstream changes with the fork's changes since
   their common ancestor. Git can merge code cleanly while still changing behavior the fork relies on.
2. For each conflict, understand the base, fork, and upstream versions. Prefer upstream's current
   architecture while preserving the recorded behavior; reimplement the customization if necessary.
   Do not retain obsolete code merely because the fork previously used it.
3. If upstream now satisfies a customization's full acceptance criteria, verify that behavior,
   remove redundant fork implementation, and mark its record `upstreamed` with the upstream commit.
   Keep the record as an explanation. Mark intentionally removed customizations `retired` and explain why.
4. If upstream and a recorded requirement cannot both be satisfied, ask Max about that specific
   tradeoff. Do not silently discard a requirement or invent a new intention.
5. Update affected records, including moved code locations and fresh verification results. Check
   every active record's acceptance criteria, not only files with conflict markers. Follow the
   repository's focused verification guidance; state anything that could not be verified.
6. Stage the reviewed resolutions and documentation explicitly. Commit the merge with its upstream
   commit, affected record IDs, compatibility decisions, and verification results in the message.
   This message is the integration record; no separate upstream changelog is needed.

When the integration is verified and ready, fast-forward the fork's `main` to the integration branch
and push to `origin` as authorized. If `main` moved meanwhile, integrate its new commits and verify
again. Do not overwrite them. Never push personal work to `upstream`.

## Customizations

- [F001 — Preserve customization intent across upstream updates](docs/fork/changes/F001-fork-intent-workflow.md)

- [F002 — Material You as the only mobile theme](docs/fork/changes/F002-material-you-only.md)

- [F003 — Drop Arch Linux support](docs/fork/changes/F003-drop-arch-support.md)

- [F004 — Pinned threads cannot settle](docs/fork/changes/F004-pinned-threads-stay-active.md)

F001 establishes the maintenance workflow; add subsequent customizations to this index.

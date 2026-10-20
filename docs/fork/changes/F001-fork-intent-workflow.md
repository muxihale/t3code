# F001 — Preserve customization intent across upstream updates

- Status: active
- Introduced: 2026-10-20
- Last reviewed against upstream: `962428df104d1958ff58a3b205e64a07117e7781`

## Intention

Max wants to customize this fork for personal use while continuing to rely on upstream development.
Max explicitly does not plan to contribute upstream and wants every personal change documented with
its intention so future LLM agents can resolve conflicts without losing the desired behavior.

Implementation choice: keep the customized version on `main`, merge upstream updates, and store
durable intent records in the repository. Short-lived feature and integration branches can be used
without changing this model.

## Acceptance criteria

- Every intentional fork change creates or updates an intent record with its reason, acceptance
  criteria, affected locations, and actual verification results.
- Agents discover the fork workflow through root `AGENTS.md`; `CLAUDE.md` already imports that file.
- Upstream integrations preserve active requirements, including where Git reports no textual conflict.
- An upstream implementation may replace a customization after equivalence is verified; the record
  remains available with its new status and replacement reference.
- Work on the personal fork does not require upstream contribution approval or unsolicited upstream PRs.

## Implementation and scope

- [Root agent instructions](../../../AGENTS.md) provide the fork-specific entry point and override
  upstream's default of not documenting ordinary changes.
- [Fork guide](../../../FORK.md) defines the branch model, record index, and upstream integration procedure.
- [Record template](TEMPLATE.md) captures intention separately from implementation details.
- `docs/fork/changes/` contains the durable records. Git commit messages reference their IDs.
- This change affects maintenance documentation only. It adds no automatic conflict resolver or
  enforcement hook; agents must follow the instructions and verify behavior.

## Guidance for upstream updates

Preserve the fork instructions and their discoverability if upstream rewrites `AGENTS.md` or changes
how `CLAUDE.md` loads it. Retain upstream engineering guidance alongside the fork's explicit
documentation and contribution-policy overrides. Keep record links and the index valid after moves.

Intent records guide resolution but cannot guarantee it. Ask Max when a real requirement tradeoff is
not settled by a record; routine refactors that preserve the acceptance criteria do not need clarification.

## Verification

- 2026-10-20: inspected the documentation changes against the acceptance criteria above; the root
  entry point, record template, integration procedure, and explicit fork-policy overrides are present.
- 2026-10-20: `git diff --check` passed for tracked changes. A Python check of all four changed/new
  Markdown files found no trailing whitespace or missing final newlines, and all 11 local links resolved.
  The same check confirmed that `CLAUDE.md` imports root `AGENTS.md`.
- Application tests are not applicable: this change modifies documentation only.
- Future-agent compliance and a real upstream integration have not been exercised.

## Retirement or replacement

Not applicable; active.

# F003 — Drop Arch Linux support

- Status: active
- Introduced: 2026-10-20
- Last reviewed against upstream: `962428df104d1958ff58a3b205e64a07117e7781`

## Intention

Max requested dropping Arch Linux support from the fork after identifying Arch in the supported
desktop platforms. The fork should not maintain or publish Arch-specific packages.

## Acceptance criteria

- No AUR package definitions, publication scripts, or publishing workflows remain.
- The release pipeline does not call an AUR workflow or require an AUR publishing secret.
- Installation and development docs do not advertise Arch Linux support or pacman/yay commands.
- Generic Linux artifacts and support for other platforms remain available. This does not add an
  operating-system block; an Arch user may still be able to run a generic Linux build independently.

## Implementation and scope

- Remove `packaging/aur/` and `.github/workflows/publish-aur.yml`.
- Remove the `publish_aur` job from `.github/workflows/release.yml`.
- Remove Arch-specific guidance from `README.md`, `docs/user/install.md`, and
  `docs/operations/development.md`; update `docs/operations/release.md` to match the release pipeline.
- Add a brief support-scope statement to the installation guide so upstream merges do not silently
  restore the expectation of Arch maintenance.

## Guidance for upstream updates

Do not reintroduce AUR publishing or Arch-specific packaging and instructions when merging upstream.
Keep shared Linux fixes and generic Linux builds. Architecture references such as ARM64/x64 or
“same-arch” are unrelated to Arch Linux and must remain intact.

## Verification

- 2026-10-20: scanned installation, development, packaging, and workflow files. No AUR package,
  publication, or Arch installation references remain; the explicit unsupported-platform statement
  and CPU-architecture references are intentionally retained.
- Bun's YAML parser accepted the updated release workflow. Comparing job IDs against `HEAD` confirmed
  that only `publish_aur` was removed. All remaining `needs` references and local reusable workflows resolve.
- `git diff --check` passed.
- No application runtime code changes are required.

## Retirement or replacement

Not applicable; active.

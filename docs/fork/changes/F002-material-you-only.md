# F002 — Material You as the only mobile theme

- Status: active
- Introduced: 2026-10-20
- Last reviewed against upstream: `962428df104d1958ff58a3b205e64a07117e7781`

## Intention

Max requested: “Eliminate all themes from the Appearance page. The ‘Material You’ must be the one.”
The existing Material You theme is mobile-only. Implementation scope: mobile Appearance and its
runtime selection. Web and desktop have a separate theme system without Material You.

## Acceptance criteria

- Mobile Appearance contains no theme picker or alternative theme cards.
- Material You is used for both light and dark appearances, including fresh installs and devices
  with previously saved legacy or separate light/dark theme choices.
- System, Light, and Dark modes remain available, along with typography and other appearance controls.
- Supported Android devices use the existing dynamic system palette and wallpaper refresh behavior.
- Devices without dynamic colors retain the existing safe fallback palette.

## Implementation and scope

- `apps/mobile/src/features/settings/appearance/sections/ThemeAppearanceSection.tsx`: remove theme
  cards and their preview-only code; keep color-scheme mode controls.
- `apps/mobile/src/lib/mobileTheme.ts`: resolve all stored theme selections to Material You;
  retain palette definitions and legacy preference types for storage compatibility and development previews.
- `apps/mobile/src/lib/mobileTheme.test.ts`: cover empty, legacy, independent, invalid, and Material You preferences.
- `apps/mobile/src/lib/nativeTheme.test.ts`: retain legacy palette contrast coverage using palette IDs
  rather than the removed theme-picker options.
- `docs/user/appearance.md`: describe the fork's mobile appearance behavior.
- The existing appearance provider applies system palettes and observes wallpaper changes on app focus.
  No theme data migration or unrelated preference rewrite is required.
- This change does not port Android system colors to web/desktop or change Material You Layout settings.

## Guidance for upstream updates

Preserve Material You as the only resolved mobile theme, including before preference hydration.
Removing the picker alone is insufficient: saved choices must never restore another palette.
Keep upstream improvements to Material You, dynamic color refresh, mode switching, and fallback behavior.
An upstream single-theme option is equivalent only if it also overrides old persisted selections.

## Verification

- 2026-10-20: after installing dependencies with `vp i --frozen-lockfile -- --yes`, the standard
  `vp test run` passed 78 tests across `apps/mobile/src/lib/mobileTheme.test.ts`,
  `mobileThemeRuntime.test.ts`, `mobileThemeVariables.test.ts`, and `materialYouTheme.test.ts`.
  A further targeted run of `apps/mobile/src/lib/nativeTheme.test.ts` passed all 12 tests.
- `vp run --filter @t3tools/mobile typecheck` passed. Targeted `vp lint` passed for the changed
  Appearance section and all three changed theme/test files. Commands ran with Node 24 through
  `vp env exec --node 24`.
- Static review confirmed that the appearance provider resolves both modes through the enforced
  selection before hydration, while retaining dynamic-color refresh and mode switching.
- `git diff --check` passed. Bun's TSX transpiler accepted the reduced Appearance section.
- Metro served a complete Android JavaScript bundle successfully (HTTP 200).
- A development Android project generated with `APP_VARIANT=development expo prebuild --platform
android --no-install` built successfully with `./gradlew :app:assembleDebug
-PreactNativeArchitectures=arm64-v8a --max-workers=2`. The resulting
  `com.t3tools.t3code.dev` APK passed `apksigner verify`.
- No device or browser verification has been performed.

## Retirement or replacement

Not applicable; active.

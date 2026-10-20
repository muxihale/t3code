# F004 — Pinned threads cannot settle

- Status: active
- Introduced: 2026-10-20
- Last reviewed against upstream: `962428df104d1958ff58a3b205e64a07117e7781`

## Intention

Max requested that pinned threads cannot be manually settled or auto-settled, and that their
menus must not offer Auto-settle behavior. A pin means keeping the thread available until explicitly unpinned.

## Acceptance criteria

- The server rejects manual and automatic settle commands against a currently pinned thread,
  including stale clients and races with pinning. A rejected settle preserves the pin and thread state.
- Web, desktop, and mobile hide settle and auto-settle menu actions for pinned threads.
- Keyboard, bulk actions, swipes, drag-and-drop, and optimistic client state do not settle pinned threads.
- Unpinning restores ordinary settling and the saved auto-settle preference. Pinning does not overwrite it.
- Existing automatic settlement exclusion remains in place; unpinned threads retain normal behavior.

## Implementation and scope

- `apps/server/src/orchestration-v2/Orchestrator.ts` rejects settling pinned threads before any
  settlement side effects. Automatic settle commands pass through the same mutation guard; the
  existing `ThreadSettlementService.ts` candidate exclusion is retained.
- `apps/web/src/components/threadActionMenu.logic.ts` hides both settlement and auto-settle entries.
  The web action hook and chat shortcut reject pinned targets; sidebar hover actions, bulk selection,
  sweep actions, drop planning, and optimistic drops preserve pins.
- `apps/mobile/src/features/threads/thread-list-v2-items.tsx` hides those menu choices.
  `threadListV2.ts` offers Unpin instead of Settle on pinned-row swipes. The mobile action executor
  and arrangement sheet also block pinned settlement.
- `packages/client-runtime/src/state/threadCommands.ts` does not optimistically settle pinned threads.
  `docs/user/thread-sidebar.md` explains unpinning before settlement and restoration of the saved preference.
- No wire schema or provider changes are needed: the rule applies to thread lifecycle commands.
- Archiving, deleting, and snoozing are outside this change.

## Guidance for upstream updates

Preserve server enforcement as well as UI visibility. Hiding a menu alone cannot protect against
MCP callers, old clients, or commands racing with a pin. Do not restore the old settle-and-unpin behavior.
Retain the saved auto-settle preference so unpinning does not unexpectedly change user settings.

## Verification

- 2026-10-20: focused `vp test run` invocations passed 360 distinct tests across
  `Orchestrator.control-reads.test.ts`, `threadCommands.test.ts`, `threadActionMenu.logic.test.ts`,
  `useThreadActions.undo.test.ts`, `Sidebar.logic.test.ts`, `threadListV2.test.ts`,
  `threadDragGap.test.ts`, and `useThreadListActions.test.ts`.
  Regressions cover direct manual and automatic server commands, unchanged pinned state on rejection,
  successful settling after unpinning, menus, swipe actions, drag plans, stale optimistic drops, and
  retained client callbacks.
- Scoped typechecks passed for `t3`, `@t3tools/client-runtime`, `@t3tools/web`, and `@t3tools/mobile`.
  Targeted lint completed with warnings in existing code; there were no lint errors.
  Commands ran with Node 24 through `vp env exec --node 24`.
- `git diff --check` passed.
- No device or browser verification has been performed.

## Retirement or replacement

Not applicable; active.

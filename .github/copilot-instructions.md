- [ ] Run `npm run lint`
- [ ] Run `npm run build`
- [ ] Run `npm run test`

## Big picture
- The entry point in [src/main.tsx](src/main.tsx) boots `App` inside `StrictMode`, so everything funnels into the single-page bingo experience in [src/App.tsx](src/App.tsx).
- `App` mirrors the `gameState` from [src/hooks/useBingoGame.ts](src/hooks/useBingoGame.ts) to show the [src/components/StartScreen.tsx](src/components/StartScreen.tsx) initially, then [src/components/GameScreen.tsx](src/components/GameScreen.tsx) + [src/components/BingoModal.tsx](src/components/BingoModal.tsx) once gameplay begins.
- [src/components/GameScreen.tsx](src/components/GameScreen.tsx) renders the 5×5 grid [src/components/BingoBoard.tsx](src/components/BingoBoard.tsx) + [src/components/BingoSquare.tsx](src/components/BingoSquare.tsx) stack, passing `winningSquareIds` from the hook so the board highlights completed rows/columns/diagonals.

## State & logic
- `useBingoGame` drives state persistence: it validates `STORAGE_KEY = "bingo-game-state"` via `validateStoredData`, reloads only matching `STORAGE_VERSION`, and re-saves on every value change to keep refreshes warm.
- `startGame` calls `generateBoard()` to seed 24 shuffled questions plus the free center square; `handleSquareClick` toggles via `toggleSquare`, then uses `checkBingo` + `queueMicrotask` to set `winningLine`, `gameState`, and the modal flag without synchronous setState conflicts.
- `winningSquareIds` relies on `getWinningSquareIds` so the visual board can share the same `Set` that the modal and header rely on when `gameState === 'bingo'`.
- Domain types in [src/types/index.ts](src/types/index.ts) keep `GameState` strictly `'start' | 'playing' | 'bingo'` while `BingoLine` enumerates `row`, `column`, `diagonal` for the logic helpers.

## Styling & tokens
- Global CSS in [src/index.css](src/index.css) simply `@import 'tailwindcss'`, defines `@theme` tokens like `--color-accent`, `--color-marked`, and lets `StartScreen`, `GameScreen`, `BingoSquare`, and `BingoModal` lean on classes such as `bg-accent`, `bg-marked`, `border-marked-border`, and `bg-amber-100` without touching a tailwind.config file.
- Follow the Tailwind v4 instructions in [.github/instructions/tailwind-4.instructions.md](.github/instructions/tailwind-4.instructions.md) when adding utilities (tokens, container queries, `@theme inline` overrides) because the build depends entirely on CSS-first configuration.
- Layout-wise, `BingoBoard` uses `grid grid-cols-5 gap-1 w-full max-w-md mx-auto aspect-square`, and `BingoModal` rides `fixed inset-0` + `bg-black/50` to float above the board whenever `showBingoModal` is true.

## Data & customization
- `src/data/questions.ts` holds the 24 question strings plus `FREE_SPACE`; editing this file immediately changes what the board asks, so keep text lean enough to wrap inside `BingoSquare` and avoid overlong sentences (the button uses `wrap-break-word` and `hyphens-auto`).
- The `generateBoard` helper slices the shuffled list to 24 items and always uses index 12 for the locked free square, so custom data must remain ≥24 unique prompts if you want a full board.

## Testing & workflow
- `npm run dev` launches Vite (`vite.config.ts`) for local exploration, `npm run build` runs `tsc -b` + `vite build`, `npm run lint` is the ESLint sweep, and `npm run test` executes the Vitest suite in [src/utils/bingoLogic.test.ts](src/utils/bingoLogic.test.ts).
- Tests exercise `generateBoard`, `toggleSquare`, `checkBingo`, and `getWinningSquareIds`, so keep the deterministic `Math.random` mocks and free-space assumptions intact when adjusting logic.
- [src/test/setup.ts](src/test/setup.ts) wires `@testing-library/jest-dom/matchers` + `cleanup`, so new component tests should rely on those globals instead of redefining matchers.

## Tips & hints
- `BingoSquare` disables the button for the free space and uses `aria-pressed` + a little checkmark badge to keep accessibility predictable, so new variants should preserve those attributes.
- Highlighting the winning squares uses the `isWinning` prop derived from `winningSquareIds` and toggles between `bg-amber-200` and `bg-marked`; avoid mutating `board` directly because Redux-like immutability is enforced throughout the hook and tests.
- When you flip `STORAGE_VERSION`, document the migration, clear stale localStorage in the hook (it already removes mismatched versions), and keep `validateStoredData` in sync with the stored shape.

Feedback welcome—let me know if any section needs clarification before guiding another agent.
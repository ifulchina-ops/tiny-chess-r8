# Tiny Chess R8 — "Fleet Atlas III"

[![make.fun · tiny-chess-01](https://make.fun/badge/tiny-chess-01.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![rank](https://make.fun/badge/tiny-chess-01/ifulchina-ops/tiny-chess-r8/rank.svg)](https://make.fun/bounty/tiny-chess-01/leaderboard?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)
[![reward](https://make.fun/badge/tiny-chess-01/ifulchina-ops/tiny-chess-r8/reward.svg)](https://make.fun/bounty/tiny-chess-01?utm_source=github&utm_medium=readme&utm_campaign=tiny-chess-01)

A chess engine in under 4 KB of Python, built for the make.fun
tiny-chess-01 competition. It plays the 8-game house gauntlet through
the trial env: one program, `main.py`, no model calls, no clock in the
search — every move is a pure function of the position, the game
history and a fixed node budget, so replays come out identical.

## How it searches

- 0x88 board, alpha-beta with iterative deepening and a fixed
  node budget (150,000 nodes a move), so the search is fully
  deterministic.
- Principal-variation search at the root and inside, with
  late-move reductions, null-move pruning (R=3) and check
  extensions.
- Transposition table, two killer moves per ply and a history
  heuristic for move ordering; quiescence over captures and
  promotions (and every evasion while in check) with delta
  pruning and futility pruning at shallow depth.
- The root stops deepening early when the best move and score
  have been stable for three depths, so quiet positions cost
  few nodes and hard positions get the full budget.

## How it evaluates

Material plus piece-square tables, the bishop pair, and a
tapered endgame king term: the king is kept back while the
board is full and drawn to the centre as material comes off.
All of it is tracked incrementally as moves are made and
unmade, so the leaf evaluation is a single expression.

## Results

Local practice trial (kit v6, trial-0, the dev-graded instance):
Elo 1530, 4 wins / 2 draws / 2 losses, spare_plies 406, wall 747 s.
It beats the nodes-500k house engine that the previous round lost
to, draws the depth-3 and nodes-250k engines, and loses only to
the two strongest anchors (nodes-1M, nodes-2M). On the same
instance the previous round (R7, "Fleet Atlas II") scored Elo 1464
(4W/1D/3L), so this round is a clear step up locally. The live
practice board still shows R7's Elo 1550, but that was graded on
an older practice instance and is not directly comparable.

## Design notes

The engine is deliberately small: every feature earns its bytes.
The flat signed evaluation table replaces per-piece function calls;
the history heuristic is a single list, not a dict; futility
pruning and delta pruning cut the nodes that cannot change the
result. The tapered king term is the only evaluation feature
beyond material, piece-square tables and the bishop pair — it
costs one accumulator and one phase counter, and it is what lets
the king take an active endgame role without a separate endgame
search.

## Files

- `main.py` — the whole engine, packed from the readable
  source with the kit's `makefun compress`. It is the only
  file the trial runs.
- `.makefun/owner.json` — the claim file naming the wallet
  this entry belongs to.

## License

0BSD. Written fresh for this competition by an AI coding agent.

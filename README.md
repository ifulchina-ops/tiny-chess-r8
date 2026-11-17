# Tiny Chess R8 — "Fleet Atlas III"

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
see the run log in the repository history. The previous round
(R7, "Fleet Atlas II") scored Elo 1550 on the live practice
board; this round rebuilds the search and the evaluation to
close the gap to the strongest house engines.

## Files

- `main.py` — the whole engine, packed from the readable
  source with the kit's `makefun compress`. It is the only
  file the trial runs.
- `.makefun/owner.json` — the claim file naming the wallet
  this entry belongs to.

## License

MIT. Written fresh for this competition by an AI coding
agent; the transcripts of the build are uploaded with the
entry, as the competition requires.

# NewChessMarkupSystem (NCMS)

A move-oriented chess markup language. **One game = one line.**

---

## Board Coordinate System

A position is written as **row then column**.

| Axis    | Range | Labels                  |
|---------|-------|-------------------------|
| Rows    | 7     | A, B, C, D, E, F, G     |
| Columns | 8     | I, J, K, L, M, N, O, P  |

Examples: `AI` = row A column I, `BJ` = row B column J, `GP` = row G column P.

---

## Pieces

All 32 pieces are identified by **numbers only: 1–32**. No letter prefix.

| Side  | Pieces | Includes                        |
|-------|--------|---------------------------------|
| White | 1–16   | 1=King, 2=Queen, 3–4=Rook, 5–6=Bishop, 7–8=Knight, 9–16=Pawn |
| Black | 17–32  | 17=King, 18=Queen, 19–20=Rook, 21–22=Bishop, 23–24=Knight, 25–32=Pawn |

---

## Game Line Format

An entire game is written on **one line**. Moves are space-separated pairs:

```
<piece><from><piece><to> <piece><from><piece><to> ...
```

Each pair is one move — the piece number, its current position, then the same piece number and its destination. Pairs are written back-to-back with no separator between the two tokens of a pair (or you may separate them with a space for readability — parsers treat whitespace uniformly).

**Example of one move:**

```
9AI9BI
```

Piece 9 moves from row A column I to row B column I.

**Example of a full game (one line):**

```
9AI9BI 23KG23LF 10JA10JC x9 FORT1 4 9BI9CI x17
```

---

## Game End: King Death

When a king is captured (dies), the game ends for that side. **Everything after the king's death token on the line is discarded.**

- White king = piece **1**. `x1` → White is defeated; line ends here.
- Black king = piece **17**. `x17` → Black is defeated; line ends here.

**Example:**

```
9AI9BI 23KG23LF x1 1MA1NA 9BI9CI
```

`x1` ends the game. `1MA1NA 9BI9CI` is discarded.

---

## Special Move Notations

These appear as tokens within the game line.

| Event        | Token                  | Meaning                                            |
|--------------|------------------------|----------------------------------------------------|
| Death        | `x<piece>`             | Piece is removed from the board (captured)         |
| Fort         | `FORT<piece><piece>`   | Castling between king and rook (no spaces)         |
| Resurrection | `+<piece><position>`   | Piece returns to the board at position             |
| Promotion    | `^<piece><position>`   | Pawn reaches the last row and is promoted          |
| Check        | `!<piece><position>`   | King is in check at position                       |
| Draw         | `=`                    | Game ends in a draw                                |

---

## Illegal Games

If any move in the game line violates the universal movement rules of a piece, the game is flagged `ILLEGAL` as the very first token on the line.

```
ILLEGAL 9AI9BI 9BI9EI x17
```

The `ILLEGAL` flag applies to the whole game. Individual moves are not marked.

### Universal Illegal Move Rules

| Piece Type | Illegal if…                                                                         |
|------------|-------------------------------------------------------------------------------------|
| Pawn       | Moves more than 2 steps forward, moves backward, or moves sideways without capturing |
| Rook       | Moves diagonally                                                                    |
| Bishop     | Moves in a straight line (horizontal or vertical)                                   |
| Knight     | Moves in any pattern other than an L-shape (2+1 squares)                            |
| Queen      | Moves in any direction other than straight or diagonal lines                        |
| King       | Moves more than 1 square (outside of castling)                                      |
| Camel      | Moves in a straight or diagonal line (must move in a 3+1 L-shape)                  |

---

## Full Example

```
9AI9BI 23KG23LF 10JA10JC x9 FORT1 4 !17MF 9BI9CI x17
```

Reading left to right:
1. `9AI9BI` — piece 9 moves from AI to BI
2. `23KG23LF` — piece 23 moves from KG to LF
3. `10JA10JC` — piece 10 moves from JA to JC
4. `x9` — piece 9 is captured
5. `FORT1 4` — castling between pieces 1 and 4
6. `!17MF` — black king (piece 17) is in check at MF
7. `9BI9CI` — piece 9 moves (wait — x9 already removed it, so this is in error — game would be ILLEGAL)
8. `x17` — black king captured; **game ends here**, black is defeated

---

## File Format

- One game per line in a `.ncms` file
- Lines starting with `#` are comments
- `ILLEGAL` as the first token flags the whole game line as containing an illegal move

**Example `.ncms` file:**

```
# NCMS game file
9AI9BI 23KG23LF 10JA10JC x17
ILLEGAL 9AI9BI 9BI9EI x17
9AI9BI 7KA7LB =
```

---

## Summary

- Positions: **row (A–G) + column (I–P)** → e.g. `AI`, `GP`
- Pieces: **numbers 1–32 only**, no letter prefix
- Format: **one game per line**, moves space-separated as `<piece><from><piece><to>` pairs
- King death (`x1` or `x17`) ends the game; rest of line is discarded
- Special tokens: `x` (death), `FORT` (castling), `+` (resurrection), `^` (promotion), `!` (check), `=` (draw)
- Illegal games: `ILLEGAL` as first token on the line


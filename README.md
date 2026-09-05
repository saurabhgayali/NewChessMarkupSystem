# NewChessMarkupSystem (NCMS)

A move-oriented chess markup language that records a game as a sequence of piece movements.

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

| Side  | Pieces  |
|-------|---------|
| White | 1–16    |
| Black | 17–32   |

The initial assignment of numbers to piece types is declared in the SETUP block.

---

## Standard Move Format

A single move is two tokens on one line:

```
<piece><from> <piece><to>
```

- `<piece>` — piece number (1–32)
- `<from>` — current position (row + column)
- `<to>` — destination position (row + column)

**Example:**

```
9BI 9BJ
```

Piece 9 moves from row B column I to row B column J.

---

## Game File Structure

```
GAME: <white player> vs <black player>
DATE: YYYY-MM-DD

SETUP:
<number> <type> <position> ...

MOVES:
<move>
<move>
...
```

### SETUP Block

Declares the type and starting position of every piece. `<type>` is informational (King, Queen, Rook, Bishop, Knight, Pawn).

```
SETUP:
1 King   MA   17 King   MG
2 Queen  LA   18 Queen  LG
3 Rook   IA   19 Rook   IG
4 Rook   PA   20 Rook   PG
5 Bishop JA   21 Bishop JG
6 Bishop OA   22 Bishop OG
7 Knight KA   23 Knight KG
8 Knight NA   24 Knight NG
9 Pawn   IA   25 Pawn   IG
10 Pawn  JA   26 Pawn   JG
11 Pawn  KA   27 Pawn   KG
12 Pawn  LA   28 Pawn   LG
13 Pawn  MA   29 Pawn   MG
14 Pawn  NA   30 Pawn   NG
15 Pawn  OA   31 Pawn   OG
16 Pawn  PA   32 Pawn   PG
```

### MOVES Block

Each line is one move.

```
MOVES:
9BI 9BJ
23KG 23LF
10JA 10JC
```

---

## Special Move Notations

| Event       | Syntax              | Meaning                                         |
|-------------|---------------------|-------------------------------------------------|
| Death       | `x<piece>`          | Piece is removed from the board (captured)      |
| Fort        | `FORT <piece> <piece>` | Castling between king and rook               |
| Resurrection | `+<piece><position>` | Piece returns to the board at position        |
| Promotion   | `^<piece><position>` | Pawn reaches the last row and is promoted      |
| Check       | `!<piece><position>` | King is in check at position                  |
| Checkmate   | `#<piece><position>` | Checkmate; game ends                          |
| Draw        | `=`                 | Game ends in a draw                             |

### Examples

```
# Piece 9 is captured
x9

# Castling: piece 1 (king) and piece 4 (rook)
FORT 1 4

# Piece 9 resurrected at row A column I
+9AI

# Piece 9 promotes at row G column I
^9GI

# Piece 1 (king) is in check at row B column N
!1BN

# Checkmate — piece 1 at row B column N
#1BN
```

---

## Full Example

```
GAME: Alice vs Bob
DATE: 2026-09-05

SETUP:
1 King   MA   17 King   MG
2 Queen  LA   18 Queen  LG
3 Rook   IA   19 Rook   IG
4 Rook   PA   20 Rook   PG
5 Bishop JA   21 Bishop JG
6 Bishop OA   22 Bishop OG
7 Knight KA   23 Knight KG
8 Knight NA   24 Knight NG
9 Pawn   IA   25 Pawn   IG
10 Pawn  JA   26 Pawn   JG
11 Pawn  KA   27 Pawn   KG
12 Pawn  LA   28 Pawn   LG
13 Pawn  MA   29 Pawn   MG
14 Pawn  NA   30 Pawn   NG
15 Pawn  OA   31 Pawn   OG
16 Pawn  PA   32 Pawn   PG

MOVES:
9AI 9BI
23KG 23LF
10JA 10JC
x9
FORT 1 4
!1NB
#1NB
```

---

## Illegal Move Notation

An illegal move is recorded with a `?` prefix before the move token. Illegal moves are **universal** — they apply regardless of the game variant and describe moves that violate the fundamental movement rules of a piece type.

### Syntax

```
?<piece><from> <piece><to>
```

The `?` marks the attempted move as illegal. Only the first token (the from-position) carries the `?`; the second token is the intended destination.

### Universal Illegal Move Rules

| Piece Type | Illegal if…                                                         |
|------------|---------------------------------------------------------------------|
| Pawn       | Moves more than 2 steps forward, moves backward, or moves sideways without capturing |
| Rook       | Moves diagonally                                                    |
| Bishop     | Moves in a straight line (horizontal or vertical)                   |
| Knight     | Moves in any pattern other than an L-shape (2+1 squares)            |
| Queen      | Moves in any direction other than straight or diagonal lines        |
| King       | Moves more than 1 square (outside of castling)                      |
| Camel      | Moves in a straight or diagonal line (must move in a 3+1 L-shape)  |

> Any piece type beyond the standard six can have its illegal patterns described the same way.

### Examples

```
# Pawn 9 tries to move 3 steps forward — illegal
?9AI 9DI

# Camel moves in a straight line — illegal
?7KA 7KD

# Bishop tries to move straight — illegal
?5JA 5JD

# King tries to move 3 squares — illegal
?1MA 1MD
```

### Combining with Other Notations

An illegal move can also be combined with a special event if the illegality is detected during a special action:

```
# Illegal castling attempt
?FORT 1 4
```

---

## File Extension

Save game files with the `.ncms` extension.

---

## Summary

- Positions: **row (A–G) + column (I–P)** → e.g. `AI`, `GP`
- Pieces: **numbers 1–32 only** (no letter prefix)
- Moves: **`<piece><from> <piece><to>`** → e.g. `9BI 9BJ`
- Special events: `x` (death), `FORT` (castling), `+` (resurrection), `^` (promotion), `!` (check), `#` (checkmate), `=` (draw)
- Illegal moves: `?<piece><from> <piece><to>` — universal, piece-type-based movement violations

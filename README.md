# NewChessMarkupSystem (NCMS)

A move-oriented chess markup language that records a game as a sequence of piece movements.

---

## Board Coordinate System

The board uses a **column-row** coordinate format.

| Axis    | Range | Labels              |
|---------|-------|---------------------|
| Columns | 8     | I, J, K, L, M, N, O, P |
| Rows    | 7     | A, B, C, D, E, F, G    |

A position is written as **column then row**: `IA`, `PG`, `LF`, etc.

---

## Piece Numbering

All 32 pieces are numbered **1–32**. Each piece has a type prefix and a unique number.

| Type   | Prefix | Numbers      |
|--------|--------|--------------|
| King   | K      | 1, 17        |
| Queen  | Q      | 2, 18        |
| Rook   | R      | 3, 4, 19, 20 |
| Bishop | B      | 5, 6, 21, 22 |
| Knight | N      | 7, 8, 23, 24 |
| Pawn   | P      | 9–16, 25–32  |

Example piece identifiers: `K1`, `Q2`, `R3`, `R4`, `B5`, `B6`, `N7`, `N8`, `P9`–`P16` (White); `K17`, `Q18`, `R19`, `R20`, `B21`, `B22`, `N23`, `N24`, `P25`–`P32` (Black).

---

## Standard Move Format

A move is written as:

```
PIECE-FROM PIECE-TO
```

- `PIECE` — piece identifier (e.g. `P9`)
- `FROM` — current board position (column + row, e.g. `IA`)
- `TO` — destination board position

**Example:**

```
P9-IA P9-IB
```

Pawn 9 moves from column I, row A to column I, row B.

---

## Game File Structure

```
GAME: <white player> vs <black player>
DATE: YYYY-MM-DD

SETUP:
<piece>-<position> ...

MOVES:
<move>
<move>
...
```

### SETUP Block

Lists every piece and its starting position. One line is fine; split across lines for readability.

```
SETUP:
K1-MA  Q2-LA  R3-IA  R4-PA  B5-JA  B6-OA  N7-KA  N8-NA
P9-IA2 P10-JA ...
```

> Note: If your variant uses a different starting layout, just list the actual positions here.

### MOVES Block

Each line is one move in `PIECE-FROM PIECE-TO` format.

```
MOVES:
P9-IB P9-IC
N7-KA N7-LB
P10-JB P10-JC
```

---

## Special Move Notations

| Event        | Syntax                    | Meaning                                      |
|--------------|---------------------------|----------------------------------------------|
| Capture/Death | `xPIECE`                 | Piece is removed from the board              |
| Fort (Castle) | `FORT K1 R3`             | Castling between king and the named rook     |
| Resurrection  | `+PIECE-POSITION`        | Piece returns to the board at position       |
| Promotion     | `^PIECE-POSITION=NEWTYPE` | Pawn promotes; new type takes over the number |
| Check         | `!KPIECE-POSITION`       | King is in check at position                 |
| Checkmate     | `#KPIECE-POSITION`       | Checkmate; game ends                         |
| Draw          | `=`                       | Game ends in a draw                          |

### Examples

```
# Capture: Black captures White pawn 9
xP9

# Castling: White king-side
FORT K1 R4

# Resurrection of piece 9 at column I row A
+P9-IA

# Pawn P9 promotes at IG to a Queen (takes over as a new Queen)
^P9-IG=Q

# Check on black king
!K17-MF

# Checkmate
#K17-MF
```

---

## Full Example

```
GAME: Alice vs Bob
DATE: 2026-09-05

SETUP:
K1-MA  Q2-LA  R3-IA  R4-PA  B5-JA  B6-OA  N7-KA  N8-NA
P9-IA  P10-JA P11-KA P12-LA P13-MA P14-NA P15-OA P16-PA
K17-MG Q18-LG R19-IG R20-PG B21-JG B22-OG N23-KG N24-NG
P25-IG P26-JG P27-KG P28-LG P29-MG P30-NG P31-OG P32-PG

MOVES:
P9-IA  P9-IB
N23-KG N23-LF
P10-JA P10-JC
xP9
FORT K1 R4
!K1-NB
#K1-NB
```

---

## File Extension

Save game files with the `.ncms` extension.

---

## Summary

- Positions: **column (I–P) + row (A–G)** → e.g. `IA`, `PG`
- Pieces: **type prefix + number 1–32** → e.g. `K1`, `P25`
- Moves: **`PIECE-FROM PIECE-TO`** on each line
- Special events (capture, fort, resurrection, promotion, check, checkmate) use dedicated keywords

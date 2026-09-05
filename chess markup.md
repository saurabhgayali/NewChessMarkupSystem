# Chess Markup System

A plain-text markup language for representing chess games, positions, and annotations.

---

## 1. Overview

Chess Markup is a lightweight, human-readable format for writing chess content in plain text. It supports:

- Board positions (FEN-based)
- Move sequences (SAN / algebraic notation)
- Inline annotations and comments
- Diagrams
- Variations and sidelines

---

## 2. File Extension

`.chessmark` or `.cm`

---

## 3. Syntax

### 3.1 Position Block

Defines a board position using FEN notation.

```
[position: <FEN>]
```

**Example:**

```
[position: rnbqkbnr/pppppppp/8/8/4P3/8/PPPP1PPP/RNBQKBNR b KQkq e3 0 1]
```

If omitted, the starting position is assumed.

---

### 3.2 Move Sequence

Moves are written in Standard Algebraic Notation (SAN), one after another, with move numbers:

```
1. e4 e5 2. Nf3 Nc6 3. Bb5 a6
```

- White moves follow the move number.
- Black moves follow without a repeated move number.
- A `...` prefix can be used to start a sequence from Black's move:

```
3... Nf6
```

---

### 3.3 Comments

Inline comments are enclosed in curly braces `{ }`:

```
1. e4 { The King's Pawn opening } e5 { The symmetric response }
```

Multi-line comments are also allowed:

```
1. e4 {
  One of the most popular first moves.
  Controls the center.
} e5
```

---

### 3.4 Annotations (Symbols)

Annotation symbols follow a move directly:

| Symbol | Meaning           |
|--------|-------------------|
| `!`    | Good move         |
| `!!`   | Brilliant move    |
| `?`    | Mistake           |
| `??`   | Blunder           |
| `!?`   | Interesting move  |
| `?!`   | Dubious move      |

**Example:**

```
15. Rxf7!! { Sacrificing the rook for a decisive attack }
```

---

### 3.5 Variations (Sidelines)

Variations are enclosed in parentheses `( )`:

```
1. e4 e5 2. Nf3 Nc6 (2... d6 { The Philidor Defence }) 3. Bb5
```

Variations can be nested:

```
3. Bb5 a6 (3... Nf6 (3... d6 4. d4) 4. O-O) 4. Ba4
```

---

### 3.6 Diagram Marker

Insert a diagram at the current position with:

```
[diagram]
```

Renders the board at that point in the game for visual reference.

---

### 3.7 Metadata Header

Metadata is declared at the top of the file using bracketed key-value pairs, similar to PGN tags:

```
[Event: "World Championship 2024"]
[Site: "New York"]
[Date: "2024.11.15"]
[White: "Carlsen, Magnus"]
[Black: "Nepomniachtchi, Ian"]
[Result: "1-0"]
```

**Supported metadata keys:**

| Key      | Description                  |
|----------|------------------------------|
| Event    | Tournament or match name     |
| Site     | Location                     |
| Date     | Date (YYYY.MM.DD)            |
| Round    | Round number                 |
| White    | White player's name          |
| Black    | Black player's name          |
| Result   | `1-0`, `0-1`, `1/2-1/2`, `*` |
| ECO      | Opening code (e.g., `C65`)   |
| Opening  | Opening name                 |

---

### 3.8 Result

The game result appears at the end of the move sequence:

```
35. Qxh7# 1-0
```

Valid results:

- `1-0` — White wins
- `0-1` — Black wins
- `1/2-1/2` — Draw
- `*` — Game in progress or unknown

---

## 4. Full Example

```
[Event: "Example Game"]
[White: "Alice"]
[Black: "Bob"]
[Result: "1-0"]

1. e4 { King's Pawn } e5
2. Nf3 Nc6
3. Bb5 { Ruy Lopez } a6
  (3... Nf6 { Berlin Defence } 4. O-O Nxe4 5. d4)
4. Ba4 Nf6
5. O-O Be7
[diagram]
6. Re1 b5 7. Bb3 d6 8. c3 O-O
9. h3 Na5 10. Bc2 c5 11. d4 Qc7

{ The main line of the Ruy Lopez Open Defence begins here. }

12. Nbd2 cxd4 13. cxd4 Nc6 14. Nb3 a5

1-0
```

---

## 5. Encoding

Files must be encoded in **UTF-8**.

---

## 6. Compatibility

Chess Markup is designed to be a superset of PGN (Portable Game Notation). Any valid PGN file is valid Chess Markup. Chess Markup files can be exported to PGN by stripping Chess Markup-specific extensions.

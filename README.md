# Tetris

Implementation of the classic **Tetris** game in vanilla JavaScript, using HTML5 Canvas and CSS. No external dependencies, no frameworks, and no build process—just open it and play.

> **Copied from [@angelo-zar/claude-tetris](https://github.com/angelo-zar/claude-tetris).**

![Tech](https://img.shields.io/badge/HTML5-Canvas-orange)
![Tech](https://img.shields.io/badge/CSS3-blueviolet)
![Tech](https://img.shields.io/badge/JavaScript-Vanilla-yellow)

---

## Table of Contents

- [Tetris](#tetris)
  - [Table of Contents](#table-of-contents)
  - [What the Project Does](#what-the-project-does)
  - [How to Run the Game](#how-to-run-the-game)
    - [Option 1: Open the File Directly](#option-1-open-the-file-directly)
    - [Option 2: Local Server Recommended](#option-2-local-server-recommended)
  - [Controls](#controls)
  - [How It Works](#how-it-works)
    - [`index.html`](#1-indexhtml)
    - [`style.css`](#2-stylecss)
    - [`game.js`](#3-gamejs)
    - [Game Flow](#game-flow)
  - [Technologies](#technologies)
  - [Project Structure](#project-structure)
  - [Customization](#customization)
  - [License](#license)

---

## What the Project Does

This is a playable version of classic Tetris with all the mechanics you would expect:

- A **10 × 20** grid.
- All **7 standard pieces** — I, O, T, S, Z, J, and L — with different colors.
- Rotation with basic _wall kicks_ — small adjustments that allow a piece to rotate near a wall.
- **Soft drop** and **hard drop**.
- A **ghost piece** that shows where the current piece will land.
- A preview of the next piece.
- A classic Tetris scoring system: 100 / 300 / 500 / 800 multiplied by the current level.
- Levels that increase every 10 cleared lines and speed up the falling pieces.
- Pause and Game Over screens with a restart option.

---

## How to Run the Game

There is nothing to install or compile. You have two options:

### Option 1: Open the File Directly

```bash
open index.html        # macOS
xdg-open index.html    # Linux
start index.html       # Windows
```

### Option 2: Local Server Recommended

Any static server will work. Here are some examples:

```bash
# With Python 3
python3 -m http.server 8000

# With Node.js using npx
npx serve .

# With PHP
php -S localhost:8000
```

Then open `http://localhost:8000` in your browser.

---

## Controls

| Key       | Action                         |
| --------- | ------------------------------ |
| `←` / `→` | Move the piece horizontally    |
| `↑` or `X` | Rotate the piece clockwise     |
| `↓`       | Soft drop                      |
| `Space`   | Hard drop                      |
| `P`       | Pause / resume                 |

---

## How It Works

The game consists of three files that work together:

### 1. `index.html`

Defines the visual structure:

- A `<canvas id="board">` measuring **300 × 600** pixels where the game board is rendered.
- A side panel containing `SCORE`, `LINES`, and `LEVEL`, a next-piece preview, and the controls list.
- An overlay for the **PAUSED** and **GAME OVER** states.

### 2. `style.css`

Provides the visual appearance with a _dark / retro arcade_ style, including a monospaced font for the score displays and _backdrop blur_ effects in the overlays.

### 3. `game.js`

Contains all of the game logic. In general:

- **Board model**: a `ROWS × COLS` matrix where each cell contains `0` for empty or a color index from `1–7` identifying the piece.
- **Pieces**: defined as square matrices. To rotate a piece, the game calculates the transpose and then reverses the rows using `rotateCW`.
- **Collision detection** (`collide`): checks that no part of the piece goes outside the board or overlaps blocks that are already locked in place.
- **Wall kicks** (`tryRotate`): if a rotation collides, the game attempts to move the piece by ±1 or ±2 columns before cancelling the rotation.
- **Game loop** (`loop`): based on `requestAnimationFrame`, it accumulates elapsed time and moves the piece down one row when `dropInterval` is reached.
- **Line clearing** (`clearLines`): scans the board from bottom to top; each completed row is removed and an empty row is inserted at the top.
- **Scoring**: uses the classic scoring table `[0, 100, 300, 500, 800]`, multiplied by the current level. A hard drop adds 2 points per cell traveled, while a soft drop adds 1 point per row.
- **Level and speed**: the level increases every 10 lines. The falling speed is calculated as `max(100, 1000 − (level − 1) × 90)` milliseconds.
- **Ghost piece** (`ghostY`): projects the final position of the current piece downward and draws it with `globalAlpha = 0.2`.

### Game Flow

```text
init()
  ├─ createBoard()                  → creates an empty matrix
  ├─ next = randomPiece()
  ├─ spawn()                        → moves next into current and generates a new next piece
  └─ requestAnimationFrame(loop)
        ↓
   loop(timestamp)
     ├─ accumulates dt
     ├─ if dt ≥ dropInterval → moves the piece down or calls lockPiece()
     ├─ draw()  (grid + board + ghost + current piece)
     └─ requestAnimationFrame(loop)

   keydown → move / rotate / soft drop / hard drop / pause
```

When a newly generated piece immediately collides with the board while spawning, `spawn()` calls `endGame()` and displays the **Game Over** overlay.

---

## Technologies

- **HTML5** — markup and two `<canvas>` elements for the board and next-piece preview.
- **CSS3** — flexbox, color variables, `backdrop-filter`, and `box-shadow`.
- **Vanilla JavaScript ES6+** — `const`/`let`, arrow functions, spread syntax, `Array.from`, template literals, and more.
- **Canvas 2D API** — used for all game rendering.
- **`requestAnimationFrame`** — for a browser-synchronized game loop.

**No dependencies.** There is no `package.json`, bundler, or transpiler.

---

## Project Structure

```text
03-tetris/
├── index.html      # DOM structure and canvas
├── style.css       # Game styles and dark theme
├── game.js         # All Tetris logic, approximately 300 lines
└── README.md
```

---

## Customization

Some easy-to-adjust parameters are located at the top of `game.js`:

| Constant      | Meaning                              | Default                 |
| ------------- | ------------------------------------ | ----------------------- |
| `COLS`        | Number of board columns              | `10`                    |
| `ROWS`        | Number of board rows                 | `20`                    |
| `BLOCK`       | Cell size in pixels                  | `30`                    |
| `COLORS`      | Color palette for each piece type   | 7 colors                |
| `LINE_SCORES` | Points for clearing 1–4 lines       | `[0,100,300,500,800]`   |
| `dropInterval` | Initial falling speed in milliseconds | `1000`                |

> If you change `COLS`, `ROWS`, or `BLOCK`, remember to also update the `width` and `height` of the `<canvas id="board">` in `index.html` so they match: `COLS × BLOCK` by `ROWS × BLOCK`.

---

## License

Free-to-use project for educational and practice purposes.

---

**Attribution:** This README and project description were copied and translated from [@angelo-zar/claude-tetris](https://github.com/angelo-zar/claude-tetris).

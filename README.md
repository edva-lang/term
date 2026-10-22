# term

High-performance, zero-flicker terminal and TUI library for [Edva](https://github.com/edva-lang/edva).

## Features

- **Raw Terminal Mode**: Clean POSIX `termios` raw mode management with automatic alternate screen buffer (`\e[?1049h`), cursor control, and signal safety.
- **Modern Keyboard Input**: Complete support for ANSI escapes, VT220/xterm function keys (F1–F12), navigation keys (arrows, Home, End, PageUp, PageDown), and progressive keyboard enhancements (Kitty CSI `u` and `modifyOtherKeys`).
- **Mouse Tracking**: Full SGR 1006 mouse tracking support (press, release, drag, scroll wheel, and modifier combinations).
- **Dark/Light Scheme Detection**: Automatic terminal color-scheme detection and live change notifications via `CSI ? 996 n` / DEC Mode `2031` (`\e[?997;1n` / `\e[?997;2n`) and `OSC 11` background RGB luminance (`\e]11;?\e\\`).
- **Double-Buffered Renderer**: Differential zero-flicker grid rendering (`Grid`) with TrueColor 24-bit RGB and 256-color ANSI support.
- **UTF-8 & Unicode Ready**: Full multi-byte UTF-8 rune decoding, glyph measurements, and Unicode box drawing characters (single, double, rounded, heavy, ASCII).
- **Zero Allocations in Hot Paths**: Packed 16-byte `Cell` representation and stack/arena-oriented rendering routines.

## Installation

Add to your `edva.ccl`:

```ccl
dependencies =
  term =
    git = "https://github.com/edva-lang/term"
    rev = "v0.1.0"
```

## Quick Start

```edva
#use "term"

main: () => Int
main = !=>
   // Enter raw mode and alternate screen
   term::init!
   
   // Create double-buffered grid for terminal dimensions
   size = term::terminal_size!
   cols = size.0
   rows = size.1
   g = term::grid_new(cols, rows)
   
   // Draw styled text and box borders
   term::grid_clear(g)
   term::draw_box(g, 2, 2, cols - 4, rows - 4, term::BOX_ROUNDED, term::style_fg(0x89b4fa))
   term::grid_write(g, 4, 3, "Hello from Edva Terminal!", term::style_bold(term::style_fg(0xa6e3a1)))
   
   // Flush to screen (computes delta against previous frame)
   term::grid_render(g)
   
   // Event loop
   0.. @
      ev = term::read_event(50) // 50ms timeout
      ev.kind
         [term::EV_KEY]
            ev.code
               [term::KEY_ESC, 'q'] -->
         |
   
   // Restore terminal state
   term::shutdown!
   0
```

## Architecture

- [`src/raw.dva`](src/raw.dva): POSIX termios raw mode, alternate screen buffer, cursor visibility, and progressive keyboard protocols.
- [`src/key.dva`](src/key.dva): Key event types, key codes, modifiers, and mouse event structures.
- [`src/input.dva`](src/input.dva): Non-blocking ANSI/CSI escape sequence parser and UTF-8 decoder.
- [`src/style.dva`](src/style.dva): TrueColor RGB colors, ANSI colors, and text attributes (bold, dim, italic, underline, reverse).
- [`src/grid.dva`](src/grid.dva): Double-buffered screen grid with differential ANSI emitter and Unicode box drawing.
- [`src/term.dva`](src/term.dva): Top-level unified public API.

## License

BSD-2-Clause

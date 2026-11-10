# term — Zero-Flicker Terminal & TUI Library for Edva

`term` is a high-performance, zero-flicker terminal and Text User Interface (TUI) library for [Edva](https://gitea.gde.to/ike/dva). It powers interactive terminal applications such as [`vade`](../vade/README.md).

---

## Features

- **Raw Terminal Lifecycle**: Clean POSIX `termios` raw mode management (`enable_raw`, `disable_raw`, `shutdown`) with alternate screen buffer (`\e[?1049h`), cursor shape/visibility control, window title setting, and `OSC 52` system clipboard copy.
- **Modern Keyboard Input**: Full support for ANSI/VT220/xterm sequences, function keys (`KEY_F1`–`KEY_F12`), navigation keys, and progressive keyboard protocols.
- **SGR 1006 Mouse Tracking**: Press, release, drag, scroll wheel (`MOUSE_SCROLL_UP`, `MOUSE_SCROLL_DOWN`), and modifier combinations.
- **Automatic Dark/Light Theme Detection**: Queries terminal color scheme via `CSI ? 996 n` / DEC Mode `2031` and `OSC 11` background RGB luminance (`detect_theme`, `EV_THEME`, `is_dark`, `bg_color`).
- **Differential Double-Buffered Renderer**: Compares `front` and `back` cell grids (`render`) and emits minimal ANSI/TrueColor 24-bit RGB escape sequences for zero-flicker updates.
- **Unicode Box Drawing & Subgrid Windows**: Built-in `BORDER_SINGLE`, `BORDER_ROUNDED`, `BORDER_DOUBLE`, and `BORDER_ASCII` frames, aligned/clipped string writers, and clipped `Window` viewports (`create_window`).
- **Built-in Color Palettes**: Re-exports `std/color/palette` themes (`TOKYONIGHT`, `CATPPUCCIN`, `NORD`, `MONOKAI`, `SOLARIZED_DARK`, `SOLARIZED_LIGHT`, `GRUVBOX_DARK`, `ONE_DARK`, `DRACULA`, `ROSE_PINE`) and color manipulation helpers (`rgb`, `blend`, `lighten`, `darken`, `grayscale`).

---

## Installation

Add `term` to your `edva.ccl`:

```ccl
dependencies =
  term =
    path = ../term
```

---

## Quick Start

```dva
#use @term

main: () => Int
main = !=>
   t = term::init!
   term::enable_raw(t)
   term::enable_mouse(t)
   term::hide_cursor(t)

   pal = term::TOKYONIGHT

   0.. @
      // Clear back buffer and draw UI
      term::clear(t)
      term::fill_rect(t, 0, 0, t.rows, t.cols, '\s, pal.fg, pal.bg, term::ATTR_NONE)
      term::draw_titled_box(
         t, 1, 2, t.rows - 2, t.cols - 4,
         term::BORDER_ROUNDED, " Edva Terminal ",
         pal.border, pal.bg, pal.accent
      )
      term::put_str(
         t, 3, 5, "Press 'q' or Esc to quit.",
         pal.green, pal.bg, term::ATTR_BOLD
      )

      // Flush differential update to terminal
      term::render(t)

      // Poll keyboard, mouse, resize, and live theme events (50ms timeout)
      ev = term::poll_event(t, 50)
      ev.kind == term::EV_KEY && (ev.code == term::KEY_ESC || ev.ch == 'q) | -->

   term::shutdown(t)
   0
```

---

## Architecture

- [`src/raw.dva`](src/raw.dva): POSIX `termios` raw mode, alternate screen buffer, cursor shapes, `OSC 52` clipboard, and window size ioctls.
- [`src/key.dva`](src/key.dva): `Event` record (`kind`, `code`, `ch`, `mods`, `x`, `y`), key constants, mouse buttons/actions, and theme event constants.
- [`src/input.dva`](src/input.dva): Non-blocking ANSI/CSI escape sequence parser, UTF-8 rune decoder, and `CSI ? 996 n` / `OSC 11` theme detector.
- [`src/style.dva`](src/style.dva): 24-bit TrueColor RGB helpers, text attributes (`ATTR_BOLD`, `ATTR_DIM`, `ATTR_ITALIC`, `ATTR_UNDERLINE`, `ATTR_REVERSE`), border styles, and `Palette` presets.
- [`src/grid.dva`](src/grid.dva): Double-buffered `Grid` and subgrid `Window` with differential ANSI rendering and Unicode box drawing.
- [`src/term.dva`](src/term.dva): Unified top-level public API (`Term`).

---

## Running Tests

```bash
cd ~/Z/edva-ware/term
edva test
```

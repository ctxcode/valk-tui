# valk-tui

Pure-Valk terminal UI library built on top of stdlib `valk.ansi` — with raw mode, ANSI/xterm/kitty input parsing, double-buffered diffed rendering, a constraint layout solver, core widgets, and a headless `TestBackend`.

Requires Valk 0.9.1 or newer.

## Install

```
vman install github.com/zikue/valk-tui
```

## Quick start

```rust
use tui

class MyApp is tui.App {
    items: tui.List
    progress: tui.Gauge

    static fn new() MyApp {
        return MyApp {
            items: tui.List {
                items: Array[String]{ "Overview", "Metrics", "Logs" }
                selected: 0
                block: tui.Block { title: " Menu ", borders: .rounded }
            }
            progress: tui.Gauge.percent(65)
        }
    }

    + fn update(event: tui.Event) bool {
        let key = event.as_key()
        if isset(key) {
            if key.is_char("q") || key.is_ctrl('c') : return false
            if key.key == .down : this.items.select_next()
            if key.key == .up : this.items.select_prev()
        }
        return true
    }

    + fn view(frame: tui.Frame) {
        let chunks = tui.Layout.vertical(Array[tui.Constraint]{
            tui.Constraint.fill(1),
            tui.Constraint.fixed(3)
        }).split(frame.area)

        frame.render(this.items, chunks[0])
        this.progress.block = tui.Block { title: " Progress ", borders: .plain }
        frame.render(this.progress, chunks[1])
    }
}

fn main() {
    let term = tui.Terminal.stdio()
    let app = MyApp.new()
    tui.run_app(term, app) ! panic(E.message)
}
```

## Features

- **Terminal Control (`terminal.valk`)**:
  - Cross-platform raw mode setup and restoration (`termios` + `ioctl(TIOCGWINSZ)` + `SIGWINCH` on Linux/macOS; Win32 virtual terminal console APIs on Windows).
  - Alternate screen, cursor hide/show, SGR 1006 mouse tracking, bracketed paste, and idempotent `close()` cleanup compatible with `defer`.
  - Pluggable `Backend` interface with `AnsiBackend` (real TTY) and `TestBackend` (in-memory headless backend).
- **Input Event Parser (`event.valk`)**:
  - Incremental `EventParser` decoding UTF-8 text, control combinations, Alt+key, arrows, Home/End/PageUp/PageDown/Insert/Delete, F1–F12, CSI modifier parameters, Kitty keyboard protocol, SGR 1006 mouse events (down/up/drag/move/scroll), and bracketed paste.
  - Typed `Event` union (`KeyEvent | MouseEvent | ResizeEvent | PasteEvent | TickEvent`).
- **Cell Buffer & Diffed Renderer (`buffer.valk`, `style.valk`)**:
  - `Color` (default, 16 ANSI, 256-color palette, 24-bit RGB) and `Style` (fg, bg, bold, dim, italic, underline, reverse, strikethrough) with minimal SGR delta emission.
  - `Buffer` with bounds clipping and wide-character (CJK / emoji) handling.
  - Double-buffered `Terminal.draw()` that flushes only changed cells in a single write and performs a full clear on resize.
- **Layout Engine (`layout.valk`)**:
  - Horizontal and vertical `Layout` solver supporting `fixed`, `percent`, `min`, `max`, and weighted `fill` constraints with `gap` and `Margin`.
- **Core Widgets & Event Loop (`widgets.valk`, `app.valk`)**:
  - `Block` (`none`, `plain`, `rounded`, `double`, `thick` borders, aligned title, padding).
  - `Paragraph` (word wrapping, alignment, vertical scrolling).
  - `List` (selection, highlight style & symbol, auto-scrolling viewport).
  - `Table` (headers, `Constraint` column widths, row selection, scrolling).
  - `Gauge` / `ProgressBar` (smooth 1/8th Unicode block fill `▏▎▍▌▋▊▉█`, centered label).
  - `Tabs` (titles, divider, active tab highlight).
  - `TextInput` (editable UTF-8 single-line input with cursor movement, horizontal scroll, placeholder, and key/paste handlers).
  - `App` interface and `run_app()` Elm/MVC loop with tick interval support.

## Headless testing

Use `TestBackend` to test layouts, widgets, diff output, and full `App` loops deterministically without a TTY:

```rust
let backend = tui.TestBackend.new(40, 10)
backend.push_input("\x1b[B") // Down arrow
let term = tui.Terminal.new(backend)
term.draw(fn(f: tui.Frame) {
    f.render(tui.Paragraph.new("Hello, Valk!"), f.area)
}) ! panic(E.message)
assert(term.buffer.row_text(0).starts_with("Hello, Valk!"))
```

## Example

Run the included dashboard demo (pass `--demo` for a headless snapshot):

```
valk build ./example -o ./debug/example && ./debug/example --demo
```

Or run interactively in a terminal with `make example`.

## API documentation

Generated API reference is available in [docs/api.md](docs/api.md) and [docs/api-full.md](docs/api-full.md).

## Development

`make test` runs the test suite, `make lint` runs the linter with `--deny-warnings`, `make example` builds and runs the example, and `make docs` regenerates the API documentation.


# Documentation

Namespaces: [main](#main)

---

# main

## Aliases for 'main'

```js
alias ProgressBar for Gauge
```

## Enums for 'main'

```js
// Horizontal text alignment used by `Block` titles and `Paragraph` lines.
+ enum Alignment { left, center, right }
// Box-drawing border style for a `Block`.
+ enum BorderType { none, plain, rounded, double, thick }
// The representation of a terminal color.
+ enum ColorKind { reset, ansi, indexed, rgb }
// Kind of sizing rule for a `Constraint`.
+ enum ConstraintKind { fixed, percent, min, max, fill }
// Axis along which a `Layout` splits an area.
+ enum Direction { horizontal, vertical }
// Logical key codes produced by the terminal input parser.
+ enum Key { char, enter, tab, backtab, backspace, delete, esc, up, down, left, right, home, end, page_up, page_down, insert, f1, f2, f3, f4, f5, f6, f7, f8, f9, f10, f11, f12, unknown }
// Mouse button associated with a `MouseEvent`.
+ enum MouseButton { left, middle, right, none }
// Kind of mouse action in a `MouseEvent`.
+ enum MouseKind { down, up, drag, move, scroll_up, scroll_down }
```

### Alignment

Horizontal text alignment used by `Block` titles and `Paragraph` lines.

### BorderType

Box-drawing border style for a `Block`.

### ColorKind

The representation of a terminal color.

### ConstraintKind

Kind of sizing rule for a `Constraint`.

### Direction

Axis along which a `Layout` splits an area.

### Key

Logical key codes produced by the terminal input parser.

### MouseButton

Mouse button associated with a `MouseEvent`.

### MouseKind

Kind of mouse action in a `MouseEvent`.

## Functions for 'main'

```js
// Returns whether stdout supports ANSI escape codes (delegates to `valk.ansi.supported()`).
+ fn ansi_supported() bool
// Returns the terminal display width in columns of a Unicode codepoint: `0`, `1`, or `2`.
+ fn char_width(cp: u32) uint
// Runs an Elm/MVC-style `App` loop on `term`, handling events, periodic ticks, and diffed redraws.
+ fn run_app(term: Terminal, app: App, options: AppOptions (AppOptions{})) void !TuiError
// Returns the terminal display width in columns of a UTF-8 string.
+ fn string_width(s: String) uint
// Decodes a single UTF-8 codepoint starting at byte `offset` in `s`.
+ fn utf8_decode_at(s: String, offset: uint) (u32, uint)
// Encodes a Unicode codepoint as a UTF-8 string.
+ fn utf8_encode(cp: u32) String
// Returns whether console output expects UTF-8 (delegates to `valk.ansi.utf8_supported()`).
+ fn utf8_supported() bool
```

### ansi_supported

Returns whether stdout supports ANSI escape codes (delegates to `valk.ansi.supported()`).

### char_width

Returns the terminal display width in columns of a Unicode codepoint: `0`, `1`, or `2`.

### run_app

Runs an Elm/MVC-style `App` loop on `term`, handling events, periodic ticks, and diffed redraws.

When `options.manage_terminal` is `true` (the default), calls `term.setup(options.mouse)`
and restores the terminal via `defer term.close()` on exit.

### string_width

Returns the terminal display width in columns of a UTF-8 string.

### utf8_decode_at

Decodes a single UTF-8 codepoint starting at byte `offset` in `s`.

Returns `(codepoint, byte_length)`. Invalid bytes decode as U+FFFD with length 1.

### utf8_encode

Encodes a Unicode codepoint as a UTF-8 string.

### utf8_supported

Returns whether console output expects UTF-8 (delegates to `valk.ansi.utf8_supported()`).

## Classes for 'main'

```js
// Real TTY backend using POSIX `termios`/`ioctl`/`poll` on Linux and macOS, or Win32 virtual terminal console APIs on Windows.
+ class AnsiBackend is Backend {
    // Enables raw mode on the terminal and installs a `SIGWINCH` resize handler on POSIX.
    + fn enter_raw_mode() void !TuiError
    // Returns whether the input and output descriptors are connected to a TTY.
    + fn is_tty() bool
    // Restores the saved terminal mode settings. Safe to call multiple times.
    + fn leave_raw_mode() void !TuiError
    // Creates an `AnsiBackend` attached to standard input (`0`) and standard output (`1`).
    + static fn new(in_fd: i32 (0), out_fd: i32 (1)) AnsiBackend
    // Reads the next event, waiting up to `timeout_ms` milliseconds.
    + fn read_event(timeout_ms: uint) ?(Event) !TuiError
    // Queries the current terminal dimensions `(width, height)`. Defaults to `(80, 24)` if unavailable.
    + fn size() (uint, uint)
    // Writes all `bytes` to the terminal output descriptor.
    + fn write(bytes: local &[u8]) void !TuiError
}
```

### AnsiBackend

Real TTY backend using POSIX `termios`/`ioctl`/`poll` on Linux and macOS, or Win32
virtual terminal console APIs on Windows.

#### enter_raw_mode

Enables raw mode on the terminal and installs a `SIGWINCH` resize handler on POSIX.

#### is_tty

Returns whether the input and output descriptors are connected to a TTY.

#### leave_raw_mode

Restores the saved terminal mode settings. Safe to call multiple times.

#### new

Creates an `AnsiBackend` attached to standard input (`0`) and standard output (`1`).

#### read_event

Reads the next event, waiting up to `timeout_ms` milliseconds.

#### size

Queries the current terminal dimensions `(width, height)`. Defaults to `(80, 24)` if unavailable.

#### write

Writes all `bytes` to the terminal output descriptor.

```js
// Elm/MVC-style application interface driven by `run_app()`.
+ interface App {
    // Processes an incoming `Event` (key, mouse, resize, paste, or tick).
    + fn update(event: Event) bool
    // Draws the current application state into `frame`.
    + fn view(frame: Frame) void
}
```

### App

Elm/MVC-style application interface driven by `run_app()`.

#### update

Processes an incoming `Event` (key, mouse, resize, paste, or tick).

Return `true` to continue running or `false` to exit the event loop cleanly.

#### view

Draws the current application state into `frame`.

```js
// Configuration options for `run_app()`.
+ class AppOptions {
    // Whether `run_app()` should enter raw mode, alternate screen, hide cursor, and restore everything automatically on exit. Set to `false` for custom or headless setups.
    + manage_terminal: bool
    // Maximum number of loop iterations (`0` means unlimited; useful for bounded headless tests).
    + max_iterations: uint
    // Whether to enable SGR 1006 mouse tracking when `manage_terminal` is true.
    + mouse: bool
    // Tick interval in milliseconds (`0` disables periodic `TickEvent`s).
    + tick_ms: uint
}
```

### AppOptions

Configuration options for `run_app()`.

#### manage_terminal

Whether `run_app()` should enter raw mode, alternate screen, hide cursor, and
restore everything automatically on exit. Set to `false` for custom or headless setups.

#### max_iterations

Maximum number of loop iterations (`0` means unlimited; useful for bounded headless tests).

#### mouse

Whether to enable SGR 1006 mouse tracking when `manage_terminal` is true.

#### tick_ms

Tick interval in milliseconds (`0` disables periodic `TickEvent`s).

```js
// Pluggable terminal I/O backend implemented by `AnsiBackend` (real TTY) and `TestBackend`.
+ interface Backend {
    // Switches the underlying terminal into raw mode.
    + fn enter_raw_mode() void !TuiError
    // Restores the terminal's original cooked mode settings.
    + fn leave_raw_mode() void !TuiError
    // Waits up to `timeout_ms` milliseconds for the next input or resize event.
    + fn read_event(timeout_ms: uint) ?(Event) !TuiError
    // Returns the current terminal dimensions as `(width, height)` in cells.
    + fn size() (uint, uint)
    // Writes raw bytes (such as ANSI escape sequences and UTF-8 text) to the terminal.
    + fn write(bytes: local &[u8]) void !TuiError
}
```

### Backend

Pluggable terminal I/O backend implemented by `AnsiBackend` (real TTY) and `TestBackend`.

#### enter_raw_mode

Switches the underlying terminal into raw mode.

#### leave_raw_mode

Restores the terminal's original cooked mode settings.

#### read_event

Waits up to `timeout_ms` milliseconds for the next input or resize event.

Returns `null` when the timeout elapses with no event.

#### size

Returns the current terminal dimensions as `(width, height)` in cells.

#### write

Writes raw bytes (such as ANSI escape sequences and UTF-8 text) to the terminal.

```js
// A container widget that draws an optional border, title, background fill, and inner padding.
+ class Block is Widget {
    // Style applied to the border characters.
    + border_style: Style
    // Border drawing style (`none`, `plain`, `rounded`, `double`, `thick`).
    + borders: BorderType
    // Inner padding inside the border.
    + padding: Padding
    // Base background/foreground style filled across the entire block area.
    + style: Style
    // Optional title drawn on the top edge.
    + title: String
    // Horizontal alignment of `title`.
    + title_alignment: Alignment
    // Style applied to `title` (patched on top of `border_style`).
    + title_style: Style

    // Returns the content `Rect` inside this block's border and padding.
    + fn inner(area: Rect) Rect
    // Renders the block background, border, and title into `buf`.
    + fn render(area: Rect, buf: Buffer) void
}
```

### Block

A container widget that draws an optional border, title, background fill, and inner padding.

#### border_style

Style applied to the border characters.

#### borders

Border drawing style (`none`, `plain`, `rounded`, `double`, `thick`).

#### padding

Inner padding inside the border.

#### style

Base background/foreground style filled across the entire block area.

#### title

Optional title drawn on the top edge.

#### title_alignment

Horizontal alignment of `title`.

#### title_style

Style applied to `title` (patched on top of `border_style`).

#### inner

Returns the content `Rect` inside this block's border and padding.

#### render

Renders the block background, border, and title into `buf`.

```js
// A 2D grid of styled terminal cells supporting clipping, wide characters, and ANSI diffing.
+ class Buffer {
    // Bounding rectangle of this buffer (origin is always `(0, 0)`).
    + area: Rect
    // Row-major cell storage of length `area.width * area.height`.
    ~ cells: Array[Cell]

    // Resets every cell in the buffer to a default space.
    + fn clear() void
    // Resets cells inside `rect` (clipped to the buffer) to default spaces.
    + fn clear_rect(rect: Rect) void
    // Copies dimensions and cell contents from `other` into `this`.
    + fn copy_from(other: Buffer) void
    // Emits minimal ANSI escape sequences into `out` to update the terminal from `prev` to `this`.
    + fn diff_to_ansi(prev: Buffer, out: ByteBuffer, full_redraw: bool (false)) void
    // Creates a blank buffer matching the dimensions of `area`.
    + static fn empty(area: Rect) Buffer
    // Fills `rect` (clipped to the buffer) with `symbol` and `style`.
    + fn fill(rect: Rect, symbol: String, style: Style) void
    // Returns the cell at `(x, y)`, or `null` if out of bounds.
    + fn get(x: uint, y: uint) ?Cell
    // Buffer height in rows.
    + get height: uint
    // Returns whether `(x, y)` is inside the buffer bounds.
    + fn in_bounds(x: uint, y: uint) bool
    // Creates a blank buffer of `width` columns by `height` rows.
    + static fn new(width: uint, height: uint) Buffer
    // Resizes the buffer to `width` x `height` and clears all cells.
    + fn resize(width: uint, height: uint) void
    // Returns the plain text of row `y` (without ANSI styles), or `""` if out of bounds.
    + fn row_text(y: uint) String
    // Sets a single cell at `(x, y)` with `symbol` and `style`.
    + fn set_cell(x: uint, y: uint, symbol: String, style: Style) void
    // Writes `text` starting at `(x, y)` with `style`, clipping at the buffer edge.
    + fn set_string(x: uint, y: uint, text: String, style: Style) uint
    // Writes `text` starting at `(x, y)` with `style`, clipped to `max_width` columns and the buffer bounds. Returns the number of columns written.
    + fn set_string_clipped(x: uint, y: uint, max_width: uint, text: String, style: Style) uint
    // Patches `style` onto every cell inside `rect` (clipped to the buffer).
    + fn set_style(rect: Rect, style: Style) void
    // Returns all rows joined by `"\n"`, useful for snapshot assertions in tests.
    + fn to_plain_string() String
    // Buffer width in columns.
    + get width: uint
}
```

### Buffer

A 2D grid of styled terminal cells supporting clipping, wide characters, and ANSI diffing.

#### area

Bounding rectangle of this buffer (origin is always `(0, 0)`).

#### cells

Row-major cell storage of length `area.width * area.height`.

#### clear

Resets every cell in the buffer to a default space.

#### clear_rect

Resets cells inside `rect` (clipped to the buffer) to default spaces.

#### copy_from

Copies dimensions and cell contents from `other` into `this`.

#### diff_to_ansi

Emits minimal ANSI escape sequences into `out` to update the terminal from `prev` to `this`.

When `full_redraw` is `true` or the buffer dimensions changed, clears the screen first
and redraws every non-blank cell.

#### empty

Creates a blank buffer matching the dimensions of `area`.

#### fill

Fills `rect` (clipped to the buffer) with `symbol` and `style`.

#### get

Returns the cell at `(x, y)`, or `null` if out of bounds.

#### height

Buffer height in rows.

#### in_bounds

Returns whether `(x, y)` is inside the buffer bounds.

#### new

Creates a blank buffer of `width` columns by `height` rows.

#### resize

Resizes the buffer to `width` x `height` and clears all cells.

#### row_text

Returns the plain text of row `y` (without ANSI styles), or `""` if out of bounds.

#### set_cell

Sets a single cell at `(x, y)` with `symbol` and `style`.

Out-of-bounds writes are ignored. Wide (2-column) characters mark the following cell
as a continuation (`skip = true`) and are replaced with a space if they would overflow
the right edge. Overwriting either half of an existing wide character clears its partner.

#### set_string

Writes `text` starting at `(x, y)` with `style`, clipping at the buffer edge.

Returns the number of columns written.

#### set_string_clipped

Writes `text` starting at `(x, y)` with `style`, clipped to `max_width` columns
and the buffer bounds. Returns the number of columns written.

#### set_style

Patches `style` onto every cell inside `rect` (clipped to the buffer).

#### to_plain_string

Returns all rows joined by `"\n"`, useful for snapshot assertions in tests.

#### width

Buffer width in columns.

```js
// A single styled cell in a terminal `Buffer`.
+ struct Cell {
    // True for the trailing cell occupied by a 2-column wide character.
    + skip: bool
    // Visual style for this cell.
    + style: Style
    // The UTF-8 character or grapheme displayed in this cell.
    + symbol: String

    // Returns a blank default cell (`" "` with `Style.default()`).
    + static fn empty() Cell
    // Returns whether two cells have the same symbol, style, and skip flag.
    + fn equals(other: Cell) bool
    // Returns whether this cell is a default blank space.
    + fn is_empty_default() bool
}
```

### Cell

A single styled cell in a terminal `Buffer`.

#### skip

True for the trailing cell occupied by a 2-column wide character.

#### style

Visual style for this cell.

#### symbol

The UTF-8 character or grapheme displayed in this cell.

#### empty

Returns a blank default cell (`" "` with `Style.default()`).

#### equals

Returns whether two cells have the same symbol, style, and skip flag.

#### is_empty_default

Returns whether this cell is a default blank space.

```js
// A terminal foreground or background color.
+ struct Color {
    // Blue channel for `rgb`.
    + b: u8
    // Green channel for `rgb`.
    + g: u8
    // Palette index for `ansi` (`0..15`) or `indexed` (`0..255`).
    + index: u8
    // Which color representation is active.
    + kind: ColorKind
    // Red channel for `rgb`.
    + r: u8

    // Returns one of the 16 standard ANSI colors (`0..15`).
    + static fn ansi(code: u8) Color
    // Standard ANSI black (color 0).
    + static fn black() Color
    // Standard ANSI blue (color 4).
    + static fn blue() Color
    // Bright ANSI black / dark gray (color 8).
    + static fn bright_black() Color
    // Bright ANSI blue (color 12).
    + static fn bright_blue() Color
    // Bright ANSI cyan (color 14).
    + static fn bright_cyan() Color
    // Bright ANSI green (color 10).
    + static fn bright_green() Color
    // Bright ANSI magenta (color 13).
    + static fn bright_magenta() Color
    // Bright ANSI red (color 9).
    + static fn bright_red() Color
    // Bright ANSI white (color 15).
    + static fn bright_white() Color
    // Bright ANSI yellow (color 11).
    + static fn bright_yellow() Color
    // Returns an independent copy of this color.
    + fn clone() Color
    // Standard ANSI cyan (color 6).
    + static fn cyan() Color
    // Returns whether two colors are equal.
    + fn equals(other: Color) bool
    // Standard ANSI green (color 2).
    + static fn green() Color
    // Returns a 256-color palette color (`0..255`).
    + static fn indexed(index: u8) Color
    // Returns whether this color is the terminal default (`ColorKind.reset`).
    + fn is_reset() bool
    // Standard ANSI magenta (color 5).
    + static fn magenta() Color
    // Standard ANSI red (color 1).
    + static fn red() Color
    // Returns the terminal default color.
    + static fn reset() Color
    // Returns a 24-bit truecolor RGB color.
    + static fn rgb(r: u8, g: u8, b: u8) Color
    // Standard ANSI white / light gray (color 7).
    + static fn white() Color
    // Standard ANSI yellow (color 3).
    + static fn yellow() Color
}
```

### Color

A terminal foreground or background color.

#### b

Blue channel for `rgb`.

#### g

Green channel for `rgb`.

#### index

Palette index for `ansi` (`0..15`) or `indexed` (`0..255`).

#### kind

Which color representation is active.

#### r

Red channel for `rgb`.

#### ansi

Returns one of the 16 standard ANSI colors (`0..15`).

#### black

Standard ANSI black (color 0).

#### blue

Standard ANSI blue (color 4).

#### bright_black

Bright ANSI black / dark gray (color 8).

#### bright_blue

Bright ANSI blue (color 12).

#### bright_cyan

Bright ANSI cyan (color 14).

#### bright_green

Bright ANSI green (color 10).

#### bright_magenta

Bright ANSI magenta (color 13).

#### bright_red

Bright ANSI red (color 9).

#### bright_white

Bright ANSI white (color 15).

#### bright_yellow

Bright ANSI yellow (color 11).

#### clone

Returns an independent copy of this color.

#### cyan

Standard ANSI cyan (color 6).

#### equals

Returns whether two colors are equal.

#### green

Standard ANSI green (color 2).

#### indexed

Returns a 256-color palette color (`0..255`).

#### is_reset

Returns whether this color is the terminal default (`ColorKind.reset`).

#### magenta

Standard ANSI magenta (color 5).

#### red

Standard ANSI red (color 1).

#### reset

Returns the terminal default color.

#### rgb

Returns a 24-bit truecolor RGB color.

#### white

Standard ANSI white / light gray (color 7).

#### yellow

Standard ANSI yellow (color 3).

```js
// Sizing constraint for a single child slot in a `Layout`.
+ struct Constraint {
    // Constraint kind.
    + kind: ConstraintKind
    // Numeric parameter (cells, percentage, or fill weight).
    + value: uint

    // Proportional share of remaining space with the given `weight` (defaults to `1`).
    + static fn fill(weight: uint (1)) Constraint
    // Exact size of `n` cells.
    + static fn fixed(n: uint) Constraint
    // At most `n` cells.
    + static fn max(n: uint) Constraint
    // At least `n` cells.
    + static fn min(n: uint) Constraint
    // `p` percent (`0..100`) of the available space.
    + static fn percent(p: uint) Constraint
}
```

### Constraint

Sizing constraint for a single child slot in a `Layout`.

#### kind

Constraint kind.

#### value

Numeric parameter (cells, percentage, or fill weight).

#### fill

Proportional share of remaining space with the given `weight` (defaults to `1`).

#### fixed

Exact size of `n` cells.

#### max

At most `n` cells.

#### min

At least `n` cells.

#### percent

`p` percent (`0..100`) of the available space.

```js
// Hardware cursor position requested by a widget during rendering.
+ struct CursorPos {
    // 0-based column.
    + x: uint
    // 0-based row.
    + y: uint
}
```

### CursorPos

Hardware cursor position requested by a widget during rendering.

#### x

0-based column.

#### y

0-based row.

```js
// A terminal input or lifecycle event.
+ union Event : KeyEvent | MouseEvent | PasteEvent | ResizeEvent | TickEvent {
    // Returns the underlying `KeyEvent`, or `null` if this is another event kind.
    + fn as_key() ?KeyEvent
    // Returns the underlying `MouseEvent`, or `null` if this is another event kind.
    + fn as_mouse() ?MouseEvent
    // Returns the underlying `PasteEvent`, or `null` if this is another event kind.
    + fn as_paste() ?PasteEvent
    // Returns the underlying `ResizeEvent`, or `null` if this is another event kind.
    + fn as_resize() ?ResizeEvent
    // Returns whether this event is a `TickEvent`.
    + get is_tick: bool
}
```

### Event

A terminal input or lifecycle event.

#### as_key

Returns the underlying `KeyEvent`, or `null` if this is another event kind.

#### as_mouse

Returns the underlying `MouseEvent`, or `null` if this is another event kind.

#### as_paste

Returns the underlying `PasteEvent`, or `null` if this is another event kind.

#### as_resize

Returns the underlying `ResizeEvent`, or `null` if this is another event kind.

#### is_tick

Returns whether this event is a `TickEvent`.

```js
// Incremental ANSI/VT100/xterm/kitty terminal input byte stream parser.
+ class EventParser {
    // Appends raw input bytes to the parser buffer.
    + fn feed(bytes: local &[u8]) void
    // Appends a string of raw input bytes to the parser buffer.
    + fn feed_string(s: String) void
    // Flushes a standalone pending Escape byte (`0x1b`) as `Key.esc`.
    + fn flush_escape() ?(Event)
    // Returns whether unread bytes remain in the parser buffer.
    + fn has_pending() bool
    // Creates a new empty `EventParser`.
    + static fn new() EventParser
    // Decodes and returns the next complete `Event`, or `null` if more bytes are needed.
    + fn next() ?(Event)
    // Parses a complete byte slice into all decoded events, flushing any trailing bare ESC.
    + static fn parse_all(bytes: local &[u8]) Array[Event]
    // Parses a complete string of terminal input bytes into all decoded events.
    + static fn parse_string(s: String) Array[Event]
}
```

### EventParser

Incremental ANSI/VT100/xterm/kitty terminal input byte stream parser.

Feed raw bytes as they arrive from the terminal with `feed()`, then drain complete
events with `next()`. Call `flush_escape()` after a short read timeout to emit a
standalone Escape key press.

#### feed

Appends raw input bytes to the parser buffer.

#### feed_string

Appends a string of raw input bytes to the parser buffer.

#### flush_escape

Flushes a standalone pending Escape byte (`0x1b`) as `Key.esc`.

Call this when `next()` returns `null` after a short input timeout so a user pressing
Escape is not held indefinitely waiting for a CSI/SS3 continuation.

#### has_pending

Returns whether unread bytes remain in the parser buffer.

#### new

Creates a new empty `EventParser`.

#### next

Decodes and returns the next complete `Event`, or `null` if more bytes are needed.

#### parse_all

Parses a complete byte slice into all decoded events, flushing any trailing bare ESC.

#### parse_string

Parses a complete string of terminal input bytes into all decoded events.

```js
// Drawing surface passed to the callback of `Terminal.draw()`.
+ class Frame {
    // Full drawable area of the terminal (`Rect { x: 0, y: 0, width, height }`).
    + area: Rect
    // Back buffer for the current frame.
    + buffer: Buffer
    // Optional hardware cursor position to show after flushing this frame.
    + cursor: ?CursorPos

    // Clears any cursor position request so the cursor remains hidden.
    + fn hide_cursor() void
    // Renders `widget` into `area` on this frame's buffer.
    + fn render(widget: Widget, area: Rect) void
    // Requests that the terminal cursor be shown at `(x, y)` after this frame is drawn.
    + fn set_cursor(x: uint, y: uint) void
}
```

### Frame

Drawing surface passed to the callback of `Terminal.draw()`.

#### area

Full drawable area of the terminal (`Rect { x: 0, y: 0, width, height }`).

#### buffer

Back buffer for the current frame.

#### cursor

Optional hardware cursor position to show after flushing this frame.

#### hide_cursor

Clears any cursor position request so the cursor remains hidden.

#### render

Renders `widget` into `area` on this frame's buffer.

#### set_cursor

Requests that the terminal cursor be shown at `(x, y)` after this frame is drawn.

```js
// Progress bar / gauge widget with smooth Unicode 1/8th-cell block fill (`▏▎▍▌▋▊▉█`).
+ class Gauge is Widget {
    // Optional surrounding `Block`.
    + block: ?Block
    // Style for the filled bar.
    + gauge_style: Style
    // Custom centered label; when empty and `show_percent` is true, renders `"{pct}%"`.
    + label: String
    // Fill ratio in `[0.0, 1.0]`.
    + ratio: f64
    // Whether to display the percentage label when `label` is empty.
    + show_percent: bool
    // Style for the unfilled track.
    + style: Style

    // Creates a `Gauge` initialized from a percentage (`0..100`).
    + static fn percent(p: uint) Gauge
    // Renders the progress bar and centered label into `buf`.
    + fn render(area: Rect, buf: Buffer) void
    // Updates `ratio` from a percentage (`0..100`).
    + fn set_percent(p: uint) void
}
```

### Gauge

Progress bar / gauge widget with smooth Unicode 1/8th-cell block fill (`▏▎▍▌▋▊▉█`).

#### block

Optional surrounding `Block`.

#### gauge_style

Style for the filled bar.

#### label

Custom centered label; when empty and `show_percent` is true, renders `"{pct}%"`.

#### ratio

Fill ratio in `[0.0, 1.0]`.

#### show_percent

Whether to display the percentage label when `label` is empty.

#### style

Style for the unfilled track.

#### percent

Creates a `Gauge` initialized from a percentage (`0..100`).

#### render

Renders the progress bar and centered label into `buf`.

#### set_percent

Updates `ratio` from a percentage (`0..100`).

```js
// A decoded keyboard input event.
+ class KeyEvent {
    // Unicode codepoint when `key == Key.char` (0 for non-character keys).
    + codepoint: u32
    // Logical key code.
    + key: Key
    // Active modifier keys.
    + modifiers: Modifiers
    // UTF-8 text when `key == Key.char` (`""` for non-character keys).
    + text: String

    // Creates a character key event from a UTF-8 string.
    + static fn char(text: String, modifiers: Modifiers (.{})) KeyEvent
    // Creates a non-character key event (arrows, enter, function keys, etc.).
    + static fn code(key: Key, modifiers: Modifiers (.{})) KeyEvent
    // Returns whether this is a plain character press matching `s` without Ctrl or Alt.
    + fn is_char(s: String) bool
    // Returns whether this is a Ctrl+character press matching `ch` (e.g. `'c'`).
    + fn is_ctrl(ch: u8) bool
}
```

### KeyEvent

A decoded keyboard input event.

#### codepoint

Unicode codepoint when `key == Key.char` (0 for non-character keys).

#### key

Logical key code.

#### modifiers

Active modifier keys.

#### text

UTF-8 text when `key == Key.char` (`""` for non-character keys).

#### char

Creates a character key event from a UTF-8 string.

#### code

Creates a non-character key event (arrows, enter, function keys, etc.).

#### is_char

Returns whether this is a plain character press matching `s` without Ctrl or Alt.

#### is_ctrl

Returns whether this is a Ctrl+character press matching `ch` (e.g. `'c'`).

```js
// Splits a `Rect` horizontally or vertically according to `Constraint`s, optional `gap`, and `margin`.
+ class Layout {
    // Sizing constraints for each resulting slot.
    + constraints: Array[Constraint]
    // Split axis (`horizontal` or `vertical`).
    + direction: Direction
    // Empty cells inserted between adjacent slots.
    + gap: uint
    // Outer margin inset before splitting.
    + margin: Margin

    // Creates a horizontal (left-to-right) layout with `constraints`.
    + static fn horizontal(constraints: Array[Constraint]) Layout
    // Computes child `Rect`s for every constraint within `area`.
    + fn split(area: Rect) Array[Rect]
    // Creates a vertical (top-to-bottom) layout with `constraints`.
    + static fn vertical(constraints: Array[Constraint]) Layout
    // Sets the gap in cells between adjacent slots and returns `this`.
    + fn with_gap(gap: uint) Layout
    // Sets the outer margin and returns `this`.
    + fn with_margin(margin: Margin) Layout
    // Sets a uniform outer margin on all four sides and returns `this`.
    + fn with_margin_all(n: uint) Layout
}
```

### Layout

Splits a `Rect` horizontally or vertically according to `Constraint`s, optional `gap`,
and `margin`.

#### constraints

Sizing constraints for each resulting slot.

#### direction

Split axis (`horizontal` or `vertical`).

#### gap

Empty cells inserted between adjacent slots.

#### margin

Outer margin inset before splitting.

#### horizontal

Creates a horizontal (left-to-right) layout with `constraints`.

#### split

Computes child `Rect`s for every constraint within `area`.

#### vertical

Creates a vertical (top-to-bottom) layout with `constraints`.

#### with_gap

Sets the gap in cells between adjacent slots and returns `this`.

#### with_margin

Sets the outer margin and returns `this`.

#### with_margin_all

Sets a uniform outer margin on all four sides and returns `this`.

```js
// Selectable scrollable list widget with highlight styling and automatic viewport tracking.
+ class List is Widget {
    // Optional surrounding `Block`.
    + block: ?Block
    // Style applied to the selected item.
    + highlight_style: Style
    // Prefix rendered before the selected item (unselected items are indented by its width).
    + highlight_symbol: String
    // Display items.
    + items: Array[String]
    // First visible item index (automatically updated during `render()` to keep `selected` visible).
    + offset: uint
    // Currently selected item index, or `null` for no selection.
    + selected: ?uint
    // Style applied to unselected items.
    + style: Style

    // Creates a `List` containing `items`.
    + static fn new(items: Array[String]) List
    // Renders the visible slice of the list into `buf`.
    + fn render(area: Rect, buf: Buffer) void
    // Sets the selected item index (clamped to valid bounds when non-null).
    + fn select(index: ?uint) void
    // Selects the first item.
    + fn select_first() void
    // Selects the last item.
    + fn select_last() void
    // Moves selection down by one item (clamped to the last item).
    + fn select_next() void
    // Moves selection up by one item (clamped to 0).
    + fn select_prev() void
}
```

### List

Selectable scrollable list widget with highlight styling and automatic viewport tracking.

#### block

Optional surrounding `Block`.

#### highlight_style

Style applied to the selected item.

#### highlight_symbol

Prefix rendered before the selected item (unselected items are indented by its width).

#### items

Display items.

#### offset

First visible item index (automatically updated during `render()` to keep `selected` visible).

#### selected

Currently selected item index, or `null` for no selection.

#### style

Style applied to unselected items.

#### new

Creates a `List` containing `items`.

#### render

Renders the visible slice of the list into `buf`.

#### select

Sets the selected item index (clamped to valid bounds when non-null).

#### select_first

Selects the first item.

#### select_last

Selects the last item.

#### select_next

Moves selection down by one item (clamped to the last item).

#### select_prev

Moves selection up by one item (clamped to 0).

```js
// Outer inset around a `Layout` or widget area.
+ struct Margin {
    // Bottom margin in rows.
    + bottom: uint
    // Left margin in columns.
    + left: uint
    // Right margin in columns.
    + right: uint
    // Top margin in rows.
    + top: uint

    // Uniform margin on all four sides.
    + static fn all(n: uint) Margin
    // Explicit margin for each side.
    + static fn new(top: uint, right: uint, bottom: uint, left: uint) Margin
    // Symmetric horizontal and vertical margins.
    + static fn xy(horizontal: uint, vertical: uint) Margin
}
```

### Margin

Outer inset around a `Layout` or widget area.

#### bottom

Bottom margin in rows.

#### left

Left margin in columns.

#### right

Right margin in columns.

#### top

Top margin in rows.

#### all

Uniform margin on all four sides.

#### new

Explicit margin for each side.

#### xy

Symmetric horizontal and vertical margins.

```js
// Modifier keys held during a keyboard or mouse event.
+ struct Modifiers {
    // Alt / Meta modifier.
    + alt: bool
    // Control modifier.
    + ctrl: bool
    // Shift modifier.
    + shift: bool

    // Returns whether two modifier sets are equal.
    + fn equals(other: Modifiers) bool
    // Returns whether no modifier keys are held.
    + fn is_empty() bool
    // Returns empty modifiers (all false).
    + static fn none() Modifiers
}
```

### Modifiers

Modifier keys held during a keyboard or mouse event.

#### alt

Alt / Meta modifier.

#### ctrl

Control modifier.

#### shift

Shift modifier.

#### equals

Returns whether two modifier sets are equal.

#### is_empty

Returns whether no modifier keys are held.

#### none

Returns empty modifiers (all false).

```js
// A decoded terminal mouse event (SGR 1006 mode).
+ class MouseEvent {
    // Button involved.
    + button: MouseButton
    // Action kind.
    + kind: MouseKind
    // Modifier keys held during the event.
    + modifiers: Modifiers
    // 0-based terminal column.
    + x: uint
    // 0-based terminal row.
    + y: uint
}
```

### MouseEvent

A decoded terminal mouse event (SGR 1006 mode).

#### button

Button involved.

#### kind

Action kind.

#### modifiers

Modifier keys held during the event.

#### x

0-based terminal column.

#### y

0-based terminal row.

```js
// Inner padding inside a `Block`.
+ struct Padding {
    // Bottom padding in rows.
    + bottom: uint
    // Left padding in columns.
    + left: uint
    // Right padding in columns.
    + right: uint
    // Top padding in rows.
    + top: uint

    // Uniform padding on all four sides.
    + static fn all(n: uint) Padding
    // Symmetric horizontal and vertical padding.
    + static fn xy(horizontal: uint, vertical: uint) Padding
}
```

### Padding

Inner padding inside a `Block`.

#### bottom

Bottom padding in rows.

#### left

Left padding in columns.

#### right

Right padding in columns.

#### top

Top padding in rows.

#### all

Uniform padding on all four sides.

#### xy

Symmetric horizontal and vertical padding.

```js
// Multi-line text widget supporting word wrapping, alignment, vertical scrolling, and an optional `Block`.
+ class Paragraph is Widget {
    // Horizontal alignment of each visual line.
    + alignment: Alignment
    // Optional surrounding `Block`.
    + block: ?Block
    // Number of visual lines to skip from the top.
    + scroll: uint
    // Style applied to the text.
    + style: Style
    // Text content (may contain `\n` line breaks).
    + text: String
    // Whether lines wrap at word boundaries to fit the available width.
    + wrap: bool

    // Creates a `Paragraph` with the given `text`.
    + static fn new(text: String) Paragraph
    // Renders the paragraph into `buf` within `area`.
    + fn render(area: Rect, buf: Buffer) void
    // Computes the wrapped visual lines for `max_width` columns.
    + fn wrapped_lines(max_width: uint) Array[String]
}
```

### Paragraph

Multi-line text widget supporting word wrapping, alignment, vertical scrolling, and an optional `Block`.

#### alignment

Horizontal alignment of each visual line.

#### block

Optional surrounding `Block`.

#### scroll

Number of visual lines to skip from the top.

#### style

Style applied to the text.

#### text

Text content (may contain `\n` line breaks).

#### wrap

Whether lines wrap at word boundaries to fit the available width.

#### new

Creates a `Paragraph` with the given `text`.

#### render

Renders the paragraph into `buf` within `area`.

#### wrapped_lines

Computes the wrapped visual lines for `max_width` columns.

```js
// Emitted when text is pasted inside bracketed paste markers (`ESC[200~ ... ESC[201~`).
+ class PasteEvent {
    // Pasted UTF-8 text.
    + text: String
}
```

### PasteEvent

Emitted when text is pasted inside bracketed paste markers (`ESC[200~ ... ESC[201~`).

#### text

Pasted UTF-8 text.

```js
// A rectangular region of terminal cells.
+ struct Rect {
    // Height in rows.
    + height: uint
    // Width in columns.
    + width: uint
    // Leftmost column (0-based).
    + x: uint
    // Topmost row (0-based).
    + y: uint

    // Returns the total number of cells (`width * height`).
    + fn area() uint
    // Returns the exclusive bottom edge (`y + height`).
    + fn bottom() uint
    // Returns whether the cell `(x, y)` lies inside this rectangle.
    + fn contains(x: uint, y: uint) bool
    // Returns whether two rectangles have equal position and dimensions.
    + fn equals(other: Rect) bool
    // Returns a rectangle inset by `horizontal` columns on the left and right and `vertical` rows on the top and bottom, clamped to zero size.
    + fn inner(horizontal: uint, vertical: uint) Rect
    // Returns a rectangle inset by individual `top`, `right`, `bottom`, and `left` margins.
    + fn inner_margins(top: uint, right: uint, bottom: uint, left: uint) Rect
    // Returns the overlapping region between `this` and `other`, or an empty `Rect`.
    + fn intersection(other: Rect) Rect
    // Returns whether the rectangle has zero area.
    + fn is_empty() bool
    // Creates a `Rect` at `(x, y)` with the given `width` and `height`.
    + static fn new(x: uint, y: uint, width: uint, height: uint) Rect
    // Returns the exclusive right edge (`x + width`).
    + fn right() uint
}
```

### Rect

A rectangular region of terminal cells.

#### height

Height in rows.

#### width

Width in columns.

#### x

Leftmost column (0-based).

#### y

Topmost row (0-based).

#### area

Returns the total number of cells (`width * height`).

#### bottom

Returns the exclusive bottom edge (`y + height`).

#### contains

Returns whether the cell `(x, y)` lies inside this rectangle.

#### equals

Returns whether two rectangles have equal position and dimensions.

#### inner

Returns a rectangle inset by `horizontal` columns on the left and right and
`vertical` rows on the top and bottom, clamped to zero size.

#### inner_margins

Returns a rectangle inset by individual `top`, `right`, `bottom`, and `left` margins.

#### intersection

Returns the overlapping region between `this` and `other`, or an empty `Rect`.

#### is_empty

Returns whether the rectangle has zero area.

#### new

Creates a `Rect` at `(x, y)` with the given `width` and `height`.

#### right

Returns the exclusive right edge (`x + width`).

```js
// Emitted when the terminal dimensions change.
+ class ResizeEvent {
    // New height in rows.
    + height: uint
    // New width in columns.
    + width: uint
}
```

### ResizeEvent

Emitted when the terminal dimensions change.

#### height

New height in rows.

#### width

New width in columns.

```js
// Visual attributes for a terminal cell: foreground, background, and text modifiers.
+ struct Style {
    // Background color.
    + bg: Color
    // Bold / increased intensity.
    + bold: bool
    // Dim / decreased intensity.
    + dim: bool
    // Foreground color.
    + fg: Color
    // Italic text.
    + italic: bool
    // Swapped foreground and background colors.
    + reverse: bool
    // Struck-through text.
    + strikethrough: bool
    // Underlined text.
    + underline: bool

    // Returns an independent copy of this style.
    + fn clone() Style
    // Returns an unstyled default `Style`.
    + static fn default() Style
    // Returns whether two styles have identical colors and modifiers.
    + fn equals(other: Style) bool
    // Returns whether every attribute is at its default value.
    + fn is_default() bool
    // Overlays non-default attributes of `other` onto `this` and returns the result.
    + fn patch(other: Style) Style
    // Returns a copy with `bg` set to `color`.
    + fn with_bg(color: Color) Style
    // Returns a copy with `bold` set.
    + fn with_bold(on: bool (true)) Style
    // Returns a copy with `dim` set.
    + fn with_dim(on: bool (true)) Style
    // Returns a copy with `fg` set to `color`.
    + fn with_fg(color: Color) Style
    // Returns a copy with `italic` set.
    + fn with_italic(on: bool (true)) Style
    // Returns a copy with `reverse` set.
    + fn with_reverse(on: bool (true)) Style
    // Returns a copy with `strikethrough` set.
    + fn with_strikethrough(on: bool (true)) Style
    // Returns a copy with `underline` set.
    + fn with_underline(on: bool (true)) Style
    // Writes a full ANSI SGR reset-and-apply sequence for this style into `out`.
    + fn write_sgr(out: ByteBuffer) void
    // Writes the minimal ANSI SGR sequence transitioning from `prev` to `this`.
    + fn write_sgr_diff(prev: Style, out: ByteBuffer) void
}
```

### Style

Visual attributes for a terminal cell: foreground, background, and text modifiers.

#### bg

Background color.

#### bold

Bold / increased intensity.

#### dim

Dim / decreased intensity.

#### fg

Foreground color.

#### italic

Italic text.

#### reverse

Swapped foreground and background colors.

#### strikethrough

Struck-through text.

#### underline

Underlined text.

#### clone

Returns an independent copy of this style.

#### default

Returns an unstyled default `Style`.

#### equals

Returns whether two styles have identical colors and modifiers.

#### is_default

Returns whether every attribute is at its default value.

#### patch

Overlays non-default attributes of `other` onto `this` and returns the result.

#### with_bg

Returns a copy with `bg` set to `color`.

#### with_bold

Returns a copy with `bold` set.

#### with_dim

Returns a copy with `dim` set.

#### with_fg

Returns a copy with `fg` set to `color`.

#### with_italic

Returns a copy with `italic` set.

#### with_reverse

Returns a copy with `reverse` set.

#### with_strikethrough

Returns a copy with `strikethrough` set.

#### with_underline

Returns a copy with `underline` set.

#### write_sgr

Writes a full ANSI SGR reset-and-apply sequence for this style into `out`.

#### write_sgr_diff

Writes the minimal ANSI SGR sequence transitioning from `prev` to `this`.

Writes nothing when `this == prev`.

```js
// Tabular data widget with column headers, `Constraint` column widths, row selection, and automatic vertical scrolling.
+ class Table is Widget {
    // Optional surrounding `Block`.
    + block: ?Block
    // Columns of spacing between adjacent table columns.
    + column_spacing: uint
    // Style for the header row.
    + header_style: Style
    // Optional column header labels.
    + headers: Array[String]
    // Style for the selected body row.
    + highlight_style: Style
    // Optional prefix shown on the selected row.
    + highlight_symbol: String
    // First visible body row index (auto-adjusted during `render()`).
    + offset: uint
    // Table body rows; each row is an array of column cell strings.
    + rows: Array[Array[String]]
    // Currently selected row index, or `null` for no selection.
    + selected: ?uint
    // Style for normal body cells.
    + style: Style
    // Sizing constraints for each column (defaults to equal `Constraint.fill(1)` if empty).
    + widths: Array[Constraint]

    // Renders the table headers and visible rows into `buf`.
    + fn render(area: Rect, buf: Buffer) void
    // Sets the selected row index.
    + fn select(index: ?uint) void
    // Selects the next row (clamped to the last row).
    + fn select_next() void
    // Selects the previous row (clamped to 0).
    + fn select_prev() void
}
```

### Table

Tabular data widget with column headers, `Constraint` column widths, row selection,
and automatic vertical scrolling.

#### block

Optional surrounding `Block`.

#### column_spacing

Columns of spacing between adjacent table columns.

#### header_style

Style for the header row.

#### headers

Optional column header labels.

#### highlight_style

Style for the selected body row.

#### highlight_symbol

Optional prefix shown on the selected row.

#### offset

First visible body row index (auto-adjusted during `render()`).

#### rows

Table body rows; each row is an array of column cell strings.

#### selected

Currently selected row index, or `null` for no selection.

#### style

Style for normal body cells.

#### widths

Sizing constraints for each column (defaults to equal `Constraint.fill(1)` if empty).

#### render

Renders the table headers and visible rows into `buf`.

#### select

Sets the selected row index.

#### select_next

Selects the next row (clamped to the last row).

#### select_prev

Selects the previous row (clamped to 0).

```js
// Horizontal tab bar widget with customizable divider and active tab highlight style.
+ class Tabs is Widget {
    // Optional surrounding `Block`.
    + block: ?Block
    // Separator drawn between tabs.
    + divider: String
    // Style for the active tab.
    + highlight_style: Style
    // Index of the active tab.
    + selected: uint
    // Style for inactive tabs and dividers.
    + style: Style
    // Tab labels.
    + titles: Array[String]

    // Creates a `Tabs` bar from `titles`.
    + static fn new(titles: Array[String]) Tabs
    // Renders the tab bar into `buf`.
    + fn render(area: Rect, buf: Buffer) void
    // Selects the next tab (wrapping around).
    + fn select_next() void
    // Selects the previous tab (wrapping around).
    + fn select_prev() void
}
```

### Tabs

Horizontal tab bar widget with customizable divider and active tab highlight style.

#### block

Optional surrounding `Block`.

#### divider

Separator drawn between tabs.

#### highlight_style

Style for the active tab.

#### selected

Index of the active tab.

#### style

Style for inactive tabs and dividers.

#### titles

Tab labels.

#### new

Creates a `Tabs` bar from `titles`.

#### render

Renders the tab bar into `buf`.

#### select_next

Selects the next tab (wrapping around).

#### select_prev

Selects the previous tab (wrapping around).

```js
// Double-buffered terminal controller that manages raw mode, screen modes, and diffed rendering.
+ class Terminal {
    // Whether the alternate screen buffer is currently active.
    ~ alt_screen: bool
    // Underlying terminal backend.
    + backend: Backend
    // Whether `close()` has been called.
    ~ closed: bool
    // Whether the hardware cursor is currently hidden.
    ~ cursor_hidden: bool
    // Whether SGR 1006 mouse tracking is currently enabled.
    ~ mouse_enabled: bool
    // Whether bracketed paste mode is currently enabled.
    ~ paste_enabled: bool
    // Whether raw mode is currently enabled.
    ~ raw_mode: bool

    // Returns the last rendered `Buffer` (useful for inspecting output in tests).
    + get buffer: Buffer
    // Marks the terminal dirty so the next `draw()` clears the screen and performs a full redraw.
    + fn clear() void
    // Restores every terminal mode enabled on this instance (mouse, paste, cursor, alternate screen, style reset, and raw mode). Safe to call multiple times or in `defer`.
    + fn close() void
    // Disables bracketed paste mode (`ESC[?2004l`).
    + fn disable_bracketed_paste() void !TuiError
    // Disables SGR 1006 mouse tracking.
    + fn disable_mouse() void !TuiError
    // Renders one frame through `render_fn`, diffs against the previous frame, and flushes only the changed cells and minimal ANSI style transitions in a single write.
    + fn draw(render_fn: fn(Frame)()) void !TuiError
    // Enables bracketed paste mode (`ESC[?2004h`).
    + fn enable_bracketed_paste() void !TuiError
    // Enables SGR 1006 mouse press/release/drag/scroll reporting.
    + fn enable_mouse() void !TuiError
    // Switches to the alternate screen buffer (`ESC[?1049h`).
    + fn enter_alt_screen() void !TuiError
    // Enables raw mode on the backend.
    + fn enter_raw_mode() void !TuiError
    // Hides the hardware cursor (`ESC[?25l`).
    + fn hide_cursor() void !TuiError
    // Returns to the primary screen buffer (`ESC[?1049l`).
    + fn leave_alt_screen() void !TuiError
    // Restores cooked mode on the backend.
    + fn leave_raw_mode() void !TuiError
    // Creates a `Terminal` backed by `backend`.
    + static fn new(backend: Backend) Terminal
    // Waits up to `timeout_ms` milliseconds for the next event from the backend.
    + fn read_event(timeout_ms: uint (0)) ?(Event) !TuiError
    // Convenience setup: enters raw mode, alternate screen, hides cursor, and enables bracketed paste. Pair with `defer term.close()`.
    + fn setup(mouse: bool (false)) void !TuiError
    // Shows the hardware cursor (`ESC[?25h`).
    + fn show_cursor() void !TuiError
    // Returns the current terminal dimensions `(width, height)`.
    + fn size() (uint, uint)
    // Creates a `Terminal` backed by a real `AnsiBackend` on stdio.
    + static fn stdio() Terminal
}
```

### Terminal

Double-buffered terminal controller that manages raw mode, screen modes, and diffed rendering.

Always pair terminal setup with `defer term.close()` so the user's shell is restored cleanly.

#### alt_screen

Whether the alternate screen buffer is currently active.

#### backend

Underlying terminal backend.

#### closed

Whether `close()` has been called.

#### cursor_hidden

Whether the hardware cursor is currently hidden.

#### mouse_enabled

Whether SGR 1006 mouse tracking is currently enabled.

#### paste_enabled

Whether bracketed paste mode is currently enabled.

#### raw_mode

Whether raw mode is currently enabled.

#### buffer

Returns the last rendered `Buffer` (useful for inspecting output in tests).

#### clear

Marks the terminal dirty so the next `draw()` clears the screen and performs a full redraw.

#### close

Restores every terminal mode enabled on this instance (mouse, paste, cursor,
alternate screen, style reset, and raw mode). Safe to call multiple times or in `defer`.

#### disable_bracketed_paste

Disables bracketed paste mode (`ESC[?2004l`).

#### disable_mouse

Disables SGR 1006 mouse tracking.

#### draw

Renders one frame through `render_fn`, diffs against the previous frame, and flushes
only the changed cells and minimal ANSI style transitions in a single write.

#### enable_bracketed_paste

Enables bracketed paste mode (`ESC[?2004h`).

#### enable_mouse

Enables SGR 1006 mouse press/release/drag/scroll reporting.

#### enter_alt_screen

Switches to the alternate screen buffer (`ESC[?1049h`).

#### enter_raw_mode

Enables raw mode on the backend.

#### hide_cursor

Hides the hardware cursor (`ESC[?25l`).

#### leave_alt_screen

Returns to the primary screen buffer (`ESC[?1049l`).

#### leave_raw_mode

Restores cooked mode on the backend.

#### new

Creates a `Terminal` backed by `backend`.

#### read_event

Waits up to `timeout_ms` milliseconds for the next event from the backend.

Automatically marks the buffers for a full redraw when a `ResizeEvent` arrives.

#### setup

Convenience setup: enters raw mode, alternate screen, hides cursor, and enables
bracketed paste. Pair with `defer term.close()`.

#### show_cursor

Shows the hardware cursor (`ESC[?25h`).

#### size

Returns the current terminal dimensions `(width, height)`.

#### stdio

Creates a `Terminal` backed by a real `AnsiBackend` on stdio.

```js
// In-memory headless terminal backend for deterministic testing of rendering, diffs, events, resizing, and widgets without a real TTY.
+ class TestBackend is Backend {
    // Current simulated terminal height in rows.
    + height: uint
    // Whether `enter_raw_mode()` is currently active.
    + raw_mode: bool
    // Current simulated terminal width in columns.
    + width: uint

    // Clears the captured output buffer.
    + fn clear_output() void
    // Marks simulated raw mode as active.
    + fn enter_raw_mode() void !TuiError
    // Marks simulated raw mode as inactive.
    + fn leave_raw_mode() void !TuiError
    // Creates a `TestBackend` of `width` columns by `height` rows.
    + static fn new(width: uint, height: uint) TestBackend
    // Queues a typed `Event` to be returned by `read_event()`.
    + fn push_event(ev: Event) void
    // Feeds raw ANSI input bytes through `EventParser` and queues all decoded events.
    + fn push_input(raw: String) void
    // Pops the next queued event, or returns `null` if the queue is empty.
    + fn read_event(_timeout_ms: uint) ?(Event) !TuiError
    // Changes the simulated terminal size and queues a `ResizeEvent`.
    + fn resize(width: uint, height: uint) void
    // Returns the simulated `(width, height)`.
    + fn size() (uint, uint)
    // Returns all captured output written since the last call and clears the capture buffer.
    + fn take_output() String
    // Captures written ANSI output bytes into the in-memory output buffer.
    + fn write(bytes: local &[u8]) void !TuiError
    // Returns all captured output written so far as a string without clearing it.
    + fn written_string() String
}
```

### TestBackend

In-memory headless terminal backend for deterministic testing of rendering, diffs,
events, resizing, and widgets without a real TTY.

#### height

Current simulated terminal height in rows.

#### raw_mode

Whether `enter_raw_mode()` is currently active.

#### width

Current simulated terminal width in columns.

#### clear_output

Clears the captured output buffer.

#### enter_raw_mode

Marks simulated raw mode as active.

#### leave_raw_mode

Marks simulated raw mode as inactive.

#### new

Creates a `TestBackend` of `width` columns by `height` rows.

#### push_event

Queues a typed `Event` to be returned by `read_event()`.

#### push_input

Feeds raw ANSI input bytes through `EventParser` and queues all decoded events.

#### read_event

Pops the next queued event, or returns `null` if the queue is empty.

#### resize

Changes the simulated terminal size and queues a `ResizeEvent`.

#### size

Returns the simulated `(width, height)`.

#### take_output

Returns all captured output written since the last call and clears the capture buffer.

#### write

Captures written ANSI output bytes into the in-memory output buffer.

#### written_string

Returns all captured output written so far as a string without clearing it.

```js
// Editable single-line UTF-8 text input widget with cursor navigation, horizontal scrolling, placeholder text, and built-in key and paste handlers.
+ class TextInput is Widget {
    // Optional surrounding `Block`.
    + block: ?Block
    // Cursor position in Unicode codepoints (`0 .. char_count`).
    + cursor: uint
    // Style applied to the cursor cell when `focused` is true.
    + cursor_style: Style
    // Whether the input is focused and should display its cursor.
    + focused: bool
    // Placeholder text displayed when `value` is empty.
    + placeholder: String
    // Style for `placeholder` text.
    + placeholder_style: Style
    // Horizontal viewport scroll offset in display columns.
    + scroll: uint
    // Base text style.
    + style: Style
    // Current UTF-8 text content.
    + value: String

    // Clears the text content and resets cursor and scroll to 0.
    + fn clear() void
    // Returns the screen coordinates `(x, y)` of the input cursor within `area`, suitable for `frame.set_cursor(pos.x, pos.y)`.
    + fn cursor_screen_pos(area: Rect) CursorPos
    // Handles a `KeyEvent` (typing, Backspace, Delete, Left/Right, Home/End, Ctrl+A/E/U/K/W).
    + fn handle_key(ev: KeyEvent) bool
    // Inserts pasted text at the cursor, replacing line breaks with spaces.
    + fn handle_paste(ev: PasteEvent) void
    // Creates an empty `TextInput` with an optional `placeholder`.
    + static fn new(placeholder: String ("")) TextInput
    // Renders the input value (or placeholder) and cursor highlight into `buf`.
    + fn render(area: Rect, buf: Buffer) void
    // Replaces the text content and moves the cursor to the end.
    + fn set_value(text: String) void
}
```

### TextInput

Editable single-line UTF-8 text input widget with cursor navigation, horizontal scrolling,
placeholder text, and built-in key and paste handlers.

#### block

Optional surrounding `Block`.

#### cursor

Cursor position in Unicode codepoints (`0 .. char_count`).

#### cursor_style

Style applied to the cursor cell when `focused` is true.

#### focused

Whether the input is focused and should display its cursor.

#### placeholder

Placeholder text displayed when `value` is empty.

#### placeholder_style

Style for `placeholder` text.

#### scroll

Horizontal viewport scroll offset in display columns.

#### style

Base text style.

#### value

Current UTF-8 text content.

#### clear

Clears the text content and resets cursor and scroll to 0.

#### cursor_screen_pos

Returns the screen coordinates `(x, y)` of the input cursor within `area`, suitable for
`frame.set_cursor(pos.x, pos.y)`.

#### handle_key

Handles a `KeyEvent` (typing, Backspace, Delete, Left/Right, Home/End, Ctrl+A/E/U/K/W).

Returns `true` if the event was handled.

#### handle_paste

Inserts pasted text at the cursor, replacing line breaks with spaces.

#### new

Creates an empty `TextInput` with an optional `placeholder`.

#### render

Renders the input value (or placeholder) and cursor highlight into `buf`.

#### set_value

Replaces the text content and moves the cursor to the end.

```js
// Emitted periodically by `run_app` when a tick interval elapses.
+ class TickEvent {
    // Milliseconds elapsed since the previous tick.
    + elapsed_ms: uint
}
```

### TickEvent

Emitted periodically by `run_app` when a tick interval elapses.

#### elapsed_ms

Milliseconds elapsed since the previous tick.

```js
// Thrown by terminal operations when raw mode, terminal queries, or I/O fail.
+ union TuiError {
    not_a_tty(String)
    terminal_init(String)
    io_error(String)
    unsupported(String)

    + get message: String
    + fn to_string() String
}
```

### TuiError

Thrown by terminal operations when raw mode, terminal queries, or I/O fail.

- `not_a_tty(message)`: the file descriptor is not connected to a terminal.
- `terminal_init(message)`: setting up or restoring raw mode failed.
- `io_error(message)`: reading from or writing to the terminal failed.
- `unsupported(message)`: the operation is not supported on this platform.

```js
// Interface implemented by all renderable UI components.
+ interface Widget {
    // Draws this widget into `buf` within the bounding rectangle `area`.
    + fn render(area: Rect, buf: Buffer) void
}
```

### Widget

Interface implemented by all renderable UI components.

#### render

Draws this widget into `buf` within the bounding rectangle `area`.

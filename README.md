# fastfetch-vertical

A vertical, column-based fastfetch layout that opens in its own
floating window.

## Requirements

- **fastfetch** (a recent version)
- **python3**
- **kitty**, for the full experience: a dedicated floating window
  with a logo. If kitty isn't installed, the script still runs as
  plain text columns in whatever terminal launched it, without a
  logo or floating window.
- **Hyprland**, with a window rule so the window floats and centers
  instead of tiling normally.

## Setup

**1. Make the script executable and run it.**

In place:
```bash
chmod +x fastfetch-vertical
./fastfetch-vertical
```

Or install it to your `$PATH` (e.g. `~/.local/bin/`) to run it by
name from anywhere:
```bash
mv fastfetch-vertical ~/.local/bin/
chmod +x ~/.local/bin/fastfetch-vertical
fastfetch-vertical
```

**2. Add a window rule to your Hyprland config**, matching whichever
format you use.

`hyprland.lua`:
```lua
hl.window_rule({
    match = { class = "^(fastfetch-vertical)$" },
    float = true,
    center = true,
})
```

`hyprland.conf`:
```
windowrule = float on, center on, match:class ^(fastfetch-vertical)$
```

Run the script again and it should open as a floating window.

## Configuration

Set any of these before running the script, either directly in your
terminal for a one-off run, or in your shell config (`~/.bashrc`,
`~/.zshrc`, etc.) to make them permanent.

| Variable | Purpose | Accepted values | Default |
|---|---|---|---|
| `FASTFETCH_VERTICAL_ACCENT` | Color of the column titles | Hex color, with or without `#` | `89b4fa` |
| `FASTFETCH_VERTICAL_LOGO` | Logo image | Path to a `.png` file | `~/.config/fastfetch/logo.png` |
| `FASTFETCH_VERTICAL_FONT_SIZE` | Font size (kitty only) | `8`, `10`, or `12` | `8` |

Example:

```bash
export FASTFETCH_VERTICAL_ACCENT="#89b4fa"
export FASTFETCH_VERTICAL_LOGO="$HOME/Pictures/logo.png"
export FASTFETCH_VERTICAL_FONT_SIZE="10"

./fastfetch-vertical
```

## Notes for AI-assisted debugging

If you're pasting this script into an AI assistant for help, a few
architectural points aren't obvious from reading it top to bottom.
The script relaunches itself inside a new kitty window using an
`exec` call, guarded by the `FASTFETCH_VERTICAL_FLOATING` environment
variable so it doesn't relaunch infinitely once inside that window --
if kitty isn't found, this relaunch is skipped and the same variable
is set directly instead, so the rest of the script runs unmodified in
whichever terminal launched it. The actual rendering (logo plus text
columns) lives in a `render_fastfetch` bash function, called once at
startup and again on every `SIGWINCH` (terminal resize) via a `trap`,
so the layout re-centers live rather than staying static. Window
height is calculated with a separate, throwaway `fastfetch --pipe`
call before the real window ever opens, since the window's size has
to be set before kitty launches but the content that determines that
size isn't known until fastfetch actually runs; this means the
module list and the "keep only modules with a key of 6 characters or
fewer" filter exist in two places and must be kept in sync if either
changes. The `Colors` column is not parsed from fastfetch's output at
all -- `--pipe` strips ANSI color codes entirely, which would defeat
the point, so those swatches are generated directly from raw ANSI
codes instead. Finally, the custom `center_ansi_aware` padding
function exists because Python's built-in string formatting counts
invisible ANSI escape sequences as visible characters, which would
otherwise misalign any colored text against plain text in the same
row.

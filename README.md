# fastfetch-vertical

A vertical, column-based fastfetch layout that opens in its own
floating kitty window.

## Requires

- **fastfetch**, a reasonably recent version (uses JSON-config-based
  invocation, not the older per-module CLI flags some releases removed)
- **kitty** for the full experience (dedicated floating window,
  logo). If kitty isn't installed, the script now detects that and
  falls back to rendering directly in whatever terminal launched it
  -- text columns only, no logo, no dedicated window -- rather than
  failing outright. The text-column rendering itself is genuinely
  terminal-agnostic (plain ANSI true-color escapes); only the logo
  specifically needs kitty's own image protocol, which most other
  terminals (Alacritty, notably) don't implement at all.
- **python3**
- **Hyprland** (or another compositor with an equivalent mechanism),
  with a window rule floating/centering the `fastfetch-vertical`
  window class -- otherwise it just opens tiled like a normal window.
  This script has no opinion on which Hyprland config format you
  use -- it just launches kitty with a `--class` flag, nothing more.
  Hyprland's own window rule syntax has changed more than once
  recently -- confirmed as of this writing:

  **Lua-based config** (e.g. `hyprland.lua`) -- Hyprland's own wiki
  confirms hyprlang (the traditional `.conf` format below) is now
  deprecated in favor of Lua as of Hyprland 0.55:
  ```lua
  hl.window_rule({
      match = { class = "^(fastfetch-vertical)$" },
      float = true,
      center = true,
  })
  ```

  **Traditional `hyprland.conf`** -- current syntax as of Hyprland
  0.53+ (`windowrulev2`, seen in many older guides, was itself
  deprecated in 0.53 -- confirmed directly from Hyprland's own GitHub
  discussions. If you're on an older release, you may still need the
  older `windowrulev2` syntax instead):
  ```
  windowrule = float on, center on, match:class ^(fastfetch-vertical)$
  ```

## Run it

```bash
chmod +x fastfetch-vertical
./fastfetch-vertical
```

## Optional: color and logo

Both are environment variables, set before running the script. Skip
both for sane defaults.

```bash
# Any hex color, with or without the #
export FASTFETCH_VERTICAL_ACCENT="#89b4fa"

# Path to your own .png
export FASTFETCH_VERTICAL_LOGO="$HOME/Pictures/logo.png"

# One of 8, 10, or 12 -- anything else (or unset) falls back to 8
export FASTFETCH_VERTICAL_FONT_SIZE="10"

./fastfetch-vertical
```

To sync the accent color with your own theming tool instead of a
fixed value, set the variable from that tool's own output:

```bash
export FASTFETCH_VERTICAL_ACCENT="$(your-theme-tool --get-accent)"
```

The script has no built-in knowledge of any specific theming tool --
it just reads whatever hex ends up in the variable.

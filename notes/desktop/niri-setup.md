# Niri Setup Guide (Alongside KDE Plasma)

Niri is a scrollable-tiling Wayland compositor. You can install it alongside KDE Plasma and switch between them at login.

## How Session Switching Works

Display managers (like SDDM) read `.desktop` files from `/usr/share/wayland-sessions/` and `/usr/share/xsessions/` to present available sessions at login. Installing niri adds its session file automatically.

## Installation

Install the base niri package (don't use `cachyos-niri-settings` as it conflicts with `cachyos-kde-settings`):

```fish
sudo pacman -S niri
```

## Configuration

Niri stores its config at `~/.config/niri/config.kdl`.

### Getting a Starter Config

Copy the default config as a starting point:

```fish
mkdir -p ~/.config/niri
cp /usr/share/niri/default-config.kdl ~/.config/niri/config.kdl
```

The default config is well-commented with keybindings documented inline.

## Essential Companion Apps

Niri is just a compositor - you'll need additional tools for a complete desktop experience:

| Purpose | Recommended Apps |
|---------|------------------|
| Status bar | `waybar`, `ironbar` |
| App launcher | `fuzzel`, `wofi`, `rofi-wayland` |
| Notifications | `mako`, `dunst` |
| Screenshots | `grim` + `slurp` |
| Wallpaper | `swaybg`, `hyprpaper` |
| Screen lock | `swaylock` |
| Idle daemon | `swayidle` |

Install a basic set:

```fish
sudo pacman -S waybar fuzzel mako grim slurp swaybg
```

## Switching Between KDE and Niri

1. Log out of your current session
2. At the SDDM login screen, find the **session selector** (usually bottom-left or a dropdown)
3. Select "Niri" or "Plasma (Wayland)" as desired
4. Log in

Your selection is typically remembered for subsequent logins.

## Key Differences from KDE

- **Tiling paradigm**: Windows are arranged automatically in a scrollable strip, not floating
- **Keyboard-driven**: Most actions use keybindings (check config for defaults)
- **Minimal by design**: No built-in panels, app menu, or system tray - you add what you need
- **Wayland-only**: No X11 fallback (XWayland available for legacy apps)

## Auto-starting Apps and Custom Keybindings

### Auto-start Apps at Login

Use `spawn-at-startup` in your `config.kdl` to launch apps when niri starts:

```kdl
spawn-at-startup "waybar"
spawn-at-startup "mako"  // notifications daemon
```

Note: Launchers like rofi don't need auto-start - they run on-demand via keybindings.

### Binding Super+D to Rofi

In the `binds` section of your config:

```kdl
binds {
    // ... other bindings ...

    Mod+D { spawn "rofi" "-show" "drun"; }
}
```

- `Mod` = Super/Windows key in niri
- `-show drun` = show desktop applications
- Other modes: `-show run` (commands), `-show window` (window switcher)

### Reloading Config

After editing `~/.config/niri/config.kdl`:

- **Hot reload**: Press `Super+Shift+R` (default binding)
- **Or**: Log out and back in

## Default Keybindings (Common)

Check your `config.kdl` for the full list, but typical defaults include:

- `Super+T` - Open terminal
- `Super+D` - App launcher
- `Super+Q` - Close window
- `Super+H/J/K/L` or Arrow keys - Navigate windows
- `Super+Shift+E` - Exit niri

## Troubleshooting

### Apps look wrong or don't scale properly

Set these environment variables in `~/.config/niri/config.kdl` or a startup script:

```kdl
environment {
    QT_QPA_PLATFORM "wayland"
    GDK_BACKEND "wayland"
}
```

### XWayland apps not working

Ensure XWayland support is enabled in your config:

```kdl
xwayland {
    enable
}
```

### No cursor in some apps

Install a cursor theme and set it:

```fish
sudo pacman -S breeze  # or another cursor theme
```

Then set in config or via `XCURSOR_THEME` environment variable.

## Resources

- [Niri GitHub](https://github.com/YaLTeR/niri)
- [Niri Wiki](https://github.com/YaLTeR/niri/wiki)
- Config format: KDL (similar to JSON but more readable)

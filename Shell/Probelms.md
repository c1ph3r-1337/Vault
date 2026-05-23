# The Hard Part

The difficult part is NOT rendering.

The hard part is:

## 1. State Synchronization

You need unified:

- workspaces
- media state
- battery state
- network state
- theme state
- notification state

across all frameworks.

---

## 2. Process Management

Switching themes would require:

- killing old processes cleanly
- launching new stack
- avoiding duplicate daemons
- avoiding tray conflicts

Example:

```
pkill waybarpkill ewwqs -c newtheme &
```

---

## 3. Unified Theme Engine

You need converters like:

```
Base theme JSON      ↓Generate:- CSS- SCSS- QML vars- GTK colors- terminal themes
```

This is the REAL engineering challenge.

---

# Example Flow

Suppose:

## User selects:

```
"Cyberpunk Theme"
```

Your orchestrator does:

```
Stop Waybar/EwwLaunch QuickshellGenerate QML theme varsSet wallpaperReload Hyprland bordersReload kitty colors
```

Then later:

## User selects:

```
"Minimal GTK Theme"
```

Now:

```
Kill QuickshellLaunch Waybar + EwwGenerate CSS/SCSSReload GTK
```

---

# This Is Basically:

A:

- desktop shell hypervisor
- shell abstraction layer
- multi-backend desktop framework
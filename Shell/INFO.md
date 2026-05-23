# Typical Hyprland Stack (Traditional)

People often use:

```
Waybar  -> top barEww     -> widgets/panelsRofi    -> launcherDunst   -> notificationsScripts -> glue logic
```

This works, but eventually becomes:

- script-heavy
- fragmented
- harder to maintain

# Quickshell Approach

Quickshell can handle ALL of these inside one framework:

```
BarWidgetsDashboardLauncherOSDNotificationsAnimationsSystem overlaysMedia controlsWorkspace UI
```

using:

- QML
- QtQuick
- JavaScript logic
- reactive components

So instead of:

```
Waybar + Eww + scripts
```

you build:

```
one integrated shell
```
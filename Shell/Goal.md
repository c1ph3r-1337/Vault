To acheive the we can shift the shells to hyde to qucikshell shells 

One shell manager  
↓  
Multiple interchangeable UI backends

where:

- one theme may use Waybar + Eww
- another may use AGS
- another may use Quickshell
- all on the same machine
- switchable dynamically


Shell Orchestrator
│
├── Theme 1
│     ├── Waybar
│     ├── Eww
│     └── GTK CSS
│
├── Theme 2
│     ├── AGS
│     ├── GTK widgets
│     └── JS services
│
├── Theme 3
│     ├── Quickshell
│     ├── QML
│     └── Qt assets
│
└── Shared Services
      ├── Hyprland IPC
      ├── Wallpaper daemon
      ├── Audio
      ├── Notifications
      └── Theme variables

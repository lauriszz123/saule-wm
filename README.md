# saule-wm

The display server and window manager of [Saule Linux](https://github.com/lauriszz123/saule-linux),
written in Saule.

Apps don't know it exists: when `SAULE_DISPLAY` is set, Shine's
`Window.create` opens the window inside this server instead of on a real
screen. The server gets each window's frames through Shine's `Compositor`,
decides where they go, draws them with their title bars, and sends keys and
clicks to the right one.

- server-side decorations
- tiling (a split tree, i3-style) first, floating later, switchable
- own protocol on top of Shine's `Compositor`: no Wayland, no X11

## Running

On the desktop, nested in an ordinary window:

```sh
saule run .
```

With the apps of Saule Linux (Settings, About This System) in the launcher
and the system menu, as `make` in saule-linux builds them:

```sh
(cd ../saule-linux && make apps)
SAULE_APPS=../saule-linux/build/apps saule run .
```

An app given after the `.` starts at once: `saule run . path/to/app`.

The window manager's shortcuts are held with its key, "Mod". On Saule Linux
that is Super, used as a Mac uses Command (Super+Space opens the launcher,
Ctrl+Super+Q locks, Alt+Super+Esc force quits). Nested in another desktop,
which keeps Super for itself, it is:

| Desktop | Mod | Launcher | Lock | Force quit |
|---|---|---|---|---|
| Windows (and WSL) | Ctrl+Alt | Ctrl+Alt+D | Ctrl+Alt+L | Ctrl+Alt+Esc |
| macOS | Ctrl+Option | Ctrl+Option+D | Ctrl+Option+L | Ctrl+Option+Esc |
| Linux (GNOME, KDE) | Alt | Alt+D | Alt+L | Alt+Shift+K |

Quitting the window manager is Ctrl+Alt+Q on Saule Linux, Mod+Shift+E nested.
The rest are the same everywhere: Mod+arrows, Mod+Shift+arrows, Mod+1…4,
Mod+Shift+1…4, Mod+H/V, Mod+R, Mod+Shift+Space (float), Mod+Shift+Q (close).
The system menu shows the keys in use. `SAULE_WM_KEY=super`, `alt` or
`ctrl+alt` overrides the guess, and `SAULE_LOG=wm.keys=trace` logs every key
the window manager receives, for finding out what a desktop swallows.

## Logging

The window manager logs through [saule-log](https://github.com/lauriszz123/saule-log),
one line per event on standard error, and passes on what the apps it started
write, so one terminal shows everything:

```
16:51:59.173 INFO  wm.session  window opened  floating=true id=1 size=300x340 title="About This System"
16:51:59.840 DEBUG settings  connected  service=saule.wm
```

`SAULE_LOG` chooses how much: `SAULE_LOG=debug`, or `SAULE_LOG=info,wm.session=debug`
for one part (`wm`, `wm.session`, `wm.launcher`, `wm.settings`, and the apps'
own names). `SAULE_LOG_FILE=/tmp/wm.log` keeps a copy. On Saule Linux, pass
them on the kernel command line: `make APPEND="saule.env.SAULE_LOG=debug" run-netbook`.

## Plan

See phases 4–6 in the Saule Linux
[ROADMAP](https://github.com/lauriszz123/saule-linux/blob/main/ROADMAP.md).

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
Ctrl+Space goes on to the next keyboard layout (Ctrl+Alt+K nested).
The system menu shows the keys in use. `SAULE_WM_KEY=super`, `alt` or
`ctrl+alt` overrides the guess, and `SAULE_LOG=wm.keys=trace` logs every key
the window manager receives, for finding out what a desktop swallows.

## First start

The first time a Saule Linux with a disk starts, the window manager asks
three things, on a card over the blurred desktop: a name for the computer
(its own, as its maker named it, is offered to keep or type over),
its time zone (a search for a city), and the look (an accent and a
wallpaper). Enter goes on, Escape back. Each choice takes effect as it is
made and is kept: the look with the other settings, the name and the zone
as files in `$SAULE_DATA/system`, which Saule Linux's init acts on (naming
the computer and setting its clock take root). The clock in the bar is in
that zone from then on.

It is shown once. `SAULE_WM_SETUP=1 saule run .` shows it anywhere, for
working on it; nested, the zone is kept but nothing acts on it, so the
time stays UTC.

## Logging in and locking

A Saule Linux installed with a password starts at the lock screen: the
time, who the computer is for, and a field for the password. The same
screen is what Lock Screen and sleep leave, without the Restart and Shut
Down it offers before anybody has been let in. The window manager can't
read what is kept of the password; it asks the system whether what was
typed is right (`Session.submit`, `System.unlock`) and waits for the
answer. With no password a click or Enter unlocks.

`SAULE_WM_PASSWORD=word saule run .` stands in for a password anywhere,
for working on the screen: it starts locked, and `word` unlocks it.

## Tiled or windowed, the Dock, and the app's menu

Windows are arranged one of two ways, switched with Mod+T, from the sun's
menu or in Settings, and remembered:

- **tiled**: each window is given its share of the screen (the split tree);
- **windowed**: every window has its own size and a title bar, is dragged
  about and overlaps the others, as on a Mac. Switching floats every
  window where it stands, and switching back tiles them again, but for
  those that were floated on purpose.

**Minimise** (Mod+M, the yellow button, or the app's menu) puts a window
out of the way in the **Dock**: a strip along the foot of the screen with
the apps there are, a dot under each that has a window open, and the
minimised windows after a divider. Clicking an app brings its window
forward or starts it; clicking a minimised window brings it back. The
Dock can be turned off in Settings.

The **app's name** in the bar is a button, where a Mac has its
application menu: About the app, its Settings if it has any, Minimise,
Float or Tile, Close Window, Force Quit. An app says what it is with
wmapi's `WmApp.describe(name, version, about, settings: true)`; choosing
"Settings…" sends it Ctrl+comma, as a Mac's apps are sent Command+comma.

**Notifications** are cards at the top right for a few seconds
(`WmApp.notify(title, body)`, and the window manager's own: a drive
connected, a screenshot saved). **Screenshots**: Print Screen or
Mod+Shift+S saves the screen as a PNG in `~/Pictures`.

A window that loses the keyboard is always told (`Compositor.sendFocus`),
and Shine then lets go of every key it held: a shortcut that takes the
keyboard away would otherwise leave its Ctrl held in the app for good.

## The computer's own keys

The keys with pictures on them act whatever is on the screen, the lock
screen too: volume up, down and mute, brightness up and down, sleep, and
the power button, which asks whether to shut down. A change of volume or
brightness is shown near the bottom of the screen for a moment, as is the
keyboard layout when its shortcut changes it (`ui/Hud.sau`). They are
keys Shine names on Saule Linux; a desktop the window manager is nested
in keeps them for itself.

**Keyboard layouts** are a setting: up to four, by XKB's names (`lt,us`).
The first is typed with when the computer starts; the shortcut goes round
them, and with more than one chosen the bar shows which is in use.
Until Settings has chosen any, the layout the installer was told is used
(`$SAULE_DATA/system/keyboard`).

## What Settings is told

Settings changes the window manager through `wmapi` (`api/`): its own
settings as `WmOptions`, and the computer as `MachineInfo`: the input
devices Shine found (a keyboard, a mouse, a trackpad, each built in or
plugged in), the sound card, the backlight, the network, the battery, the
computer's name and time zone, and whether there is a password. Settings
lists a pane only where there is something for it to be about, and asks
again every second, so plugging a mouse in brings its pane.

The same service starts apps for other apps (`launch`): the file browser
asks for the editor with the file to open, or a terminal with the folder
to start in, and the window manager starts it as the launcher would.

## The bar's right end

Before the clock, as on a Mac: the keyboard layout where more than one
was chosen, the network (Wi-Fi's strength, or a cable),
the sound, and the battery with how full it is. Clicking one opens its
panel: a slider and a switch for the sound; Wi-Fi's switch and the
networks in range, with a field for a password where one is wanted; the
cable's address. The sun's menu has Sleep.

The window manager can do none of that itself. On Saule Linux a program
runs as root beside it for the purpose (saule-linux's `saule-system`): it
says how things stand in files in `/run/saule/system`, which are read here
a couple of times a second (`wm/System.sau`), and takes requests as files
dropped there. Anywhere else there is no such folder and the bar shows
none of it. For working on it, `SAULE_SYSTEM=folder` names a folder of
such files written by hand, and `SAULE_POWER_SUPPLY=folder` one standing
in for `/sys/class/power_supply`; `dev/` has both, ready:

```sh
SAULE_SYSTEM=dev/system SAULE_POWER_SUPPLY=dev/power saule run .
```

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

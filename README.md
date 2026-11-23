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

On Saule Linux, as the session:

```sh
cd ../saule-linux
make APP=../saule-wm APPS="examples/shine-demo" run-netbook
```

## Plan

See phases 4–6 in the Saule Linux
[ROADMAP](https://github.com/lauriszz123/saule-linux/blob/main/ROADMAP.md).

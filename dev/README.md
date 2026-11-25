# Stand-ins for a computer

The bar's right end shows how the computer stands — its network, its sound,
its battery — which on Saule Linux `saule-system` and the kernel say. Nested
in another desktop nobody says, and the bar shows none of it. These files
say it instead, so the bar and its panels can be worked on anywhere:

```sh
SAULE_SYSTEM=dev/system SAULE_POWER_SUPPLY=dev/power saule run .
```

- `system/power`, `sound`, `network`: as `saule-system` writes them
  (saule-linux's `system/src`). Edit one while the window manager runs and
  the bar follows within half a second.
- `power/BAT0/`: a battery as the kernel lists one in
  `/sys/class/power_supply`.

Nothing answers what is asked here: a volume set in the panel goes back to
what the file says, and a network joined stays unjoined.

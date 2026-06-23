# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What This Is

A heavily-patched fork of dwm 6.8 built to run alongside polybar. The installed binary is named `dwm-polybar` (not `dwm`) so it can coexist with a vanilla dwm install. Configuration is done by editing `config.h` and recompiling — there is no runtime config file.

## Build & Install

```bash
make                  # compile only
make clean install    # full rebuild and install to /usr/local/bin/dwm-polybar
make clean            # remove dwm, *.o, *.tar.gz
make uninstall        # remove installed binary and man page
```

Requires: Xlib headers, libXinerama, libXft, libfontconfig, libX11-xcb, libxcb, libxcb-res, libXrender.

## Source Layout

- `dwm.c` — core WM logic (~3,300 lines); all patches are applied directly here
- `vanitygaps.c` — layout implementations included by `dwm.c` (not compiled separately)
- `drw.c` / `drw.h` — drawing library (text rendering, shapes)
- `util.c` / `util.h` — die() and ecalloc() helpers
- `config.h` — active configuration (edit this to change behavior)
- `config.def.h` — reference default; keep in sync with `config.h` when adding options
- `config.mk` — compiler flags, paths, feature toggles
- `patches/` — all patch files that were applied (for reference and future rebasing)

## Applied Patches

| Patch | Effect |
|---|---|
| vanitygaps-6.2 | Gap-aware layouts; `vanitygaps.c` contains the layout functions |
| pertag-20200914 | Per-tag layout, mfact, nmaster, showbar state |
| scratchpads-20200414 | Named scratchpad windows (spterm, discord, vesktop, TeamSpeak) |
| swallow-20201211 | Terminal window swallowing when opening GUI apps |
| systray-6.7 + alpha + clicks-and-pin | System tray with transparency and click handling |
| alpha-20250918 | Window/bar transparency |
| status2d-6.3 | Color codes in the status string |
| actualfullscreen | True fullscreen toggle |
| fakefullscreen | Fake fullscreen (contained within tile) |
| alwayscenter | Floating windows spawn centered |
| autostart | Runs `~/.dwm/autostart.sh` and `~/.dwm/autostart_blocking.sh` on init |
| attachbottom-6.3 | New clients attach to the bottom of the stack |
| push-20201112 | Push clients down the stack |
| active-tag-underline | Underlines the focused tag in the bar |
| statusallmons-6.2 | Status text shown on all monitors |
| tagpad | Extra padding around tag labels |

`.rej` files exist from patch conflicts during earlier application — check them when rebasing onto a newer dwm version.

## Configuration Notes

- **Font:** JetBrainsMonoNL Nerd Font 14px
- **Color scheme:** Tokyonight dark (`#1a1b26` background)
- **Bar position:** Top; bar is for tags/layout indicator only — status text is fed by polybar, not a shell loop
- **Gaps:** 8px inner and outer (horiz + vert)
- **Layouts defined in config.h:** tile, monocle, spiral, dwindle, deck, bstack, grid, nrowgrid, horizgrid, gaplessgrid, centeredmaster, centeredfloatingmaster, floating (13 total)
- **Tags:** 9 tags; browser on tag 1, games on tag 2, VMs on tag 3, remote tools on tag 4
- **Scratchpads:** toggled per their keybinding; each is a named window class matched in `scratchpads[]`

## Patching Workflow

Patches in `patches/` are reference copies. To apply a new patch or update an existing one:

```bash
patch -p1 < patches/dwm-somepatch.diff
# resolve any .rej files manually, then remove .orig/.rej artifacts
make clean install
```

When pulling a newer dwm base, diff `config.def.h` against the upstream to find new options, then merge into `config.h` manually.

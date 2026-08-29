
# MIDI MuRF Editor — Operator Manual (v2.8)

Control surface for the MF-105M. No sound engine. Sends CC, PC, notes, and pattern SysEx.

Live URL: https://nt1me.github.io/murfeditor/ in MIDIWeb Browser.
After a deploy: force-quit MIDIWeb, reopen, then load the page. Refresh is not enough.

MIDI channel defaults to 1. Pick the port. Hit Refresh if the box was plugged in late.

---

## Pages

Main · Pattern · Keyboard

| Key | Action |
|-----|--------|
| ` (backtick) | Next page |
| Shift+` | Previous page |

Pattern Reset, Clock Reset, and Stop are on every page.

---

## Focus (no mouse)

| Key | Action |
|-----|--------|
| Tab / Shift+Tab | Next / previous control on this page |
| Option+Tab / Option+Shift+Tab | Next / previous section |
| G | Jump to the grid (remembers the control you were on) |
| Tab from the grid | Return to that control |

Number boxes only use 0-9, Backspace, Enter, and arrows. Letter commands still run.
Library Search and the Save-name box swallow letters until you leave them.

---

## Always-on commands

| Key | Action |
|-----|--------|
| P | Send current pattern |
| A | Auto Send on/off |
| Z | Pattern Reset (CC90) |
| C | Clock Reset (note 65) |
| X | Stop (note 108) |
| B | Bypass |
| L | LFO on/off |
| M | Bass / Mids |
| S | Staccato |
| Cmd+Z / Cmd+Shift+Z | Undo / Redo |
| Cmd+X | Clear all steps |
| I | Invert |
| F | Flip bands |
| U | Collapse selection to the caret (Escape does the same on a full keyboard) |

---

## Grid

Eight bands, independent lengths 1-64. Band 1 is the top row.

### Bands

| Key | Action |
|-----|--------|
| Up / Down | Move to one band |
| Shift+Up / Shift+Down | Add or peel bands |
| Click band label | Select that band |
| Shift-click band label | Add or remove without moving the caret |

### Steps

| Key | Action |
|-----|--------|
| Left / Right | Move the caret |
| Shift+Left / Shift+Right | Grow or shrink a step range (does not write) |
| Option+Left / Option+Right | Shorten or lengthen selected bands |
| , (comma) | Selected bands → shortest length |
| . (period) | Selected bands → longest length |
| Cmd+Left / Cmd+Right | Slide the step range |
| Cmd+Up / Cmd+Down | Slide the band set, keep spacing |
| O | Toggle selected steps on selected bands |

Arrows move. O writes.

### Pointer

Tap a cell in range to toggle. Drag the end grip to change length.
Fit 8 / 16 / 24 / 32 / 40 / 48 / 56 / 64. Zoom - / +.
Set 8 / 16 / 32 / 64 applies to the selected bands.

---

## Clipboard and files

| Key | Action |
|-----|--------|
| Cmd+C | Copy selected band(s) |
| Cmd+V | Paste onto selected band(s) |
| Cmd+Shift+C | Copy whole pattern |
| Cmd+Shift+V | Paste whole pattern |

Init Pattern: empty grid, length 1 on every band, LFO 0. Slot unchanged.

Export Current .syx downloads the grid.
Export Library Item downloads the selected library entry.
Import .syx puts files into the library. It does not load the grid and does not send.
Finger or click opens Files. Return usually opens Files; the button may not flash on Return.

---

## Library

Load = grid only. Send with P.
Save Current to Library uses an in-app name box.
Delete All asks Yes/No (arrows + Enter). iCloud files stay put.

Local storage is disposable. Keep .syx files in iCloud and Import when you sit down.

Send as = destination slot (Bass 2-12, Mids 14-24). Slots 1 and 13 are EQ.

---

## Main

Sliders plus typed integers 0-127: Drive, Output, Mix, Envelope, Envelope Scale, Rate, LFO Sweep, eight band levels.
Up/Down on the number box. Left/Right on the slider.
Band levels are labeled in Hertz for Bass and Mids. Moving a band level stops the pattern clock (hardware).

---

## Keyboard

Keys are colored like the grid bands.

| Mode | Pitches |
|------|---------|
| Triggered | C3 D3 E3 F3 G3 A3 B3 C4 |
| Sustained | C5-C6 |
| Mute | C1-C2 |
| Step | note 108 |

Left / Right on this page cycle modes.
Last-note priority is a toggle. Slide-finger legato stays on.

---

## Fast path

1. Force-quit MIDIWeb if you just deployed.
2. Port, channel.
3. ` to Pattern.
4. Init or Load.
5. Fit, set lengths, Shift-select, O.
6. P (or Auto Send).
7. Z / C / X from any page.

---

## Not in 2.8

MIDI clock, Ableton Link, playlist chaining, MIDI learn.
The hardware has no MIDI output, so sliders are send-only.

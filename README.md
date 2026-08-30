
# MIDI MuRF Editor — Operator Manual

Single-page editor for the MF-105M. Sends CC, PC, notes, pattern SysEx, MIDI clock, and performance clips. No sound engine.

Live: GitHub Pages URL you already use, in MIDIWeb or Chrome.
After a deploy: force-quit MIDIWeb or hard-refresh Chrome. A normal refresh is not enough on iPad.

Default MIDI channel 1. Pick the port. ↻ if the box was plugged in late.

---

## Pages

Main · Pattern · Keyboard · Stack · Clock

| Key | Action |
|-----|--------|
| ` (backtick) | Next page |
| Shift+` | Previous page |

---

## Global bar

| Control | What it is |
|---------|------------|
| Clock | Arm F8. Off at launch — the app does not clock the rig until this is on |
| Play | MIDI Start (FA) — pattern to step 1, ticks run |
| Cont | MIDI Continue (FB) — pattern stays on current step, ticks run |
| Stop | MIDI Stop (FC) — box freezes. Does **not** end a recording |
| Rec | Arm / punch out a clip |
| Play clip | Fire the loaded clip on top of the current grid |
| BPM | 20–300, integer. Type, arrows when focused |
| Division | Rate as a clock divider (CC9). Names from the hardware manual |
| Pat Reset | CC90 — pattern to step 1 now |
| Clk Reset | Note 65 — pattern restarts on the **next** tick |
| Map | Digit aliases |
| ? | Hold for captions (or hold Shift+/ ) |

### Transport keys

| Key | Action |
|-----|--------|
| Enter | Play (Start) — unless a number box is focused |
| Shift+Enter | Continue |
| Shift+Space | Device Stop (FC). Clip playhead keeps running if you are recording |
| \ | Rec arm, or punch out if a take is rolling |
| Z | Pattern Reset (CC90) |
| C | Clock Reset (note 65) |
| X | Note 108 **only** on Keyboard / Stack (step / freeze animation) |

Enter / Shift+Enter / Shift+Space / \ are ignored while typing in a name, search, or number field.

---

## Two clocks

**Our playhead** — BPM × 24 PPQN. This is Record and Play clip. It can run while the box is stopped.

**The box** — follows F8 only when Clock is armed and Play/Continue has been sent. Rate knob on the hardware becomes the **division** you pick (CC9). Do not add a PPQN control; the MuRF table is 24 PPQN.

| Message | Box |
|---------|-----|
| Start FA | Step 1, then run |
| Continue FB | Same step, then run |
| Stop FC | Freeze |
| Note 65 | Restart on next tick; ticks keep going |
| CC90 | Step 1 now |
| Note 108 | Step / stop animation (Keyboard & Stack) |

---

## Clips (Clock tab)

v1 records: Mute / Trigger / Sustain notes, 65, 108, CC90, and FA / FB / FC if you press them.

Does **not** record: sliders, SysEx grids, division, BPM.

`\` = arm (red / Arm). Enter while armed starts clock **and** the take. `\` again punches out. Shift+Space during a take writes FC into the clip and stops F8 to the box; our playhead still counts.

Save take / Load / Delete on the Clock tab. Recorded BPM is stored and shown. **Clip BPM** can be changed after load. Play clip uses the **current** header BPM for the ruler unless you set clip BPM and we apply it — change header BPM to hear it faster/slower.

Cap: 10 minutes or 10,000 events.

Clips are not married to a pattern. Any clip on any grid. localStorage + Save take. Export of clips is the library on that tab.

Play clip toggle = overlay. Pattern in the box stays whatever you last sent.

---

## Pattern page

Slot list is **2–12 Bass** and **14–24 Mids**. 1 and 13 are EQ on the hardware (still on Main program change).

Eight bands, lengths 1–64. Band 1 is the top row.

### Grid keys

| Key | Action |
|-----|--------|
| ↑ ↓ | Move band |
| Shift+↑ ↓ | Add / peel bands |
| ← → | Move caret |
| Shift+← → | Step range (does not write) |
| Option+← → | Length − / + |
| Cmd+← → | Slide step range |
| Cmd+↑ ↓ | Slide band set |
| O | Toggle selected steps |
| , / . | Snap to shortest / longest (one band = whole grid; several = the group) |
| [ ] | Caret to step 1 / 64 |
| ; : | Caret −8 |
| ' " | Caret +8 |
| − / = | Zoom |
| N | Seq write on/off |
| G | Focus grid (remembers the last control) |
| Tab | Return from grid to that control |
| ⌘A | All bands on / off (off returns to last single band) |
| ⌘I | Init Pattern |
| ⌘X | Clear all steps |
| I / F | Invert / Flip |
| U or Escape | Collapse selection to caret |

Fit 8…64 and Set 8…64 (same sizes). Row tools: Clr Steps, Init Row, Rnd Steps / Len / Both, Save/Load Row.

### Seq write (N)

Auto Send is forced off. **Q W E R T Y H J** = bands 1–8 at the caret. **Space** = next step. Length grows only for a band you stamp. Other Pattern commands stay live.

### Clipboard / library

| Key | Action |
|-----|--------|
| ⌘C / ⌘V | Copy / paste selected band(s) |
| ⌘⇧C / ⌘⇧V | Copy / paste whole pattern |
| ⌘S | Save current to library (grid + Main sliders/toggles sidecar) |
| ⌘O | Load to grid only (does not send SysEx; does not send filter CCs — those stop the hardware clock) |
| P | Send pattern |
| A | Auto Send (blocked while Seq write is on) |

Import .syx → library, does not replace the grid. Export current / export library item. Deletes are click-only.

Load of an old library item with no sidecar leaves sliders alone.

---

## Keyboard

One mode at a time. Mode buttons or ← →.

Computer keys **A S D F G H J K** = bands 1–8 of the **current** mode. Step mode = A only (note 108).

Last-note priority: Triggered trills. Sustain and Mute are polyphonic.

**V** Latch (Mute / Sustain stick; tap again to release). Trigger ignores Latch.

---

## Stack

Three rows at once:

| Row | Keys | Notes |
|-----|------|-------|
| Mute | 1–8 | C1–C2 |
| Triggered | Q–I | C3–C4 |
| Sustained | A–K | C5–C6 |

Same band cannot be live in two modes — the new one kicks the old one off.

A computer keyboard often reports only **six** keys at once. Latch eight one-by-one, or use fingers / MIDI in. Last-note is per Triggered row, not across Mute.

---

## Main

Sliders + number boxes 0–127. Filter levels are Hertz labels (Bass / Mids). Moving a filter level **stops the hardware pattern clock** (manual). Drive, Mix, Envelope, Rate (free, off-clock), LFO Sweep, toggles: Freq, LFO, Bypass, Staccato, Clock Sync (CC89).

---

## Map and hints

Map: tap Map, tap a **button**, tap 1–0. Factory keys stay. Digits are notes on Stack (`1–8` Mute), so mappings do not fire there.

Hold **?** or the header button: captions on buttons + grid one-liner.

---

## MIDI in / Ableton / Max

Not in this build. Next likely cut: listen on an input and thru/remap to these note numbers (IAC from Max or Live).

---

## Fast path

1. Port. Arm **Clock**. Division 16th. BPM 120. Enter. Orange Rate LED.  
2. Pattern: Init or Load, Seq write or grid, **P**.  
3. Stack: V to latch mutes over a short pattern, snappy envelope.  
4. `\` Rec, play, `\` punch out, Save take, Play clip.

---

## Not in this build

Ableton Link, clock-in, playlist SysEx chaining, PPQN switch, clip-pattern marriage, overdub UI, recording sliders or grids into a clip.

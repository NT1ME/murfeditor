# MIDI MuRF Editor — Operator Manual (2.9.17)

Single-file Web MIDI companion for the Moog MF-105M MIDI MuRF. Pattern SysEx, documented CC/PC, performance notes, MIDI clock master, clips, projects, library.

Host: GitHub Pages. Runtime: MIDIWeb Browser (iPad) or Chrome. After a deploy: force-quit MIDIWeb or hard-refresh.

Default channel 1. Pick the output port in the header. The editor sends; the MuRF does not talk back.

---

## Pages

| Page | Job |
|------|-----|
| Main | Program change, voicing switches, Drive / Output / Mix / Envelope / Scale / Rate / LFO Sweep, filter-band levels |
| Pattern | 8x64 grid, independent lengths, LFO Rate in the dump, slot 1-24, library, Import/Export .syx |
| Keyboard | One row: Triggered / Sustain / Mute / Step |
| Stack | All three play rows at once plus Step |
| Clips | Record / play a take, tap tempo, save/load project |

Slots 1 and 13 are the hardware EQ patterns (empty). Editable: 2-12 Bass, 14-24 Mids.

---

## Header (every page)

Left: title only. Nothing sits under it.

MIDI — connection label, LED, port, refresh, channel (CC102 when you change it).

Clock — Arm, Play, Stop, Cont, Rec, Play clip, BPM, Division.

Under that row, aligned with MIDI/Clock: Pat Reset, Halt, Step, Clk Reset, Map, ?

---

## Pages and focus

| Key | Action |
|-----|--------|
| Backtick | Next page |
| Shift+Backtick | Previous page |
| Tab | Next control on this page |
| Shift+Tab | Previous control |
| Option+Tab | Next section |
| Ctrl/Cmd+Tab | Cycle pages |
| G | Drop Tab focus. Enter becomes Play. On Pattern, G also focuses the grid |
| Enter | Armed button: fire it. BPM box: commit BPM only. Never Play. |
| Esc or U | Collapse step/band selection to the caret |

Typing a name (library save, search) swallows letter shortcuts until you Tab or click out.

---

## Clock

Two timebases:

- Playhead (clips) — 24 PPQN times BPM. Can run in memory while the box is stopped.
- Box — F8 only when Armed and Play/Continue. Division is Rate used as a clock divider (manual table, CC9).

Arm also turns Pattern Clock Sync (CC89) on. Arm does not start ticks. Play does.

Clock is off when the app opens.

| Key / button | MIDI | Notes |
|--------------|------|--------|
| Play / Shift+Enter | FA Start | Step 1, run |
| Cont / Cmd+Enter | FB Continue | Same step, run |
| Stop Clock / Shift+Space | FC Stop | Freeze. Does not punch out a take |
| Rec / backslash | — | Arm / punch out the clip |
| Play clip | — | Replay last take over the current grid |
| BPM | — | 20-300. Arrows plus/minus 1 when focused |
| Division | CC9 | 16 rows from the hardware table |
| Cmd+, / Cmd+. | CC9 | Focus Division and step it. Menu stays closed. Hold to run |
| Z / Pat Reset | CC90 | Jump to step 1 now |
| C / Clk Reset | Note 65 | Restart on next tick |
| X / Step | Note 108 | Step / freeze animation |
| Shift+X / Halt | CC20 | Halt using parked F1 value. Does not rewrite the mix |

Plain comma and period are Pattern length, not Division.

Plain comma and period are Pattern length, not Division.

### Division banks

Three banks. Cmd+B walks Straight → Dotted/trip → Custom → Straight. Label sits next to the Division box.

Cmd+1 is the slowest slot in the live bank. Cmd+0 is the fastest.

Straight: 4 bars, 3 bars, 2 bars, whole, half, quarter, 8th, 16th, 32nd, 64th.

Dotted/trip: dotted whole, dotted half, dotted quarter, dotted 8th, dotted 16th, dotted 32nd, dotted 64th, whole trip, half trip, quarter trip.

Custom: Map on, set the division you want, Cmd+1 through Cmd+0 stores that value in that slot. First launch copies Straight. Saved with the project.

The Division box no longer typeaheads on 1/2/4/8.


T on the Clips page is Tap tempo.

---

## Main

### Program / Voicing

- Pattern dropdown = Program Change 0-23 (display 1-24).
- Freq Bass / Mids — M — CC86
- LFO On/Off — L — CC85
- Bypass — B — CC87 (inverted: UI on = MIDI 0)
- Staccato — Shift+S — CC68 (inverted)
- Pattern Clock Sync — hint K — CC89 (Arm Clock also sets this)

Hints sit under the name, not on the paddle.

### Main controls (0-127)

Drive CC2, Output CC7, Mix CC8, Envelope CC1, Envelope Scale CC70, Rate CC9, LFO Sweep CC3.

Slider or type. Arrows on a focused slider = left/right. Arrows on a focused number box = up/down. Hold to run.

Send on change: Tab off a box or slider only sends if the number actually changed. Walking Filter Levels or Rate with no edit will not Halt or kill the clock. Leaving Envelope does not write Scale.

### Filter levels

F1-F8 = CC20-27. Labels are Bass Hz / Mids Hz. CC20 is also Halt when you fire the Halt button (parked value).

Pattern Reset on Main is the same CC90 as header Z.

---

## Pattern

8 bands x 64 steps. Each band has its own length. The SysEx dump is the whole pattern (slot + LFO Rate + 8 rows).

### Grid mouse / touch

Click a cell in range to toggle it. Drag to paint. The length handle at the end of a row grows/shrinks that band. Fit / Set buttons resize selected bands.

### Grid keys (grid focused; press G if you were in a box)

| Key | Action |
|-----|--------|
| Up / Down | Move caret band |
| Shift+Up / Shift+Down | Add / peel bands (last-in peels first) |
| Left / Right | Move caret step |
| Shift+Left / Shift+Right | Grow / peel a step range from the anchor |
| Option+Left / Option+Right | Shorten / lengthen selected bands |
| Cmd+arrows | Slide the current selection (bands and/or steps) |
| O | Toggle selected cells. Grows length if you write past the end |
| Comma | Selected bands to the shortest length among them |
| Period | Selected bands to the longest length among them |
| [ ] | Caret to step 1 / 64 |
| ; ' | Caret minus 8 / plus 8 |
| - = | Zoom out / in |
| I | Invert selected bands |
| F | Flip channels |
| U or Esc | Collapse selection to the caret |

### Pattern commands

| Key | Action |
|-----|--------|
| P | Send current pattern (SysEx) |
| A | Auto Send on/off |
| N | Seq write on/off (forces Auto Send off) |
| Space | Seq on: advance caret one step |
| Q W E R T Y H J | Seq on: stamp bands 1-8 at the caret. Grows that band only |
| Cmd+Z / Cmd+Shift+Z | Undo / Redo |
| Cmd+X | Clear All steps |
| Cmd+A | All bands / clear band selection |
| Cmd+I | Init Pattern |
| Cmd+C / Cmd+V | Copy / paste selected bands |
| Cmd+Shift+C / Cmd+Shift+V | Copy / paste whole pattern |
| Cmd+S | Save current to library |
| Cmd+O | Load selected library item onto the grid (does not send unless Auto Send is on) |

### Toolbar groups

Edit — Send, Auto Send, Seq write, Undo, Redo, Clear All, Invert, Flip.

Set — All bands, Clear sel, Set 8 / 16 / 24 / 32 / 40 / 48 / 56 / 64 (selected bands).

Fit — Fit 8 through 64 (zoom the grid to that many columns).

Zoom — minus / plus.

Band lengths — eight number boxes. Tab from Band 1 to 8 commits each value as you leave if it changed, then continues.

Clipboard — Copy/Paste Pattern, Copy/Paste Band(s), Init Pattern.

Row ops — Clr Steps, Init Row, Rnd Steps / Len / Both, Save / Load / Del Row (row library).

Files — Export current .syx, Export library item, Import .syx (into the library, not onto the grid).

Library — search, list sorted by slot, Load, Save Current, Delete, Delete All (confirm).

Import once after a cold launch if the picker was sleepy; force-quit MIDIWeb if it stays dead.

---

## Keyboard

Modes: Triggered, Sustain, Mute, Step.

Triggered / Sustain / Mute keys: note name on top, letter under it.

All three modes use A S D F G H J K (bands 1 through 8). Step has no letters; header X / note 108 is Step.

Left / Right (no modifiers) cycle Triggered, Sustain, Mute, Step.

| Control | Key |
|---------|-----|
| Latch | V |
| Last-note priority | N |
| Pattern clock reset | C or the Note 65 button |
| Step | X |

Triggered — one-shots. Last-note on = mono trill (hold A, tap S, release S returns to A). Latch ignored.

Sustain — notes stay until release, or until Latch holds them.

Mute — mute map (C1 area). Latch holds mutes while the clock runs.

Latch memories are separate. Mute lights stay on the Mute keys when you visit Sustain and do nothing. Come back: still lit. Play that band in Sustain or Triggered: it leaves the Mute latch (clock also stops, as the box does).

Legato slide on the glass is always on.

---

## Stack

Three rows at once:

| Row | Computer keys | MIDI |
|-----|---------------|------|
| Mute | 1 2 3 4 5 6 7 8 | C1 to C2 white keys |
| Triggered | Q W E R T Y U I | C3 to C4 white keys |
| Sustain | A S D F G H J K | C5 to C6 white keys |

Note on top, letter under. Same Latch / Last-note / Note 65 / Step buttons as Keyboard.

Same band cannot be two modes at once (playing Sustain band 8 drops a Mute latch on band 8).

Computer keyboards often cap at six keys down. Latch or use fingers for eight.

Stack Mute lights follow the Keyboard Mute latch.

---

## Clips

Records: Mute / Trigger / Sustain notes, Note 65, Note 108, CC90, and FA / FB / FC if you press them.

Does not record: sliders or SysEx dumps.

Does record: notes, 65, 108, CC90, FA / FB / FC, and division (CC9).

Backslash arm, Enter starts clock plus take, play, backslash punch out.

Shift+Space during a take writes FC into the clip; the take keeps rolling until you punch out.

Save take / Load / Delete on the Clips page. Cmd+S save take, Cmd+Shift+S save project, Cmd+O load take, Cmd+Shift+O open project, Cmd+X delete clip (on this page only).

Play clip overlays the current grid. A clip is not married to a pattern.

Cap: 10 minutes or 10k events. localStorage plus explicit Save.

Tap (T) averages taps into BPM.

---

## Projects

Save project downloads JSON.

Restores: current grid plus slot plus LFO, Main sliders and switches, pattern library, row library, clips plus loaded clip name, digit Map, BPM plus division.

Load leaves Clock / Play / Rec off. Does not send CC20-27 or Rate (those stop or hijack the box clock). P and Play when you mean it.

---

## Map and hints

Map, click a button, press digit 1 through 0. Factory shortcuts stay. Stack 1-8 stay Mute notes; they are not Map aliases.

Hold ? (Shift+/) for on-screen captions.

---

## MIDI IDs

| Kind | ID | What |
|------|----|------|
| SysEx | F0 04 0A 05 ... F7 | Full pattern dump |
| PC | 0-23 | Pattern 1-24 |
| CC1 / 2 / 3 | Envelope / Drive / LFO Sweep |
| CC7 / 8 / 9 | Output / Mix / Rate (divider when clocked) |
| CC20-27 | F1-F8 levels (CC20 = Halt payload) |
| CC68 / 70 | Staccato / Envelope Scale |
| CC85 / 86 / 87 | LFO / Freq / Bypass |
| CC89 / 90 | Clock Sync / Pattern Reset |
| CC102 | Set device channel (sent on ch 1 as vehicle) |
| Note 24-36 | Mute bands |
| Note 48-60 | Triggered bands |
| Note 72-84 | Sustain bands |
| Note 65 | Clock reset |
| Note 108 | Step |
| FA FB FC | Start / Continue / Stop |
| F8 | Clock tick (24 PPQN) |

---

## Fast path

Port, Arm Clock (Sync on), Cmd+. to a 16th, BPM 120, G, Enter.

Pattern: G, write, P.

Stack: V Latch, hold mutes, play.

Backslash Rec, play, backslash, Save take, Play clip.

Save project when the jam is a jam.

---

## Not in 2.9.15 (3.0 pile)

MIDI input / Twister learn, divider sample-and-hold, playlist (Halt then SysEx then Start), Ableton Link, clock-in, overdub UI, recording the grid into a clip, PPQN switch.

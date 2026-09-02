# MF-105M Virtual Controller — Operator Manual

Single-page Web MIDI editor and performer for the Moog MIDI MuRF (MF-105M).

It sends. The box does not talk back. Default MIDI channel is 1. Pick the output port in the header.

**Host:** GitHub Pages. **Runtime:** MIDIWeb Browser on iPad, or a desktop browser with Web MIDI. After a deploy, force-quit MIDIWeb or hard-refresh the page. A plain refresh is not enough if Import or MIDI looks stuck.

Slots **1** and **13** are the hardware EQ patterns (no animation). Editable slots: **2–12 Bass**, **14–24 Mids**.

---

## What each page is for

| Page | Job |
|------|-----|
| Main | Program change, Bass/Mids, LFO, Bypass, Staccato, Clock Sync, Drive / Output / Mix / Envelope / Scale / Rate / LFO Sweep, eight filter levels |
| Pattern | 8 × 64 grid, independent band lengths, LFO Rate inside the dump, library, Import / Export .syx |
| Keyboard | One performance row: Triggered, Sustain, Mute, or Step |
| Stack | Mute, Triggered, and Sustain rows at once, plus Step |
| Clips | Record and play a take. Loop / Once / Once + Halt for that take. Tap tempo. Save / load project lives in the header |
| Playlist | Two independent lists that share the same clock: a pattern chain and a clip list |

---

## Header

Always visible.

**MIDI** — connection label, activity LED, port, refresh, channel. Changing channel sends CC102 (the editor still transmits on the channel you picked).

**Project** — Save project / Open project.

**Clock**

- Clock — arm MIDI clock listen and turn Pattern Clock Sync (CC89) on. Does not start ticks.
- Play — Start (FA). Step 1.
- Stop Clock — Stop (FC). Freezes the box clock. Does not punch out a recording.
- Cont — Continue (FB). Same step, run again.
- Pause all — Halt plus Stop Clock.
- Rec — arm or punch out a take.
- Play clip — play the loaded recorded clip on top of whatever pattern is current.
- BPM — 20–300.
- Division — Rate used as a clock divider (CC9). Sixteen hardware values.
- Beat LED — quarter-note pulse while the clock runs.

**Meters** (same width)

- Sand — recorded clip (Play clip).
- Blue — playlist pattern list.
- Mint — playlist clip list.

**Reset lists** — rewind both playlist lists, clear their highlights and meters, stop and reset the clock. Does not re-dump patterns to the box.

**Transport that hits the box**

- Pat Reset — CC90. Jump the MuRF pattern to step 1 now.
- Halt — CC20 at the parked F1 level. Stops the MuRF sequencer. Does not rewrite the mix.
- Step — note 108. Step / freeze animation.
- Clk Reset — note 65. Restart on the next tick.
- Reset all — Halt + stop clock + stop clip + rewind lists in memory + notes off + pattern to step 1.
- Map — bind digit keys 1–0 to buttons.
- ? — hold for on-screen shortcut captions.

---

## Moving around without a mouse

| Key | What it does |
|-----|----------------|
| Backtick | Next page |
| Shift+Backtick | Previous page |
| Tab | Next control on this page |
| Shift+Tab | Previous control |
| Option+Tab | Next section on the page |
| G | Drop Tab focus so letter shortcuts work again. On Pattern, G also focuses the grid |
| Enter | Fire the armed button, or commit a number box. Enter is never Play |
| Esc or U | Collapse the grid selection to the caret |

If you are typing a name (save to library, search, save list), letter shortcuts are off until you Tab or click out.

---

## Clock, in plain language

Two clocks live here.

**The app clock** is 24 pulses per quarter note at the BPM you set. Play starts it. Stop Clock stops it. Lists and clips count this clock.

**The box clock** only moves when Clock is armed and Play or Continue has been sent. Then the app sends F8 ticks. Division (the Rate knob, CC9) is how many of those ticks equal one MuRF step.

Arming Clock turns Pattern Clock Sync on. Arming does not start anything. Play does.

Clock is off when the app opens.

| Control | Key | MIDI |
|---------|-----|------|
| Clock arm | Shift+C | CC89 |
| Play | Shift+Enter | Start (FA) |
| Continue | Cmd+Enter | Continue (FB) |
| Stop Clock | Shift+Space | Stop (FC) |
| Pause all | Option+Space | Halt + Stop |
| Rec | Backslash | — |
| Play clip | Cmd+Shift+Enter | — |
| Reset lists | Option+X | Stop + rewind lists |
| Reset all | Option+Z | Halt + stop + panic |
| Pat Reset | Z | CC90 |
| Halt | Shift+X | CC20 |
| Step | X | Note 108 |
| Clk Reset | C | Note 65 |

### Division banks

Cmd+B walks Straight → Dotted/trip → Custom → Straight.

Cmd+1 is the slowest slot in the live bank. Cmd+0 is the fastest.

- Straight: 4 bars, 3 bars, 2 bars, whole, half, quarter, 8th, 16th, 32nd, 64th.
- Dotted/trip: dotted whole through dotted 64th, then whole trip, half trip, quarter trip.
- Custom: turn Map on, set the division, Cmd+1 through Cmd+0 stores that value. First launch copies Straight. Saved with the project.

Cmd+, and Cmd+. step the Division box without opening the menu. Hold to run. Plain comma and period are pattern-length commands, not Division.

T on the Clips page is Tap tempo.

---

## Main

**Program / Voicing**

- Pattern menu = Program Change 0–23 (shown as 1–24).
- Frequency Bass / Mids — M — CC86.
- LFO On/Off — L — CC85.
- Bypass — B — CC87 (UI on = MIDI 0).
- Staccato — Shift+S — CC68 (inverted).
- Pattern Clock Sync — Shift+C — CC89 (same as arming Clock).

**Main controls (0–127)**

Drive CC2, Output CC7, Mix CC8, Envelope CC1, Envelope Scale CC70, Rate CC9, LFO Sweep CC3.

Slider or type. Focused slider: left / right. Focused number box: up / down. Hold to run.

Tabbing off a box only sends if the number changed. Walking Filter Levels or Rate with no edit will not Halt or steal the clock. Leaving Envelope does not write Scale.

**Filter levels**

F1–F8 = CC20–27. Labels are Bass Hz over Mids Hz. CC20 is also the Halt payload when you press Halt (parked F1 value).

---

## Pattern

Eight bands, sixty-four steps. Each band has its own length. A send is always a full dump: slot + LFO Rate + all eight rows.

### Mouse and touch

Click a cell inside the current length to toggle it. Drag to paint. The handle at the end of a row grows or shrinks that band. Set buttons change length of the selected bands. Fit buttons zoom the view.

### Grid keys (press G if you were in a box)

| Key | Action |
|-----|--------|
| Up / Down | Move the caret to another band |
| Shift+Up / Shift+Down | Add or peel bands. The last band you added is the first one peeled |
| Left / Right | Move the caret to another step |
| Shift+Left / Shift+Right | Grow or peel a step range from the anchor |
| Option+Left / Option+Right | Shorten or lengthen the selected bands |
| Cmd+arrows | Slide the current selection (bands and/or steps) |
| O | Toggle the selected cells. Writing past the end grows the length |
| Comma | Selected bands become as short as the shortest one |
| Period | Selected bands become as long as the longest one |
| [ ] | Caret to step 1 / step 64 |
| ; ' | Caret minus 8 / plus 8 |
| - = | Zoom out / in |
| I | Invert selected bands |
| F | Flip channels |
| U or Esc | Collapse selection to the caret |

### Pattern commands

| Key | Action |
|-----|--------|
| P | Send the current pattern (SysEx) |
| A | Auto Send on/off |
| N | Seq write on/off (turns Auto Send off) |
| Space | Seq write on: advance the caret one step |
| Q W E R T Y H J | Seq write on: stamp bands 1–8 at the caret |
| Cmd+Z / Cmd+Shift+Z | Undo / Redo |
| Cmd+X | Clear all steps |
| Cmd+A | Select all bands, or clear the band selection |
| Cmd+I | Init Pattern |
| Cmd+C / Cmd+V | Copy / paste selected bands |
| Cmd+Shift+C / Cmd+Shift+V | Copy / paste the whole pattern |
| Cmd+S | Save current pattern to the library |
| Cmd+O | Load the selected library item onto the grid (does not send unless Auto Send is on) |

### Toolbar groups

- **Edit** — Send, Auto Send, Seq write, Undo, Redo, Clear All, Invert, Flip.
- **Set** — All bands, Clear selection, Set 8 / 16 / 24 / 32 / 40 / 48 / 56 / 64 (selected bands).
- **Fit** — Fit 8 through 64 (how many columns you can see).
- **Zoom** — minus / plus.
- **Band lengths** — eight number boxes. Tab from Band 1 to Band 8 commits each value if it changed.
- **Clipboard** — Copy / Paste Pattern, Copy / Paste Band(s), Init Pattern.
- **Row ops** — Clear steps, Init row, random steps / length / both, save / load / delete a row.
- **Files** — Export current .syx, export the selected library item, import .syx into the library (not onto the grid).
- **Library** — search, list sorted by slot, Load, Save Current, Delete, Delete All (asks first).

If Import does nothing on the first try after a cold launch, force-quit MIDIWeb and open the page again.

---

## Keyboard

One row. Modes: Triggered, Sustain, Mute, Step.

Triggered / Sustain / Mute keys show the note name on top and the letter underneath. All three modes use A S D F G H J K for bands 1–8. Step has no letters; header X (note 108) is Step.

Left / Right with no modifiers cycle Triggered → Sustain → Mute → Step.

| Control | Key |
|---------|-----|
| Latch | V |
| Last-note priority | N |
| Pattern clock reset | C, or the Note 65 button |
| Step | X |

**Triggered** — one-shots. Last-note on means a mono trill: hold A, tap S, release S, A sounds again. Latch is ignored.

**Sustain** — notes stay until you let go, or until Latch holds them.

**Mute** — mute map (C1 area). Latch holds mutes while the clock runs.

Latch memories are separate per mode. Mute lights stay on the Mute keys when you visit Sustain. Playing that band in Sustain or Triggered drops the Mute latch (the box also stops the clock, which is hardware behavior).

Finger slide on the glass is always on.

---

## Stack

Three rows at once.

| Row | Computer keys | MIDI |
|-----|---------------|------|
| Mute | 1 2 3 4 5 6 7 8 | C1 to C2 white keys |
| Triggered | Q W E R T Y U I | C3 to C4 white keys |
| Sustain | A S D F G H J K | C5 to C6 white keys |

Same Latch / Last-note / Note 65 / Step buttons as Keyboard.

A band cannot be two modes at once. Playing Sustain band 8 drops a Mute latch on band 8.

Computer keyboards often cap at six keys down. Latch, or use fingers, if you need all eight.

Stack Mute lights follow the Keyboard Mute latch.

When a clip plays Mute / Trigger / Sustain notes — from Play clip or from the playlist clip list — those keys light on Keyboard and Stack for the length of each event.

---

## Clips

A clip is a recorded take of performance and transport. It is not married to a pattern.

**Records:** Mute, Trigger, and Sustain notes; Note 65; Note 108; CC90; Start / Continue / Stop if you press them; BPM changes; Division (CC9).

**Does not record:** sliders, SysEx dumps, or the pattern grid.

**Workflow**

1. Backslash arms Rec.
2. Shift+Enter starts the clock and the take.
3. Play.
4. Backslash punches out.

Shift+Space during a take writes Stop into the clip. The take keeps rolling until you punch out.

Save take / Load / Delete live on the Clips page.

On the Clips page only: Cmd+S saves the take, Cmd+O loads a take, Cmd+X deletes the selected clip.

Play clip (header) overlays the current grid. Loop / Once / Once + Halt for that recorded clip are on the Clips page, not in the header.

Cap: about 10 minutes or 10,000 events. Saved in localStorage when you press Save take, and inside a project file.

T is Tap tempo. Averages taps into BPM.

---

## Playlist

Two lists. They share the app clock. They do not have to be the same length.

**Pattern list** — what the MuRF sequencer is playing.  
**Clip list** — what performance clip is firing on top.

Each list has its own row count. Only that many rows are drawn. Each list has its own Loop / Once / Once + Halt.

### Pattern row types

There is no blank pattern cell. Every visible pattern row is one of:

- **Pattern** — pick a library pattern. Set chain dumps it to a locked slot (row 1 → slot 2, then 3, 4… skipping 1 and 13). While the list runs, that row is a program change only.
- **Rest** — Halt. The MuRF sequencer stops. Notes on a clip in the same window will not speak. After a rest, the next Pattern or EQ row is program change plus Start.
- **EQ Bass** — program change to slot 1. No Halt. Clock keeps running. Audio still comes through the EQ until a clip takes the bands.
- **EQ Mids** — program change to slot 13. Same rules as EQ Bass.

### Clip rows

A clip row may be empty. An empty clip row still lasts as many bars as you set. That is how a clip on row 3 lines up with pattern row 3: three clip rows, first two empty.

The clip list does not have to cover the whole pattern list. One four-bar clip under a twelve-bar pattern list plays four bars and then sits quiet, unless Clip list is set to Loop.

### Dump vs Chain

- **Dump** — you pick the destination slot on each Pattern row. Send this row / Send all rows writes SysEx now. Good for parking patterns without playing a list.
- **Chain** — slots are locked to row order. **Set chain** dumps every Pattern row once, then program-changes to the first destination. After that, Play only sends program changes (and Start after a rest). No SysEx while the list is running.

You must Set chain again if you change row count, row type, or which pattern sits on a row.

### How to run a list

1. Set how many pattern rows and how many clip rows you want.
2. Fill them.
3. Chain on, Set chain.
4. Play.

Play starts both lists if they have work to do. A clip list with no named clips does not start.

**Reset lists** (Playlist page or header, Option+X) rewinds both lists to row 1, zeros both playlist meters, and stops the clock. It does not dump again. Set chain stays on.

### Loop / Once / Once + Halt

Each list has its own ending.

- Loop — that list wraps to row 1. The highlight follows.
- Once — that list stops. The other list can keep going.
- Once + Halt — that list ends, Halt, Stop Clock. The other list stops too.

### Highlights and meters

The pattern highlight follows the pattern list. The clip highlight follows the clip list. They can be on different rows.

Blue meter = pattern list. Mint meter = clip list.

---

## Projects

Save project downloads a JSON file.

It stores: the current grid, slot, LFO Rate, Main sliders and switches, pattern library, row library, clips and the loaded clip name, digit Map, BPM, division, custom division bank, and the current playlist (row counts, row types, bars, attached pattern and clip names, plus copies of those patterns and clips).

Load leaves Clock / Play / Rec off. It does not send filter levels or Rate (those stop or hijack the box). Press P or Play when you mean it.

Playlist lists can also be saved, loaded, exported, and imported on the Playlist page without touching the rest of the project.

---

## Map and hints

Map, click a button, press digit 1 through 0. Factory shortcuts stay. Stack 1–8 stay Mute notes; they are not Map aliases.

Hold ? (Shift+/) for on-screen captions.

---

## MIDI IDs

| Kind | ID | What |
|------|----|------|
| SysEx | F0 04 0A 05 … F7 | Full pattern dump |
| Program change | 0–23 | Pattern 1–24 |
| CC1 / 2 / 3 | Envelope / Drive / LFO Sweep |
| CC7 / 8 / 9 | Output / Mix / Rate (divider when clocked) |
| CC20–27 | F1–F8 levels (CC20 is also the Halt payload) |
| CC68 / 70 | Staccato / Envelope Scale |
| CC85 / 86 / 87 | LFO / Frequency / Bypass |
| CC89 / 90 | Clock Sync / Pattern Reset |
| CC102 | Set device channel |
| Notes 24–36 | Mute bands |
| Notes 48–60 | Triggered bands |
| Notes 72–84 | Sustain bands |
| Note 65 | Clock reset |
| Note 108 | Step |
| FA / FB / FC | Start / Continue / Stop |
| F8 | Clock tick (24 PPQN) |

---

## Fast path

Port. Shift+C to arm Clock. Cmd+. to a 16th. BPM 120. G. Shift+Enter.

Pattern: G, write, P.

Stack: V for Latch, hold mutes, play.

Backslash, play, backslash, Save take, Play clip.

Playlist: set rows, Set chain, Play. Option+X to rewind both lists.

Save project when the jam is a jam.

---

## Not in this build

MIDI input / encoder learn, Ableton Link, clock-in from another device, overdub UI, recording the grid into a clip, a true mute-all row (EQ still passes audio).


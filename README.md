# MF-105M Virtual Controller — Operator Manual

This is a single-page Web MIDI editor and live performance controller for the
Moog MIDI MuRF (MF-105M). It runs entirely in the browser and talks to the
hardware over MIDI — there is no connection back the other way. The MuRF
does not report its own state, so everything this app shows you (which
pattern is selected, where the playhead is, whether the clock is synced) is
the app's own best guess based on what it has sent, not a live readout from
the box. Keep that in mind when something looks wrong on screen but sounds
right on the hardware, or vice versa.

The app transmits on MIDI channel 1 by default. Change it from the port
picker in the header.

**Hosting and runtime.** The app is hosted on GitHub Pages and is meant to
run either in the MIDIWeb browser app on iPad, or in any desktop browser
with Web MIDI support. After a new version is deployed, force-quit MIDIWeb
(or hard-refresh a desktop browser tab) before using it again — a plain
refresh is sometimes not enough to pick up the update, and you may see
Import or MIDI connection issues if you skip this step.

**Slots.** The MuRF has 24 pattern slots. Slots **1** and **13** are the
hardware's built-in EQ patterns — static filter curves with no pattern
animation. All the slots this app treats as "editable" for patterns are
**2–12 (labeled Bass)** and **14–24 (labeled Mids)**.

---

## What each page is for

| Page | What you do there |
|------|--------------------|
| Main | Program Change (pick a pattern by number), the Bass/Mids frequency switch, LFO on/off, Bypass, Staccato, Clock Sync arming, and the continuous controls: Drive, Output, Mix, Envelope, Envelope Scale, Rate, LFO Sweep, and all eight filter band levels. |
| Pattern | The 8-band × 64-step pattern grid editor. Each band has its own independent length. Sending a pattern always sends the whole thing — slot, LFO rate, and all eight rows — as one SysEx dump. |
| Keyboard | One live performance row at a time, in one of four modes: Triggered, Sustain, Mute, or Step. |
| Stack | Mute, Triggered, and Sustain rows all active simultaneously, plus Step. |
| Clips | Record and play back a take of your performance and transport actions. Loop, Once, or Once + Halt for that take. Tap tempo lives here too. |
| Playlist | Two independent lists that share the same app clock: a pattern chain and a clip list, so you can sequence a whole set of patterns and layer a performance clip on top. |
| Log | A filtered, readable record of every meaningful MIDI message the app has actually sent, for troubleshooting. |

---

## Header

The header is visible on every page.

**MIDI** — connection status, an activity LED, the output port picker, a
refresh button, and the channel selector. Changing the channel updates which
channel the app transmits on going forward, and also sends a one-time CC102
message (see the MIDI reference table below) so the hardware knows to expect
that channel — this CC102 message is always sent on channel 1 itself,
regardless of what channel you're switching to or from, because the MuRF
listens for CC102 on channel 1 specifically.

**Project** — Save Project downloads the current state of the whole app —
pattern editor contents, every panel control, the pattern library, saved
rows, saved clips, the digit map, clock/division settings, and the entire
Playlist — as one JSON file. Open Project loads one back in.

**A caveat worth knowing:** loading a project does re-transmit most of the
Main tab's panel controls live over MIDI if a port is currently selected —
despite the status message you'll see saying "nothing sent." It deliberately
holds back the filter levels, Rate/Division, and MIDI channel (those would
either stop or hijack the box), but everything else on the Main panel does
go out the moment you load a project with a port connected. If you don't
want that, disconnect the MIDI port first, load the project, then reconnect.

**Clock cluster:**

- **Clock** — arms MIDI clock listening and turns Pattern Clock Sync (CC89)
  on. This does not start anything moving by itself; it just tells the MuRF
  to expect a synced clock once one arrives.
- **Play** — sends Start (0xFA). Resets the app's own tick counter to the
  beginning.
- **Cont** — sends Continue (0xFB). Resumes from wherever the app's tick
  counter already was, rather than restarting from step one.
- **Stop Clock** — sends Stop (0xFC) and freezes the app's own clock. This
  does not punch out an in-progress recording by itself.
- **Pause all** — sends Stop, then Halt. See the safety section below
  before relying on this mid-performance.
- **Rec** — arms recording if nothing is currently recording, or punches out
  and finalizes the take if one is in progress.
- **Play clip** — plays the currently loaded recorded clip on top of
  whatever pattern is already active.
- **BPM** — 20 to 300, drives the app's internal tick timing.
- **Division** — sets how many MIDI clock ticks equal one MuRF step, sent
  as CC9. Sixteen selectable hardware divisions are exposed through the
  banks described later in this manual. Note that the Main tab's Rate
  slider also sends CC9 — they are two different controls sharing the same
  underlying message, so moving one can make the other's on-screen value
  stale without you having touched it directly.
- **Beat LED** — pulses once per quarter note whenever the app's clock is
  running, whether or not the clock is actually armed to transmit ticks to
  the hardware.

**Meters** (all the same width, for a quick at-a-glance read):

- **Sand** — progress through the currently recorded/playing clip (Play
  clip).
- **Blue** — progress through the Playlist's pattern list.
- **Mint** — progress through the Playlist's clip list.

**Reset lists** — rewinds both Playlist lists back to their first row,
clears their row highlights and meters, and stops the clock. It does not
re-dump any patterns to the hardware, and it does not turn off a chain
you've already set — you can press Play again afterward without needing to
run Set Chain a second time.

**Transport that hits the box directly:**

- **Pat Reset** — sends CC90 (Pattern Reset). If the MuRF is currently
  clock-synced, the hardware defers this to the next synchronized beat
  rather than applying it the instant it arrives; if the MuRF is unsynced,
  it applies immediately.
- **Halt** — sends CC20 at value 0. This is the button behind everything in
  the safety section below — read that before using it inside a live chain.
- **Step** — sends Note 108 (on, then off). Advances the pattern by one
  step, or freezes/unfreezes the pattern's animation depending on hardware
  mode.
- **Clk Reset** — sends Note 65 (on, then off). Restarts the pattern's
  internal step counter on the next tick, and releases any latched
  performance notes in the app.
- **Reset all** — the full teardown: stops the clock, stops clip playback,
  flushes a burst of note-off messages across the performance note range,
  zeroes the app's internal step counter, and resets every Playlist
  run-state field back to its start. If a chain is currently set, it also
  re-parks the MuRF on the chain's first row before halting — in that
  order, Program Change before Halt, deliberately, for reasons covered in
  the safety section.
- **Map** — put the app into mapping mode, then click any button and press
  a digit key 1 through 0 to bind that digit to that button going forward.
- **?** — hold this down to show on-screen captions naming every visible
  shortcut.

---

## Moving around without a mouse

| Key | What it does |
|-----|----------------|
| Backtick | Move to the next page (Main → Pattern → Keyboard → Stack → Clips → Playlist → Log → MIDI → back to Main) |
| Shift+Backtick | Move to the previous page in that same order |
| Tab | Move focus to the next control on the current page |
| Shift+Tab | Move focus to the previous control |
| Option+Tab | Jump focus to the next labeled section on the page |
| G | Drop keyboard focus out of whatever control you were in, so single-letter shortcuts work again. On the Pattern page, pressing G also moves focus straight into the grid. |
| Enter | Fires whichever button currently has focus, or commits a number box you were typing in. Enter never triggers Play by itself — Play needs Shift+Enter. |
| Escape or U | Collapses the current grid selection down to just the caret position |

If you're actively typing a name into a text field — saving to the pattern
library, a search box, saving a list — single-letter shortcuts are
suspended until you Tab out or click elsewhere, so your typing doesn't
accidentally trigger something.

---

## MIDI safety: what never to send while the MuRF is Halted

This section exists because of a lot of hard-won, real-hardware debugging,
and it matters more than almost anything else in this manual if you're
using the Playlist/Chain feature.

**The MuRF has no genuine "pause."** Halt is CC20 sent at value 0 —
literally the level control for Filter Band 1, repurposed to freeze the
pattern's playback. It is not a real transport state the hardware
recognizes the way Stop or Continue are. Practically, this means:

- Halt stops the MuRF's pattern engine from advancing, which is why it
  works as a pause in normal use.
- **Halt only ever clears three ways: a Rate change, a Rate CV signal on
  the back panel, or a MIDI Start/Continue message.** It does not clear on
  its own, and it does not clear on a Program Change or a SysEx dump.
- **Sending a SysEx dump or a Program Change to the MuRF while it is
  sitting in this Halted state has reliably broken MIDI communication
  outright**, in repeated, controlled testing — not just "the pattern
  doesn't change," but the device stops responding to further MIDI
  entirely, and getting it talking again requires a physical bypass on the
  hardware itself. This is not a timing-sensitive edge case; it's a
  consistent, repeatable failure whenever anything other than a transport
  message lands during Halt.

**The rule that follows from this: while the MuRF is Halted, never send
anything except Start, Continue, or Stop.** Anything else — a fresh
pattern dump, a Program Change to switch patterns, even routine CC
traffic — needs to wait until the halt has been cleared by one of those
three legitimate methods first.

**What the app does to protect you from this automatically:**

- **Set Chain** always sends a Continue message before it does anything
  else, every single time, whether or not the app believes the device is
  currently halted. This is deliberate: the app's own internal "is it
  halted" flag is just in-memory state — it doesn't survive a page reload,
  and there's no way to ask the actual hardware what state it's really in.
  Rather than trust a flag that can silently go stale, Set Chain just
  always clears a halt unconditionally before it dumps anything. Sending
  Continue when the device wasn't actually halted is harmless — it's just
  a normal resume — so there's no downside to doing it every time.
- **Reset Lists / Reset All**, when re-parking a chain on its first row,
  sends that Program Change *before* sending Halt, never after — so the
  re-park always lands on a device that's merely stopped, not halted.

**What you still need to watch for yourself:** anything outside those two
built-in guards. If you manually halt the MuRF (via the Halt button, Pause
All, or a Playlist list ending on "Once + Halt") and then, outside of Set
Chain or Reset Lists, send a Program Change from the Main tab or trigger
another dump — you're back in the dangerous sequence. If you need to
recall a pattern or check something on the hardware after halting it,
un-halt it first with Play or Continue, then do whatever you needed to do.

**A second, separate and less severe quirk:** independent of the Halt
danger above, the MuRF's own internal "am I locked to the clock" indicator
can occasionally drift out of sync with reality even during otherwise
correct playback — you may see its Rate LED read as unsynced (or need two
presses of Pause instead of one to actually stop the pattern) even though
the pattern itself never audibly drifted out of time. This has never been
tied to anything the app sends incorrectly — every message in these cases
has gone out at the exact right time with the exact right content — so
treat it as a known hardware quirk to work around (a second Pause press
usually resolves it) rather than a bug to chase in the software.

---

## Clock, in plain language

There are effectively two clocks at play here.

**The app's own clock** ticks 24 times per quarter note, at whatever BPM
you've set. Play starts it running; Stop Clock stops it. The Playlist's
lists and any recorded clips all count against this clock, whether or not
it's actually being transmitted to the hardware.

**The MuRF's clock** only moves once Clock Sync is armed and a Start or
Continue message has actually been sent — from that point on, the app
streams MIDI clock ticks (0xF8) to the hardware. The Division setting is
how many of those ticks equal one MuRF step.

Arming Clock Sync (CC89 on) does not start anything by itself — it only
tells the hardware to expect a synced clock stream once one shows up. Play
is what actually starts the stream. Clock Sync is off by default whenever
the app first opens.

| Control | Key | MIDI |
|---------|-----|------|
| Clock arm | Shift+C | CC89 |
| Play | Shift+Enter | Start (0xFA) |
| Continue | Cmd+Enter | Continue (0xFB) |
| Stop Clock | Shift+Space | Stop (0xFC) |
| Pause all | Option+Space | Halt, then Stop |
| Rec | Backslash | — |
| Play clip | Cmd+Shift+Enter | — |
| Reset lists | Option+X | Stop, then rewind both lists |
| Reset all | Option+Z | Halt, stop, and a note-off sweep |
| Pat Reset | Z | CC90 |
| Halt | Shift+X | CC20 = 0 |
| Step | X | Note 108 |
| Clk Reset | C | Note 65 |

### Division banks

Cmd+B cycles through three banks of ten divisions each: Straight → Dotted/
triplet → Custom → back to Straight.

Cmd+1 through Cmd+0 jump directly to a slot in whichever bank is currently
active — Cmd+1 is the slowest division in that bank, Cmd+0 is the fastest.

- **Straight** bank: 4 bars, 3 bars, 2 bars, whole note, half, quarter,
  eighth, sixteenth, thirty-second, sixty-fourth.
- **Dotted/triplet** bank: dotted whole through dotted sixty-fourth, then
  whole-note triplet, half-note triplet, and quarter-note triplet.
- **Custom** bank: starts out as a copy of the Straight bank. Turn Map
  mode on, dial in whatever division you want on the Division control, then
  press Cmd+1 through Cmd+0 to store that division into that slot. Custom
  bank contents are saved with your project.

Cmd+, and Cmd+. step the Division selector by one entry at a time without
opening its dropdown — hold either key down to run through several values
quickly. Plain comma and period (no modifier) are Pattern-page length
commands instead, not Division controls — see the Pattern section.

T, while on the Clips page, is Tap Tempo — it averages your taps into a
BPM value.

---

## Main

**Program / Voicing**

- The Pattern menu sends Program Change 0 through 23 (displayed to you as
  patterns 1 through 24).
- Frequency Bass/Mids toggle — key M — sends CC86.
- LFO On/Off toggle — key L — sends CC85.
- Bypass toggle — key B — sends CC87. This one has inverted logic: turning
  it on in the UI actually transmits value 0, and turning it off transmits
  127.
- Staccato toggle — Shift+S — sends CC68, also inverted the same way as
  Bypass.
- Pattern Clock Sync toggle — Shift+C — sends CC89 (this is the exact same
  control as the header's Clock-arm button).

**Main controls (0–127 continuous)**

Drive is CC2, Output is CC7, Mix is CC8, Envelope is CC1, Envelope Scale is
CC70, Rate is CC9 (shared with the Division control — see the note in the
Clock section above), and LFO Sweep is CC3.

You can either drag the slider or type a value directly into its number
box. With a slider focused, Left/Right arrow keys nudge it; with a number
box focused, Up/Down arrow keys do the same. Hold either down to run
through values continuously.

Tabbing out of a number box only actually sends a message if the value
changed — so just tabbing through the Filter Levels or Rate controls
without editing anything will not trigger a Halt or steal the clock by
accident. Similarly, tabbing away from the Envelope box without changing it
will not write a value into Envelope Scale.

**Filter levels**

F1 through F8 correspond to CC20 through CC27, one per filter band, each
labeled with its Bass-mode and Mids-mode center frequency. CC20 specifically
doubles as the payload the Halt button sends (a Halt is just CC20 forced to
0), which is why the Filter Levels panel itself is labeled as stopping the
pattern clock — moving any of these eight sliders, not just F1, has that
same clock-stopping side effect on the hardware.

---

## Pattern

Eight bands, up to sixty-four steps each, and every band tracks its own
independent length — Band 3 can be 12 steps long while Band 7 is 40. Every
time you send a pattern, the whole thing goes out together as one SysEx
message: the destination slot, the LFO rate, and all eight bands' data.

### Mouse and touch

Click a cell inside a band's current length to toggle it on or off. Drag to
paint across multiple cells at once. The small handle at the end of a row
grows or shrinks that band's length by dragging it. The Set buttons in the
toolbar change the length of whichever bands are currently selected. The
Fit buttons just change your zoomed-in view of the grid — they never touch
any band's actual data.

### Grid keys (press G first if focus was somewhere else)

| Key | Action |
|-----|--------|
| Up / Down | Move the caret to a different band |
| Shift+Up / Shift+Down | Add or remove a band from the current selection. The last band you added is the first one removed if you reverse direction |
| Left / Right | Move the caret to a different step |
| Shift+Left / Shift+Right | Grow or shrink the selected step range from wherever the selection anchor is |
| Option+Left / Option+Right | Shorten or lengthen the selected band(s) |
| Cmd+arrows | Slide the current selection — bands, steps, or both — without changing its size |
| O | Toggle every currently selected cell on or off. Writing past a band's current end automatically grows that band's length to fit |
| Comma | Shrink the selected bands so they all match the shortest one among them |
| Period | Grow the selected bands so they all match the longest one among them |
| [ / ] | Jump the caret to step 1 or step 64 |
| ; / ' | Jump the caret back or forward 8 steps at a time |
| - / = | Zoom the grid view out or in |
| Escape or U | Collapse the current selection down to just the caret |

### Whole-pattern vs. selection-scoped operations

These four commands are easy to mix up because their names are similar, so
it's worth being precise: **I** and **F** (no modifier) act on the *entire*
pattern regardless of what's selected — I inverts every step in every band,
and F reverses the order of all eight bands top to bottom. **Cmd+Shift+I**
and **Cmd+Shift+F** ("Invert sel" / "Flip sel") act only on your current
selection — Cmd+Shift+I inverts just the selected bands within the selected
step range, and Cmd+Shift+F mirrors just the selected bands against each
other (first selected swaps with last selected, and so on) within that same
step range.

### Pattern commands

| Key | Action |
|-----|--------|
| P | Send the current pattern as a SysEx dump |
| A | Toggle Auto Send (dumps automatically on every edit) |
| N | Toggle Seq Write (turns Auto Send off if it was on) |
| Space | While Seq Write is on: advance the caret one step |
| Q W E R T Y H J | While Seq Write is on: stamp bands 1 through 8 at the caret's current step |
| Cmd+Z / Cmd+Shift+Z | Undo / Redo |
| Cmd+X | Clear every step in every band (lengths are untouched) |
| Cmd+A | Select all bands, or clear the band selection if all are already selected |
| Cmd+I | Init Pattern — resets every band to length 1 with no steps active |
| I | Invert every step in every band (see note above) |
| F | Reverse the order of all eight bands (see note above) |
| Cmd+Shift+I | Invert only the selected bands within the selected step range |
| Cmd+Shift+F | Mirror only the selected bands within the selected step range |
| Cmd+C / Cmd+V | Copy / paste the selected band(s) |
| Cmd+Shift+C / Cmd+Shift+V | Copy / paste the entire pattern (all 8 bands plus LFO rate) |
| Cmd+S | Save the current pattern into the library |
| Cmd+O | Load the selected library pattern onto the grid (this does not send it unless Auto Send is on) |

### Toolbar groups

- **Edit** — Send, Auto Send, Seq Write, Undo, Redo, Clear All, Invert,
  Flip Channels.
- **Set** — Select all bands, clear the selection, and Set 8 / 16 / 24 /
  32 / 40 / 48 / 56 / 64 to resize whichever bands are currently selected.
- **Fit** — Fit 8 through 64, controlling how many columns are visible at
  once (view only, not data).
- **Zoom** — minus / plus, for fine pixel-level zoom control.
- **Band lengths** — eight number boxes, one per band. Tabbing from Band 1
  through Band 8 commits each one's value as you leave it, if it changed.
- **Clipboard** — Copy/Paste Pattern (the whole thing at once), Copy/Paste
  Band(s) (just the current band selection — this behaves as a whole-band
  copy if no steps are selected, or as a copyable step-range "phrase" if
  they are), and Init Pattern.
- **Row ops** — Clear steps, Init row, randomize steps/length/both, and
  save/load/delete a single band's data as a named row in your library.
- **Files** — Export the current pattern as a .syx file, export a selected
  library item the same way, or import one or more .syx files into your
  library (importing does not load anything onto the grid by itself).
- **Library** — search by name, browse the list sorted by slot, Load, Save
  Current, Delete, and Delete All (which asks for confirmation first).

If Import does nothing the first time you try it right after a cold
launch, force-quit MIDIWeb and reopen the page.

---

## Keyboard

One performance row at a time. Four modes: Triggered, Sustain, Mute, and
Step.

The Triggered, Sustain, and Mute keys all use the same computer-key row —
**A S D F G H J K** — for bands 1 through 8, and each key's cap shows both
the MIDI note name and the letter underneath it. Step mode has no letter
keys of its own; the header's X (Note 108) button is what triggers a Step
in that mode.

**One thing worth knowing about the note layout: the key-to-band mapping
runs in reverse of the note order.** The lowest note in each mode's range
is labeled Band 8, and the highest is labeled Band 1 — this matches how the
pattern's own SysEx dump orders its bands internally.

Left/Right arrow keys, with no other modifier held, cycle you through
Triggered → Sustain → Mute → Step in order.

| Control | Key |
|---------|-----|
| Latch | V |
| Last-note priority | N |
| Pattern clock reset | C, or the Note 65 button |
| Step | X |

**Triggered** notes are one-shots. With Last-note priority on, holding one
key and tapping a second creates a mono trill effect — releasing the
second returns to the first, sounding it again. Latch has no effect in
Triggered mode.

**Sustain** notes hold until you release the key, or indefinitely if Latch
is on.

**Mute** notes work against a mute map. With Latch on, mutes stay engaged
for as long as the clock keeps running.

Latch state is tracked separately per mode. If you latch a Mute note and
then switch to Sustain, that Mute key still shows as lit. Playing that same
band in Sustain or Triggered mode will release its Mute latch — this is a
deliberate "steal" behavior that follows the band, not the mode, and it can
happen even on the Keyboard page via the computer keyboard, though it's
most visible on the Stack page where all three rows are active at once. Note
also that on the Keyboard page specifically, Latch only actually works
through the computer keyboard — tapping notes with the mouse or a touch
screen on this page never latches them, regardless of whether the Latch
toggle is on; the Stack page's mouse/touch handling does support latching.

Finger/touch input on the on-screen keys is always active, regardless of
which input method you're otherwise using.

---

## Stack

All three performance rows active simultaneously.

| Row | Computer keys | MIDI notes |
|-----|---------------|------------|
| Mute | 1 2 3 4 5 6 7 8 | C1 to C2 white keys |
| Triggered | Q W E R T Y U I | C3 to C4 white keys |
| Sustain | A S D F G H J K | C5 to C6 white keys |

The same Latch, Last-note priority, Note 65, and Step controls from the
Keyboard page apply here too.

A single band cannot be in two modes at once — playing Sustain on band 8
while it has a latched Mute note releases that Mute latch, the same "steal"
behavior described on the Keyboard page, just easier to trigger here since
all three rows are live together.

Most computer keyboards can only report about six simultaneous key presses
at once, which limits how many notes you can play together by hand across
all three rows combined. Use Latch, or play with actual fingers on a touch
surface, if you need to hold more than that.

Stack's Mute row lights follow the same latch state as the Keyboard page's
Mute mode — they're the same underlying latches, just shown in two places.

Whenever a clip plays back a Mute, Trigger, or Sustain note — whether from
Play Clip or from the Playlist's clip list — the corresponding keys light
up on both the Keyboard and Stack pages for the duration of that note.

---

## Clips

A clip is a recorded take of your performance and transport actions. It is
not tied to any particular pattern, so the same clip can play over
different patterns at different times.

**What gets recorded:** Mute/Trigger/Sustain notes, the Note 65 clock
reset, the Note 108 step, Pattern Reset (CC90), five specific toggle
switches (Staccato, LFO, Frequency, Bypass, and Clock Sync), BPM changes,
Division changes, and Stop events.

**What does not get recorded:** the Main tab's continuous sliders (Drive,
Output, Mix, Envelope, Envelope Scale, Rate), the eight Filter Level
sliders, any SysEx pattern dump, Program Changes, or the pattern grid
itself.

**Workflow**

1. Backslash arms recording.
2. Shift+Enter starts both the clock and the take together.
3. Perform.
4. Backslash punches out and finalizes the clip.

Pressing Shift+Space mid-take writes a Stop event into the clip, but the
take itself keeps recording until you actually punch out with Backslash.

Save/Load/Delete for takes live on the Clips page. While you're on that
page specifically, Cmd+S saves the current take, Cmd+O loads a saved one,
and Cmd+X deletes whichever clip is currently selected.

The header's Play Clip button layers the loaded clip on top of whatever's
currently on the grid. Loop, Once, and Once + Halt for that clip's own
playback loop point live on the Clips page itself, not in the header.

A take is capped at roughly ten minutes or ten thousand recorded events,
whichever comes first, and is stored both in your browser's local storage
when you press Save, and inside any project file you save afterward.

T is Tap Tempo — tap it a few times in rhythm and it averages the interval
into a BPM value.

---

## Playlist

Two independent lists, sharing the same app clock, but not required to be
the same length as each other.

**Pattern list** — controls which pattern the MuRF's own sequencer is
actually playing.
**Clip list** — controls which performance clip fires on top of whatever
the pattern list is doing.

Each list has its own row count, and only that many rows are drawn on
screen. Each list also has its own independent Loop / Once / Once + Halt
setting for what happens when it reaches its end.

### Pattern row types

Every visible row in the pattern list is one of four types — there is no
such thing as a truly empty row:

- **Pattern** — you pick a saved pattern from your library. In Chain mode,
  Set Chain dumps it to a slot that's locked to that row's position (row 1
  always dumps to slot 2, row 2 to slot 3, and so on, skipping slots 1 and
  13). While the list is actually running, entering this row only ever
  sends a Program Change — no SysEx goes out during playback.
- **Rest** — despite how this might sound, a Rest row is **not** a Halt.
  Set Chain dumps a genuinely silent, blank pattern into that row's slot
  (using the same locked slot numbering as a Pattern row), and while the
  list runs, entering a Rest row sends nothing more than the same plain
  Program Change any other row would send — it just happens to be pointing
  at a slot with no active steps in it, which is what makes it silent.
- **EQ Bass** — sends a Program Change to slot 1 (the hardware's built-in
  Bass EQ pattern). No dump is ever sent for this row type, and nothing
  about it halts the clock — audio keeps passing through the EQ curve
  until something else (like a clip) takes over the bands.
- **EQ Mids** — the same as EQ Bass, but targets slot 13 (the Mids EQ)
  instead.

**A correction worth being explicit about, since it's easy to assume
otherwise:** whether the *next* row you enter also gets a Start message (in
addition to its Program Change) is not actually tied to Rest rows at all —
it's tied to whether a **clip** most recently played a Trigger or Sustain
note. If a clip plays one of those note types, the very next *Pattern* row
the list enters (not EQ rows — only Pattern rows) will also get a fresh
Start alongside its Program Change, because those note types are known to
stop the MuRF's own clock as a hardware side effect. This can happen
regardless of what row type you were sitting on when the clip note fired.

### Clip rows

A clip row is allowed to be genuinely empty. An empty clip row still
occupies however many bars you've set for it — that's how you line a clip
up with, say, pattern row 3: give it two empty clip rows first, then the
real clip on the third row.

The clip list doesn't need to span the whole pattern list either. A single
four-bar clip sitting under a twelve-bar pattern list will play its four
bars and then simply go quiet for the rest of the list, unless you've set
the clip list to Loop.

### Dump vs. Chain

- **Dump** — you choose the destination slot yourself, per Pattern row.
  Send This Row or Send All Rows writes the corresponding SysEx
  immediately. This mode is useful for just parking a set of patterns onto
  slots without actually running them as a list.
- **Chain** — slot numbers are locked to row order instead of being
  user-chosen. **Set Chain** unconditionally clears any Halt state first
  (see the safety section above), stops the clock if it was running,
  re-asserts the currently selected Division, dumps every Pattern and Rest
  row once (spaced out, about a second apart), then finishes by sending a
  plain Program Change parking the MuRF on the first row's slot — see
  "Combo commands" below for the exact sequence and why each step is
  there. From that point, Play only ever sends Program Changes during
  playback — no further SysEx goes out while the list is actually
  running.

You'll need to run Set Chain again any time you change the row count, a
row's type, or which pattern is assigned to a row — those changes don't
take effect on the hardware until you re-dump.

### How to run a list

1. Set how many pattern rows and how many clip rows you want.
2. Fill them in.
3. Turn Chain on, then press Set Chain.
4. Press Play.

Play starts both lists together, as long as each one actually has content
to play — a clip list with no clips named in it simply won't start.

**Reset Lists** (available both on this page and in the header, or via
Option+X) rewinds both lists back to row 1, zeroes both meters, and stops
the clock. It does not re-dump anything, and Set Chain stays active, so you
can just press Play again without re-running Set Chain. If a chain is
currently set, it also re-parks the MuRF on the chain's first row with a
Program Change before halting, so the next Play/Continue already starts
on the right pattern — see "Combo commands" below for the exact sequence.

### Loop / Once / Once + Halt

Each list has its own independent ending behavior:

- **Loop** — that list wraps back around to row 1 and keeps going. The
  highlight follows it back to the start.
- **Once** — that list simply stops where it is. The *other* list is
  unaffected and can keep running on its own.
- **Once + Halt** — this one is not scoped to just its own list: reaching
  the end here stops *both* lists, sends a real Halt, and stops the clock
  entirely. Use this when you actually want the whole performance to end,
  not just one of the two lists.

### Highlights and meters

The pattern-row highlight tracks the pattern list; the clip-row highlight
tracks the clip list independently — they are allowed to be sitting on
different row numbers at the same time. The header's Blue meter tracks the
pattern list's overall progress, and the Mint meter tracks the clip list's.

---

## Log

The Log page exists so you can see exactly what the app actually sent,
without needing an external MIDI monitor. It deliberately shows only the
messages that matter for figuring out what happened: Program Change,
Control Change (with a friendly label for the ones you're most likely to
care about — Division, the Halt-related filter levels, Clock Sync, Pattern
Reset), SysEx dumps (shown as which slot they targeted rather than raw
hex), and Start/Stop/Continue.

Clock ticks and note on/off messages are left out entirely, and not just
filtered out afterward — they're sent through a separate, more
time-sensitive path in the app that never reaches this log in the first
place, precisely so this page stays short enough to actually read and copy
during troubleshooting, instead of being buried in hundreds of clock
messages per second.

**Copy** copies every visible entry, timestamp and all, to your clipboard
as plain text — handy for pasting a session's worth of activity somewhere
else for review. **Clear** empties the log.

---

## Map and hints

Turn Map on, click any button, then press a digit key 1 through 0 to bind
that digit to that button from then on. The built-in factory shortcuts
described throughout this manual stay active alongside your custom digit
bindings — Stack's 1–8 keys remain the Mute row's notes regardless, they
are not available as Map targets.

Hold ? (Shift+/) at any time to see on-screen captions for every visible
shortcut on the current page.

---

## MIDI reference

| Kind | ID | What it does |
|------|----|----|
| SysEx | `F0 04 0A 05 … F7` | A full pattern dump — destination slot, LFO rate, and all eight bands' step data together |
| Program Change | 0–23 | Selects pattern 1–24 |
| CC1 | — | Envelope |
| CC2 | — | Drive |
| CC3 | — | LFO Sweep |
| CC7 | — | Output |
| CC8 | — | Mix |
| CC9 | — | Rate / Division (the same message, sent from two different controls — see the Clock section) |
| CC20–27 | — | Filter Levels 1–8. CC20 doubles as the Halt payload (sent at value 0) |
| CC68 | — | Staccato (inverted: on = 0, off = 127) |
| CC70 | — | Envelope Scale |
| CC85 | — | LFO on/off |
| CC86 | — | Frequency Bass/Mids |
| CC87 | — | Bypass (inverted: on = 0, off = 127) |
| CC89 | — | Clock Sync arm |
| CC90 | — | Pattern Reset (deferred to the next beat if the MuRF is currently clock-synced, instant otherwise) |
| CC102 | — | Sets the device's MIDI channel; always transmitted on channel 1 itself |
| Notes 24–36 | — | Mute bands (note order runs opposite to band number — see the Keyboard section) |
| Notes 48–60 | — | Triggered bands |
| Notes 72–84 | — | Sustain bands |
| Note 65 | — | Pattern/clock reset |
| Note 108 | — | Step |
| 0xFA / 0xFB / 0xFC | — | Start / Continue / Stop |
| 0xF8 | — | Clock tick, 24 per quarter note |

---

## Combo commands: exactly what each button sends

Several buttons in the app bundle more than one MIDI message together,
in a specific order, for reasons that usually trace back to the safety
rule above. This table spells out exactly what each one sends, in order,
using the message names from the reference table above.

| Button | Exact sequence | Why this order |
|---|---|---|
| **Halt** | CC20 = 0 | Just the one message — freezes the pattern engine. |
| **Pause all** | Stop → Halt | Stop first so the Halt lands on a device that's already stopped rather than mid-cycle. |
| **Play** | (Clock Sync CC89, only if not already armed) → Start → clock ticks begin | Resets the app's own tick counter to zero before starting. |
| **Cont** | (Clock Sync CC89, only if not already armed) → Continue → clock ticks resume | Resumes from wherever the app's tick counter already was; also one of the only three messages that clear a Halt (see the safety section). |
| **Stop Clock** | Stop | Also stops the app's own tick-sending loop — no more clock bytes go out until Play or Continue. |
| **Play clip** | Same as Play, then fires the clip's recorded events starting at tick 0 | |
| **Pat Reset** | CC90 = 127 | Deferred to the next beat if clock-synced, instant otherwise. |
| **Step** | Note 108 on, then off | |
| **Clk Reset** (Keyboard/Stack tabs) | Note 65 on, then off ~50ms later | |
| **Global Clock Reset** (header) | Note 65 on, then off ~50ms later, plus releases any latched performance notes in the app | Same underlying message as Clk Reset above; this one also clears latch state since it lives outside the Keyboard/Stack context where latch is normally managed. |
| **Rec** | No MIDI by itself | Arms or finalizes recording of whatever CCs/notes/transport get sent through other controls while it's running. |
| **Reset all** / **Reset Lists** (Reset Lists = Reset all, plus clearing the Playlist's row highlights) | Stop → a spaced sweep of Note-off across notes 36–108 (2ms apart, ~146ms total) → **if a chain is currently set**: after that sweep finishes, Program Change parking the chain's first row, then Halt — **if no chain is set**: Halt fires immediately, no Program Change | Stop first so the device isn't mid-cycle; the note-off sweep (not a CC/panic message) clears any stuck performance notes without risking a CC hitting a Halted device; when a chain is set, the Program Change re-points the MuRF at row 1 so the next Play/Continue starts the chain from the beginning without re-running Set Chain — and it must land *before* Halt, never after, exactly per the safety rule above. |
| **Set Chain** | Continue (unconditional) → Stop (only if the clock was left running) → CC9 (currently selected Division) → one SysEx dump per Pattern/Rest row, ~1 second apart → Program Change parking the first row's slot | Continue guarantees the device can't still be halted before any dump goes out, regardless of what the app's own tracked state claims. Stop exists to keep the MuRF's own pattern-cycling engine from actively advancing through slots while their contents are being overwritten by SysEx — it is *not* a Halt-safety step (a dump has never broken MIDI whether the clock was running or stopped, only Halt does that); this is purely about not rewriting a slot's memory while the device might be actively reading it. CC9 re-sends the app's Division because that message is otherwise only ever sent when the Division control is touched directly — without this, a chain synced for the first time in a session would start on the MuRF's power-on default division instead of what the app displays. The closing Program Change parks the MuRF on row 1 so Play can start the chain immediately afterward. |
| **Load Project** | Re-sends most of the Main tab's panel CCs live if a port is connected (skips Division/CC9, Filter Levels/CC20–27, and channel) | See the header section above for the full caveat. |

---

## Fast path

Pick your port. Shift+C to arm Clock Sync. Cmd+. down to a sixteenth note.
Set BPM to 120. Press G to release focus. Shift+Enter to play.

On the Pattern page: G to focus the grid, write your pattern, P to send it.

On Stack: V for Latch, hold your mutes, then play.

For a quick take: Backslash to arm, play, Backslash again to punch out,
Cmd+S to save it, then Play Clip to hear it back.

For a Playlist run: set your row counts, fill them in, Set Chain, then
Play. Option+X rewinds both lists without needing to re-dump.

Save your project whenever the take is worth keeping.

---

## Not in this build

Ableton Link, receiving clock from another device (the MIDI tab has
placeholder Sync checkboxes for this, not yet wired up), an overdub-style
recording UI, recording the pattern grid directly into a clip, and a true
mute-all row (the EQ row types still pass audio through). Several of
these are on the roadmap — see `ROADMAP.md` in this repository for what's
currently planned.

The MIDI tab itself — Map External, port roles, note passthrough, and
mapping presets — isn't written up in this manual yet; ask for that
section if you want it added.

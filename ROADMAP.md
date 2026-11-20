# Roadmap

Running list of planned work, kept here so it survives across sessions.
Not in strict priority order except where noted.

## Near-term (this week)

- **Keyboard shortcuts for the Playlist/Chain tab.** Still none for the
  actual Chain actions (Set Chain, Dump, row navigation) - the only
  shortcuts on this tab are the generic Cmd+S/Cmd+O/Cmd+X reused for
  saving/loading/deleting a named list preset.
- **Regression test: clips + chain together.** Confirm a clip chain can still
  play correctly on top of / alongside a running pattern chain - hasn't been
  tested since the Chain rework this session.
- **Minor UI/UX polish.** No specific list yet - nothing about the MIDI
  messages themselves, just interface tweaks to call out as they come up.
- **Clock division randomizer ("sample & hold").** Generate randomized
  clock division (CC9) changes on some interval, simulating sending a
  random voltage to the Rate CV input on the MuRF's rear panel.

## Bigger features

- ~~**Note Sequencer tab.**~~ Done. New **Sequencer** tab: an 8-band ×
  up-to-64-step grid, independent of the Pattern tab, for Trigger, Sustain,
  Mute, and the shared Step pulse, plus CC automation lanes (folding in
  the "MIDI CC sequencing" item below - same feature). Content is stored
  by absolute MIDI tick rather than by step index, so changing the
  Division/zoom setting never retroactively moves anything already
  placed. Option+click focuses a Sustain/Mute block for non-destructive
  Option+Left/Right resizing. A Rec button arms/punches the same clip
  recorder the rest of the app uses, so a Sequencer run can be captured
  and chained like any other clip. Runs on the same shared clock as
  Playlist; a fix after this landed closes the one real conflict found so
  far - the Sequencer now also stops on any global Stop (it used to keep
  ticking silently in the background) and holds off sending during an
  in-flight SysEx dump, so it can't land a note/CC in the middle of a
  Set Chain dump and wedge the MuRF's MIDI receiver the way the original
  Halt-state failure mode (see the README's safety section) did.
  Not yet saved with the project - see the README's Sequencer section.
  Deferred from the original design: the "chain row can't have empty
  trigger/sustain/step data while a pattern is actively playing" Playlist
  constraint was never built, since the Sequencer ended up as its own
  independent tab rather than feeding the Playlist's row types directly.
- ~~**External MIDI controller mapping.**~~ Done. "Map External" (header)
  learns any connected MIDI controller's CC/notes into app sliders and
  buttons, independent of the existing digit-key Map mode and active on
  every tab. New MIDI tab lists Input/Output ports with Ableton-style
  Track/Sync/Remote checkboxes: **Track (input)** is the only thing needed
  to map anything - it also passes Mute/Trigger/Sustain notes straight
  through to Keyboard/Stack automatically, no per-note learn step, using
  the app's fixed note ranges (so a QuNexus-style preset with the right
  octaves just works). **Remote (input)** is unused/reserved - mapping does
  not depend on it. **Remote (output)** is the feedback opt-in, independent
  of Track/mapping - only controllers that want LED/ring/button-color
  feedback need it checked. Sync (both directions) is reserved for later
  clock-follow/multi-destination work. Mappings are keyed by port *name*
  (not the browser's port id) so they survive reloads, replugs, and
  exporting to another machine. A live mappings table shows per-mapping
  clear, and for continuous controls an absolute/relative toggle with step
  size. Mappings + port roles are included in Save/Open project, and can
  also be saved/loaded/exported/imported as standalone named presets in the
  MIDI tab, independent of any project.
  Possible later add-on: a preset/"control surface script" model for
  controllers with a fixed, known layout, so a Twister doesn't need to be
  re-learned from scratch on a new machine (partly covered now by the
  named-preset export/import above, but a script model would recognize a
  controller automatically instead of requiring a manual Load).
- **Gamepad mapping.** Map a game controller (Xbox Elite Series 2 named
  specifically) to app parameters via the browser's Gamepad API - buttons,
  sticks, and triggers are all readable; the paddles are the one part in
  question, since Xbox paddle remapping typically happens at the OS/
  driver level (duplicating an existing face button) rather than exposing
  a distinct input to the browser - confirm this when it's actually
  attempted, don't assume either way.
  - Stretch: navigating the Pattern editor (or more of the app) via
    gamepad, not just parameter mapping.
- **Clock output to multiple destinations.** Send MIDI clock to more than
  one place at once.
- **External Clock Sync (follow an external MIDI clock).** Built, headless
  dry-run only so far - NOT yet verified against real hardware, and
  deliberately not merged to main until it is. Check Sync on an input port
  (MIDI tab) to make the app follow that port's MIDI Clock/Start/Continue/
  Stop instead of running its own free-running timer: `onExternalClockMessage`
  re-anchors `state.clock.origin/tick/bpm` from each incoming 0xF8 so every
  existing tick-consumer (Sequencer, Playlist/Chain) works unchanged
  regardless of where ticks come from; `clockPlay()` gained a `skipArm`
  option so an external Start/Continue never arms MIDI Clock Sync (CC89) on
  the MuRF itself - that stays under the user's own control, since the
  motivating setup (MuRF clocked via its Tap/Step input from a modular/CV
  rig, VC only following tempo for its own Sequencer/Playlist timing) needs
  CC89 to stay off. Includes a stall watchdog (flags it if ticks stop
  arriving while still "running") and a visible EXT badge + disabled BPM
  box while active. System Realtime bytes carry no channel nibble, so
  there's no such thing as channel-filtering the clock itself - isolation
  from a port carrying other traffic comes from listening only for
  Clock/Start/Continue/Stop byte values, and from which Push output port
  has Track left off, not from a channel number.
  Also added: a "Relay → MuRF" toggle (visible once Sync is armed) for the
  other topology - the MuRF only has one MIDI input, so if it needs to stay
  genuinely MIDI-Clock-synced (Division/CC9 only work as a clock divider
  while it is) rather than clocked via Tap/Step, the same VC-to-MuRF cable
  has to carry the relayed clock too, there's no splitting it in parallel.
  Turning the toggle on arms CC89 and forwards each incoming external tick
  onward via `sendClockAt()` (which now also checks `sysexBusyUntil`,
  closing the same gap `seqOnTick()` got in the SysEx-interleaving fix, so
  a relayed tick can't land mid-dump either); turning it off disarms CC89
  symmetrically rather than leaving the MuRF believing a clock is still
  coming. Double-hop latency (Push → VC → MuRF) is expected to be a few
  constant, non-jittery ms - real-hardware confirmation still pending.

## Longer-term / ongoing

- Bug fixes as discovered.
- Additional keyboard commands as requests come up.

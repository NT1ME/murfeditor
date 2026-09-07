# Roadmap

Running list of planned work, kept here so it survives across sessions.
Not in strict priority order except where noted.

## Near-term (this week)

- **Keyboard shortcuts for the Playlist/Chain tab.** None mapped yet.
- **Regression test: clips + chain together.** Confirm a clip chain can still
  play correctly on top of / alongside a running pattern chain - hasn't been
  tested since the Chain rework this session.
- **Minor UI/UX polish.** No specific list yet - nothing about the MIDI
  messages themselves, just interface tweaks to call out as they come up.
- **Clock division randomizer ("sample & hold").** Generate randomized
  clock division (CC9) changes on some interval, simulating sending a
  random voltage to the Rate CV input on the MuRF's rear panel.

## Bigger features

- **Note Sequencer tab.** Per-band, per-step sequencing of MIDI note
  performance data (mirroring the Pattern tab's grid layout), up to 64
  steps to match the longest pattern length. Each step chooses between:
  Trigger, Sustain, Mute, or (pattern) Step. Mute is optional per step -
  not needed for a pure trigger/sustain sequence, but available for
  sequencing mutes on top of or between patterns instead of playing them
  by hand. Recording the sequencer's playback produces a clip, which can
  then be chained like any other clip.
  - Constraint to design around: a chain row can't have empty
    trigger/sustain/step data while a pattern is actively playing -
    during pattern playback, only Mute and CC clips can run concurrently.
- **MIDI CC sequencing.** Alongside note data, make the CC parameters on
  the main page sequenceable the same way (per-step values), not just
  playable live.
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

## Longer-term / ongoing

- Bug fixes as discovered.
- Additional keyboard commands as requests come up.


# MIDI MuRF Editor — Operator Manual (2.9 start)

Single-page companion for the MF-105M. Pattern SysEx, CC/PC, performance notes, MIDI clock, clips, projects.

After a deploy: force-quit MIDIWeb or hard-refresh Chrome.

Channel 1 by default. Pick the port.

---

## Pages

Main · Pattern · Keyboard · Stack · Clock

` ` next page · Shift+` previous.

**G** — release Tab focus on every page (Enter becomes Play). On Pattern, G also focuses the grid.

---

## Clock cluster (global header)

Order: **Arm · Play · Stop · Cont · Rec · Play clip · BPM · Division**

Arm also turns **Clock Sync (CC89)** on. Without that the box ignores F8. Arm does **not** start ticks. Play does.

| Key | Action |
|-----|--------|
| Enter | Play (FA) if nothing in the cluster is Tab-armed. If a cluster button is armed, Enter fires **that** button. If BPM is focused, Enter commits BPM only |
| Shift+Enter | Continue (FB) |
| Shift+Space | Device Stop (FC). Does not punch out a take |
| \ | Rec arm / punch out |
| Z | Pattern Reset CC90 |
| C | Clock Reset note 65 |
| X | Note 108 on Keyboard / Stack only |

Tab walks the cluster on every page. Option+Tab jumps sections.

**Division** focused: arrows walk the manual table, send CC9, list stays closed. Click opens the menu.

**BPM** focused: arrows ±1 or type. 20–300 integer.

Clock is **off** when the app opens. No F8 until Arm + Play.

---

## Two timebases

**Playhead** (clips): 24 PPQN × BPM. Can run in memory while the box is stopped.

**Box:** F8 only when Armed and Play/Continue. Division = Rate as divider.

| Byte | Box |
|------|-----|
| FA Start | Step 1, run |
| FB Continue | Same step, run |
| FC Stop | Freeze |
| Note 65 | Restart on next tick |
| CC90 | Step 1 now |
| Note 108 | Step / freeze animation (play pages) |

---

## Clips

Records: notes (Mute / Trigger / Sustain), 65, 108, CC90, FA / FB / FC if you press them.

Does not record: sliders, SysEx, division changes.

`\` arm → Enter starts clock + take → perform → `\` punch out. Shift+Space during a take **is** a Stop in the clip; the take keeps rolling.

Save / Load / Delete / Clip BPM on the Clock tab. Play clip overlays the current grid. Clips are not married to a pattern.

Cap: 10 min or 10k events. localStorage + Save take.

---

## Projects (Clock tab)

Save project downloads JSON. Open project restores:

- Current grid, slot, LFO  
- Main sliders + switches  
- Pattern library + row library  
- Clips + loaded clip name  
- Digit Map  
- BPM + division  

Load leaves Clock / Play / Rec **off** and does not send filter CCs or Rate (those stop or restart the hardware clock). P and Play when you mean it.

---

## Pattern

Slots **2–12 Bass**, **14–24 Mids**.

| Key | Action |
|-----|--------|
| ↑ ↓ / Shift+↑ ↓ | Band / add-peel |
| ← → / Shift+← → | Caret / range |
| Option+← → | Length |
| Cmd+arrows | Slide |
| O | Write steps |
| , . | Snap shortest / longest |
| [ ] | Step 1 / 64 |
| ; ' | −8 / +8 |
| − = | Zoom |
| N | Seq write |
| Space | Next step (Seq on) |
| Q W E R T Y H J | Stamp bands 1–8 (Seq on) |
| ⌘A | All bands toggle |
| ⌘I | Init Pattern |
| ⌘X | Clear steps |
| I F U | Invert / Flip / collapse |
| ⌘C ⌘V | Bands |
| ⌘⇧C ⌘⇧V | Pattern |
| ⌘S ⌘O | Save / Load library |
| P / A | Send / Auto Send |

Seq on forces Auto Send off. Stamp grows length only on bands you hit.

Row tools, Fit/Set 8…64, Import to library (not onto the grid). Load library does not blast CC20–27.

---

## Keyboard / Stack

Keyboard: **A–K** = current mode.  
Stack: **1–8** Mute · **Q–I** Trigger · **A–K** Sustain.

**V** Latch (Mute / Sustain). Trigger ignores Latch.  
Last-note: Triggered trills. Sustain + Mute stay poly.  
Many computer keyboards cap at six keys down. Latch or fingers for eight.

Same band cannot be two modes at once.

---

## Map / hints

Map: Map button → a button → digit 1–0. Factory keys stay. Stack `1–8` are Mute notes, not aliases.

Hold **?** (Shift+/) for captions, including transport and mapped digits.

---

## Fast path

Port → Arm Clock (Sync on) → 16th → 120 → G → Enter.  
Pattern work → P.  
Stack / Latch.  
`\` Rec → play → `\` → Save take → Play clip.  
Save project when the jam is a jam.

---

## Not built

Link, clock-in, playlist SysEx chaining, overdub UI, recording the grid into a clip, PPQN switch.

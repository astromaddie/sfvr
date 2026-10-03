# Steam Frame VR conventions (v0.1)

`sfvr` implements a small set of shared conventions for native, standalone VR ports on the Valve Steam Frame. The aim is that a player moves between games without relearning recentring, menus, comfort options or settings. The section numbers are referenced from the headers.

The conventions draw on:

- Valve's Steam Frame input and compatibility documentation;
- Meta and Microsoft XR design guidance;
- established flat-to-VR ports (Team Beef's Quest ports, HaloCEVR, UEVR, DFUVR).

Game-specific control maps may add actions. They do not move the system layer (section 1), and they follow the roles in section 2 unless the game's own on-screen prompts say otherwise. Physical buttons always match what the game shows.

## 0. Platform floor (Valve)

- At least 72 fps at a 1728² eye resolution.
- The default Frame bindings reach all content, and menus are fully navigable by controller.
- Button glyphs use Frame (or Xbox) names.
- The System button belongs to the runtime and is never bound.
- On focus loss the game keeps submitting frames, ignores input, and pauses.

## 1. System layer

| Input | Action |
| --- | --- |
| Right Menu | The game's Start/pause |
| Left View, short press | The game's Select or Back; in menus, back |
| Left View, hold 1 s | Recentre (yaw and horizontal position), with a short buzz on both hands |
| Left View, hold 3 s | Recentre and recalibrate standing height, with a second buzz |
| Pause menu, "VR settings" | Opens the VR settings. Where the game's pause menu cannot be extended (for example a ROM-driven simulation), holding Menu + View together for 0.5 s opens them instead |
| VR settings, "Quit to Steam" | Confirm, then a clean `xrRequestExitSession` and exit |

How recentring behaves:

- The head's yaw is transferred into the game's heading, and its horizontal position into the play-space origin.
- World-locked panels are re-anchored.
- It takes effect at a stereo frame boundary.
- A runtime recentre (`XrEventDataReferenceSpaceChangePending`) is handled the same way.

Stick clicks and multi-button chords never do system work.

`sfvr_view` implements the View button timing.

## 2. Gameplay roles

| Role | Input |
| --- | --- |
| Move or steer | Left stick |
| Turn | Right stick X: snap or smooth (section 5), or game-defined for vehicles and rail games |
| Primary action | Dominant-hand trigger |
| Secondary action | Off-hand trigger |
| Grab, hold or gesture modifier | Grips |
| Tertiary actions | Bumpers |
| Jump or confirm | A |
| Cancel or back | B |
| Context actions | X, Y |
| Quick-select | Left D-pad |
| Game toggles | Stick clicks |

Handedness:

- A single `dominant_hand` setting swaps everything hand-bound: aim, triggers, grips, weapon model and menu pointer.
- It never moves the physical buttons. The Frame controllers are asymmetric, so the system layer stays where it is.

Fallbacks:

- Index and Touch fallback profiles keep every action reachable.
- The Frame profile is suggested only when `XR_VALVE_frame_controller_interaction` is advertised.

## 3. Menus and pointing

- Menus sit on a world-locked panel about 2 m away and about 50° wide. It is anchored on yaw only, and placed on open and on recentre.
- Pointer:
  - A ray from the dominant hand's aim pose.
  - The trigger clicks, with hysteresis.
  - A confirms; B and a View short press go back; the right stick scrolls.
- Pad navigation always works too.
- Targets are at least 2.5–3°, and body text is at least 0.6° tall.

## 4. HUD

- Diegetic placement (cockpit instruments, weapon, wrist) is preferred where the game allows.
- Otherwise the HUD is drawn in its own pass with derived alpha, and composited as a quad that follows head yaw and pitch but never roll.
- `hud_mode` offers three placements:

| Mode | Placement |
| --- | --- |
| `near` | About 1.5–2 m, about 40° wide |
| `far` | About 15 m, drawn from the head camera so markers sit over their targets |
| `leash` | Body-locked, re-yawing past a 20–30° dead zone |

Transitions between flat, stereo and cinema presentation fade through black.

## 5. Comfort

| Key | Default | Range |
| --- | --- | --- |
| `turn_mode` | `snap` | `snap`, `smooth`, `off` |
| `snap_angle` | 30° | 15, 30, 45, 60, 90 |
| `smooth_turn_speed` | 120°/s | 30–360 |
| `vignette`, `vignette_strength` | off, 0.6 | Artificial motion only |
| `roomscale`, `lean_limit` | off, 0.35 m | 0.15–0.5 m |
| `horizon_lock`, `follow_vehicle_rotation` | off | Vehicles and cockpits |
| `head_translation` | 1.0 | 0–2 |

How the comfort modules work:

- `sfvr_turn` implements snap turning (arm at 70% deflection, re-arm under 30%) and smooth turning (20% dead zone, dt capped at 0.1 s).
- `sfvr_room` implements roomscale walking:
  - The player's collision body follows the head's horizontal steps through the game's own collision move.
  - A step longer than 0.5 m is taken up without moving.
  - The remaining lean is clamped.

## 6. Haptics

All pulses are scaled by a `haptics` strength (0–1, default 0.6). Overlapping pulses on a hand are merged into the strongest one per frame.

| Event | Amplitude / duration |
| --- | --- |
| `ui_hover` | 0.2 / 10 ms |
| `ui_click` | 0.5 / 30 ms |
| `system` | 0.6 / 80 ms, both hands |
| `gesture` | about 0.5 / 45 ms |
| `weapon_fire` | Per weapon, by weight |
| `impact` | 0.35–1.0 / 30–80 ms |
| `rumble` | Authored dual-band rumble mapped to max(low, high), in 40 ms pulses |

`sfvr_haptics` implements the table and the per-frame coalescing queue.

## 7. Settings

- Keys use the snake_case names above. They are stored in a versioned settings file in the persistent data directory.
- Loading the file follows four rules:
  - missing keys take their defaults;
  - out-of-range values are clamped;
  - unknown keys are kept;
  - writes are atomic.
- Environment overrides are named `<PORT>_VR_<KEY>`.
- The in-headset settings page uses a common tab order: Comfort, Controls, HUD & menus, Display & performance, Accessibility & audio, Room, Quit to Steam.

`sfvr_settings` holds the key registry and the value helpers. File I/O stays with each game.

## 8. Diagnostics and telemetry

| Key | Meaning |
| --- | --- |
| `force_render` | Render stereo while the headset is in standby, with a synthetic head |
| `diag_*` | Synthetic head and hand poses for unattended tests |
| `dump_frame` | Write eye and HUD images for frame N |
| `timing`, `timing_gpu` | Stage and GPU timing |

Every port logs one line in the same format every 10 s:

```
[vr-perf] fps=71.9 missed=3 cpu=7.31ms logic=1.20 render=5.02 eye=2.21/2.19ms gpu=8.40ms
```

`sfvr_perf` produces it.

## 9. Display

- The refresh rate is requested with `XR_FB_display_refresh_rate`. The request is the highest offered rate at or below the target, which defaults to 90 Hz.
- After two consecutive 10 s windows below 90% of the target while focused, the game falls back to 72 Hz.
- Rendering is at the runtime-recommended size, times `resolution_scale`.

## 10. Code shape

- The XR host sits behind an injectable function table, so the lifecycle and bindings are testable without a runtime.
- `sfvr` is pure C99, with no allocation and no platform dependencies. It is copied into each game's source tree and distributed under that project's licence.

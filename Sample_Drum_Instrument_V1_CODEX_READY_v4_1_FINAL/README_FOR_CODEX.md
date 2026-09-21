# Sample Drum Instrument V1 — Codex handoff

This folder is the complete V1 handoff. Start here, then read `SPEC.md` and `UI_LAYOUT.md` before coding.

## Source-of-truth order
1. `SPEC.md` — product behavior and V1 scope.
2. `UI_LAYOUT.md` — screen geometry and control placement contract.
3. The three JPEG files in `assets/backgrounds/` — approved production backgrounds.

If anything is ambiguous, preserve the behavior in `SPEC.md` and do not invent additional features.

## Approved backgrounds
- `assets/backgrounds/MAIN_background.jpg`
- `assets/backgrounds/USER_background.jpg`
- `assets/backgrounds/SETTINGS_background.jpg`

All three are 1536×1024 and must be used without stretching or changing their internal geometry. They are three pages of the same plugin.

### Important bitmap/UI rule
The JPEGs are the approved visual chassis. Do not redesign, regenerate, crop, stretch or rearrange them.

Functional UI must be programmatic: tab labels/states, category/sample text, DICE, +, waveform and markers, knobs, switches, values, USER folder/sample rows, HISTORY/stars, SETTINGS labels/buttons/paths/lists/status/color controls, and all interaction/state feedback.

Some decorative branding is intentionally baked into the MAIN bitmap (for example SAMPLE DRUM INSTRUMENT, SOUND SHAPES IDEAS, v1.0 and PROFESSIONAL DRUM SAMPLER). Keep it as decoration. The three blank top tab slots are reserved for programmatic `MAIN | USER | SETTINGS` labels; illuminate/state them programmatically without changing the background artwork.

## Implementation priority
Build the smallest stable V1 that satisfies `SPEC.md`. Prioritize VST3/FL Studio stability, real-time audio safety, large-library indexing, correct MIDI/sample playback, DICE no-repeat behavior, USER copy workflow, project/instance persistence, and responsive GUI. Architecture and implementation details are Codex's responsibility.

## Deliverable expectation
Create the project, implement V1, compile VST3, run automated tests where possible, test persistence and failure cases, and report exact build/test results plus any remaining issues. Do not claim FL Studio validation unless it was actually performed in FL Studio.

## Final HISTORY UI clarification
HISTORY is per plugin instance/project, never global. Show the same synchronized history data in two places: a compact bottom view on MAIN and the full HISTORY view in USER. Do not show HISTORY in SETTINGS and do not create a second independent history store.

## V4 top-rail clarification — mandatory
Use a fixed programmatic silver/metal top control rail on all three pages. It exists to normalize the top control geometry across the approved backgrounds/skins. Put `MAIN | USER | SETTINGS` on it on every page; on MAIN also mount Category, Sample, DICE and `+` there without overlap.

Do **not** cover the decorative raised hardware along the very top of the Walkman/cassette artwork. Those physical-looking buttons/switches/protrusions are part of the visual identity and stay visible and non-interactive. The rail covers only the flat front-header area required for the interactive controls. It is not skin-dependent and does not move when backgrounds change.

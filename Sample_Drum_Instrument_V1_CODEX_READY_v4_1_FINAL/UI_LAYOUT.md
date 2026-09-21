# UI Layout Contract — V1

Reference canvas for all three approved backgrounds: **1536 × 1024**. Preserve aspect ratio and internal geometry. Do not stretch one page differently from another.

## Shared top navigation
Each page has three blank tab slots in the top metal header. Overlay programmatic labels in this order:
`MAIN | USER | SETTINGS`
The active page receives the programmatic active/accent state. Do not bake the labels into replacement images.

## MAIN — `assets/backgrounds/MAIN_background.jpg`
This is the approved MAIN chassis and must not be redesigned.

Use the existing visual zones:
- Top three blank slots: MAIN / USER / SETTINGS navigation.
- Large central gridded display: real waveform for the entire source sample, plus START, END, selected-region and LOOP POSITION overlays.
- Narrow horizontal panel directly below waveform: current category/sample status and compact actions such as DICE / + as appropriate to the established hierarchy.
- Tall left panel: VELOCITY ON/OFF + AMOUNT/RANGE and related compact MAIN controls.
- Tall right panel: LOCK, ROOT NOTE, LOOP ON/OFF and ANCHOR LEFT/RIGHT.
- Large lower center panel and its narrow header strip: START/LEFT, END/RIGHT, MOVE/POSITION and LOOP POSITION controls, arranged clearly without adding a normal Volume control.
- Bottom-most available strip/panel area: a **compact HISTORY view** for the current instance, with recent sample names and ★ state. It must not cover the waveform or primary region controls.
- Lower left/right subpanels may hold compact supporting controls/status needed by `SPEC.md`; do not create new features.

The MAIN HISTORY view and USER HISTORY view are synchronized presentations of the same per-instance/project history data. MAIN is compact; USER is the full browser. SETTINGS has no HISTORY.

The waveform itself is programmatic and visual-only in V1; dedicated controls change the region.

## USER — `assets/backgrounds/USER_background.jpg`
This is the approved USER chassis: **two upper panels + one full-width lower panel**.

Use:
- Top blank slots: shared navigation.
- Upper-left panel: USER folder tree and nested subfolders. `HISTORY` is the final item at the bottom of this tree.
- Upper-right panel: samples in the selected USER folder, with programmatic rows/actions.
- Full-width lower panel: HISTORY contents when HISTORY is selected, including programmatic star toggles and sample loading/comparison behavior.

Folder names, sample names, rows, icons, scrollbars, context actions, stars and selection states are all programmatic. USER supports arbitrary folder depth. HISTORY is not a separate global tab. The compact HISTORY shown on MAIN is the same data, not a second history system.

## SETTINGS — `assets/backgrounds/SETTINGS_background.jpg`
This is the approved SETTINGS chassis: **one tall left column + four right-side panels in a 2×2 grid**. Do not add a middle strip or alter this geometry.

Use:
- Top blank slots: shared navigation.
- Tall left column: compact programmatic navigation for the V1 settings sections only.
- Four right panels: arrange the required V1 settings clearly:
  1. Sample Library — main path, choose folder, Refresh/Update, Rebuild, status/info.
  2. USER Library — USER root path and choose/change path.
  3. Categories — defaults + custom categories; add/rename/delete according to `SPEC.md`.
  4. Appearance — Skins import/select/delete and Control Color picker.

Do not invent General/Audio/MIDI/Performance pages. All labels, paths, buttons, lists, status/progress and color controls are programmatic.

## Skin contract
A skin is one background image only. It does not contain control assets or layout metadata. Controls stay in fixed logical positions across skins. Imported skins are copied into plugin-managed storage; the default skin is protected from deletion.

## V4 fixed top control rail — mandatory
The three approved background JPEGs remain exact, immutable artwork. Their decorative top hardware/protrusions and Walkman/cassette character must remain fully visible.

To eliminate geometry differences in the painted header areas between skins/pages, implement **one programmatic fixed silver/metal control rail** as a foreground UI layer. This rail is part of the plugin UI, not part of any skin bitmap, and therefore does not change when a skin/background changes.

Mandatory rules:
- The rail may cover only the flat front-header/control area needed to provide a consistent mounting surface for interactive top controls.
- **Do not cover, crop, mask or redraw the decorative physical hardware above the front header** (the raised top buttons/switches/handle-like Walkman details). Those are intentionally non-interactive visual elements and must remain visible.
- The rail must not alter the 1536×1024 background bitmap or stretch/crop it.
- Place the programmatic `MAIN | USER | SETTINGS` navigation on this fixed rail in consistent coordinates on every page.
- On MAIN, place the category selector, current sample selector, DICE and `+` on the same fixed rail, using the available horizontal space and preventing overlap with navigation.
- On USER and SETTINGS, keep the same rail dimensions and navigation coordinates. Unused portions of the rail remain visually present rather than allowing the underlying skin geometry to determine control placement.
- The rail is always the same silver/metal appearance across MAIN, USER, SETTINGS and imported skins. Skin changes affect the background behind/below it, not this rail.
- All controls on the rail remain programmatic and clickable. Decorative hardware visible above it remains non-clickable.
- The rail must visually read as an intentional part of the Walkman chassis: thin/compact, metallic, with restrained screw/rivet details if useful. It must not become a large slab that hides the characteristic top silhouette.

This V4 rule supersedes any earlier implication that the existing painted blank top slots alone determine the geometry of interactive top controls. They are visual reference only; the fixed programmatic rail is the authoritative mounting surface.

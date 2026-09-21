# VST3 Sample / Drum Instrument — V1
## Final functional specification for Codex

**Status:** Final V1 specification. Implement the behavior below; do not invent competing product rules.

## 1. Product
A VST3 sample/drum instrument optimized for FL Studio. Main workflow:
1. Choose category.
2. DICE selects a sample from the indexed library.
3. Play it from MIDI/Piano Roll.
4. Adjust START, END, MOVE, LOOP, LOOP POSITION, ANCHOR, ROOT NOTE and VELOCITY.
5. Mark good results with ★ in HISTORY.
6. Save chosen samples to USER with `+`.

Main tabs: **MAIN | USER | SETTINGS**.

Each plugin instance can use a different category. The current category should be clearly identifiable in the instance/channel context where the host permits it.

## 2. Categories
Default categories always exist and cannot be deleted:
- KICK
- SNARE
- CLOSED HI-HAT
- OPEN HI-HAT
- RIM
- PERC
- 808 / BASS

User can add, rename and delete custom categories. Do not impose an artificial small limit.

A file is eligible for a category when the category name occurs in the audio filename. Matching is case-insensitive and should ignore ordinary separators such as spaces, `_`, `-`, commas and similar punctuation. Example category `KICK` matches `kick.wav`, `Kick_01.wav`, `MY-KICK-808.wav`, `My Kick, Heavy.wav`.

Do not infer synonyms unless explicitly configured. A file may belong to multiple categories; e.g. `Kick_808_01.wav` may be both KICK and 808/BASS.

If a category has no matching/available files, DICE does nothing.

## 3. Audio formats
V1 supports:
- WAV
- AIFF
- FLAC
- MP3

Support mono and stereo and varying sample rates/bit depths. Original library files must never be modified.

## 4. Main sample library
User selects a root library folder. Recursively scan all subfolders.

The library may be very large (e.g. 50–200 GB, hundreds of thousands of files). Do **not** rescan the entire disk on every DICE click.

Use a persistent index/cache. First run performs a full scan. Later startup should reuse the index and detect changes. Provide:
- **REFRESH / UPDATE LIBRARY** — detect added/changed/deleted files.
- **REBUILD LIBRARY** — full rebuild.

Indexing must not unnecessarily block audio/UI. Exact architecture, cache format, database and threading are implementation decisions for Codex.

Unavailable/corrupt/unsupported files must be skipped safely. Never crash the plugin because a file disappeared or cannot be opened.

## 5. DICE
DICE samples only from the current category.

Within the currently available pool, selection is random. Avoid selecting a sample already present in the current instance's HISTORY while unused samples remain. Once the entire available pool has been used, the pool may reset and allow repeats.

DICE does not change MIDI. It changes the loaded sample only. Existing playback must not be forcibly interrupted just because DICE was pressed; subsequent Note Ons use the new sample.

## 6. HISTORY
History belongs to the specific plugin instance/project. It is saved with the project and restored when the project is reopened.

HISTORY is the same per-instance/project data view shown in **both MAIN and USER**. It is never a separate top-level tab and it is not shown in SETTINGS.

- **MAIN:** show a compact HISTORY area along the bottom of the MAIN view so recent samples and ★ states remain visible while working.
- **USER:** `HISTORY` remains the final item at the bottom of the USER tree, and the lower USER panel shows the full history list when selected.
- Both views are two presentations of the **same HISTORY data** for the current plugin instance. Loading or starring an item in either view must immediately be reflected in the other.

History stores references/metadata sufficient to reload previously used samples; it does not need to duplicate audio files.

Clicking a history item loads that sample immediately. Loading an existing history item must not create a duplicate history entry.

If the referenced file is gone, mark it unavailable and fail safely.

## 7. History stars / favorites
Every history item has a ★ toggle.

Users can mark any number of samples. Stars:
- do not copy audio,
- do not move samples to USER,
- do not affect DICE,
- are simply favorites inside HISTORY.

Users should be able to move between starred samples and compare them. Do **not** create a separate Favorites tab/list.

## 8. USER library
USER is a persistent user-owned sample library, separate from the main library. User chooses the USER root path.

USER supports arbitrary folder depth. Example:
```
USER/
  KICK/
    Kick 01/
      Heavy Kick.wav
      Punch Kick.wav
    Kick 02/
      808 Kick.wav
```

Also allow a simple structure such as `USER/KICK/Kick A.wav`. Do not force a particular subfolder depth.

User can:
- create folders/subfolders,
- rename folders,
- delete folders,
- move samples,
- rename samples,
- delete samples,
- click a sample to load it.

Deleting from USER must never delete the original main-library file.

## 9. `+` save-to-USER
`+` on MAIN copies the currently loaded sample physically into the selected USER location.

User can choose destination folder/category and rename the copied sample. If a name already exists, resolve the conflict safely, e.g. numeric suffixes `(1)`, `(2)`, etc.

The copy is audio only. Do not save the current START/END/LOOP/ROOT/VELOCITY state into the audio file.

## 10. MAIN UI
MAIN contains:
- current category,
- current sample name,
- DICE,
- `+`,
- waveform,
- START / LEFT,
- END / RIGHT,
- MOVE / POSITION,
- LOOP POSITION,
- LOOP ON/OFF,
- ANCHOR LEFT/RIGHT,
- LOCK,
- ROOT NOTE,
- VELOCITY ON/OFF + AMOUNT/RANGE,
- button to open USER,
- compact HISTORY strip/area at the bottom, showing the current instance's recent history and ★ states.

The MAIN history area is not a separate history system or tab; it is a compact view of the same per-instance HISTORY used by USER.

Waveform shows the **entire source sample**, even when the selected region is short. START/END/LOOP POSITION are visualized, but V1 controls are adjusted by the dedicated controls rather than direct mouse waveform editing.

## 11. Region controls
**START / LEFT:** beginning of playback region.

**END / RIGHT:** end of playback region.

**MOVE / POSITION:** moves the whole selected region without changing its length.

If a new sample is shorter than locked/current values, clamp safely to the actual sample length. Never access audio outside valid bounds.

## 12. MIDI playback
MIDI Note On starts the sample at START.

The MIDI note duration determines how long the note is held. If Note Off occurs before END, playback stops at Note Off. If the selected sample region ends before the MIDI note ends and LOOP is off, playback ends at END.

Overlapping MIDI notes are allowed and may play simultaneously. Do not artificially choke one note merely because another Note On occurs.

No time-stretching in V1.

## 13. LOOP
Controls:
- LOOP ON/OFF
- LOOP POSITION

LOOP OFF:
`START -> END -> stop`

LOOP ON:
`START -> END -> LOOP POSITION -> END -> LOOP POSITION -> ...`

The initial pass starts at START. After END, playback jumps to LOOP POSITION and repeats the `LOOP POSITION -> END` region until MIDI Note Off.

A short automatic crossfade at the loop boundary may be used to reduce clicks. No separate crossfade knob in V1.

Looping must continue to work after MIDI pitch changes.

## 14. ANCHOR
Two modes:
- ANCHOR LEFT
- ANCHOR RIGHT

LEFT: START is the time reference and is aligned with MIDI note start.

RIGHT: END is the time reference and is aligned with MIDI note end. Useful for reverse hats, risers and effects that must finish with the MIDI note.

Anchor does not mean time-stretching and does not itself change pitch.

## 15. ROOT NOTE / MIDI pitch
ROOT NOTE is manually selected by the user.

It defines which MIDI note represents the sample's current/base pitch. The plugin does **not** need to detect the real pitch/key of the audio automatically.

Example ROOT NOTE = G:
- MIDI G = base pitch
- MIDI G# = +1 semitone
- MIDI F# = -1 semitone
- MIDI A = +3 semitones

This is standard sampler-style pitch behavior. Do not automatically tune the sample to project key/scale.

The separate future pitch-correction/key-matching plugin is outside V1.

## 16. VELOCITY
Do not add a conventional channel-volume knob. Channel volume belongs in the FL Studio Mixer.

Plugin has:
- VELOCITY ON/OFF
- VELOCITY AMOUNT/RANGE

When ON, MIDI velocity controls sample playback level according to the configured range/amount.

When OFF, the plugin must not impose additional global velocity shaping; individual Piano Roll velocity values remain usable normally.

Velocity is per plugin instance, not per sample. This lets a user turn it OFF for hi-hats and draw individual hat velocities in Piano Roll.

## 17. LOCK
LOCK ON means:
> Only the sample changes; the current settings remain locked.

Changing the sample preserves as far as physically possible:
- START
- END
- MOVE
- LOOP ON/OFF
- LOOP POSITION
- ANCHOR
- ROOT NOTE
- VELOCITY ON/OFF
- VELOCITY AMOUNT/RANGE

If the new sample is too short, values must be safely clamped to valid audio bounds. LOCK must never cause invalid memory/audio access.

LOCK OFF permits normal adjustment/adaptation to the new sample.

No additional lock modes.

## 18. Persistence
Global settings:
- main library path,
- library index,
- USER library path,
- custom categories,
- imported skins,
- control color.

Per plugin instance/project:
- current category,
- current sample reference,
- START, END, MOVE,
- LOOP and LOOP POSITION,
- ANCHOR,
- LOCK,
- ROOT NOTE,
- VELOCITY settings,
- HISTORY,
- HISTORY stars.
HISTORY and HISTORY stars are strictly per plugin instance/project. They MUST NOT be stored as global settings.

Saving/reopening the project restores these states.

## 19. Settings
SETTINGS contains:

### SAMPLE LIBRARY
- choose main library path,
- show current path,
- REFRESH / UPDATE,
- REBUILD LIBRARY,
- basic library/index status.

### USER LIBRARY
- choose USER path,
- show current path.

### CATEGORIES
- list categories,
- add custom category,
- rename custom category,
- delete custom category.

Default categories cannot be deleted.

### SKINS
- import/add image,
- select skin,
- delete imported skin.

### CONTROL COLOR
Simple horizontal spectrum/color picker with palette/selection area. Selected color applies to controls/indicators.

## 20. Skins
Skin is a background image. It must not change control positions or layout.

Imported skins are copied into the plugin's own skin folder. Deleting an imported skin deletes only the plugin's copy, never the user's original source image.

One default skin is always available and protected from deletion.

## 21. Failure handling
Never crash because of:
- missing sample,
- removed drive,
- corrupted audio file,
- unsupported file,
- empty category,
- missing USER folder,
- missing history item,
- sample shorter than region parameters.

Use safe fallback behavior and clear UI status where appropriate.

## 22. V1 scope exclusions
Do not implement unless required for stability:
- automatic key detection,
- automatic project-key tuning,
- advanced pitch correction,
- time stretching,
- intelligent beat matching,
- automatic BPM/harmony/energy analysis,
- separate Favorites UI,
- separate/independent History system or top-level History tab,
- unnecessary advanced technical settings.

A separate future plugin may handle automatic pitch correction/key matching.

## 23. Implementation/test requirement
Build as a real VST3 instrument suitable for FL Studio.

Codex should:
1. create/organize the project,
2. implement the V1 behavior,
3. compile the plugin,
4. run automated/unit tests where possible,
5. validate state persistence and edge cases,
6. review for crashes, invalid audio access, race conditions and UI/audio-thread violations,
7. report exact build/test results and remaining issues.

The implementation architecture is Codex's responsibility. Prioritize real-time audio safety, stable file indexing, non-blocking library scans, deterministic state serialization, and correct MIDI/sample playback.

## 24. Acceptance checklist
Before calling V1 complete, verify at minimum:
- VST3 loads in FL Studio.
- MIDI triggers samples correctly.
- Pitch follows ROOT NOTE and MIDI semitone offsets.
- START/END/MOVE work.
- LOOP and LOOP POSITION work until MIDI Note Off.
- ANCHOR LEFT/RIGHT work as specified.
- VELOCITY ON/OFF works and does not destroy individual Piano Roll velocities.
- LOCK preserves parameters while changing samples.
- DICE uses the selected category and avoids history repeats until the pool is exhausted.
- HISTORY persists with the project and remains per plugin instance.
- The same HISTORY is visible on MAIN (compact bottom view) and USER (full view); changes/load/star state stay synchronized.
- HISTORY stars persist and starred samples can be compared/loaded.
- USER folders/subfolders work.
- `+` physically copies samples to USER without modifying originals.
- Category matching is case-insensitive and separator-tolerant.
- WAV/AIFF/FLAC/MP3 load safely.
- Large libraries use an index rather than a full scan per DICE click.
- Missing/corrupt files do not crash the plugin.
- Settings persist.
- Skin import/delete works safely.
- Control color works.
- No invalid memory/audio access occurs when a sample is shorter than region settings.

## 25. Final product rule
When an ambiguity is encountered, preserve the core workflow:
**category -> DICE -> sample -> MIDI playback -> adjust region/pitch/velocity/loop -> star favorites in USER/HISTORY -> save chosen sample to USER.**

Do not add complexity merely because it is technically possible.

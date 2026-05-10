# Audio fixtures

All clips are mono 16 kHz `pcm_s16le` WAV — the format every adapter
in the workspace can decode without conversion. The set spans short
(< 1 minute) through long (~24 minutes) so consumers can pick a
duration that fits their test runtime budget.

| File | Duration | Source |
| --- | ---: | --- |
| `01_dialogue.wav` | 3:47 | `dia/01_dialogue/clip_16k.wav` |
| `02_pyannote_sample.wav` | 0:30 | `dia/02_pyannote_sample/clip_16k.wav` (upstream pyannote sample) |
| `03_dual_speaker.wav` | 1:00 | `dia/03_dual_speaker/clip_16k.wav` |
| `04_three_speaker.wav` | 0:40 | `dia/04_three_speaker/clip_16k.wav` |
| `05_four_speaker.wav` | 1:00 | `dia/05_four_speaker/clip_16k.wav` |
| `06_long_recording.wav` | 16:18 | `dia/06_long_recording/clip_16k.wav` |
| `07_yuhewei_dongbei_english.wav` | 0:25 | `dia/07_yuhewei_dongbei_english/clip_16k.wav` |
| `08_luyu_jinjing_freedom.wav` | 23:37 | `dia/08_luyu_jinjing_freedom/clip_16k.wav` |
| `09_mrbeast_dollar_date.wav` | 17:22 | `dia/09_mrbeast_dollar_date/clip_16k.wav` |
| `10_mrbeast_clean_water.wav` | 10:19 | `dia/10_mrbeast_clean_water/clip_16k.wav` |
| `11_mrbeast_age_race.wav` | 23:30 | `dia/11_mrbeast_age_race/clip_16k.wav` |
| `12_mrbeast_schools.wav` | 16:04 | `dia/12_mrbeast_schools/clip_16k.wav` |
| `13_mrbeast_saved_animals.wav` | 17:35 | `dia/13_mrbeast_saved_animals/clip_16k.wav` |
| `14_mrbeast_strongman_robot.wav` | 18:23 | `dia/14_mrbeast_strongman_robot/clip_16k.wav` |

## Format invariants

Every clip in this directory carries:

- one stream;
- `pcm_s16le` codec;
- `16000 Hz` sample rate;
- `1` channel (mono);
- `16` bits per sample.

Tests can hard-code those values rather than re-probing each fixture.
`manifest.json` carries the same data in machine-readable form
(per-clip `samples` count and exact `duration` to nanosecond precision)
for tests that pick fixtures programmatically.

## Picking a fixture

| If your test wants… | Pick |
| --- | --- |
| A multi-second smoke test, fast | `02_pyannote_sample.wav` (30 s) |
| Multiple speakers, short | `03_dual_speaker.wav`, `04_three_speaker.wav`, `05_four_speaker.wav` |
| A non-English clip | `07_yuhewei_dongbei_english.wav` (Chinese / English code-switch), `08_luyu_jinjing_freedom.wav` (Mandarin) |
| Long-running stress | any of the `mrbeast_*` clips or `06_long_recording.wav` |

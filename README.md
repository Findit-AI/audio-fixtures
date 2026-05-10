<div align="center">
<h1>audio-fixtures</h1>
</div>

Shared audio fixtures for findit-studio's media stack
([`mediadecode`](https://github.com/findit-ai/mediadecode),
[`dia`](https://github.com/findit-ai/dia), …).

The clips are real spoken recordings — speech in multiple languages,
mixed speaker counts, durations from 25 seconds to ~24 minutes — so
consumers exercise their decode / diarize paths against representative
content rather than synthetic tones.

Audio gets its own repo (separate from a future `video-fixtures`) so
audio-only consumers don't pull video bytes they'll never use, and
vice versa. All clips are checked in directly (no Git LFS); the repo
size budget assumes the set grows slowly.

## Layout

Fixtures are grouped by codec — one directory per codec — so a
consumer that only cares about, say, AAC can pull just `aac/` without
faulting in the rest. The codec name follows FFmpeg's
`AVCodecID`-string convention (e.g. `pcm_s16le`, `aac`, `opus`,
`flac`). `manifest.json` at the repo root lists every codec
directory with per-file metadata so tests can pick fixtures
programmatically.

```
pcm_s16le/         mono 16 kHz pcm_s16le WAV clips (existing dia
                   diarization corpus, 25 s – ~24 min each)
manifest.json      machine-readable index, grouped by codec
```

Future codec directories (`opus/`, `aac/`, `flac/`, …) will land
beside `pcm_s16le/` with the same per-codec README format and a new
entry in the manifest.

## `pcm_s16le/`

Mono 16 kHz `pcm_s16le` WAV — the format every workspace adapter
decodes natively (FFmpeg's `pcm_s16le` decoder; WebCodecs'
`"pcm-s16"` codec string).

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

Format invariants for this directory:

- one stream;
- `pcm_s16le` codec;
- `16000 Hz` sample rate;
- `1` channel (mono);
- `16` bits per sample.

Tests can hard-code those values rather than re-probe each fixture.

## Picking a fixture

| If your test wants… | Pick |
| --- | --- |
| A multi-second smoke test, fast | `pcm_s16le/02_pyannote_sample.wav` (30 s) |
| Multiple speakers, short | `pcm_s16le/03_dual_speaker.wav`, `04_three_speaker.wav`, `05_four_speaker.wav` |
| A non-English clip | `pcm_s16le/07_yuhewei_dongbei_english.wav` (Chinese / English code-switch), `08_luyu_jinjing_freedom.wav` (Mandarin) |
| Long-running stress | any of the `mrbeast_*` clips or `06_long_recording.wav` |

## Consuming from another repo

Recommended pattern is a **git submodule** pinned at a specific SHA —
gives bisectable, reproducible test runs and works the same on every
CI runner without extra tooling.

```sh
# one-time, in the consumer repo
git submodule add https://github.com/findit-ai/audio-fixtures \
  tests/fixtures/audio
git commit -m "test: pin audio-fixtures submodule"
```

CI checkouts then need `submodules: recursive` (GitHub Actions) or
`git submodule update --init --depth=1` (other runners). Tests look
up files by codec directory:

```rust
let path = std::path::Path::new(env!("CARGO_MANIFEST_DIR"))
  .join("tests/fixtures/audio/pcm_s16le/02_pyannote_sample.wav");
```

A flat `git clone` works too if you don't need exact-revision pinning —
point an `AUDIO_FIXTURES_DIR` env var at the local path and have tests
fall back to that.

## Adding fixtures

1. If the file is a new codec, create a `<codec>/` directory at the
   repo root using FFmpeg's `AVCodecID` string (`opus`, `aac`,
   `flac`, …). Otherwise drop it into the existing matching directory.
2. Use a sortable, human-readable name (`NN_short_topic.<ext>`).
3. Add a row to that codec's table in this README and an entry in
   `manifest.json` (under the matching `codecs[].files[]` array).
4. PR. CI is intentionally minimal — there's no Rust crate here, just
   binaries — but please attribute the source.

## License

Fixtures are licensed Apache-2.0 / MIT (your choice) at the repo
level. Individual recordings retain their upstream license; see the
table above for per-file attribution.

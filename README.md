<div align="center">
<h1>media-fixtures</h1>
</div>

Shared audio / video fixtures for findit-studio's media stack
([`mediadecode`](https://github.com/findit-ai/mediadecode),
[`colconv`](https://github.com/findit-ai/colconv),
[`dia`](https://github.com/findit-ai/dia), …).

The clips are real recordings — speech in multiple languages, mixed
speaker counts, varying durations — so consumers can exercise their
decode / convert / diarize paths against representative content rather
than synthetic tones. All fixtures are checked in directly (no Git LFS
required); the repo size budget assumes the set grows slowly.

## Layout

```
audio/
  *.wav          mono 16 kHz pcm_s16le clips (5–24 minutes each)
  README.md      per-file table + format invariants
  manifest.json  machine-readable index — sample rate, channels,
                 duration, codec — used by consumer test code
                 that wants to assert format without re-probing.
```

`video/` will land alongside as soon as a downstream crate needs one;
keep the same shape (per-format dir, README, manifest.json).

## Consuming from another repo

The recommended pattern is a **git submodule** pinned at a specific
SHA — gives bisectable, reproducible test runs and works the same on
every CI runner without extra tooling.

```sh
# one-time, in the consumer repo
git submodule add https://github.com/findit-ai/media-fixtures \
  tests/fixtures
git commit -m "test: pin media-fixtures submodule"
```

CI checkouts then need `submodules: recursive` (GitHub Actions) or
`git submodule update --init --depth=1` (other runners). Tests look
up files by the same relative path as if the fixtures lived in-tree:

```rust
let path = std::path::Path::new(env!("CARGO_MANIFEST_DIR"))
  .join("tests/fixtures/audio/02_pyannote_sample.wav");
```

A flat `git clone` works too if you don't need exact-revision pinning —
point a `MEDIA_FIXTURES_DIR` env var at the local path and have tests
fall back to that. The submodule path is what the tests in `mediadecode`
ship against.

## Adding fixtures

1. Drop the file under the matching category (`audio/`, `video/`, …)
   with a sortable, human-readable name (the existing audio clips use
   `NN_short_topic.wav`).
2. Add a row to that category's `README.md` table and an entry to
   `manifest.json` so consumers can pick clips by metadata without
   re-probing.
3. PR. CI is intentionally minimal — there's no Rust crate here, just
   binaries — but please attribute the source in the README row.

## License

Fixtures are licensed Apache-2.0 / MIT (your choice) at the repo
level. Individual recordings retain their upstream license; see
`audio/README.md` for per-file attribution.

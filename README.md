# Lyric Remote local translation models

Unofficial quantizations derived from [IndexTeam/Index-Translate-9B](https://huggingface.co/IndexTeam/Index-Translate-9B), declared Apache-2.0 upstream. This repository contains model distribution metadata and license notices, not the app source or user data.

## Index 9B slim · 2026-10-30

[Download the release](https://github.com/coymdd/lyric-remote-models/releases/tag/index9-slim-20261030). The 3,080,171,360-byte GGUF is stored as two raw binary parts because each GitHub release asset must be below 2 GiB. Lyric Remote's download-enabled build downloads both, verifies their sizes and SHA-256, and joins them automatically. Do not import either part as a GGUF.

Manual reconstruction on macOS/Linux:

```sh
cat Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf.part1 Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf.part2 > Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf
shasum -a 256 Index-Translate-9B.IQ2_S-slim-lower2-compact.gguf
```

Expected combined SHA-256: `3aa6dc5f98dabc1b831ce40b3f62b982e3c71d643ca32c9405fc0af738f31ea2`. See model-manifest.json for exact component sizes and hashes.

## Changes and evaluation

Importance-aware quantization from pinned original F16. Two input/output tensors are IQ2_S; 440 other tensors are byte-identical to the prior compact model. All 442 tensor names and shapes, including unused MTP, are retained. 4K context, F16 cache/state, 64/64 batch settings. This is not an official IndexTeam release.

On Apple M5 Pro 24GB macOS 27.0.1, native Lyric Remote translation-engine CLI maximum resident memory across four runs was 3,326,918,656 bytes, compared with the prior compact model's maximum 3,790,635,008 bytes. UI overhead is excluded. No iPad or iPhone measurement of this new model was made. No hard RAM ceiling is guaranteed.

Frozen synthetic Japanese-to-Korean challenge corpus of 64 lines: one Codex reviewer judged 19 major meaning errors for actual app Index2 defaults, 7 for the previous 9B compact model, and 6 for this model. This is not human gold, an independent review, or population accuracy evidence. New meaning errors remain. The new model took 40.12 seconds versus Index2's 14.85 seconds for that corpus. Each of 7 synthetic stories was generated three times in one process (240 lines); all completed and outputs per story matched.

See LICENSE, NOTICE, and UPSTREAM_README.md for upstream attribution and license information.

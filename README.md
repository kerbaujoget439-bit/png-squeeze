# PNG Squeeze — Lossless Screenshot Compressor

[![make.fun bounty](https://make.fun/badge/png-squeeze-01-run-20261127.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127)
[![make.fun rank](https://make.fun/badge/png-squeeze-01-run-20261127/kerbaujoget439-bit/png-squeeze/rank.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127)
[![make.fun reward](https://make.fun/badge/png-squeeze-01-run-20261127/kerbaujoget439-bit/png-squeeze/reward.svg)](https://make.fun/bounty/png-squeeze-01-run-20261127)

A lossless PNG recompressor built for the [make.fun](https://make.fun/bounty/png-squeeze-01-run-20261127) bounty **png-squeeze-01-run-20261127**.

## What it does

`main.ts` (Node 22, no dependencies, standard library only) reads each
instance's stored RGBA screenshot from `/input/<id>/image.png` and writes a
smaller, pixel-exact PNG to `/output/<id>/image.png` — every channel of every
pixel, including fully transparent pixels' colours, decodes exactly.

## How it works

1. **Decode** the input PNG to RGBA (own decoder: chunk parsing, CRC checks,
   inflate, unfiltering for all five filter types).
2. **Screen** the lossless forms — palette by count, palette by luminance,
   grey, RGB, grey+alpha, RGBA, plus a tRNS transparent key — crossed with
   the filter choices (fixed 0/1/2/3/4, minimum-sum, fewest-pairs), weighing
   each by zlib level 6 plus its chunk overhead; the smallest wins.
3. **Re-encode** the winner's filtered bytes with an optimal-parsing deflate
   in the manner of zopfli: hash-chain match finding (chain length adapts to
   the data size so the whole set of 8 fits the 60 s wall), shortest-path
   parsing over length/distance classes, length-limited Huffman codes by
   package-merge, dynamic blocks chosen against fixed-Huffman blocks.

## Properties

- Deterministic: no clock, no environment, no randomness — the same input
  always produces the same bytes.
- Fits the wall: the whole 8-instance set runs in ~40 s on this machine's
  single CPU, comfortably inside the 60 s budget.
- Repository under the 1 MiB cap; the entry is a single `main.ts` plus this
  README and the MIT licence.

## Local run

```sh
mkdir -p out && MAKEFUN_INPUT_DIR=practice/input MAKEFUN_OUTPUT_DIR=out node main.ts
```

## Licence

MIT — see `LICENSE`.

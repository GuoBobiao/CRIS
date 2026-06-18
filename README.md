# Seeing is Not Believing: Towards Credibility-Aware Robust Image Steganography

This repository contains the source code of our accepted paper entitled
 "Seeing is Not Believing: Towards Credibility-Aware Robust Image Steganography".

- `main_embed.m`: embed secret bits and generate trusted stego images.
- `main_extract.m`: extract secret bits and evaluate credibility.

## Quick Start

1. Run `main_embed.m`.
2. Run `main_extract.m`.

The default demo reads images from `examples/cover` and writes results under
`outputs/<method>/`.

## Methods

Set the parameter `opts.Method` to one of the following options to 
select the feature extraction method to be adopted:

- `"PCET"`：
- `"PCT"`
- `"PST"`

## Outputs

After embedding:

- `stego/`: image with the secret message embedded.
- `trusted_stego/`: image with the secret message and robust hash embedded.
- `metadata/`: secret bits, robust hash, and parameters for extraction.
- `results/embedding_summary.csv`: PSNR summary.

After extraction:

- `extracted_secret/`: recovered secret bits.
- `metrics/`: per-image extraction metrics.
- `extraction_summary.csv`: message, hash, DCT, and credibility accuracy.

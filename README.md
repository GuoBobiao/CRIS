# CRIS Reproducibility Package

This repository provides the paper-aligned implementation and experimental
pipeline of **Credibility-Aware Robust Image Steganography (CRIS)**, including
the PCET, PCT, and PST variants.

The release contains two parts:

1. **Direct reproduction of the main results** using the supplied `raw data`
   and `reproduce_table_iii.m`, `main_table_iii_psnr.m`,
   `main_table_iii_lpips.py`, and `reproduce_fig8.m`.
2. **End-to-end CRIS generation and extraction** using `main_embed.m` and
   `main_extract.m` for the remaining experiments.

## 1. Entry Points

| Entry point | Purpose |
|---|---|
| `reproduce_table_iii.m` | Reproduce the quality, robustness, and credibility results in Table III for all three CRIS variants. |
| `main_table_iii_psnr.m` | Independently compute the PSNR values and Difference in Table III. |
| `main_table_iii_lpips.py` | Independently compute the LPIPS values and Difference in Table III. |
| `reproduce_fig8.m` | Apply all 24 attacks in Fig. 8 and export the numerical results and plots. |
| `main_embed.m` | Batch-embed newly generated random 2048-bit secret images and save the stego images, trustworthy images, metadata, quality, and runtime. |
| `main_extract.m` | Blindly extract messages and fingerprints, then evaluate accuracy using the saved secrets. |
| `LSB.m` | Generate the LSB stego images used for method [36]. |

## 2. Requirements

### MATLAB

- MATLAB R2024a or later
- Image Processing Toolbox
- At least 4 GB of available memory

Parallel Computing Toolbox is not required.

### Python for LPIPS

- Conda or Miniconda
- `requirements/environment-lpips.yml`
- Optional CUDA-capable GPU; CPU execution is supported

The pinned environment uses Python 3.10.13, PyTorch 2.1.2,
torchvision 0.16.2, Pillow 10.2.0, and `lpips` 0.1.4.

## 3. Raw Data

The image data are hosted separately because of the GitHub repository size
limit.

> **Google Drive:**
> [Download the raw data](https://drive.google.com/drive/folders/1nolgmnKv4_bkIbyzPZqHhGnNwYRFoyKZ?usp=sharing)

Extract the data beside the code repository:

```text
project_root/
  CRIS_Reproducibility/
  raw data/
```

Required layout:

```text
raw data/
  Cover_Image/
  CRIS-PCET/
    dataRead/
    secret/
    Stego_Image/
    Image/
      Trustworthy_Image/
      NO1_JPEG80/
      ...
      NO17_JPEG80_Scaling0.8_Sanit_Zhu/
  CRIS-PCT/
    dataRead/
    secret/
    Stego_Image/
    Image/
      Trustworthy_Image/
      NO1_JPEG80/
      ...
      NO17_JPEG80_Scaling0.8_Sanit_Zhu/
  CRIS-PST/
    dataRead/
    secret/
    Stego_Image/
    image/
      Trustworthy_Image/
      NO1_JPEG80/
      ...
      NO17_JPEG80_Scaling0.8_Sanit_Zhu/
```

For another data location, set:

```matlab
cfg = cris_default_config(projectRoot);
cfg.dataRoot = "D:\path\to\raw data";
```

## 4. Reproducing the Main Results

### 4.1 Table III

Run in MATLAB:

```matlab
reproduce_table_iii
```

The script reads all 60 covers, their 2048-bit secrets, and the 17 supplied
degradation sets for PCET, PCT, and PST. It blindly extracts the message and
fingerprints, computes robustness, credibility, and
`Difference = Credibility - Robustness`, and exports the summary and
full-precision per-image results under `results/table_iii/`.

The quality values reported in Table III are:

| Metric | Variant | Stego/Robustness | Trustworthy/Credibility | Difference |
|---|---|---:|---:|---:|
| PSNR | PCET | 43.5172 | 40.9443 | -2.5729 |
| PSNR | PCT | 46.0083 | 42.2399 | -3.7684 |
| PSNR | PST | 44.7347 | 41.6336 | -3.1011 |
| LPIPS | PCET | 0.0467 | 0.0742 | 0.0275 |
| LPIPS | PCT | 0.0210 | 0.0532 | 0.0322 |
| LPIPS | PST | 0.0262 | 0.0575 | 0.0313 |

The complete degradation results are listed below. R, C, and D denote
Robustness, Credibility, and Difference, respectively.

| No. | Degradation | PCET-R | PCET-C | PCET-D | PCT-R | PCT-C | PCT-D | PST-R | PST-C | PST-D |
|---:|---|---:|---:|---:|---:|---:|---:|---:|---:|---:|
| 1 | JPEG (QF=80) | 0.9943 | 0.9931 | -0.0012 | 0.9922 | 0.9869 | -0.0053 | 0.9940 | 0.9927 | -0.0013 |
| 2 | JPEG (QF=60) | 0.9904 | 0.9875 | -0.0029 | 0.9828 | 0.9762 | -0.0066 | 0.9886 | 0.9830 | -0.0056 |
| 3 | JPEG (QF=40) | 0.9788 | 0.9813 | 0.0025 | 0.9572 | 0.9669 | 0.0097 | 0.9726 | 0.9770 | 0.0044 |
| 4 | Scaling (CF=0.8) | 0.9959 | 0.9957 | -0.0002 | 0.9939 | 0.9934 | -0.0005 | 0.9952 | 0.9963 | 0.0011 |
| 5 | Scaling (CF=0.7) | 0.9955 | 0.9942 | -0.0013 | 0.9938 | 0.9923 | -0.0015 | 0.9951 | 0.9950 | -0.0001 |
| 6 | Scaling (CF=0.6) | 0.9948 | 0.9918 | -0.0030 | 0.9934 | 0.9878 | -0.0056 | 0.9948 | 0.9924 | -0.0024 |
| 7 | JPEG (80) + Scaling (0.8) | 0.9941 | 0.9917 | -0.0024 | 0.9916 | 0.9842 | -0.0074 | 0.9939 | 0.9914 | -0.0025 |
| 8 | Sanit_Zhu | 0.9957 | 0.9953 | -0.0004 | 0.9940 | 0.9934 | -0.0006 | 0.9954 | 0.9971 | 0.0017 |
| 9 | Sanit_Cheng | 0.9769 | 0.9792 | 0.0023 | 0.9611 | 0.9659 | 0.0048 | 0.9740 | 0.9775 | 0.0035 |
| 10 | LSB + Sanit_Zhu | 0.9958 | 0.9953 | -0.0005 | 0.9941 | 0.9934 | -0.0007 | 0.9954 | 0.9971 | 0.0017 |
| 11 | LSB + Sanit_Cheng | 0.9769 | 0.9791 | 0.0022 | 0.9612 | 0.9660 | 0.0048 | 0.9740 | 0.9775 | 0.0035 |
| 12 | Steghide + Sanit_Zhu | 0.9958 | 0.9953 | -0.0005 | 0.9939 | 0.9935 | -0.0004 | 0.9952 | 0.9970 | 0.0018 |
| 13 | Steghide + Sanit_Cheng | 0.9768 | 0.9791 | 0.0023 | 0.9611 | 0.9655 | 0.0044 | 0.9740 | 0.9775 | 0.0035 |
| 14 | JPEG (QF=60) + Sanit_Zhu | 0.9900 | 0.9867 | -0.0033 | 0.9822 | 0.9753 | -0.0069 | 0.9886 | 0.9830 | -0.0056 |
| 15 | JPEG (QF=80) + Sanit_Cheng | 0.9753 | 0.9777 | 0.0024 | 0.9576 | 0.9626 | 0.0050 | 0.9716 | 0.9752 | 0.0036 |
| 16 | Scaling (CF=0.7) + Sanit_Zhu | 0.9849 | 0.9893 | 0.0044 | 0.9796 | 0.9840 | 0.0044 | 0.9876 | 0.9891 | 0.0015 |
| 17 | JPEG (80) + Scaling (0.8) + Sanit_Zhu | 0.9882 | 0.9890 | 0.0008 | 0.9819 | 0.9809 | -0.0010 | 0.9887 | 0.9873 | -0.0014 |
| Average | — | 0.9882 | 0.9883 | 0.0001 | 0.9807 | 0.9805 | -0.0002 | 0.9870 | 0.9874 | 0.0004 |

### 4.2 Independent PSNR Evaluation

Run:

```matlab
main_table_iii_psnr
```

This entry point reads the supplied cover, stego, and trustworthy images
without rerunning CRIS embedding. It exports per-image values and the averages
under `results/table_iii_quality/psnr/`.

### 4.3 Independent LPIPS Evaluation

Create the pinned environment:

```bash
conda env create -f requirements/environment-lpips.yml
conda activate cris-lpips
```

Validate all 360 image pairs:

```bash
python main_table_iii_lpips.py --data-root "../raw data" --output-dir "results/table_iii_quality/lpips" --dry-run
```

Run the complete evaluation:

```bash
python main_table_iii_lpips.py --data-root "../raw data" --output-dir "results/table_iii_quality/lpips" --net alex --device auto
```

RGB images are kept unchanged. A grayscale image is replicated into three
identical channels. Images retain their original width and height; no resizing
or interpolation is applied. The script uses calibrated AlexNet LPIPS version
0.1 and maps the input range to `[-1,1]`.

### 4.4 Fig. 8

Run:

```matlab
reproduce_fig8
```

The script evaluates all three variants under 24 attacks:

- Gaussian noise variances: `0.0005` to `0.0010`;
- salt-and-pepper noise densities: `0.0005` to `0.0010`;
- centered cropping ratios: `0.1` to `0.6`;
- rotation angles: `30`, `60`, `90`, `120`, `150`, and `180` degrees.

Results and plots are saved under `results/fig8/`.

## 5. Generating New CRIS Experiments

### 5.1 Batch Embedding

Select the variant and image count in `main_embed.m`:

```matlab
cfg.variant = "PCET";           % "PCET", "PCT", or "PST"
cfg.execution.maxImages = Inf;  % Use 1 for a smoke test
```

Then run:

```matlab
main_embed
```

For each cover, the script generates a random `32 x 64` binary secret image,
embeds its 2048 bits, generates and backs up the state fingerprint, and saves
the secret, stego image, trustworthy image, metadata, PSNR, and stage-specific
runtime under `results/batch/<VARIANT>/`.

### 5.2 Batch Extraction

Use the same variant in `main_extract.m`, then run:

```matlab
main_extract
```

The receiver blindly extracts the message, degraded fingerprint, backup
fingerprint, and credibility. The saved secret is loaded only after extraction
to calculate bit accuracy. Results are saved under
`results/batch/<VARIANT>/receiver/`.

For attacked images, preserve the filenames and set:

```matlab
cfg.batch.receivedImageDirectory = "D:\path\to\attacked_images";
cfg.batch.secretImageDirectory = ...
    fullfile(batchRoot, "secret_images");
```

### 5.3 Remaining Experiments

#### Table IV: Robustness Comparison

Use `stego_images/` as the CRIS robustness-only output, apply the evaluated
degradations, and extract the messages with `main_extract.m`. The comparison
protocol uses:

- [HiDDeN](https://github.com/jirenz/HiDDeN): 64 patches per image and the
  original 30-bit model for each patch;
- [ROAST](https://github.com/coriverchen/Robust_Steganography): QF=60 PNG
  precompression and a maximum payload of 2048 bits;
- Hu et al.: sixteen `256 x 256` blocks with 128 bits per block;
- [WatermarkAttacker](https://github.com/XuandongZhao/WatermarkAttacker):
  quality level 6 for Sanit_Cheng;
- [Steghide 0.5.1](https://steghide.sourceforge.net/) and the included
  `LSB.m` for the corresponding stegomalware conditions.

The Hu et al. and Sanit_Zhu implementations cannot be redistributed. Their
processed inputs used for the main CRIS evaluation are included in
`raw data`.

#### Fig. 6: Intermediate Credibility Indicators

`receiver_metadata/<name>.mat` contains the extracted message, degraded
fingerprint, backup fingerprint, and pair credibility.
`sender_metadata/<name>.mat` contains the original state fingerprint.
Compute:

1. **Info. Ext.**: extracted-message accuracy;
2. **Deg. FP**: degraded/original fingerprint matching rate;
3. **Backup FP**: backup/original fingerprint matching rate;
4. **Cred.**: degraded/backup fingerprint matching rate.

#### Fig. 7: Failure Cases

Apply the extreme conditions specified in the paper, including JPEG QF=10,
scaling CF=0.25, and the corresponding sanitization combinations. Run
`main_extract.m` and evaluate the same four indicators as in Fig. 6.

#### Tables V and VI: Steganalysis

Use BOSSBase images 1--6000 for training, 6001--8000 for validation, and
8001--10000 for testing. Generate CRIS stego and trustworthy images with
`main_embed.m`, and use the official
[SRNet](https://github.com/brijeshiitg/Pytorch-implementation-of-SRNet) and
[SiaStegNet](https://github.com/SiaStg/SiaStegNet) implementations.

For the unknown-model setting, train SRNet on WOW and SiaStegNet on
S-UNIWARD, then test on CRIS images. For the known-model setting, generate
CRIS images for training and validation as well.

#### Table VII: Efficiency

Run `main_embed.m` and `main_extract.m` on all test images and use the
stage-specific averages in their summary CSV files. Report the MATLAB version,
operating system, CPU, GPU if used, and image count. All baseline methods must
be timed on the same machine and images.

### 5.4 LSB Baseline [36]

`LSB.m` uses:

```matlab
inputFolder = fullfile(cfg.dataRoot, 'Cover_Image');
outputFolder = fullfile(cfg.resultsRoot, 'baselines', 'LSB', 'Stego_Image');
```

Run:

```matlab
LSB
```

The script performs classical LSB replacement at `0.5` BPP and saves
lossless PNG outputs.

Additional paper/code correspondence is provided in
`docs/PAPER_CODE_ALIGNMENT.md`, and the evaluation protocol is documented in
`docs/DATA_AND_EVALUATION_PROTOCOL.md`.

## 6. Reproducibility and Third-Party Code

This package provides the CRIS algorithms, complete parameters, raw inputs for
the main results, deterministic attack definitions, direct reproduction
entries, independent PSNR/LPIPS evaluators, per-image evidence, and end-to-end
sender/receiver pipelines.

Third-party datasets, baselines, and sanitizers remain subject to their own
licenses. Public implementations are linked above; privately obtained code is
not redistributed without permission.

## 7. Citation

If this code or data is useful in your research, please cite the corresponding
CRIS paper. The final BibTeX entry will be added after publication.

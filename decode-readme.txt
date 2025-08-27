# EM Covert Channel Decoder (Frame-Based)

A reference implementation to decode frame-indexed payloads from complex IQ samples.
The pipeline:
1) Load interleaved **float32** IQ from a binary file.
2) Compute magnitude and wrapped phase-difference features.
3) Frame-head alignment and image-ID verification.
4) Row-wise bit extraction with polarity + threshold adaptation.
5) Save per-row results, plots (optional), and aggregate statistics.

> **Note:** This code mirrors the evaluation setting used in our paper. All defaults match the experiments unless overridden via CLI.

## Installation
```bash
pip install -r requirements.txt


Basic Usage:
python decode.py \
  --iq_file ./data \
  --txt_folder ./txt \
  --img_dir ./img_result \
  --result_dir ./result



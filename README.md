# EM Covert Channel Toolkit

This toolkit contains two main components:
1. **Bit Embedding Pipeline**: Converts text into bitstreams and embeds them into 1920×1080 images/videos.
2. **Frame-Based Decoder**: Decodes embedded bitstreams and image content from received IQ samples.

---

## 1. Bit Embedding Pipeline (1920×1080, 60-bit rows)

This tool converts a UTF-8 text file into a bitstream, splits it into blocks of `60 * (NUM_LINES - 1)` bits, and embeds each block into a 1920×1080 RGB image:

- **Row 0**: `FIRST_ROW_PREFIX` (14 bits) + 11-bit image index, right-padded with zeros to 60 bits  
- **Rows 1..NUM_LINES-1**: `SYNC_BITS` (default `1010`) + 60 data bits per row  
- For each image, a companion `N.txt` is written with 60 bits per line  

Column groups per bit are computed by rounding the ideal group width (`ideal≈29.7` over 1920 columns -> 64 groups per row).

### Installation
```bash
pip install -r requirements.txt
```

### Usage Example
```bash
python encode.py   --txt_input ./text8_1MB.txt   --bin_output ./1MB/output.bin   --txt_output_dir ./1MB/bin_txt   --img_template ./black.png   --img_output_dir ./1MB/img   --bits_per_line 60   --num_lines 1080   --sync_bits 1 0 1 0   --first_row_prefix 10101010000000   --on_value 255   --width 1920 --height 1080
```

---

## 2. Image → Video (Lossless FFV1)

Create a video from a sequence of PNG/JPEG frames using OpenCV.  
The default codec is **FFV1** in MKV (lossless).

### Installation
```bash
pip install -r requirements.txt
```

### Usage Example
```bash
python video_from_images.py   --image_dir ./1MB   --pattern "black.png"   --output ./1MB.mkv   --fps 60   --width 1920 --height 1080   --codec FFV1
```

---

## 3. EM Covert Channel Decoder (Frame-Based)

This module provides a reference implementation for decoding frame-indexed payloads from complex IQ samples.

### Decoding Pipeline
1. Load interleaved **float32** IQ data from a binary file.  
2. Compute magnitude and wrapped phase-difference features.  
3. Perform frame-head alignment and image ID verification.  
4. Extract bits row-wise with polarity and threshold adaptation.  
5. Save per-row results, plots (optional), and aggregate statistics.  

> **Note:** Defaults match the experimental settings used in our paper unless overridden via CLI.

### Installation
```bash
pip install -r requirements.txt
```

### Usage Example
```bash
python decode.py   --iq_file ./data   --txt_folder ./txt   --img_dir ./img_result   --result_dir ./result
```

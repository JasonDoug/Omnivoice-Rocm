# OmniVoice-ROCm 🚀

<p align="center">
  <img width="200" height="200" alt="OmniVoice" src="https://zhu-han.github.io/omnivoice/pics/omnivoice.jpg" />
</p>

<p align="center">
  <a href="https://github.com/JasonDoug/Omnivoice-Rocm"><img src="https://img.shields.io/badge/AMD_ROCm-7.x-red?logo=amd" alt="AMD ROCm 7.x"></a>
  &nbsp;
  <a href="https://huggingface.co/k2-fsa/OmniVoice"><img src="https://img.shields.io/badge/%F0%9F%A4%97%20Hugging%20Face-Model-FFD21E" alt="Hugging Face Model"></a>
  &nbsp;
  <a href="https://arxiv.org/abs/2604.00688"><img src="https://img.shields.io/badge/arXiv-Paper-B31B1B.svg"></a>
</p>

**OmniVoice-ROCm** is a specialized, high-performance fork of **OmniVoice** optimized for **AMD Radeon GPUs, Strix Point APUs (Radeon 890M / `gfx1150`), and Instinct accelerators** running **AMD ROCm 7.x**.

OmniVoice is a state-of-the-art massively multilingual zero-shot text-to-speech (TTS) model supporting over 600 languages with voice cloning and text-guided voice design.

---

## ⚡ AMD ROCm Optimizations & Key Features

- **AMD ROCm 7.x Native Support**: Pre-configured for official PyTorch ROCm 7.0 builds (`torch==2.10.0+rocm7.0`) with native `gfx1150` hardware support (AMD Ryzen AI 9 HX 370 / Radeon 890M APUs).
- **Sub-2-Second Generations**: Includes MIOpen and AOTriton flash attention tuning (`MIOPEN_FIND_MODE=FAST` & `TORCH_ROCM_AOTRITON_ENABLE_EXPERIMENTAL=1`), generating speech in **~1.8s**.
- **Enhanced Audio Format Compatibility**: Automatic fallback to `pydub` + `ffmpeg` for seamless loading of `.m4a`, `.aac`, `.mp3`, `.wma`, `.flac`, and `.wav` reference audio clips.
- **Fixed Gradio 6.0 Web UI**: Resolved component ordering and type validation bugs across Voice Design and Voice Clone modes.
- **600+ Languages Supported**: Massively multilingual zero-shot voice cloning and text-guided voice design.

---

## 🚀 Quick Start (AMD ROCm 7.x)

### 1. Installation

Clone this repository and set up a virtual environment:

```bash
git clone https://github.com/JasonDoug/Omnivoice-Rocm.git
cd Omnivoice-Rocm

# Create a virtual environment using uv or venv
uv venv
source .venv/bin/activate

# Install OmniVoice-ROCm with dependencies
uv pip install -e .
```

### 2. Launch Gradio Web UI on GPU

Run the launch script with ROCm speedup flags enabled:

```bash
# 1. Enable fast MIOpen kernel selection and experimental AOTriton
export MIOPEN_FIND_MODE=FAST
export TORCH_ROCM_AOTRITON_ENABLE_EXPERIMENTAL=1

# 2. Add ROCm shared library paths
export LD_LIBRARY_PATH=$PWD/.venv/lib/python3.13/site-packages/torch/lib:/opt/rocm/lib:$LD_LIBRARY_PATH

# 3. Activate environment & launch Web UI targeting GPU (`cuda`)
source .venv/bin/activate
omnivoice-demo --port 7860 --device cuda
```

Navigate to **`http://localhost:7860`** in your browser.

---

## 💻 Python API Usage

```python
from omnivoice import OmniVoice
import soundfile as sf

# Load pre-trained model on ROCm GPU
model = OmniVoice.from_pretrained(
    "k2-fsa/OmniVoice", 
    device_map="cuda", 
    dtype="float16"
)

# 1. Zero-Shot Voice Cloning (.m4a, .wav, .mp3 supported)
audio_cloned = model.generate(
    text="Hello! This is zero-shot voice cloning running natively on AMD ROCm.",
    ref_audio="reference_sample.m4a",
    ref_text="Matching transcript of the reference audio clip.",
)
sf.write("cloned_output.wav", audio_cloned[0], 24000)

# 2. Text-Guided Voice Design (No reference audio required)
audio_designed = model.generate(
    text="Welcome to the audio presentation.",
    instruct="female, low pitch, British accent"
)
sf.write("designed_output.wav", audio_designed[0], 24000)
```

---

## 🛠️ Command-Line Interface (CLI)

#### **Single Item Generation on GPU**
```bash
omnivoice-infer \
  --text "OmniVoice-ROCm fast inference test." \
  --device cuda \
  --num_step 16 \
  --output test_gpu.wav
```

#### **Voice Design Mode**
```bash
omnivoice-infer \
  --text "Hello world! Welcome back to the channel." \
  --instruct "male, deep voice, American accent" \
  --device cuda \
  --num_step 20 \
  --output voice_design.wav
```

---

## 📊 Performance Benchmarks (Radeon 890M / ROCm 7)

| Mode | Steps (`num_step`) | Execution Time | Real-Time Factor (RTF) |
| :--- | :---: | :---: | :---: |
| **GPU (ROCm 7 Native)** | 16 | **~1.8 seconds** | **~0.18** |
| **GPU (ROCm 7 Native)** | 32 | **~3.4 seconds** | **~0.34** |
| **CPU Fallback** | 32 | **~20.5 seconds** | **~1.05** |

---

## 📜 License

Apache 2.0 License. Original model checkpoint by [k2-fsa/OmniVoice](https://github.com/k2-fsa/OmniVoice).

---

## 🔗 References & Citation

```bibtex
@article{zhu2026omnivoice,
  title={OmniVoice: Towards Omnilingual Zero-Shot Text-to-Speech with Diffusion Language Models},
  author={Zhu, Han and others},
  journal={arXiv preprint arXiv:2604.00688},
  year={2026}
}
```

<div align="center">

# 🤖 AI Digital Twin — Talking Head Video Generation

**A fully AI-generated digital twin that speaks in my own cloned voice — built entirely in ComfyUI.**

[![ComfyUI](https://img.shields.io/badge/ComfyUI-Workflow-blue?style=flat-square)](https://github.com/comfyanonymous/ComfyUI)
[![WanVideo](https://img.shields.io/badge/WanVideo-2.1_14B-orange?style=flat-square)](https://huggingface.co/city96/Wan2.1-I2V-14B-480P-gguf)
[![InfiniteTalk](https://img.shields.io/badge/InfiniteTalk-MultiTalk-red?style=flat-square)](https://huggingface.co/Kijai/WanVideo_comfy)
[![FLUX LoRA](https://img.shields.io/badge/FLUX-LoRA-purple?style=flat-square)](https://blackforestlabs.ai)
[![GenAI](https://img.shields.io/badge/Generative-AI-green?style=flat-square)]()

</div>

---

## What is this?

This project builds a **realistic AI digital twin** — a talking-head video where an AI-generated version of me speaks in my own cloned voice, with accurate lip-sync and natural facial animation.

The entire pipeline runs inside **ComfyUI**. No stock avatars. No celebrity likenesses. Every visual and every word of audio is generated from my own data.

> **Final output:** A 1-minute talking-head video featuring AI-generated images of myself, voice cloned from my own recordings, and frame-perfect lip-synced animation.

---

## 🎬 Final Output

<div align="center">

### [⬇ Click to Download & Watch — AI Digital Twin (1 min)](https://github.com/GuruCharan6/AI-Digital-Twin/raw/main/Final%20Output.mp4)

> *1-minute talking head video — AI-generated visuals, cloned voice, frame-perfect lip-sync*

</div>

---

## 🔧 ComfyUI Workflow

<div align="center">

![ComfyUI I2V InfiniteTalk Workflow](https://github.com/user-attachments/assets/97ec54ff-395c-4493-964d-aad31bd842f0)

*The complete I2V_InfiniteTalk pipeline — image input (top), audio processing (middle), text conditioning (bottom), converging into the WanVideoSampler*

</div>

---

## Pipeline Overview

```
Personal Photos (10–20)
       ↓
FLUX LoRA Training → AI-Generated Images of Myself
       ↓
Voice Recording (2–3 min) → Chatterbox Voice Cloning → Cloned Audio
       ↓
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
          ComfyUI I2V_InfiniteTalk Workflow
━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━
  Image  → Resize (640×640) → CLIP Vision → Image Embeddings
  Audio  → MelBandRoFormer  → Wav2Vec2   → MultiTalk Embeddings
  Prompt → UMT5-XXL Encoder             → Text Embeddings
                      ↓
          WanVideo 2.1 Sampler (14B)
                      ↓
              VAE Decode → Frames (25 FPS)
                      ↓
          Video Combiner + Cloned Audio
                      ↓
                  Final MP4 🎬
```

---

## Stage 1 — Image Generation (FLUX LoRA)

**Goal:** Generate realistic AI images of myself to use as reference frames.

- Collected 10–20 personal photos with varied lighting and angles
- Trained a custom **FLUX LoRA** on these photos
- Generated 4 high-quality AI images of myself for the animation pipeline

| Why FLUX? |
|---|
| FLUX.1 Dev's transformer architecture produces significantly more realistic faces than SD 1.5 or SDXL — critical for a believable digital twin |

---

## Stage 2 — Voice Cloning (Chatterbox)

**Goal:** Clone my voice so the digital twin speaks exactly like me.

- Recorded 2–3 minutes of clean voice samples
- Used **Chatterbox** for voice replication
- Output: a cloned `.wav` audio file of my script, spoken in my own voice

---

## Stage 3 — Talking Head Animation (InfiniteTalk)

**Goal:** Animate the AI images to lip-sync with the cloned audio inside ComfyUI.

### Models Used

| Component | Model | Purpose |
|---|---|---|
| **Video Diffusion** | `wan2.1-i2v-14b-720p-Q8_0.gguf` | Image-to-video generation (14B params) |
| **MultiTalk** | `Wan2_1-InfiniTetalk-Single_fp16.safetensors` | Talking head + lip-sync |
| **VAE** | `Wan2_1_VAE_bf16.safetensors` | Latent decoding to frames |
| **LoRA** | `lightx2v_I2V_14B_480p_cfg_step_distill_rank128_bf16.safetensors` | CFG distillation for faster inference |
| **Audio Processor** | `wav2vec2-chinese-base_fp16.safetensors` | Audio feature extraction |
| **Vocal Separator** | `MelBandRoformer_fp16.safetensors` | Clean vocal isolation |
| **CLIP Vision** | `clip_vision_h.safetensors` | Image embedding for consistency |
| **Text Encoder** | `umt5-xxl-enc-bf16.safetensors` | Prompt conditioning |

### Workflow Node Chain

```
Input Image
    ↓
Resize (640×640) → CLIP Vision Encoder → Image Embeddings
    ↓
I2V Conditioning (WanVideo 2.1)
    ↓                              ↑
Input Audio                   Text Prompt
    ↓                              ↓
MelBandRoFormer           UMT5-XXL Encoder
(Vocal Separation)         (Text Embeddings)
    ↓
Wav2Vec2 Audio Encoder
    ↓
MultiTalk Embeddings
    ↓
WanVideo Sampler → Latents
    ↓
VAE Decode → Frames (25 FPS)
    ↓
Video Combiner + Cloned Audio
    ↓
Final MP4
```

### Generation Settings

| Parameter | Value |
|---|---|
| Resolution | 640 × 640 px |
| Frame Rate | 25 FPS |
| Max Frames | 500 (1 sec = 25 frames) |
| Model Format | GGUF Q8_0 (optimized for lower VRAM) |
| Video Model | WanVideo 2.1 I2V 14B |

---

## Model Directory Structure

```
ComfyUI/models/
├── diffusion_models/
│   ├── wan2.1-i2v-14b-720p-Q8_0.gguf
│   ├── MelBandRoformer_fp16.safetensors
│   └── InfiniteTalk/
│       └── Wan2_1-InfiniTetalk-Single_fp16.safetensors
├── vae/
│   └── Wan2_1_VAE_bf16.safetensors
├── loras/
│   └── lightx2v_I2V_14B_480p_cfg_step_distill_rank128_bf16.safetensors
├── wav2vec2/
│   └── wav2vec2-chinese-base_fp16.safetensors
├── clip_vision/
│   └── clip_vision_h.safetensors
└── text_encoders/
    └── umt5-xxl-enc-bf16.safetensors
```

---

## How to Reproduce

### Step 1 — Prepare Your Data
```
1. Collect 10–20 high-quality photos of yourself
2. Train FLUX LoRA on your photos
3. Generate AI images of yourself
4. Record 2–3 minutes of voice samples
5. Clone your voice using Chatterbox
6. Generate your script audio
```

### Step 2 — Setup ComfyUI
```
1. Install ComfyUI
2. Download all models (links below)
3. Place models in correct directories
4. Load I2V InfiniteTalk Workflow.json into ComfyUI
```

### Step 3 — Configure & Run
```
1. Load your reference image
2. Load your cloned audio file
3. Set max_frames = audio_length_seconds × 25
4. Queue the workflow
5. Video saves to ComfyUI/output/
```

---

## Model Download Links

| Model | Source |
|---|---|
| WanVideo 2.1 I2V 14B GGUF | [HuggingFace](https://huggingface.co/city96/Wan2.1-I2V-14B-480P-gguf) |
| InfiniteTalk MultiTalk | [HuggingFace](https://huggingface.co/Kijai/WanVideo_comfy/tree/main/InfiniteTalk) |
| Wav2Vec2 | [HuggingFace](https://huggingface.co/Kijai/wav2vec2_safetensors) |
| MelBandRoFormer | [HuggingFace](https://huggingface.co/Kijai/MelBandRoFormer_comfy) |

---

## Key Takeaways

- **No stock avatars** — all visuals generated from personal photos via FLUX LoRA
- **No stock voices** — audio cloned from personal recordings via Chatterbox
- **14B parameter model** (WanVideo 2.1) produces significantly more realistic motion
- **GGUF quantization** makes the 14B model accessible on consumer GPUs
- **InfiniteTalk** enables frame-perfect lip-sync by fusing audio embeddings directly into the video diffusion process

---

## License

MIT — free to fork and build on.

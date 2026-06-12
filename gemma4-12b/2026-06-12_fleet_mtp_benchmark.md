# Fleet MTP Benchmark — Gemma 4 12B QAT + llama.cpp MTP

**Date:** 2026-06-12
**Model:** gemma-4-12B-it-qat-UD-Q4_K_XL.gguf + MTP heads (unsloth Q8_0, 465MB)
**Backend:** llama-server (PR #23398, merged 2026-06-07)
**Method:** OpenAI API, prompt "How are you?", max_tokens 350, avg 3 runs

## Results

| Node | GPU | VRAM | Baseline (tok/s) | MTP (tok/s) | Speedup | Acceptance | Notes |
|------|-----|------|-----------------|-------------|---------|------------|-------|
| RTX 4090 (ref) | GeForce RTX 4090 | 24GB | 98.2 | 235.9 | **2.2x** | 70.6% | Reference from Oracle Pattern PDF |
| Yuki (.6) | RX 7900 XTX | 24GB | 66.0 | 94.6 | **1.43x** | 43.1% | ROCm 7.0, /tmp full → moved to /mnt/ai_beast |
| Kumo (.10) | Tesla P4 ×3 | 48GB | 16.3 | 24.9 | **1.53x** | 58.1% | Used GPU 1+2 only (GPU 0 thermal throttle 88°C) |
| kaze (.9) | CMP 30HX ×2 | 12GB | 17.4 | 8.2 | **❌ 0.47x** | 41-49% | VRAM insufficient — QAT needs 12.8GB, spilled to CPU RAM |

## Key Findings

### MTP = Free Lunch (when VRAM fits)
- RTX 4090: **2.2x** speedup — CUDA maturity + high acceptance rate
- ROCm (RX 7900 XTX): **1.43x** — lower acceptance (43%) vs NVIDIA (70%), ROCm MTP path not fully optimized
- Pascal (Tesla P4): **1.53x** — older arch but acceptance 58%, multi-GPU helps

### VRAM Threshold is Critical
kaze (12GB) fails because QAT model requires 12.8GB → overflow to system RAM → PCIe bottleneck kills all gains.

**Rule:** MTP only helps if entire model fits in VRAM. No headroom = slower than baseline.

| Config | VRAM Required |
|--------|--------------|
| Q4_K_M baseline | 13.4 GB |
| Q4_K_M + MTP | 14.2 GB |
| QAT baseline | 12.8 GB |
| QAT + MTP | 13.5 GB |

### ROCm vs CUDA Acceptance Rate Gap
- NVIDIA: 70.6% acceptance → 2.2x speedup
- AMD ROCm: 43.1% acceptance → 1.43x speedup

ROCm MTP speculative decoding implementation lag behind CUDA. Still worth enabling on 24GB+ VRAM AMD cards.

## Setup Commands

```bash
# Download models
hf download unsloth/gemma-4-12B-it-qat-GGUF gemma-4-12B-it-qat-UD-Q4_K_XL.gguf --local-dir ~/models/
hf download unsloth/gemma-4-12b-it-GGUF MTP/gemma-4-12b-it-Q8_0-MTP.gguf --local-dir ~/models/

# Run with MTP
./build/bin/llama-server \
  -m ~/models/gemma-4-12B-it-qat-UD-Q4_K_XL.gguf \
  --spec-draft-model ~/models/MTP/gemma-4-12b-it-Q8_0-MTP.gguf \
  --spec-type draft-mtp --spec-draft-n-max 4 \
  -ngl 99 --port 8080 --host 0.0.0.0
```

## Fleet Recommendation

| VRAM | Action |
|------|--------|
| ≥14GB | Enable MTP, use QAT model |
| 12-14GB | Baseline only (no MTP), QAT fits tight |
| <12GB | Use smaller model (gemma:2b, llama3.2:1b) |

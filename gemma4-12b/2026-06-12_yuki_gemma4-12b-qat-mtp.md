# Yuki — Gemma 4 12B QAT + MTP Benchmark

**Date:** 2026-06-12
**Host:** Yuki (192.168.1.6)
**GPU:** AMD RX 7900 XTX 24GB
**Backend:** llama-server + ROCm (PR #23398)
**Model:** gemma-4-12B-it-qat-UD-Q4_K_XL.gguf + MTP heads (unsloth Q8_0, 465MB)

## Results

| Mode | tok/s | Notes |
|------|-------|-------|
| Baseline QAT | 66.0 | no MTP |
| **MTP ON** | **94.6** | **+43% speedup** |
| Acceptance Rate | 43.1% | lower than NVIDIA (70%) |
| Speedup | **1.43x** | |

## Notes
- `/tmp` full during setup → moved build + models to `/mnt/ai_beast/mtp-benchmark`
- ROCm MTP acceptance rate (43%) significantly lower than CUDA RTX 4090 (70%) — ROCm speculative decoding path not yet fully optimized
- MTP still worthwhile on 24GB VRAM — model fits entirely, no spill

## vs Fleet

| GPU | Backend | Baseline | MTP | Speedup | Acceptance |
|-----|---------|----------|-----|---------|------------|
| RTX 4090 (ref) | CUDA | 98.2 | 235.9 | 2.2x | 70.6% |
| **RX 7900 XTX (Yuki)** | **ROCm** | **66.0** | **94.6** | **1.43x** | **43.1%** |
| Tesla P4 ×3 (Kumo) | CUDA | 16.3 | 24.9 | 1.53x | 58.1% |
| CMP 30HX ×2 (kaze) | CUDA SM75 | 17.4 | 8.2 ❌ | 0.47x | ~45% |

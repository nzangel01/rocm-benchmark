# Yuki — gemma4:12b-it-GGUF MTP Benchmark

**Date:** 2026-06-12
**Host:** Yuki (192.168.1.6)
**GPU:** AMD RX 7900 XTX 24GB
**Backend:** llama.cpp + ROCm
**Model:** gemma4:12b-it-GGUF

## Results

| Mode | Speed (tps) | Notes |
|------|-------------|-------|
| MTP OFF | 36 | baseline |
| **MTP ON** | **65** | ~1.8x speedup |

## vs Fleet

| GPU | tps | Backend | MTP |
|-----|-----|---------|-----|
| RTX 3090 (reference) | 65.9 | CUDA | - |
| **RX 7900 XTX (Yuki)** | **65** | **ROCm** | **✅ ON** |
| RX 7900 XTX (Yuki) | 36 | ROCm | ❌ OFF |
| Tesla P100 (Kokkoro) | 35.9 | CUDA | - |
| Arc B580 (Silvia) | 21.3 | Vulkan | - |

## Notes
- MTP (Multi-Token Prediction) patch landed in llama.cpp upstream — no manual patch needed
- ROCm + llama.cpp MTP achieves parity with RTX 3090 on gemma4:12b
- Previous attempts failed for days; upstream fixed itself

# kaze — CMP 30HX ×2 MTP Deep Dive

**Date:** 2026-06-12
**Host:** kaze (192.168.1.9) — VM300 on PVE .221
**GPU:** 2× NVIDIA CMP 30HX (Turing TU116, 6GB VRAM each = 12GB total)
**Backend:** llama-server (PR #23398, merged 2026-06-07)

## Gemma 4 12B QAT + MTP

| | tok/s | Notes |
|---|---|---|
| Baseline QAT | 17.4 | VRAM overflow → CPU RAM spill |
| MTP ON | 8.2 ❌ | Worse — PCIe bottleneck |

**Root cause:** QAT 12B requires 12.8GB VRAM, kaze has 12GB → overflow to system RAM → MTP overhead adds 465MB → double the bottleneck.

## Gemma 4 4B QAT + MTP

| | tok/s | VRAM | Notes |
|---|---|---|---|
| Baseline QAT | **36.5** | ~5.0GB | ✅ Fits easily |
| MTP ON | 14.1 ❌ | ~5.3GB | Flash Attention crash |

**Root causes:**
1. Turing SM75 (`fattn.cu` fatal error) — MTP requires Flash Attention, not supported on SM75 for this model
2. Forced `-fa 0` (disable Flash Attention) → KV cache bandwidth overhead kills MTP gains
3. 4B draft acceptance rate ~34% (vs 70% on 12B RTX 4090) — too low to overcome overhead

## kaze Sweet Spot — Ollama, Small Models

| Model | Backend | tok/s |
|---|---|---|
| llama3.2:1b | Ollama | **143** |
| gemma:2b | Ollama | **83** |
| gemma4:4b QAT | llama-server (no MTP) | 36.5 |

## Conclusions

1. **MTP requires Flash Attention** — Turing SM75 cannot run MTP on Gemma 4 (any size)
2. **12GB VRAM is below the QAT 12B threshold** (12.8GB) — no headroom
3. **kaze's real value = high-speed small model inference via Ollama** — 143 tok/s on llama3.2:1b is excellent for agent workflows
4. **MTP sweet spot** starts at 14GB+ VRAM with Ampere (SM80+) or newer architecture

## Architecture Compatibility Notes

| Arch | SM | Flash Attn (Gemma4 MTP) | Verdict |
|---|---|---|---|
| Turing (CMP 30HX, RTX 20xx) | SM75 | ❌ crash | MTP not usable |
| Ampere (RTX 30xx) | SM80+ | ✅ | MTP works |
| Ada (RTX 40xx) | SM89 | ✅ | Best results |
| RDNA3 (RX 7900 XTX) | ROCm | ✅ | 1.43x (lower acceptance) |
| Pascal (P4) | SM61 | ✅ | 1.53x |

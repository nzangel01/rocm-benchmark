# Gemma 4 12B QAT — Silvia (2026-06-11)

**Model:** gemma4:12b-it-qat (QAT Q4_0, 7.2 GiB)
**Node:** silvia (192.168.1.24)
**GPU:** Intel Arc B580 12GB (BMG G21)
**Ollama:** 0.30.7
**Backend:** Vulkan (Intel Arc via Mesa/GGML_VK)

## Results (tg — token generation)

| run | eval rate |
|---|---|
| 1 | 20.97 tok/s |
| 2 | 21.34 tok/s |
| 3 | 21.60 tok/s |
| **avg** | **21.30 tok/s** |

## Fleet Comparison

| Node | GPU | VRAM | Backend | tok/s |
|---|---|---|---|---|
| RTX 3090 (ref) | RTX 3090 | 24GB | CUDA | **90.87** |
| yuki | RX 7900 XTX | 24GB | ROCm | **53.67** |
| kokkoro | RTX 3060 x2 | 24GB | CUDA (PCIe 4.0 x16, S8030) | **35.95** |
| sheffy | RTX 2080 Ti | 11GB | CUDA | 41.6 |
| silvia | Arc B580 | 12GB | Vulkan | **21.30** |
| kumo | Tesla P4 x3 | 24GB | CUDA | 13.0 |

## Notes

- Ollama install script แจ้ง "No NVIDIA/AMD GPU detected" แต่ B580 ใช้งานผ่าน Vulkan ได้จริง
- ยืนยัน GPU active: clinfo + vulkaninfo เห็น "Intel Arc B580 Graphics (BMG G21)"
- 21 tok/s สอดคล้องกับ fleet benchmark เดิม (20.4 tok/s) — ไม่ใช่ CPU mode
- CPU-only จะได้แค่ ~5-8 tok/s สำหรับ 12B model บน Threadripper 3960X

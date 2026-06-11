# Gemma 4 12B QAT — Kokkoro (2026-06-11)

**Model:** gemma4:12b-it-qat (QAT Q4_0, 6.48 GiB)
**Node:** kokkoro (192.168.1.5)
**GPU:** 2x RTX 3060 12GB (24GB total VRAM) — PCIe 4.0 x16
**Board:** SuperMicro S8030 (server board, bundled with GPUs)
**Ollama:** 0.30.7
**Backend:** CUDA

## Results (tg — token generation)

| run | eval rate |
|---|---|
| 1 | 35.13 tok/s |
| 2 | 36.37 tok/s |
| 3 | 36.36 tok/s |
| **avg** | **35.95 tok/s** |

## Fleet Comparison

| Node | GPU | VRAM | Backend | tok/s |
|---|---|---|---|---|
| RTX 3090 (ref) | RTX 3090 | 24GB | CUDA | **90.87** |
| yuki | RX 7900 XTX | 24GB | ROCm | **53.67** |
| kokkoro | RTX 3060 x2 | 24GB | CUDA | **35.95** |
| sheffy | RTX 2080 Ti | 11GB | CUDA | 41.6 |
| silvia | Arc B580 | 12GB | Vulkan | 20.4 |
| kumo | Tesla P4 x3 | 24GB | CUDA | 13.0 |

## Notes

- RTX 3060 x2 vs RTX 3090: ช้ากว่า 60% แม้ VRAM รวมเท่ากัน (24GB)
- Multi-GPU split inference overhead ลด throughput เทียบ single GPU
- S8030 board มาพร้อม GPU → ต้นทุนจริงต่อ tok/s ถูกมาก (board ราคาหลักพัน)
- PCIe 4.0 x16 ทั้งสอง slot — bandwidth ไม่เป็น bottleneck
- Kokkoro จะดีขึ้นหลัง swap เพิ่ม 3060 x3 (36GB) จาก Ayanami

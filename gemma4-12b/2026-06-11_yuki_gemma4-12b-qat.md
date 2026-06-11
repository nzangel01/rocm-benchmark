# Gemma 4 12B QAT — Yuki (2026-06-11)

**Model:** gemma4:12b-it-qat (QAT Q4_0, 6.48 GiB)
**Node:** yuki (192.168.1.6)
**GPU:** AMD RX 7900 XTX 24GB
**Ollama:** 0.30.7 (upgraded from 0.30.4)
**Backend:** ROCm 7.2
**GPU Power Cap:** 291W

## Results

| metric | value |
|---|---|
| **tg (eval rate)** | **53.67 tok/s** |

## Notes

- Root partition 100% full → OLLAMA_MODELS ย้ายไป /mnt/ai_beast (3TB) แล้ว /dev/shm (RAM) สำหรับ benchmark
- ROCm 7.2 active, CUDA QAT optimization ยังดีกว่า ROCm → gap กับ 3090 เป็นเรื่อง driver
- ดีกว่า fleet benchmark เดิม (49.5 tok/s) หลังอัป Ollama 0.30.7

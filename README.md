# ROCm / CUDA GPU LLM Benchmark

Homelab GPU fleet benchmark results — LLM inference (tok/s), VRAM usage, context length.

## Fleet

| Node | IP | GPU | VRAM | Backend |
|---|---|---|---|---|
| yuki | .6 | AMD RX 7900 XTX | 24GB | ROCm |
| kokkoro | .5 | RTX 3060 x2 | 12GB x2 | CUDA |
| silvia | .24 | Intel Arc B580 | 12GB | SYCL/Vulkan |
| sheffy | .7 | RTX 2080 Ti | 11GB | CUDA |
| kumo | .10 | Tesla P4 x3 | 8GB x3 | CUDA |

## Results

See subdirectories per model:
- `gemma4-12b/` — Gemma 4 12B QAT Q4_0

## Format

Each result file: `YYYY-MM-DD_<node>_<model>.md`

# PiD Upscale 관련 모델 다운로드 가이드

아래 스크립트 전체를 복사하여 RunPod 터미널에 붙여넣으면
PixelDiT 모델, Gemma 텍스트 인코더, 그리고 FLUX VAE 모델까지 모두 `hf` 명령어로 한 번에 다운로드됩니다.

---

## 다운로드 스크립트

```bash
# 1. PixelDiT 본체 확산 모델 (DiT)
hf download Comfy-Org/PixelDiT \
  --include "pixeldit_1300m_1024px_bf16.safetensors" \
  --local-dir /workspace/runpod-slim/ComfyUI/models/diffusion_models && \

# 2. Gemma 텍스트 인코더
hf download Comfy-Org/PixelDiT \
  --include "gemma_2_2b_it_elm_bf16.safetensors" \
  --local-dir /workspace/runpod-slim/ComfyUI/models/text_encoders && \

# 3. FLUX.1-dev VAE (ae.safetensors)
hf download black-forest-labs/FLUX.1-dev \
  --include "ae.safetensors" \
  --local-dir /workspace/runpod-slim/ComfyUI/models/vae

echo "=========================================="
echo " PiD Upscale 관련 모델 다운로드 완료! "
echo "=========================================="
```

---

## 💻 하드웨어 권장 사양

PiD Upscale 워크플로우 실행 시 선택하는 정밀도 옵션에 따른 VRAM 소모량 가이드입니다.

| 모드 | 정밀도 | 소요 VRAM |
|------|--------|-----------|
| 기본 모드 | bf16 | 10–14 GB |
| 경량 모드 | mxfp8 | 8–12 GB |

---

## 📦 다운로드 모델 목록

| 파일명 | 저장 경로 |
|--------|-----------|
| `pixeldit_1300m_1024px_bf16.safetensors` | `models/diffusion_models/` |
| `gemma_2_2b_it_elm_bf16.safetensors` | `models/text_encoders/` |
| `ae.safetensors` (FLUX.1-dev VAE) | `models/vae/` |

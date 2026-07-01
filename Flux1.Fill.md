# Flux.1 Fill Dev (Inpaint) 모델 다운로드 가이드

이 문서는 ComfyUI에서 **Flux.1 Fill Dev** 모델을 활용한 인페인팅(Inpainting) 및 이미지 채우기(Fill) 워크플로우를 구동하기 위해 필요한 필수 모델들의 다운로드 명령어를 정리한 가이드입니다.

---

## ① Diffusion Model (인페인트 전용 메인 모델)

`Comfy-Org/flux1-dev` 저장소의 하위 경로에 있는 인페인트/필 특화 디퓨전 모델을 다운로드합니다.

hf download Comfy-Org/flux1-dev --include "split_files/diffusion_models/flux1-fill-dev.safetensors" --local-dir /workspace/runpod-slim/ComfyUI/models/diffusion_models

## ② Text Encoders (프롬프트 인식 엔진)

# CLIP-L 다운로드
hf download comfyanonymous/flux_text_encoders --include "clip_l.safetensors" --local-dir /workspace/runpod-slim/ComfyUI/models/text_encoders

# T5XXL 다운로드
hf download comfyanonymous/flux_text_encoders --include "t5xxl_fp16.safetensors" --local-dir /workspace/runpod-slim/ComfyUI/models/text_encoders

## ③ VAE (이미지 복원 오토인코더)

hf download Comfy-Org/Lumina_Image_2.0_Repackaged --include "split_files/vae/ae.safetensors" --local-dir /workspace/runpod-slim/ComfyUI/models/vae

## 4 로라들
# Big Butt FLUX
# https://civitai.com/api/download/models/727590?fileId=642137
wget --content-disposition "https://civitai.com/api/download/models/727590?fileId=642137&token=${CIVITAI_TOKEN}" -O /workspace/runpod-slim/ComfyUI/models/loras/big_butt_flux.safetensors

# Ahegao FLUX
# https://civitai.com/api/download/models/1477302?fileId=1378682
# trigger words : Ahegao, Ahegao face, Tongue out, Cross eyed, 4h3g40
wget --content-disposition "https://civitai.com/api/download/models/1477302?fileId=1378682&token=${CIVITAI_TOKEN}" -O /workspace/runpod-slim/ComfyUI/models/loras/ahegao_flux.safetensors

# Better Butt
# https://civitai.com/api/download/models/2408383?fileId=2299166
# Weight: 0.4 - 0.6 (start with 0.45)
# Trigger words: butt
# Tips : lora:butt:0.45 nice butt, wider hips, round butt, curvy lower body
wget --content-disposition "https://civitai.com/api/download/models/2408383?fileId=2299166&token=${CIVITAI_TOKEN}" -O /workspace/runpod-slim/ComfyUI/models/loras/better_butt_flux.safetensors

# Skinny Waist Huge Butt
# https://civitai.com/api/download/models/981486?fileId=887567
# skinny, skinny waist, very skinny waist, large butt, bubble butt, huge butt, very large butt, The image is a high-resolution photograph, This image is a highly realistic, computer-generated CGI rendering
wget --content-disposition "https://civitai.com/api/download/models/981486?fileId=887567&token=${CIVITAI_TOKEN}" -O /workspace/runpod-slim/ComfyUI/models/loras/skinny_waist_huge_butt_flux.safetensors

# large nipples for FLUX
# https://civitai.com/api/download/models/957540?fileId=866293
# Trigger : long nipples, large nipples
wget --content-disposition "https://civitai.com/api/download/models/957540?fileId=866293&token=${CIVITAI_TOKEN}" -O /workspace/runpod-slim/ComfyUI/models/loras/large_nipples_flux.safetensors

# FLUX Nipples fix and Different breast size
# https://civitai.com/api/download/models/1095735?fileId=1001391
# Setting : 0.6 ~ 0.9
# Trigger : Small breasts, Medium breasts, Big breasts
wget --content-disposition "https://civitai.com/api/download/models/1095735?fileId=1001391&token=${CIVITAI_TOKEN}" -O /workspace/runpod-slim/ComfyUI/models/loras/nipples_fix_breast_size_flux.safetensors

# Flux - Ultra-Realistic Nipple Generator
# https://civitai.com/api/download/models/1112151?fileId=1016973
wget --content-disposition "https://civitai.com/api/download/models/1112151?fileId=1016973&token=${CIVITAI_TOKEN}" -O /workspace/runpod-slim/ComfyUI/models/loras/ultra_realistic_nipple_flux.safetensors

---

## 💡 실행 및 마스크(Mask) 설정 팁

### ComfyUI 최신 버전 유지

본 워크플로우는 내부 노드들을 하나로 묶어 정리한 서브그래프(Subgraph) 형태(`Flux.1 Fill Dev Image Inpainting`)를 사용하므로, 에러 방지를 위해 실행 전 ComfyUI Manager를 통해 전체 패키지를 최신 버전으로 업데이트하는 것을 권장합니다.

### 인페인트 마스크 칠하는 방법

1. `LoadImage` 노드에 원본 이미지를 업로드한 후, 이미지 위에서 마우스 우클릭 → `Open in MaskEditor`를 실행합니다.
2. 팝업 창이 뜨면 수정하고 싶은 영역(옷, 배경, 오브젝트 등)을 마우스로 슥슥 색칠한 뒤 우측 하단 `Save to node`를 누르면 마스크 설정이 완료됩니다. (마우스 휠로 브러시 크기 조절 가능)

### 프롬프트 입력

마스크를 칠한 뒤 `CLIPTextEncode` (Positive Prompt) 노드 창에 새로 채워 넣고 싶은 내용을 영문으로 입력하고 `Queue Prompt`를 실행하면 주변 맥락과 어우러지는 완벽한 인페인팅 결과물이 생성됩니다.


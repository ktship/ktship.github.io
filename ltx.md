# LTX-Video 2.3 & Sulphur 환경 구축 스크립트

RunPod 워크스페이스(`/workspace`) 환경에서 ComfyUI 내부 폴더에 직접 모델, 로라, 커스텀 노드를 일괄 경로 지정 방식으로 다운로드하고 설치하는 커맨드 가이드.

---

## 0. 사전 준비

```bash
# ComfyUI 심볼릭 링크 생성 (RunPod 환경)
ln -s /workspace/runpod-slim/ComfyUI/ /workspace/ComfyUI

# huggingface_hub 설치 (hf 명령어가 없는 경우)
pip install -U huggingface_hub
hf version  # hf 명령어 확인
```

---

## 1. 메인 모델 (Checkpoints) 다운로드

사용 환경의 VRAM 사양에 맞춰 아래 명령어를 선택하여 터미널에 입력하세요.

```bash
# 옵션 A (압축 안 한 순정 bf16, VRAM 32G 이상 권장)
hf download vantagewithai/Sulphur-2-Base-Split \
  model/sulphur_dev_bf16_model.safetensors \
  --local-dir /workspace/ComfyUI/models/checkpoints

# 옵션 B (FP8 경량화 버전, VRAM 16G~24G 환경용 — Sulphur 베이스)
hf download SulphurAI/Sulphur-2-base \
  ltx-2.3-22b-dev-fp8.safetensors \
  --local-dir /workspace/ComfyUI/models/checkpoints

# 옵션 C (FP8 경량화 버전, VRAM 16G~24G 환경용 — Lightricks 공식)
hf download Lightricks/LTX-2.3-fp8 \
  ltx-2.3-22b-dev-fp8.safetensors \
  --local-dir /workspace/ComfyUI/models/checkpoints
```

---

## 2. 필수 로라 (LoRA) 다운로드

워크플로우 구동 및 증류(Distilled) 가속을 위해 필수적인 로라를 로라 절대 경로(`/workspace/ComfyUI/models/loras/`)로 다운로드합니다.

```bash
# Distilled 가속 로라 (Sulphur용)
hf download SulphurAI/Sulphur-2-base \
  distill_loras/ltx-2.3-22b-distilled-lora-1.1_fro90_ceil72_condsafe.safetensors \
  --local-dir /workspace/ComfyUI/models/loras

# Distilled 가속 로라 384 v1.1 (워크플로우 기본 사용)
hf download Lightricks/LTX-2.3 \
  ltx-2.3-22b-distilled-lora-384-1.1.safetensors \
  --local-dir /workspace/ComfyUI/models/loras

# Sulphur 메인 로라
hf download SulphurAI/Sulphur-2-base \
  sulphur_lora_rank_768.safetensors \
  --local-dir /workspace/ComfyUI/models/loras
```

---

## 3. 커스텀 노드 일괄 설치 스크립트

비디오 제어 및 대형 텍스트 인코더 연산, 세이지 어텐션 패치 등 워크플로우에 필요한 모든 외부 커스텀 노드를 일괄 다운로드하고 종속 패키지를 설치합니다.

```bash
# 1. 올바른 ComfyUI 커스텀 노드 디렉토리로 이동
cd /workspace/runpod-slim/ComfyUI/custom_nodes && \

# 2. 커스텀 노드 레포지토리 클론 (이미 존재하면 건너뜀)
[ -d "ComfyUI-VideoHelperSuite" ] || git clone https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite.git && \
[ -d "ComfyUI-KJNodes" ] || git clone https://github.com/Kijai/ComfyUI-KJNodes.git && \
[ -d "ComfyUI-Custom-Scripts" ] || git clone https://github.com/pythongosssss/ComfyUI-Custom-Scripts.git && \
[ -d "comfyui-ltxvideo-native" ] || git clone https://github.com/Lightricks/LTX-Video.git comfyui-ltxvideo-native && \
[ -d "rgthree-comfy" ] || git clone https://github.com/rgthree/rgthree-comfy.git && \

# 3. 의존성 기본 패키지 설치
pip install wheel setuptools --upgrade && \
pip install -r ComfyUI-VideoHelperSuite/requirements.txt && \
pip install -r ComfyUI-KJNodes/requirements.txt && \

# 4. 중요: 3090 비디오 가속을 위한 SageAttention 및 필수 패키지 직접 설치
# (이 단계에서 컴파일 때문에 1~3분 정도 시간이 걸릴 수 있습니다)
pip install sageattention triton --no-cache-dir

echo "=========================================="
echo " 모든 커스텀 노드 및 의존성 설치가 완료되었습니다! "
echo " 안심하고 ComfyUI 서버를 재시작해 주세요.   "
echo "=========================================="
```

---

## 4. 공간 업스케일러 (Spatial Upscaler) 다운로드

저해상도 비디오를 화질 저하 없이 1080p, 4K 등 영화급 화질로 업스케일하는 전용 모델입니다.

```bash
# 2배 업스케일러 v1.1
hf download Lightricks/LTX-2.3 \
  ltx-2.3-spatial-upscaler-x2-1.1.safetensors \
  --local-dir /workspace/ComfyUI/models/latent_upscale_models

```

---

## 5. 텍스트 인코더 & VAE 다운로드

```bash
# 텍스트 인코더 (Gemma3 기반)
hf download Pavpif/ltx2-gemma3-text-encoder \
  model_gemma_3_12B_it_fp8_e4m3fn.safetensors \
  --local-dir /workspace/ComfyUI/models/text_encoders

# (옵션) 비디오 VAE — 워크플로우에 따라 별도로 필요할 수 있음
hf download Kijai/LTX2.3_comfy \
  vae/taeltx2_3.safetensors \
  --local-dir /workspace/ComfyUI/models/vae

# (옵션) 오디오 VAE — 워크플로우에 따라 별도로 필요할 수 있음
hf download Kijai/LTX2.3_comfy \
  vae/LTX23_audio_vae_bf16.safetensors \
  --local-dir /workspace/ComfyUI/models/vae
```
# LTX-Video 2.3 & Sulphur 환경 구축 스크립트

RunPod, Vast.ai 등 워크스페이스(`/workspace`) 환경에서 ComfyUI 내부 폴더에 직접 모델, 로라, 커스텀 노드를 일괄 경로 지정 방식으로 다운로드하고 설치하는 커맨드 가이드입니다.

---

## 1. Sulphur 주 모델 (Checkpoints) 다운로드

사용 환경의 VRAM 사양에 맞춰 절대 경로 옵션이 지정된 wget 명령어를 선택하여 터미널에 입력하세요.

```bash
# 옵션 A (압축 안 한 순정 bf16, VRAM 32G 이상 권장)
wget -c -O /workspace/ComfyUI/models/checkpoints/sulphur_dev_bf16_model.safetensors "https://huggingface.co/vantagewithai/Sulphur-2-Base-Split/resolve/main/model/sulphur_dev_bf16_model.safetensors"

# 옵션 B (FP8 경량화 버전, VRAM 16G~24G 환경용)
wget -c -O /workspace/ComfyUI/models/checkpoints/ltx-2.3-22b-dev-fp8.safetensors "https://huggingface.co/SulphurAI/Sulphur-2-base/resolve/main/ltx-2.3-22b-dev-fp8.safetensors?download=true"
```

---

## 2. 필수 로라 (LoRA) 다운로드

워크플로우 구동 및 증류(Distilled) 가속을 위해 필수적인 로라 2종을 로라 절대 경로(`/workspace/ComfyUI/models/loras/`)로 다운로드합니다.

```bash
wget -O /workspace/ComfyUI/models/loras/ltx-2.3-22b-distilled-lora-1.1_fro90_ceil72_condsafe.safetensors "https://huggingface.co/SulphurAI/Sulphur-2-base/resolve/main/distill_loras/ltx-2.3-22b-distilled-lora-1.1_fro90_ceil72_condsafe.safetensors?download=true" && \
wget -O /workspace/ComfyUI/models/loras/sulphur_lora_rank_768.safetensors "https://huggingface.co/SulphurAI/Sulphur-2-base/resolve/main/sulphur_lora_rank_768.safetensors?download=true"
```

---

## 3. 커스텀 노드 일괄 설치 스크립트

비디오 제어 및 대형 텍스트 인코더 연산, 세이지 어텐션 패치 등 워크플로우에 필요한 모든 외부 커스텀 노드를 일괄 다운로드하고 종속 패키지를 설치합니다.

```bash
# ComfyUI 커스텀 노드 디렉토리로 이동
cd /workspace/ComfyUI/custom_nodes && \

# 1. 비디오 생성 및 저장 관련 노드 (CreateVideo, SaveVideo)
git clone https://github.com/Kosinkadink/ComfyUI-VideoHelperSuite.git && \

# 2. Sage Attention 패치 노드 (PatchSageAttentionKJ)
git clone https://github.com/Kijai/ComfyUI-KJNodes.git && \

# 3. 수식 연산 노드 (ComfyMathExpression)
git clone https://github.com/pythongosssss/ComfyUI-Custom-Scripts.git && \

# 4. LTX-Video 2.3 오디오 통합 전용 노드 스위트
git clone https://github.com/Lightricks/LTX-Video.git comfyui-ltxvideo-native && \

# 의존성 패키지 설치
pip install -r ComfyUI-VideoHelperSuite/requirements.txt && \
pip install -r ComfyUI-KJNodes/requirements.txt

echo "=========================================="
echo " 모든 커스텀 노드 다운로드가 완료되었습니다! "
echo " ComfyUI 서버를 재시작해 주세요.           "
echo "=========================================="
```

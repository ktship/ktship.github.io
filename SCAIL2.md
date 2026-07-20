# SCAIL-2 환경 구축 스크립트


## 0. 사전 준비

# ComfyUI 심볼릭 링크 생성 (RunPod 환경)
ln -s /workspace/runpod-slim/ComfyUI/ /workspace/ComfyUI

# huggingface_hub 설치 (hf 명령어가 없는 경우)
pip install -U huggingface_hub
hf update
pip install sageattention

---

# 1. 메인 디퓨전 모델 (SCAIL-2)
hf download Comfy-Org/SCAIL-2 diffusion_models/wan2.1_14B_SCAIL_2_fp16.safetensors --local-dir /workspace/ComfyUI/models/diffusion_models

# 2. 가속용 Distill LoRA (Lightx2v)
hf download data0x/lightx2v_I2V_14B_480p_cfg_step_distill_rank64_bf16 lightx2v_I2V_14B_480p_cfg_step_distill_rank64_bf16.safetensors --local-dir /workspace/ComfyUI/models/loras

# 3. 화질 개선용 DPO LoRA (SCAIL-2)
hf download Comfy-Org/SCAIL-2 loras/wan2.1_SCAIL_2_DPO_lora_bf16.safetensors --local-dir /workspace/ComfyUI/models/loras

# 4. 캐릭터 구역 지정 마스크 모델 (SAM 3.1)
hf download Comfy-Org/sam3.1 checkpoints/sam3.1_multiplex_fp16.safetensors --local-dir /workspace/ComfyUI/models/checkpoints

# 5. 프롬프트 해석 텍스트 인코더 (T5 XXL)
hf download Comfy-Org/Wan_2.1_ComfyUI_repackaged split_files/text_encoders/umt5_xxl_fp16.safetensors --local-dir /workspace/ComfyUI/models/text_encoders

# 6. 이미지 특징 추출 인코더 (CLIP Vision)
hf download Comfy-Org/Wan_2.1_ComfyUI_repackaged split_files/clip_vision/clip_vision_h.safetensors --local-dir /workspace/ComfyUI/models/clip_vision

# 7. 비디오 복원 모델 (Wan 2.1 VAE)
hf download Comfy-Org/Wan_2.1_ComfyUI_repackaged split_files/vae/wan_2.1_vae.safetensors --local-dir /workspace/ComfyUI/models/vae

---
## Output 내용 다운로드
tar -czvf output_all.tar.gz /workspace/ComfyUI/output
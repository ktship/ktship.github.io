# Qwen-2511 Edit 워크플로우 설정 가이드

## 1. 체크포인트 모델
# 메인 확산 모델 (FP8 Mixed)
hf download Comfy-Org/Qwen-Image-Edit_ComfyUI \
  split_files/diffusion_models/qwen_image_edit_2511_fp8mixed.safetensors \
  --local-dir /workspace/ComfyUI/models/diffusion_models

# 라이트닝 LoRA (4스텝 고속 생성)
hf download lightx2v/Qwen-Image-Edit-2511-Lightning \
  Qwen-Image-Edit-2511-Lightning-4steps-V1.0-fp32.safetensors \
  --local-dir /workspace/ComfyUI/models/loras
# 라이트닝 LoRA (8스텝)
hf download lightx2v/Qwen-Image-Edit-2511-Lightning \
  Qwen-Image-Edit-2511-Lightning-8steps-V1.0-fp32.safetensors \
  --local-dir /workspace/ComfyUI/models/loras

# 텍스트 인코더 (Qwen 2.5 VL 7B FP8)
hf download Comfy-Org/Qwen-Image_ComfyUI \
  split_files/text_encoders/qwen_2.5_vl_7b_fp8_scaled.safetensors \
  --local-dir /workspace/ComfyUI/models/text_encoders

# 텍스트 인코더 (Abliterated)
hf download ussoewwin/Qwen2.5-VL-7B-Instruct-abliterated --include "Qwen2.5-VL-7B-Instruct-abliterated_converted.safetensors" --local-dir /workspace/runpod-slim/ComfyUI/models/text_encoders

# VAE
hf download Comfy-Org/Qwen-Image_ComfyUI \
  split_files/vae/qwen_image_vae.safetensors \
  --local-dir /workspace/ComfyUI/models/vae

# 다각도 뷰 
hf download fal/Qwen-Image-Edit-2511-Multiple-Angles-LoRA \
  qwen-image-edit-2511-multiple-angles-lora.safetensors \
  --local-dir /workspace/ComfyUI/models/loras

---

## 2. LoRA
# Qwen-image_NSFW_Adv1
wget -O /workspace/runpod-slim/ComfyUI/models/loras/Qwen-image_NSFW_Adv1.safetensors "https://civitai.red/api/download/models/2328988?fileId=2219270&token=your_key"
# Sampler: res_2s and Scheduler: bong_tangent

# QW_BreastEnhancer
wget -O "/workspace/ComfyUI/models/loras/QW_BreastEnhancer.safetensors" "https://civitai.red/api/download/models/2305397?fileId=2196123&token=your_key"
# tiny/small/medium/large sized areoles   
# tiny/small/medium/large sized breasts  
# pale/brown/dark/ghost areoles   
# hard/erect nipples  


# QW_ButtSlider
wget -O "/workspace/ComfyUI/models/loras/QW_ButtSlider.safetensors" "https://civitai.red/api/download/models/2260485?fileId=2152693&token=your_key"
# -2.0(Flat) ~ 2.0(Large))

# QW_BreastBigger
wget -O "/workspace/ComfyUI/models/loras/QW_BreastBigger.safetensors" "https://civitai.red/api/download/models/2567144?fileId=2455406&token=your_key"
# Make your breasts bigger , Lora weight: 1.5-1.2

# QW_Ahegao
wget -O "/workspace/ComfyUI/models/loras/QW_Ahegao.safetensors" "https://civitai.red/api/download/models/2209972?fileId=2102957&token=your_key"
# making an ahegao face, making an ahegao face with tongue out, making an ahegao face with drool

# Spit_on_Qwen
wget -O "/workspace/ComfyUI/models/loras/Spit_on_Qwen.safetensors" "https://civitai.red/api/download/models/2205295?fileId=2098226&token=your_key"
# spitting

# QW_Ani2Real 다운로드
wget -O /workspace/runpod-slim/ComfyUI/models/loras/QW_Ani2Real.safetensors "https://civitai.red/api/download/models/2653480?fileId=2541320&token=your_key"

# QW_MCNL 다운로드
wget -O /workspace/runpod-slim/ComfyUI/models/loras/QW_MCNL.safetensors "https://civitai.red/api/download/models/2105899?fileId=2000660&token=your_key"
Trigger words: nsfw, cum_on_face, blowjob, cowgirlout, creamp1e, penis, l1ck, missionary, nipples, reversecowgirlpov, vagina


echo "=========================================="
echo " 런포드 환경에 추가 LoRA 다운로드 완료! "
echo "=========================================="

---

## 3. 커스텀 노드
## 4. 워크플로우
ktship_image_qwen_image_edit_2511.json
ktship_Qwen2511Multiple-Angles.json
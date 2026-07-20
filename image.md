# Qwen-2511 Edit 워크플로우 설정 가이드

---

## 0. Pre

export HF_TOKEN="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"
export CIVITAI_TOKEN="8dfbdadb6eb4a3336e4ffcb75697fd07"

# ComfyUI 심볼릭 링크 생성 (RunPod 환경)
ln -s /workspace/runpod-slim/ComfyUI/ /workspace/ComfyUI

# huggingface_hub 설치 (hf 명령어가 없는 경우)
pip install -U huggingface_hub
hf update
pip install sageattention

# AIO임. 이거 하나면 일단 Qwen Image Edit 됨. 아래는 lora를 써야할때
hf download Phr00t/Qwen-Image-Edit-Rapid-AIO v23/Qwen-Rapid-AIO-NSFW-v23.safetensors --local-dir /workspace/ComfyUI/models/checkpoints/Qwen

# hf download Phr00t/Qwen-Image-Edit-Rapid-AIO v23/Qwen-Rapid-AIO-NSFW-v23.safetensors --local-dir /workspace/ComfyUI/models/checkpoints/

---

## 1. 체크포인트 모델

# 메인 확산 모델 (FP8 Mixed)
hf download Comfy-Org/Qwen-Image-Edit_ComfyUI split_files/diffusion_models/qwen_image_edit_2511_fp8mixed.safetensors --local-dir /workspace/ComfyUI/models/diffusion_models

# 라이트닝 LoRA (4스텝 고속 생성)
hf download lightx2v/Qwen-Image-Edit-2511-Lightning Qwen-Image-Edit-2511-Lightning-4steps-V1.0-fp32.safetensors --local-dir /workspace/ComfyUI/models/loras
# 라이트닝 LoRA (8스텝)
# hf download lightx2v/Qwen-Image-Edit-2511-Lightning Qwen-Image-Edit-2511-Lightning-8steps-V1.0-fp32.safetensors --local-dir /workspace/ComfyUI/models/loras

# 텍스트 인코더 (Qwen 2.5 VL 7B FP8)
# hf download Comfy-Org/Qwen-Image_ComfyUI split_files/text_encoders/qwen_2.5_vl_7b_fp8_scaled.safetensors --local-dir /workspace/ComfyUI/models/text_encoders

# 텍스트 인코더 (Abliterated)
hf download ussoewwin/Qwen2.5-VL-7B-Instruct-abliterated --include "Qwen2.5-VL-7B-Instruct-abliterated_converted.safetensors" --local-dir /workspace/runpod-slim/ComfyUI/models/text_encoders

# VAE
hf download Comfy-Org/Qwen-Image_ComfyUI split_files/vae/qwen_image_vae.safetensors --local-dir /workspace/ComfyUI/models/vae

# 다각도 뷰 
hf download fal/Qwen-Image-Edit-2511-Multiple-Angles-LoRA qwen-image-edit-2511-multiple-angles-lora.safetensors --local-dir /workspace/ComfyUI/models/loras

---

## 2. LoRA
# Qwen-image_NSFW_Adv1
wget -O /workspace/runpod-slim/ComfyUI/models/loras/Qwen-image_NSFW_Adv1.safetensors "https://civitai.red/api/download/models/2328988?fileId=2219270&token=${CIVITAI_TOKEN}"
# Sampler: res_2s and Scheduler: bong_tangent

# realistic_nipples
wget -O "/workspace/ComfyUI/models/loras/realistic_nipples.safetensors" "https://civitai.red/api/download/models/2392385?fileId=2282541&token=${CIVITAI_TOKEN}"
# Trigger : ALN

# QW_BreastEnhancer
wget -O "/workspace/ComfyUI/models/loras/QW_BreastEnhancer.safetensors" "https://civitai.red/api/download/models/2305397?fileId=2196123&token=${CIVITAI_TOKEN}"
# tiny/small/medium/large sized areoles   
# tiny/small/medium/large sized breasts  
# pale/brown/dark/ghost areoles   
# hard/erect nipples  


# QW_ButtSlider
wget -O "/workspace/ComfyUI/models/loras/QW_ButtSlider.safetensors" "https://civitai.red/api/download/models/2260485?fileId=2152693&token=${CIVITAI_TOKEN}"
# -2.0(Flat) ~ 2.0(Large))

# QW_BreastBigger
wget -O "/workspace/ComfyUI/models/loras/QW_BreastBigger.safetensors" "https://civitai.red/api/download/models/2567144?fileId=2455406&token=${CIVITAI_TOKEN}"
# Make your breasts bigger , Lora weight: 1.5-1.2

# QW_Ahegao
wget -O "/workspace/ComfyUI/models/loras/QW_Ahegao.safetensors" "https://civitai.red/api/download/models/2209972?fileId=2102957&token=${CIVITAI_TOKEN}"
# making an ahegao face, making an ahegao face with tongue out, making an ahegao face with drool

# Spit_on_Qwen
wget -O "/workspace/ComfyUI/models/loras/Spit_on_Qwen.safetensors" "https://civitai.red/api/download/models/2205295?fileId=2098226&token=${CIVITAI_TOKEN}"
# spitting
# Distilled 가속 로라 (Sulphur용)
hf download SulphurAI/Sulphur-2-base distill_loras/ltx-2.3-22b-distilled-lora-1.1_fro90_ceil72_condsafe.safetensors --local-dir /workspace/ComfyUI/models/loras

# Distilled 가속 로라 384 v1.1 (워크플로우 기본 사용)
# hf download Lightricks/LTX-2.3 ltx-2.3-22b-distilled-lora-384-1.1.safetensors --local-dir /workspace/ComfyUI/models/loras

# Sulphur 메인 로라
hf download SulphurAI/Sulphur-2-base sulphur_lora_rank_768.safetensors --local-dir /workspace/ComfyUI/models/loras

---

## 3. 공간 업스케일러 (Spatial Upscaler) 다운로드
# 저해상도 비디오를 화질 저하 없이 1080p, 4K 등 영화급 화질로 업스케일하는 전용 모델입니다.
# 2배 업스케일러 v1.1
hf download Lightricks/LTX-2.3 ltx-2.3-spatial-upscaler-x2-1.1.safetensors --local-dir /workspace/ComfyUI/models/latent_upscale_models

---

## 4. 텍스트 인코더 & VAE 다운로드
# 텍스트 인코더 (Gemma3 기반)
# hf download Pavpif/ltx2-gemma3-text-encoder model_gemma_3_12B_it_fp8_e4m3fn.safetensors --local-dir /workspace/ComfyUI/models/text_encoders

hf download Sikaworld1990/gemma-3-12b-it-abliterated-sikaworld-high-fidelity-edition-Ltx-2 gemma-3-12b-it-abliterated-sikaworld-high-fidelity-edition.safetensors --local-dir /workspace/ComfyUI/models/text_encoders

  
# (옵션) 비디오 VAE — 워크플로우에 따라 별도로 필요할 수 있음
hf download Kijai/LTX2.3_comfy \
  vae/taeltx2_3.safetensors \
  --local-dir /workspace/ComfyUI/models/vae

# (옵션) 오디오 VAE — 워크플로우에 따라 별도로 필요할 수 있음
hf download Kijai/LTX2.3_comfy \
  vae/LTX23_audio_vae_bf16.safetensors \
  --local-dir /workspace/ComfyUI/models/vae

---

# QW_Ani2Real 다운로드
wget -O /workspace/runpod-slim/ComfyUI/models/loras/QW_Ani2Real.safetensors "https://civitai.red/api/download/models/2653480?fileId=2541320&token=${CIVITAI_TOKEN}"

# QW_MCNL 다운로드
wget -O /workspace/runpod-slim/ComfyUI/models/loras/QW_MCNL.safetensors "https://civitai.red/api/download/models/2105899?fileId=2000660&token=${CIVITAI_TOKEN}"
Trigger words: nsfw, cum_on_face, blowjob, cowgirlout, creamp1e, penis, l1ck, missionary, nipples, reversecowgirlpov, vagina


echo "=========================================="
echo " 런포드 환경에 추가 LoRA 다운로드 완료! "
echo "=========================================="

---

## 3. 커스텀 노드 (ktship_Qwen2511Multiple-Angles용)
cd /workspace/runpod-slim/ComfyUI/custom_nodes

# Git Clone
git clone https://github.com/chflame163/ComfyUI_LayerStyle.git
git clone https://github.com/yolain/ComfyUI-Easy-Use.git
git clone https://github.com/jtydhr88/ComfyUI-qwenmultiangle.git

# 파이썬 종속성 라이브러리 및 허깅페이스 CLI 설치
pip install -r ComfyUI-Easy-Use/requirements.txt
pip install -r ComfyUI_LayerStyle/requirements.txt

## 4. 워크플로우
ktship_NSFW_AIO_260703.json
ktship_image_qwen_image_edit_2511.json
ktship_Qwen2511Multiple-Angles.json

## Output 내용 다운로드
tar -czvf output_all.tar.gz /workspace/ComfyUI/output
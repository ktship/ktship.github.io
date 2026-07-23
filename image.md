# Qwen-2511 Edit 워크플로우 설정 가이드

---

## 0. Pre

# ComfyUI 심볼릭 링크 생성 (RunPod 환경)
ln -s /workspace/runpod-slim/ComfyUI/ /workspace/ComfyUI

# huggingface_hub 설치 (hf 명령어가 없는 경우)
pip install -U huggingface_hub
hf update
pip install sageattention
pip uninstall -y kornia
# 안되면 6.12
pip install kornia==0.7.3


cd /workspace

# AIO임. 이거 하나면 일단 Qwen Image Edit 됨. 아래는 lora를 써야할때
hf download Phr00t/Qwen-Image-Edit-Rapid-AIO v23/Qwen-Rapid-AIO-NSFW-v23.safetensors --local-dir /workspace/ComfyUI/models/checkpoints/Qwen
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

# Qwen-consistence_edit_v2
hf download JackieZaaa/Qwen-consistence_edit_v2.safetensors Qwen-consistence_edit_v2.safetensors --local-dir /workspace/runpod-slim/ComfyUI/models/loras
---

## 2. LoRA
# Qwen-image_NSFW_Adv1
wget -O /workspace/runpod-slim/ComfyUI/models/loras/Qwen-image_NSFW_Adv1.safetensors "https://civitai.red/api/download/models/2328988?fileId=2219270&token=${CIVITAI_TOKEN}"
# Sampler: res_2s and Scheduler: bong_tangent

# ALN, realistic_nipples
wget -O "/workspace/ComfyUI/models/loras/realistic_nipples.safetensors" "https://civitai.red/api/download/models/2392385?fileId=2282541&token=${CIVITAI_TOKEN}"
# Trigger : ALN

# nipple ring, Nipple_Ring_Piercing_1.0
wget -O "/workspace/ComfyUI/models/loras/Nipple_Ring_Piercing_1.0.safetensors" "https://civitai.red/api/download/models/2297084?fileId=2188030&token=${CIVITAI_TOKEN}"

# QW_BreastEnhancer
wget -O "/workspace/ComfyUI/models/loras/QW_BreastEnhancer.safetensors" "https://civitai.red/api/download/models/2305397?fileId=2196123&token=${CIVITAI_TOKEN}"
# tiny/small/medium/large sized areoles   
# tiny/small/medium/large sized breasts  
# pale/brown/dark/ghost areoles   
# hard/erect nipples  

# bigsloppytits (0.85)
wget -O "/workspace/ComfyUI/models/loras/bigsloppytits.safetensors" "https://civitai.red/api/download/models/2943581?fileId=2822775&token=${CIVITAI_TOKEN}"

# QW_ButtSlider
wget -O "/workspace/ComfyUI/models/loras/QW_ButtSlider.safetensors" "https://civitai.red/api/download/models/2260485?fileId=2152693&token=${CIVITAI_TOKEN}"
# -2.0(Flat) ~ 2.0(Large))

# Make your breasts bigger(1.5-1.2), QW_BreastBigger
wget -O "/workspace/ComfyUI/models/loras/QW_BreastBigger.safetensors" "https://civitai.red/api/download/models/2567144?fileId=2455406&token=${CIVITAI_TOKEN}"
# Make your breasts bigger , Lora weight: 1.5-1.2

# QW_Ahegao
wget -O "/workspace/ComfyUI/models/loras/QW_Ahegao.safetensors" "https://civitai.red/api/download/models/2209972?fileId=2102957&token=${CIVITAI_TOKEN}"
# making an ahegao face, making an ahegao face with tongue out, making an ahegao face with drool

# Spit_on_Qwen
wget -O "/workspace/ComfyUI/models/loras/Spit_on_Qwen.safetensors" "https://civitai.red/api/download/models/2205295?fileId=2098226&token=${CIVITAI_TOKEN}"
# spitting

# QW_Ani2Real 다운로드
wget -O /workspace/runpod-slim/ComfyUI/models/loras/QW_Ani2Real.safetensors "https://civitai.red/api/download/models/2653480?fileId=2541320&token=${CIVITAI_TOKEN}"

# QW_MCNL 다운로드
wget -O /workspace/runpod-slim/ComfyUI/models/loras/QW_MCNL.safetensors "https://civitai.red/api/download/models/2105899?fileId=2000660&token=${CIVITAI_TOKEN}"
# Trigger words: nsfw, cum_on_face, blowjob, cowgirlout, creamp1e, penis, l1ck, missionary, nipples, reversecowgirlpov, vagina


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

git clone https://github.com/Suzie1/ComfyUI_Comfyroll_CustomNodes.git
git clone https://github.com/cubiq/ComfyUI_essentials.git
git clone https://github.com/1038lab/ComfyUI-RMBG.git
git clone https://github.com/pythongosssss/ComfyUI-Custom-Scripts.git
git clone https://github.com/Kijai/ComfyUI-KJNodes.git
git clone https://github.com/omar92/ComfyUI-QualityOfLifeSuit_Omar92.git
git clone https://github.com/rgthree/rgthree-comfy.git
git clone https://github.com/chflame163/ComfyUI_LayerStyle.git

for d in */; do [ -f "${d}requirements.txt" ] && echo "Installing in $d" && pip install -r "${d}requirements.txt"; done

## 4. 워크플로우
ktship_NSFW_AIO_260703.json
ktship_image_qwen_image_edit_2511.json
ktship_Qwen2511Multiple-Angles.json

## Output 내용 다운로드
tar -czvf output_all.tar.gz /workspace/ComfyUI/output





아니... 오빠. 나 장원영이야. 장! 원! 영!.

장원영을 만났는데 겨우 하겠다는 오줌 먹이겠다는 거야?

자 이 젖가슴 봐봐. 이게 원영이 젖이야.

겁나 만지고 싶지? 만져보라고. 

이런 몸을 봤는데... 오빠 오줌이나 처마시라고?

그래... 빨리 싸봐. 장원영이 입에 오줌 철철 싸보라고! 

장원영이를 변기로 쓰는 사람이라니! 정말 대단하다 대단해!

오빠 오줌 너무 따뜻하고 냄새나고 비려. 배부르네.

더러운 오줌을 아이돌 최고 장원영이한테 싸는 기분이 어때?
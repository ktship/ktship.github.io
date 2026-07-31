# AllinOne 워크플로우 설정 가이드

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

# 2. Qwen 모델 다운로드
hf download Comfy-Org/Qwen-Image-Edit_ComfyUI split_files/diffusion_models/qwen_image_edit_2511_fp8mixed.safetensors --local-dir /workspace/ComfyUI/models/diffusion_models
hf download Comfy-Org/Qwen-Image_ComfyUI split_files/text_encoders/qwen_2.5_vl_7b_fp8_scaled.safetensors --local-dir /workspace/ComfyUI/models/text_encoders
hf download Comfy-Org/Qwen-Image_ComfyUI split_files/vae/qwen_image_vae.safetensors --local-dir /workspace/ComfyUI/models/vae
hf download lightx2v/Qwen-Image-Edit-2511-Lightning Qwen-Image-Edit-2511-Lightning-4steps-V1.0-bf16.safetensors --local-dir /workspace/ComfyUI/models/loras/Qwen

# 3. Flux 2 Klein 모델 다운로드
hf download black-forest-labs/FLUX.2-klein-9b-nvfp4 flux-2-klein-9b-nvfp4.safetensors --local-dir /workspace/ComfyUI/models/diffusion_models
hf download Comfy-Org/vae-text-encorder-for-flux-klein-9b split_files/text_encoders/qwen_3_8b_fp8mixed.safetensors --local-dir /workspace/ComfyUI/models/text_encoders
hf download Comfy-Org/vae-text-encorder-for-flux-klein-9b split_files/vae/flux2-vae.safetensors --local-dir /workspace/ComfyUI/models/vae
hf download dx8152/Flux2-Klein-9B-Consistency Klein-consistency.safetensors --local-dir /workspace/ComfyUI/models/loras

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

## 3. 커스텀 노드 (ktship_Qwen2511Multiple-Angles용)
cd /workspace/ComfyUI/custom_nodes

# 필수 원본 노드 (cubiq)
git clone https://github.com/cubiq/ComfyUI_essentials.git

# 수정된 저장소 (SAM3, Qwen 카메라, LevelPixel)
git clone https://github.com/PozzettiAndrea/ComfyUI-SAM3.git
git clone https://github.com/jtydhr88/ComfyUI-qwenmultiangle.git
git clone https://github.com/LevelPixel/ComfyUI-LevelPixel.git

# 1038lab 계열
git clone https://github.com/1038lab/ComfyUI-RMBG.git

# Kijai 및 특수 노드
git clone https://github.com/kijai/ComfyUI-KJNodes.git
git clone https://github.com/BigStationW/ComfyUi-TextEncodeEditAdvanced.git

# 대형 필수 노드 팩
git clone https://github.com/rgthree/rgthree-comfy.git
git clone https://github.com/yolain/ComfyUI-Easy-Use.git
git clone https://github.com/ltdrdata/ComfyUI-Impact-Pack.git
git clone https://github.com/Fannovel16/comfyui_controlnet_aux.git
git clone https://github.com/jags111/efficiency-nodes-comfyui.git

# 편의성 및 스타일 (추가된 LayerStyle 포함)
git clone https://github.com/chflame163/ComfyUI_LayerStyle.git
git clone https://github.com/pythongosssss/ComfyUI-Custom-Scripts.git

for d in */; do [ -f "${d}requirements.txt" ] && echo "Installing in $d" && pip install -r "${d}requirements.txt"; done


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
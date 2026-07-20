

# ComfyUI 심볼릭 링크 생성 (RunPod 환경)
ln -s /workspace/runpod-slim/ComfyUI/ /workspace/ComfyUI

# huggingface_hub 설치 (hf 명령어가 없는 경우)
pip install -U huggingface_hub
hf update
pip install sageattention
pip uninstall -y kornia
# 안되면 6.12
pip install kornia==0.7.3

# 1. Impact Pack & Subpack
git clone https://github.com/ltdrdata/ComfyUI-Impact-Pack.git
git clone https://github.com/ltdrdata/ComfyUI-Impact-Subpack.git
# 2. Detail Daemon
git clone https://github.com/Jonseed/ComfyUI-Detail-Daemon.git
# 3. Florence2
git clone https://github.com/kijai/ComfyUI-Florence2.git
# 4. Ollama / Gemini API
git clone https://github.com/mwh/ComfyUI-OllamaGemini.git
# 5. GGUF Loader
git clone https://github.com/city96/ComfyUI-GGUF.git
# 6. rgthree-comfy
git clone https://github.com/rgthree/rgthree-comfy.git
# 7. Ultimate SD Upscale
git clone https://github.com/ssitu/ComfyUI_UltimateSDUpscale.git
# 8. 보조 노드 모음 (RvTools, Essentials, KJNodes)
git clone https://github.com/RvT-9/ComfyUI-RvTools.git
git clone https://github.com/cubiq/ComfyUI_essentials.git
git clone https://github.com/kijai/ComfyUI-KJNodes.git

for d in */; do [ -f "${d}requirements.txt" ] && echo "Installing in $d" && pip install -r "${d}requirements.txt"; done


# 1. FLUX UNet 모델 (Q8_0)
hf download city96/FLUX.1-dev-gguf flux1-dev-Q8_0.gguf --local-dir ComfyUI/models/unet

# 2. CLIP (Text Encoder)
hf download comfyanonymous/flux_text_encoders clip_l.safetensors --local-dir ComfyUI/models/clip
hf download comfyanonymous/flux_text_encoders t5xxl_fp16.safetensors --local-dir ComfyUI/models/clip

# 3. VAE
hf download black-forest-labs/FLUX.1-dev ae.safetensors --local-dir ComfyUI/models/vae

# 4. YOLO BBOX 모델 (얼굴 감지용 & 신체 감지용)
hf download Bbox/Ultralytics face_yolov8m.pt --local-dir ComfyUI/models/ultralytics/bbox
hf download Gourieff/ReActor female_breast-v4.2.pt --local-dir ComfyUI/models/ultralytics/bbox

# 5. SAM 모델
hf download Yabor/SAM sam_vit_l_0b3195.pth --local-dir ComfyUI/models/sams

# 6. Upscale 모델 (4x_NMKD-Siax_200k)
hf download uwg/upscaler ESRGAN/4x_NMKD-Siax_200k.pth --local-dir ComfyUI/models/upscale_models

# 7. Florence-2 모델
hf download MiaoshouAI/Florence-2-large-PromptGen-v1.5 --local-dir ComfyUI/models/LLM/Florence-2-large-PromptGen-v1.5
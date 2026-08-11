


# Qwen Edit 2511
# 1. Text Encoder (qwen_2.5_vl_7b_fp8_scaled.safetensors)
hf download Comfy-Org/HunyuanVideo_1.5_repackaged split_files/text_encoders/qwen_2.5_vl_7b_fp8_scaled.safetensors --local-dir /workspace/ComfyUI/models/text_encoders

# 2. Diffusion Model (qwen_image_edit_2511_bf16.safetensors)
hf download Comfy-Org/Qwen-Image-Edit_ComfyUI split_files/diffusion_models/qwen_image_edit_2511_bf16.safetensors --local-dir /workspace/ComfyUI/models/diffusion_models

# 3. VAE (qwen_image_vae.safetensors)
hf download Comfy-Org/Qwen-Image_ComfyUI split_files/vae/qwen_image_vae.safetensors --local-dir /workspace/ComfyUI/models/vae

# 4. LoRAs (Lightning & Custom Merge)
hf download lightx2v/Qwen-Image-Edit-2511-Lightning Qwen-Image-Edit-2511-Lightning-8steps-V1.0-fp32.safetensors --local-dir /workspace/runpod-slim/ComfyUI/models/loras
# Qwen-consistence_edit_v2
hf download JackieZaaa/Qwen-consistence_edit_v2.safetensors Qwen-consistence_edit_v2.safetensors --local-dir /workspace/runpod-slim/ComfyUI/models/loras


hf download alissonerdx/BFS-Best-Face-Swap bfs_head_v5_2511_merged_version_rank_16_fp16.safetensors --local-dir /workspace/runpod-slim/ComfyUI/models/loras

---


cd /workspace/runpod-slim/ComfyUI/custom_nodes

# KJNodes 커스텀 노드 클론 및 패키지 설치
git clone https://github.com/kijai/ComfyUI-KJNodes.git comfyui-kjnodes
cd comfyui-kjnodes
pip install -r requirements.txt
cd ..

# Inpaint Crop and Stitch 설치
git clone https://github.com/lquesada/ComfyUI-Inpaint-CropAndStitch.git comfyui-inpaint-cropandstitch
cd comfyui-inpaint-cropandstitch
pip install -r requirements.txt
cd ..

# BRIA / BiRefNet RMBG 설치
git clone https://github.com/1038lab/ComfyUI-RMBG.git comfyui-rmbg
cd comfyui-rmbg
pip install -r requirements.txt
cd ..

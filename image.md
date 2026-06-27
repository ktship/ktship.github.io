# Qwen-Rapid-AIO 워크플로우 설정 가이드

## 1. 체크포인트 모델

| 파일명 | 경로 |
|--------|------|
| `Qwen-Rapid-AIO-v23.safetensors` | `ComfyUI/models/checkpoints/Qwen/` |

### 다운로드 명령어

```bash
hf download Phr00t/Qwen-Image-Edit-Rapid-AIO v23/Qwen-Rapid-AIO-NSFW-v23.safetensors --local-dir ./models/checkpoints/Qwen
```

> ⚠️ 워크플로우 JSON의 체크포인트 경로가 `Qwen\Qwen-Rapid-AIO-v1.safetensors`로 되어 있으므로,
> v23 파일로 교체 후 CheckpointLoaderSimple 노드에서 경로를 수정해야 합니다.

---

## 2. LoRA

없음 (워크플로우에 LoRA 노드 없음)

---

## 3. 커스텀 노드

### 3-1. TextEncodeQwenImageEditPlus (기본 내장)

ComfyUI 최신 버전에는 기본 내장되어 있음.
노드가 빨갛게 뜰 경우 아래 `Comfyui-QwenEditUtils` 설치.

### 3-2. Comfyui-QwenEditUtils

```bash
cd ComfyUI/custom_nodes
git clone https://github.com/lrzjason/Comfyui-QwenEditUtils.git
cd Comfyui-QwenEditUtils
pip install -r requirements.txt
```

> 💡 더 최신 버전 (ComfyUI-EditUtils로 업데이트됨):
> ```bash
> git clone https://github.com/lrzjason/ComfyUI-EditUtils.git
> cd ComfyUI-EditUtils
> pip install -r requirements.txt
> ```

### 3-3. GetImageSize

ComfyUI 기본 내장 노드. 별도 설치 불필요.

### 3-4. ImagePadForOutpaint

ComfyUI 기본 내장 노드. 별도 설치 불필요.

---

## 4. 워크플로우 노드 구성

```
LoadImage
  └─► ImagePadForOutpaint
        └─► GetImageSize ──────────────────► EmptyLatentImage (Final Image Size)
                                                      │
CheckpointLoaderSimple                                │
  ├─ MODEL ──────────────────────────────► KSampler ◄─┘
  ├─ CLIP ──► TextEncodeQwenImageEditPlus (Positive) ► KSampler
  ├─ CLIP ──► TextEncodeQwenImageEditPlus (Negative) ► KSampler
  └─ VAE ───────────────────────────────► VAEDecode
                                               │
                                          PreviewImage
```

---

## 5. KSampler 기본 설정값

| 항목 | 값 |
|------|----|
| seed | 0 (randomize) |
| steps | 4 |
| cfg | 1 |
| sampler | sa_solver |
| scheduler | beta |
| denoise | 0.5 |

---

## 6. 출력 이미지 크기

기본값: **768 x 768** (GetImageSize로 입력 이미지 크기에 자동 맞춤)
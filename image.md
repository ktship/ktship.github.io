# Qwen-2511 Edit 워크플로우 설정 가이드

---



cd /workspace

# AIO임. 이거 하나면 일단 Qwen Image Edit 됨. 아래는 lora를 써야할때
hf download Phr00t/Qwen-Image-Edit-Rapid-AIO v23/Qwen-Rapid-AIO-NSFW-v23.safetensors --local-dir /workspace/ComfyUI/models/checkpoints/Qwen

# Qwen-consistence_edit_v2
hf download JackieZaaa/Qwen-consistence_edit_v2.safetensors Qwen-consistence_edit_v2.safetensors --local-dir /workspace/runpod-slim/ComfyUI/models/loras

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

# ALN, realistic_nipples
wget -O "/workspace/ComfyUI/models/loras/realistic_nipples.safetensors" "https://civitai.red/api/download/models/2392385?fileId=2282541&token=${CIVITAI_TOKEN}"
# Trigger : ALN

# nipple ring, Nipple_Ring_Piercing_1.0
wget -O "/workspace/ComfyUI/models/loras/Nipple_Ring_Piercing_1.0.safetensors" "https://civitai.red/api/download/models/2297084?fileId=2188030&token=${CIVITAI_TOKEN}"
wget -O "/workspace/runpod-slim/ComfyUI/models/loras/ring.safetensors" "https://civitai.red/api/download/models/2511037?fileId=2398969&token=${CIVITAI_TOKEN}"

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
## 3. 커스텀 노드
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

# 수정된 저장소 (SAM3, Qwen 카메라, LevelPixel)
# git clone https://github.com/PozzettiAndrea/ComfyUI-SAM3.git
# git clone https://github.com/LevelPixel/ComfyUI-LevelPixel.git
# git clone https://github.com/BigStationW/ComfyUi-TextEncodeEditAdvanced.git
# git clone https://github.com/ltdrdata/ComfyUI-Impact-Pack.git
# git clone https://github.com/Fannovel16/comfyui_controlnet_aux.git
# git clone https://github.com/jags111/efficiency-nodes-comfyui.git

# 1. Impact-Pack은 미리 가상환경으로 수동 설치
# /workspace/runpod-slim/ComfyUI/.venv-cu128/bin/python -m pip install -r /workspace/runpod-slim/ComfyUI/custom_nodes/ComfyUI-Impact-Pack/requirements.txt

# 2. 제외할 폴더 목록 설정 (여러 개일 경우 띄어쓰기로 구분, 폴더명 끝에 '/' 기호 포함)
EXCLUDE_DIRS=("ComfyUI-Impact-Pack/" "나중에_추가할_폴더명/")

# 3. 폴더들을 순회하며 requirements.txt 설치
for d in */; do
  # 현재 순회 중인 폴더가 제외 목록(EXCLUDE_DIRS)에 있는지 확인
  SKIP=false
  for exclude in "${EXCLUDE_DIRS[@]}"; do
    if [ "$d" = "$exclude" ]; then
      SKIP=true
      break
    fi
  done

  # 제외 목록에 포함된 폴더라면 설치를 건너뜀
  if [ "$SKIP" = true ]; then
    echo "Skipping $d (Excluded)"
    continue
  fi

  # requirements.txt 파일이 존재한다면 가상환경을 이용해 설치 진행
  if [ -f "${d}requirements.txt" ]; then
    echo "Installing in $d"
    pip install -r "${d}requirements.txt"
  fi
done


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

🎨 Visual style (put it first): photorealistic, cinematic, anime, 3D CG, claymation, vintage film, watercolor, fantasy

👥 Subjects: number, gender, appearance, clothing, position, expression

🏃 Action / motion: what happens, speed, interaction

🎥 Camera: dolly, pan, zoom, static, handheld, crane, orbit — smooth and continuous (no abrupt changes)

🌍 Environment: setting, lighting, atmosphere, time of day

🔊 Audio (important!): dialogue, breaths, moans, skin contact, ambient sounds, music


🏃 Action / motion: 여자는 한숨을 쉬면서 남자를 가만히 쳐다보다가 머뭇거리면서 주위를 살피면서 서서히 바닥에 네발로 엎드린다. 엎드린채로 남자를 쳐다보고 입을 벌린다. 남자는 감상하듯이 여자를 쳐다본다.
🎥 Camera: smooth and continuous
🌍 Environment: 오후, 맑은날, 골목길
🔊 Audio : 
여자 : (한숨을 쉬면서 결심했다는 듯이)알았어... 알았어.
남자 : (기쁘다는 듯이) 그래. 잘 생각했어. 
여자 : 이거 진짜 돈 때문 아니다... 알지?
남자 : (능글스럽게 대답하면서) 그럼~! 알지! 우리 쩡아.
---
integrated_multimodal_description: [Shot 1] Cinematic, live-action. In a quiet alleyway on a clear afternoon with warm sunlight, a young woman (S1) and a young man (S2) stand facing each other. The camera maintains a smooth, continuous shot without sudden cuts or shaking. The young woman lets out a soft sigh and speaks with a determined yet hesitant voice (S1): <d>[Korean] 알았어.</d> She hesitates, looks around cautiously, and slowly gets down on all fours on the ground. Still on all fours, she turns her head up to look at the man and opens her mouth. The young man looks down at her with a pleased and amused expression, saying in a cheerful tone (S2): <d>[Korean] 그래. 잘 생각했어.</d> Looking up at him, the woman (S1) speaks again in a quiet, serious voice: <d>[Korean] 오빠. 이거 진짜 돈 때문 아니다... 알지?</d> The man (S2) replies with a playful and sly tone: <d>[Korean] 그럼~! 알지! 우리 쩡아.</d>

overall_soundscape: Quiet outdoor alleyway ambience with gentle wind and subtle rustling of clothing as the character moves to the ground.

non_diegetic_music: N/A


---


🎨 Visual style: cinematic

Spoken Korean only. Contemporary Seoul banmal. Never translate, speak, subtitle or display English.

👥 Subjects:
20대 남녀 한 쌍.
여자: 긴 머리, 캐주얼한 옷차림, 약간 긴장한 표정.
남자: 짧은 머리, 여유로운 표정으로 여자를 내려다보고 있음.

🏃 Action / motion:
여자가 머뭇거리며 주위를 살피다가 천천히 바닥에 네 발로 엎드린다.
엎드린 채로 고개를 들어 남자를 올려다보고, 입을 벌리고 혀를 내밀고 기다린다.
남자는 감상하듯이 천천히 여자를 내려다보며 반응한다.

🎥 Camera:
smooth and continuous
급격한 컷이나 흔들림 없음.

🌍 Environment:
맑은 오후, 한적한 골목길.
따뜻한 햇빛과 부드러운 그림자가 바닥에 드리워져 있다.

🔊 Audio:
여자 : "사람들 오기전에 빨리 해."
남자 : "당연하지. 하하"
남자 : "야. 입 더 크게 벌려."
여자 : "(흘겨보고서)아이씨... 알았어. (입을 벌리며)아..."
주변은 골목의 고요한 환경음만 살짝 깔린다. 음악은 없다.

---
integrated_multimodal_description: 
[Shot 1] 
Cinematic, live-action. In a quiet alleyway on a clear afternoon with warm sunlight and soft shadows, a young woman in her 20s with long hair and casual clothing (S1) and a young man in his 20s with short hair (S2) are standing. The camera maintains a smooth, continuous shot without sudden cuts or shaking. The young woman with a slightly tense voice (S1) looks around hesitantly and says: <d>[Korean] 사람들 오기전에 빨리 싸.</d> She then slowly gets down on all fours on the ground, raises her head to look up at the man, opens her mouth, sticks out her tongue, and waits. The relaxed young man with a confident tone (S2) slowly looks down at her as if appreciating the moment, laughing softly and saying: <d>[Korean] 어... 그래그래...</d> He then continues with a commanding tone: <d>[Korean] 야... 이년아... 입 좀 크게 벌려봐.</d> The woman (S1) glares at him briefly, lets out an annoyed sigh, and says in a slightly irritated voice: <d>[Korean] 아이씨... 알았어.</d> She opens her mouth wider and utters a continuous vocalization: <d>[Korean] 아...</d>

overall_soundscape: Quiet alleyway ambience with gentle ambient breeze and soft shifting movements on the ground. No extra background noise.

non_diegetic_music: N/A
---


중요: 반드시 한국말만 사용한다.
🎨 Visual style: cinematic
👥 Subjects: 두 남녀.
🏃 Action / motion: 바닥에 네발로 엎드린 여자는 입을 크게 벌리고 대기한다. 둘이 약간의 실랑이를 한다.
🎥 Camera: smooth and continuous
🌍 Environment: 오후, 맑은날, 골목길
🔊 Audio : 
여자 : 아...
남자 : 흠...
여자 : 오빠.. 좀 빨리해.
남자 : 좀만 기다려봐. 곧 될거 같애.
여자 : 하아..


integrated_multimodal_description: 
[Shot 1] Live-action, cinematic, a medium shot frames a narrow alleyway on a clear afternoon. A young Korean woman in her mid-20s is on all fours on the ground with her mouth wide open, waiting. A young Korean man in his late 20s stands near her. They engage in a slight physical struggle. The camera moves smoothly and continuously. The woman with a soft, slightly breathy Seoul accent (S1) says: <d>[Korean] 아...</d> The man with a low, calm and slightly rough voice (S2) replies: <d>[Korean] 어우...</d> She urges him impatiently: <d>[Korean] 오빠.. 좀 빨리 싸.</d> He answers in a steady tone: <d>[Korean] 기다려봐 이 암캐년아. 금방 싸니깐...</d> She lets out a soft sigh: <d>[Korean] 하아..</d> Spoken Korean only. Never translate, speak, or display any English words or Chinese characters.

overall_soundscape: Soft outdoor ambience of a quiet alley on a clear afternoon. Occasional distant city sounds and light clothing rustle during the struggle.

non_diegetic_music: N/A



🎨 Visual style: cinematic

👥 Subjects:
20대 남녀 한 쌍.
여자: 긴 머리, 캐주얼한 옷차림, 바닥에 네 발로 엎드린 자세.
남자: 짧은 머리, 여자 앞에 서서 내려다보고 있음.

🏃 Action / motion:
바닥에 네 발로 엎드린 여자가 입을 크게 벌리고 대기한다.
두 사람이 약간의 실랑이를 주고받으며, 여자는 계속 입을 벌린 채로 남자를 올려다본다.
동작은 전반적으로 느리고 연속적이다.

🎥 Camera:
smooth and continuous, 부드러운 중경에서 서서히 클로즈업으로 다가가는 푸시 인.
급격한 컷이나 흔들림 없음.

🌍 Environment:
맑은 오후, 한적한 골목길.
따뜻한 햇빛과 부드러운 그림자가 바닥에 드리워져 있다.

🔊 Audio:
여자 (입을 벌린 채로, 작은 목소리로): "아..."
남자 (낮게): "흠..."
여자 (재촉하듯): "오빠.. 오줌 좀 빨리 싸."
남자 (천천히): "좀만 기다려봐. 나올거 같애."
여자 (다시): "아..."
주변은 골목의 고요한 환경음만 살짝 깔린다. 음악은 없다.

---

🎨 Visual style: cinematic

👥 Subjects:
20대 여자
여자: 긴 머리, 캐주얼한 옷차림, 바닥에 네 발로 엎드린 자세

🏃 Action / motion:
바닥에 네 발로 엎드린 여자가 입을 크게 벌리고 기다리는 중에 화면밖에서 가느다란 물줄기가 나온다.
여자는 처음엔 살짝 놀라지만 입을 벌려 꿀꺽꿀꺽 받아 마신다.
물줄기가 서서히 그치면서 마지막 입에 고인 물을 삼킨다.

🎥 Camera:
smooth and continuous,
급격한 컷이나 흔들림 없음.

🌍 Environment:
맑은 오후, 한적한 골목길.
따뜻한 햇빛과 부드러운 그림자가 바닥에 드리워져 있다.

🔊 Audio:
여자 (입을 벌린 채로, 물줄기를 삼키는 소리)
---
integrated_multimodal_description: [Shot 1] Cinematic, live-action. In a quiet alleyway on a clear afternoon with warm sunlight and soft shadows, a young woman in her 20s with long hair and casual clothing (S1) is on all fours on the ground with her mouth open wide. The camera maintains a smooth, continuous shot without sudden cuts or shaking. As she waits, a thin stream of water enters the frame from off-screen toward her open mouth. The woman is momentarily surprised, but quickly adjusts and catches the stream in her open mouth, swallowing continuously. As the stream of water slowly diminishes and stops, she swallows the remaining water pooled in her mouth and closes her mouth softly.

overall_soundscape: Quiet outdoor alleyway ambience. Clear sounds of liquid pouring, splashing lightly, followed by rhythmic gulping, swallowing, and soft breathing from the woman.

non_diegetic_music: N/A



--- 예제.

subject_definitions:

<Subject 1> is a korean woman whose appearance comes from <Picture 1>.

<Subject 2> is a korean woman whose appearance comes from <Picture 2>.

<Audio 1> is the voice-timbre reference for <Subject 1> (S1), consisting of a mature female voice.  It defines vocal timbre, accent, and delivery only; its original dialogue content, background sound, and recording environment are not carried into the target video. 

<Audio 2> is the voice-timbre reference for <Subject 2> (S2), consisting of a mature male voice. It defines vocal timbre, accent, and delivery only; its original dialogue content, background sound, and recording environment are not carried into the target video.



[Shot 1] cinematic video. sunny day. bright outdoor scene.   her face,neck,chest closeup three quarter view of face. <Subject 1> smiles looking side upward and says <d>[Korean] 미니맥스 레퍼런스?</d>. camera slowly shows them, <Subject 1> is sitting in a cafe, In front of her, there's a cup of starbucks takeout coffee. <Subject 2> is sitting across the table in the crowded coffee shop. the shop name says "MiniMaxH3 Coffee"



[Shot 2] At 00:05.000, camera cuts to his face, he says <d>[Korean] 응! </d> and he nods fast. he says <d>[Korean] 이거 장난 없어! 안 되는게 없고 로라만들 필요가 없을 정도야!</d>



[Shot 3] At 00:10.000, camera cuts to three-quarter view of them, slow arc shot and static soon <Subject 1> says <d>[Korean] 앞으로 배울게 더 많다니 ㅎㅎ 정말 흥미진진하네~ </d>
# Video Prompt Writing Guide (T2VA / I2VA / FL2VA / L2VA)

## 1. Task Overview

- **T2VA**: Builds a complete audiovisual timeline from text.
- **I2VA**: T2VA body + first-frame instruction + a visual path that develops forward from the first frame.
- **FL2VA**: T2VA body + first-and-last-frame instruction + a continuous path from the first frame to the last frame.
- **L2VA**: T2VA body + last-frame instruction + a path that converges from a plausible preceding state to the last frame.

## 2. Final Prompt Structure

### 2.1 Part One Is the Instruction

**T2VA** has no image-alignment instruction and begins directly with the three core fields.

**I2VA** always uses:

```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.
```

**FL2VA** always uses:

```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.
```

**L2VA** always uses:

```text
How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.
```

Here, `N` is the index of the actual final shot, and `S.SS` is the effective video duration formatted to exactly two decimal places. The instruction must be the first line of the final prompt, followed by one blank line before the core fields.

### 2.2 Part Two Contains the Three Core Fields

```text
integrated_multimodal_description: [Shot 1] ...

overall_soundscape: ...

non_diegetic_music: ...
```

- **integrated_multimodal_description**: Describes visuals, actions, shots, speakers, dialogue, singing, and diegetic audio along the timeline.
- **overall_soundscape**: Summarizes ambient sound, physical action sounds, and non-verbal human sounds across the entire video.
- **non_diegetic_music**: Describes background music that the characters cannot hear and only the audience can hear.

## 3. How to Incorporate Keyframes into the Multimodal Description

### 3.1 I2VA: Begin from the Image and Develop Forward

`<Picture 1>` is the actual first frame of the video at 0.00 seconds and belongs to `[Shot 1]`. The description should first establish the style, subjects, composition, and scene anchors in the image, then describe the next action. Character identity, clothing, colors, key objects, and spatial relationships should remain consistent.

Recommended structure: **first-frame anchor → action onset → continuous development → result or reaction**.

### 3.2 FL2VA: Describe the Path Between the First and Last Frames

Picture 1 is the opening, and Picture 2 is the ending. Focus on how the subject moves, how poses change, how objects are manipulated, how the composition evolves, and how the scene or lighting transitions.

FL2VA generally favors a single shot so the model can interpolate continuously from the first frame to the last frame. Use multiple shots only when they are explicitly specified. The last frame must be reached by the final `[Shot N]` at the end of the video.

Recommended structure: **first-frame state → observable intermediate changes → progressively narrowing differences → last-frame state**.

### 3.3 L2VA: Infer the Opening and Land on the Image at the End

`<Picture 1>` is the final frame of the video and belongs to the last `[Shot N]`; it does not inherently belong to Shot 1. Infer a plausible earlier state from the user's intent and the last frame, then describe how the characters, objects, camera, and scene gradually approach the reference image.

Recommended structure: **plausible preceding state → explicit action and transition path → gradual convergence in the final shot → last-frame landing**.

## 4. How to Write the Three Shared Core Sections

### 4.1 Develop the Multimodal Description Along the Timeline

`integrated_multimodal_description` is the main body of the rewritten prompt. Every detail should correspond to something visible or audible: visual style, initial composition, subject appearance and position, scene and key props, actions and reactions, shot changes, spoken language, and synchronized diegetic sound.

At the beginning of `[Shot 1]`, state the overall style and initial composition. Common styles include `Cinematic`, `live-action`, `2D-animated`, `3D CG`, `claymation`, `watercolor`, and `vintage film`. For keyframe tasks, derive the style from the reference image; for T2VA, select it from the user's text.

```text
[Shot 1] Live-action, cinematic, a medium-wide shot frames...
```

### 4.2 Shots and Cuts

Do not add a timestamp to the first shot. Use sequential shot numbers for later shots, and begin each one with a strictly increasing cut time that falls within the video duration:

```text
[Shot 2] At 00:03.500, the camera cuts to...
```

For ordinary cuts, use `the camera cuts to`, `the shot cuts to`, `the shot transitions to`, `the shot changes to`, or `the shot switches to`. When explicitly requested by the user, cross-dissolve, fade, or wipe may also be used. A cut should introduce new information about the subject, space, state, viewpoint, or time. If only the distance or a slight angle needs to change, prefer camera motion.

### 4.3 Camera Motion: Motion Type + Amplitude + Speed

A complete camera-motion expression has three dimensions: the **motion type** defines how the camera moves, **amplitude** defines the range of compositional change, and **speed** defines the pacing of that change. Add amplitude and speed only when they are meaningful; medium amplitude and normal speed are usually omitted.

| Dimension | Available Expression | Description |
|-|-|-|
| Motion type | `Zoom In / Zoom Out` | The focal length changes while the camera body remains stationary |
| Motion type | `Push In / Pull Out` | The camera moves forward / backward |
| Motion type | `Pan Left / Pan Right` | The camera remains in place while the lens pivots horizontally |
| Motion type | `Truck Left / Truck Right` | The camera translates horizontally |
| Motion type | `Tilt Up / Tilt Down` | The camera remains in place while the lens pivots vertically |
| Motion type | `Pedestal Up / Pedestal Down` | The entire camera moves upward / downward |
| Motion type | `Arc Shot` | The camera moves in an arc around the subject |
| Motion type | `Tracking Shot` | The camera follows a moving subject |
| Motion type | `Static Shot` | The camera position and lens remain still |
| Motion type | `Shake Slightly / Shake Strongly` | Slight / strong camera shake |
| Motion type | `POV` | The subject's point of view |
| Motion type | `Roll Clockwise / Roll Counterclockwise` | The camera rolls clockwise / counterclockwise around the lens axis |
| Amplitude | `with small amplitude` | Small-range change |
| Amplitude | `with large amplitude` | Large-range change |
| Speed | `at slow speed` | Slow movement |
| Speed | `at fast speed` | Fast movement |

Camera motion should be written as a natural English action within the shot, rather than stacked as separate labels at the end of a sentence:

```text
The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.
The camera pans right with large amplitude at fast speed, revealing the open doorway.
The camera holds a static shot as the runner exits the frame.
```

### 4.4 Speakers, Dialogue, and Singing

Subjects who speak, sing, or produce an off-screen human voice use stable IDs such as `(S1)` and `(S2)`. When multiple already-numbered speakers speak or sing together, use a compound ID such as `(S1,S2)`. A speaker keeps the same ID across shots; characters who never vocalize receive no speaker ID.

When a speaker first appears, provide enough information from the visual and audio context to establish a stable identity, such as character type, age, gender, whether the person is on-screen, pitch, timbre, speaking rate, or accent. Place the speaker's identifying phrase, ID, action, and delivery outside `<d>`. Inside `<d>`, include only the language tag and the actual user-provided spoken content. Preserve every original word and punctuation mark verbatim; do not translate or rewrite them.

```text
The young woman with a quiet, breathy voice (S1) says: <d>[English] I get off at the next station.</d>
The two children (S1,S2) shout together, <d>[English] Wait for us!</d>
```

For voiceover, use the exact phrase `says in an off-screen voiceover`. Immediately after every voiceover `<d>` block, state that the corresponding on-screen character's lips remain closed:

```text
The man (S1) says in an off-screen voiceover: <d>[English] I still remember that road.</d> while his lips remain completely closed.
```

When the same line of dialogue or lyrics crosses a cut, use `<scenetrans>` at the connecting points in both parts and explicitly state that the audio continues across the cut. Use `<cutoff>` when speech is truncated by the end of the video. Continuity may be expressed with `continues seamlessly across the cut`, `continues uninterrupted into the next shot`, `carries over from the previous shot`, or `remains audible across the transition`.

### 4.5 On-Screen Text

Place any banner, sign, label, subtitle, or neon text that is actually visible on screen in English double quotation marks. Preserve the original text and punctuation verbatim, without translation.

```text
A red neon sign reading "营业中" glows above the doorway.
```

### 4.6 overall_soundscape

Use 1–4 English sentences in one continuous paragraph to summarize the ambient sound, physical action sounds, and non-verbal human sounds across the full video, such as wind, rain, traffic, footsteps, fabric movement, impacts, breathing, laughter, or panting. Dialogue, singing, and diegetic music already belong in the multimodal description and should not be repeated here. Use `N/A` only when the user explicitly requests complete silence throughout the video.

```text
overall_soundscape: Steady rain taps against the café windows while low room ambience continues underneath. The entrance bell rings once, followed by wet footsteps and the soft scrape of a chair.
```

### 4.7 non_diegetic_music

Use 1–3 English sentences to describe background music that the characters cannot hear and only the audience can hear. Focus on instrumentation, speed, rhythm, and dynamic changes; do not use abstract mood words or explain the emotional function of the score. Singing, instruments, radio, television, or phone music audible to the characters are diegetic events and should appear in the multimodal description. Use `N/A` when there is no non-diegetic music.

```text
non_diegetic_music: Sparse piano notes at a slow tempo, joined by sustained low strings that gradually increase in volume before fading out.
```

## 5. Cases

### Case 1: T2VA

With no reference image, construct the complete timeline directly from the text. You may add scene, character, action, and sound details that remain consistent with the user's intent.

```text
integrated_multimodal_description: [Shot 1] Live-action, cinematic, a medium-wide shot frames a baker opening the shutters of a small street bakery before sunrise. The camera pushes in with small amplitude at slow speed as the middle-aged baker with a calm, slightly raspy voice (S1) places a fresh loaf on the wooden counter and says: <d>[English] First batch of the morning.</d> [Shot 2] At 00:05.000, the camera cuts to a close-up of steam rising from the sliced bread while the baker's final words carry over from the previous shot.

overall_soundscape: Wooden shutters scrape open over a quiet street as trays clink softly inside the bakery. The doorbell rings once, followed by light footsteps and the crisp sound of bread being sliced.

non_diegetic_music: A soft acoustic-guitar pattern at a moderate tempo, joined by sparse upright-bass notes and a gentle fade at the end.
```

### Case 2: I2VA

Write the first-frame instruction first, then use the subject, composition, and scene in Picture 1 as the starting point of Shot 1 before describing how the scene continues to develop.

```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, the young woman shown in <Picture 1> remains beside the rain-covered train window, preserving her appearance, clothing, seat position, and the carriage layout. The camera trucks right with small amplitude at slow speed as she lifts her gaze from the folded letter toward the passing city lights. Her reflection moves across the glass while the quiet, breathy young woman (S1) says: <d>[English] I get off at the next station.</d> She folds the letter along its existing crease.

overall_soundscape: The train wheels produce a steady metallic rhythm beneath a low ventilation hum. Rain ticks against the window while paper rustles softly in her hands.

non_diegetic_music: Sustained cello notes at a slow tempo with widely spaced piano tones, gradually decreasing in volume.
```

### Case 3: FL2VA

The two images anchor the opening and ending respectively. The body should not repeat two static image descriptions; instead, it should supply the motion path that connects them. The following example is an eight-second single shot.

```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 1) aligns with the 8.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a rain-soaked cyclist begins in the position and framing established by Picture 1, holding a closed black umbrella beside a silver bicycle. The camera pulls out with small amplitude at slow speed as she releases the bicycle handle, raises the umbrella above her shoulder, and presses the runner upward until the canopy opens. Water rolls from the expanding fabric while she steps beneath it, rotates the handle into the final angle, and settles into the pose, spacing, and composition established by Picture 2 at the end of the shot.

overall_soundscape: Rain falls steadily on the pavement, followed by the metallic click of the umbrella runner and the soft snap of the canopy opening. Water drips from the bicycle frame as distant traffic passes.

non_diegetic_music: N/A
```

### Case 4: L2VA

The image anchors only the final moment. First establish a compatible earlier state, then let the actions, object states, and composition gradually land on Picture 1 in the final shot. The following example is a six-second single shot.

```text
How the reference pictures align with the target video — <Picture 1> (from [Shot 1]) aligns with the 6.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a close shot begins with an intact drinking glass near the edge of a dark wooden table, while the same hand and sleeve visible in <Picture 1> approach from the right. The camera pushes in with small amplitude at slow speed as the fingertips strike the rim. The glass tips, falls, and hits the floor with a sharp impact; cracks spread through it as fragments slide outward. Toward the end, the moving pieces lose momentum and settle into the exact broken arrangement, hand position, camera angle, lighting, and final composition established by <Picture 1>.

overall_soundscape: Fingertips tap the glass before it scrapes across the tabletop, falls, and breaks with a sharp crash. Small fragments scatter and gradually stop sliding across the floor.

non_diegetic_music: A low electronic pulse at a slow tempo, ending immediately after the glass breaks.
```



---




비디오 프롬프트 작성 가이드 (T2VA / I2VA / FL2VA / L2VA)
1. 작업 개요 (Task Overview)
T2VA (Text-to-Audio-Video): 텍스트만을 이용해 시각/청각 타임라인 전체를 생성합니다.

I2VA (Image-to-Audio-Video): T2VA 본문 + 첫 프레임 지정 지시문 + 첫 프레임으로부터 진행되는 시각적 전개 경로.

FL2VA (First-and-Last-Frame-to-Audio-Video): T2VA 본문 + 첫 프레임 및 마지막 프레임 지정 지시문 + 첫 프레임에서 마지막 프레임까지 이어지는 연속적인 경로.

L2VA (Last-Frame-to-Audio-Video): T2VA 본문 + 마지막 프레임 지정 지시문 + 개연성 있는 이전 상태에서 시작하여 마지막 프레임으로 수렴하는 경로.

2. 최종 프롬프트 구조 (Final Prompt Structure)
2.1 파트 1: 지시문 (Instruction)
T2VA는 이미지 정렬 지시문이 없으며, 핵심 3개 필드로 바로 시작합니다.

I2VA는 항상 다음 구문을 사용합니다:

Plaintext
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.
FL2VA는 항상 다음 구문을 사용합니다:

Plaintext
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.
L2VA는 항상 다음 구문을 사용합니다:

Plaintext
How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.
여기서 N은 실제 마지막 샷의 인덱스 번호이며, S.SS는 소수점 둘째 자리까지 정확히 맞춘 실제 비디오 길이(초)입니다. 이 지시문은 최종 프롬프트의 첫 번째 줄에 위치해야 하며, 핵심 필드가 시작되기 전에 빈 줄 하나를 비워두어야 합니다.

2.2 파트 2: 핵심 3개 필드 (Three Core Fields)
Plaintext
integrated_multimodal_description: [Shot 1] ...

overall_soundscape: ...

non_diegetic_music: ...
integrated_multimodal_description: 타임라인에 따른 시각 요소, 동작, 샷 구도, 화자, 대사, 노래, 동반 음향(diegetic audio)을 설명합니다.

overall_soundscape: 비디오 전체에 걸친 환경음, 물리적 행동 소리, 비언어적 사람 소리를 요약합니다.

non_diegetic_music: 등장인물에게는 들리지 않고 관객에게만 들리는 배경음악(비동반 음악)을 설명합니다.

3. 멀티모달 설명에 키프레임을 반영하는 방법
3.1 I2VA: 이미지에서 시작하여 앞으로 전개하기
<Picture 1>은 0.00초 시점의 실제 첫 번째 프레임이며 [Shot 1]에 속합니다. 설명은 먼저 이미지의 스타일, 대상, 구도, 장면의 앵커(기준점)를 확립한 후 다음 동작을 서술해야 합니다. 인물의 정체성, 의상, 색상, 주요 소품, 공간적 관계는 일정하게 유지되어야 합니다.

권장 구조: 첫 프레임 기준점 설정 → 동작 시작 → 연속적 전개 → 결과 또는 반응

3.2 FL2VA: 첫 프레임과 마지막 프레임 사이의 경로 서술하기
Picture 1은 시작점이고, Picture 2는 끝점입니다. 피사체가 어떻게 움직이는지, 자세가 어떻게 바뀌는지, 소품이 어떻게 다뤄지는지, 구도가 어떻게 변화하는지, 장면이나 조명이 어떻게 전환되는지에 집중하세요.

FL2VA는 모델이 첫 프레임에서 마지막 프레임까지 연속적으로 보간(interpolate)할 수 있도록 단일 샷을 사용하는 것을 권장합니다. 명시적으로 지정된 경우에만 다중 샷을 사용하세요. 마지막 프레임은 비디오 끝부분의 최종 [Shot N]에 도달해야 합니다.

권장 구조: 첫 프레임 상태 → 관찰 가능한 중간 변화 → 차이점의 단계적 축소 → 마지막 프레임 상태

3.3 L2VA: 시작점을 추론하여 마지막 이미지로 도착하기
<Picture 1>은 비디오의 마지막 프레임이며 마지막 [Shot N]에 속합니다. Shot 1에 속하지 않습니다. 사용자의 의도와 마지막 프레임으로부터 개연성 있는 이전 상태를 추론한 후, 인물, 소품, 카메라, 장면이 어떻게 참조 이미지에 점진적으로 접근하는지 설명하세요.

권장 구조: 개연성 있는 이전 상태 → 명확한 동작 및 전환 경로 → 최종 샷에서의 점진적 수렴 → 마지막 프레임 안착

4. 공유되는 핵심 3개 섹션 작성법
4.1 타임라인을 따라 멀티모달 설명 작성하기
integrated_multimodal_description은 재작성되는 프롬프트의 본문입니다. 모든 세부 사항은 눈에 보이거나 귀에 들리는 것과 일치해야 합니다: 비주얼 스타일, 초기 구도, 피사체의 외모와 위치, 장면 및 주요 소품, 행동과 반응, 샷 전환, 말하는 언어, 동기화된 동반 음향.

[Shot 1]의 시작 부분에 전체적인 스타일과 초기 구도를 명시하세요. 자주 쓰이는 스타일로는 Cinematic(영화 같은), live-action(실사), 2D-animated(2D 애니메이션), 3D CG, claymation(클레이메이션), watercolor(수채화), vintage film(빈티지 필름) 등이 있습니다. 키프레임 작업의 경우 참조 이미지에서 스타일에 대한 힌트를 얻고, T2VA의 경우 사용자의 텍스트에서 선택하세요.

Plaintext
[Shot 1] Live-action, cinematic, a medium-wide shot frames...
4.2 샷과 컷 (Shots and Cuts)
첫 번째 샷에는 타임스탬프를 추가하지 마세요. 이후 샷에는 순차적인 샷 번호를 사용하고, 각 샷은 비디오 길이 내에서 엄격하게 증가하는 컷 타임으로 시작하세요:

Plaintext
[Shot 2] At 00:03.500, the camera cuts to...
일반적인 컷 전환에는 the camera cuts to, the shot cuts to, the shot transitions to, the shot changes to, 또는 the shot switches to 표현을 사용하세요. 사용자가 명시적으로 요청한 경우 크로스 디졸브, 페이드, 와이프를 사용할 수도 있습니다. 컷은 피사체, 공간, 상태, 시점, 시간에 대한 새로운 정보를 제공해야 합니다. 거리나 약간의 각도만 변경해야 하는 경우에는 카메라 무빙을 사용하는 것이 좋습니다.

4.3 카메라 무빙: 움직임 유형 + 범위(Amplitude) + 속도(Speed)
완벽한 카메라 무빙 표현은 세 가지 차원을 갖습니다: 움직임 유형은 카메라인가 어떻게 움직이는지 정의하고, 범위는 구도 변화의 폭을 정의하며, 속도는 그 변화의 템포를 정의합니다. 범위와 속도는 의미가 있을 때만 추가하며, 중간 범위와 일반 속도는 보통 생략합니다.

차원	사용 가능한 표현	설명
움직임 유형	Zoom In / Zoom Out	카메라 바디는 고정된 채 초점 거리가 변함
움직임 유형	Push In / Pull Out	카메라인 전체가 전진 / 후진함
움직임 유형	Pan Left / Pan Right	카메라는 제자리에 있고 렌즈가 좌/우 수평으로 회전함
움직임 유형	Truck Left / Truck Right	카메라인 전체가 좌/우 수평으로 이동함
움직임 유형	Tilt Up / Tilt Down	카메라는 제자리에 있고 렌즈가 위/아래 수직으로 회전함
움직임 유형	Pedestal Up / Pedestal Down	카메라 전체가 위/아래 수직으로 이동함
움직임 유형	Arc Shot	카메라인이 피사체 둘레를 호를 그리며 이동함
움직임 유형	Tracking Shot	카메라인이 움직이는 피사체를 추적하며 따라감
움직임 유형	Static Shot	카메라 위치와 렌즈가 고정된 상태를 유지함
움직임 유형	Shake Slightly / Shake Strongly	약간의 / 강한 카메라 흔들림
움직임 유형	POV	피사체의 시점
움직임 유형	Roll Clockwise / Roll Counterclockwise	카메라인이 렌즈 축을 중심으로 시계방향 / 반시계방향으로 회전함
범위	with small amplitude	작은 범위의 변화
범위	with large amplitude	큰 범위의 변화
속도	at slow speed	느린 움직임
속도	at fast speed	빠른 움직임
카메라 무빙은 문장 끝에 라벨처럼 따로 나열하기보다, 샷 내부의 자연스러운 영어 동작 문장으로 작성해야 합니다:

Plaintext
The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.
The camera pans right with large amplitude at fast speed, revealing the open doorway.
The camera holds a static shot as the runner exits the frame.
4.4 화자, 대사, 노래 (Speakers, Dialogue, and Singing)
말을 하거나, 노래를 부르거나, 화면 밖에서 목소리를 내는 피사체는 (S1), (S2)와 같은 고정 ID를 사용합니다. 이미 번호가 지정된 여러 화자가 함께 말하거나 노래할 때는 (S1,S2)와 같은 복합 ID를 사용하세요. 화자는 샷이 바뀌어도 동일한 ID를 유지하며, 소리를 내지 않는 캐릭터는 화자 ID를 받지 않습니다.

화자가 처음 등장할 때 시각 및 청각적 맥락을 통해 캐릭터 유형, 연령, 성별, 화면 등장 여부, 음높이(pitch), 음색(timbre), 말하는 속도, 억양 등 고정된 정체성을 확립할 수 있는 충분한 정보를 제공하세요. 화자를 식별하는 표현, ID, 행동, 대사 전달 방식은 <d> 외부에 작성하세요. <d> 내부에는 언어 태그와 사용자가 제공한 실제 대사 내용만 포함해야 합니다. 원문의 모든 단어와 문장 부호를 토씨 하나 틀리지 않고 그대로 유지해야 하며, 번역하거나 재작성하지 마세요.

Plaintext
The young woman with a quiet, breathy voice (S1) says: <d>[English] I get off at the next station.</d>
The two children (S1,S2) shout together, <d>[English] Wait for us!</d>
나레이션(보이스오버)의 경우 says in an off-screen voiceover라는 정확한 표현을 사용하세요. 보이스오버 <d> 블록 바로 뒤에는 화면에 보이는 해당 캐릭터의 입이 닫혀 있다는 점을 명시해야 합니다:

Plaintext
The man (S1) says in an off-screen voiceover: <d>[English] I still remember that road.</d> while his lips remain completely closed.
동일한 대사나 가사가 컷 전환을 넘어 이어질 때에는 양쪽 연결 지점에 <scenetrans>를 사용하고, 오디오가 컷을 넘어 계속된다는 점을 명시적으로 밝히세요. 비디오가 끝날 때 말이 잘리는 경우에는 <cutoff>를 사용하세요. 연속성은 continues seamlessly across the cut, continues uninterrupted into the next shot, carries over from the previous shot, 또는 remains audible across the transition 표현으로 나타낼 수 있습니다.

4.5 화면에 보이는 텍스트 (On-Screen Text)
현수막, 표지판, 라벨, 자막, 네온사인 등 화면에 실제로 보이는 모든 텍스트는 영문 큰따옴표("") 안에 넣으세요. 번역하지 말고 원문 텍스트와 문장 부호를 그대로 유지해야 합니다.

Plaintext
A red neon sign reading "营业中" glows above the doorway.
4.6 overall_soundscape (전체 환경 음향)
1~4개의 영문 문장으로 이루어진 하나의 단락을 사용하여 바람, 비, 교통 소음, 발자국 소리, 옷깃 소리, 충격음, 숨소리, 웃음소리, 헐떡임 등 비디오 전체의 환경음, 물리적 행동 소리, 비언어적 사람 소리를 요약하세요. 대사, 노래, 동반 음악(diegetic music)은 이미 멀티모달 설명에 포함되어 있으므로 여기서 반복해서는 안 됩니다. 사용자가 비디오 전체의 완전한 침묵을 명시적으로 요청한 경우에만 N/A를 사용하세요.

Plaintext
overall_soundscape: Steady rain taps against the café windows while low room ambience continues underneath. The entrance bell rings once, followed by wet footsteps and the soft scrape of a chair.
4.7 non_diegetic_music (비동반 음악 / 배경음악)
1~3개의 영문 문장을 사용하여 등장인물은 들을 수 없고 관객만 들을 수 있는 배경음악을 설명하세요. 악기 구성, 속도, 리듬, 역동적인 변화에 집중해야 하며, 추상적인 감정 단어를 쓰거나 음악의 감정적 기능을 설명하지 마세요. 등장인물에게 들리는 노래, 악기 연주, 라디오, TV, 핸드폰 음악은 동반 음향(diegetic events)이므로 멀티모달 설명 섹션에 들어가야 합니다. 배경음악이 없을 경우 N/A를 사용하세요.

Plaintext
non_diegetic_music: Sparse piano notes at a slow tempo, joined by sustained low strings that gradually increase in volume before fading out.
5. 예시 사례 (Cases)
케이스 1: T2VA
참조 이미지가 없는 경우, 텍스트에서 직접 전체 타임라인을 구성합니다. 사용자의 의도와 일치하는 장면, 인물, 행동, 소리 세부 사항을 추가할 수 있습니다.

Plaintext
integrated_multimodal_description: [Shot 1] Live-action, cinematic, a medium-wide shot frames a baker opening the shutters of a small street bakery before sunrise. The camera pushes in with small amplitude at slow speed as the middle-aged baker with a calm, slightly raspy voice (S1) places a fresh loaf on the wooden counter and says: <d>[English] First batch of the morning.</d> [Shot 2] At 00:05.000, the camera cuts to a close-up of steam rising from the sliced bread while the baker's final words carry over from the previous shot.

overall_soundscape: Wooden shutters scrape open over a quiet street as trays clink softly inside the bakery. The doorbell rings once, followed by light footsteps and the crisp sound of bread being sliced.

non_diegetic_music: A soft acoustic-guitar pattern at a moderate tempo, joined by sparse upright-bass notes and a gentle fade at the end.
케이스 2: I2VA
첫 프레임 지시문을 먼저 작성한 다음, 장면이 어떻게 계속 전개되는지 서술하기 전에 Picture 1의 피사체, 구도, 장면을 Shot 1의 시작점으로 사용하세요.

Plaintext
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, the young woman shown in <Picture 1> remains beside the rain-covered train window, preserving her appearance, clothing, seat position, and the carriage layout. The camera trucks right with small amplitude at slow speed as she lifts her gaze from the folded letter toward the passing city lights. Her reflection moves across the glass while the quiet, breathy young woman (S1) says: <d>[English] I get off at the next station.</d> She folds the letter along its existing crease.

overall_soundscape: The train wheels produce a steady metallic rhythm beneath a low ventilation hum. Rain ticks against the window while paper rustles softly in her hands.

non_diegetic_music: Sustained cello notes at a slow tempo with widely spaced piano tones, gradually decreasing in volume.
케이스 3: FL2VA
두 이미지가 각각 시작과 끝의 기준점 역할을 합니다. 본문에서 정적인 두 이미지 설명을 반복해서는 안 되며, 대신 두 이미지를 연결하는 움직임 경로를 제공해야 합니다. 다음 예시는 8초짜리 단일 샷입니다.

Plaintext
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 1) aligns with the 8.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a rain-soaked cyclist begins in the position and framing established by Picture 1, holding a closed black umbrella beside a silver bicycle. The camera pulls out with small amplitude at slow speed as she releases the bicycle handle, raises the umbrella above her shoulder, and presses the runner upward until the canopy opens. Water rolls from the expanding fabric while she steps beneath it, rotates the handle into the final angle, and settles into the pose, spacing, and composition established by Picture 2 at the end of the shot.

overall_soundscape: Rain falls steadily on the pavement, followed by the metallic click of the umbrella runner and the soft snap of the canopy opening. Water drips from the bicycle frame as distant traffic passes.

non_diegetic_music: N/A
케이스 4: L2VA
이미지가 마지막 순간만을 고정합니다. 먼저 부합하는 이전 상태를 설정한 다음, 동작, 물체의 상태, 구도가 마지막 샷에서 gradual하게 Picture 1에 착지하도록 하세요. 다음 예시는 6초짜리 단일 샷입니다.

Plaintext
How the reference pictures align with the target video — <Picture 1> (from [Shot 1]) aligns with the 6.00-second mark of the target video.

integrated_multimodal_description: [Shot 1] Live-action, cinematic, a close shot begins with an intact drinking glass near the edge of a dark wooden table, while the same hand and sleeve visible in <Picture 1> approach from the right. The camera pushes in with small amplitude at slow speed as the fingertips strike the rim. The glass tips, falls, and hits the floor with a sharp impact; cracks spread through it as fragments slide outward. Toward the end, the moving pieces lose momentum and settle into the exact broken arrangement, hand position, camera angle, lighting, and final composition established by <Picture 1>.

overall_soundscape: Fingertips tap the glass before it scrapes across the tabletop, falls, and breaks with a sharp crash. Small fragments scatter and gradually stop sliding across the floor.

non_diegetic_music: A low electronic pulse at a slow tempo, ending immediately after the glass breaks.
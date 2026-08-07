# 비디오 프롬프트 작성 가이드 (T2VA / I2VA / FL2VA / L2VA)

## 1. 작업 개요

- **T2VA**: 텍스트만을 기반으로 완전한 시청각 타임라인을 생성합니다.
- **I2VA**: T2VA 본문에 **첫 번째 프레임 지시문**을 추가하고, 첫 프레임을 시작점으로 이후 장면이 자연스럽게 전개되는 시각적 흐름을 작성합니다.
- **FL2VA**: T2VA 본문에 **첫 번째 프레임과 마지막 프레임 지시문**을 추가하고, 첫 프레임에서 마지막 프레임까지 연속적으로 이어지는 전개 과정을 작성합니다.
- **L2VA**: T2VA 본문에 **마지막 프레임 지시문**을 추가하고, 그 마지막 프레임에 자연스럽게 도달하도록 이전 상태에서 수렴하는 전개 과정을 작성합니다.

## 2. 최종 프롬프트 구조

### 2.1 첫 번째 부분: 참조 이미지 지시문(Instruction)

**T2VA**는 이미지 정렬(Alignment) 지시문이 없으며, 바로 세 개의 핵심 필드(Core Fields)로 시작합니다.

**I2VA**는 항상 다음 형식을 사용합니다.

```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.
```

**FL2VA**는 항상 다음 형식을 사용합니다.

```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot N) aligns with the S.SS-second mark of the target video.
```

**L2VA**는 항상 다음 형식을 사용합니다.

```text
How the reference pictures align with the target video — <Picture 1> (from [Shot N]) aligns with the S.SS-second mark of the target video.
```

여기서 `N`은 실제 마지막 샷(Shot)의 번호를 의미하며, `S.SS`는 소수점 둘째 자리까지 표기한 실제 영상 길이(초)입니다.

이 지시문은 **최종 프롬프트의 첫 번째 줄**에 반드시 위치해야 하며, 이후 **한 줄을 비운 뒤** 세 개의 핵심 필드를 작성합니다.

### 2.2 두 번째 부분: 세 가지 핵심 필드(Core Fields)

```text
integrated_multimodal_description: [Shot 1] ...

overall_soundscape: ...

non_diegetic_music: ...
```

- **integrated_multimodal_description**: 영상의 시간 흐름에 따라 시각적 요소, 동작, 샷 구성, 화자(Speaker), 대사, 노래, 그리고 화면 속 세계에서 실제로 들리는 음향(Diegetic Audio)을 통합하여 기술합니다.
- **overall_soundscape**: 영상 전체에 걸쳐 들리는 주변 환경음(Ambient Sound), 물리적 동작에서 발생하는 소리, 그리고 말이 아닌 사람의 소리(숨소리, 웃음소리 등)를 요약합니다.
- **non_diegetic_music**: 등장인물은 들을 수 없고 오직 시청자만 들을 수 있는 배경음악(Non-diegetic Music)을 설명합니다.


## 3. 멀티모달 설명에 키프레임을 반영하는 방법

### 3.1 I2VA: 이미지에서 시작하여 앞으로 전개하기

`<Picture 1>`은 영상의 **0.00초 시점에 해당하는 실제 첫 번째 프레임**이며 `[Shot 1]`에 속합니다.

설명은 먼저 이미지에 포함된 **스타일, 피사체, 구도, 장면의 기준점(Scene Anchor)** 을 확립한 뒤, 그 다음에 이어지는 동작을 서술해야 합니다.

등장인물의 정체성, 의상, 색상, 주요 오브젝트, 그리고 공간적 배치는 영상이 진행되는 동안 일관되게 유지되어야 합니다.

권장 구조

> **첫 번째 프레임 기준점 → 동작 시작 → 연속적인 전개 → 결과 또는 반응**

---

### 3.2 FL2VA: 첫 번째 프레임과 마지막 프레임 사이의 변화 과정 서술

Picture 1은 영상의 시작이며, Picture 2는 영상의 끝입니다.

설명의 핵심은 다음과 같습니다.

- 피사체가 어떻게 움직이는가
- 자세가 어떻게 변화하는가
- 사물이 어떻게 조작되는가
- 화면 구도가 어떻게 변하는가
- 장면이나 조명이 어떻게 전환되는가

FL2VA는 일반적으로 **하나의 Shot(Single Shot)** 을 사용하는 것이 가장 적합합니다. 이렇게 하면 모델이 첫 번째 프레임부터 마지막 프레임까지 자연스럽게 중간 과정을 보간(interpolate)할 수 있습니다.

여러 Shot은 사용자가 명시적으로 요구한 경우에만 사용하며, 마지막 프레임은 반드시 영상의 마지막 `[Shot N]`에서 도달해야 합니다.

권장 구조

> **첫 프레임 상태 → 관찰 가능한 중간 변화 → 점진적으로 줄어드는 차이 → 마지막 프레임 상태**

---

### 3.3 L2VA: 시작 상태를 추론하여 마지막 이미지에 도달하기

`<Picture 1>`은 **영상의 마지막 프레임**이며 마지막 `[Shot N]`에 속합니다.

즉, 이 이미지는 Shot 1의 시작 장면이 아닙니다.

사용자의 의도와 마지막 프레임을 바탕으로 자연스러운 이전 상태를 추론한 뒤,

- 등장인물
- 오브젝트
- 카메라
- 장면

이 참조 이미지에 점진적으로 수렴하도록 서술해야 합니다.

권장 구조

> **가능성 있는 이전 상태 → 명확한 동작과 전환 과정 → 마지막 Shot에서 점진적 수렴 → 마지막 프레임 도달**

---

## 4. 공통 핵심 섹션(Core Sections) 작성 방법

## 4.1 타임라인을 따라 멀티모달 설명 작성하기

`integrated_multimodal_description`는 최종 프롬프트의 **핵심 본문**입니다.

여기에 포함되는 모든 내용은 실제로 **보이거나 들리는 정보**여야 합니다.

예를 들면 다음과 같습니다.

- 영상 스타일
- 초기 구도
- 등장인물의 외형과 위치
- 장면 및 주요 소품
- 동작과 반응
- Shot 전환
- 대사
- 화면과 동기화된 다이제틱 사운드(Diegetic Sound)

`[Shot 1]`의 시작에서는 반드시 **전체 영상의 스타일과 초기 구도**를 먼저 명시합니다.

대표적인 스타일은 다음과 같습니다.

- `Cinematic`
- `live-action`
- `2D-animated`
- `3D CG`
- `claymation`
- `watercolor`
- `vintage film`

키프레임 기반 작업에서는 스타일을 **참조 이미지**에서 추론하고,

T2VA에서는 **사용자가 제공한 텍스트**를 기준으로 선택합니다.

```text
[Shot 1] Live-action, cinematic, a medium-wide shot frames...
```

---

## 4.2 Shot과 Cut 작성

첫 번째 Shot에는 **타임스탬프를 추가하지 않습니다.**

두 번째 Shot부터는 순차적인 Shot 번호를 사용하며,

각 Shot은 영상 길이 안에서 **이전보다 증가한 Cut 시각**으로 시작해야 합니다.

```text
[Shot 2] At 00:03.500, the camera cuts to...
```

일반적인 장면 전환에는 다음 표현을 사용합니다.

- `the camera cuts to`
- `the shot cuts to`
- `the shot transitions to`
- `the shot changes to`
- `the shot switches to`

사용자가 명시적으로 요청한 경우에는

- cross-dissolve
- fade
- wipe

등의 전환도 사용할 수 있습니다.

Cut은 반드시 다음 중 하나 이상의 새로운 정보를 제공해야 합니다.

- 피사체
- 공간
- 상태
- 시점
- 시간

단순히 카메라 거리나 각도만 조금 바뀌는 경우에는 Cut보다 **카메라 움직임(Camera Motion)** 을 사용하는 것이 좋습니다.

---

## 4.3 카메라 움직임(Camera Motion)

### 구성 요소

완전한 카메라 움직임은 세 가지 요소로 구성됩니다.

1. **Motion Type**
   - 카메라가 어떻게 움직이는지

2. **Amplitude**
   - 구도가 얼마나 크게 변하는지

3. **Speed**
   - 움직임의 속도

Amplitude와 Speed는 의미가 있을 때만 추가하며,

중간 정도의 변화와 일반적인 속도는 보통 생략합니다.

| 구분 | 표현 | 의미 |
|------|------|------|
| Motion Type | `Zoom In / Zoom Out` | 카메라는 고정된 상태에서 초점거리만 변경 |
| Motion Type | `Push In / Pull Out` | 카메라 자체가 전진 / 후진 |
| Motion Type | `Pan Left / Pan Right` | 제자리에서 좌우 회전 |
| Motion Type | `Truck Left / Truck Right` | 카메라가 수평 이동 |
| Motion Type | `Tilt Up / Tilt Down` | 제자리에서 상하 회전 |
| Motion Type | `Pedestal Up / Pedestal Down` | 카메라 전체가 위/아래로 이동 |
| Motion Type | `Arc Shot` | 피사체를 중심으로 원호 이동 |
| Motion Type | `Tracking Shot` | 움직이는 피사체를 따라감 |
| Motion Type | `Static Shot` | 카메라 완전 고정 |
| Motion Type | `Shake Slightly / Shake Strongly` | 약한/강한 흔들림 |
| Motion Type | `POV` | 피사체 시점 |
| Motion Type | `Roll Clockwise / Roll Counterclockwise` | 렌즈 축을 중심으로 회전 |
| Amplitude | `with small amplitude` | 작은 변화 |
| Amplitude | `with large amplitude` | 큰 변화 |
| Speed | `at slow speed` | 느리게 |
| Speed | `at fast speed` | 빠르게 |

카메라 움직임은 문장 끝에 라벨처럼 나열하지 말고,

**Shot 안에서 자연스러운 영어 문장으로 작성해야 합니다.**

```text
The camera pushes in with small amplitude at slow speed toward the folded letter in her hands.

The camera pans right with large amplitude at fast speed, revealing the open doorway.

The camera holds a static shot as the runner exits the frame.
```

---

## 4.4 화자(Speaker), 대사(Dialogue), 노래(Singing)

말하거나 노래하거나 화면 밖에서 음성을 내는 인물은 `(S1)`, `(S2)`와 같은 **고정된 Speaker ID**를 사용합니다.

이미 번호가 부여된 여러 화자가 함께 말하거나 노래하는 경우에는 `(S1,S2)`처럼 복합 ID를 사용합니다.

동일한 화자는 Shot이 바뀌어도 같은 ID를 유지합니다.

한 번도 말하지 않는 등장인물은 Speaker ID를 부여하지 않습니다.

화자가 처음 등장할 때에는 다음과 같은 정보를 충분히 제공하여 고유한 화자임을 식별할 수 있어야 합니다.

- 인물 유형
- 나이
- 성별
- 화면 안/밖 여부
- 음높이
- 음색
- 말하는 속도
- 억양(Accent)

화자의 설명(ID, 행동, 전달 방식)은 `<d>` 태그 밖에 작성합니다.

`<d>` 안에는 **언어 태그와 사용자가 제공한 실제 대사만** 넣습니다.

대사는 **원문과 구두점을 그대로 유지**하며 절대 번역하거나 수정하지 않습니다.

```text
The young woman with a quiet, breathy voice (S1) says:
<d>[English] I get off at the next station.</d>

The two children (S1,S2) shout together,
<d>[English] Wait for us!</d>
```

화면 밖 내레이션(Voiceover)은 반드시 다음 표현을 사용합니다.

> `says in an off-screen voiceover`

또한 Voiceover 뒤에는 화면 속 인물의 입이 움직이지 않는다는 사실을 반드시 명시합니다.

```text
The man (S1) says in an off-screen voiceover:
<d>[English] I still remember that road.</d>
while his lips remain completely closed.
```

동일한 대사나 노래가 Cut을 넘어 이어지는 경우에는 `<scenetrans>`를 사용하고,

오디오가 장면 전환 이후에도 계속 이어진다는 점을 명시합니다.

영상이 끝나면서 대사가 잘리는 경우에는 `<cutoff>`를 사용합니다.

---

## 4.5 화면에 보이는 텍스트(On-Screen Text)

배너, 간판, 라벨, 자막, 네온사인 등

**실제로 화면에 보이는 모든 텍스트**는 영어 큰따옴표(`" "`) 안에 작성합니다.

텍스트는 번역하지 않고 원문 그대로 유지합니다.

```text
A red neon sign reading "营业中" glows above the doorway.
```

---

## 4.6 overall_soundscape

영상 전체의

- 환경음
- 동작 소리
- 비언어적 사람 소리

를 하나의 문단(1~4개의 영어 문장)으로 요약합니다.

예시

- 바람
- 비
- 교통 소리
- 발자국
- 옷감 마찰
- 충돌음
- 숨소리
- 웃음
- 헐떡임

대사, 노래, 다이제틱 음악은 이미 `integrated_multimodal_description`에 포함되므로 이곳에서 반복하지 않습니다.

영상 전체가 완전한 무음일 때만 `N/A`를 사용합니다.

```text
overall_soundscape:
Steady rain taps against the café windows while low room ambience continues underneath.
The entrance bell rings once, followed by wet footsteps and the soft scrape of a chair.
```

---

## 4.7 non_diegetic_music

등장인물은 들을 수 없고

**오직 시청자만 들을 수 있는 배경음악**을

1~3개의 영어 문장으로 설명합니다.

다음 요소를 중심으로 작성합니다.

- 악기 구성
- 템포
- 리듬
- 음량 변화

추상적인 분위기 표현이나 음악의 감정적 역할은 설명하지 않습니다.

등장인물이 실제로 들을 수 있는

- 노래
- 악기 연주
- 라디오
- TV
- 휴대전화 음악

은 모두 **Diegetic Audio**에 해당하므로 멀티모달 설명에 작성합니다.

배경음악이 없다면 `N/A`를 사용합니다.

```text
non_diegetic_music:
Sparse piano notes at a slow tempo,
joined by sustained low strings that gradually increase in volume before fading out.
```


## 5. 예제(Cases)

## Case 1: T2VA

참조 이미지가 없는 경우에는 **텍스트만을 기반으로 전체 영상의 타임라인을 구성**합니다.

사용자의 의도와 일치하는 범위 내에서 장면(Scene), 등장인물(Character), 행동(Action), 사운드(Sound) 등의 세부 요소를 자유롭게 추가할 수 있습니다.

```text
integrated_multimodal_description:
[Shot 1] Live-action, cinematic, a medium-wide shot frames a baker opening the shutters of a small street bakery before sunrise. The camera pushes in with small amplitude at slow speed as the middle-aged baker with a calm, slightly raspy voice (S1) places a fresh loaf on the wooden counter and says: [English] First batch of the morning.

[Shot 2] At 00:05.000, the camera cuts to a close-up of steam rising from the sliced bread while the baker's final words carry over from the previous shot.

overall_soundscape:
Wooden shutters scrape open over a quiet street as trays clink softly inside the bakery. The doorbell rings once, followed by light footsteps and the crisp sound of bread being sliced.

non_diegetic_music:
A soft acoustic-guitar pattern at a moderate tempo, joined by sparse upright-bass notes and a gentle fade at the end.
```

---

## Case 2: I2VA

먼저 **첫 번째 프레임 지시문(Instruction)** 을 작성한 뒤,

Picture 1에 포함된 **피사체, 화면 구도, 장면(Scene)** 을 `[Shot 1]`의 시작 상태로 삼아 이후 장면이 어떻게 이어지는지를 설명합니다.

```text
For the target video, at 0.00 seconds into the target video, <Picture 1> (from [Shot 1]) is fully referenced.

integrated_multimodal_description:
[Shot 1] Live-action, cinematic, the young woman shown in <Picture 1> remains beside the rain-covered train window, preserving her appearance, clothing, seat position, and the carriage layout. The camera trucks right with small amplitude at slow speed as she lifts her gaze from the folded letter toward the passing city lights. Her reflection moves across the glass while the quiet, breathy young woman (S1) says: [English] I get off at the next station. She folds the letter along its existing crease.

overall_soundscape:
The train wheels produce a steady metallic rhythm beneath a low ventilation hum. Rain ticks against the window while paper rustles softly in her hands.

non_diegetic_music:
Sustained cello notes at a slow tempo with widely spaced piano tones, gradually decreasing in volume.
```

---

## Case 3: FL2VA

두 개의 참조 이미지는 각각 **영상의 시작과 끝**을 고정하는 기준점입니다.

본문에서는 두 장의 정적인 이미지를 단순히 반복해서 설명하는 것이 아니라,

**두 이미지를 연결하는 움직임과 변화 과정**을 서술해야 합니다.

아래 예시는 **8초 길이의 단일 Shot**입니다.

```text
How the reference pictures align with the target video — Picture 1 (from Shot 1) aligns with the 0.00-second mark of the target video; Picture 2 (from Shot 1) aligns with the 8.00-second mark of the target video.

integrated_multimodal_description:
[Shot 1] Live-action, cinematic, a rain-soaked cyclist begins in the position and framing established by Picture 1, holding a closed black umbrella beside a silver bicycle. The camera pulls out with small amplitude at slow speed as she releases the bicycle handle, raises the umbrella above her shoulder, and presses the runner upward until the canopy opens. Water rolls from the expanding fabric while she steps beneath it, rotates the handle into the final angle, and settles into the pose, spacing, and composition established by Picture 2 at the end of the shot.

overall_soundscape:
Rain falls steadily on the pavement, followed by the metallic click of the umbrella runner and the soft snap of the canopy opening. Water drips from the bicycle frame as distant traffic passes.

non_diegetic_music:
N/A
```

---

## Case 4: L2VA

참조 이미지는 **영상의 마지막 순간만을 고정**합니다.

먼저 마지막 프레임과 자연스럽게 이어질 수 있는 이전 상태를 설정한 뒤,

행동, 사물의 상태 변화, 화면 구도가 점진적으로 변화하여

최종적으로 **Picture 1과 완전히 일치하는 마지막 프레임**에 도달하도록 작성합니다.

아래 예시는 **6초 길이의 단일 Shot**입니다.

```text
How the reference pictures align with the target video — <Picture 1> (from [Shot 1]) aligns with the 6.00-second mark of the target video.

integrated_multimodal_description:
[Shot 1] Live-action, cinematic, a close shot begins with an intact drinking glass near the edge of a dark wooden table, while the same hand and sleeve visible in <Picture 1> approach from the right. The camera pushes in with small amplitude at slow speed as the fingertips strike the rim. The glass tips, falls, and hits the floor with a sharp impact; cracks spread through it as fragments slide outward. Toward the end, the moving pieces lose momentum and settle into the exact broken arrangement, hand position, camera angle, lighting, and final composition established by <Picture 1>.

overall_soundscape:
Fingertips tap the glass before it scrapes across the tabletop, falls, and breaks with a sharp crash. Small fragments scatter and gradually stop sliding across the floor.

non_diegetic_music:
A low electronic pulse at a slow tempo, ending immediately after the glass breaks.
```
# Dolly — No-Train AI & Technical Stack

**Project:** Dolly — Autonomous Production Director  
**Event:** AdventureX 2026  
**Version:** v1.0  
**Research date:** 2026-07-20  
**Core rule:** **Ямар ч model-ийг сууриас нь train хийхгүй.** Pretrained model + classical CV metric + deterministic state machine ашиглана.

---

## 0. Final decision

Dolly-г dataset цуглуулах, label хийх, epoch train хийх research project болгохгүй.

MVP нь:

- pretrained person detection/tracking;
- pretrained face landmark;
- pretrained voice activity detection;
- pretrained speech-to-text;
- optional pretrained active-speaker detection;
- rule-based camera scoring;
- OBS WebSocket live switching/recording;
- FFmpeg/Kinocut local editing;
- OpenTimelineIO professional handoff;
- Figma UX Workflow behavior source of truth;

ашиглана.

### Бид хийхгүй зүйл

- custom dataset үүсгэх;
- video annotate хийх;
- YOLO train/fine-tune хийх;
- Whisper fine-tune хийх;
- “Dolly” wake-word model train хийх;
- active-speaker model train хийх;
- shot-quality neural model train хийх;
- video editor сууриас нь хийх;
- frame бүр дээр LLM ажиллуулах.

Threshold/config тааруулах бол model training биш.

---

# 1. Figma source of truth

Dolly-ийн бүх шийдвэр дараах 3 эх сурвалжтай нийцнэ:

1. **UX Workflow** — flow, state, voice command, screen behavior.
2. **Design System** — color, typography, spacing, component, surface.
3. **Branding Deck** — tone, naming, messaging, visual identity.

```text
Behavior / state / transition -> UX Workflow
UI implementation             -> Design System
Copy / pitch / brand           -> Branding Deck
```

## Creator-ийн гараар хийх 5 зүйл

1. Open Dolly.
2. Connect cameras.
3. Place cameras.
4. Speak commands.
5. Review and approve.

## Canonical voice commands

```text
Dolly, action
Dolly, again
Dolly, hold
Dolly, cut
```

Энэ 4 command бол stretch биш, core MVP.

---

# 2. Final architecture

```text
CAM A ─┐
CAM B ─┼─> Frame Capture ─> Perception ─> Per-camera metrics ─┐
CAM C ─┘                                                     │
                                                            ├─> Director Engine
MIC ─────> VAD ─> STT ─> Command Parser ─> State Machine ───┤
MIC ─────> Audio Activity ─> Active Speaker Fusion ─────────┤
                                                            │
                                                            └─> OBS Controller
                                                                  ├─ scene switching
                                                                  ├─ virtual crop/zoom
                                                                  ├─ record/pause/stop
                                                                  └─ program/ISO files

Session events + transcript + camera metrics
                    │
                    v
          Post-production Planner
                    │
                    v
        Kinocut / FFmpeg / OTIO
                    │
                    v
      Draft -> Review -> Revise -> Export
```

## Runtime decision

Hackathon MVP-г microservice болгож тараахгүй.

```text
OBS Studio              external process
Python FastAPI backend  AI + logic + integrations
Next.js dashboard       Dolly UI
Local filesystem        recordings + metadata + drafts
```

Light-ASD dependency conflict гарвал л тусдаа process болгоно.

---

# 3. Complete stack table

| Capability | Primary | Backup | Train? | MVP? |
|---|---|---|---:|---:|
| Person detection | YOLO26n pretrained | MediaPipe Pose | No | Yes |
| Tracking | ByteTrack / BoT-SORT | OpenCV tracker | No | Yes |
| Face landmarks | MediaPipe Face Landmarker | face detector only | No | Yes |
| Mouth motion | MediaPipe landmarks/blendshapes | optical flow | No | Yes |
| Active speaker | VAD + mouth-motion fusion | Light-ASD/LR-ASD pretrained | No | Yes |
| Voice activity | Silero VAD ONNX | whisper.cpp VAD | No | Yes |
| Speech-to-text | faster-whisper `tiny.en` | whisper.cpp `tiny.en` | No | Yes |
| Command parsing | exact matcher + state validator | controlled fuzzy aliases | No | Yes |
| Voice confirmation | Windows SAPI / `pyttsx3` | prerecorded WAV | No | Yes |
| Shot quality | OpenCV metrics | fixed camera priority | No | Yes |
| Camera decision | weighted rules | active-speaker-only | No | Yes |
| OBS control | obs-websocket 5.x | hotkeys | No | Yes |
| Virtual PTZ | OBS transforms/crop | static scenes | No | Yes |
| Physical PTZ | vendor SDK only | none | No | Stretch |
| Editing | Kinocut + FFmpeg | FFmpeg scripts | No | Yes |
| Captions | faster-whisper | none | No | Should |
| Timeline handoff | OpenTimelineIO | EDL/FCPXML | No | Stretch |
| Dashboard | Next.js/React | Vite/React | No | Yes |
| Backend | FastAPI/WebSocket | Node | No | Yes |
| LLM | not in realtime loop | post-edit helper only | No | Optional |

---

# 4. Vision models

## 4.1 Person detection — YOLO26n

Official pretrained `yolo26n.pt` ашиглана. COCO `person` class л авна.

```python
from ultralytics import YOLO

model = YOLO("yolo26n.pt")
results = model.track(
    source=0,
    classes=[0],
    tracker="bytetrack.yaml",
    persist=True,
    verbose=False,
)
```

### Яагаад

- pretrained weight бэлэн;
- predict/track mode бэлэн;
- ByteTrack/BoT-SORT дэмждэг;
- custom dataset хэрэггүй;
- nano model тул MVP-д тохиромжтой.

### Tracker

Эхлээд:

```text
ByteTrack
```

Exact camera/test footage дээр BoT-SORT-тай харьцуулж сонгоно.

### License warning

YOLO26 нь AGPL-3.0/Enterprise нөхцөлтэй. Hackathon open-source prototype-д ашиглаж болно гэж шууд хуульчилж хэлэхгүй; submission болон future commercial product дээр license review хийнэ. Closed-source startup хувилбарт enterprise license эсвэл өөр detector хэрэгтэй байж болно.

---

## 4.2 Face analysis — MediaPipe Face Landmarker

Use cases:

- face position;
- landmarks;
- mouth opening/motion;
- head orientation;
- face visibility;
- framing;
- optional blendshapes.

```python
FaceLandmarkerOptions(
    running_mode=RunningMode.LIVE_STREAM,
    num_faces=4,
    min_face_detection_confidence=0.5,
    min_face_presence_confidence=0.5,
    min_tracking_confidence=0.5,
    output_face_blendshapes=True,
)
```

### Mouth motion

```text
mouth_open(t) = vertical_lip_distance / face_height
mouth_motion(t) = EMA(abs(mouth_open(t) - mouth_open(t-1)))
```

Starting config:

```yaml
mouth_motion:
  ema_alpha: 0.35
  minimum_activity: 0.015
```

---

## 4.3 Active-speaker detection

### MVP: pretrained VAD + mouth-motion heuristic

1. Silero VAD speech байгаа эсэхийг хэлнэ.
2. MediaPipe хүн бүрийн mouth motion гаргана.
3. Face visibility/size нэмнэ.
4. Previous speaker bonus ашиглана.

```text
if VAD == false:
    all speaker scores = 0

speaker_score(person_i) =
    0.60 * normalized_mouth_motion
  + 0.15 * face_visibility
  + 0.10 * face_size
  + 0.15 * previous_speaker_bonus
```

```yaml
active_speaker:
  candidate_hold_ms: 450
  switch_margin: 0.12
  previous_speaker_bonus: 0.15
  lost_timeout_ms: 900
```

### Яагаад энэ MVP дээр зөв вэ

- zero training;
- explainable;
- low integration risk;
- two-person interview/podcast demo-д хангалттай;
- bug debugging амар.

### Optional upgrade: Light-ASD / LR-ASD

Pretrained weights-тэй. Separate process хэлбэрээр ажиллуулна:

```text
face crops + synchronized audio
-> Light-ASD
-> person_id + speaking_probability
-> Director Engine fusion
```

Core demo Light-ASD-аас хамаарахгүй.

**Final:**

```text
MVP     = Silero VAD + MediaPipe mouth motion
Stretch = pretrained Light-ASD/LR-ASD
Training/fine-tuning = none
```

---

# 5. Voice command system

## 5.1 Pipeline

```text
Microphone
-> 16 kHz mono
-> Silero VAD
-> faster-whisper tiny.en
-> normalize
-> exact command matcher
-> state validator
-> OBS action
-> spoken + on-screen confirmation
```

## 5.2 Silero VAD

Recommended:

```text
Sample rate: 16 kHz
Frame: 512 samples
Backend: ONNX Runtime
```

Reasons:

- lightweight;
- streaming;
- local;
- MIT;
- no cloud/API key.

## 5.3 Speech-to-text — faster-whisper

Primary:

```text
tiny.en
```

Stronger hardware:

```text
base.en
```

```python
from faster_whisper import WhisperModel

model = WhisperModel(
    "tiny.en",
    device="cpu",
    compute_type="int8",
)

segments, info = model.transcribe(
    audio,
    language="en",
    beam_size=1,
    vad_filter=False,
    condition_on_previous_text=False,
    hotwords="Dolly action again hold cut",
)
```

NVIDIA GPU:

```python
model = WhisperModel("tiny.en", device="cuda", compute_type="float16")
```

Backup: `whisper.cpp`.

Handy-г full app хэлбэрээр embed хийхгүй. Handy-ийн local VAD + STT architecture-г reference болгоно.

## 5.4 Custom wake-word train хийхгүй

“Dolly” wake-word model train хийхийн оронд:

1. VAD speech segment барина.
2. STT text гаргана.
3. Parser `dolly` байгаа эсэхийг шалгана.
4. Дараагийн үг зөв command эсэхийг шалгана.
5. State machine legal эсэхийг шалгана.

## 5.5 Exact parser

```python
import re

ALIASES = {
    "dolly action": "ACTION",
    "dolly again": "AGAIN",
    "dolly hold": "HOLD",
    "dolly cut": "CUT",
}

def normalize_command(text: str) -> str | None:
    cleaned = re.sub(r"[^a-z ]", "", text.lower())
    cleaned = " ".join(cleaned.split())
    return ALIASES.get(cleaned)
```

Unrestricted sentence MVP-д авахгүй.

## 5.6 State machine

```text
STANDBY
  ACTION -> RECORDING

RECORDING
  AGAIN  -> RECORDING + new take marker
  HOLD   -> HOLDING
  CUT    -> WRAPPED

HOLDING
  ACTION -> RECORDING
  CUT    -> WRAPPED

WRAPPED
  production commands rejected
```

## 5.7 Command action

### Dolly, action

STANDBY үед:

- OBS start record;
- take 1;
- auto switch ON;
- virtual PTZ ON;
- state `RECORDING`.

HOLDING үед:

- resume record;
- auto switch resume;
- state `RECORDING`.

```text
Voice: “Action. Recording.”
UI: Dolly heard “Action” · REC
```

### Dolly, again

RECORDING үед л:

- current take close;
- retry mark;
- new take marker;
- footage delete хийхгүй;
- OBS recording continuous байж болно.

```text
Voice: “Again. New take.”
UI: Take 03 started · Nothing deleted
```

### Dolly, hold

- OBS pause;
- switching freeze;
- camera feeds/setup live;
- state `HOLDING`.

```text
Voice: “Holding.”
UI: HOLDING · Setup stays live
```

### Dolly, cut

RECORDING/HOLDING үед:

- stop recording;
- close session;
- persist event metadata;
- state `WRAPPED`;
- editing job trigger.

```text
Voice: “Cut. That’s a wrap.”
UI: Dolly is editing
```

## 5.8 Safety

- wake word mandatory;
- exact grammar;
- current state validation;
- CUT өндөр confidence;
- duplicate debounce;
- every command spoken + on-screen confirmation;
- uncertain text execute хийхгүй.

```yaml
voice:
  general_confidence: 0.72
  cut_confidence: 0.86
  debounce_ms: 1200
  max_segment_seconds: 3.5
  language: en
```

## 5.9 TTS

Primary:

```text
Windows SAPI via pyttsx3
```

Most reliable fallback:

```text
4 prerecorded WAV confirmations
```

---

# 6. Camera framing and virtual PTZ

## MVP decision

Physical gimbal control дээр demo-г бүү тулгуурлуул.

Use:

- OBS crop;
- scale;
- scene-item transform;
- virtual pan;
- digital zoom.

```text
target_x = face_center_x
target_y = face_center_y - headroom_offset
target_scale = desired_face_height / observed_face_height
```

Smoothing:

```text
smoothed = alpha * new + (1-alpha) * previous
```

```yaml
virtual_ptz:
  position_alpha: 0.18
  scale_alpha: 0.12
  dead_zone_px: 18
  max_zoom_per_second: 0.20
  target_face_height_ratio: 0.27
```

## obs-face-tracker

Use as:

- reference;
- baseline;
- single-person backup.

Main dependency болгож болохгүй. Reported limitations:

- high CPU;
- gradual memory increase;
- jitter/vibration;
- multiple faces дээр weak;
- GPLv2.

---

# 7. Camera quality and director logic

No neural shot-quality model.

## Per-camera signals

### Subject

- active speaker visible;
- tracked subject visible;
- face confidence;
- person confidence;
- subject size.

### Composition

- rule-of-thirds distance;
- headroom;
- face crop safety;
- shoulder visibility;
- crop stability.

### Technical quality

- sharpness;
- brightness;
- over/under exposure;
- motion blur;
- obstruction;
- frame stability.

### Continuity

- minimum shot duration;
- current camera bonus;
- recent camera penalty;
- no rapid ping-pong switching.

## OpenCV metrics

Blur:

```python
sharpness = cv2.Laplacian(gray, cv2.CV_64F).var()
```

Brightness:

```python
brightness = gray.mean() / 255.0
```

Clipping:

```python
underexposed = (gray < 12).mean()
overexposed = (gray > 243).mean()
```

## Camera score

```text
camera_score =
    0.34 * active_speaker_score
  + 0.20 * composition_score
  + 0.14 * face_visibility
  + 0.10 * sharpness_score
  + 0.08 * exposure_score
  + 0.08 * stability_score
  + 0.06 * continuity_score
```

## Hysteresis

```text
switch only when:
candidate_score > current_score + margin
AND candidate stays better long enough
AND minimum shot duration passed
```

```yaml
director:
  switch_margin: 0.12
  candidate_hold_ms: 650
  minimum_shot_ms: 2500
  emergency_switch_ms: 350
  tracking_lost_ms: 900
  recent_camera_penalty_ms: 4500
```

## Wide fallback

Always have `CAM_A` wide safety shot.

Fallback when:

- speaker uncertain;
- all tracking lost;
- camera disconnect;
- framing collapses;
- backend exception;
- overlapping subjects.

---

# 8. Room map MVP

Full 3D SLAM хийхгүй.

Dolly detects:

- connected camera count;
- person visible in each;
- field-of-view overlap;
- wide/close role;
- duplicated angle.

Recommendation:

```text
CAM A -> wide safety
CAM B -> medium left
CAM C -> medium right / close-up
```

Room map UI:

- camera thumbnails;
- suggested role labels;
- rough placement diagram;
- coverage warnings;
- Scan again;
- I’ve placed them.

Optional no-training calibration:

- ArUco/AprilTag;
- manual room corners;
- camera role selector.

Centimeter-accurate reconstruction гэж claim хийхгүй.

---

# 9. OBS integration

OBS WebSocket нь OBS Studio 28+ дотор default орсон.

```text
ws://127.0.0.1:4455
```

Password заавал асаана.

Python client:

```powershell
pip install obsws-python
```

## Scene structure

```text
SCENE_WIDE
  CAM_A

SCENE_LEFT
  CAM_B

SCENE_RIGHT
  CAM_C

SCENE_FAILSAFE
  CAM_A + Dolly status overlay
```

## Required actions

- connect/auth;
- get record state;
- start record;
- pause/resume;
- stop record;
- switch program scene;
- scene-item transform;
- filter enable/disable;
- source state;
- disconnect/record events.

```python
import obsws_python as obs

client = obs.ReqClient(
    host="localhost",
    port=4455,
    password="CHANGE_ME",
    timeout=3,
)

client.start_record()
client.set_current_program_scene("SCENE_WIDE")
client.toggle_record_pause()
client.stop_record()
```

Installed version дээр exact method name test хийнэ.

## Recording

Best:

- program output;
- ISO file per camera;
- clean mic;
- JSON event log.

Fallback:

- program output only;
- command/take timestamp;
- simple draft.

---

# 10. Session storage

```text
sessions/
└── 2026-07-23_14-30-12_session-001/
    ├── session.json
    ├── commands.jsonl
    ├── camera_metrics.jsonl
    ├── transcript.json
    ├── recordings/
    │   ├── program.mkv
    │   ├── cam_a.mkv
    │   ├── cam_b.mkv
    │   ├── cam_c.mkv
    │   └── mic.wav
    ├── drafts/
    │   ├── draft_01.mp4
    │   └── draft_01.otio
    └── logs/
        └── dolly.log
```

Command event:

```json
{
  "event_id": "evt_00012",
  "session_id": "session_001",
  "timestamp_ms": 84210,
  "state_before": "RECORDING",
  "command": "AGAIN",
  "transcript": "dolly again",
  "confidence": 0.94,
  "state_after": "RECORDING",
  "take_before": 2,
  "take_after": 3,
  "executed": true
}
```

Camera decision:

```json
{
  "timestamp_ms": 115300,
  "current_camera": "CAM_A",
  "candidate_camera": "CAM_B",
  "scores": {
    "CAM_A": 0.61,
    "CAM_B": 0.82,
    "CAM_C": 0.54
  },
  "reason": [
    "active speaker visible",
    "better composition",
    "minimum shot duration passed"
  ],
  "switched": true
}
```

---

# 11. Post-production

## Kinocut + FFmpeg

Use for:

- probe;
- trim;
- concatenate;
- silence removal;
- captions;
- scene detection;
- local draft generation.

```powershell
pip install kinocut
kino doctor
```

Optional extras only after core works:

```powershell
pip install "kinocut[ai]"
```

## Deterministic first draft

1. Load event log.
2. ACTION/AGAIN/HOLD/CUT -> timeline ranges.
3. Preserve raw footage.
4. Retakes low priority mark.
5. Remove hold/silence gaps from default draft.
6. Program output as emergency first draft.
7. Whisper captions.
8. Export MP4.
9. Save edit metadata.

## ISO re-edit stretch

- rescore ISO feeds offline;
- smoother cut decisions;
- live program remains fallback.

## OpenTimelineIO

```powershell
pip install opentimelineio
```

Stores:

- clips;
- timing;
- tracks;
- transitions;
- markers;
- metadata;
- external media references.

DaVinci Resolve handoff exact demo machine дээр test хийнэ. Automatic import гэж урьдчилж claim хийхгүй.

---

# 12. App stack

## Backend

```text
Python 3.11
FastAPI
Uvicorn
Pydantic
WebSocket
OpenCV
NumPy
Ultralytics
MediaPipe
Silero VAD
faster-whisper
ONNX Runtime
obsws-python
Kinocut
OpenTimelineIO
```

## Frontend

```text
Next.js
React
TypeScript
Tailwind CSS
Zustand
native WebSocket
```

Frontend OBS password авахгүй. Dashboard backend-тай л ярилцана.

## State contract

```typescript
type DollyState =
  | "SETUP"
  | "STANDBY"
  | "RECORDING"
  | "HOLDING"
  | "WRAPPED"
  | "EDITING"
  | "REVIEW"
  | "ERROR";

interface DollyStatus {
  state: DollyState;
  heardText?: string;
  recognizedCommand?: "ACTION" | "AGAIN" | "HOLD" | "CUT";
  currentCamera?: string;
  takeNumber?: number;
  recordElapsedMs?: number;
  connectedCameras: CameraStatus[];
  editingProgress?: number;
  error?: DollyError;
}
```

---

# 13. Repo structure

```text
dolly/
├── apps/
│   └── dashboard/
├── backend/
│   ├── main.py
│   ├── api/
│   ├── core/
│   │   ├── config.py
│   │   ├── events.py
│   │   └── state_machine.py
│   ├── audio/
│   │   ├── capture.py
│   │   ├── vad.py
│   │   ├── transcription.py
│   │   ├── command_parser.py
│   │   └── confirmation.py
│   ├── vision/
│   │   ├── capture.py
│   │   ├── person_tracker.py
│   │   ├── face_landmarks.py
│   │   ├── active_speaker.py
│   │   └── quality.py
│   ├── director/
│   │   ├── scorer.py
│   │   ├── hysteresis.py
│   │   ├── framing.py
│   │   └── fallback.py
│   ├── integrations/
│   │   ├── obs.py
│   │   ├── kinocut.py
│   │   └── otio.py
│   ├── editing/
│   │   ├── planner.py
│   │   ├── draft.py
│   │   └── captions.py
│   └── storage/
│       ├── sessions.py
│       └── event_log.py
├── config/
│   ├── dolly.yaml
│   └── obs-scenes.example.yaml
├── models/
├── sessions/
├── scripts/
│   ├── check_environment.py
│   ├── test_voice.py
│   ├── test_cameras.py
│   ├── test_obs.py
│   └── run_demo.py
├── tests/
├── requirements-core.txt
├── requirements-asd.txt
├── .env.example
└── README.md
```

---

# 14. Dependencies

## `requirements-core.txt`

```text
fastapi
uvicorn[standard]
pydantic
pydantic-settings
python-dotenv
websockets
orjson
aiofiles
numpy
scipy
opencv-python
ultralytics
mediapipe
onnxruntime
silero-vad
faster-whisper
sounddevice
soundfile
obsws-python
pyttsx3
kinocut
opentimelineio
```

Working environment болсны дараа:

```powershell
pip freeze > requirements-lock.txt
```

## Optional `requirements-asd.txt`

```text
torch
torchvision
torchaudio
python_speech_features
scenedetect
```

Light-ASD dependency-г core environment-оос тусгаарлана.

---

# 15. Main config

```yaml
app:
  session_root: ./sessions
  log_level: INFO

audio:
  sample_rate: 16000
  channels: 1
  command_max_seconds: 3.5

vad:
  backend: silero_onnx
  threshold: 0.55
  min_speech_ms: 180
  min_silence_ms: 280

stt:
  backend: faster_whisper
  model: tiny.en
  device: cpu
  compute_type: int8
  language: en
  hotwords: "Dolly action again hold cut"

voice:
  wake_word: dolly
  general_confidence: 0.72
  cut_confidence: 0.86
  debounce_ms: 1200
  spoken_confirmation: true

vision:
  detector: yolo26n.pt
  person_class_id: 0
  tracker: bytetrack.yaml
  detection_width: 640
  face_landmarker: mediapipe
  max_faces: 4

active_speaker:
  mode: heuristic
  vad_required: true
  mouth_weight: 0.60
  visibility_weight: 0.15
  size_weight: 0.10
  previous_speaker_weight: 0.15
  candidate_hold_ms: 450
  switch_margin: 0.12

director:
  active_speaker_weight: 0.34
  composition_weight: 0.20
  face_visibility_weight: 0.14
  sharpness_weight: 0.10
  exposure_weight: 0.08
  stability_weight: 0.08
  continuity_weight: 0.06
  switch_margin: 0.12
  candidate_hold_ms: 650
  minimum_shot_ms: 2500
  tracking_lost_ms: 900
  fallback_camera: CAM_A

virtual_ptz:
  enabled: true
  position_alpha: 0.18
  scale_alpha: 0.12
  dead_zone_px: 18
  target_face_height_ratio: 0.27

obs:
  host: 127.0.0.1
  port: 4455
  password_env: OBS_WEBSOCKET_PASSWORD
  wide_scene: SCENE_WIDE
  left_scene: SCENE_LEFT
  right_scene: SCENE_RIGHT
  failsafe_scene: SCENE_FAILSAFE

editing:
  engine: kinocut
  preserve_raw_takes: true
  captions: true
  otio_export: true
```

---

# 16. Build order

## A. Demo spine

1. OBS 3 scene.
2. Backend OBS connect.
3. Dashboard connection status.
4. API-аар manual scene switch.
5. API-аар start/stop record.
6. Session log save.

**Pass:** AI-гүйгээр switching/recording ажиллана.

## B. Voice

1. mic capture;
2. Silero VAD;
3. faster-whisper;
4. exact parser;
5. state machine;
6. OBS action;
7. spoken/on-screen confirmation.

**Pass:** 4 command тус бүр 10 удаа зөв ажиллана.

## C. Tracking

1. YOLO person track;
2. MediaPipe face;
3. stable IDs;
4. virtual crop target;
5. smoothing;
6. wide fallback.

**Pass:** one-person movement severe jitter-гүй framed байна.

## D. Active speaker

1. VAD;
2. mouth motion;
3. speaker stability;
4. camera score;
5. min shot length.

**Pass:** two-person conversation enough correct switching.

## E. Editing

1. metadata;
2. take markers;
3. program recording;
4. Kinocut/FFmpeg draft;
5. captions;
6. review;
7. export.

**Pass:** `Dolly, cut` -> playable draft.

## F. Stretch

- Light-ASD/LR-ASD;
- ISO re-edit;
- OTIO/Resolve;
- room calibration;
- physical gimbal;
- social publish.

---

# 17. Team ownership

## Nero

- backend/API;
- state machine;
- OBS controller;
- dashboard wiring;
- storage;
- virtual PTZ;
- end-to-end integration;
- fallback demo.

## Ceaser

- YOLO;
- MediaPipe;
- active-speaker fusion;
- camera scoring;
- latency/stability;
- optional Light-ASD;
- runtime optimization.

## Jeboy

- Figma source of truth;
- UI/UX;
- setup/room map;
- voice confirmation copy;
- recording/review/export screens;
- demo choreography;
- pitch.

---

# 18. Testing

## Voice matrix

Test:

```text
Dolly, action
Dolly, again
Dolly, hold
Dolly, cut
```

Conditions:

- quiet;
- music;
- fan noise;
- near/far mic;
- fast/slow speech;
- accents;
- another speaker;
- command inside normal sentence.

Pass:

- valid executes;
- invalid rejected;
- every command confirmed;
- unrelated speech never triggers CUT.

## Vision files

```text
01_single_speaker.mp4
02_two_speakers.mp4
03_speaker_switching.mp4
04_low_light_blur.mp4
05_occlusion_tracking_loss.mp4
06_person_enters_exits.mp4
07_two_faces_overlap.mp4
```

## Audio files

```text
clean_audio.wav
noisy_audio.wav
overlapping_speech.wav
command_action.wav
command_again.wav
command_hold.wav
command_cut.wav
```

## Failure drills

- CAM B disconnect;
- mic unplug;
- OBS close;
- wrong WebSocket password;
- backend restart;
- tracking lost;
- STT timeout;
- disk full warning;
- editing failure.

Error copy format:

```text
CAM B isn’t answering.
Dolly switched to the wide camera.
Check CAM B’s USB-C cable.
```

---

# 19. Fallback ladder

## Level 1

```text
3 live cameras + voice + active speaker + auto switch + tracking + generated draft
```

## Level 2

```text
3 cameras + voice + rule switching + OBS recording + prepared draft result
```

## Level 3

```text
1 camera + voice + virtual pan/zoom + state UI
```

## Level 4

```text
prerecorded inputs + live decision engine + dashboard + event log
```

## Level 5

Complete backup demo video.

---

# 20. Do not add before MVP

- general chatbot;
- realtime cloud LLM;
- custom wake-word training;
- face recognition database;
- emotion recognition;
- full 3D SLAM;
- physical gimbal control;
- automatic social publishing;
- generative video;
- AI avatar;
- distributed microservices;
- payment/accounts.

---

# 21. LLM policy

LLM хэрэггүй зүйл:

- person detection;
- face tracking;
- active speaker per frame;
- live camera cut;
- exact command recognition;
- OBS control;
- deterministic edit.

Optional post-edit LLM:

- transcript summary;
- title;
- chapters;
- feedback -> structured edit instruction;
- editing report.

LLM fail болсон ч recording/switching үргэлжилнэ.

---

# 22. License notes

| Project | License/status | Use |
|---|---|---|
| Ultralytics YOLO26 | AGPL-3.0 / Enterprise | detector, commercial review |
| MediaPipe | Apache-2.0 | face/pose |
| OpenCV | Apache-2.0 | classical metrics |
| Silero VAD | MIT | VAD |
| faster-whisper | MIT | STT runtime |
| whisper.cpp | MIT | native STT backup |
| Light-ASD / LR-ASD | MIT | optional ASD |
| obs-websocket | GPL-2.0 | external OBS protocol |
| obs-websocket-js | MIT | optional JS client |
| Kinocut | Apache-2.0 | local edit |
| OpenTimelineIO | Apache-2.0 | timeline |
| obs-face-tracker | GPLv2 | reference/backup |
| Handy | MIT | voice architecture reference |

This is engineering guidance, not legal advice.

---

# 23. Final MVP story

```text
1. Creator opens Dolly.
2. Dolly detects cameras.
3. Dolly shows placement guidance.
4. Creator places cameras.
5. Dolly tracks subject and connects OBS.
6. “Dolly, action.”
7. Recording + auto switching starts.
8. “Dolly, again.”
9. New take, nothing deleted.
10. “Dolly, hold.”
11. Recording/switching pauses; setup stays live.
12. “Dolly, action.”
13. Recording resumes.
14. “Dolly, cut.”
15. Production ends.
16. Dolly organizes footage and creates draft.
17. Creator reviews, revises, or exports.
```

## Core promise

```text
No custom training.
No core cloud dependency.
No footage deletion.
No manual camera operation after placement.
No buttons during the shoot.
Every decision visible and explainable.
```

---

# 24. Primary sources

## Dolly

- Figma UX Workflow / Design System / Branding source:  
  https://www.figma.com/design/1i1rFhuvgvWqFOr9TG4YXk/Dolly---Design-System?node-id=61-1303

## Vision

- YOLO26: https://docs.ultralytics.com/models/yolo26/
- YOLO tracking: https://docs.ultralytics.com/modes/track/
- MediaPipe Face Landmarker: https://ai.google.dev/edge/api/mediapipe/python/mp/tasks/vision/FaceLandmarker
- MediaPipe: https://github.com/google-ai-edge/mediapipe
- OpenCV: https://github.com/opencv/opencv
- Light-ASD: https://github.com/Junhua-Liao/Light-ASD
- LR-ASD: https://github.com/Junhua-Liao/LR-ASD

## Voice

- Silero VAD: https://github.com/snakers4/silero-vad
- faster-whisper: https://github.com/SYSTRAN/faster-whisper
- whisper.cpp: https://github.com/ggml-org/whisper.cpp
- Handy: https://github.com/cjpais/Handy

## OBS

- OBS WebSocket: https://github.com/obsproject/obs-websocket
- obs-websocket-js: https://github.com/obs-websocket-community-projects/obs-websocket-js
- obsws-python: https://github.com/aatikturk/obsws-python
- obs-face-tracker: https://github.com/norihiro/obs-face-tracker

## Editing

- Kinocut: https://kinocut.dev/
- Kinocut install: https://kinocut.dev/install.html
- OpenTimelineIO: https://opentimelineio.readthedocs.io/
- FFmpeg: https://ffmpeg.org/documentation.html

---

# 25. One-line build rule

> **Эхлээд complete no-training demo spine-аа найдвартай болго. Дараа нь л heuristic module-уудыг хүчтэй pretrained model-оор upgrade хий.**

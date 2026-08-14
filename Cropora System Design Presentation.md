# Cropora — System Design Presentation (20 Slides)

> **What this file is.** A simplified, visual-first walkthrough of how Cropora works — built for
> explaining to teachers, peers, and non-technical audiences. Each slide is one diagram + a short caption.
>
> **How to render.** Each `## SLIDE n` block is one slide. Paste any ` ```mermaid ` block into
> [mermaid.live](https://mermaid.live), Marp, Slidev, or PowerPoint + Mermaid add-in to get
> a rendered image. The **MOTION** line describes the animation hint for tools that support it.

## Global visual language (apply to every slide)

| Meaning | Color |
|---|---|
| Android app (on device) | 🟩 Green |
| Backend API (cloud) | 🟦 Blue |
| ML model | 🟧 Orange |
| Local storage / bundled assets | ⬜ Grey |
| User / camera / network | 🟪 Purple |
| The single shared answer contract | 🟨 Gold thick border |

---

## SLIDE 1 — Title

**TEXT:**
```text
CROPORA
Plant-Disease Detection

One photo. Two engines. One answer.
```

**VISUAL:**
```mermaid
flowchart LR
    U(["🌿 Photo of leaf"]) --> APP["📱 Android App"]
    APP --> CLOUD["☁️ Cloud engine"]
    APP --> EDGE["📲 On-device engine"]
    CLOUD --> ANS(["✅ Diagnosis & care plan"])
    EDGE --> ANS

    style APP fill:#2e7d32,color:#fff
    style CLOUD fill:#1565c0,color:#fff
    style EDGE fill:#ef6c00,color:#fff
    style U fill:#6a1b9a,color:#fff
    style ANS fill:#6a1b9a,color:#fff
```

**MOTION:** Nodes fade in left → right; pulse travels Leaf → App → splits to both engines → merges into Answer.

---

## SLIDE 2 — The Problem

**TEXT:**
```text
A farmer photos a leaf.
The app must say: What disease? How sure? What to do?
— even with no internet.
```

**VISUAL:**
```mermaid
flowchart LR
    F(["👩‍🌾 Farmer"]) --> L(["🌿 Sick leaf"])
    L --> Q{{"❓ What is this?\nHow sure?\nWhat do I do?"}}
    Q --> A(["📱 Cropora answers"])

    style F fill:#6a1b9a,color:#fff
    style L fill:#6a1b9a,color:#fff
    style Q fill:#f9a825,color:#000,stroke:#b8860b,stroke-width:3px
    style A fill:#2e7d32,color:#fff
```

**MOTION:** Farmer and leaf appear → question box zooms in → answer card slides in from right.

---

## SLIDE 3 — Why It Matters

**TEXT:**
```text
Late detection = lost crops.
Cropora gives an instant answer, on-site, with or without signal.
```

**VISUAL:**
```mermaid
flowchart LR
    P1["🐌 Slow / no diagnosis"] --> P2["🌾 Crop loss"]
    S1["⚡ Instant Cropora scan"] --> S2["🌱 Early treatment"]

    style P1 fill:#c62828,color:#fff
    style P2 fill:#c62828,color:#fff
    style S1 fill:#2e7d32,color:#fff
    style S2 fill:#2e7d32,color:#fff
```

**MOTION:** Red "problem" row fades in first, then green "solution" row slides in below it.

---

## SLIDE 4 — The Big Picture

**TEXT:**
```text
Three worlds work together:
  📱 Device — captures, decides, stores
  ☁️ Cloud  — powerful inference
  📦 Assets — shared model & knowledge
```

**VISUAL:**
```mermaid
flowchart TB
    subgraph DEV["📱 Device"]
        CAP["Camera / Gallery"]
        ENG["On-device model"]
        DB[("Local storage")]
    end
    subgraph CLD["☁️ Cloud"]
        API["FastAPI service"]
        MDL["Keras model"]
    end
    subgraph AST["📦 Shared assets"]
        TFL["model.tflite"]
        KRS["cropora_model.keras"]
    end

    CAP --> ENG
    CAP -. "HTTPS upload" .-> API
    API --> MDL
    ENG --> DB
    TFL -.->|bundled| ENG
    KRS -.->|served| MDL

    style DEV fill:#e8f5e9,stroke:#2e7d32
    style CLD fill:#e3f2fd,stroke:#1565c0
    style AST fill:#fff3e0,stroke:#ef6c00
```

**MOTION:** Three containers fade in. Solid arrows (runtime) draw first; dashed arrows (build-time bundling) draw last.

---

## SLIDE 5 — Who Uses Cropora

**TEXT:**
```text
One app, no login needed, works fully offline once installed.
```

**VISUAL:**
```mermaid
flowchart LR
    U(["👩‍🌾 Farmer"]) --> APP["📱 Cropora App"]
    U2(["🧑‍🎓 Student / Agronomist"]) --> APP
    APP --> R(["✅ Diagnosis + care plan"])

    style U fill:#6a1b9a,color:#fff
    style U2 fill:#6a1b9a,color:#fff
    style APP fill:#2e7d32,color:#fff
    style R fill:#6a1b9a,color:#fff
```

**MOTION:** Both user icons fade in together → converge into app → answer pops out.

---

## SLIDE 6 — The Journey in 7 Steps

**TEXT:**
```text
Every scan follows this path:
Capture → Route → Infer → Answer → Gate → Show → Save
```

**VISUAL:**
```mermaid
flowchart LR
    S1["📸 Capture"] --> S2["🔀 Route"]
    S2 --> S3["🧠 Infer"]
    S3 --> S4["📋 Answer"]
    S4 --> S5["🚦 Gate"]
    S5 --> S6["🖥️ Show"]
    S6 --> S7["💾 Save"]

    style S1 fill:#6a1b9a,color:#fff
    style S2 fill:#2e7d32,color:#fff
    style S3 fill:#ef6c00,color:#fff
    style S4 fill:#f9a825,color:#000,stroke:#b8860b,stroke-width:3px
    style S5 fill:#2e7d32,color:#fff
    style S6 fill:#6a1b9a,color:#fff
    style S7 fill:#9e9e9e,color:#fff
```

**MOTION:** Steps pop in one by one left to right.

---

## SLIDE 7 — Step 1: Capture

**TEXT:**
```text
Photo comes from camera or gallery.
App resizes it to 224×224 before anything else happens.
```

**VISUAL:**
```mermaid
flowchart LR
    C1["📷 Camera"] --> IMG(["🖼️ Photo"])
    C2["🖼️ Gallery"] --> IMG
    IMG --> RS["📐 Resize to 224×224"]

    style C1 fill:#6a1b9a,color:#fff
    style C2 fill:#6a1b9a,color:#fff
    style IMG fill:#6a1b9a,color:#fff
    style RS fill:#2e7d32,color:#fff
```

**MOTION:** Camera and gallery icons fade in → merge into photo → resize box grows.

---

## SLIDE 8 — Step 2: Route

**TEXT:**
```text
App checks network → picks an engine.
Online? → Cloud.   Offline? → Phone answers by itself.
```

**VISUAL:**
```mermaid
flowchart TD
    U(["📸 Photo"]) --> CHK{{"📶 Network available?"}}
    CHK -- Yes --> CLD["☁️ Send to cloud"]
    CHK -- No  --> LOC["📲 Use on-device model"]

    style U fill:#6a1b9a,color:#fff
    style CHK fill:#2e7d32,color:#fff
    style CLD fill:#1565c0,color:#fff
    style LOC fill:#ef6c00,color:#fff
```

**MOTION:** Photo drops in → network check diamond appears → two branches slide out in parallel.

---

## SLIDE 9 — Step 3: The Two Inference Engines

**TEXT:**
```text
Cloud engine: FastAPI + Keras model (server-side, stateless, no image stored).
On-device engine: TFLite model memory-mapped on the phone (38 disease classes, 224×224 input).
Both produce the exact same answer format.
```

**VISUAL:**
```mermaid
flowchart LR
    IMG(["🖼️ 224×224 image"]) --> E1["☁️ Cloud\nFastAPI + Keras"]
    IMG --> E2["📲 On-device\nTFLite model"]
    E1 --> OUT{{"📋 Same answer\nboth ways"}}
    E2 --> OUT

    style IMG fill:#6a1b9a,color:#fff
    style E1 fill:#1565c0,color:#fff
    style E2 fill:#ef6c00,color:#fff
    style OUT fill:#f9a825,color:#000,stroke:#b8860b,stroke-width:4px
```

**MOTION:** Image appears → two engine boxes grow → both arrows converge onto the gold answer box, which pulses.

---

## SLIDE 10 — Inside the Cloud Engine

**TEXT:**
```text
Photo uploaded over HTTPS → FastAPI receives it → Keras model predicts →
result sent back → nothing is stored on the server.
```

**VISUAL:**
```mermaid
flowchart LR
    APP["📱 App"] -- "HTTPS upload" --> API["☁️ FastAPI"]
    API --> MDL["🧠 Keras model"]
    MDL --> API
    API -- "JSON result" --> APP
    API -. "no image saved" .-> X["🚫"]

    style APP fill:#2e7d32,color:#fff
    style API fill:#1565c0,color:#fff
    style MDL fill:#ef6c00,color:#fff
    style X fill:#9e9e9e,color:#fff
```

**MOTION:** Upload arrow draws first → model box pulses while "thinking" → result arrow returns → grey "no storage" note fades in last.

---

## SLIDE 11 — Inside the On-Device Engine

**TEXT:**
```text
model.tflite is bundled inside the app.
It loads once, then predicts instantly — no internet, no waiting.
```

**VISUAL:**
```mermaid
flowchart LR
    A["📦 model.tflite\n(bundled)"] --> B["📲 Loaded into memory"]
    B --> C["🧠 Predicts on photo"]
    C --> D(["✅ Instant result"])

    style A fill:#9e9e9e,color:#fff
    style B fill:#ef6c00,color:#fff
    style C fill:#ef6c00,color:#fff
    style D fill:#6a1b9a,color:#fff
```

**MOTION:** Bundled file icon appears → arrow flows into memory box → predicts → result pops with a spark.

---

## SLIDE 12 — Step 4: The One Shared Answer (Hero Slide)

**TEXT:**
```text
No matter which engine ran, the answer is always these 8 fields.
```

**VISUAL:**
```mermaid
flowchart TB
    CONTRACT{{"📋 PREDICTION CONTRACT\n──────────────────────\nmodel_label · disease\nconfidence · uncertain\nguidance_available\nsymptoms · treatment · prevention"}}

    CLD["☁️ Cloud engine"] --> CONTRACT
    EDG["📲 On-device engine"] --> CONTRACT

    style CONTRACT fill:#f9a825,color:#000,stroke:#b8860b,stroke-width:5px
    style CLD fill:#1565c0,color:#fff
    style EDG fill:#ef6c00,color:#fff
```

**MOTION:** Cloud and on-device boxes appear → both arrows converge → gold contract box zooms in and pulses twice.

---

## SLIDE 13 — Step 5: The Confidence Gate

**TEXT:**
```text
Low confidence? → marked "uncertain" and flagged to the user.
Default threshold: 50% (user can adjust).
```

**VISUAL:**
```mermaid
flowchart TD
    ANS(["📋 Prediction + confidence %"]) --> GT{{"🚦 Confidence ≥ threshold?"}}
    GT -- Yes  --> OK["✅ Show diagnosis"]
    GT -- No   --> UNC["⚠️ Show as uncertain"]

    style ANS fill:#f9a825,color:#000,stroke:#b8860b,stroke-width:3px
    style GT fill:#2e7d32,color:#fff
    style OK fill:#2e7d32,color:#fff
    style UNC fill:#9e9e9e,color:#fff
```

**MOTION:** Prediction card drops in → gate diamond lights up → two paths draw outward.

---

## SLIDE 14 — Step 6 & 7: Result Screen & Save

**TEXT:**
```text
Result screen shows disease name, confidence bar, symptoms, treatment, prevention.
User taps Save → stored in local database (never leaves the phone).
```

**VISUAL:**
```mermaid
flowchart LR
    RES["🖥️ Result screen\ndisease · confidence\nsymptoms · treatment\nprevention"] --> SAV{{"💾 User taps Save"}}
    SAV --> DB[("📁 Local DB\nscan_history")]

    style RES fill:#6a1b9a,color:#fff
    style SAV fill:#2e7d32,color:#fff
    style DB fill:#9e9e9e,color:#fff
```

**MOTION:** Result screen slides in → Save button pulses → arrow draws into database cylinder.

---

## SLIDE 15 — Revisit: History, Analytics & Library

**TEXT:**
```text
Saved scans power three features — all local, no account needed.
```

**VISUAL:**
```mermaid
flowchart TB
    DB[("📁 scan_history")] --> HIS["📜 History\nall past scans"]
    DB --> ANA["📊 Analytics\ncharts & trends"]
    LIB["📚 Library\nbundled diseases.xml"] --> DET["🔍 Disease details\n& guidance"]

    style DB fill:#9e9e9e,color:#fff
    style HIS fill:#2e7d32,color:#fff
    style ANA fill:#2e7d32,color:#fff
    style LIB fill:#9e9e9e,color:#fff
    style DET fill:#6a1b9a,color:#fff
```

**MOTION:** Database cylinder appears → three feature cards fan out.

---

## SLIDE 16 — Privacy by Design

**TEXT:**
```text
Photos are never stored on the cloud server.
Saved scans live only in your local database — on your phone.
```

**VISUAL:**
```mermaid
flowchart LR
    PH(["🖼️ Your photo"]) --> CLD["☁️ Cloud (predict only)"]
    CLD -. "discarded after use" .-> X["🚫 Not stored"]
    PH --> DB[("📁 Local DB\non your phone only")]

    style PH fill:#6a1b9a,color:#fff
    style CLD fill:#1565c0,color:#fff
    style X fill:#c62828,color:#fff
    style DB fill:#9e9e9e,color:#fff
```

**MOTION:** Photo appears → dashed arrow to cloud fades with a "discarded" stamp → solid arrow to local DB stays lit.

---

## SLIDE 17 — Safe Degradation

**TEXT:**
```text
If anything goes wrong, the app stays safe:
  • Cloud down → switch to on-device engine
  • Bad photo (too large / wrong format) → friendly error
  • Model missing at build time → Gradle build is blocked
```

**VISUAL:**
```mermaid
flowchart TD
    F1["❌ Cloud unavailable"] --> FB["📲 On-device fallback"]
    F2["❌ Photo too large / bad format"] --> ER["⚠️ Error message\n(400 / 413 / 503)"]
    F3["❌ Model file missing at build"] --> BLK["🚫 Gradle build blocked"]

    style F1 fill:#c62828,color:#fff
    style F2 fill:#c62828,color:#fff
    style F3 fill:#c62828,color:#fff
    style FB fill:#ef6c00,color:#fff
    style ER fill:#9e9e9e,color:#fff
    style BLK fill:#9e9e9e,color:#fff
```

**MOTION:** Each failure row fades in one at a time, each with its safety response.

---

## SLIDE 18 — The Tech Stack at a Glance

**TEXT:**
```text
Simple, proven tools — nothing exotic.
```

**VISUAL:**
```mermaid
flowchart TB
    subgraph APPSIDE["📱 App"]
        K["Kotlin / Android"]
        T["TensorFlow Lite"]
    end
    subgraph SERVERSIDE["☁️ Server"]
        F["FastAPI (Python)"]
        Ke["Keras / TensorFlow"]
    end

    APPSIDE -. "HTTPS" .-> SERVERSIDE

    style APPSIDE fill:#e8f5e9,stroke:#2e7d32
    style SERVERSIDE fill:#e3f2fd,stroke:#1565c0
    style K fill:#2e7d32,color:#fff
    style T fill:#ef6c00,color:#fff
    style F fill:#1565c0,color:#fff
    style Ke fill:#ef6c00,color:#fff
```

**MOTION:** App box and server box fade in side by side → dashed HTTPS arrow connects them last.

---

## SLIDE 19 — Full Journey in One Picture

**TEXT:**
```text
From leaf photo to saved result — the complete Cropora flow.
```

**VISUAL:**
```mermaid
flowchart LR
    U(["👩‍🌾 Farmer"]) --> CAM["📸 Photo"]
    CAM --> RT{{"📶 Online?"}}
    RT -- Yes --> CLD["☁️ Cloud\nFastAPI + Keras"]
    RT -- No  --> EDG["📲 On-device\nTFLite"]
    CLD --> CON{{"📋 8-field answer"}}
    EDG --> CON
    CON --> GT{{"🚦 Confident?"}}
    GT -- Yes --> OK["✅ Result screen"]
    GT -- No  --> UNC["⚠️ Uncertain flag"]
    OK --> DB[("💾 Local DB")]
    UNC --> DB
    DB --> HIS["📜 History"]
    DB --> ANA["📊 Analytics"]

    style U fill:#6a1b9a,color:#fff
    style CAM fill:#6a1b9a,color:#fff
    style RT fill:#2e7d32,color:#fff
    style CLD fill:#1565c0,color:#fff
    style EDG fill:#ef6c00,color:#fff
    style CON fill:#f9a825,color:#000,stroke:#b8860b,stroke-width:4px
    style GT fill:#2e7d32,color:#fff
    style OK fill:#2e7d32,color:#fff
    style UNC fill:#9e9e9e,color:#fff
    style DB fill:#9e9e9e,color:#fff
    style HIS fill:#2e7d32,color:#fff
    style ANA fill:#2e7d32,color:#fff
```

**MOTION:** Flow draws itself left to right, one node at a time, finishing with History and Analytics.

---

## SLIDE 20 — Thank You / What's Next

**TEXT:**
```text
CROPORA
One photo. Two engines. One answer.

Thank you!
Questions?
```

**VISUAL:**
```mermaid
flowchart LR
    A(["🌿 Today"]) --> B["📱 Cropora"]
    B --> C(["🚀 More crops\nmore languages\nsmarter models"])

    style A fill:#6a1b9a,color:#fff
    style B fill:#2e7d32,color:#fff
    style C fill:#f9a825,color:#000,stroke:#b8860b,stroke-width:3px
```

**MOTION:** Today fades in → app box pulses once → gold "what's next" box slides in and glows.

---

## Rendering Appendix

| Tool | How to use |
|---|---|
| **mermaid.live** | Paste any code block at [mermaid.live](https://mermaid.live) → copy as PNG/SVG |
| **Marp** | Use `marp --html` on this file; fenced mermaid blocks render automatically |
| **Slidev** | Paste blocks inside `<Mermaid>` tags or use the built-in mermaid plugin |
| **PowerPoint / Google Slides** | Render each diagram on mermaid.live → export PNG → insert as image |
| **draw.io** | File → Import → Mermaid syntax → auto-convert to editable diagram |

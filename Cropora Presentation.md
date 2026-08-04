# Cropora — Full Product Presentation Blueprint

> **What this document is.** A slide-by-slide, pipeline-oriented presentation script for
> Cropora, based only on the code and documentation present in this repository. It is intended
> as the complete input for a human designer or slide-generation agent.
>
> **Evidence rule.** This deck distinguishes implemented code from demonstrated behaviour.
> The repository does not contain `model.tflite` or the default Keras model, so it does not
> claim verified real-model inference, parity measurements, accuracy measurements, or a
> production release.

## How to use this document

1. One `## SLIDE n` heading is one slide; do not merge or split slides.
2. Every slide has **PURPOSE**, **ON-SLIDE CONTENT**, **VISUAL / DIAGRAM SPEC**,
   **SPEAKER NOTES**, and **SOURCE OF TRUTH** blocks.
3. Use green for Android components, blue for backend components, orange for model artifacts,
   and grey for local storage/assets. Solid arrows are runtime flows; dashed arrows are
   build-time or prerequisite flows.
4. Never add metrics that are not supported by this repository. Mark unknown values as `TBD`.
5. Keep the limitation slides. They are an engineering-strength statement, not an apology.

**Deck statistics:** 36 slides · 8 sections · full technical walkthrough.

| Section | Slides | Theme |
|---|---:|---|
| A. Framing | 1–5 | Product, scope, and system landscape |
| B. Master pipeline | 6–9 | End-to-end flow, orchestration, UI, repository |
| C. Inference pipelines | 10–18 | Capture, routing, cloud, offline, convergence |
| D. Post-inference pipelines | 19–24 | Results, data, history, analytics, reference, settings |
| E. Delivery | 25–27 | Model readiness, build/deployment, data residency |
| F. Engineering quality | 28–31 | Lifecycle, security, tests, limitations |
| G. Project story | 32–34 | Current implementation and roadmap |
| H. Close | 35–36 | Demo and Q&A |

---

# SECTION A — FRAMING

## SLIDE 1 — Title

**PURPOSE**  
Establish Cropora as an Android and FastAPI plant-disease application with cloud and offline
implementation paths.

**ON-SLIDE CONTENT**
```text
Cropora
Android Plant-Disease Detection

Capture or choose a leaf image, receive a predicted condition,
confidence, and care guidance.

Two implemented paths: TensorFlow Lite on device + FastAPI in the cloud
One shared prediction response contract

38 labels · 8 Activities · 5 bottom-navigation tabs

Kotlin · Android · FastAPI · TensorFlow / TensorFlow Lite · Room · Retrofit
com.cropora · v0.2.0-beta · minSdk 24 · target/compileSdk 34
```

**VISUAL / DIAGRAM SPEC**  
Use the supplied Cropora screenshot as the background, darkened for legibility. Place the
technology names in a bottom strip.

**SPEAKER NOTES**  
"Cropora is a leaf-image diagnosis assistant. The app implements two routes from image to
prediction: an on-device TensorFlow Lite route and a FastAPI route. Both aim to produce the same
response shape so the result, history, and analytics features do not need to know where a
prediction originated."

**SOURCE OF TRUTH**  
`README.md`; `android-app/app/build.gradle`; `android-app/app/src/main/AndroidManifest.xml`;
`docs/evidence/week-01/Cropora.jpg`.

---

## SLIDE 2 — How to read this deck

**PURPOSE**  
Give the audience a data-flow mental model.

**ON-SLIDE CONTENT**
```text
THIS DECK FOLLOWS ONE LEAF IMAGE

Capture → choose mode → infer → normalize/enrich → confidence gate
        → show result → optionally save → review locally

The deck separates:
  Implemented code     from     verified runtime evidence

The model artifacts required for real inference are not present in this repository.
```

**VISUAL / DIAGRAM SPEC**  
Show the eight-stage ribbon above, with the currently discussed stage highlighted on pipeline
slides.

**SPEAKER NOTES**  
"We will follow a single image through the product. This is deliberately an engineering deck:
we will show what the code implements and state where a model artifact or device test is still
required before making a runtime claim."

**SOURCE OF TRUTH**  
`android-app/app/src/main/java/com/cropora/ScanActivity.kt`; `README.md`.

---

## SLIDE 3 — The problem and the user

**PURPOSE**  
Connect the product design to a farmer or grower’s decisions.

**ON-SLIDE CONTENT**
```text
A DAMAGED LEAF CREATES THREE IMMEDIATE QUESTIONS

What is it?      What should I do now?      How can I prevent recurrence?

Cropora’s response:
  • photograph or select a leaf image
  • predict a class and confidence
  • provide symptoms, treatment, and prevention text where guidance exists

Deployment constraint:
  Connectivity can be unavailable in the field.
  → Offline mode is selected by default.
```

**VISUAL / DIAGRAM SPEC**  
Three question cards lead to a phone with an offline chip and an optional cloud connection.

**SPEAKER NOTES**  
"The product is designed around a field use case. Cloud mode can use a centrally served model,
but the scan screen starts in offline mode so the product’s intended default does not require a
network connection."

**SOURCE OF TRUTH**  
`README.md`; `ScanActivity.kt` (`cloudMode`, `setupModeToggle()`).

---

## SLIDE 4 — What Cropora is, and what it is not

**PURPOSE**  
Set an honest scope boundary before discussing architecture.

**ON-SLIDE CONTENT**
```text
WHAT IT IS                                      WHAT IT IS NOT YET

✓ Kotlin Android application                    ✗ Certified diagnostic device
✓ Camera and gallery image acquisition          ✗ Field-validated accuracy claim
✓ Cloud and offline inference implementations   ✗ Verified real-model run in this repository
✓ Room scan history and local analytics         ✗ Production-authenticated cloud service
✓ XML disease reference library                 ✗ Complete guidance for all 38 labels
✓ Client and server confidence gates             ✗ Production-signed public release

CURRENT STATUS
The code paths exist; valid model artifacts and device validation are prerequisites for
demonstrating real inference.
```

**VISUAL / DIAGRAM SPEC**  
Use a two-column comparison: green checks for implemented capabilities, grey scope boundaries for
unsupported claims.

**SPEAKER NOTES**  
"This is a functional codebase with a clear end-to-end design, but the repository intentionally
does not include the TFLite or Keras model binary. Therefore, we do not claim accuracy, parity,
or a release-ready diagnostic product from this checkout."

**SOURCE OF TRUTH**  
`README.md`; `android-app/app/build.gradle`; `backend-api/config.py`;
`backend-api/models/README.md`.

---

## SLIDE 5 — System landscape: four subsystems

**PURPOSE**  
Introduce the major parts of the repository.

**ON-SLIDE CONTENT**
```text
FOUR SUBSYSTEMS, ONE PRODUCT

1. ANDROID CLIENT       android-app/
   Kotlin Activities · Room · Retrofit · TFLite Interpreter · XML parser

2. BACKEND SERVICE      backend-api/
   FastAPI · Pillow · NumPy · optional TensorFlow/Keras model loader

3. MODEL ASSETS         app/src/main/assets/ and backend-api/models/
   labels.txt · diseases.xml · required model.tflite / cropora_model.keras artifacts

4. PRODUCT EVIDENCE     README.md · PRODUCT_PROGRESS_MAP.md · docs/evidence/week-01/

THE CONTRACT
raw RGB image → model label, display name, confidence, uncertainty,
guidance availability, symptoms, treatment, prevention
```

**VISUAL / DIAGRAM SPEC**  
Four colour-coded boxes point to a central `PredictionResponse` contract card. Make model
artifacts orange and note that the actual model binaries are not supplied.

**SPEAKER NOTES**  
"The client and service are separate deployable pieces. Labels and guidance are versioned assets,
while model binaries are expected at known paths. The shared prediction response is the join
between both inference routes."

**SOURCE OF TRUTH**  
Repository tree; `PredictionResponse.kt`; `backend-api/main.py`; `README.md`.

---

# SECTION B — THE MASTER PIPELINE

## SLIDE 6 — The end-to-end master pipeline

**PURPOSE**  
Show the full data path in one view.

**ON-SLIDE CONTENT**
```text
ONE IMAGE, ELEVEN STAGES

1  Acquire       camera via FileProvider URI or gallery via GetContent
2  Preview       selected URI becomes the image preview
3  Route         Offline (default) or Cloud
4a Offline       URI → Bitmap → 224×224 raw RGB float32 → TFLite → argmax
4b Cloud         URI → cache file → multipart "image" → POST /predict → backend argmax
5  Normalize     model label → human-readable disease name
6  Enrich        guidance lookup → symptoms, treatment, prevention
7  Unify         both routes use PredictionResponse
8  Gate          low confidence becomes an uncertainty-focused result
9  Present       six Intent extras populate ResultActivity
10 Persist       explicit Save to History → Room scan_history
11 Aggregate     history, detail, and local analytics read Room
```

**VISUAL / DIAGRAM SPEC**  
Use a left-to-right swimlane. Split at a `cloudMode?` diamond, then merge both branches at
`PredictionResponse` before the confidence gate.

**SPEAKER NOTES**  
"The central architectural idea is the merge. Cloud and offline inference take different routes,
but after they return a PredictionResponse, the rest of the application is shared."

**SOURCE OF TRUTH**  
`ScanActivity.kt`; `TFLiteClassifier.kt`; `backend-api/main.py`; `ResultActivity.kt`;
`database/ScanDao.kt`.

---

## SLIDE 7 — Explicit orchestration

**PURPOSE**  
Name the components that coordinate application behaviour.

**ON-SLIDE CONTENT**
```text
ORCHESTRATION IS EXPLICIT

Flow orchestrator       ScanActivity
  input selection · permissions · mode routing · progress · errors · uncertainty gate

Contract orchestrator   PredictionResponse
  Retrofit/Gson response · offline classifier return value · result hand-off data

Navigation orchestrator setupBottomNav(...)
  selects one of five Activity-based tabs and finishes the current tab

Configuration           SharedPreferences through SettingsActivity
  pref_backend_url · pref_confidence_threshold

NO ViewModel, Fragment, Navigation Component, or dependency-injection framework is present.
```

**VISUAL / DIAGRAM SPEC**  
Place `ScanActivity` centrally with three satellites: `PredictionResponse`, `setupBottomNav()`,
and `SharedPreferences`.

**SPEAKER NOTES**  
"There is no hidden framework layer here. The scan activity coordinates the detection transaction;
the response class is the contract boundary; the shared bottom-navigation extension coordinates
tabs; and two preferences steer the next detection."

**SOURCE OF TRUTH**  
`ScanActivity.kt`; `network/PredictionResponse.kt`; `ui/BottomNav.kt`; `SettingsActivity.kt`.

---

## SLIDE 8 — Screen map: eight Activities

**PURPOSE**  
Give the complete user-interface surface.

**ON-SLIDE CONTENT**
```text
FIVE TABS
MainActivity             Home       dashboard and shortcuts
ScanActivity             Scan       acquisition and inference routing
AnalyticsActivity        Analytics  local history summary
DiseaseLibraryActivity   Library    searchable XML reference
SettingsActivity         About      backend URL and confidence threshold

THREE PUSHED SCREENS
ResultActivity           prediction display, sharing, explicit persistence
HistoryActivity          saved scan list and deletion
HistoryDetailActivity    one saved scan, sharing, deletion

Only MainActivity is exported and is the launcher Activity.
```

**VISUAL / DIAGRAM SPEC**  
Show five tab phone frames and three pushed-screen frames. Label `ScanActivity → ResultActivity`
with “six Intent extras” and `HistoryActivity → HistoryDetailActivity` with `EXTRA_SCAN_ID`.

**SPEAKER NOTES**  
"Five screens share a bottom navigation bar. Results and history details are explicit pushed
screens. The manifest exports only the launcher, reducing the app’s externally launchable surface."

**SOURCE OF TRUTH**  
`AndroidManifest.xml`; `res/menu/bottom_nav_menu.xml`; `ui/BottomNav.kt`.

---

## SLIDE 9 — Repository map

**PURPOSE**  
Connect repository folders to runtime responsibilities.

**ON-SLIDE CONTENT**
```text
android-app/
  app/src/main/java/com/cropora/
    Activities · ml/TFLiteClassifier.kt · network/ · database/ · ui/ · utils/
  app/src/main/assets/
    labels.txt (38 labels) · diseases.xml (10 entries) · model.tflite (required, absent)
  app/src/main/res/
    layouts · strings · menu · drawables · XML configuration

backend-api/
  main.py · config.py · model_loader.py · labels.py · test_api.py
  labels-38.txt · Dockerfile · requirements*.txt
  models/cropora_model.keras (default expected path, absent)

docs/evidence/week-01/
  product idea · screen map · system sketch · Cropora screenshot
```

**VISUAL / DIAGRAM SPEC**  
Render the tree using the four subsystem colours. Put an orange “required artifact” badge next to
both expected model paths.

**SPEAKER NOTES**  
"The repository has one Kotlin Android project and one Python backend. The labels and guidance
assets are committed; model binaries are intentionally not present in this clone, so they remain
deployment prerequisites."

**SOURCE OF TRUTH**  
Repository tree; `.gitignore`; `README.md`; `backend-api/models/README.md`.

---

# SECTION C — INFERENCE PIPELINES

## SLIDE 10 — Pipeline 1: image acquisition

**PURPOSE**  
Show the permission-aware, privacy-conscious image-input path.

**ON-SLIDE CONTENT**
```text
CAMERA
1  Check CAMERA permission
2  If needed, request it and remember the pending camera action
3  Create app-scoped Pictures/captures/cropora_<timestamp>.jpg
4  Wrap it in a FileProvider content URI
5  Launch ActivityResultContracts.TakePicture(uri)
6  On success, set selectedImageUri and reveal controls

GALLERY
1  Launch ActivityResultContracts.GetContent("image/*")
2  Receive a scoped content URI
3  Set selectedImageUri and reveal controls

No broad storage/media permission is requested for the gallery flow.
```

**VISUAL / DIAGRAM SPEC**  
Use a camera/gallery decision diamond. Show a permission denial route to a Toast and the gallery
route with a “scoped URI access” callout.

**SPEAKER NOTES**  
"The camera writes to an app-controlled file through FileProvider rather than receiving a raw
file URI. The gallery uses the system picker, which supplies the selected content URI without
granting broad photo-library access."

**SOURCE OF TRUTH**  
`ScanActivity.kt`; `AndroidManifest.xml`; `res/xml/file_provider_paths.xml`.

---

## SLIDE 11 — Pipeline 2: the routing decision

**PURPOSE**  
Isolate the one decision that selects the inference route.

**ON-SLIDE CONTENT**
```text
PIPELINE 2 — ROUTE

private var cloudMode = false

Offline is selected initially.
The mode toggle assigns cloudMode when the user selects Cloud or Offline.

detectDisease()
  selectedImageUri missing → show “select an image first”
  otherwise → show progress and disable the detect button
  cloudMode true  → runCloudDetection()
  cloudMode false → runOfflineDetection()

Both paths are expected to produce PredictionResponse.
```

**VISUAL / DIAGRAM SPEC**  
Use a central `cloudMode?` diamond: false to `TFLiteClassifier`, true to `Retrofit → FastAPI`.

**SPEAKER NOTES**  
"The branch is small because the response contract is shared. The button state protects against
starting a second detection while one is already in progress."

**SOURCE OF TRUTH**  
`ScanActivity.kt` (`setupModeToggle()`, `detectDisease()`, `setDetectionInProgress()`).

---

## SLIDE 12 — Pipeline 3A: cloud inference, client side

**PURPOSE**  
Trace the cloud request from a selected URI to a response.

**ON-SLIDE CONTENT**
```text
1  Resolve backend URL from SharedPreferences
   blank → default http://10.0.2.2:8000
   parse with toHttpUrlOrNull(); invalid URL stops the flow

2  Copy the content URI to cacheDir/cropora_upload_<timestamp>.jpg

3  Build multipart request
   form field name: "image"
   MIME type: resolver type, otherwise image/*

4  Retrofit POST /predict asynchronously

5  On response or failure
   delete the temporary upload file
   hide progress
   open ResultActivity only for a successful, non-null response

OkHttp uses 30-second connect, read, and write timeouts.
```

**VISUAL / DIAGRAM SPEC**  
Draw a five-step vertical flow. Highlight the multipart field name `image` and show deletion bins
on both callback exits.

**SPEAKER NOTES**  
"The backend’s FastAPI parameter is named image, so the multipart part must be image too. The
selected content URI is copied to cache because OkHttp uploads a file body. The app removes that
temporary file on either callback path."

**SOURCE OF TRUTH**  
`ScanActivity.kt`; `network/ApiService.kt`; `network/RetrofitClient.kt`; `backend-api/main.py`.

---

## SLIDE 13 — Pipeline 3A continued: backend service

**PURPOSE**  
Show the backend’s startup and defensive request processing.

**ON-SLIDE CONTENT**
```text
STARTUP
labels-38.txt → load non-empty, unique class names
MODEL_PATH → attempt to load and validate a Keras model when available
USE_MOCK=true → enable deterministic mock predictor for development/tests

POST /predict
1  require image/* content type                         → 400 otherwise
2  read at most configured limit + 1 byte               → empty 400, oversized 413
3  Pillow decode → RGB → resize 224×224 → float32 batch
4  unavailable real model and mock disabled             → 503
5  argmax prediction → model label and confidence
6  add reviewed guidance when available
7  mark confidence below server threshold as uncertain
8  close the uploaded file in finally

GET / and /health · GET /diseases · POST /predict
```

**VISUAL / DIAGRAM SPEC**  
Show startup modes (mock, unavailable, loaded) above the numbered request pipeline. Put error
codes at their rejecting stages.

**SPEAKER NOTES**  
"The API does not fabricate a real-model answer when the model is unavailable: without mock mode
it returns 503. It validates declared image content, decodes the bytes with Pillow, bounds upload
size, and always closes the uploaded file."

**SOURCE OF TRUTH**  
`backend-api/main.py`; `backend-api/model_loader.py`; `backend-api/config.py`;
`backend-api/labels.py`.

---

## SLIDE 14 — Pipeline 3B: on-device inference

**PURPOSE**  
Describe the implemented offline classifier and its model dependency.

**ON-SLIDE CONTENT**
```text
OFFLINE INFERENCE IMPLEMENTATION

Run in lifecycleScope with Dispatchers.IO
  URI → Bitmap
  Bitmap → scaled 224×224 image
  raw R/G/B values → direct native-order float32 ByteBuffer
  TFLite Interpreter → output score array → argmax

Classifier construction validates:
  labels.txt is non-empty
  model input is [1, 224, 224, 3]
  model output is [1, class count]
  output count equals label count

REQUIRED ARTIFACT
app/src/main/assets/model.tflite is not present in this repository.
Without it, offline detection reports an error rather than guessing.
```

**VISUAL / DIAGRAM SPEC**  
Use construct / infer / output bands. Place a prominent orange prerequisite badge at
`assets/model.tflite`.

**SPEAKER NOTES**  
"The code path is real and strict: it memory-maps a TFLite model, checks its shape, and closes the
interpreter after each detection. But the model file is absent here, so offline inference cannot
be presented as demonstrated until a compatible artifact is supplied."

**SOURCE OF TRUTH**  
`ml/TFLiteClassifier.kt`; `ScanActivity.kt`; `android-app/app/build.gradle`; `README.md`.

---

## SLIDE 15 — The intended parity contract

**PURPOSE**  
Explain the shared preprocessing and label assumptions without claiming unmeasured parity.

**ON-SLIDE CONTENT**
```text
INTENDED CONTRACT FOR CLOUD AND OFFLINE PATHS

                    ANDROID                         BACKEND
Resize              224×224 Bitmap                  Pillow resize(224, 224)
Colour              RGB channels                    convert("RGB")
Input dtype         float32 ByteBuffer              NumPy float32
Pixel range         raw 0..255                      raw 0..255
Decision            argmax                           argmax
Labels              assets/labels.txt               backend-api/labels-38.txt
Class count         expected output count = labels  expected output count = labels

Both committed label files contain 38 labels.

NOT YET CLAIMED
No compatible pair of Keras/TFLite model binaries or parity measurement is included here.
```

**VISUAL / DIAGRAM SPEC**  
Use a two-column comparison with a vertical equality spine. Put an amber “requires validation”
panel below the table.

**SPEAKER NOTES**  
"The code aligns both paths around 224-pixel RGB float32 inputs and matching label order. That is
an implementation contract, not evidence of numerical parity. Verifying it requires compatible
model artifacts and the same test images through both engines."

**SOURCE OF TRUTH**  
`TFLiteClassifier.kt`; `backend-api/main.py`; `backend-api/model_loader.py`;
`android-app/app/src/main/assets/labels.txt`; `backend-api/labels-38.txt`.

---

## SLIDE 16 — Convergence: PredictionResponse

**PURPOSE**  
Make the common response contract concrete.

**ON-SLIDE CONTENT**
```text
ONE SHARED RESPONSE OBJECT

modelLabel          original model class
disease             human-readable class
confidence          score from 0 to 1
uncertain           low-confidence flag
guidanceAvailable   whether curated guidance exists
symptoms            guidance or explicit generic text
treatment           guidance or explicit generic text
prevention          guidance or explicit generic text

The cloud response is deserialized by Gson.
The offline classifier constructs the same Kotlin data class.

38 labels are predictable by contract; 10 currently have curated guidance.
```

**VISUAL / DIAGRAM SPEC**  
Merge green offline and blue cloud arrows into a large card listing the eight fields. Use a
38-segment guidance bar with 10 filled segments and 28 neutral segments.

**SPEAKER NOTES**  
"This data class is the architectural join. Both routes return model information and guidance in
one shape, allowing ScanActivity to apply the same safety treatment and ResultActivity to render
the same extras."

**SOURCE OF TRUTH**  
`network/PredictionResponse.kt`; `TFLiteClassifier.kt`; `backend-api/main.py`;
`android-app/app/src/main/assets/diseases.xml`.

---

## SLIDE 17 — The uncertainty gate

**PURPOSE**  
Show how low-confidence predictions are reframed before presentation.

**ON-SLIDE CONTENT**
```text
TWO CONFIDENCE CHECKS

Server: confidence < CONFIDENCE_THRESHOLD
        default threshold = 0.50

Client: response.uncertain OR
        confidence × 100 < SharedPreferences confidence threshold
        default threshold = 50%

WHEN THE CLIENT GATE TRIPS
  disease     → Uncertain / retake image (top match retained)
  symptoms    → result is not a diagnosis
  treatment   → retake in good light and verify with an expert
  prevention  → do not act on this result alone
  uncertain   → true

The rewritten fields are passed to the result screen and can be saved to history.
```

**VISUAL / DIAGRAM SPEC**  
Contrast a normal result card with an uncertainty card, separated by a 50% threshold slider.

**SPEAKER NOTES**  
"A low-confidence class is not presented as an ordinary diagnosis. The app preserves the top match
as a clue, but changes the disease and care text to tell the user to retake and verify."

**SOURCE OF TRUTH**  
`ScanActivity.kt` (`openResult()`); `SettingsActivity.kt`; `backend-api/main.py`;
`backend-api/config.py`; `res/values/strings.xml`.

---

## SLIDE 18 — Cloud versus offline

**PURPOSE**  
Make the two routes’ operational trade-offs explicit.

**ON-SLIDE CONTENT**
```text
                    OFFLINE                         CLOUD
Runs on             Android device                  FastAPI server
Network required    No                              Yes
Input               Bitmap / TFLite                 multipart image upload
Model artifact      model.tflite in APK assets      cropora_model.keras on server
Privacy             image stays local               image is uploaded
Upgrade path        ship a new APK                  deploy a server model
Guidance source     diseases.xml                    DISEASE_INFO
Failure surface     missing/incompatible model      HTTP and model-availability errors
Current prerequisite compatible TFLite artifact     compatible Keras artifact or mock mode

Offline is the default selection; cloud is the optional route.
```

**VISUAL / DIAGRAM SPEC**  
Use side-by-side green and blue columns. Add a lock to offline and a cloud-refresh icon to cloud.

**SPEAKER NOTES**  
"Offline favours availability and image locality. Cloud allows server deployment and mock-driven
development, but requires a reachable service. Both still need a compatible model artifact for
real inference."

**SOURCE OF TRUTH**  
`ScanActivity.kt`; `TFLiteClassifier.kt`; `backend-api/config.py`; `backend-api/model_loader.py`.

---

# SECTION D — POST-INFERENCE PIPELINES

## SLIDE 19 — Pipeline 4: result presentation and sharing

**PURPOSE**  
Show the boundary from prediction to user-facing result.

**ON-SLIDE CONTENT**
```text
ScanActivity → ResultActivity

SIX INTENT EXTRAS
disease name · confidence · symptoms · treatment · prevention · image URI

RESULT SCREEN
• converts confidence to a percentage and progress bar
• uses placeholders for missing extras
• displays symptoms, treatment, and prevention

ACTIONS
Share: standard ACTION_SEND text chooser
Save: explicit Room insertion; button becomes Saved
Back home: clear-top, single-top navigation
```

**VISUAL / DIAGRAM SPEC**  
Show an annotated result phone frame and arrows to a share sheet, Room database, and home screen.

**SPEAKER NOTES**  
"The result screen receives primitives, not a global classifier or network reference. It can render
a returned cloud response or offline response identically. Persistence is an explicit user action."

**SOURCE OF TRUTH**  
`ResultActivity.kt`; `ScanActivity.kt`; `res/values/strings.xml`.

---

## SLIDE 20 — Pipeline 5: persistence with Room

**PURPOSE**  
Describe the local history data layer.

**ON-SLIDE CONTENT**
```text
ROOM DATABASE: cropora.db
Entity: scan_history, schema version 1

ScanRecord fields
id · disease_name · confidence · symptoms · treatment · prevention
image_uri · latitude · longitude · timestamp

ScanDao operations
insertScan · getAllScans · getRecentScans · getScanById
deleteScan · deleteScanById

AppDatabase is an application-context singleton.
No destructive-migration fallback is configured:
future schema changes require an explicit migration rather than silently deleting history.
```

**VISUAL / DIAGRAM SPEC**  
Show Activities → ScanDao → AppDatabase → `cropora.db`, with a table representation of
`scan_history`.

**SPEAKER NOTES**  
"Room stores a user-selected history record locally. Queries are suspend functions, and the
database builder has no destructive fallback, so an unplanned schema change cannot silently wipe
saved scans."

**SOURCE OF TRUTH**  
`database/ScanRecord.kt`; `database/ScanDao.kt`; `database/AppDatabase.kt`; `ResultActivity.kt`.

---

## SLIDE 21 — Pipeline 6: history and detail

**PURPOSE**  
Show how saved scans are listed, viewed, shared, and deleted.

**ON-SLIDE CONTENT**
```text
HISTORY
onCreate / onResume → getAllScans() → RecyclerView
Each row: disease · confidence · formatted timestamp · delete action
Empty data: list hidden and empty-state message shown

DETAIL
HistoryActivity sends only EXTRA_SCAN_ID (Long)
HistoryDetailActivity re-queries Room by ID
Invalid or missing record: Toast then finish

Detail actions: share text or delete record

Design principle: pass an identifier between screens; Room remains the data source.
```

**VISUAL / DIAGRAM SPEC**  
Show list and detail phone frames. A dashed arrow from detail back to `cropora.db` reads
“query by ID.”

**SPEAKER NOTES**  
"Passing only the primary key avoids a stale copied object. The detail activity reads the current
record from Room, and the history tab refreshes on return."

**SOURCE OF TRUTH**  
`HistoryActivity.kt`; `HistoryDetailActivity.kt`; `database/ScanDao.kt`.

---

## SLIDE 22 — Pipeline 7: on-device analytics

**PURPOSE**  
Show privacy-preserving aggregation of saved records.

**ON-SLIDE CONTENT**
```text
ANALYTICS IS DERIVED LOCALLY FROM scan_history

Total scans       records.size
Mean confidence   average confidence × 100, rounded and clamped
Most frequent     disease name with the highest local count

Calculated on onCreate and onResume.
No analytics SDK, user identifier, event store, or upload path is implemented.

The feature summarizes local scan history without sending analytics data off-device.
```

**VISUAL / DIAGRAM SPEC**  
Display three metric cards beside a phone-to-cloud arrow crossed out.

**SPEAKER NOTES**  
"Analytics is not a separate telemetry system. It is a local computation over Room records when
the screen is opened, which keeps these field-history summaries on the device."

**SOURCE OF TRUTH**  
`AnalyticsActivity.kt`; `android-app/app/build.gradle`; `AndroidManifest.xml`.

---

## SLIDE 23 — Pipeline 8: offline disease library

**PURPOSE**  
Describe the bundled disease-reference pipeline and its fallback.

**ON-SLIDE CONTENT**
```text
SOURCE: app/src/main/assets/diseases.xml

10 bundled disease entries
name · plant · symptoms · treatment · prevention · severity

LIBRARY SCREEN
XmlPullParser streaming parse → RecyclerView rows
Text search matches disease or plant name, case-insensitively
Severity chip colours: high, medium, low
No matches: visible empty-library message

RESILIENCE
Unreadable or malformed XML → built-in five-entry fallback list

SECOND CONSUMER
TFLiteClassifier parses the same XML into offline guidance by display name.
```

**VISUAL / DIAGRAM SPEC**  
An XML card feeds both the library RecyclerView and the offline classifier guidance map.

**SPEAKER NOTES**  
"The same bundled XML powers the reference screen and enriches offline predictions. The library
has a five-item fallback so it can still show reference content if its asset cannot be read."

**SOURCE OF TRUTH**  
`DiseaseLibraryActivity.kt`; `ml/TFLiteClassifier.kt`; `app/src/main/assets/diseases.xml`;
`MainActivity.kt`.

---

## SLIDE 24 — Pipelines 9 and 10: configuration and notifications

**PURPOSE**  
Show the small control-plane and notification utilities.

**ON-SLIDE CONTENT**
```text
CONFIGURATION — SettingsActivity
Backend URL           persisted to SharedPreferences as text changes
Confidence threshold  persisted when SeekBar tracking stops
App version           read from PackageManager
Reset                 restores default URL and 50% threshold

The next scan rereads both settings.

NOTIFICATIONS — NotificationHelper
Channel: cropora_scan_reminders
Android 8+: creates a notification channel
Android 13+: returns without posting when POST_NOTIFICATIONS is not granted
PendingIntent opens MainActivity with immutable/update-current flags

The helper exists; no scheduling workflow invokes it in the current repository.
```

**VISUAL / DIAGRAM SPEC**  
Split the slide: Settings controls → SharedPreferences → ScanActivity, and notification helper →
system tray → MainActivity.

**SPEAKER NOTES**  
"Settings changes apply on the next detection without a restart. The notification helper follows
modern channel and runtime-permission rules, but this repository does not yet schedule reminders
or call the send method from a product workflow."

**SOURCE OF TRUTH**  
`SettingsActivity.kt`; `ScanActivity.kt`; `utils/NotificationHelper.kt`; `MainActivity.kt`.

---

# SECTION E — DELIVERY

## SLIDE 25 — Model readiness and artifact contract

**PURPOSE**  
State exactly what model artifacts the implementation expects and what remains to be supplied.

**ON-SLIDE CONTENT**
```text
MODEL READINESS: EXPECTED ARTIFACTS

Offline
  android-app/app/src/main/assets/model.tflite
  must be a TensorFlow Lite FlatBuffer with TFL3 header
  input [1,224,224,3] and output count matching labels.txt

Cloud
  backend-api/models/cropora_model.keras by default
  must load as Keras with input (None,224,224,3)
  and output count matching backend labels-38.txt

Committed contract assets
  app labels.txt and backend labels-38.txt: 38 labels
  diseases.xml and backend DISEASE_INFO: 10 reviewed guidance entries

CURRENT STATE
Both expected model binaries are absent. The release Gradle task and backend loader fail safely
instead of pretending an incompatible model is valid.
```

**VISUAL / DIAGRAM SPEC**  
Draw a dashed supply chain ending at the expected Android and backend model locations. Use amber
badges for “artifact to supply” and green badges for committed labels/guidance.

**SPEAKER NOTES**  
"The repository includes validation logic, labels, and guidance, but not the models. Before a real
demo or release, a compatible Keras model and derived compatible TFLite model must be supplied and
then tested against the 38-label contract."

**SOURCE OF TRUTH**  
`android-app/app/build.gradle`; `TFLiteClassifier.kt`; `backend-api/config.py`;
`backend-api/model_loader.py`; `README.md`; `backend-api/models/README.md`.

---

## SLIDE 26 — Local build, test, and deployment paths

**PURPOSE**  
Provide reproducible local commands without claiming CI that is not in the repository.

**ON-SLIDE CONTENT**
```text
ANDROID
Open android-app/ in Android Studio, or:
  ./gradlew testDebugUnitTest
  ./gradlew connectedDebugAndroidTest      (device/emulator required)

BACKEND
  python -m venv .venv
  pip install -r requirements-base.txt
  cp .env.example .env
  uvicorn main:app --reload
  python -m unittest -v test_api.py

REAL BACKEND INFERENCE
Install requirements.txt and provide the configured Keras model artifact.

CONTAINER
Dockerfile uses python:3.11-slim, exposes 8000, has a /health healthcheck,
and installs TensorFlow only when INSTALL_TENSORFLOW=true.

No CI workflow is present in this repository.
```

**VISUAL / DIAGRAM SPEC**  
Use three local lanes: Android Gradle, backend tests/server, and Docker. Label the final state
“local verification; CI not configured.”

**SPEAKER NOTES**  
"These are the commands and deployment ingredients available now. The Dockerfile supports both a
small non-TensorFlow image and an inference-capable image. Automated repository CI is a future
delivery improvement, not a current claim."

**SOURCE OF TRUTH**  
`README.md`; `android-app/gradlew`; `backend-api/Dockerfile`;
`backend-api/requirements-base.txt`; `backend-api/requirements.txt`.

---

## SLIDE 27 — Data residency

**PURPOSE**  
Show what data is local and when an image leaves the device.

**ON-SLIDE CONTENT**
```text
DATA                         LOCATION                              LEAVES DEVICE?
Camera capture               app-scoped Pictures/captures          No by itself
Gallery selection            scoped content URI                    No by itself
Cloud temp upload            cacheDir/cropora_upload_*.jpg         Yes, Cloud mode only
Saved scan history           Room cropora.db                       No
Backend URL / threshold      SharedPreferences                     No
Labels / disease library     APK assets                            No
Offline model                APK assets when supplied              No
Analytics                    computed from Room in memory          No
Shared text                  ACTION_SEND only                      Only when user shares

Network egress in the app flow: POST /predict after the user selects Cloud mode.
Cleartext traffic is denied by default and allow-listed only for local development hosts.
```

**VISUAL / DIAGRAM SPEC**  
Put the local items inside a phone outline. Draw one outbound arrow, “Cloud mode POST /predict,”
to the FastAPI service.

**SPEAKER NOTES**  
"Cloud mode sends the image to the configured backend. Offline inference and local history do not
create that request. Note that a gallery URI is stored as text with a history record, so long-term
availability of a selected external image is not guaranteed by the current code."

**SOURCE OF TRUTH**  
`ScanActivity.kt`; `ResultActivity.kt`; `database/ScanRecord.kt`;
`res/xml/network_security_config.xml`.

---

# SECTION F — ENGINEERING QUALITY

## SLIDE 28 — Concurrency, lifecycle, and resources

**PURPOSE**  
Show deliberate work placement and resource handling while naming limits accurately.

**ON-SLIDE CONTENT**
```text
WORK PLACEMENT
Offline inference       lifecycleScope + Dispatchers.IO
Room DAO calls          suspend methods from lifecycleScope
Cloud HTTP              Retrofit enqueue callback
Preference writes       asynchronous apply()

RESOURCE DISCIPLINE
ViewBinding is nulled in onDestroy()
TFLiteClassifier implements AutoCloseable and is used with use { }
TFLite model is memory-mapped
Scaled replacement bitmap is recycled after preprocessing
XML streams use use { }
Cloud upload cache file is deleted in response and failure callbacks

KNOWN LIMIT
The Retrofit call is not explicitly cancelled when ScanActivity is destroyed.
```

**VISUAL / DIAGRAM SPEC**  
Show a main-thread/IO-thread timeline and a resource checklist. Add an amber caveat badge to the
HTTP callback.

**SPEAKER NOTES**  
"CPU-intensive offline work is dispatched away from the UI. Bindings and the interpreter have
explicit lifecycle cleanup. One hardening opportunity remains: the current cloud request is not
explicitly cancelled with the activity lifecycle."

**SOURCE OF TRUTH**  
`ScanActivity.kt`; `TFLiteClassifier.kt`; Activity `onDestroy()` methods; `RetrofitClient.kt`.

---

## SLIDE 29 — Security and privacy posture

**PURPOSE**  
Consolidate defensive choices across client, server, and repository.

**ON-SLIDE CONTENT**
```text
CLIENT
• Permissions: INTERNET, CAMERA, POST_NOTIFICATIONS only
• Only MainActivity is exported
• FileProvider serves camera capture URIs
• Gallery uses scoped GetContent access
• Backend URL is parsed before Retrofit receives it
• Cleartext is denied except specified development hosts
• Logging is BODY in debug and BASIC in release builds

SERVER
• image/* content-type check, Pillow decode, empty/oversize rejection
• bounded upload read; default limit 10 MB
• 503 when no real model is loaded and mock mode is off
• generic 500 response and guaranteed upload-handle close
• CORS does not allow credentials when origins are wildcarded

REPOSITORY
• .gitignore excludes environment files, models, APKs, and signing materials
```

**VISUAL / DIAGRAM SPEC**  
Use Client, Server, and Repository shield columns. Make 400, 413, 503, and 500 visible as
server-rejection chips.

**SPEAKER NOTES**  
"The security posture is built from narrow permissions, scoped URI handling, defensive upload
validation, and safe failure behaviour. It is not a substitute for production authentication,
rate limiting, and HTTPS deployment, which remain roadmap work."

**SOURCE OF TRUTH**  
`AndroidManifest.xml`; `ScanActivity.kt`; `RetrofitClient.kt`; `backend-api/main.py`;
`backend-api/config.py`; `.gitignore`.

---

## SLIDE 30 — Testing and current validation evidence

**PURPOSE**  
Separate existing automated tests from validation that has not been recorded.

**ON-SLIDE CONTENT**
```text
TESTS PRESENT IN THE REPOSITORY

Android JVM
  PredictionResponseTest: JSON response deserialization

Android instrumented
  MainActivityTest and ScanActivityTest
  require a connected device or emulator to execute

Backend unittest suite
  health aliases and class count
  disease-library count
  valid mock prediction
  503 without real model
  raw-RGB preprocessing shape/value
  non-image, spoofed-image, and oversized-upload rejection

NOT PRESENT AS EVIDENCE
  real-model inference run · Android device execution record · cloud/offline parity
  accuracy study · artifact provenance/hash record · CI run history
```

**VISUAL / DIAGRAM SPEC**  
Show a test pyramid, with the existing tests in green and unavailable validation evidence in
amber.

**SPEAKER NOTES**  
"The backend tests establish key API behaviour without a real model by using controlled mock mode.
The repository does not contain the evidence needed to claim device execution, model accuracy, or
agreement between cloud and offline engines."

**SOURCE OF TRUTH**  
`android-app/app/src/test/java/com/cropora/network/PredictionResponseTest.kt`;
`android-app/app/src/androidTest/java/com/cropora/`; `backend-api/test_api.py`.

---

## SLIDE 31 — Limitations and release gate

**PURPOSE**  
Define the work needed before presenting Cropora as a real deployment.

**ON-SLIDE CONTENT**
```text
RELEASE-BLOCKING GAPS

1  Provide a compatible TFLite model in Android assets
2  Provide a compatible Keras model for the configured backend path
3  Verify cloud/offline preprocessing and output parity on documented leaf images
4  Run Android instrumented tests and manual camera/gallery tests on supported devices
5  Exercise offline scanning with networking disabled
6  Perform an accuracy study across the supported label set
7  Deploy cloud mode with HTTPS, authentication, and rate limiting
8  Sign, install, and document the exact production APK
9  Expand curated guidance beyond the current 10 of 38 labels

CURRENT POSITION
An implemented product architecture with unverified model artifacts and deployment readiness.
```

**VISUAL / DIAGRAM SPEC**  
Use a gate diagram: implemented code on the left, required validation and release checkpoints on
the right.

**SPEAKER NOTES**  
"This is the definition of done. Each item converts a code-path claim into a deployment claim,
from supplying models to field-relevant accuracy and secured service operation."

**SOURCE OF TRUTH**  
`README.md`; `android-app/app/build.gradle`; `backend-api/model_loader.py`;
`backend-api/models/README.md`; `backend-api/test_api.py`.

---

# SECTION G — PROJECT STORY

## SLIDE 32 — One Kotlin Android implementation

**PURPOSE**  
Accurately describe the implementation present in this repository.

**ON-SLIDE CONTENT**
```text
ONE ANDROID CLIENT IMPLEMENTATION

android-app/
  Kotlin Activities and data classes
  Coroutines for offline inference and Room access
  ViewBinding for layouts
  Retrofit/Gson for cloud communication
  Room for local persistence
  TensorFlow Lite API for offline inference

Several source comments mention Java “twins,” but no Java Android implementation is present
in this repository. Cropora’s deliverable should be evaluated as the Kotlin implementation shown.
```

**VISUAL / DIAGRAM SPEC**  
Show the Kotlin Android app as a single component stack. Put a small document-note callout,
“repository contains one Android implementation.”

**SPEAKER NOTES**  
"The present repository contains the Kotlin Android implementation. Some inherited comments refer
to a Java counterpart, but there is no corresponding Java source tree here, so the presentation
does not claim a dual-track product."

**SOURCE OF TRUTH**  
Repository tree; `android-app/app/src/main/java/com/cropora/`; Kotlin source-file headers.

---

## SLIDE 33 — Product evidence currently in the repository

**PURPOSE**  
Tell the project story from recorded evidence without inventing a delivery timeline.

**ON-SLIDE CONTENT**
```text
THE REPOSITORY RECORDS A PRODUCT FOUNDATION

README
  product definition, repository layout, Android/backend setup

docs/evidence/week-01/
  product idea · screen map · architecture sketch · Cropora screenshot

PRODUCT_PROGRESS_MAP.md and progress-tracker.md
  repository planning and progress material

RUNTIME IMPLEMENTATION
  UI, acquisition, cloud API integration, offline classifier code,
  guidance library, persistence, analytics, settings, and tests

No complete, verifiable multi-week delivery record is included.
The story should remain anchored to the code and evidence that are present.
```

**VISUAL / DIAGRAM SPEC**  
Use an evidence-board layout: product idea, screen map, system sketch, screenshot, and source
implementation tiles.

**SPEAKER NOTES**  
"Cropora has early product evidence and a substantial implementation. We avoid converting that
into an unsupported week-by-week completion narrative; the repository itself is the evidence base."

**SOURCE OF TRUTH**  
`README.md`; `PRODUCT_PROGRESS_MAP.md`; `progress-tracker.md`; `docs/evidence/week-01/`.

---

## SLIDE 34 — Roadmap

**PURPOSE**  
Give an ordered, evidence-driven path from implementation to readiness.

**ON-SLIDE CONTENT**
```text
NEXT, IN PRIORITY ORDER

MODEL AND VALIDATION
1  Supply compatible Keras and TFLite model artifacts
2  Verify label order, preprocessing, and cloud/offline parity
3  Run accuracy and out-of-distribution evaluation on representative leaf images
4  Run device matrix, camera/gallery, offline, and permission-denial tests

SECURE DELIVERY
5  Production HTTPS backend, authentication, rate limiting, and operational monitoring
6  Signing, release artifacts, reproducible build records, and CI

PRODUCT DEPTH
7  Curated guidance for all supported labels
8  Persistable image strategy with explicit user-consent/privacy design
9  Notification scheduling and user controls
10 Navigation and lifecycle hardening, including cancellable cloud calls
```

**VISUAL / DIAGRAM SPEC**  
Use three swimlanes: validation, secure delivery, and product depth. Place validation first on
the time axis.

**SPEAKER NOTES**  
"The roadmap puts proof before new features. Model artifacts, parity, device testing, and
accuracy must be completed before a grower-facing claim. Security and operational controls come
before public cloud use."

**SOURCE OF TRUTH**  
`README.md`; `backend-api/config.py`; `ScanActivity.kt`; `NotificationHelper.kt`;
`network_security_config.xml`.

---

# SECTION H — CLOSE

## SLIDE 35 — Demonstration script

**PURPOSE**  
Provide a safe demonstration order that does not overstate what the current checkout can run.

**ON-SLIDE CONTENT**
```text
DEMO PREPARATION
• Build the Android app
• Start backend in USE_MOCK=true mode for a demonstrable cloud response
• Use a known image on the device/emulator
• For real offline/cloud demonstrations, first supply validated compatible model artifacts

SCRIPT
1  Home: show local history and library shortcuts
2  Scan: choose gallery or camera and preview an image
3  Cloud: select Cloud and show the multipart request/result with mock mode
4  Result: show confidence, guidance fields, and share chooser
5  Save: persist to Room, open History, inspect Detail, delete
6  Analytics: show locally derived metrics
7  Library: search bundled disease entries
8  Settings: change threshold, then show uncertainty framing with a low-confidence response

Do not demonstrate offline inference as working until model.tflite is supplied and tested.
```

**VISUAL / DIAGRAM SPEC**  
Use an eight-thumbnail grid. Mark mock-cloud steps blue and artifact-dependent offline steps
amber.

**SPEAKER NOTES**  
"The safe live path demonstrates UI, API integration, persistence, analytics, library, and
confidence framing. The offline feature should be demonstrated only after its required model has
been placed and validated."

**SOURCE OF TRUTH**  
`README.md`; `ScanActivity.kt`; `backend-api/.env.example`; `backend-api/test_api.py`;
`SettingsActivity.kt`.

---

## SLIDE 36 — Q&A anchors and appendix

**PURPOSE**  
Close with concise, defensible answers.

**ON-SLIDE CONTENT**
```text
Q  Why two inference paths?
A  Offline is selected by default for field availability; cloud supports a server-side model.

Q  Do they currently have verified numerical parity?
A  No. The code aligns input handling and labels, but compatible artifacts and measured parity
   evidence are not in this repository.

Q  Where is orchestration?
A  ScanActivity owns detection flow; PredictionResponse is the shared contract; bottom navigation
   and SharedPreferences coordinate tabs and configuration.

Q  Is it ready for production diagnosis?
A  No. Model supply, parity/accuracy evaluation, device validation, and secure cloud delivery
   remain release gates.

Q  What happens below the confidence threshold?
A  Cropora rewrites the result into uncertainty-focused guidance before it reaches the result UI.

VERIFY CLAIMS IN
README.md · android-app/app/src/main/java/com/cropora/ · backend-api/
docs/evidence/week-01/ · PRODUCT_PROGRESS_MAP.md · progress-tracker.md
```

**VISUAL / DIAGRAM SPEC**  
Use a Q&A column beside a monospace source index. Footer: `Cropora · com.cropora · v0.2.0-beta`.

**SPEAKER NOTES**  
"Cropora’s value is a clear mobile and service architecture around leaf-image classification, with
local history and guidance. Its responsible next step is validation: demonstrate the supplied
models on device, measure the results, and secure deployment before making broader claims."

**SOURCE OF TRUTH**  
All repository paths listed on the slide.

---

## Appendix A — Fact sheet for the slide-generation agent

| Fact | Value | Where |
|---|---|---|
| Application ID | `com.cropora` | `android-app/app/build.gradle` |
| Version | `0.2.0-beta`, versionCode `2` | `android-app/app/build.gradle` |
| SDK levels | min 24, target 34, compile 34 | `android-app/app/build.gradle` |
| Activities | 8; 5 tabs and 3 pushed screens | `AndroidManifest.xml` |
| Permissions | INTERNET, CAMERA, POST_NOTIFICATIONS | `AndroidManifest.xml` |
| Model input contract | raw RGB float32, 224×224, 3 channels | `TFLiteClassifier.kt`, `main.py` |
| Label count | 38 | both `labels.txt` files |
| Curated guidance | 10 entries | `diseases.xml`, `main.py` |
| Database | `cropora.db`, `scan_history`, version 1 | database classes |
| Cloud route | multipart `POST /predict`, part `image` | `ApiService.kt`, `main.py` |
| Default backend URL | `http://10.0.2.2:8000` | `SettingsActivity.kt` |
| HTTP timeouts | 30 seconds connect/read/write | `RetrofitClient.kt` |
| Server confidence threshold | 0.50 | `backend-api/config.py` |
| Client confidence threshold | 50%, user configurable | `SettingsActivity.kt` |
| Upload limit | 10 MB | `backend-api/config.py` |
| Required Android model | `assets/model.tflite`, absent | Gradle task / repository tree |
| Required backend model | `models/cropora_model.keras`, absent | `config.py` / repository tree |

## Appendix B — Diagram inventory

| # | Diagram | Slides |
|---:|---|---|
| D1 | Four-subsystem contract | 5 |
| D2 | Master pipeline split/merge | 6 |
| D3 | Explicit orchestration | 7 |
| D4 | Activity screen map | 8 |
| D5 | Image-acquisition flow | 10 |
| D6 | `cloudMode` decision | 11 |
| D7 | Cloud client flow | 12 |
| D8 | Backend request/error flow | 13 |
| D9 | Offline classifier with artifact prerequisite | 14 |
| D10 | Intended parity comparison | 15 |
| D11 | PredictionResponse convergence | 16 |
| D12 | Confidence-gate result cards | 17 |
| D13 | Room data layer | 20 |
| D14 | Model readiness supply chain | 25 |
| D15 | Data-residency phone | 27 |
| D16 | Lifecycle/resource timeline | 28 |

## Appendix C — Short version

For a short technical walkthrough, present:

**1 → 3 → 4 → 5 → 6 → 7 → 10 → 11 → 12 → 13 → 14 → 16 → 17 → 20 → 25 → 31 → 35 → 36**

Keep Slide 6 (master pipeline), Slide 14 (offline artifact prerequisite), and Slide 31
(release gate): together they prevent the presentation from overstating current validation.

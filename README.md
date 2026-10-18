# Face Locker

**Unlock your phone with your face — built with OpenCV, before ML Kit made it a one-liner.** Face Locker is an Android lock-screen app (package `wavetech.facelocker`) that replaces the system lock screen with one that recognizes your face, with pattern and PIN unlock as fallbacks.

This is a 2021-era project built on the classic computer-vision stack — Haar/LBP cascades for detection and an LBPH recognizer for identification — rather than a modern ML SDK. It's kept as a record of how face unlock was hand-rolled in the pre-ML-Kit days.

## How it works

1. **Enroll** — `FaceRegister` captures samples of your face from the front camera.
2. **Train** — samples train an OpenCV **LBPH face recognizer** (`LBPHFaceRecognizer.create(2, 8, 8, 8, 200)`, via JavaCV/JavaCPP bindings); the model is stored on-device.
3. **Detect** — faces are found with the bundled **LBP cascade** (`res/raw/lbpcascade_frontalface.xml`) through a `CascadeClassifier`.
4. **Lock & unlock** — `LockscreenService` (started on boot by `LockscreenIntentReceiver`) presents the `LockScreen` activity as a HOME/launcher surface; the camera checks the face against the trained model and unlocks on a match.
5. **Fallbacks** — if face unlock isn't available or doesn't match, `PatternActivity` (pattern lock) and `PincodeActivity` (PIN) provide alternate unlock paths.

## Tech stack

| Piece | Choice |
|---|---|
| Language / UI | Java, Android support libraries (appcompat-v7 28) |
| Vision | OpenCV 3.4.3 via JavaCV / JavaCPP (bundled jars in `app/libs/`, incl. FFmpeg) |
| Detection | LBP cascade classifier |
| Recognition | LBPH face recognizer |
| Target | compileSdk 28 · minSdk 26 (Android 8.0+) |

## Screenshots

<!-- Add screenshots here: enrollment, lock screen, pattern fallback. -->

## Build & run

1. Open the project in Android Studio and let it Gradle-sync.
2. Run on a **physical device** — you need a working front camera, which most emulators can't provide meaningfully.
3. Grant the camera permission, enroll your face from the main screen, then enable the lock switch.

Expect some archaeology: this project predates AndroidX (support libraries), and its OpenCV/JavaCV dependencies ship as local jars rather than Maven artifacts.

## Permissions (from the manifest)

| Permission | Why |
|---|---|
| `CAMERA` (+ front-camera features) | Face enrollment and unlock |
| `READ/WRITE_EXTERNAL_STORAGE` | Storing face samples / model files |
| `DISABLE_KEYGUARD`, `WAKE_LOCK`, `SYSTEM_ALERT_WINDOW`, `FOREGROUND_SERVICE`, `REORDER_TASKS` | Presenting and running the lock screen |
| `RECEIVE_BOOT_COMPLETED` | Restarting the lock-screen service on boot |
| `READ_PHONE_STATE` | Lock-screen behavior around calls |

That permission list is exactly why projects like this belong to the pre-scoped-storage, pre-biometric-API era — today you'd build this with BiometricPrompt instead, and this repo documents why that API needed to exist.

## Status

Unmaintained 2021 project, kept for portfolio/history. A prebuilt `FaceLocker.apk` from the original build is in `files/`.

## License

Apache-2.0 — see [LICENSE](LICENSE).

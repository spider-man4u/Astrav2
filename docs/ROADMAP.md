# ASTRA roadmap

## Phase 1 — Offline-first foundation
- [x] Create an ASTRA working branch separate from the upstream default branch.
- [x] Change default AI routing to the local Ollama-compatible endpoint.
- [x] Block cloud inference unless its separate consent setting is enabled.
- [x] Block remote neural TTS unless its separate consent setting is enabled.
- [x] Require Android on-device speech recognition when the OS supports it.
- [x] Turn off always-on microphone listening by default.
- [x] Update initial ASTRA app labels and default identity.
- [x] Document known privacy gaps instead of claiming complete privacy.

## Phase 2 — On-device AI runtime
- [ ] Integrate llama.cpp for Android (arm64) or another maintained on-device runtime.
- [ ] Add a GGUF model manager with model metadata, disk-space checks, cancellation and checksum verification.
- [ ] Benchmark small quantized models on the target tablet for RAM use, speed, heat and battery drain.
- [ ] Route all chat, agent, vision and task-planning inference to the local runtime.
- [ ] Ensure that unavailable models produce a clear local error, never a cloud fallback.

## Phase 3 — Offline speech
- [ ] Integrate a bundled/offline speech-to-text engine such as whisper.cpp.
- [ ] Add an offline wake-word detector with a visible microphone state and an easy stop control.
- [ ] Verify local Android TTS voice data or bundle a supported local TTS voice.
- [ ] Test airplane-mode speech input and output on supported devices.

## Phase 4 — Private storage
- [ ] Move memory from SharedPreferences to an encrypted SQLite store.
- [ ] Create and protect encryption keys using Android Keystore.
- [ ] Migrate API keys, bot tokens and integration credentials to secure storage.
- [ ] Exclude private app data from Android backup or encrypt backup material.
- [ ] Add memory review, export, individual deletion and complete wipe controls.
- [ ] Add tests for migration, deletion, restore and key invalidation.

## Phase 5 — Internet tools and updates
- [ ] Introduce a central online capability policy for search, weather, news, traffic and updates.
- [ ] Send only a minimum public-information query; never silently attach personal memory or chat history.
- [ ] Use HTTPS by default and restrict cleartext exceptions to a verified local endpoint.
- [ ] Add update checks, release-note display, checksum/signature verification and user-confirmed installation.
- [ ] Add a visible network activity log that records destination and purpose without recording private query content.

## Phase 6 — Android agent safety
- [ ] Audit accessibility, notification listener, SMS, contacts, camera and storage permissions.
- [ ] Make each integration opt-in and explain exactly what it can read or change.
- [ ] Keep high-impact actions behind confirmation.
- [ ] Add automation allow-lists, action previews and an emergency stop.

## Phase 7 — Verification and release
- [ ] Run Flutter analysis and unit/widget tests.
- [ ] Build and install a debug APK on the target tablet.
- [ ] Test airplane mode, poor connectivity, model unavailable and update failure paths.
- [ ] Perform outbound network inspection and permission review.
- [ ] Publish a privacy report and a release checklist with each stable build.

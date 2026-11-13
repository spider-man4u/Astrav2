# ASTRA privacy audit

Audit scope: the fork's Flutter settings, AI service, voice services, Android manifest and native speech bridge.

## Findings and first changes

| Area | Baseline finding | Foundation-branch change |
|---|---|---|
| AI provider | Fresh settings defaulted to DeepSeek; several cloud providers were available. | Local Ollama-compatible endpoint is the default. Cloud inference requires a separate opt-in and is blocked by the AI service otherwise. |
| AI endpoint detection | A privacy-mode flag could make the service classify a configured URL as local. | Local status is now checked against the actual endpoint host. |
| Neural TTS | Edge TTS sends text to Microsoft's speech endpoint. | Remote TTS is disabled by default and guarded in the synthesis service. |
| Speech recognition | Native code explicitly used the standard Android SpeechRecognizer and documented Google Speech Services as its intended provider. | Native code now requires Android's on-device recognizer and reports unavailable when the OS says it is not supported. Always-on listening defaults off. |
| Branding / disclosure | Android labels and assistant prompts used inherited JARVIS / PrivateAgent branding. | Initial visible app branding and default assistant identity changed to ASTRA; accessibility disclosure describes local processing and cloud opt-in. |

## Risks still open — do not call the app fully private yet

1. **Conversation memory is unencrypted.** `ConversationalMemoryService` stores facts and recent chat history in SharedPreferences. SharedPreferences is app-private storage, but the current code does not provide application-level encryption.
2. **Credentials are unencrypted.** AI keys, Home Assistant tokens and Telegram credentials are also stored through SharedPreferences. Migrate secrets to Android Keystore-backed secure storage.
3. **No bundled local model yet.** The app's local provider points at `127.0.0.1:11434`. A local Ollama-compatible runtime must be installed and running; ASTRA does not yet bundle llama.cpp or a model manager.
4. **On-device speech availability varies.** Voice input now fails closed if Android reports no on-device recognizer. The FlutterTts output path selects only voices explicitly reported with `network_required=false`; if the engine does not expose an offline voice, speech output is unavailable. A bundled offline STT/TTS engine is still needed for consistent behavior across devices.
5. **Cleartext traffic is broadly enabled.** The Android manifest currently has `usesCleartextTraffic="true"`. Restrict cleartext to the loopback endpoint or remove it after verifying the local runtime and LAN integrations.
6. **The Internet permission remains.** It is required for online retrieval and updates. Removing it would also disable those features. Network access must be audited per feature and must not be treated as proof that requests are safe.
7. **Sensitive Android permissions remain broad.** Contacts, SMS, camera, storage, usage access, accessibility and notification-listener capabilities exist in the manifest. Each feature needs an explicit permission explanation and least-privilege review.
8. **Online tools have not all been routed through a central network policy yet.** The AI inference and remote TTS paths are guarded in this patch; each search, news, weather, traffic, smart-home, Telegram and other network-capable service still needs an individual data-flow review.
9. **No packet-capture or offline-mode test has been run.** A code review alone cannot establish that no data leaves the device.

## Release-blocking verification

Before describing a release as private/offline:

- Run chat and agent mode with airplane mode enabled and verify they use only the local model.
- Inspect outbound connections for chat, memory, voice, online lookup, update check and failure paths.
- Verify that online lookups send only the search query, not stored memory or chat history.
- Test that cloud AI and remote TTS are blocked after a cold start and after settings migrations.
- Verify encrypted storage, backup behavior, deletion and key invalidation.
- Review all runtime permissions and confirm that disabling each permission deactivates only the relevant feature.

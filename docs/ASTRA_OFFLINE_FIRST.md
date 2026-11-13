# ASTRA — Offline-first architecture

## Product rule

ASTRA is **offline-first, not offline-only**.

The assistant's private core must remain useful without a network connection. Internet access is a separate capability used only for tasks that need current external information or for obtaining updates.

## Data boundaries

### Local by default

The following must stay on the device:

- LLM inference and prompt construction.
- Conversation history, long-term memory and user profile.
- Personal files, contacts, messages, notifications and screen contents.
- Android automation decisions and execution state.
- Speech recognition and speech playback, when a verified offline engine is installed.

A cloud model must never be selected as an automatic fallback when the local model is unavailable.

### Online retrieval

ASTRA may connect for an explicitly requested task such as web search, current weather, news, traffic, public information or checking for updates.

For a retrieval request:

1. Send only the minimum query needed to retrieve public information.
2. Do not attach chat history, memory, contacts, notifications, screenshots, files or the full system prompt.
3. Show that the request requires the internet.
4. Keep retrieval results separate from private memory and local inference.
5. Return to local-only operation after the request.

A search query can itself disclose information to the search provider. The UI must make that clear when a query contains personal details.

### Separate opt-ins

- **Cloud AI inference:** off by default. Enabling it may send the prompt, conversation context and any included screen context to the selected provider.
- **Remote neural TTS:** off by default. Enabling it sends the response text to the speech provider.
- **Online retrieval:** available for internet-dependent tools. It must not use cloud AI as an implicit inference fallback.

## Local runtime direction

The current branch exposes two loopback endpoints: Ollama at `127.0.0.1:11434/v1` and llama.cpp server at `127.0.0.1:8080/v1`. These are connection targets, not bundled runtimes: a local server and model still need to be installed and started. The target Android runtime is a bundled/on-device llama.cpp engine with GGUF models, so a separate desktop Ollama server is not required. Model downloads should be user-triggered or explicitly approved, integrity-checked and stored in app-private storage.

Speech recognition must use a true on-device engine. Android's on-device SpeechRecognizer is used only when Android reports it is available. The current FlutterTts path now selects only installed Android voices whose `network_required` property is explicitly false; if none are reported, ASTRA does not speak rather than guessing. The longer-term target is a bundled offline engine such as whisper.cpp so speech behavior does not depend on the device's installed speech provider.

## Updates

ASTRA may check for updates while online. Downloading an update is not the same as installing it:

- Show the version, source and release notes.
- Verify checksums and, when available, signed release metadata.
- Ask before downloading large models or APKs.
- Ask before applying an update.
- Never upload user memory or diagnostic contents as part of update checks.

## Current implementation status

This document describes the intended privacy contract, not a claim that every part has shipped.

The first foundation patch adds a local AI default, a cloud inference consent gate, a remote TTS consent gate, and on-device SpeechRecognizer checks. The existing memory service still uses SharedPreferences without encryption; a local inference engine and bundled offline speech models are also still to be implemented. See [PRIVACY_AUDIT.md](PRIVACY_AUDIT.md) and [ROADMAP.md](ROADMAP.md).

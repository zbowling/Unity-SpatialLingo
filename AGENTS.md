# Agent Instructions — Spatial Lingo

Spatial Lingo is a Unity mixed-reality app for Meta Quest that teaches languages by identifying real-world objects around the user. It combines the Passthrough Camera API (PCA), MR Utility Kit, Voice SDK, Interaction SDK, the Llama API, and on-device ML inference. Supports both hand tracking and controllers.

## Source-of-truth files (read these first, do not duplicate their contents in this file)

For setup, build steps, SDK versions, and project layout, read:

- `README.md` — official setup, feature overview, and Llama key configuration
- `Documentation/MetaSdk.md`, `Documentation/StateMachine.md` — Meta SDK usage and visual-scripting state machine
- `Packages/com.meta.utilities.llamaapi/README.md` — Llama API integration and key handling
- `Packages/com.meta.utilities.speechandtext/Documentation~/VoiceTranscription.md`, `VoiceSynthesis.md` — voice pipeline
- `Packages/com.meta.utilities.objectclassifier/Documentation~/ImageObjectRecognition.md`, `FaceBlurring.md` — object classifier
- `ProjectSettings/ProjectVersion.txt` — pinned Unity editor version
- `Packages/manifest.json` — Unity package versions
- `.gitattributes` — Git LFS filters; run `git lfs install` before cloning
- `LICENSE.md` — license terms

## Quest / Horizon-specific notes

- **Never ship a Quest app with the Llama API key embedded** — the README explicitly warns that keys can be extracted from the binary. For production use `LlamaRestApi.GetApiKeyAsync` (server-side auth); see `Packages/com.meta.utilities.llamaapi/README.md#configuration`.
- Passthrough Camera API access requires specific manifest permissions and a recent Horizon OS version. Verify against developer docs before stripping permissions while refactoring.
- The PCA-based experiences require Quest 3 / 3S hardware. Quest 2 / Pro cannot run the camera-driven scenes.
- The face-blurring pipeline in the object-classifier package is a privacy guardrail — do not disable it when modifying camera-frame handling.
- The README minimum editor and the actual `ProjectVersion.txt` pin may not match. Open in the version pinned by `ProjectVersion.txt` to avoid downgrading serialized assets.

## Meta Quest tooling

This repository is part of the Meta Quest / Horizon OS ecosystem (a sample, library, template, or related project — the bespoke intro above describes which). Use that intro and the source-of-truth files it references for project-specific decisions; don't restate or invent facts from memory.

When the user asks anything about Quest device behavior, build / deploy / debug / capture flows, on-device performance, or Horizon OS APIs, reach for these tools instead of generic Unity answers:

- **`hzdb`** — Quest-aware ADB wrapper (device list, install / launch / stop, logs, screenshots, Perfetto traces, on-device docs search). Already wired up as an MCP server via `.mcp.json`, `.vscode/mcp.json`, and `.cursor/mcp.json`. Also runnable directly: `npx -y @meta-quest/hzdb <subcommand>`.
- **Meta Quest Agentic Tools** — the full skill set, including Unity-specific skills: [github.com/meta-quest/agentic-tools](https://github.com/meta-quest/agentic-tools). Install per your client (Claude Code: `/plugin install meta-vr@meta-quest`; Gemini CLI: `gemini extensions install https://github.com/meta-quest/agentic-tools`; Cursor / VS Code: install the **Meta Horizon** extension from the Marketplace).

A few behavior expectations:

- **Read this repo's files first.** Before answering anything project-specific, read `README.md` and whichever source-of-truth files the intro above points at. Don't restate their contents in chat — quote or link instead.
- **Use `hzdb` for device-side work.** Anything that touches an attached Quest (install, launch, logs, screenshot, capture, manifest inspection) goes through `hzdb`, not raw `adb`.
- **Check live Horizon OS docs before answering API questions.** `hzdb docs search "..."` queries the live docs; training data on Horizon OS APIs goes stale fast.
- **Don't fabricate SDK / engine versions.** If a version isn't visible in this repo's files, say so rather than guessing.

# JARVIS — free, local, Windows voice assistant

JARVIS is an Electron app that runs entirely on your PC. No account, no API key,
no paid plan, no cloud. It talks to a local Ollama model through the Claude Agent
SDK with `ANTHROPIC_BASE_URL` pointed at `http://127.0.0.1:11434`. As of v1.4 it
also listens offline with whisper.cpp and speaks with a local British neural voice.

> **One app.** v1.4 checks what is already on the machine and installs only the
> missing pieces. There is no second setup file.

## Get it

**Release page:** https://github.com/laiss3r/jarvis/releases/latest

| Situation | What to download | Size |
| --- | --- | --- |
| New machine | `JARVIS-portable-win64.7z` | 99 MB |
| You already have the 99 MB app | `JARVIS-UPGRADE.cmd` | 1.5 KB |
| It won't answer | `JARVIS-CHECK.cmd` | 17 KB |

### Already downloaded the 99 MB app

Do **not** download 99 MB again. Put `JARVIS-UPGRADE.cmd` in the
`JARVIS-win32-x64` folder next to `JARVIS.exe`, close JARVIS, and double-click
the `.cmd`. It downloads a 46 KB patch, updates the unpacked folder *and* the
installed copy, and prints `Done`. Launch `JARVIS.exe`, then click **INSTALL THE
MISSING PARTS** once to fetch the 86 MB of voice components (or **CONTINUE
WITHOUT VOICE** to type instead).

### New machine

1. Unpack the `.7z` (7-Zip, or Windows 11 right-click → Extract All).
2. Double-click `JARVIS.exe`. Windows warns once — **More info → Run anyway** (unsigned build).
3. It copies itself to `%LOCALAPPDATA%\Programs\JARVIS`, creates a **JARVIS** shortcut on the
   Desktop and in the Start Menu, registers in Add/Remove Programs, and relaunches from the
   installed copy. Use that shortcut from now on.
4. Click **INSTALL THE MISSING PARTS** once and wait. The installer's output streams into
   the window. Up to 20–40 minutes if the 2.5 GB model has to download.
5. The interface opens on its own — its own window, not a browser tab.

## Voice mode

| Part | How it works |
| --- | --- |
| **Hold to talk** | Hold `SPACE` or the TALK button. The window captures 16 kHz mono PCM, the main process writes a WAV, `whisper-cli.exe` transcribes it. Release to send. |
| **Wake word** | Say `Hey Jarvis`, `OK Jarvis`, or just `Jarvis`. An energy gate transcribes only complete utterances after a pause, ignores ordinary conversation and ignores JARVIS while he is speaking. Say his name alone and he waits for the next sentence. Checkbox in the MICROPHONE panel turns it off. |
| **Spoken replies** | Kokoro 82M (`bm_george`, measured British RP baritone) runs locally on WebGPU, falls back to WASM/CPU, then to the Windows system voice. Streamed sentence-by-sentence. The VOICE picker cycles four British male voices when loaded. |
| **Offline** | Both the whisper model (77 MB) and Kokoro model (~86 MB) download once and are cached. No key, no account, no audio leaves the machine. |

An actual Paul Bettany voice clone is not possible in a free/local build — it needs
licensed voice data. `bm_george` is the closest approximation available.

## What the checklist checks

| Part | Detected by | If missing |
| --- | --- | --- |
| Node.js | `JARVIS_NODE`, Program Files, PATH | winget install |
| Ollama | local paths, PATH, `/api/version` | winget install + `ollama serve` |
| A local model | `GET /api/tags` | `ollama pull` of the smallest suitable model |
| The engine | `%USERPROFILE%\jarvis` with `node_modules` and `bridge/server.mjs` | `git clone` + `npm install` |
| Claude Code | `claude.exe`, the SDK bundled in the engine, or `~/.claude.json` | official installer, with winget as fallback |
| Local speech-to-text | `%LOCALAPPDATA%\JARVIS\stt\Release\whisper-cli.exe` + `ggml-tiny.en.bin` | pinned whisper.cpp b5454 CPU build + tiny.en model |

Everything already present is skipped. If a model is already on disk, `jarvis-brain`
is built **from that model** instead of pulling a new one. Ollama that is installed
but not running is started before the check. Voice input is the one optional row:
**CONTINUE WITHOUT VOICE - I WILL TYPE** skips the 86 MB download.

## Using it

- **MODEL** panel lists every model Ollama has; picking one switches the brain live.
- `-` / `+` change verbosity; `Esc` interrupts; `V` cycles the voice; `Enter` sends.
- `--no-install-prompt` skips the checklist and reports missing parts instead.
- `--no-install` / `JARVIS_NO_INSTALL=1` run portably without copying to Programs.
- `JARVIS_WHISPER_EXE` / `JARVIS_WHISPER_MODEL` override the STT paths;
  `JARVIS_STT_DIR` overrides the folder.
- Logs: `%APPDATA%\JARVIS\desktop.log`.
- Uninstall: **Add or Remove Programs → JARVIS**. That removes the app, shortcuts and
  registry entry only — Node, Ollama, your models, whisper and the engine are left alone.

## Troubleshooting

Run `JARVIS-CHECK.cmd`. It prints a **WHERE IT BREAKS** block, writes
`JARVIS-REPORT.txt` on the Desktop, and leaves the window open. Send that block.

Common causes of `thinking` / `words 0`: no model downloaded, Ollama not running, or the
engine not started. The checklist catches all three before the interface opens.

If the mic says *voice input not installed*, open the checklist again and install
the missing part. If the SESSION panel says *system (Kokoro unavailable)*, WebGPU
is not available on that GPU — replies still work with the Windows voice.

## Known limitations

- The packaged executable has not been run in this workspace (Linux). Verified here:
  build, archive integrity, code identity, PowerShell parse of generated scripts,
  HTML/inline scripts, the upgrader's patch flow, the installer's STT step in both
  fresh and reuse cases, and **73 automated checks** — including a real spoken
  sentence transcribed end-to-end by the pinned whisper.cpp build and model.
  Desktop shortcuts, WebGPU/Kokoro on your GPU, the Windows whisper.exe, actual
  microphone capture, Ollama discovery and the brain's replies need confirmation on
  your machine.
- Unsigned build: SmartScreen warns once.
- The upstream engine still contains browser-only voice paths (ElevenLabs Scribe,
  Chrome SpeechRecognition). The desktop window does not use them.

## Layout

```
desktop/main.js               Electron main process, diagnosis, installer, engine supervisor, IPC
desktop/preload.js            contextBridge API
desktop/install-ps.js         shortcut + HKCU uninstall PowerShell generator
desktop/stt.js                whisper.cpp discovery, WAV writer, transcription
desktop/kokoro-bridge.js      Vite-served module exposing the local neural voice
desktop-ui/app.html           interface: model picker, mic capture, wake word, speech
install-jarvis-local.ps1      bundled reuse-first dependency installer
installer.nsi                 older NSIS setup, kept for reference
JARVIS-CHECK.cmd / .ps1       hop-by-hop diagnostic
JARVIS-UPGRADE.cmd            small in-place upgrader for the 99 MB app
tests/                        model choice, UI, install scripts, install page, voice mode, STT
```

## License

MIT.

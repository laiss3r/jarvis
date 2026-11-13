# JARVIS — free, local, Windows voice assistant

JARVIS is an Electron app that runs entirely on your PC. No account, no API key,
no paid plan, no cloud. It talks to a local Ollama model through the Claude Agent
SDK with `ANTHROPIC_BASE_URL` pointed at `http://127.0.0.1:11434`.

> **One app.** v1.3 checks what is already on the machine and installs only the
> missing pieces. There is no second setup file.

## Get it

**Release page:** https://github.com/laiss3r/jarvis/releases/latest

| Situation | What to download | Size |
| --- | --- | --- |
| New machine | `JARVIS-portable-win64.7z` | 99 MB |
| You already have the 99 MB app | `JARVIS-UPGRADE.cmd` | 1.5 KB |
| It won't answer | `JARVIS-CHECK.cmd` | 17 KB |

### New machine

1. Unpack the `.7z` (7-Zip, or Windows 11 right-click → Extract All).
2. Double-click `JARVIS.exe`. Windows warns once — **More info → Run anyway** (unsigned build).
3. It copies itself to `%LOCALAPPDATA%\Programs\JARVIS`, creates a **JARVIS** shortcut on the
   Desktop and in the Start Menu, registers in Add/Remove Programs, and relaunches from the
   installed copy. Use that shortcut from now on.
4. If anything is missing, a checklist appears. Click **INSTALL THE MISSING PARTS** once and
   wait. The installer's output streams into the window. Up to 20–40 minutes if the model has
   to download (about 2.5 GB).
5. The interface opens on its own — its own window, not a browser tab.

### Already downloaded the 99 MB app

Do **not** download 99 MB again. Put `JARVIS-UPGRADE.cmd` in the `JARVIS-win32-x64` folder
next to `JARVIS.exe`, close JARVIS, and double-click the `.cmd`. It downloads a 25 KB patch,
updates the unpacked folder and the installed copy, and prints `Done`. Then launch `JARVIS.exe`.

## What the checklist checks

| Part | Detected by | If missing |
| --- | --- | --- |
| Node.js | `JARVIS_NODE`, Program Files, PATH | winget install |
| Ollama | local paths, PATH, `/api/version` | winget install + `ollama serve` |
| A local model | `GET /api/tags` | `ollama pull` of the smallest suitable model |
| The engine | `%USERPROFILE%\jarvis` with `node_modules` and `bridge/server.mjs` | `git clone` + `npm install` |
| Claude Code | `claude.exe`, the SDK bundled in the engine, or `~/.claude.json` | official installer, with winget as fallback |

Everything already present is skipped. If a model is already on disk, `jarvis-brain` is
built **from that model** instead of pulling a new one. Ollama that is installed but not
running is started before the check, so a rebooted machine isn't mistaken for an empty one.

## Using it

- **MODEL** panel lists every model Ollama has; picking one switches the brain live.
- `-` / `+` change verbosity; the mic button listens; `Esc` closes blades.
- `--no-install-prompt` skips the checklist and reports missing parts instead.
- `--no-install` / `JARVIS_NO_INSTALL=1` run portably without copying to Programs.
- Logs: `%APPDATA%\JARVIS\desktop.log`.
- Uninstall: **Add or Remove Programs → JARVIS**. That removes the app, shortcuts and
  registry entry only — Node, Ollama, your models and the engine are left alone.

## Troubleshooting

Run `JARVIS-CHECK.cmd`. It prints a **WHERE IT BREAKS** block, writes
`JARVIS-REPORT.txt` on the Desktop, and leaves the window open. Send that block.

Common causes of `thinking` / `words 0`: no model downloaded, Ollama not running, or the
engine not started. The checklist in v1.3 catches all three before the interface opens.

## Known limitations

- The Electron build cannot use `webkitSpeechRecognition`; microphone capture has to move
  into the main process. **Not fixed yet.**
- Unsigned build: SmartScreen warns once.
- Packaged executable has not been run in this workspace (Linux). Verified here: build,
  archive integrity, code identity, PowerShell parse of generated scripts, HTML/inline
  script of the install page, the upgrader's patch flow, and 46 automated checks.
  Desktop shortcut creation, Ollama discovery and the mic need confirmation on Windows.

## Layout

```
desktop/main.js               Electron main process, diagnosis, installer, engine supervisor
desktop/preload.js            contextBridge API
desktop/install-ps.js         shortcut + HKCU uninstall PowerShell generator
desktop-ui/app.html           interface (model picker, blades, mic)
install-jarvis-local.ps1      bundled dependency installer (reuse-first)
installer.nsi                 older NSIS setup, kept for reference
JARVIS-CHECK.cmd / .ps1       hop-by-hop diagnostic
tests/                       node tests (model choice, UI, install script, install page)
```

## License

MIT.

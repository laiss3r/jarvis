# JARVIS

A free, local, offline voice assistant for **Windows**. It runs entirely on your own
machine with [Ollama](https://ollama.com) — no accounts, no API keys, no cloud, no
subscription. It listens, it answers out loud, and it keeps working with the network
cable unplugged.

## Install

1. Download **JARVIS-Setup.exe** from [Releases](../../releases/latest) and run it.
   It installs Node, Ollama, the local model, the voice and the engine.
2. Talk to it: Start Menu > JARVIS > JARVIS, or say the wake word once it is running.

## Optional: the app window

**JARVIS.exe** (portable, 98 MB) runs JARVIS as its own application instead of a
browser tab. Unpack it anywhere and double-click. It starts the engine itself,
hidden, and shuts it down when you close the window. No console window appears.

You still need step 1 first — the app is the window, not the brain.

## Checksums (SHA-256)

```
DCA10EF79B2E9E16BA2CD2889F5114860FFBC030D15C874E971B5FFCC0482E3B  JARVIS-Setup.exe
73BE170F8104BF9062F1F64A8B2883E7F40807FCEFE1CBC4AD9196DBE7C883BF  JARVIS-portable-win64.7z
```

Verify on Windows:

```cmd
certutil -hashfile JARVIS-portable-win64.7z SHA256
```

## Notes

- The builds are unsigned, so Windows SmartScreen warns once: **More info > Run anyway**.
- The app is an Electron shell; the 98 MB is mostly the Chromium runtime. The
  assistant itself is 140 KB of HTML and JavaScript.
- Engine log: `%LOCALAPPDATA%\JARVIS\desktop\desktop.log`

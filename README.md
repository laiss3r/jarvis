# JARVIS

A free, local, offline voice assistant for **Windows**. It runs entirely on your own
machine with [Ollama](https://ollama.com) - no accounts, no API keys, no cloud, no
subscription. It listens, it answers out loud, and it keeps working with the network
cable unplugged.

## Install

1. Download **JARVIS-Setup.exe** from [Releases](../../releases/latest) and run it.
   It installs Node, Ollama, the local model, the voice and the engine.
2. Download **JARVIS-portable-win64.7z**, unpack it anywhere, and double-click
   **JARVIS.exe** *once*. It installs itself from there:
   - copies itself to `%LOCALAPPDATA%\Programs\JARVIS`
   - puts **JARVIS** on the Desktop and in the Start Menu
   - registers itself in Add or Remove Programs, with a working uninstaller
   - relaunches from the installed copy, so the shortcut is permanent

   After that, launch it from the desktop icon and forget the folder you unpacked.

## Which models it uses

The app asks Ollama what is installed and lists every model in the MODEL panel, so
any model you have downloaded can be the brain. Switch while it runs; it restarts the
engine and remembers your choice. If the configured model is missing it picks the
best one that exists, and it never picks an embedding model, which cannot talk.

## When it will not answer

Download **JARVIS-CHECK.cmd** from the same release and double-click it. It tests every
hop - Ollama, the model, the Anthropic endpoint the CLI uses, the bridge on 8787, the
interface on 5173, the install itself - repairs what it can, and ends with a
`WHERE IT BREAKS` verdict plus `JARVIS-REPORT.txt` on your Desktop.

## Checksums (SHA-256)

```
95E96D8B90A6A2F21FF65563E49534BCBE37558DE678AD95799A2F3543E23C6E  JARVIS-portable-win64.7z
DCA10EF79B2E9E16BA2CD2889F5114860FFBC030D15C874E971B5FFCC0482E3B  JARVIS-Setup.exe
```

Verify on Windows: `certutil -hashfile JARVIS-portable-win64.7z SHA256`

## Notes

- Unsigned builds: SmartScreen warns once - **More info > Run anyway**.
- The app is an Electron shell; most of the 99 MB is the Chromium runtime. The
  assistant itself is about 70 KB of HTML, JavaScript and one installer module.
- Install skipped? Run with `--no-install`, or see
  `%LOCALAPPDATA%\JARVIS\desktop\desktop.log`.
- Uninstalling removes the app, shortcuts and registry entry, and leaves your engine,
  Node, Ollama and downloaded models alone - those cost hours to get back.

---
name: rea-reversing
description: Reverse-engineer binaries, apps, and captures with the installed `rea` CLI. Use whenever the user asks to analyze, decompile, inspect, trace, or reverse engineer a binary, executable, APK/IPA, Electron or JavaScript app, .NET assembly, firmware, WASM, model file, HAR/pcap capture, or to find how an unfamiliar app implements a feature — even if they don't mention "rea" or "reverse engineering".
---

# REA: Reverse Engineer Anything

`rea` (v6.x) is installed on this system. It analyzes software the user owns or is authorized to examine, entirely locally, and returns structured results with **Evidence, limitations, and next probes**. It is not for attacking systems the user has no rights to; if the request looks like unauthorized access, malware creation, or evasion, decline that part.

## Workflow

1. **Orient.** Run `rea inspect <path>` (or `rea analyze <path>`) on the target. The result includes observations and suggested next probes — follow those rather than guessing.
2. **Drill down.** Use targeted commands (`function`, `decompile`, `trace`, `search`, `xrefs`, `inspect-*`) based on what the user wants to know.
3. **Report with evidence.** State conclusions and cite the evidence/limitations REA returned. Never present a guess as fact; say what is confirmed vs. inferred.

## Output control (important)

Output can be huge. Control it with the global flags:

- `--format json|yaml|md|toon|jsonl` — pick a compact format; `md`/`yaml` for reading, `json` when saving for follow-up.
- `--filter-output <keys>` — keep only the keys you need (e.g. `--filter-output summary,procedures[0,3]`).
- `--token-limit <n>` / `--token-offset <n>` — page through huge results.
- `--token-count` — check size before dumping.

Prefer `--format md` (or filtered JSON) for direct reading; save full JSON to a file when the user will iterate on the analysis.

## Command map

Choose by target type. Full manifest: `rea --llms` (or `rea --llms-full`). Per-command schema: `rea <command> --schema`, help: `rea <command> --help`.

**Any target — start here**
- `rea inspect <path>` — overview with evidence and next probes
- `rea analyze <path>` — deep overview; `--provider <id>` to bind an engine (Hopper/Ghidra/IDA/Kuna where installed)
- `rea capabilities` / `rea providers` / `rea doctor` — what's available and healthy

**Native binaries (PE/ELF/Mach-O)**
- `rea function <path> [addr]` / `rea decompile <path> [addr]` (or `--procedure <name>`, `--file-offset <off>`) — pseudocode with evidence
- `rea instructions`, `rea xrefs <path> <addr>`, `rea search <path> <pattern>` — disassembly, cross-references, strings/names
- `rea inspect-pe-imports|pe-certificates|pe-resources|elf-notes|native-exports|binary-layout <path>` — static structure
- `rea trace-native-values`, `rea resolve-native-call-targets`, `rea inspect-native-api` — dataflow and API boundaries
- `rea decompile-all <path>` — dump all pseudocode to a directory
- `rea debug-with-gdb <cmd>` / `rea debug-with-rizin <path> <cmd>` — live debugging

**JavaScript / Electron apps**
- `rea analyze-javascript-application <path>` — static reconstruction (modules, imports, Electron boundaries)
- `rea inspect-electron-fuses`, `rea list-electron-targets`, `rea observe-javascript-runtime` — packaging and runtime

**Android / mobile**
- `rea inspect-android-package <apk>`, `rea inspect-android-class`, `rea inspect-android-method` — static APK analysis
- `rea decode-android-resources`, `rea inspect-apk-signature`, `rea identify-flutter-build`
- `rea list-adb-devices` … `rea read-adb-logcat` — live device interaction via adb

**.NET / managed**
- `rea inspect-managed-artifact|managed-members <path>`, `rea compare-managed-members`

**Other artifacts** — `rea inspect-artifact <path>` first; specialized: firmware (`extract-firmware`, `inspect-firmware-regions`), WASM (`inspect-wasm-artifact`), GGUF/safetensors (`inspect-model-artifact`), pcap (`inspect-pcap-capture`), HAR (`inspect-web-network-capture`), SQLite, Python pyc, Go/Rust binaries, EVM, kernel modules, devicetree, Apple .ips crashes.

**Web (authorized targets)**
- `rea inspect-web-page <endpoint>`, `rea capture-web-screenshot`, `rea analyze-web-bundle`, `rea export-web-scripts`

## Behavior notes

- REA is read-first: most `inspect-*` commands never execute the target. Commands that do execute things (debug, Frida `instrument-with-frida`, adb package install, browser scenarios) say so in their help — use deliberately.
- `compare`/`trace`/`analyze-*` commands that take `<inputJson>` expect a path to a JSON Evidence file produced by an earlier step. Chain steps by saving JSON, then passing the file.
- Analysis snapshots: `rea analyze <path> --snapshot <file>` persists progress for large binaries.
- If a required engine (Hopper/Ghidra/IDA) is missing, `rea doctor` says so; static JS/.NET/Android analysis works without one.
- On Windows, use absolute paths.

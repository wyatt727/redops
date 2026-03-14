<div align="center">

<img src="docs/images/redops_logo.png" alt="RedOps Mobile" width="280"/>

### The penetration testing platform that turns your phone into a weapon.

[![Android](https://img.shields.io/badge/Android-26%2B-34A853?style=flat-square&logo=android&logoColor=white)](https://developer.android.com)
[![Kotlin](https://img.shields.io/badge/Kotlin-1.9.24-7F52FF?style=flat-square&logo=kotlin&logoColor=white)](https://kotlinlang.org)
[![Compose](https://img.shields.io/badge/Jetpack_Compose-Material3-4285F4?style=flat-square&logo=jetpackcompose&logoColor=white)](https://developer.android.com/jetpack/compose)
[![Frida](https://img.shields.io/badge/Frida-Integrated-EF4444?style=flat-square)](https://frida.re)
[![Claude](https://img.shields.io/badge/Claude_AI-Agent-D97706?style=flat-square)](https://anthropic.com)
[![Lines](https://img.shields.io/badge/91%2C000%2B_Lines-Kotlin-A855F7?style=flat-square)](#architecture)
[![License](https://img.shields.io/badge/License-Proprietary-1F2937?style=flat-square)](#license)

---

**RedOps Mobile** is a full-stack Android penetration testing platform — a native Kotlin/Compose UI fused with a Kali Linux chroot backend. No laptop. No USB tethering. Just your rooted phone running 17 security tools, an AI agent, and the full Kali toolchain from your pocket.

[Features](#features) · [How It Works](#how-it-works) · [Quick Start](#quick-start) · [The pq Tool](#the-pq-tool) · [Architecture](#architecture) · [Building](#building-from-source)

</div>

---

## Why This Exists

Every mobile pentester knows the pain: you're testing an app on a phone, but your actual tools are on a laptop connected over ADB. You context-switch between Burp Suite, Frida CLI, jadx, drozer, and a dozen terminal tabs. Your phone is just a test device. The intelligence lives somewhere else.

**RedOps Mobile inverts this.** The phone *is* the pentesting platform. The UI runs natively on the device. The backend runs inside a NetHunter chroot with the full Kali arsenal. An AI agent powered by Claude connects every tool into a coherent investigation. The result is something that didn't exist before: an integrated, on-device mobile security platform that replaces a desk full of tools.

---

## Features

### Target Analysis

Select any installed app and get an instant security profile.

- **Component Scanner** — discovers every exported Activity, Service, Receiver, and Provider. Flags 20+ vulnerability types: missing permissions, debuggable, task hijacking, insecure deep links, cleartext traffic, backup misconfigs
- **Framework Detection** — identifies Flutter, React Native (Hermes/JSC), Unity (IL2CPP/Mono), Xamarin, and native. Recommends the right decompilation strategy
- **Decompilation** — JADX, Hermes decompiler, Blutter (Flutter), IL2CPP Dumper. Run locally or offload to VPS. Streams progress in real-time
- **Manifest Inspection** — full AndroidManifest.xml security audit with inline vulnerability annotations
- **Data Extraction** — pull SharedPreferences and SQLite databases. Edit values directly. Flip feature flags, inject tokens, escalate privileges

### Traffic Interception

Capture 100% of an app's network traffic. Not "most of it" — all of it.

- **Library Coverage** — hooks OkHttp3, HttpURLConnection, Volley, Cronet, gRPC, WebSocket, and native SSL. If the app makes a network call, RedOps sees it
- **Spawn-Mode Capture** — launch with hooks pre-loaded. Capture auth flows, startup API calls, and token exchanges that attach-mode misses
- **SSL Pinning Bypass** — certificate pinning, public key pinning, network security config — all handled automatically alongside capture
- **Session Management** — timestamped sessions with JSONL storage. Export to SQLite for external analysis
- **Scope Control** — include/exclude URL patterns to focus on what matters

### HTTP Repeater

Captured a request? Edit it and send it again.

- **Full Request Editor** — modify method, URL, headers, query parameters, and body
- **Response History** — track how the server responds to each modification
- **Parameter Toggling** — enable/disable parameters without deleting them
- **Export to Agent** — send any request to the AI for deeper analysis

### IPC Monitor

Map every inter-process communication vector for a target app.

- **Automated Discovery** — parses `dumpsys` output to find all exported components, intent filters, content provider authorities, and deep link schemes
- **Vulnerability Enrichment** — automatically flags task hijacking targets, `file://` deep link schemes, implicit intent manipulation surfaces, and unprotected component access
- **Autofire** — select discovered components and fire test intents in bulk with stealth enforcement
- **Replay** — re-send captured IPC events with modified payloads

### Intent Builder & Interceptor

Two-tab interface for intent construction and live interception.

- **Build** — construct intents from scratch with action/category/data pickers, extra data editors, and component targeting. Launch with `startActivity`, `startService`, or `sendBroadcast`
- **Intercept** — monitor in-flight intents in real-time. Inspect, modify, block, or forward

### Task Hijack PoC Generator

Found a task affinity vulnerability? Generate a signed proof-of-concept APK in seconds.

- Detects vulnerable task affinity configurations from the manifest scan
- Generates a PoC APK with matching task affinity
- Signs it using the chroot's build tools
- Installs and demonstrates the hijack

### Web Pentesting (CDP)

Bridge native and web security testing through Chrome DevTools Protocol integration.

- **Chrome CDP** — connect to Chrome on the device for full WebView and browser testing
- **Two Modes** — `browse` for ad-hoc recon (disposable scratch), `start` for persistent pentest sessions with organized output
- **Full Audit Suite** — crawl pages, audit security headers/CSP, inspect cookies, extract JWTs, analyze forms/CSRF, discover endpoints
- **JS Execution** — evaluate arbitrary JavaScript inside WebView/browser contexts
- **Screenshots** — labeled, timestamped captures organized per target

### RCE & Native Scanning

Automated vulnerability scanning at both DEX and native layers.

- **RCE Scan** — discovers DEX-layer RCE vectors: `Runtime.exec`, `DexClassLoader`, WebView JavaScript bridges, deserialization sinks, intent redirect chains
- **Native Scan** — analyzes native `.so` libraries for unsafe functions, symbol exposure, and exploitable patterns

### PQ Manager

GUI control panel for the `pq` Frida toolkit.

- **Server Lifecycle** — start/stop Frida with stealth naming (`media_session_d`) and custom socket forwarding
- **Script Browser** — discover available Frida hooks, mark favorites, launch with one tap
- **Gate Status** — visual indicators for every active spawn gate
- **Live Output** — real-time Frida output with ANSI color rendering

### AI Agent

A Claude-powered security analyst that understands your entire investigation.

- **Context-Aware** — feed it scan results, captured traffic, decompiled code, or raw findings. It knows what tools you've run and what data is available
- **Streaming Chat** — responses render in real-time via stream-json protocol. Not a web wrapper — the Claude CLI runs directly in the chroot
- **Multi-Session** — maintain parallel investigation threads. Sessions persist across app restarts via Room database. Resume any conversation with full history
- **Tool Integration** — the Agent can suggest next steps, generate Frida hooks, analyze traffic patterns, and connect findings across all your tools into a unified pentesting narrative
- **Report Generation** — `/report-workflow` produces a professional pentest engagement report. Reads all findings, notes, captures, and code, presents a summary for approval, then generates a polished HTML report with evidence and exports to PDF — full report in minutes
- **Asset Deployment** — automatically deploys and verifies the full agent toolkit (scripts, docs, skills, templates) to `/root/pentest/`

---

## How It Works

RedOps Mobile operates across two environments simultaneously:

```
 ANDROID HOST         CHROOT (Kali)
┌────────────────┐   ┌────────────────┐
│ Compose UI     │   │ /root/pentest/ │
│ ├ Target Dash  │   │ ├ pq (CLI)    │
│ ├ Traffic Cap  │   │ ├ spawn_gate  │
│ ├ IPC Monitor  │ → │ ├ pq_lib/    │
│ ├ Repeater     │su │ ├ docs/      │
│ ├ PQ Manager   │   │ ├ targets/   │
│ └ AI Agent     │   │ └ Claude CLI │
│                │   │              │
│ Room, Hilt DI  │   │ Frida, jadx  │
└───────┬────────┘   └──────┬───────┘
        └─ PqCommandExecutor ┘
        su → chroot → entry
```

The **Android host** runs the APK — all UI, navigation, database persistence, and state management happen here. The **NetHunter chroot** has the Kali Linux toolchain — Python, Frida, jadx, apktool, the pq CLI, and Claude.

**`PqCommandExecutor`** is the bridge. Every command from the UI is routed through `su` → `chroot` → `android-entry` (which sets `PATH`, `HOME`, and locale) → the actual tool.

<details>
<summary>Execution modes</summary>

| Method | Route | Use Case |
|--------|-------|----------|
| `execute()` | chroot → android-entry → bash | Standard commands |
| `executeAndroid()` | `su -mm` (no chroot) | Host-only operations |
| `executeSimple()` | nohup + background | Long-running detached tasks |
| `executeQuick()` | 1-second timeout | Status checks |
| `executeShellScript()` | configurable timeout | Decompilation scripts |
| `executeWithStreaming()` | returns `Flow<Event>` | Real-time progress UI |

</details>

---

## The pq Tool

`pq` is a custom-built Python CLI backed by 26 library modules (~43,000 lines of Python/JS) that manages all Frida operations. It replaces direct Frida usage with a higher-level interface that handles server lifecycle, stealth, spawn gating, multi-script merging, traffic capture, and interactive hook control. Frida server is auto-started in stealth mode before any command that needs it.

```bash
# Discovery
pq packages [filter]              # List apps (PID if running)
pq classes com.app -f Manager     # Find classes by pattern
pq classes com.app -f Manager -m  # Show methods too
pq methods com.app "ClassName"    # Method signatures

# Spawn Gating — injected at process creation
pq gate com.app hook.js           # Persistent injection
pq gate com.app s1.js,s2.js       # Multi-script (merged)
pq gate com.app hook.js --respawn # Re-hook on restart
pq gate com.app --mitm            # Traffic from first byte
pq gate com.app --mitm --native   # + native (Flutter, etc)
pq gate com.app --mitm hook.js    # Traffic + custom hooks
pq gate send com.app "status()"   # Interactive control
pq gate reload com.app new.js     # Hot-reload scripts

# Traffic
pq traffic com.app --recent 50    # Query requests
pq traffic com.app --search token # Full-text search
pq sessions com.app               # List sessions
pq traffic com.app --export       # Export to SQLite + md

# Attach (running process, no Zygote contamination)
pq attach com.app hook.js         # Hook running process (PairIP-safe)

# RCE & Native Scanning
pq rce-scan com.app               # DEX-layer RCE vector scan
pq native-scan com.app            # Native library vulnerability scan

# Web Pentesting (Chrome CDP)
pq web browse <url>               # Ad-hoc web session
pq web start --url <url> --target $PKG  # Persistent pentest session
pq web crawl                      # Discover pages/endpoints
pq web audit                      # Security audit (headers, CSP, etc.)
pq web cookies --audit            # Cookie security audit
pq web jwt                        # JWT extraction and analysis
pq web forms                      # Form/CSRF analysis
pq web screenshot --name <label>  # Labeled screenshot

# Management
pq status                         # What's running
pq gate list                      # Active gates
pq gate stop                      # Kill all gates
pq stop                           # Safe cleanup (all)
pq cleanup com.app                # Clean one app only
```

### Spawn Gating

The spawn gate system (`spawn_gate.py`) is the core of RedOps' Frida integration:

- **Spawn-based injection** — hooks are injected the moment a process is created, before any app code runs. This catches initialization logic that attach-mode misses
- **Crash survival** — with `--respawn`, the gate automatically re-injects hooks whenever the app restarts
- **Multi-script merging** — multiple Frida scripts are intelligently merged into a single `Java.perform()` block, preventing the crashes that occur when multiple scripts each call `Java.perform()` independently
- **Hot-reload** — send `SIGUSR1` to reload scripts without restarting the target app
- **Interactive control** — call exported functions or evaluate arbitrary JavaScript in the context of a running hook via `pq gate send`
- **PID registry** — all active gates tracked in `~/.pq/gate_pids.json` for reliable lifecycle management

### Stealth

Frida detection is common in hardened apps. `pq` runs Frida server under a stealth name (`media_session_d` or `sslkeylog`), communicates over a Unix socket instead of the default TCP port, and sets up ADB port forwarding to keep the connection off the network interface entirely.

### Flutter Introspection

Flutter apps compile to native ARM64 via Dart AOT, making traditional decompilation useless. RedOps has a three-tier system that turns them inside out:

**Tier 1 — `flutter_ctrl.js`** works on any Flutter app immediately. Load it with `pq gate` and get:
- **Channel sniffing** — intercepts all Platform Channel messages (Dart↔Java) in both directions, decoding both JSON and binary StandardMessageCodec
- **Navigation injection** — push arbitrary routes (`dart.nav('/admin')`)
- **Native hooking** — hook `libapp.so` offsets with Dart object extraction
- **Channel interception** — modify messages in-flight (e.g. flip `vipStatus` in API responses before they reach the UI)

**Tier 2 — Blutter bridge** adds full object introspection. Run Blutter's static analysis on `libapp.so`, then `gen_blutter_bridge.sh` fuses the class layouts with `flutter_ctrl.js` into a single `flutter_blutter.js`. Now every Dart object that passes through your hooks is readable — field names, types, nested objects, the full tree. Without the bridge, app objects show as opaque `<App:cid1141@ptr>`. With it, you get the actual `AtVipController` with its fields.

**Tier 3 — `pq gate send`** makes it interactive. Adjust interception rules, inspect state, call hook functions, and evaluate JS — all live, without restarting the app:

```bash
pq gate send com.app "call dart.sniffFull()"     # Start channel monitor
pq gate send com.app "call dart.channels()"       # What channels exist
pq gate send com.app "call dart.status()"          # Hook/engine summary
pq gate send com.app "call dart.findClass('Ctrl')" # Search app classes
```

### Domain Recon Pipeline

`pq enum-domains` runs a 6-phase automated reconnaissance pipeline against a target's extracted domains:

```bash
pq enum-domains com.app               # Run full pipeline
pq enum-domains com.app --force        # Re-run all phases from scratch
pq enum-domains com.app --skip-graphql # Skip GraphQL phase
```

| Phase | Tool | What it does |
|-------|------|-------------|
| 1. Domain Discovery | `extract-domains` | Extracts URLs/domains from decompiled APK source, separates target-owned from third-party |
| 2. Subdomain Enum | `subfinder` | Passive subdomain enumeration against discovered root domains |
| 3. DNS Resolution | `dnsx` | Resolves all subdomains, filters to live hosts |
| 4. HTTP Probing | `httpx` | Probes live hosts for HTTP endpoints with status codes, titles, and tech detection |
| 5. Firebase Assessment | `firebase-enum` | Auto-extracts API keys from source and google-services.json. Tests RTDB, Storage, and Firestore — both unauthenticated and with each key — for read/write access, shallow enumeration, common path exposure, and query bypass |
| 6. GraphQL Discovery | `enum-graphql` | Discovers GraphQL endpoints across all live hosts, tests for introspection and open access |

If the target has prior traffic capture sessions, observed hostnames are automatically injected into the pipeline — domains seen in real traffic get enumerated alongside those found in code.

Each phase is idempotent (skips if output exists, use `--force` to re-run). Individual phases can also be run standalone via `pq extract-domains`, `pq firebase-enum`, or `pq enum-graphql`.

Output goes to `targets/<pkg>/recon/` with structured subdirectories per phase.

---

## Quick Start

### Prerequisites

| Requirement | Details |
|-------------|---------|
| **Rooted Android** | Magisk, KernelSU, or APatch — any root solution works |
| **Kali NetHunter** | Required — provides the chroot environment with full Kali toolchain |
| **Frida 16.5.9+** | Auto-managed by `pq` — no manual setup |
| **ADB access** | From the NetHunter terminal |

### Install

```bash
# Build
cd /root/Tools/redops-mobile
./gradlew --no-daemon assembleDebug

# Install
adb install app/build/outputs/apk/debug/app-debug.apk
```

Launch the app and grant root permissions. RedOps automatically deploys the agent toolkit to `/root/pentest/` on first run.

### First Assessment

1. **Target tab** → Select an app → **Scan Components**
2. Review exported components, permissions, vulnerability findings
3. **Traffic Capture** → Start a spawn-mode capture → Use the target app → Stop capture → Browse requests
4. **Repeater** → Import an interesting request → Modify parameters → Replay
5. **AI tab** → Send findings to the Agent → Get analysis and next steps

### Typical Workflow

```
Select target app
  │
  ├─ Scan components ─── Exported? Task hijack? Deep links?
  ├─ Detect framework ── Flutter / Hermes / IL2CPP
  ├─ Decompile ───────── Source for Agent context
  │
  ├─ Capture traffic ─── Auth flows, API calls, tokens
  │   └─ Repeater ────── IDOR, parameter tampering
  │
  ├─ Monitor IPC ─────── Component enum, autofire
  ├─ Extract data ────── SharedPrefs, databases, tokens
  ├─ Hook with Frida ─── Custom scripts via pq gate
  │
  └─ AI Agent ────────── Connects findings, generates
                          hooks, writes the report
```

---

## Architecture

### Stats

| Metric | Value |
|--------|-------|
| **Kotlin Source Files** | 319 |
| **Lines of Code** | 91,000+ |
| **Feature Modules** | 14 |
| **Room Entities** | 13 |
| **Room DAOs** | 12 |
| **Database Migrations** | 22 |
| **Agent Scripts (Python/JS)** | 43,000+ lines |

### Tech Stack

| Layer | Technology |
|-------|------------|
| **UI** | Jetpack Compose, Material3, Compose Navigation 2.8.5 |
| **Architecture** | Clean Architecture (MVVM), 14 feature modules |
| **DI** | Hilt 2.52 |
| **Persistence** | Room 2.6.1 (22 migrations), DataStore, EncryptedSharedPreferences |
| **Networking** | OkHttp 4.12, Retrofit, Ktor |
| **Async** | Kotlin Coroutines 1.7.3 + Flow |
| **Build** | Gradle 8.12.1, AGP 8.7.3, KSP |
| **Backend** | Python 3 (pq + 26 library modules), Frida 16.5.9+, Claude CLI |
| **Target SDK** | 35 (Android 15) |
| **Min SDK** | 26 (Android 8.0) |

<details>
<summary><b>Module Map</b></summary>

```
app/src/main/java/com/redops/mobile/
├── feature/
│   ├── agent/              # Claude AI bridge, streaming I/O, session mgmt
│   ├── scanner/            # Component scanner, framework detection, vuln analysis
│   ├── trafficcapture/     # HTTP/S capture, session storage, scope filtering
│   ├── ipcmonitor/         # Dumpsys parsing, IPC enumeration, autofire, replay
│   ├── repeater/           # HTTP request editor, history, response diffing
│   ├── pqmanager/          # PqCommandExecutor, Frida lifecycle, gate management
│   ├── intents/            # Intent builder + interceptor (dual-tab)
│   ├── target/             # Consolidated target dashboard (4 sub-tabs)
│   ├── directprefs/        # Direct SharedPreferences extraction + editing
│   ├── manifest_inspection/# Manifest security audit
│   ├── netinfo/            # Network interface management
│   ├── home/               # Dashboard
│   ├── appselection/       # Target app picker
│   └── data/               # Shared feature data layer
├── core/
│   ├── domain/root/     # RootManager — su execution, root detection
│   ├── domain/intent/   # Intent analysis, fuzzing, vulnerability detection
│   ├── domain/apk/      # APK signing, AXML encoding, task hijack PoC
│   ├── ui/              # Shared composables (CodeBlock, TerminalOutput, etc.)
│   └── di/              # Hilt modules (App, Core, Database, Prefs)
├── data/                # Room database, 13 entities, 12 DAOs
├── navigation/          # Nav graph, destinations, per-tab back stacks
└── service/chroot/      # NetHunter verification, network interface mgmt
```

</details>

<details>
<summary><b>Cross-Tool Data Flow</b></summary>

Every tool feeds into every other tool:

```
Scanner findings ───────> AI Agent (analysis context)
Traffic captures ───────> Repeater (import for editing)
Traffic captures ───────> AI Agent (HTTP pattern analysis)
IPC discovery ──────────> Intent Builder (pre-filled components)
IPC discovery ──────────> AI Agent (vulnerability context)
Data extraction ────────> AI Agent (token/credential analysis)
Repeater requests ──────> AI Agent (request-level analysis)
AI Agent suggestions ──> Back to any tool (guided next steps)
```

</details>

---

## Compared To Traditional Tools

| Capability | Burp Suite | Frida CLI | drozer | jadx | RedOps Mobile |
|:-----------|:----------:|:---------:|:------:|:----:|:-------------:|
| Traffic interception | Desktop | Script-based | — | — | On-device, spawn-mode |
| Component scanning | — | — | CLI only | — | GUI + auto-vuln detection |
| IPC monitoring & replay | — | — | Partial | — | Full + autofire |
| Frida hook management | — | Manual | — | — | Spawn gating, hot-reload, multi-script |
| Framework detection | — | — | — | — | Auto (Flutter/Hermes/IL2CPP/RN) |
| Flutter introspection | — | — | — | — | Channel sniffing, live object reading, route injection |
| Decompilation | — | — | — | CLI only | Integrated + streaming progress |
| SharedPrefs editing | — | — | — | — | Direct GUI editor |
| Task hijack PoC | — | — | — | — | Auto-generate + sign |
| SSL pinning bypass | Burp cert | Per-script | — | — | Bundled, multiple techniques |
| RCE vector scanning | — | — | — | — | DEX + native layer analysis |
| WebView / CDP testing | — | — | — | — | Chrome DevTools Protocol integration |
| Domain recon pipeline | — | — | — | — | 6-phase automated recon |
| AI-powered analysis | — | — | — | — | Claude Agent with full context |
| Report generation | Manual | — | — | — | Automated engagement reports |
| Runs on phone | — | — | — | — | Native Android UI |

---

<details>
<summary><h2>Agent Documentation</h2></summary>

RedOps includes 14 comprehensive guides deployed to `/root/pentest/docs/`:

| Guide | Coverage |
|-------|----------|
| **PQ_COMPREHENSIVE.md** | Complete pq tool reference |
| **PQ.md** | Quick-reference pq cheatsheet |
| **FLUTTER_CTRL_GUIDE.md** | Flutter app analysis, Blutter bridge |
| **HERMES_DECOMPILATION.md** | React Native Hermes bytecode |
| **IL2CPP_GUIDE.md** | Unity game reverse engineering |
| **GAME_HACKING.md** | Economy manipulation, IAP bypass, ad rewards |
| **APK_MODIFICATION_GUIDE.md** | Decompile → patch → sign → install |
| **DOMAIN_RECON_WORKFLOW.md** | Subdomain enumeration, HTTP probing |
| **GRAPHQL_ENUM.md** | GraphQL introspection and endpoint discovery |
| **RADARE2_WORKFLOW.md** | Native binary analysis |
| **API_CHECKS.md** | API key/secret pattern matching |
| **DEV_ENVIRONMENT_CHECKS.md** | Developer environment misconfiguration detection |
| **PWA_INTERCEPTION.md** | Progressive Web App traffic interception |
| **WEB_PENTEST_GUIDE.md** | WebView and web application testing |

### Agent Skills

The AI agent has 4 specialized skills that encode multi-phase pentesting workflows:

| Skill | Purpose |
|-------|---------|
| **discovery** | 4-phase parallel reconnaissance (components, traffic, domains, code analysis) with merged synthesis |
| **rce-investigation** | Remote code execution vulnerability analysis pipeline |
| **report-workflow** | Automated engagement report generation (findings → HTML → PDF) |
| **web-discovery** | Web-specific enumeration and WebView security assessment |

</details>

---

## Building from Source

### Prerequisites

- **JDK 17** (OpenJDK)
- **Android SDK** with API 35
- **Gradle 8.11+** (wrapper included)

### Build

```bash
git clone <repository-url>
cd redops-mobile

# Debug build
./gradlew --no-daemon assembleDebug

# Release build
./gradlew --no-daemon assembleRelease
```

> **Always use `--no-daemon`.** The Gradle daemon consumes ~1.7GB and will crash memory-constrained devices.

### Output

| Build | Path |
|-------|------|
| Debug APK | `app/build/outputs/apk/debug/app-debug.apk` |
| Release APK | `app/build/outputs/apk/release/app-release.apk` |

---

<details>
<summary><h2>Dual File System</h2></summary>

Agent scripts exist in two places. Both must stay in sync.

| Location | Purpose | Edit When |
|----------|---------|-----------|
| `/root/pentest/` | Live deployed files | Testing changes immediately |
| `app/src/main/assets/agent/` | APK source | Persisting changes across installs |

```bash
# After testing changes in /root/pentest/:
./sync_to_source.sh                    # Sync deployed → source
./compare_deployed_vs_source.sh        # Verify sync
```

</details>

---

## License

Proprietary. All rights reserved.

---

## Acknowledgments

- [Frida](https://frida.re/) — dynamic instrumentation
- [jadx](https://github.com/skylot/jadx) — Android decompilation
- [mitmproxy](https://mitmproxy.org/) — HTTPS interception
- [Anthropic Claude](https://anthropic.com/) — AI agent backbone
- [Kali NetHunter](https://www.kali.org/docs/nethunter/) — mobile pentesting platform

---

<div align="center">

*Built for authorized security testing. Your phone is now the most dangerous thing in the room.*

</div>

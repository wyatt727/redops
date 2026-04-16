<div align="center">

<img src="screenshots/app_logo.png" alt="RedOps Mobile" width="200"/>

# RedOps Mobile

On-device Android application security tooling for rooted phones with a Kali NetHunter chroot.

[![Android](https://img.shields.io/badge/Android-26%2B-34A853?style=flat-square&logo=android&logoColor=white)](https://developer.android.com)
[![Kotlin](https://img.shields.io/badge/Kotlin-1.9.24-7F52FF?style=flat-square&logo=kotlin&logoColor=white)](https://kotlinlang.org)
[![Gradle](https://img.shields.io/badge/Gradle-8.12.1-02303A?style=flat-square&logo=gradle&logoColor=white)](https://gradle.org)
[![License](https://img.shields.io/badge/License-Proprietary-1F2937?style=flat-square)](#license)

</div>

RedOps Mobile is a native Android app that drives a full mobile application testing stack from the phone itself. The UI is Kotlin and Jetpack Compose; privileged work executes through `su` into a NetHunter chroot, then into the `pq` tooling and agent assets deployed under `/root/pentest/`. No laptop required mid-engagement — decompile, scan, capture, instrument, and write up findings all from the device.

---

## About the Builder

**Wyatt Becker, OSCP** — offensive security engineer with 7 years of direct penetration-testing experience. Shipped **RedOps Mobile** solo as a proprietary end-to-end mobile security platform: native Android UI, chroot bridge, Frida tooling, reporting pipeline, and AI agent integration.

Currently looking for **penetration tester**, **red team**, **cybersecurity engineer**, or **security consultant** roles. Remote worldwide or hybrid.

| | |
|---|---|
| **Email** | [wyatt.becker@gmail.com](mailto:wyatt.becker@gmail.com) |
| **Full resume** | [wobbz.myddns.me](https://wobbz.myddns.me/) |
| **LinkedIn** | [linkedin.com/in/wyattbecker](https://linkedin.com/in/wyattbecker) |
| **Live demo** | [wobbz.myddns.me/redops](https://wobbz.myddns.me/redops) |
| **Certs** | OSCP |

Real engagement results are documented in [Selected Findings](#selected-findings) below.

---

## Picking a Target

Every workflow starts from an installed package. The app selector lists installed apps with filtering and jumps straight into the target dashboard.

<div align="center">
<img src="screenshots/27_target_app_selection_list.jpg" alt="Target app selection" width="300"/>
</div>

---

## Selected Findings

Two critical vulnerabilities discovered during real engagements, driven end-to-end with RedOps Mobile. Both anonymized per NDA; technical chain components are stock public stack names and do not identify the targets.

### One unauthenticated `GET` → 301K-user database → webshell → full host compromise

**Target:** Production enterprise backend · 301K users
**Severity:** Critical · Full RCE

> 1. Credentials pulled from the APK via RedOps' SharedPreferences module
> 2. Reused on an unauthenticated Spring Boot Admin console
> 3. `GET /actuator/heapdump` → 260 MB of cleartext heap
> 4. MySQL, Redis, and Nacos passwords sitting inside the heap
> 5. Redis `CONFIG SET dir /www/wwwroot` + `dbfilename shell.php` + `SAVE`
> 6. PHP webshell written into the BT Panel webroot → full host RCE

Credential extraction, chain reproduction, and the final writeup all driven from the phone. No laptop on engagement.

<div align="center">
<img src="screenshots/23_agent_chat_rce_chain_writeup.jpg" alt="Agent writeup of the full RCE chain" width="320"/>
</div>

*Discovered during an authorized pentest engagement · anonymized per NDA*

### Zero-click, persistent account takeover across a 34M-MAU Flutter game

**Target:** Mass-market Android Flutter game · 34M+ MAU
**Severity:** CVSS 9.8 · Zero-click · Persistent

> 1. Traffic hooked with the RedOps CDP bridge (`pq attach --mitm`)
> 2. VPS reverse-proxied legitimate game assets while injecting malicious JS inline
> 3. Plugin-bridge Frida hooks intercepted every auth flow in real time
> 4. Exfiltrated Facebook OAuth tokens, Google auth codes, session JWTs, device IDs on every launch
> 5. Stolen Facebook token queried the Graph API directly from the in-app V8 context
> 6. Chained as the delivery vector for a second, persistent code-injection path

Flutter navigation discovery, V8 hooking, and live traffic interception all orchestrated from the phone. App behavior stayed normal to the victim.

<div align="center">
<img src="screenshots/28_agent_chat_exploit_chain_detail.jpg" alt="Agent writeup of the zero-click Flutter exploit chain" width="320"/>
</div>

*Discovered during an authorized pentest engagement · anonymized per NDA*

---

## Target Dashboard

The `Target` tab is a unified four-tab workspace for the selected app: **Overview**, **Manifest**, **Components**, and **Findings**. Everything you need for a first-pass static/dynamic assessment lives here.

### Overview — package info, framework detection, decompilation, scans

The Overview tab surfaces package metadata, auto-detects the app framework (Flutter, Hermes/React Native, IL2CPP/Unity, etc.), and exposes decompilation and scan entry points in one screen.

<div align="center">
<img src="screenshots/01_target_overview_app_info_framework_detection.jpg" alt="Overview with framework detection" width="280"/>
<img src="screenshots/04_target_overview_component_stats_data_extraction.jpg" alt="Component stats and data extraction" width="280"/>
</div>

Decompilation can run locally on-device or be offloaded to a VPS for heavier targets. The tool dropdown picks the right decompiler for the detected framework.

<div align="center">
<img src="screenshots/02_target_overview_decompiler_selection_dropdown.jpg" alt="Decompiler selection dropdown" width="280"/>
<img src="screenshots/22_target_overview_vps_decompile_options.jpg" alt="VPS decompile options" width="280"/>
</div>

Native and RCE scans run from the same screen and feed directly into the Findings tab.

<div align="center">
<img src="screenshots/03_target_overview_rce_scan_results.jpg" alt="RCE scan results" width="300"/>
</div>

### Manifest — security flags and permissions

Parses `AndroidManifest.xml` and flags risky flags, exported components, intent filters, and sensitive permissions.

<div align="center">
<img src="screenshots/05_target_manifest_security_flags_permissions.jpg" alt="Manifest security review" width="300"/>
</div>

### Components — exported surface

Enumerates exported activities, services, receivers, and providers. Each row is a jumping-off point into the component test dialog — tap a row to open a prebuilt intent builder with suggested actions, categories, and data URIs.

<div align="center">
<img src="screenshots/06_target_components_exported_activities.jpg" alt="Exported components" width="280"/>
<img src="screenshots/30_target_component_test_dialog.jpg" alt="Component test dialog" width="280"/>
</div>

### Findings — consolidated, filterable, agent-ready

All scan results land here with severity filtering. Findings can be exported into an agent session for triage and writeup, and specific findings (task hijacking, intent redirection, deep link takeover, etc.) open dedicated test dialogs with one-click POC APK generation.

<div align="center">
<img src="screenshots/07_target_findings_summary_severity_filters.jpg" alt="Findings summary" width="280"/>
<img src="screenshots/31_target_finding_task_hijacking_dialog.jpg" alt="Task hijacking test dialog" width="280"/>
</div>

---

## Tools

The `Tools` tab groups everything operational: traffic capture, HTTP repeater, IPC monitor, SharedPreferences editor, and the PQ/Frida manager.

<div align="center">
<img src="screenshots/09_tools_menu_overview.jpg" alt="Tools menu" width="300"/>
</div>

### Traffic Capture

HTTP/HTTPS capture is driven by a Frida-based MITM (attach or spawn) and stores entries in a Room-backed session database. Pick a target, confirm the capture mode, then browse requests and responses with full body inspection and scope filtering.

<div align="center">
<img src="screenshots/10_traffic_capture_target_app_selection.jpg" alt="Traffic capture target selection" width="240"/>
<img src="screenshots/20_traffic_capture_confirm_start_dialog.jpg" alt="Capture start confirmation" width="240"/>
<img src="screenshots/11_traffic_capture_request_list.jpg" alt="Request list" width="240"/>
</div>

<div align="center">
<img src="screenshots/12_traffic_capture_request_detail_body_json.jpg" alt="Request detail — JSON body" width="280"/>
<img src="screenshots/19_traffic_capture_entry_detail_params_headers.jpg" alt="Entry detail — params and headers" width="280"/>
</div>

### HTTP Repeater

A Burp-style request repeater with persistent request/response pairs. Edit headers, method, body, resend, diff responses.

<div align="center">
<img src="screenshots/13_http_repeater_edit_request_body.jpg" alt="Repeater — edit request body" width="280"/>
<img src="screenshots/14_http_repeater_response_json_body.jpg" alt="Repeater — JSON response" width="280"/>
</div>

### PQ Manager — Frida server and hooks

The `pq` tool is the primary interface for all Frida operations. PQ Manager is its Android front-end: server lifecycle, stealth-named binary, favorites, hook/script discovery, and one-tap launch.

<div align="center">
<img src="screenshots/15_pq_manager_frida_server_favorites.jpg" alt="PQ Manager — server and favorites" width="280"/>
<img src="screenshots/16_pq_manager_add_favorites_frida_hooks.jpg" alt="Add favorite hooks" width="280"/>
</div>

Favorites expand to a detail view with the script source and parameters. Running a hook against a live app shows up as an in-game overlay, HUD, or whatever the script chooses to draw.

<div align="center">
<img src="screenshots/25_pq_manager_favorite_expanded_detail.jpg" alt="Favorite expanded detail" width="280"/>
<img src="screenshots/26_frida_hook_ingame_cheat_menu_overlay.jpg" alt="Frida hook overlay" width="280"/>
</div>

### Data — SharedPreferences browser and editor

Enumerates every app that has SharedPreferences on the device, then opens into a per-app entry editor with a banner that flags world-readable/writable files and other security smells.

<div align="center">
<img src="screenshots/17_shared_prefs_package_list.jpg" alt="SharedPrefs package list" width="280"/>
<img src="screenshots/21_shared_prefs_editor_entries_security_banner.jpg" alt="SharedPrefs entry editor" width="280"/>
</div>

---

## Agent

The `Agent` tab is a chat/session workflow backed by the Claude CLI running inside the chroot, pre-loaded with the bundled `/root/pentest/CLAUDE.md` context, skills, and docs. Sessions can start fresh or open pre-loaded with target findings, traffic exports, or a full web setup. Both engagements in [Selected Findings](#selected-findings) above came out of sessions just like this one — the agent chained the vulnerabilities and wrote the exploit paths while I drove the investigation.

<div align="center">
<img src="screenshots/08_agent_chat_new_session.jpg" alt="Agent — new session" width="300"/>
</div>

---

## Framework-Specific Tooling

### Flutter

Dart-compiled apps get dedicated tooling: live navigation route enumeration via a Frida overlay, and `blutter` snapshot analysis launched straight from the target view.

<div align="center">
<img src="screenshots/24_flutter_nav_routes_overlay.jpg" alt="Flutter navigation overlay" width="280"/>
<img src="screenshots/29_flutter_blutter_script_overview_launch.jpg" alt="Flutter blutter launch" width="280"/>
</div>

---

## Architecture

RedOps Mobile operates across two execution environments:

```
         ANDROID HOST (phone)                    NETHUNTER CHROOT (Kali ARM64)
                                                 /data/local/nhsystem/kali-arm64
  RedOps APK (Kotlin/Compose)
  ├─ PqCommandExecutor ──── su/chroot/entry ──> /root/pentest/scripts/pq
  ├─ ClaudeBridge ──────── su/chroot/entry ──> claude CLI (stream-json)
  ├─ ClaudeMdManager ───── deploys assets ──> /root/pentest/*
  │
  │  /data/local/tmp/
  │  └─ pq_wrapper.sh ──── setsid+chroot ──> android-entry → pq
  │
  │  /data/local/.cache/
  │  └─ media_session_d ← Frida server        /usr/local/bin/
     (stealth-named)                           └─ android-entry (sets PATH/HOME)
```

Most privileged operations flow through `Android UI → su → chroot → android-entry → pq/tooling`. `PqCommandExecutor` is the primary bridge.

---

## Requirements

### Device

- Root via Magisk, KernelSU, APatch, or equivalent
- Android 8.0+ (API 26+)
- Kali NetHunter chroot installed at `/data/local/nhsystem/kali-arm64`
- Python venv inside the chroot at `/data/local/nhsystem/kali-arm64/venv`
- Network access on first bootstrap (Frida server download)

### Build

- JDK 17
- Android SDK with API 35
- Gradle wrapper included
- ADB

### Hard-coded Runtime Paths

| Purpose | Path |
|---|---|
| NetHunter root | `/data/local/nhsystem/kali-arm64` |
| Chroot entry bridge | `/usr/local/bin/android-entry` |
| Python venv | `/data/local/nhsystem/kali-arm64/venv` |
| `pq` CLI | `/root/pentest/scripts/pq` |
| Host-side wrapper | `/data/local/tmp/pq_wrapper.sh` |
| Agent deployment root | `/root/pentest/` |
| Frida server (stealth) | `/data/local/.cache/media_session_d` |

The app bootstraps `pq`, agent assets, and `pq_wrapper.sh` into these locations on startup. Frida is provisioned automatically when missing.

---

## Build

```bash
# Debug (primary)
./gradlew --no-daemon assembleDebug

# Tracing (debug + agent tracing)
./gradlew --no-daemon assembleTracing

# Release
./gradlew --no-daemon assembleRelease
```

Always pass `--no-daemon` — the Gradle daemon holds ~1.7GB of RAM and will crash the phone on incremental builds.

| Variant | Output |
|---|---|
| Debug | `app/build/outputs/apk/debug/app-debug.apk` |
| Tracing | `app/build/outputs/apk/tracing/app-tracing.apk` |
| Release | `app/build/outputs/apk/release/app-release.apk` |

Install with `adb install -r <apk>`.

---

## First Run

1. Build the debug APK and install it.
2. Launch the app and grant root.
3. Confirm the NetHunter chroot is reachable and `/root/pentest/` has been populated.
4. Open `Target`, pick a package, and run the initial scan.

Manual environment sanity check:

```bash
adb shell su -c 'test -d /data/local/nhsystem/kali-arm64 && echo chroot-ok'
adb shell su -c 'chroot /data/local/nhsystem/kali-arm64 /bin/bash -lc "test -x /root/pentest/scripts/pq && echo pq-ok"'
adb shell su -c 'test -f /data/local/.cache/media_session_d && echo frida-ok'
```

---

## Bundled Agent Assets

The APK carries agent-side docs, skills, and scripts that are deployed into `/root/pentest/` on startup.

**Skills**: `discovery`, `rce-investigation`, `report-workflow`, `web-discovery`

**Docs** (non-exhaustive): `PQ_COMPREHENSIVE.md`, `MCP_SERVER.md`, `WEB_PENTEST_GUIDE.md`, `FLUTTER_CTRL_GUIDE.md`, `DOMAIN_RECON_WORKFLOW.md`, `GRAPHQL_ENUM.md`, `HERMES_DECOMPILATION.md`, `IL2CPP_GUIDE.md`, `GAME_HACKING.md`

See `app/src/main/assets/agent/` for the full set.

---

## Repo Layout

```text
app/src/main/java/com/redops/mobile/
  core/            Shared domain, shell, root, UI, persistence
  feature/         agent, scanner, target, trafficcapture, repeater,
                   pqmanager, ipcmonitor, intents, data, manifest_inspection
  navigation/      Cross-tab nav graph
  service/chroot/  NetHunter environment verification

app/src/main/assets/agent/
  CLAUDE.md        Agent system prompt (deployed to /root/pentest/CLAUDE.md)
  scripts/         pq CLI and library modules
  docs/            Pentest guides
  skills/          Claude skills
  templates/       Checklist templates

docs/              Interception writeups, troubleshooting, architecture notes
screenshots/       Screens used in this README
```

---

## Dual File System

Agent-facing files live in two places during development:

| Location | Purpose |
|---|---|
| `/root/pentest/` | Live deployed runtime on the device/chroot |
| `app/src/main/assets/agent/` | Source of truth packaged into the APK |

Helpers: `sync_to_source.sh`, `compare_deployed_vs_source.sh`, `deploy_from_source.sh`.

---

## Safety and Scope

Intended for authorized security testing and research on systems you are permitted to assess.

## License

Proprietary. All rights reserved.

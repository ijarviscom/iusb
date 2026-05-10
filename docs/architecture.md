# Architecture

iUSB is a portable, cross-platform diagnostic scanner built around four principles:

1. **Use native OS tools, not bundled dependencies.** Anything iUSB needs is already on the machine.
2. **Read-only by default.** No writes to the customer's machine outside opt-in elevated diagnostics.
3. **Offline by default.** No network calls during a Community-edition scan.
4. **One source of truth per module.** Each module owns one diagnostic area and outputs one JSON fragment.

## High-level layout

```
USB stick mounted to customer machine
  │
  ├── launcher.sh                 (entry for macOS and Linux)
  ├── launcher.ps1                (entry for Windows)
  │
  ├── scanners/
  │   ├── windows/                (PowerShell modules)
  │   │   ├── system.ps1
  │   │   ├── cpu.ps1
  │   │   ├── memory.ps1
  │   │   ├── disk.ps1
  │   │   ├── battery.ps1
  │   │   ├── network.ps1
  │   │   ├── security.ps1
  │   │   ├── services.ps1
  │   │   ├── boot.ps1
  │   │   └── software.ps1
  │   │
  │   ├── macos/                  (bash + macOS native tools)
  │   │   └── ... (mirrors windows/ layout)
  │   │
  │   └── linux/                  (bash + distro-aware adapters)
  │       └── ... (mirrors windows/ layout)
  │
  ├── report/                     (cross-platform Python or pure HTML+JS)
  │   ├── render.py
  │   ├── template.html
  │   └── styles.css
  │
  ├── config.json                 (shop branding, labor rate, thresholds)
  └── README.txt                  (printed help and version info)
```

## Launcher responsibilities

The launcher is the only piece that knows about the OS. Its job is:

1. Detect the host OS and architecture.
2. Load and validate `config.json`.
3. Dispatch to the right `scanners/<os>/` directory.
4. Collect every module's JSON output.
5. Hand off to the report renderer.
6. Print a one-line summary to the terminal: total time, total findings, total estimated cost.

Modules do not know about each other. The launcher serializes them, but a future version can parallelize independent modules without changing module code.

## Module contract

Every module, regardless of OS, follows the same contract:

1. **Input:** A subset of `config.json` (the module's own section plus shop-wide settings like labor rate).
2. **Process:** Call native OS tools, read system state, never write outside the USB stick.
3. **Output:** A single JSON document conforming to `examples/scan-output.example.json`'s `modules[]` schema.
4. **Exit code:** 0 on success, non-zero on hard failure. A module's findings can include severity-graded issues; that is not a failure.

A module that needs elevation declares so in its metadata and the launcher prompts once for the entire scan.

## Severity model

Every finding carries one of four severities:

| Severity | Meaning | Customer-facing color |
|---|---|---|
| `CRIT` | Stops the customer from using the machine, or imminent failure | Red |
| `WARN` | Degraded performance, security risk, or aging hardware | Amber |
| `INFO` | Worth knowing, but not urgent | Blue |
| `PASS` | Module ran and found no issue | Green |

The report sorts CRIT first, then WARN, then INFO. PASS findings appear in a compact footer summary.

## Why not a single binary?

A compiled binary would be faster to launch, but it would:

- Trip aggressive AV more often than plain script files
- Be harder for an IT department to audit (no source visibility)
- Require platform-specific code signing for every architecture
- Make it harder for a shop to make a small tweak for their workflow

Script-based architecture trades a small launch-time cost for trustworthiness and customizability. For a tool that's plugged into customer machines, that trade is right.

## How updates propagate

iUSB does not self-update during a scan (it has no network). To update:

1. The shop receives a new signed bundle (or pulls from the public release once v1.0 ships).
2. The shop copies the bundle onto the USB stick, overwriting the previous version.
3. Next scan uses the new code.

`config.json` is preserved across updates (the launcher merges new defaults rather than overwriting).

## Reporting pipeline

```
scanner module 1 ──┐
scanner module 2 ──┤
scanner module 3 ──┼──> launcher merge ──> report renderer ──> single HTML file
...                │                                              │
scanner module 10 ─┘                                              └─> JSON sidecar
```

The HTML file is fully self-contained: every style, every image, every script is inlined. The technician hands one file to the customer, period.

## Design constraints we honor

- **No internet.** Community-edition modules never call out, never resolve DNS for anything other than diagnostic purposes (i.e. `nslookup` is a finding, not a beacon).
- **No persistence.** No registry keys, no scheduled tasks, no LaunchAgents, no systemd units, no shell history pollution.
- **No PII reads.** No file content reads under user home directories, no browser history, no saved-password access.
- **No silent elevation.** Every elevation prompt is preceded by a launcher line explaining which module needs it and why.

## What changes in Pro

Pro adds three things to this architecture:

1. An optional network call to the Anthropic Claude API for AI-powered finding triage, with the JSON output as input and the enhanced findings as output. The customer machine still does not connect; the tech's machine sends the JSON.
2. A cloud sync option that uploads the JSON (not the HTML report, not the customer machine identity) to a per-shop dashboard.
3. A separate fix-script library that the tech reviews and runs after the scan, never automatically.

Pro is built so it can be turned off and the scanner behaves exactly like Community.

## What changes in Enterprise

Enterprise adds:

1. SSO / SAML / SCIM provisioning for the dashboard tier.
2. On-premises deployment of the dashboard (no cloud at all).
3. Audit logs covering every scan, every fix-script run, every user action.
4. Air-gapped environment support (signed update bundles delivered by physical media).
5. Custom integrations into PSA, RMM, and ticketing systems.

The scanner itself is unchanged from Community in Enterprise environments. The differences are all in what's around the scanner.

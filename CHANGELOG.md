# Changelog

All notable changes to iUSB are recorded in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Public source release under MIT license (target: Q3 2026)
- Printer queue and USB device enumeration modules (target: v1.1)
- Spanish, French, and Brazilian Portuguese localization (target: v1.5)
- Pro tier with cloud dashboard and AI finding triage via Claude API (target: Q3 2026)
- Enterprise tier with SSO, audit logs, on-premises deployment, air-gapped support (target: Q4 2026)

## [1.0-beta] - 2026-05-10

First private beta release to selected repair shops.

### Added

- Cross-platform launcher (PowerShell on Windows, bash on macOS and Linux)
- 10 diagnostic modules: System ID, CPU & Thermals, Memory, Disk / SMART, Battery, Network, Security, Services & Logs, Boot Performance, Software Audit
- HTML report generator with severity badges (CRIT / WARN / INFO / PASS)
- `config.json` for shop branding, labor rate, and threshold tuning
- Native-tool wrappers for Windows (WMI / CIM, PowerShell, powercfg, Defender)
- Native-tool wrappers for macOS (system_profiler, diskutil, ioreg, pmset, sysctl)
- Native-tool wrappers for Linux (dmidecode, lscpu, smartctl, lm-sensors, journalctl, upower)
- Linux package manager autodetection: apt, dnf, pacman, zypper
- JSON output mode for integration with PSA / RMM / ticketing systems
- Quick mode (`--quick`) running only critical-severity modules
- Module skip and include filters (`--skip`, `--only`)
- Read-only operation enforced: no writes to the customer machine outside elevated diagnostic commands
- Fully offline operation: zero network calls during a scan

### Known limitations

- BitLocker-protected drives: pre-boot decryption status detected; in-use partitions limited to non-encrypted metadata
- ARM Macs: full support on M1 / M2 / M3 / M4; M4 Max-specific thermal sensor IDs validated through May 2026
- Server editions: Windows Server 2019 and 2022 supported; older builds not tested
- Exotic Linux distros without systemd: best-effort, file a [scanner compatibility report](.github/ISSUE_TEMPLATE/scanner_compatibility.md)

### Security

- All security disclosure routed through hello@ijarvis.ai per [SECURITY.md](SECURITY.md)
- Canonical security.txt published at https://iusb.us/.well-known/security.txt

[Unreleased]: https://github.com/ijarviscom/iusb/compare/v1.0-beta...HEAD
[1.0-beta]: https://github.com/ijarviscom/iusb/releases/tag/v1.0-beta

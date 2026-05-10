# Supported Platforms

iUSB runs on every form factor where you can plug in a USB stick and reach a normal shell. This document is the canonical reference for what's supported.

## Form factors

All of these are tested and supported. The Battery module emits "no battery present" on machines without a battery; every other module runs identically.

| Form factor | Notes |
|---|---|
| Laptops | The primary design target. All modules apply. |
| Desktops | Tower, small form factor, micro. Battery module skipped. |
| All-in-ones | iMacs, Surface Studios, HP EliteOnes, Lenovo ThinkCentre M-series, etc. |
| Workstations | HP Z, Lenovo ThinkStation, Dell Precision, Apple Mac Studio / Mac Pro. |
| Mini PCs / NUCs | Intel NUCs, ASUS PNs, Beelink, Minisforum, GMKtec, Apple Mac mini. |
| Towers | Custom builds, gaming desktops, prosumer workstations. |
| Servers | Rack-mount, tower servers. Windows Server 2019+, RHEL, Rocky, Alma, Ubuntu Server. |
| Chromebooks | Supported via Linux developer mode (Crostini). Native ChromeOS support on the v1.1+ roadmap. |
| Embedded / single-board | Raspberry Pi, Jetson, Pine64 with a Linux distro and USB host port. Best effort. |

## Architectures

| Architecture | Status | Coverage notes |
|---|---|---|
| **x86_64 (Intel)** | Full | All Intel Core, Xeon, Atom, Pentium, Celeron generations from 2015 onward. Older silicon is best-effort. |
| **x86_64 (AMD)** | Full | Ryzen, EPYC, Threadripper, Athlon. Older FX-series is best-effort. |
| **ARM64 (Apple Silicon)** | Full | M1, M2, M3, M4 and Pro / Max / Ultra variants. |
| **ARM64 (Windows on ARM)** | Full | Surface Pro X, Surface Pro 9 5G, Surface Pro 11 ARM, Snapdragon X laptops. |
| **ARM64 (Linux)** | Full | Raspberry Pi 4/5, Ampere Altra, AWS Graviton (if running interactively), generic ARM servers. |
| **ARM32 (Linux)** | Best effort | Raspberry Pi Zero / 1 / 2 / 3 not officially tested. |
| **RISC-V** | Roadmap | StarFive VisionFive, SiFive Unmatched on the post-v1.0 roadmap. |

## Windows

| Edition | Support |
|---|---|
| Windows 10 (Home, Pro, Enterprise, Education) | Yes, 22H2 builds and later. Earlier builds best-effort. |
| Windows 11 (all editions including 23H2, 24H2) | Yes, full support. |
| Windows 10 / 11 LTSC | Yes. |
| Windows Server 2019 | Yes. |
| Windows Server 2022 | Yes. |
| Windows Server 2025 | Yes once GA. |
| Windows on ARM | Yes. |
| Windows 7 / 8 / 8.1 | No. EOL. |
| Windows Server 2016 and older | No. Best effort, not tested. |

**Required:** PowerShell 5.1+ (ships with Windows 10 and later). PowerShell 7 is supported but not required.

**Optional:** `smartmontools` for richer SMART data. The scanner detects whether it's installed and degrades gracefully if not.

## macOS

| Version | Support |
|---|---|
| macOS 15 Sequoia | Yes. |
| macOS 14 Sonoma | Yes. |
| macOS 13 Ventura | Yes. |
| macOS 12 Monterey | Yes. |
| macOS 11 Big Sur | Best effort. No new development. |
| macOS 10.15 Catalina and earlier | No. |

| Chip | Support |
|---|---|
| Apple Silicon M1 / M1 Pro / M1 Max / M1 Ultra | Yes. |
| Apple Silicon M2 / M2 Pro / M2 Max / M2 Ultra | Yes. |
| Apple Silicon M3 / M3 Pro / M3 Max | Yes. |
| Apple Silicon M4 / M4 Pro / M4 Max / M4 Ultra | Yes (sensor IDs validated through May 2026). |
| Intel x86_64 (any supported macOS) | Yes. |

**Required:** A standard shell (`bash` and `zsh` both work). No Homebrew, no MacPorts.

**Optional:** `smartmontools` via Homebrew or USB-bundled copy. Without it, SMART data is limited to what `diskutil` and `ioreg` expose.

## Linux

iUSB autodetects the distribution and the package manager and runs the right native commands.

| Distro | Package manager | Tested as of |
|---|---|---|
| Ubuntu (22.04, 24.04 LTS, latest non-LTS) | apt | May 2026 |
| Debian (11, 12, 13 testing) | apt | May 2026 |
| Linux Mint (latest LTS) | apt | May 2026 |
| Pop!_OS (latest) | apt | May 2026 |
| Fedora (40, 41, 42) | dnf | May 2026 |
| RHEL (8, 9) | dnf | May 2026 |
| Rocky Linux (8, 9) | dnf | May 2026 |
| AlmaLinux (8, 9) | dnf | May 2026 |
| openSUSE Leap (15.x), Tumbleweed | zypper | May 2026 |
| Arch Linux (rolling) | pacman | May 2026 |
| Manjaro | pacman | May 2026 |
| EndeavourOS | pacman | May 2026 |
| elementary OS (latest) | apt | Best effort |
| Kali Linux (latest) | apt | Best effort, not officially in scope |

**Required tools** (the scanner checks for them at launch and reports a clear error if any are missing):

- A POSIX-compliant shell (`bash` 4+, `zsh`, or `dash`)
- `dmidecode` (in `dmidecode` package on all major distros)
- `smartctl` (in `smartmontools` package)
- `lscpu` (in `util-linux`, virtually always installed)
- One of: `journalctl` (systemd) or readable `/var/log/syslog`

**Optional tools** that unlock additional findings:

- `lm-sensors` for thermal data
- `upower` for battery diagnostics on laptops
- `nvme-cli` for richer NVMe SMART
- `iw` for wireless probing

**Init system:** systemd is the assumed default. The Services & Logs module includes a fallback path for SysV-init (Slackware, very old Debian) and OpenRC (Alpine, Gentoo), but those are best-effort.

## ChromeOS

ChromeOS Flex (Intel and AMD) and standard ChromeOS run iUSB via Linux developer mode (Crostini). Steps:

1. Enable Linux on the Chromebook (Settings > Developers > Linux development environment).
2. Open the Terminal app and `cd` to the iUSB mount (typically `/mnt/chromeos/removable/`).
3. Run `./launcher.sh`.

Native ChromeOS support (no Crostini required) is on the v1.1+ roadmap.

## Servers and headless machines

iUSB requires an interactive shell. For a headless server, this means you SSH in (or attach a keyboard and monitor temporarily), mount the USB stick, and run the launcher. The output prints to the terminal you're attached to, and the HTML report writes to the USB stick.

For fully air-gapped server fleets, the Enterprise tier provides a bundle-and-deliver workflow: technician runs the scan locally, the JSON output is exported to a sneakernet drive, and a central dashboard ingests it offline.

## Filing a new compatibility report

If you hit a combination that fails, [file a scanner compatibility report](../.github/ISSUE_TEMPLATE/scanner_compatibility.md). Concrete failure logs from a real machine are how this matrix grows.

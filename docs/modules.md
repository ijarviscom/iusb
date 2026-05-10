# Modules

iUSB ships ten diagnostic modules. Each module is independent, contract-compliant ([see architecture](architecture.md#module-contract)), and produces severity-graded findings.

This document is the per-module specification: what each module reads, what native tools it uses on each OS, and what findings it can emit.

## 1. System ID

**Purpose:** Identify the machine. Every report starts with this.

| OS | Native tools |
|---|---|
| Windows | `Get-CimInstance Win32_ComputerSystem`, `Win32_BIOS`, `Win32_OperatingSystem`, registry inspection |
| macOS | `system_profiler SPHardwareDataType`, `sw_vers`, `sysctl hw` |
| Linux | `dmidecode`, `/proc/cpuinfo`, `/etc/os-release`, `hostnamectl` |

**Findings:**

- Manufacturer, model, serial number
- BIOS / UEFI version and age
- OS version and edition
- Uptime
- Optional: warranty status (Pro tier only, looked up offline against shop-cached vendor data)

Always `INFO` severity. Treated as identification, not diagnosis.

## 2. CPU & Thermals

**Purpose:** Catch thermal throttling, dead fans, and overclocked-and-unstable systems.

| OS | Native tools |
|---|---|
| Windows | `Win32_Processor`, `Get-Counter '\Processor(*)\% Processor Time'`, `Win32_TemperatureProbe` (limited), optional OpenHardwareMonitor / LibreHardwareMonitor adapter |
| macOS | `sysctl machdep.cpu`, `powermetrics --samplers smc` (requires elevation), `ioreg -l` for sensor fan data |
| Linux | `lscpu`, `sensors` (lm-sensors), `/sys/class/thermal/`, `cpufreq` reads |

**Findings:**

- Sustained throttling under benchmark load (`WARN`)
- Critical temperature reads (`CRIT`)
- Fan RPM reading at zero with non-zero CPU load (`CRIT`)
- Unusual core-count or mismatched-frequency reads (`WARN`)
- Healthy thermal envelope (`PASS`)

## 3. Memory

**Purpose:** Catch failing DIMMs, memory pressure, and slot/channel mismatches.

| OS | Native tools |
|---|---|
| Windows | `Win32_PhysicalMemory`, `Get-Counter` memory pressure counters, Reliability Monitor for memory errors |
| macOS | `system_profiler SPMemoryDataType`, `vm_stat`, memory pressure via `memory_pressure` |
| Linux | `dmidecode --type memory`, `/proc/meminfo`, `/sys/devices/system/edac/` for ECC if present |

**Findings:**

- Total capacity, slot info, speed (`INFO`)
- ECC error counts above zero (`CRIT` on uncorrectable, `WARN` on corrected)
- Single-channel install where dual-channel is supported (`INFO`)
- Sustained high memory pressure (`WARN`)
- Slot mismatch (different sizes per channel) (`INFO`)

## 4. Disk / SMART

**Purpose:** Catch dying storage before the customer's data is gone.

| OS | Native tools |
|---|---|
| Windows | `Get-PhysicalDisk`, `Get-StorageReliabilityCounter`, optional `smartctl` from smartmontools |
| macOS | `diskutil`, `ioreg`, optional `smartctl` (brew or USB-bundled) |
| Linux | `lsblk`, `smartctl`, `nvme-cli` for NVMe, `/sys/block/*/stat` |

**Findings:**

- SMART health overall status (`PASS` / `WARN` / `CRIT`)
- Reallocated sectors > 0 (`WARN`)
- Pending sectors > 0 (`CRIT`)
- High percentage used (NVMe wear indicator) (`WARN` at 80, `CRIT` at 95)
- Low free space (`WARN` at 90, `CRIT` at 97)
- Filesystem errors in `dmesg` / Event Viewer (`WARN`)

## 5. Battery

**Purpose:** Tell the customer if the battery is the problem.

> Applicable to laptops, tablets, and UPS-equipped systems. Desktops and servers without batteries return a single `INFO` finding: "No battery present."

| OS | Native tools |
|---|---|
| Windows | `powercfg /batteryreport`, `Win32_Battery`, registry battery data |
| macOS | `pmset -g batt`, `ioreg -rn AppleSmartBattery`, `system_profiler SPPowerDataType` |
| Linux | `upower -i $(upower -e | grep BAT)`, `/sys/class/power_supply/BAT*/` |

**Findings:**

- Cycle count and design vs current capacity (`INFO`, `WARN` below 80% health, `CRIT` below 60%)
- Battery reporting hardware fault (`CRIT`)
- High wear from few cycles (`WARN`)
- Recent rapid-discharge events (`WARN`)

## 6. Network

**Purpose:** Diagnose connectivity issues without changing anything.

| OS | Native tools |
|---|---|
| Windows | `Test-NetConnection`, `Get-NetAdapter`, `Get-DnsClientServerAddress`, `netsh wlan show`, `ipconfig /all` |
| macOS | `networksetup`, `ifconfig`, `system_profiler SPNetworkDataType`, `airport -I` |
| Linux | `ip addr`, `ip route`, `nmcli`, `iwconfig` / `iw dev`, `resolvectl status` |

**Findings:**

- No active interface with carrier (`CRIT`)
- DNS resolution failure (`CRIT`)
- High packet loss on first-hop probe (`WARN`)
- Weak wireless signal where wired is available (`INFO`)
- IPv6 misconfiguration where IPv4 works (`INFO`)
- Outdated wireless driver (`WARN`)

These probes touch the local network and DNS resolver only. No internet endpoints are contacted.

## 7. Security

**Purpose:** Surface obvious security holes a customer should know about.

| OS | Native tools |
|---|---|
| Windows | `Get-MpComputerStatus`, `Get-WindowsUpdateLog` (truncated), `Get-BitLockerVolume`, firewall policy via `Get-NetFirewallProfile` |
| macOS | `csrutil status`, `spctl --status`, `fdesetup status` (FileVault), `softwareupdate --history` |
| Linux | `ufw status` / `firewall-cmd --state`, `lsmod` for security modules, `aa-status` (AppArmor) / `getenforce` (SELinux), `journalctl -u sshd` |

**Findings:**

- AV disabled or definitions stale > 30 days (`CRIT`)
- Firewall disabled (`WARN`)
- Disk encryption available but not enabled (`WARN`)
- Pending OS updates older than 30 days (`WARN`)
- SIP / Gatekeeper / SELinux disabled (`WARN`)
- Stale TPM provisioning (`INFO`)

## 8. Services & Logs

**Purpose:** Read the system's recent self-reported pain.

| OS | Native tools |
|---|---|
| Windows | Event Viewer via `Get-WinEvent` (System, Application), `Get-Service`, BSOD memory.dmp metadata |
| macOS | `log show --predicate 'eventType == "fault"'`, `kextstat`, recent crash log inventory under `~/Library/Logs/DiagnosticReports/` |
| Linux | `journalctl -p err -b` (current boot errors), `systemctl --failed`, `dmesg` last entries |

**Findings:**

- Recent BSOD or kernel panic (`CRIT`)
- Repeating service crash within last 7 days (`WARN`)
- Failed systemd units (`WARN`)
- Suspicious event repetition (same source ID > 50 times / day) (`WARN`)

## 9. Boot Performance

**Purpose:** Tell the customer why their machine takes three minutes to log in.

| OS | Native tools |
|---|---|
| Windows | `Get-WinEvent -LogName 'Microsoft-Windows-Diagnostics-Performance/Operational'`, startup items via `Win32_StartupCommand` |
| macOS | `log show --start "..." --predicate 'subsystem == "com.apple.boot"'`, LaunchAgents inventory |
| Linux | `systemd-analyze blame`, `systemd-analyze critical-chain`, autostart inventory |

**Findings:**

- Total boot time > 60 seconds (`WARN`), > 120 seconds (`CRIT`)
- Single startup item exceeding 5 seconds (`WARN`)
- Heavy startup item count (more than 20 user-startup items) (`INFO`)

## 10. Software Audit

**Purpose:** Identify bloatware, unsigned binaries, and known-bad shovelware.

| OS | Native tools |
|---|---|
| Windows | `Get-AppxPackage`, `Get-WmiObject Win32_Product` (limited), registry uninstall keys, Defender allowed-threat list |
| macOS | `system_profiler SPApplicationsDataType`, `codesign --verify` on top-level apps, install receipts under `/var/db/receipts/` |
| Linux | Package manager queries (`apt list --installed`, `dnf list installed`, `pacman -Q`), unowned-file scan in `/usr/local/bin` |

**Findings:**

- Known bloatware present (`WARN`, with a published list shipped in `config.json`)
- Unsigned binary in a system path (`WARN`)
- Adware / PUP detected by Defender or equivalent (`CRIT`)
- Out-of-support application (`INFO`)

The bloatware and PUP lists are part of `config.json`'s `software_audit` section so a shop can tune them. Defaults are conservative.

## Module skip and override behavior

Every module can be:

- **Skipped** via `launcher --skip <MODULE>` or `config.json` `modules.skip`
- **Forced** via `launcher --only <MODULE>` (runs only that module)
- **Tuned** via `config.json` `thresholds.*`

A skipped module emits one `INFO` finding ("Skipped by configuration") so the report stays complete.

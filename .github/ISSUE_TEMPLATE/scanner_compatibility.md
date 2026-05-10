---
name: Scanner compatibility
about: Report a specific OS, distro, or hardware combination where iUSB fails or behaves wrong
title: "[COMPAT] "
labels: compatibility, needs-triage
assignees: ''
---

## What machine is this?

- **Manufacturer:** (e.g. Dell, HP, Apple, Lenovo, ASUS, custom build)
- **Model and revision:** (e.g. Latitude 7440, MacBook Pro 14-inch M3 Max, custom Ryzen 9 build)
- **Form factor:** Laptop / Desktop / Workstation / All-in-one / Mini PC / NUC / Tower / Server / Chromebook / Other
- **CPU / architecture:** (e.g. Intel Core i7-1365U, Apple M3 Max, AMD EPYC 7763, ARM Cortex-A78)
- **RAM:** (e.g. 16 GB DDR5)
- **Storage:** (e.g. 1 TB NVMe SSD, RAID-1 of two 8 TB HDDs)
- **Anything unusual:** (eGPU, dock, multi-monitor, hardware token, FIPS mode, etc.)

## What OS is it running?

- **OS family:** Windows / macOS / Linux / ChromeOS / Other
- **OS version:** (e.g. Windows 11 23H2, macOS 14.5 Sonoma, Ubuntu 24.04 LTS, Fedora 40, Rocky 9.4, Arch as of YYYY-MM-DD)
- **Edition:** (e.g. Windows 11 Pro, Windows Server 2022 Datacenter, RHEL 9.4 Workstation)
- **Init system:** systemd / OpenRC / runit / SysV / Other (Linux only)
- **Package manager:** apt / dnf / pacman / zypper / nix / Other (Linux only)
- **Shell:** bash / zsh / fish / dash / Other

## What did the scanner do?

Pick all that apply:

- [ ] Refused to launch
- [ ] Launched but failed early
- [ ] Ran but produced no report
- [ ] Produced a report with incorrect or missing findings
- [ ] Hung indefinitely
- [ ] Returned a non-zero exit code
- [ ] Other

Pasting the relevant terminal output is the single most helpful thing:

```
# paste terminal output here
```

## Which module(s) misbehaved?

- [ ] System ID
- [ ] CPU & Thermals
- [ ] Memory
- [ ] Disk / SMART
- [ ] Battery
- [ ] Network
- [ ] Security
- [ ] Services & Logs
- [ ] Boot Performance
- [ ] Software Audit
- [ ] Unclear / general launcher issue

## Workaround you found (if any)

If you got it working by changing something, tell us. That info is gold for the next person.

## Anything else

Quirks of this hardware, vendor-specific drivers, corporate policy that might affect behavior, etc.

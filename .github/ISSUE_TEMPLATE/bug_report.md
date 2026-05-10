---
name: Bug report
about: Something the scanner does is wrong or unexpected
title: "[BUG] "
labels: bug, needs-triage
assignees: ''
---

## Summary

One or two sentences describing what's wrong.

## What I did

The exact command (and any non-default config) that triggered the bug. If the bug happened during a normal scan, just say so.

```
# example
launcher --quick
```

## What I expected to happen

What the scanner should have done.

## What actually happened

What the scanner did instead. Paste the relevant portion of terminal output below. If a report was generated, also attach the JSON output (`launcher --json`) with any sensitive data redacted.

```
# paste relevant output here
```

## Environment

- **iUSB version:** (run `launcher --version`)
- **OS:** Windows / macOS / Linux
- **OS version:** (e.g. Windows 11 23H2, macOS 14.5, Ubuntu 24.04)
- **Form factor:** Laptop / Desktop / Workstation / All-in-one / Mini PC / NUC / Tower / Server / Chromebook
- **Architecture:** x86_64 / ARM64
- **Manufacturer and model:** (e.g. ThinkPad X1 Carbon Gen 11)

## Frequency

- [ ] Reproducible every time
- [ ] Reproducible most of the time
- [ ] Happened once, can't reproduce
- [ ] Reproducible only on a specific machine

## Additional context

Anything else that might help. Recent OS updates, weird hardware, network constraints, etc.

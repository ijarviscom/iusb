# Security Policy

## Reporting a vulnerability

Found a security issue in iUSB? Thank you for taking the time to tell us.

**Please email hello@ijarvis.ai.**

Include in the report:

- A description of the vulnerability and the impact you observed
- Reproduction steps (a script or short sequence of commands is ideal)
- The version of iUSB you tested against
- The host OS, OS version, and form factor
- Your name or handle if you'd like attribution; anonymous reports are also welcome

You can also use the canonical [security.txt](https://iusb.us/.well-known/security.txt) contact published on the website.

## Disclosure timeline

We aim to:

- Acknowledge the report within **one business day**
- Confirm whether the issue is reproduced within **five business days**
- Ship a fix or a clear mitigation within **30 days** for critical issues, **60 days** for high severity, **90 days** for medium and below

We coordinate public disclosure with the reporter. We will not publish a CVE or write-up before the fix has shipped to early-access shops, except where there is active exploitation in the wild.

## Scope

The following are in scope for this disclosure policy:

- The iUSB scanner code (Windows, macOS, Linux variants)
- The HTML report generator
- The launcher and dispatch logic
- The default `config.json` schema and parsing
- Anything shipped under this repository or distributed in the signed iUSB bundle

Out of scope:

- The marketing website at iusb.us (separate scope, but feel free to report and we'll route appropriately)
- Third-party tools the scanner shells out to (report those to their maintainers; we'll coordinate)
- Customer machines and their data (iUSB never accesses these directly, but if a finding affects how iUSB handles customer-facing artifacts, that is in scope)

## What we consider a security issue

In scope:

- Any path where iUSB writes to a customer machine when it should be read-only
- Any path where iUSB transmits data over a network during a Community-edition scan
- Any path where iUSB elevates without prompting the technician
- Any injection vulnerability in the report generator (HTML, shell, PowerShell, JSON parsing)
- Any way to make iUSB execute attacker-controlled code via crafted USB contents or config
- Any way to exfiltrate technician credentials or customer machine data via the scanner
- Any way to tamper with a report after it has been generated and before the technician reads it

Out of scope (not security issues, but file as bugs):

- Performance regressions
- False positives or false negatives in module findings
- UI issues in the HTML report
- Compatibility failures on specific distros or hardware combinations (file as a [scanner compatibility report](.github/ISSUE_TEMPLATE/scanner_compatibility.md))

## Safe-harbor commitment

If you are researching a vulnerability in good faith, we will not pursue legal action against you for:

- Accessing iUSB code or running iUSB against test machines you own
- Reverse-engineering the scanner or report logic
- Publishing your findings after the disclosure timeline above

We ask only that you:

- Do not run iUSB against machines you do not own or have explicit permission to test
- Do not exfiltrate customer data from a third-party shop's environment for any reason
- Do not publish a working exploit before the fix has shipped to early-access shops

## PGP key

A PGP key for encrypted vulnerability reports is not yet published. If you need encryption, email us at hello@ijarvis.ai and we'll set up a one-time encrypted channel (Signal, Keybase, or a Tutanota relay).

## Hall of fame

Researchers who report a confirmed security issue and follow this policy are credited (with permission) in the release notes of the version that ships the fix.

---

Thanks again for helping us keep iUSB safe for the shops and customers that depend on it.

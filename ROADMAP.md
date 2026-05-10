# iUSB Roadmap

This is the public roadmap for iUSB. Dates are targets, not commitments. Order within a milestone is approximate.

## Now (private beta, May 2026)

- Hardening edge cases on the v1.0-beta scanner with selected early-access shops
- Tightening BitLocker, ARM Mac, and Windows Server compatibility
- Stabilizing the `config.json` schema before public release
- Building out the public-launch website, GitHub presence, and documentation

## v1.0 (Q3 2026 target)

The first public release. Source code lands in `src/` under MIT license.

- [ ] Public source release on GitHub
- [ ] Signed download bundles for Windows, macOS, and Linux
- [ ] `iusb.us/downloads` with checksums and signing keys
- [ ] Full documentation set (currently in `docs/`)
- [ ] Public bug tracker and feature request board
- [ ] Localization scaffolding (English only at v1.0)

## v1.1 (Q4 2026 target)

Additional modules and quality-of-life improvements.

- [ ] Printer queue diagnostics (jam state, driver age, queue depth)
- [ ] USB device enumeration (recognize peripherals, flag known-bad cables)
- [ ] Bluetooth audit (paired devices, driver versions, common pairing failures)
- [ ] Display module (DPI mismatch, ICC profile sanity, refresh-rate anomalies)
- [ ] Audio module (default device verification, latency probe, codec detection)
- [ ] Network adapter deep probe (TCP stack tuning, IPv6 sanity, common ISP issues)

## v1.5 (Q1 2027 target)

Localization and report customization.

- [ ] Spanish, French, Brazilian Portuguese report localization
- [ ] German, Dutch, Polish localization
- [ ] Right-to-left language support (Arabic, Hebrew)
- [ ] Custom report templates beyond `config.json` overrides
- [ ] Customer-facing report in plain text and CSV in addition to HTML

## Pro tier (Q3 2026 target, parallel to v1.0)

Subscription tier for shops with active fleet management needs.

- [ ] AI-powered finding triage via the Anthropic Claude API
- [ ] Cloud dashboard for scan history across machines
- [ ] Multi-tech fleet view with assignment and follow-up
- [ ] Automated fix-script library, reviewed and approved per shop
- [ ] Custom report templates with shop-specific branding
- [ ] White-label option for resellers
- [ ] Priority support with named contact

## Enterprise tier (Q4 2026 target)

For organizations running iUSB across 100 or more seats.

- [ ] SSO / SAML / SCIM provisioning
- [ ] Audit logs and compliance reporting (SOC 2 aligned)
- [ ] On-premises deployment option
- [ ] Air-gapped environment support
- [ ] Custom integrations with PSA, RMM, and ticketing systems (ConnectWise, Atera, HaloPSA, Jira, etc.)
- [ ] Dedicated SLA with named support engineer
- [ ] Procurement and MSA paperwork ready
- [ ] Custom training and onboarding

## Longer term (2027 and beyond)

Directional, not committed.

- Hardware-specific modules: server chassis sensors (iDRAC, iLO, IPMI), workstation-grade thermal benchmarks, specialized GPU diagnostics
- ChromeOS native scanner (currently routes through Linux mode)
- iOS and Android scanner companions for mobile-device repair shops
- Offline AI triage option (local LLM) for shops that prefer no cloud dependency
- Community-contributed module marketplace

## How to propose roadmap items

Open a [feature request](.github/ISSUE_TEMPLATE/feature_request.md) describing the shop workflow problem first and the proposed feature second. Roadmap inclusion is decided based on number of independent requests, shop-impact estimate, and engineering cost.

Enterprise customers can request roadmap items directly via hello@ijarvis.ai.

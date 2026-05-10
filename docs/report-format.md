# Report format

The iUSB report is a single self-contained HTML file. Every style, every image, every piece of data is inlined. The technician hands one file to the customer. The customer opens it in any browser, prints it, saves it as PDF, or emails it forward.

The full example renders at [`examples/report-example.html`](../examples/report-example.html).

## Structure

```
┌────────────────────────────────────────────────┐
│                                                │
│  HEADER                                        │
│  ├── Shop logo (left)                          │
│  ├── "Diagnostic Report" (center)              │
│  ├── Customer name, prepared-for date (right)  │
│  └── Machine: manufacturer model               │
│                                                │
├────────────────────────────────────────────────┤
│                                                │
│  FINDINGS  (sorted: CRIT, WARN, INFO, PASS)    │
│                                                │
│  ┌──┬─────────────────────┬──────┬──────┐      │
│  │  │ Finding             │ Time │ Cost │      │
│  ├──┼─────────────────────┼──────┼──────┤      │
│  🔴│ SSD failing         │ 45m  │ $89  │      │
│  🟡│ Battery at 62%      │ 30m  │ $69  │      │
│  🟡│ Out-of-date OS      │ 30m  │ $59  │      │
│  🔵│ Cosmetic updates    │ 15m  │ $29  │      │
│  🟢│ Disk integrity OK   │  -   │  -   │      │
│  └──┴─────────────────────┴──────┴──────┘      │
│                                                │
├────────────────────────────────────────────────┤
│                                                │
│  SUMMARY                                       │
│  Est. Repair Time: ~95 min                     │
│  Estimated Total: $246                         │
│                                                │
├────────────────────────────────────────────────┤
│                                                │
│  FOOTER                                        │
│  Shop name, phone, email                       │
│  Disclaimer line                               │
│  Powered by iUSB                               │
│                                                │
└────────────────────────────────────────────────┘
```

## Severity badge palette

The default palette is tuned for legibility on both screen and print:

| Severity | Hex | Meaning |
|---|---|---|
| `CRIT` (red) | `#dc2626` | Stops the customer from using the machine, or imminent failure |
| `WARN` (amber) | `#d97706` | Degraded performance, security risk, or aging hardware |
| `INFO` (blue) | `#2563eb` | Worth knowing, not urgent |
| `PASS` (green) | `#16a34a` | Module ran, no issue found |

Each badge has a background tint (very light) and a 3-pixel left border in the severity color, so even readers with red-green color blindness can distinguish by position and shape.

Override the palette in `config.json` under `report.severity_palette`.

## Print and PDF behavior

The report is built to be printed at 8.5×11 or A4 without modification:

- Page-break-inside-avoid rules on each finding row
- Margins tuned for default browser print
- Severity badges use both color and shape (background tint + left border + bold severity word) so monochrome printing still works
- Header repeats on each page (CSS `@page` and table-header strategies)
- Footer with shop contact info appears on the last page only

To save as PDF, open the report in any browser and use "Save as PDF" from the print dialog.

## Branding

The minimum branding is the shop name (required). The recommended branding is:

- Shop name
- Shop logo (PNG or SVG, displayed at 40 pixels tall in the header)
- Phone number and email in the footer
- A short tagline or address line in the footer

All branding is set in `config.json` under the `shop` key. See [`docs/config.md`](config.md#shop).

## Localization

v1.0 ships English-only. Localization is on the v1.5 roadmap. The HTML report uses semantic structure that translation tools (and human translators) can work with without breaking the layout.

When localization lands, the template will support a `lang` field in `config.json` and the report will set `<html lang="...">` accordingly. RTL languages (Arabic, Hebrew) will get appropriate directional styling.

## Embedded data sidecar

Every HTML report includes a `<script type="application/json" id="iusb-data">...</script>` block containing the raw JSON output. PSA / RMM / ticketing integrations can read this directly without re-parsing the HTML.

The JSON conforms to [`examples/scan-output.example.json`](../examples/scan-output.example.json)'s schema.

## What is NOT in the report

The report deliberately omits:

- Customer personal information beyond what the technician entered (no auto-detected email, no usernames)
- File path listings under user home directories
- Browser history, saved passwords, recent documents
- Any data the scanner read for diagnostic purposes but doesn't need to communicate (e.g. the scanner reads the running process list to evaluate boot performance, but the report shows boot time only, not the process list)
- Network identifiers like local IP, MAC address, hostname (unless `report.include_machine_serial` is also true and the shop wants full inventory data)

When in doubt, the report errs on the side of less information rather than more. The customer is the audience.

## Print regression testing

Each new release runs the example report through:

- Chrome print preview at Letter and A4
- Firefox print preview at Letter and A4
- Safari print preview at Letter
- Edge print preview at Letter

A check fails if the layout changes meaningfully or any finding row breaks across pages.

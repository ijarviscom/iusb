# Configuration reference: `config.json`

iUSB reads a single `config.json` file at the root of the USB stick. This document is the canonical reference for every field.

A starter file with sane defaults lives at [`examples/config.example.json`](../examples/config.example.json).

## Top-level shape

```json
{
  "$schema": "https://iusb.us/schema/config-v1.json",
  "shop": { ... },
  "report": { ... },
  "thresholds": { ... },
  "modules": { ... },
  "software_audit": { ... }
}
```

The launcher validates `config.json` against the embedded schema at startup. A validation failure prints a clear error and exits before any module runs.

## `shop`

Identifies the technician's shop. Appears on every report.

| Field | Type | Default | Notes |
|---|---|---|---|
| `shop.name` | string | `"My Repair Shop"` | Required. Shown in the report header. |
| `shop.logo_path` | string or null | null | Path to a logo image, relative to the USB root. PNG or SVG recommended. |
| `shop.website` | string | `""` | Optional shop website. Appears in the report footer if set. |
| `shop.phone` | string | `""` | Optional. Appears in the report footer. |
| `shop.email` | string | `""` | Optional. Appears in the report footer. |
| `shop.address` | string | `""` | Optional. Single-line address for the report footer. |
| `shop.labor_rate_per_hour_usd` | number | 95 | Used for the report's cost column. |
| `shop.minimum_charge_usd` | number | 30 | Floor for any non-zero repair line item. |
| `shop.currency` | string | `"USD"` | ISO 4217 code. The report renders the symbol accordingly. |

## `report`

Controls the customer-facing HTML report.

| Field | Type | Default | Notes |
|---|---|---|---|
| `report.show_pass_findings` | boolean | true | When false, PASS findings are summarized in the footer only. |
| `report.show_cost_column` | boolean | true | Hide the cost column if you prefer to quote in conversation. |
| `report.show_time_estimates` | boolean | true | Hide the time-estimate column. |
| `report.include_machine_serial` | boolean | true | If false, only manufacturer and model appear in the report header. |
| `report.severity_palette` | object | (see below) | Override the four severity colors. |
| `report.fonts` | object | (see below) | Override font stacks. |
| `report.custom_footer_html` | string | `""` | Optional HTML appended to the report footer. Sanitized to a safe subset. |

### Default severity palette

```json
{
  "crit": "#dc2626",
  "warn": "#d97706",
  "info": "#2563eb",
  "pass": "#16a34a"
}
```

### Default fonts

```json
{
  "body": "system-ui, -apple-system, 'Segoe UI', sans-serif",
  "mono": "'IBM Plex Mono', 'Courier New', monospace"
}
```

## `thresholds`

Tunable per-finding severity boundaries. Defaults are conservative.

| Field | Type | Default | Affects |
|---|---|---|---|
| `thresholds.battery_health_warn_pct` | number | 80 | Battery module: WARN below this % health |
| `thresholds.battery_health_crit_pct` | number | 60 | Battery module: CRIT below this % health |
| `thresholds.smart_pending_sectors_warn` | number | 1 | Disk module: WARN at this many pending sectors |
| `thresholds.smart_reallocated_warn` | number | 10 | Disk module: WARN at this many reallocated sectors |
| `thresholds.nvme_percent_used_warn` | number | 80 | Disk module: WARN at NVMe wear indicator |
| `thresholds.nvme_percent_used_crit` | number | 95 | Disk module: CRIT at NVMe wear indicator |
| `thresholds.disk_free_warn_pct` | number | 10 | Disk module: WARN below this % free |
| `thresholds.disk_free_crit_pct` | number | 3 | Disk module: CRIT below this % free |
| `thresholds.boot_time_warn_seconds` | number | 60 | Boot module: WARN above this many seconds |
| `thresholds.boot_time_crit_seconds` | number | 120 | Boot module: CRIT above this many seconds |
| `thresholds.cpu_temp_warn_c` | number | 85 | CPU module: WARN above this °C sustained |
| `thresholds.cpu_temp_crit_c` | number | 95 | CPU module: CRIT above this °C sustained |
| `thresholds.updates_stale_days` | number | 30 | Security module: WARN if updates older than N days |
| `thresholds.av_stale_days` | number | 30 | Security module: CRIT if AV defs older than N days |

## `modules`

Controls which modules run.

```json
{
  "modules": {
    "skip": [],
    "only": [],
    "battery_required": false,
    "elevation": {
      "prompt_once": true,
      "skip_elevated_only": false
    }
  }
}
```

| Field | Type | Default | Notes |
|---|---|---|---|
| `modules.skip` | string array | `[]` | Module names to skip. See [modules.md](modules.md) for canonical names. |
| `modules.only` | string array | `[]` | If non-empty, only these modules run. Overrides `skip`. |
| `modules.battery_required` | boolean | false | When true, Battery module marks "no battery present" as `WARN` instead of `INFO`. Useful for laptop-only shops. |
| `modules.elevation.prompt_once` | boolean | true | Prompt once for elevation up front; modules that need it use the cached privilege. |
| `modules.elevation.skip_elevated_only` | boolean | false | When true, modules requiring elevation are skipped without prompting. |

## `software_audit`

Tunes the bloatware and PUP lists for module 10.

```json
{
  "software_audit": {
    "bloatware_warn": [
      "McAfee LiveSafe",
      "Norton 360",
      "Dropbox Promo",
      "Candy Crush Saga"
    ],
    "unsigned_binary_paths_warn": [
      "/usr/local/bin",
      "C:\\Program Files"
    ],
    "ignore_publishers": [
      "Microsoft Corporation",
      "Apple Inc.",
      "Canonical Ltd."
    ]
  }
}
```

The defaults shipped with iUSB are a starting point. Update them to match your shop's experience.

## Schema validation

The launcher loads `config.json`, validates against the embedded schema, and fails fast on errors. The version of the schema in use is reported in the JSON sidecar output under `meta.config_schema_version`.

Backward compatibility: minor version bumps add fields with defaults. Major version bumps may rename or restructure; the launcher prints migration guidance when it detects an older schema.

## Example

See [`examples/config.example.json`](../examples/config.example.json) for a complete, valid file with comments describing each section.

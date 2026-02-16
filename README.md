# OpenTelemetry Collector (contrib) → S3 Templates (Windows + Linux)

This repo contains copy/paste-ready OpenTelemetry Collector (contrib) configuration examples for collecting logs from:

- Windows Event Logs
- Syslog (single UDP/514 listener)
- Flat files (filelog receiver)

…and exporting each log type to various locations.

## Design Pattern: "Tag → Filter → Pipeline → Bucket"

For syslog fan-out (Cisco vs pfSense vs ESXi vs Other) we use:
1) **syslog receiver** on UDP/514
2) **transform processor** to tag logs with `resource.attributes["log.source"]` based on `attributes["net.peer.ip"]`
3) **filter processors (OTTL)** to keep only the desired log types in each pipeline
4) **one pipeline per destination bucket**, each with its own `awss3/*` exporter

## Configs

### Windows
- `configs/windows/01-windows-eventlog-to-s3.yaml`
- `configs/windows/02-windows-eventlog-and-filelog-to-s3.yaml`
- `configs/windows/03-windows-eventlog-syslog-filelog-to-s3.yaml`

### Linux
- `configs/linux/04-linux-syslog-to-s3-multi-bucket.yaml`
- `configs/linux/05-linux-syslog-and-filelog-to-s3.yaml`
- `configs/linux/06-linux-filelog-only-to-s3-multi-bucket.yaml`

## AWS Authentication

The `awss3` exporter uses the standard AWS credential chain (env vars, shared config/profile, instance/host role).
See: `docs/aws-auth.md`.

## Getting Started (quick)

1) Install `otelcol-contrib` on Windows or Linux
2) Copy a template config and update:
   - `s3_bucket`
   - `region`
   - `s3_prefix`
   - syslog sender IPs (for syslog routing)
   - file paths (for filelog)
3) Start collector

## Notes
- Syslog routing is done by matching sender IP (`net.peer.ip`) and setting `log.source`.
- Filter statements are written to drop logs that do NOT match the pipeline’s `log.source`.

## License


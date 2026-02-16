# Routing Model: Tag → Filter → Pipeline → Bucket

## Why this exists
OpenTelemetry Collector doesn't provide a "route to different S3 buckets by field" feature.
So we route by running multiple pipelines, and ensuring each pipeline only receives the logs meant for its bucket.

## Syslog fan-out (single UDP/514)
All syslog messages arrive on the same receiver:
- receiver: `syslog` (udp/514)

We then tag each message based on its sender IP:
- `attributes["net.peer.ip"]` → `resource.attributes["log.source"]`

Example:
- 192.168.2.1 → `syslog.pfsense`
- 192.168.2.10 → `syslog.cisco`

Then we filter per pipeline.
Important: the filter processor DROPS logs that match the expression.

So in the pfSense pipeline we drop everything that is NOT pfSense:
- drop if `log.source != syslog.pfsense`

What remains is only pfSense logs, and they are exported to the pfSense S3 bucket.

# OTEL Ingest

OTEL ingest allows external telemetry pipelines to feed engineering activity signals into Leanmote.

This is useful when teams need to complement standard integrations with custom observability or tooling events.

## Common scenarios

- Ingesting internal workflow events not covered by native integrations
- Sending AI coding telemetry summaries for analytics
- Consolidating event streams from custom developer platforms

## Implementation guidance

- Use a stable schema strategy for emitted telemetry.
- Ensure event attributes support team and workflow segmentation.
- Validate ingestion quality before relying on downstream dashboards.

## Related docs

- [Metrics API](metrics-api.md)
- [Architecture Overview](../architecture/overview.md)

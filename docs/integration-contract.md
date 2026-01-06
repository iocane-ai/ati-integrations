# docs/integration-contract.md
# Integration Contract (Instrumentors)

All integrations MUST implement the same public surface.

## Package naming
- `ati-sdk` (shared)
- `ati-integrations-<framework>` (per framework)

## Public API
Each integration exports:
- `<Framework>Instrumentor`
  - `instrument(config: AtiConfig | None = None) -> None`
  - `uninstrument() -> None`

## Idempotency
- Calling `instrument()` multiple times must not double-wrap hooks.
- `uninstrument()` must restore original handlers if patched.

## Configuration precedence
1) explicit `config` argument
2) env vars
3) framework defaults

## Required env vars (common)
- `OTEL_EXPORTER_OTLP_ENDPOINT`
- `OTEL_SERVICE_NAME` (or `service.name`)
Optional:
- `ATI_CAPTURE_PAYLOADS=0|1` (default 0)
- `ATI_CAPTURE_PROMPTS=0|1` (default 0)
- `ATI_REDACTION_PATTERNS` (comma-separated)

## Overhead constraints
- No heavy processing in callbacks; emit spans/events only.
- Use sampling if configured by OTEL SDK; do not implement custom sampling in integrations.

## Testing requirements
- Minimal unit test that:
  - calls `instrument()`
  - runs a tiny workflow
  - asserts spans include required attributes (`ati.trace.schema_version`, `ati.framework`, `ati.agent.id`)

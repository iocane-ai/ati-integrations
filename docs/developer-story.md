# docs/developer-story.md
# Developer Story (Integrations v0.1)

## Primary persona
AI platform / infra engineer operating agent workflows in production.

## Job-to-be-done
Enable agent-aware tracing in <framework> in under 5 minutes so I can:
- see where latency came from (coordination vs model vs tool)
- identify fan-out/retry patterns
- get actionable fixes (reduce concurrency, batch, rate-limit, restructure planner)

## Setup path (happy path)
1) Install
- `pip install ati-sdk ati-integrations-<framework> opentelemetry-sdk opentelemetry-exporter-otlp`

2) Configure exporter to ATI Collector
- `OTEL_EXPORTER_OTLP_ENDPOINT=http://<ati-collector>:4318`
- `OTEL_EXPORTER_OTLP_PROTOCOL=http/protobuf`
- `OTEL_SERVICE_NAME=<your-service>`

3) Enable
- In code: `Instrumentor().instrument()`
  - OR env-only mode where feasible: `ATI_INSTRUMENTATION=1`

4) Run existing app unchanged

5) Validate
- ATI dashboard shows trace → agent DAG → incidents/explanations

## Success criteria
- First trace visible in ATI within 5 minutes
- One-line enablement (or env-only)
- No prompts/payloads collected by default
- Spans carry enough semantics for ATI detectors (fan-out, blocking chains, retries)

## Anti-goals
- No scheduling/enforcement
- No proprietary tracing backend required
- No “rewrite your app” requirements

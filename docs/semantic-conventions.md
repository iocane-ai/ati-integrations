# ATI Semantic Conventions (v0.1)

This document defines OpenTelemetry attributes and span naming used by ATI integrations to produce agent-aware traces.

## Goals
- Framework-agnostic: same concepts across LangChain/CrewAI/AutoGen/LlamaIndex/AutoGPT
- Sufficient to reconstruct: agent DAG, fan-out, retries, blocking chains, tool-call bursts
- Privacy-safe by default: no prompts/payloads unless explicitly enabled

## Versioning
- `ati.trace.schema_version`: semantic conventions version, e.g. `"0.1"`
- Changes:
  - Patch: additive attributes
  - Minor: additive span types or optional fields
  - Major: breaking renames/removals

## Required resource attributes
Set by OTEL SDK / app config:
- `service.name` (required)
- `telemetry.sdk.language=python` (recommended)
- `service.version` (recommended)

## Required span attributes (all ATI spans)
- `ati.trace.schema_version` = `"0.1"`
- `ati.framework` = one of: `langchain | crewai | autogen | llamaindex | autogpt`
- `ati.span.type` = one of:
  - `agent` (agent loop / agent execution unit)
  - `step` (logical step within an agent)
  - `tool` (tool/function call)
  - `llm` (model invocation)
  - `io` (retrieval/memory/cache/network IO stage)
  - `orchestration` (planner/dispatcher/scheduler action)

## Identity attributes
### Agent identity
- `ati.agent.id` (required): stable within trace (string)
- `ati.agent.name` (optional): human-friendly label
- `ati.agent.role` (optional): `planner|worker|critic|router|executor|memory` etc.

### Step identity
- `ati.step.id` (recommended): stable within trace
- `ati.step.name` (optional)
- `ati.step.type` (recommended): `planner|worker|tool|retriever|synthesizer|router|memory|executor`
- `ati.parent_step.id` (optional): enables explicit DAG edges when context propagation is imperfect
- `ati.loop.iteration` (optional, int): useful for AutoGPT-like loops

## Tool attributes (when ati.span.type = tool)
- `ati.tool.name` (recommended)
- `ati.tool.provider` (optional): `openai|anthropic|serpapi|custom` etc.
- `ati.tool.kind` (optional): `http|db|function|filesystem|queue|browser`
- `ati.tool.target` (optional): hostname/service/db name (avoid secrets)

## LLM attributes (when ati.span.type = llm)
- `ati.llm.provider` (optional): `openai|anthropic|azure_openai|local`
- `ati.llm.model` (optional)
- `ati.tokens.in` (optional, int)
- `ati.tokens.out` (optional, int)
- `ati.cache.hit` (optional, bool)

## Coordination / contention attributes
### Fan-out
- `ati.fanout.size` (optional, int): number of child operations spawned from this span
- `ati.concurrency.limit` (optional, int): if known (framework config)
- `ati.queue.depth` (optional, int): if known (executor queue)

### Retries
- `ati.retry.count` (optional, int): retries performed within this span
- `ati.retry.reason` (optional): `timeout|rate_limit|tool_error|network|unknown`

### Waiting / blocking
- `ati.wait.on` (optional): identifier of dependency (agent/step/tool)
- `ati.wait.kind` (optional): `lock|queue|dependency|rate_limit|network|tool`

## Error mapping
Use standard OTEL error fields:
- Span status: `ERROR`
- `exception.type`, `exception.message`, `exception.stacktrace` (when available)
Additionally (optional):
- `ati.error.class` (string): normalized ATI category, e.g. `rate_limit`, `tool_timeout`

## Span naming
Format: `<framework>.<component>.<action>`
Examples:
- `langchain.agent.step`
- `langchain.tool.call`
- `crewai.task.execute`
- `autogen.agent.message`
- `llamaindex.query.execute`
- `autogpt.action.execute`

## Minimal span set (MVP bar)
A trace is considered “ATI-usable” if it contains:
- At least 1 `ati.span.type=agent`
- At least 1 of (`tool`, `llm`, `io`) spans nested within agent/step spans
- Agent identity present (`ati.agent.id`)
- Step or action delineation (`ati.step.type` or span name semantics)

## Privacy defaults
- Do not record prompt, tool arguments, retrieved documents by default.
- If enabled, store under:
  - `ati.payload.enabled=true`
  - `ati.payload.redacted=true|false`
  - `ati.payload.kind=prompt|tool_args|retrieved_text`
  - Actual payload should be attached as OTEL event field (not span attribute) to avoid cardinality explosion.

## Example: tool call span (attributes)
- `ati.trace.schema_version="0.1"`
- `ati.framework="langchain"`
- `ati.span.type="tool"`
- `ati.agent.id="planner_v2"`
- `ati.step.type="tool"`
- `ati.tool.name="search"`
- `ati.fanout.size=10` (if this call spawned parallel searches)
- `ati.retry.count=0`

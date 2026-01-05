# ATI Integrations

OpenTelemetry-based instrumentation SDK and framework integrations for **IOcane ATI**.

These integrations capture agent execution flows (LLM calls, tool usage, steps, orchestration) and emit them as standard OTLP traces with standard semantic attributes.

## Packages

| Package | Framework | Status | Description |
|---------|-----------|--------|-------------|
| `ati-sdk` | - | Stable | Shared config & semantic conventions |
| `ati-integrations-langchain` | LangChain | Stable | Callback-based instrumentation |
| `ati-integrations-crewai` | CrewAI | Stable | Hook-based instrumentation for Crew/Agent |
| `ati-integrations-autogen` | AutoGen | Stable | Middleware for Chat Agents |
| `ati-integrations-llamaindex` | LlamaIndex | Stable | Callback handler for RAG/Agents |
| `ati-integrations-autogpt` | AutoGPT | Beta | Agent loop instrumentation |

## Getting Started

1.  **Install SDK & Integration:**
    ```bash
    pip install ati-sdk
    pip install ati-integrations-langchain  # or crewai, autogen, etc.
    ```

2.  **Configure Exporter (Standard OTEL):**
    ```bash
    export OTEL_SERVICE_NAME=my-agent-service
    export OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318
    ```

3.  **Instrument Code:**
    ```python
    from ati_langchain import LangChainInstrumentor
    LangChainInstrumentor().instrument()
    ```

## Documentation

- [Semantic Conventions](docs/semantic-conventions.md)
- [Developer Story](docs/developer-story.md)
- [Integration Contract](docs/integration-contract.md)

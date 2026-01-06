# ATI Integrations

OpenTelemetry-based instrumentation SDK and framework integrations for **IOcane ATI**.

These integrations capture agent execution flows (LLM calls, tool usage, steps, orchestration) and emit them as standard OTLP traces with standard semantic attributes.

## Packages

| Package | Framework | Status | Description |
|---------|-----------|--------|-------------|
| [`ati-sdk`](sdk/) | - | Stable | Shared config & semantic conventions |
| [`ati-integrations-langchain`](integrations/langchain/) | LangChain | Stable | Callback-based instrumentation |
| [`ati-integrations-crewai`](integrations/crewai/) | CrewAI | Stable | Hook-based instrumentation for Crew/Agent |
| [`ati-integrations-autogen`](integrations/autogen/) | AutoGen | Stable | Middleware for Chat Agents |
| [`ati-integrations-llamaindex`](integrations/llamaindex/) | LlamaIndex | Stable | Callback handler for RAG/Agents |
| [`ati-integrations-autogpt`](integrations/autogpt/) | AutoGPT | Stable | Agent loop instrumentation |

## Getting Started

1.  **Install SDK & Integration:**
    ```bash
    pip install ati-sdk
    pip install ati-integrations-langchain  # or crewai, autogen, etc.
    ```

2.  **Configure Exporter (Standard OTEL):**
    
    You need your Environment ID and API Key. Run `iocane connect` to generate them in `.env.iocane`.

    ```bash
    # 1. Service Name (Your Agent Name)
    export OTEL_SERVICE_NAME=my-agent-service
    
    # 2. Endpoint
    export OTEL_EXPORTER_OTLP_ENDPOINT=https://api.iocane.ai/v1/traces
    
    # 3. Auth & Environment Headers
    # Replace with values from .env.iocane or Dashboard
    export OTEL_EXPORTER_OTLP_HEADERS="x-iocane-key=<YOUR_API_KEY>,x-ati-env=<YOUR_ENV_ID>"
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

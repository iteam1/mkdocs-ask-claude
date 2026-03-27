# Architecture

This page describes how the internal modules of `mkdocs-claude-chat` collaborate.

## Module Collaboration

```mermaid
graph TD
    config["config.py\nDefines _PluginConfig\nmodel · position · llmstxt_url\nsystem_prompt · chat_title"]
    plugin["plugin.py\nMkDocs plugin entry point\non_config · on_post_build\non_page_context"]
    assets["assets.py\nInjects chat.js and chat.css\ninto the built site"]
    server["server.py\nChat backend\nClaudeSDKClient session\nstreams answers"]
    logger["logger.py\nPlugin-namespaced\nlogging utilities"]

    config -->|imported by| plugin
    plugin -->|build time: inject CSS/JS| assets
    plugin -->|runtime: delegate question| server
    server -->|passes llmstxt_url hint| Claude["Claude\nbuilt-in tools\nWebFetch · curl · WebSearch"]
    Claude -->|checks first| llmstxt["/llms.txt (optional)"]
    Claude -->|fallback| web["WebFetch / WebSearch"]

    logger -.->|used by| plugin
    logger -.->|used by| server
    logger -.->|used by| assets
```

## Build-time Flow

When `mkdocs build` runs:

```mermaid
sequenceDiagram
    participant MkDocs
    participant plugin.py
    participant config.py
    participant assets.py

    MkDocs->>plugin.py: on_config()
    plugin.py->>config.py: read _PluginConfig (model, position, …)
    plugin.py->>assets.py: inject chat.js + chat.css into site

    MkDocs->>plugin.py: on_page_context()
    plugin.py->>MkDocs: write window.__CLAUDE_CHAT_CONFIG__ into page

    MkDocs->>plugin.py: on_post_build()
    plugin.py->>assets.py: copy bundled assets to site/
```

## Chat Runtime Flow

When a visitor asks a question in the widget:

```mermaid
sequenceDiagram
    participant Browser
    participant server.py
    participant Claude
    participant Web

    Browser->>server.py: user question
    server.py->>Claude: ClaudeSDKClient session\n+ llmstxt_url hint in system prompt
    Claude->>Web: curl/WebFetch <url>/llms.txt
    Web-->>Claude: doc index (if exists)
    Claude->>Web: fetch relevant pages
    Web-->>Claude: page content
    Claude-->>server.py: answer
    server.py-->>Browser: streamed response
```

## Module Dependencies

```plantuml
@startuml
skinparam componentStyle rectangle

component "plugin.py" as plugin
component "config.py" as config
component "assets.py" as assets
component "server.py" as server
component "logger.py" as logger

plugin --> config : reads
plugin --> assets : injects at build time
plugin --> server : starts on serve
logger <-- plugin : used by
logger <-- server : used by
logger <-- assets : used by

note right of server
  FastAPI + uvicorn sidecar
  POST /chat → SSE stream
end note
@enduml
```

## Implementation Status

| Module | Status | Notes |
|---|---|---|
| `config.py` | Done | Full config schema |
| `plugin.py` | Done | on_config, on_post_build, on_page_context, on_post_page, on_startup |
| `assets.py` | Done | CSS/JS injection + copy |
| `server.py` | Done | FastAPI chat backend, SSE streaming |
| `logger.py` | Done | Plugin-namespaced logging adapter |

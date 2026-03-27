# mkdocs-claude-chat

MkDocs plugin to add a Claude-powered chatbot to your documentation site.

The chatbot uses the [`claude-agent-sdk`](https://github.com/anthropics/claude-agent-sdk-python)
to answer questions about your documentation. Claude fetches content autonomously — checking
[`/llms.txt`](https://llmstxt.org/) first if available, then falling back to WebFetch or WebSearch.

## Installation

```bash
pip install mkdocs-claude-chat
```

## Usage

```yaml
# mkdocs.yml
plugins:
  - claude-chat:
      model: claude-sonnet-4-6
      chat_title: "Ask Claude"
      position: bottom-right
```

Optionally add [`mkdocs-llmstxt`](https://github.com/pawamoy/mkdocs-llmstxt) to generate a `/llms.txt`
index — Claude will use it automatically for faster, more accurate answers.

## Requirements

- The `claude` CLI must be installed and authenticated in the deployment environment.

## Configuration

| Option | Type | Default | Description |
|---|---|---|---|
| `enabled` | bool | `true` | Disable the plugin entirely |
| `model` | str | `claude-opus-4-5` | Claude model for responses |
| `system_prompt` | str | *(built-in)* | Custom system prompt |
| `llmstxt_url` | str | *(auto from site_url)* | Hint to Claude: URL of the `/llms.txt` index |
| `chat_title` | str | `Ask Claude` | Widget button label |
| `position` | str | `bottom-right` | Widget position (`bottom-right` or `bottom-left`) |

## Related Projects

### MkDocs ecosystem

- [**MkDocs**](https://www.mkdocs.org/) — Static site generator for project documentation ([GitHub](https://github.com/mkdocs/mkdocs))
- [**MkDocs Catalog**](https://github.com/mkdocs/catalog) — Community catalog of MkDocs plugins and themes
- [**mkdocstrings**](https://github.com/mkdocstrings/mkdocstrings) — Automatic API documentation generation from docstrings
- [**mkdocs-llmstxt**](https://github.com/pawamoy/mkdocs-llmstxt) — Generates `/llms.txt` index files for AI consumption (required by this plugin)
- [**mkdocs-obsidian-interactive-graph-plugin**](https://daxcore.github.io/mkdocs-obsidian-interactive-graph-plugin/) — Interactive graph visualization for MkDocs

### AI / Claude

- [**claude-agent-sdk-python**](https://github.com/anthropics/claude-agent-sdk-python) — Official Python SDK for Claude agentic workflows (used by this plugin)

### Templating

- [**Jinja2**](https://jinja.palletsprojects.com/en/stable/) — Templating engine used internally by MkDocs
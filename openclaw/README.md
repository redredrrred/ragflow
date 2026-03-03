# RAGFlow Knowledge Base Plugin for OpenClaw

Connect OpenClaw to [RAGFlow](https://ragflow.io) knowledge bases for intelligent document retrieval and Q&A.

## Features

- 🔍 **Search Knowledge Bases** - Query your RAGFlow knowledge bases directly from OpenClaw
- 🤖 **Auto-Context Injection** - Automatically injects relevant knowledge into AI conversations
- 📚 **Multiple Knowledge Bases** - Support for searching across multiple datasets
- 🎯 **Smart Retrieval** - Hybrid search with vector similarity + keyword matching
- 🛠️ **CLI Tools** - Command-line interface for quick searches
- 📊 **Source Citations** - See which documents the information came from

## Installation

The plugin is included in OpenClaw extensions. To enable it:

### 1. Get RAGFlow API Credentials

1. Go to your RAGFlow console
2. Navigate to **Profile → API**
3. Copy your API Key
4. Note your RAGFlow server URL (e.g., `http://localhost:80`)

### 2. Configure the Plugin

Add to your `~/.openclaw/config.yaml`:

```yaml
plugins:
  entries:
    ragflow-knowledge:
      apiUrl: "http://localhost:80"          # Your RAGFlow server URL
      apiKey: "${RAGFLOW_API_KEY}"           # Or paste your API key directly
      datasetIds:                            # Optional: specific datasets to search
        - "dataset-123"
        - "dataset-456"
      autoInject: true                       # Auto-inject context (default: true)
      similarityThreshold: 0.1               # Minimum similarity score (0-1)
      topK: 5                                # Max chunks to retrieve
```

Or set via environment variables:

```bash
export RAGFLOW_API_KEY="ragflow-xxxxxxxx"
export RAGFLOW_API_URL="http://localhost:80"
```

### 3. Restart OpenClaw

```bash
openclaw restart
```

## Usage

### AI Agent Tools

The plugin registers two tools that the AI can use:

#### `ragflow_search`

Search the knowledge base:

```
You: What are the features of our product?
AI: [Uses ragflow_search tool] Based on the knowledge base...
```

#### `ragflow_list_datasets`

List available knowledge bases:

```
You: What knowledge bases are available?
AI: [Uses ragflow_list_datasets tool] You have 3 knowledge bases...
```

### CLI Commands

Search knowledge base from terminal:

```bash
# Search knowledge base
openclaw ragflow search "how to install the product"

# List all knowledge bases
openclaw ragflow datasets

# Get more results
openclaw ragflow search "product features" --top-k 10
```

### Auto-Context Injection

When enabled (default), the plugin automatically searches for relevant knowledge before each AI conversation and injects it into context:

```yaml
plugins:
  entries:
    ragflow-knowledge:
      autoInject: true    # Enable auto-injection
```

This means the AI will automatically have access to relevant information without needing explicit tool calls!

## Configuration Options

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `apiUrl` | string | Required | RAGFlow server URL |
| `apiKey` | string | Required | RAGFlow API key |
| `datasetIds` | array | All datasets | Specific datasets to search |
| `autoInject` | boolean | `true` | Auto-inject relevant context |
| `similarityThreshold` | number | RAGFlow API default | Minimum similarity (0-1). If not set, RAGFlow API uses its own default |
| `topK` | number | RAGFlow API default | Max chunks to retrieve. If not set, RAGFlow API uses its own default |

## RAGFlow Setup

If you don't have RAGFlow running yet:

### Using Docker

```bash
docker run -d -p 80:80 \
  -v /path/to/data:/ragflow/data \
  infiniflow/ragflow:latest
```

### From Source

See [RAGFlow Installation Guide](https://ragflow.io/docs/install)

### Create Knowledge Base

1. Open RAGFlow web interface
2. Click "Knowledge Base" → "Create"
3. Upload your documents (PDF, DOCX, TXT, etc.)
4. Wait for parsing and chunking
5. Note the Dataset ID

## Examples

### Example 1: Product FAQ Bot

```yaml
plugins:
  entries:
    ragflow-knowledge:
      apiUrl: "http://localhost:80"
      apiKey: "${RAGFLOW_API_KEY}"
      datasetIds:
        - "product-faq-dataset"
      autoInject: true
```

Now the AI can answer product questions:

```
User: How do I reset the device?
AI: [Auto-injects relevant FAQ entries]
     According to the documentation, you can reset...
```

### Example 2: Technical Documentation Search

```yaml
plugins:
  entries:
    ragflow-knowledge:
      apiUrl: "https://ragflow.example.com"
      apiKey: "${RAGFLOW_API_KEY}"
      datasetIds:
        - "api-docs"
        - "developer-guide"
      similarityThreshold: 0.2
      topK: 10
```

### Example 3: Multi-Knowledge Base

Search across multiple knowledge bases:

```yaml
plugins:
  entries:
    ragflow-knowledge:
      apiUrl: "http://localhost:80"
      apiKey: "${RAGFLOW_API_KEY}"
      datasetIds:
        - "hr-docs"
        - "it-docs"
        - "company-policies"
```

## Troubleshooting

### Plugin Not Found

```bash
# Check if plugin is installed
openclaw doctor

# Reinstall OpenClaw
npm install -g openclaw@latest
```

### API Connection Error

```bash
# Check RAGFlow server is running
curl http://localhost:80/api/v1/datasets \
  -H "Authorization: Bearer YOUR_API_KEY"

# Check firewall/network settings
```

### No Results Returned

- Lower `similarityThreshold` (e.g., `0.05`)
- Increase `topK` (e.g., `10`)
- Check dataset has documents uploaded
- Verify documents have been parsed

## Development

Build the plugin:

```bash
cd extensions/ragflow-knowledge
pnpm install
pnpm build
```

Run tests:

```bash
pnpm test
```

## License

MIT

## Links

- [RAGFlow Documentation](https://ragflow.io/docs)
- [RAGFlow API Reference](https://ragflow.io/docs/dev/http_api_reference)
- [OpenClaw Plugins](https://docs.openclaw.ai/tools/plugin)

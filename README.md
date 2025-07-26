# Graphiti MCP Server
[中文版本](README_zh.md)

This is a standalone Model Context Protocol (MCP) server implementation for Graphiti, specifically designed as an independent service with enhanced features.

## Source Repository

This project is based on the official [Graphiti](https://github.com/getzep/graphiti) project. The original Graphiti framework provides the core functionality for building and querying temporally-aware knowledge graphs.

This standalone edition maintains compatibility with the original Graphiti while adding enhanced features and improved performance through FastMCP refactoring.

## Key Differences from Official Graphiti MCP

This standalone edition differs from the official Graphiti MCP implementation in the following ways:

1. **Client-defined Group ID**: Unlike the official version, this implementation allows clients to define their own `group_id` for better data organization and isolation.

2. **FastMCP Refactoring**: The server has been refactored using FastMCP framework for improved performance and maintainability.

## Features

The Graphiti MCP server exposes the following key high-level functions of Graphiti:

- **Episode Management**: Add, retrieve, and delete episodes (text, messages, or JSON data)
- **Entity Management**: Search and manage entity nodes and relationships in the knowledge graph
- **Search Capabilities**: Search for facts (edges) and node summaries using semantic and hybrid search
- **Group Management**: Organize and manage groups of related data with group_id filtering
- **Graph Maintenance**: Clear the graph and rebuild indices

## Quick Start

### Installation

1. Ensure you have Python 3.10 or higher installed.
2. Install the package using pip:
install from source:

```bash
git clone git@github.com:dreamnear/graphiti-mcp.git
cd graphiti-mcp
pip install -e .
```

### Prerequisites

1. A running Neo4j database (version 5.26 or later required)
2. OpenAI API key for LLM operations (optional, but required for entity extraction)

### Setup

1. Copy the provided `.env.example` file to create a `.env` file:
   ```bash
   cp .env.example .env
   ```

2. Edit the `.env` file to set your configuration:
   ```
   # Required Neo4j configuration
   NEO4J_URI=bolt://localhost:7687
   NEO4J_USER=neo4j
   NEO4J_PASSWORD=your_password_here

   # Optional OpenAI API key for LLM operations
   OPENAI_API_KEY=your_openai_api_key_here
   MODEL_NAME=gpt-4.1-mini
   ```

## Running the Server

### Direct Execution

To run the Graphiti MCP server directly:

```bash
graphiti-mcp-server
```

Or with options:

```bash
graphiti-mcp-server --model gpt-4.1-mini --transport sse --group-id my_project
```

### Using uv

If you prefer to use `uv` for package management:

```bash
# Install uv if you don't have it already
curl -LsSf https://astral.sh/uv/install.sh | sh

# Install dependencies
uv sync

# Run the server
uv run graphiti-mcp-server
```

### Docker Deployment

The Graphiti MCP server can be deployed using Docker:

```bash
docker build -t graphiti-mcp-server .
docker run -p 8000:8000 --env-file .env graphiti-mcp-server
```

Or using Docker Compose (includes Neo4j):

```bash
docker-compose up
```

## Configuration

The server uses the following environment variables:

- `NEO4J_URI`: URI for the Neo4j database (default: `bolt://localhost:7687`)
- `NEO4J_USER`: Neo4j username (default: `neo4j`)
- `NEO4J_PASSWORD`: Neo4j password (default: `demodemo`)
- `OPENAI_API_KEY`: OpenAI API key (required for LLM operations)
- `OPENAI_BASE_URL`: Optional base URL for OpenAI API
- `MODEL_NAME`: OpenAI model name to use for LLM operations (default: `gpt-4.1-mini`)
- `SMALL_MODEL_NAME`: OpenAI model name to use for smaller LLM operations (default: `gpt-4.1-nano`)
- `LLM_TEMPERATURE`: Temperature for LLM responses (0.0-2.0, default: 0.0)
- `AZURE_OPENAI_ENDPOINT`: Optional Azure OpenAI LLM endpoint URL
- `AZURE_OPENAI_DEPLOYMENT_NAME`: Optional Azure OpenAI LLM deployment name
- `AZURE_OPENAI_API_VERSION`: Optional Azure OpenAI LLM API version
- `AZURE_OPENAI_EMBEDDING_API_KEY`: Optional Azure OpenAI Embedding deployment key
- `AZURE_OPENAI_EMBEDDING_ENDPOINT`: Optional Azure OpenAI Embedding endpoint URL
- `AZURE_OPENAI_EMBEDDING_DEPLOYMENT_NAME`: Optional Azure OpenAI embedding deployment name
- `AZURE_OPENAI_EMBEDDING_API_VERSION`: Optional Azure OpenAI API version
- `AZURE_OPENAI_USE_MANAGED_IDENTITY`: Optional use Azure Managed Identities for authentication
- `SEMAPHORE_LIMIT`: Episode processing concurrency (default: 10)
- `MCP_SERVER_HOST`: Host to bind the server to (default: 127.0.0.1)
- `MCP_SERVER_PORT`: Port to bind the server to (default: 8000)

## Available Arguments

- `--transport`: Choose the transport method (`stdio`, `http`, or `sse`, default: `stdio`)
- `--model`: Overrides the `MODEL_NAME` environment variable
- `--small-model`: Overrides the `SMALL_MODEL_NAME` environment variable
- `--temperature`: Overrides the `LLM_TEMPERATURE` environment variable
- `--group-id`: Set a namespace for the graph (default: "default")
- `--destroy-graph`: If set, destroys all Graphiti graphs on startup
- `--use-custom-entities`: Enable entity extraction using the predefined ENTITY_TYPES
- `--host`: Host to bind the MCP server to (default: 127.0.0.1)
- `--port`: Port to bind the MCP server to (default: 8000)
- `--path`: Path for transport endpoint (default: /mcp for HTTP, /sse for SSE)

## Integrating with MCP Clients

### STDIO Transport (for Claude Desktop, etc.)

```json
{
  "mcpServers": {
    "graphiti-memory": {
      "transport": "stdio",
      "command": "graphiti-mcp-server",
      "args": ["--transport", "stdio"],
      "env": {
        "NEO4J_URI": "bolt://localhost:7687",
        "NEO4J_USER": "neo4j",
        "NEO4J_PASSWORD": "your_password",
        "OPENAI_API_KEY": "your_api_key"
      }
    }
  }
}
```

### HTTP Transport (for general HTTP clients)

```json
{
  "mcpServers": {
    "graphiti-memory": {
      "type": "http",
      "url": "http://localhost:8000/mcp/?group_id=default"
    }
  }
}
```

### SSE Transport (for Cursor, etc.)

```json
{
  "mcpServers": {
    "graphiti-memory": {
      "transport": "sse",
      "url": "http://localhost:8000/sse?group_id=my_project"
    }
  }
}
```

## Available Tools

The Graphiti MCP server exposes the following tools:

- `add_memory`: Add an episode to the knowledge graph (supports text, JSON, and message formats)
- `search_memory_nodes`: Search the knowledge graph for relevant node summaries
- `search_memory_facts`: Search the knowledge graph for relevant facts (edges between entities)
- `delete_entity_edge`: Delete an entity edge from the knowledge graph
- `delete_episode`: Delete an episode from the knowledge graph
- `get_entity_edge`: Get an entity edge by its UUID
- `get_episodes`: Get the most recent episodes for a specific group
- `clear_graph`: Clear all data from the knowledge graph and rebuild indices

## Working with JSON Data

The Graphiti MCP server can process structured JSON data through the `add_memory` tool with `source="json"`:

```python
add_memory(
    name="Customer Profile",
    episode_body='{"company": {"name": "Acme Technologies"}, "products": [{"id": "P001", "name": "CloudSync"}, {"id": "P002", "name": "DataMiner"}]}',
    source="json",
    source_description="CRM data"
)
```

## Requirements

- Python 3.10 or higher
- Neo4j database (version 5.26 or later required)
- OpenAI API key (for LLM operations and embeddings)

# Graphiti MCP Server - Project Structure

This document describes the structure and key components of the standalone Graphiti MCP Server project.

## Project Overview

The Graphiti MCP Server is a standalone Model Context Protocol (MCP) server that exposes Graphiti's knowledge graph capabilities through standardized MCP tools and resources. It can be used with any MCP-compatible AI assistant or IDE.

## Directory Structure

```
graphiti-mcp-standalone/
├── graphiti_mcp_server.py     # Main server implementation
├── pyproject.toml            # Project metadata and dependencies
├── README.md                 # Project documentation and usage guide
├── LICENSE                   # Apache 2.0 License
├── Dockerfile               # Docker image definition
├── docker-compose.yml       # Docker Compose configuration (includes Neo4j)
├── .env.example            # Environment variable examples
├── example_usage.py        # Example usage demonstration
├── cursor_rules.md         # Cursor IDE integration rules
├── mcp_config_sse_example.json  # SSE transport configuration example
├── mcp_config_stdio_example.json # STDIO transport configuration example
├── TESTING_GUIDE.md        # Testing guide and instructions
└── uv.lock                # Dependency lock file
```

## Key Components

### Main Server (`graphiti_mcp_server.py`)

The core implementation that provides:

- **MCP Tools**: Exposes Graphiti functionality as MCP tools
  - `add_memory`: Add episodes to the knowledge graph
  - `search_memory_nodes`: Search for entity nodes
  - `search_memory_facts`: Search for relationships/facts
  - `delete_entity_edge`: Delete entity relationships
  - `delete_episode`: Delete episodes
  - `get_entity_edge`: Retrieve specific entity relationships
  - `get_episodes`: Get recent episodes
  - `clear_graph`: Clear the entire knowledge graph

- **MCP Resources**: Provides server status information
  - `http://graphiti/status`: Server and database connection status

- **Transports**: Supports multiple communication protocols
  - STDIO: For local AI assistants (Claude Desktop, etc.)
  - HTTP: For web-based integrations
  - SSE: For streaming connections (Cursor, etc.)

### Configuration Files

- **pyproject.toml**: Defines project metadata, dependencies, and entry points
- **Dockerfile**: Container image definition for easy deployment
- **docker-compose.yml**: Complete deployment with Neo4j database
- **.env.example**: Environment variable templates

### Documentation

- **README.md**: Comprehensive usage guide and integration examples
- **cursor_rules.md**: Specific integration instructions for Cursor IDE
- **TESTING_GUIDE.md**: Testing procedures and guidelines

## Installation and Usage

### Quick Start

```bash
# Install the package
pip install graphiti-mcp-server

# Or install from source
pip install -e .

# Run the server
graphiti-mcp-server --transport sse --group-id my_project
```

### Docker Deployment

```bash
# Build and run with Docker Compose (includes Neo4j)
docker-compose up
```

## Integration Examples

The server can be integrated with various MCP-compatible clients:

- **Cursor IDE**: Via SSE transport
- **Claude Desktop**: Via STDIO transport with mcp-remote gateway
- **Custom AI Applications**: Via any MCP transport

## Dependencies

Key dependencies include:

- `graphiti-core`: The core knowledge graph library
- `fastmcp`: MCP protocol implementation
- `openai`: LLM client library
- `neo4j`: Graph database driver
- `azure-identity`: Azure authentication support
- `python-dotenv`: Environment variable management

## Development

The standalone server is designed to be:

- **Self-contained**: All necessary components included
- **Configurable**: Flexible environment variable system
- **Deployable**: Multiple deployment options (pip, Docker, source)
- **Compatible**: Works with standard MCP clients
- **Extensible**: Easy to add new tools and features

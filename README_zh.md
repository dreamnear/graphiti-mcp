# Graphiti MCP 服务器

[English Version](README.md)

这是一个为 Graphiti 设计的独立 Model Context Protocol (MCP) 服务器实现，专门作为具有增强功能的独立服务。

## 来源仓库

本项目基于官方 [Graphiti](https://github.com/getzep/graphiti) 项目。原始的 Graphiti 框架提供了构建和查询时间感知知识图谱的核心功能。

这个独立版本保持与原始 Graphiti 的兼容性，同时通过 FastMCP 重构增加了增强功能和改进的性能。

## 与官方 Graphiti MCP 的主要区别

这个独立版本与官方 Graphiti MCP 实现在以下方面有所不同：

1. **客户端定义的组 ID**：与官方版本不同，此实现允许客户端定义自己的 `group_id`，以实现更好的数据组织和隔离。

2. **FastMCP 重构**：服务器使用 FastMCP 框架进行了重构，以提高性能和可维护性。

## 功能特性

Graphiti MCP 服务器暴露了 Graphiti 的以下关键高级功能：

- **事件管理**：添加、检索和删除事件（文本、消息或 JSON 数据）
- **实体管理**：搜索和管理知识图谱中的实体节点和关系
- **搜索功能**：使用语义和混合搜索搜索事实（边）和节点摘要
- **组管理**：使用 group_id 过滤组织和管理相关数据组
- **图维护**：清除图谱并重建索引

## 快速开始

### 安装

1. 确保已安装 Python 3.10 或更高版本。
从源码安装：

```bash
git clone git@github.com:dreamnear/graphiti-mcp.git
cd graphiti-mcp
pip install -e .
```

### 先决条件

1. 运行中的 Neo4j 数据库（需要 5.26 或更高版本）
2. OpenAI API 密钥用于 LLM 操作（可选，但实体提取需要）

### 设置

1. 复制提供的 `.env.example` 文件创建 `.env` 文件：
   ```bash
   cp .env.example .env
   ```

2. 编辑 `.env` 文件设置配置：
   ```
   # 必需的 Neo4j 配置
   NEO4J_URI=bolt://localhost:7687
   NEO4J_USER=neo4j
   NEO4J_PASSWORD=your_password_here

   # 可选的 OpenAI API 密钥用于 LLM 操作
   OPENAI_API_KEY=your_openai_api_key_here
   MODEL_NAME=gpt-4.1-mini
   ```

## 运行服务器

### 直接执行

直接运行 Graphiti MCP 服务器：

```bash
graphiti-mcp-server
```

或者带选项运行：

```bash
graphiti-mcp-server --model gpt-4.1-mini --transport sse --group-id my_project
```

### 使用 uv

如果更喜欢使用 `uv` 进行包管理：

```bash
# 如果还没有安装 uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# 安装依赖
uv sync

# 运行服务器
uv run graphiti-mcp-server
```

### Docker 部署

可以使用 Docker 部署 Graphiti MCP 服务器：

```bash
docker build -t graphiti-mcp-server .
docker run -p 8000:8000 --env-file .env graphiti-mcp-server
```

或者使用 Docker Compose（包含 Neo4j）：

```bash
docker-compose up
```

## 配置

服务器使用以下环境变量：

- `NEO4J_URI`: Neo4j 数据库 URI（默认：`bolt://localhost:7687`）
- `NEO4J_USER`: Neo4j 用户名（默认：`neo4j`）
- `NEO4J_PASSWORD`: Neo4j 密码（默认：`demodemo`）
- `OPENAI_API_KEY`: OpenAI API 密钥（LLM 操作必需）
- `OPENAI_BASE_URL`: OpenAI API 的可选基础 URL
- `MODEL_NAME`: 用于 LLM 操作的 OpenAI 模型名称（默认：`gpt-4.1-mini`）
- `SMALL_MODEL_NAME`: 用于较小 LLM 操作的 OpenAI 模型名称（默认：`gpt-4.1-nano`）
- `LLM_TEMPERATURE`: LLM 响应的温度（0.0-2.0，默认：0.0）
- `AZURE_OPENAI_ENDPOINT`: 可选的 Azure OpenAI LLM 端点 URL
- `AZURE_OPENAI_DEPLOYMENT_NAME`: 可选的 Azure OpenAI LLM 部署名称
- `AZURE_OPENAI_API_VERSION`: 可选的 Azure OpenAI LLM API 版本
- `AZURE_OPENAI_EMBEDDING_API_KEY`: 可选的 Azure OpenAI 嵌入部署密钥
- `AZURE_OPENAI_EMBEDDING_ENDPOINT`: 可选的 Azure OpenAI 嵌入端点 URL
- `AZURE_OPENAI_EMBEDDING_DEPLOYMENT_NAME`: 可选的 Azure OpenAI 嵌入部署名称
- `AZURE_OPENAI_EMBEDDING_API_VERSION`: 可选的 Azure OpenAI API 版本
- `AZURE_OPENAI_USE_MANAGED_IDENTITY`: 可选的使用 Azure 托管身份进行身份验证
- `SEMAPHORE_LIMIT`: 事件处理并发数（默认：10）
- `MCP_SERVER_HOST`: 服务器绑定的主机（默认：127.0.0.1）
- `MCP_SERVER_PORT`: 服务器绑定的端口（默认：8000）

## 可用参数

- `--transport`: 选择传输方法（`stdio`、`http` 或 `sse`，默认：`stdio`）
- `--model`: 覆盖 `MODEL_NAME` 环境变量
- `--small-model`: 覆盖 `SMALL_MODEL_NAME` 环境变量
- `--temperature`: 覆盖 `LLM_TEMPERATURE` 环境变量
- `--group-id`: 为图谱设置命名空间（默认："default"）
- `--destroy-graph`: 如果设置，则在启动时销毁所有 Graphiti 图谱
- `--use-custom-entities`: 启用使用预定义 ENTITY_TYPES 的实体提取
- `--host`: MCP 服务器绑定的主机（默认：127.0.0.1）
- `--port`: MCP 服务器绑定的端口（默认：8000）
- `--path`: 传输端点路径（默认：HTTP 为 /mcp，SSE 为 /sse）

## 与 MCP 客户端集成

### STDIO 传输（用于 Claude Desktop 等）

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

### HTTP 传输（用于通用 HTTP 客户端）

```json
{
  "mcpServers": {
    "graphiti-memory": {
      "transport": "http",
      "url": "http://localhost:8000/mcp"
    }
  }
}
```

### SSE 传输（用于 Cursor 等）

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

## 可用工具

Graphiti MCP 服务器暴露以下工具：

- `add_memory`: 向知识图谱添加事件（支持文本、JSON 和消息格式）
- `search_memory_nodes`: 搜索知识图谱中相关的节点摘要
- `search_memory_facts`: 搜索知识图谱中相关的事实（实体间的边）
- `delete_entity_edge`: 从知识图谱中删除实体边
- `delete_episode`: 从知识图谱中删除事件
- `get_entity_edge`: 通过 UUID 获取实体边
- `get_episodes`: 获取特定组的最新事件
- `clear_graph`: 清除知识图谱中的所有数据并重建索引

## 处理 JSON 数据

Graphiti MCP 服务器可以通过 `add_memory` 工具处理结构化 JSON 数据，使用 `source="json"`：

```python
add_memory(
    name="客户档案",
    episode_body='{"company": {"name": "Acme Technologies"}, "products": [{"id": "P001", "name": "CloudSync"}, {"id": "P002", "name": "DataMiner"}]}',
    source="json",
    source_description="CRM 数据"
)
```

## 要求

- Python 3.10 或更高版本
- Neo4j 数据库（需要 5.26 或更高版本）
- OpenAI API 密钥（用于 LLM 操作和嵌入）


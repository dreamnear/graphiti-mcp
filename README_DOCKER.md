# Graphiti MCP Server Docker 部署指南

## Docker 镜像构建与推送

### 本地构建 Docker 镜像

```bash
docker build -t graphiti-mcp-server .
```

### 运行 Docker 容器

```bash
docker run -p 8000:8000 \
  -e OPENAI_API_KEY=your_openai_api_key \
  -e NEO4J_URI=bolt://your-neo4j-host:7687 \
  -e NEO4J_USER=neo4j \
  -e NEO4J_PASSWORD=your_password \
  graphiti-mcp-server
```

### 环境变量配置

| 环境变量 | 描述 | 默认值 |
|---------|------|--------|
| `OPENAI_API_KEY` | OpenAI API 密钥 | 必需 |
| `NEO4J_URI` | Neo4j 数据库 URI | `bolt://localhost:7687` |
| `NEO4J_USER` | Neo4j 用户名 | `neo4j` |
| `NEO4J_PASSWORD` | Neo4j 密码 | `password` |
| `MODEL_NAME` | LLM 模型名称 | `gpt-4.1-mini` |
| `SMALL_MODEL_NAME` | 小型 LLM 模型名称 | `gpt-4.1-nano` |
| `EMBEDDER_MODEL_NAME` | 嵌入模型名称 | `text-embedding-3-small` |
| `MCP_SERVER_HOST` | MCP 服务器主机 | `127.0.0.1` |
| `MCP_SERVER_PORT` | MCP 服务器端口 | `8000` |

## Docker Hub 自动推送配置

### 1. 设置 Docker Hub 凭据

在 GitHub 仓库的 **Settings** → **Secrets and variables** → **Actions** 中添加以下 secrets：

- `DOCKERHUB_USERNAME`: 你的 Docker Hub 用户名
- `DOCKERHUB_TOKEN`: 你的 Docker Hub 访问令牌

### 2. GitHub Actions 工作流

工作流会自动在以下情况下推送镜像：

- 推送到 `main` 分支：推送 `latest` 标签
- 创建 Git 标签（如 `v1.0.0`）：推送版本标签
- Pull Request：仅构建不推送

### 3. 手动构建和推送

```bash
# 登录 Docker Hub
docker login

# 构建镜像
docker build -t your-dockerhub-username/graphiti-mcp:latest .

# 推送镜像
docker push your-dockerhub-username/graphiti-mcp:latest
```

## 使用 Docker Compose

创建 `docker-compose.yml` 文件：

```yaml
version: '3.8'
services:
  graphiti-mcp:
    build: .
    ports:
      - "8000:8000"
    environment:
      - OPENAI_API_KEY=your_openai_api_key
      - NEO4J_URI=bolt://neo4j:7687
      - NEO4J_USER=neo4j
      - NEO4J_PASSWORD=your_password
    depends_on:
      - neo4j

  neo4j:
    image: neo4j:5.19.0-community
    ports:
      - "7474:7474"
      - "7687:7687"
    environment:
      - NEO4J_AUTH=neo4j/your_password
      - NEO4J_PLUGINS=["apoc"]
    volumes:
      - ./neo4j/data:/data
      - ./neo4j/logs:/logs
      - ./neo4j/import:/var/lib/neo4j/import
```

运行：
```bash
docker-compose up -d

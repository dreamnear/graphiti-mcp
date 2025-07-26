# Graphiti MCP Server - 测试指南

## 🧪 测试脚本概览

本项目提供了多个测试脚本来验证 FastMCP 2.0 重构后的 Graphiti MCP 服务器功能。

## 📋 测试脚本列表

### 1. `test_fastmcp2.py` - 基础功能测试
**用途**: 验证服务器基本连接和工具注册
```bash
python test_fastmcp2.py
```

**测试内容**:
- 服务器连接
- Ping 测试
- 工具和资源列表

### 2. `test_http.py` - HTTP 传输测试
**用途**: 验证 Streamable HTTP 传输功能
```bash
python test_http.py
```

**测试内容**:
- 启动 HTTP 服务器
- 通过 HTTP 连接测试
- 工具调用验证

### 3. `test_tools_simple.py` - 工具模式测试
**用途**: 验证所有工具的模式和基本调用（无需数据库）
```bash
python test_tools_simple.py
```

**测试内容**:
- 所有 8 个工具的模式验证
- 工具调用测试（预期数据库错误）
- 服务器状态检查

### 4. `test_all_tools.py` - 全面功能测试
**用途**: 完整的端到端测试（需要 Neo4j 数据库）
```bash
# 内存模式测试
python test_all_tools.py memory

# HTTP 模式测试  
python test_all_tools.py http

# 性能测试
python test_all_tools.py perf

# 所有测试
python test_all_tools.py all
```

**测试内容**:
- 完整的工具功能测试
- 数据添加、搜索、删除流程
- 性能和并发测试

## 🚀 快速开始

### 1. 基础验证（推荐开始）
```bash
# 验证服务器基本功能
python test_tools_simple.py
```

### 2. HTTP 传输测试
```bash
# 启动 HTTP 服务器（新终端）
python graphiti_mcp_server.py --transport http --port 8001

# 运行 HTTP 测试（另一个终端）
python test_http.py
```

### 3. 完整功能测试（需要数据库）
```bash
# 设置环境变量
export NEO4J_URI="bolt://localhost:7687"
export NEO4J_USER="neo4j"
export NEO4J_PASSWORD="your_password"
export OPENAI_API_KEY="your_openai_key"

# 运行完整测试
python test_all_tools.py all
```

## 🔧 环境配置

### 最小配置（基础测试）
```bash
# 只需要 FastMCP 2.0
pip install fastmcp>=2.0.0
```

### 完整配置（全功能测试）
```bash
# 安装所有依赖
pip install -r requirements.txt

# 设置环境变量
export NEO4J_URI="bolt://localhost:7687"
export NEO4J_USER="neo4j"  
export NEO4J_PASSWORD="password"
export OPENAI_API_KEY="sk-..."
```

### Neo4j 数据库设置
```bash
# 使用 Docker 启动 Neo4j
docker run \
    --name neo4j \
    -p 7474:7474 -p 7687:7687 \
    -d \
    -e NEO4J_AUTH=neo4j/password \
    neo4j:latest
```

## 📊 测试结果解读

### ✅ 成功指标
- `✓ Server ping successful` - 服务器连接正常
- `✓ Found X tools` - 工具注册成功
- `✓ HTTP server ping successful` - HTTP 传输正常

### ⚠️ 预期警告
- `Graphiti client not initialized` - 正常，无数据库时的预期行为
- `Neo4j connection failed` - 需要配置数据库连接
- `307 Temporary Redirect` - 正常，FastAPI 自动重定向行为

### ❌ 错误情况
- `ImportError` - 依赖包未安装
- `Connection refused` - 服务器未启动或端口被占用

## 🎯 测试场景

### 场景 1: 开发环境验证
```bash
# 验证重构是否成功
python test_fastmcp2.py
python test_tools_simple.py
```

### 场景 2: 部署前验证
```bash
# 验证 HTTP 传输
python test_http.py

# 验证所有功能（需要数据库）
python test_all_tools.py all
```

### 场景 3: 性能测试
```bash
# 并发性能测试
python test_all_tools.py perf
```

### 场景 4: 持续集成
```bash
# CI/CD 管道中的自动化测试
python test_tools_simple.py  # 无需外部依赖
```

## 🔍 故障排除

### 常见问题

1. **导入错误**
   ```bash
   # 确保在正确目录
   cd mcp_server
   python test_tools_simple.py
   ```

2. **连接失败**
   ```bash
   # 检查服务器是否运行
   python graphiti_mcp_server.py --transport http --port 8001
   ```

3. **数据库错误**
   ```bash
   # 检查 Neo4j 状态
   docker ps | grep neo4j
   ```

### 调试模式
```bash
# 启用详细日志
export PYTHONPATH=.
python -v test_tools_simple.py
```

## 📈 测试覆盖率

| 组件 | 基础测试 | HTTP测试 | 完整测试 |
|------|----------|----------|----------|
| 服务器连接 | ✅ | ✅ | ✅ |
| 工具注册 | ✅ | ✅ | ✅ |
| STDIO 传输 | ✅ | - | ✅ |
| HTTP 传输 | - | ✅ | ✅ |
| 数据操作 | - | - | ✅ |
| 性能测试 | - | - | ✅ |

## 🎉 成功标准

测试通过的标准：
- ✅ 所有基础测试无错误
- ✅ HTTP 传输测试成功
- ✅ 工具模式验证通过
- ✅ 预期的数据库错误正确处理

这表明 FastMCP 2.0 重构成功，服务器已准备好用于生产环境。

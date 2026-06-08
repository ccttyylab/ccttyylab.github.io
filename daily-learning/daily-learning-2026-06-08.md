# 📅 2026-06-08 每日学习报告

## 📖 今日主题：MCP（Model Context Protocol）— AI 工具集成的统一协议

---

### 一、学到了什么

MCP（Model Context Protocol）是由 Anthropic 于 2024 年底发布的开放协议，旨在标准化 AI 模型/Agent 与外部数据源、工具和服务之间的连接方式。可以把它理解为"AI 领域的 USB-C"——一个通用接口，让任何 AI 助手都能通过单一集成接入任何工具或服务。

MCP 协议使用 JSON-RPC 2.0 作为底层通信格式，支持 stdio、SSE 和 Streamable HTTP 三种传输方式。目前已获得 VS Code（GitHub Copilot）、Cursor、Claude Desktop、Windsurf 等主流 AI 开发工具的采纳，生态中有 1000+ 个 MCP Server 实现。

---

### 二、核心要点

#### 1. 架构角色
- **MCP Server（服务端）**：提供工具、资源和提示词模板，是数据和功能的提供者
- **MCP Client（客户端）**：消费 Server 提供的能力，通常是 AI 应用或 Agent
- **Transport（传输层）**：支持 stdio（本地进程）、SSE（Server-Sent Events）和 Streamable HTTP

#### 2. 三大原语（Primitives）
- **Tools（工具）**：类似 REST API 的 POST 端点，执行代码或产生副作用
- **Resources（资源）**：类似 GET 端点，向 LLM 上下文加载数据
- **Prompts（提示词）**：可复用的 LLM 交互模板，定义标准化的对话模式

#### 3. Python SDK 快速上手
使用 `mcp` 包 + FastMCP 框架，几十行代码即可构建一个 MCP Server：

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("MyServer")

@mcp.tool()
def add(a: int, b: int) -> int:
    """两数相加"""
    return a + b

@mcp.resource("greeting://{name}")
def get_greeting(name: str) -> str:
    return f"Hello, {name}!"

if __name__ == "__main__":
    mcp.run(transport="streamable-http")
```

安装方式：`uv add "mcp[cli]"` 或 `pip install "mcp[cli]"`

#### 4. 生命周期管理
支持 `lifespan` 上下文管理器，在 Server 启动时初始化资源（如数据库连接），关闭时自动清理，保证类型安全。

---

### 三、实际应用场景

1. **企业内部知识库接入**：构建 MCP Server 连接公司 Confluence/Notion，让 AI 助手能直接检索内部文档
2. **数据库查询助手**：将 PostgreSQL/MySQL 封装为 MCP Server，AI 可以自然语言查询数据
3. **DevOps 自动化**：将 Docker、K8s、CI/CD 流水线封装为 MCP Tools，AI Agent 可以执行部署操作
4. **个人效率工具**：将日历、邮件、待办事项等封装为 MCP Server，打造全能 AI 秘书
5. **多 Agent 协作**：不同 Agent 通过 MCP 协议共享工具和数据，实现分工协作

---

### 四、参考链接

- [MCP 官方文档](https://modelcontextprotocol.io)
- [Python SDK](https://github.com/modelcontextprotocol/python-sdk)
- [MCP 规范](https://modelcontextprotocol.io/specification/latest)
- [Awesome MCP Servers（1000+ 服务器列表）](https://github.com/punkpeye/awesome-mcp-servers)
- [Anthropic 官方文档](https://docs.anthropic.com/en/docs/agents-and-tools/mcp)

---

### ✅ GitHub Issue 评论状态

- 评论已成功发布到 [Issue #4](https://github.com/ccttyylab/ccttyylab.github.io/issues/4#issuecomment-4647042281)
- HTTP Status: 201 Created
- 评论 ID: 4647042281

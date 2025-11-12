# Siti Claude Marketplace

Claude Code 插件市场项目，提供代码开发和 OP 平台相关的插件集合。

## 项目简介

`siti-claude-marketplace` 是一个 Claude Code 插件市场，包含两个核心插件：

- **coder-plugin**：代码开发插件（designer、developer、frontend agents）
- **op-plugin**：OP 平台插件（工作流、连接器、事件和数据库相关的 agents 和 skills）

## 项目结构

```
siti-claude-marketplace/
├── .claude-plugin/
│   └── marketplace.json          # 市场文件
└── plugins/
    ├── coder-plugin/              # 代码开发插件
    └── op-plugin/                 # OP 平台插件
```

## Claude Code CLI 配置

### 方式一：插件市场安装（官方推荐）

通过插件市场统一管理和分发插件，这是官方推荐的方式：

```bash
# 添加市场
/plugin marketplace add SeSiTing/siti-claude-marketplace

# 安装插件
/plugin install op-plugin@siti-claude-marketplace
/plugin install coder-plugin@siti-claude-marketplace

# 或交互式浏览并安装
/plugin
```

**优势**：
- 统一管理多个插件
- 版本控制和自动更新
- 团队分发更方便
- 支持插件发现和浏览

### 方式二：GitHub 直接安装

直接从 GitHub 存储库安装插件：

```bash
# 直接从 GitHub 安装 op-plugin
/plugin install op-plugin@github:SeSiTing/siti-claude-marketplace

# 直接从 GitHub 安装 coder-plugin
/plugin install coder-plugin@github:SeSiTing/siti-claude-marketplace
```

### 方式三：项目内自动识别

将市场项目克隆到项目目录中，Claude Code 会自动扫描并加载：

```bash
# 克隆到项目目录
git clone git@github.com:SeSiTing/siti-claude-marketplace.git plugins/siti-claude-marketplace

# 或使用 submodule
git submodule add git@github.com:SeSiTing/siti-claude-marketplace.git plugins/siti-claude-marketplace

# 启动 Claude Code，插件会自动加载
claude
```

### 方式四：作为依赖项加载特定插件

在项目代码中直接指定插件路径（适用于 SDK 集成）：

```python
from claude_agent_sdk import ClaudeAgentOptions

# 只加载 op-plugin
options = ClaudeAgentOptions(
    plugins=[
        {
            "type": "local",
            "path": "./plugins/siti-claude-marketplace/plugins/op-plugin"
        }
    ]
)

# 只加载 coder-plugin
options = ClaudeAgentOptions(
    plugins=[
        {
            "type": "local",
            "path": "./plugins/siti-claude-marketplace/plugins/coder-plugin"
        }
    ]
)
```

### 验证配置

```bash
# 使用调试模式查看插件加载情况
claude --debug

# 列出已安装的插件
/plugin list

# 列出已添加的市场
/plugin marketplace list

# 在 Claude Code 中验证
# 使用 /agents 命令查看可用 agents
```

### 删除配置

```bash
# 删除市场（不会删除已安装的插件）
/plugin marketplace remove SeSiTing/siti-claude-marketplace

# 删除插件（需单独删除）
/plugin uninstall op-plugin@siti-claude-marketplace
/plugin uninstall coder-plugin@siti-claude-marketplace

# 删除 MCP
/mcp remove <mcp-name>
```

**注意**：删除市场不会自动删除已安装的插件，需要单独执行删除插件命令。

## 插件说明

### coder-plugin

代码开发插件，包含：
- `designer` - 系统集成方案设计专家
- `developer` - 资深Java开发工程师
- `frontend` - 前端页面生成专家

### op-plugin

OP 平台插件，包含：
- **Agents**：`op_button`、`op_connector`、`op_event`、`op_workflow`
- **Skills**：`op_db`、`op_db_metadata`、`op_db_openapi`、`op_db_user`

## 版本管理

版本号在各个插件的 `.claude-plugin/plugin.json` 中管理。

- **version**：语义化版本号（如 1.0.1），重大更新时升级
- **updatedAt**：ISO 8601 格式的更新时间（如 2025-11-12T12:08:45+08:00），使用东八区时间，用于追踪小更新

**注意**：
- `updatedAt` 是自定义字段，仅在各插件的 `plugin.json` 中使用
- `marketplace.json` 的 schema 不支持 `updatedAt` 字段，因此不会出现在市场配置中
- 格式采用 ISO 8601 标准，包含时区信息（+08:00 表示东八区）

**更新策略**：
- 重大功能更新或 API 变更：升级版本号并更新 updatedAt
- 文档优化、bug 修复等小更新：仅更新 updatedAt，不升级版本号

发布时更新对应插件的版本号和 updatedAt，并创建 Git 标签。

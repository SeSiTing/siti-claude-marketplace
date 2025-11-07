# Siti Claude Marketplace 发布手册

## 项目概述

`siti-claude-marketplace` 是一个 Claude Code 插件市场项目，包含多个插件：

- **coder-plugin**：代码开发插件，包含 designer、developer、frontend 三个 agents
- **op-plugin**：OP 平台插件，包含工作流、连接器、事件和数据库相关的 agents 和 skills

## 项目结构

```
siti-claude-marketplace/
├── .claude-plugin/
│   └── marketplace.json          # 市场文件（必需）
└── plugins/
    ├── coder-plugin/              # 代码开发插件
    │   ├── .claude-plugin/
    │   │   └── plugin.json
    │   └── agents/
    │       ├── designer.md
    │       ├── developer.md
    │       └── frontend.md
    └── op-plugin/                 # OP 平台插件
        ├── .claude-plugin/
        │   └── plugin.json
        ├── agents/
        │   ├── op_button.md
        │   ├── op_connector.md
        │   ├── op_event.md
        │   └── op_workflow.md
        └── skills/
            ├── op_db/
            ├── op_db_metadata/
            ├── op_db_openapi/
            └── op_db_user/
```

## 版本管理

版本号在各个插件的 `.claude-plugin/plugin.json` 中管理：

```json
{
  "name": "plugin-name",
  "version": "1.0.0"
}
```

## 发布流程

### 1. 更新插件版本号

编辑对应插件的 `.claude-plugin/plugin.json`，更新 `version` 字段：

```bash
# 更新 coder-plugin 版本
vim plugins/coder-plugin/.claude-plugin/plugin.json

# 更新 op-plugin 版本
vim plugins/op-plugin/.claude-plugin/plugin.json
```

### 2. 更新市场文件（可选）

如果插件版本更新，同步更新 `.claude-plugin/marketplace.json` 中的版本信息。

### 3. 提交并创建标签

```bash
# 读取版本号（以 op-plugin 为例）
export VERSION=$(python3 -c "import json; print(json.load(open('plugins/op-plugin/.claude-plugin/plugin.json'))['version'])")

# 提交更改
git add .
git commit -m "chore: update plugins to version ${VERSION}"
git push origin main

# 创建标签
git tag -a "v${VERSION}" -m "Release version ${VERSION}"
git push origin "v${VERSION}"
```

## 在其他项目中使用

### 方式一：作为依赖项加载特定插件（推荐）

在项目代码中直接指定插件路径：

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

# 同时加载两个插件
options = ClaudeAgentOptions(
    plugins=[
        {
            "type": "local",
            "path": "./plugins/siti-claude-marketplace/plugins/op-plugin"
        },
        {
            "type": "local",
            "path": "./plugins/siti-claude-marketplace/plugins/coder-plugin"
        }
    ]
)
```

### 方式二：Git Submodule

```bash
# 在目标项目中添加 submodule
git submodule add git@github.com:SeSiTing/siti-claude-marketplace.git plugins/siti-claude-marketplace

# 使用特定版本
cd plugins/siti-claude-marketplace
git checkout v1.0.0
cd ../..
```

### 方式三：通过插件市场安装

```bash
# 添加市场
/plugin marketplace add SeSiTing/siti-claude-marketplace

# 安装特定插件
/plugin install op-plugin@siti-claude-marketplace
/plugin install coder-plugin@siti-claude-marketplace

# 或交互式浏览并安装
/plugin
```

## 插件说明

### coder-plugin

**用途**：代码开发相关功能

**包含的 Agents**：
- `designer` - 系统集成方案设计专家
- `developer` - 资深Java开发工程师
- `frontend` - 前端页面生成专家

**适用场景**：
- 系统集成方案设计
- Java 代码开发
- 前端页面生成

### op-plugin

**用途**：OP 平台相关功能

**包含的 Agents**：
- `op_button` - 按钮相关操作
- `op_connector` - 连接器相关操作
- `op_event` - 业务事件相关操作
- `op_workflow` - 工作流相关操作

**包含的 Skills**：
- `op_db` - 数据库操作技能
- `op_db_metadata` - 元数据查询技能
- `op_db_openapi` - OpenAPI 查询技能
- `op_db_user` - 用户相关技能

**适用场景**：
- OP 平台工作流开发
- 连接器集成
- 业务事件处理
- 数据库操作

## 发布检查清单

- [ ] 更新各插件的 `.claude-plugin/plugin.json` 版本号
- [ ] 更新 `.claude-plugin/marketplace.json` 中的版本信息（如需要）
- [ ] 确保所有 agents 和 skills 文件格式正确
- [ ] 提交更改到 Git
- [ ] 创建并推送 Git 标签
- [ ] 验证标签创建成功

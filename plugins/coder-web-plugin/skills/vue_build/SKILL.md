---
name: vue_build
description: Vue 项目构建工具，执行依赖安装和生产构建，生成可部署的静态文件并返回预览链接。
---

# Vue 项目构建工具

## 功能说明

自动执行 Vue 项目的完整构建流程：
1. 检查项目配置文件（package.json）
2. 安装项目依赖（npm install）
3. 执行生产构建（npm run build）
4. 生成可访问的预览链接

## 使用方式

**重要**：在完成 Vue 项目开发后，使用终端命令执行构建：

```bash
# 1. 检查 package.json 是否存在
ls -la package.json

# 2. 安装依赖
npm install

# 3. 执行构建
npm run build
```

## 构建要求

### 前置条件
- 工作目录中必须存在 `package.json`
- package.json 中必须定义 `build` 脚本
- 项目必须是有效的 Vue 3 + Vite 项目

### 构建产物
- **输出目录**：`dist/`
- **包含文件**：
  - `index.html`：入口 HTML（已注入资源引用）
  - `assets/`：打包后的 JS、CSS、图片等资源
- **文件特点**：已压缩、已优化、可直接部署

## 执行流程

### 步骤1：检查项目配置
```bash
ls -la package.json
```

如果不存在，提示用户：
```
❌ 未找到 package.json 文件
请先使用 frontend-vue Agent 创建 Vue 项目
```

### 步骤2：安装依赖
```bash
npm install
```

**说明**：
- 首次安装可能需要 1-3 分钟
- 会下载并安装所有依赖到 `node_modules/` 目录
- 失败时检查网络连接和 npm 配置

### 步骤3：执行构建
```bash
npm run build
```

**说明**：
- 构建时间通常为 10-30 秒
- Vite 会自动生成 `dist/` 目录
- 失败时检查代码错误和依赖问题

### 步骤4：生成预览链接

构建成功后，生成可访问的预览链接。

**链接格式**：
```
{WEB_BASE_URL}/ai-coder/code/{project_type}/o_{org_id}/w_{coder_id}/dist/
```

**示例**：
```
http://localhost:8080/ai-coder/code/web/o_20251114/w_102/dist/
```

**如何获取路径信息**：
- 从当前工作目录路径中提取 org_id 和 coder_id
- 工作目录格式：`/workspace/code/web/o_{org_id}/w_{coder_id}/`
- WEB_BASE_URL 默认为：`http://localhost:8080`

## 输出格式

构建成功后，以 Markdown 格式输出：

```markdown
✅ Vue 应用构建完成！

📦 构建信息：
- 项目类型：Vue 3 + Vite
- 输出目录：dist/
- 入口文件：index.html

🌐 预览地址：
[点击访问应用](http://localhost:8080/ai-coder/code/web/o_20251114/w_102/dist/)

💡 使用提示：
- 点击上方链接即可在浏览器中预览应用
- 应用已部署到工作区，可随时访问
- 如需修改，请重新编辑代码并再次构建
```

## 错误处理

### 错误1：package.json 不存在
```markdown
❌ 构建失败：未找到 package.json

请先使用 frontend-vue Agent 创建 Vue 项目：
1. 创建 package.json 配置文件
2. 创建 vite.config.js 配置
3. 创建 src/main.js 和 src/App.vue
4. 然后再执行构建
```

### 错误2：npm install 失败
```markdown
❌ 依赖安装失败

可能原因：
- 网络连接问题
- npm 配置错误
- package.json 配置错误

建议操作：
1. 检查网络连接
2. 检查 package.json 中的依赖版本
3. 尝试使用 npm cache clean --force 清理缓存
4. 重新执行 npm install
```

### 错误3：npm run build 失败
```markdown
❌ 构建失败

可能原因：
- 代码存在语法错误
- 依赖缺失或版本不兼容
- Vite 配置错误

建议操作：
1. 仔细检查错误信息中的具体错误
2. 修复代码中的语法错误
3. 确保所有依赖已正确安装
4. 检查 vite.config.js 配置是否正确
```

## 构建产物说明

### dist/ 目录结构
```
dist/
├── index.html          # 入口 HTML（已注入资源引用）
└── assets/
    ├── index-[hash].js     # 打包后的 JS（含 Vue 运行时和应用代码）
    ├── index-[hash].css    # 打包后的 CSS
    └── [其他资源]          # 图片、字体等静态资源
```

### 文件说明
- **index.html**：入口文件，浏览器首先加载此文件
- **assets/index-[hash].js**：所有 JavaScript 代码打包在一起，包含 Vue 框架和应用逻辑
- **assets/index-[hash].css**：所有样式打包在一起
- **[hash]**：文件名包含哈希值，用于缓存控制

## 清理说明

构建后的文件说明：
- **node_modules/**：依赖目录（约 50-100MB，可选择性删除）
- **dist/**：构建产物（必须保留，用于访问）
- **src/**：源代码（保留，用于后续修改）
- **其他配置文件**：保留

如需节省空间，可在构建完成后删除 node_modules/：
```bash
rm -rf node_modules/
```

**注意**：删除 node_modules/ 后，如果需要重新构建，需要再次执行 `npm install`。

## 性能优化

### 构建时间优化
- **首次构建**：1-3 分钟（需要下载依赖）
- **后续构建**：10-30 秒（已有依赖缓存）
- **增量构建**：Vite 支持快速增量构建

### 产物大小优化
- Vite 自动进行代码压缩和优化
- 使用 Tree-shaking 移除未使用的代码
- CSS 和 JS 自动分离和压缩
- 资源文件自动优化

## 访问路径解析

### 路径组成
```
http://localhost:8080/ai-coder/code/web/o_20251114/w_102/dist/
│                      │          │   │          │        │
│                      │          │   │          │        └─ 构建产物目录
│                      │          │   │          └─ Coder ID（工作区ID）
│                      │          │   └─ Organization ID（组织ID）
│                      │          └─ 项目类型（web）
│                      └─ 固定前缀
└─ 域名（从环境变量读取）
```

### 路径映射
- **URL路径**：`/ai-coder/code/web/o_20251114/w_102/dist/`
- **文件系统路径**：`/workspace/code/web/o_20251114/w_102/dist/`
- **后端路由**：`/ai-coder/code/{path:path}` → 映射到 `/workspace/code/{path}`

## 注意事项

1. **环境要求**：
   - Docker 容器中已安装 Node.js 和 npm
   - 确保网络连接正常（用于下载依赖）

2. **路径配置**：
   - 确保 vite.config.js 中 `base` 设置为 `'./'`（相对路径）
   - 这样可以适配任意部署路径

3. **构建时间**：
   - 首次构建较慢（需要下载依赖）
   - 后续构建会利用缓存加速

4. **预览链接**：
   - 链接格式由环境变量 `WEB_BASE_URL` 决定
   - 默认为 `http://localhost:8080`
   - 可在 `.env` 文件中修改

5. **安全性**：
   - 只能访问 `/workspace/code/` 下的文件
   - 路径遍历攻击已被防护
   - 文件系统权限由 Docker 容器控制

## 使用示例

### 示例1：简单页面构建
```bash
# 1. 检查文件
ls -la package.json

# 2. 安装依赖（首次）
npm install
# 输出：added 50 packages in 45s

# 3. 执行构建
npm run build
# 输出：
# vite v5.0.0 building for production...
# ✓ 15 modules transformed.
# dist/index.html                   0.45 kB
# dist/assets/index-abc123.js      150.25 kB │ gzip: 50.12 kB
# dist/assets/index-def456.css       2.10 kB │ gzip: 0.85 kB
# ✓ built in 3.25s

# 构建成功！返回预览链接
```

### 示例2：带接口调用的应用
```bash
# 同上，构建流程相同
# 区别在于应用包含 fetch 调用，但不影响构建过程
```

## 故障排查

### 问题1：npm install 超时
**解决方案**：
```bash
# 使用国内镜像源
npm config set registry https://registry.npmmirror.com
npm install
```

### 问题2：构建产物过大
**解决方案**：
- 检查是否引入了不必要的依赖
- 使用 Vite 的代码分割功能
- 压缩图片和其他静态资源

### 问题3：预览链接无法访问
**检查项**：
1. 确认 dist/ 目录已生成
2. 检查文件路径是否正确
3. 确认后端静态文件服务已启动
4. 检查 WEB_BASE_URL 环境变量配置

## 最佳实践

1. **开发完成后立即构建**：避免忘记构建导致无法预览
2. **检查构建输出**：确认没有错误和警告
3. **测试预览链接**：点击链接确认应用正常工作
4. **保留源代码**：不要删除 src/ 目录，便于后续修改
5. **文档化接口**：在代码注释中说明接口地址和参数


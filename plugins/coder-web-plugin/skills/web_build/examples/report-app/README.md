# 报工页面 - 工单管理系统

一个响应式的移动端报工页面，支持工单选择、物料信息展示和报工数据提交。

## 功能特性

### 核心功能

- 🔍 **工单选择**: 支持搜索和选择工单
- 📋 **物料信息**: 显示工单关联的物料详情和生产统计
- 📝 **报工表单**: 录入生产数量、辅助数量等信息
- 📤 **数据提交**: 在线提交报工数据

### 技术特性

- 📱 **响应式设计**: 移动端优先，适配各种屏幕尺寸
- 🔐 **智能认证**: 支持 URL 参数 code 和自动登录获取 token
- ⚡ **性能优化**: 防抖搜索、请求重试
- 🎨 **现代 UI**: 使用 Tailwind CSS，流畅的动画效果
- 🎯 **主题色**: 使用 `#02B980` 作为主题色

## 技术栈

- **前端框架**: 原生 JavaScript (ES6+)
- **样式框架**: Tailwind CSS 3.x
- **HTTP 客户端**: Fetch API
- **本地存储**: LocalStorage (用于 token 存储)
- **构建工具**: 无需构建，直接运行

## 项目结构

```
web/report/
├── index.html              # 主页面
├── css/
│   └── custom.css          # 自定义样式（主题色配置）
├── js/
│   ├── main.js            # 主应用逻辑
│   └── common.js          # 工具函数 + UI组件
├── services/
│   ├── core.js            # API服务 + 认证服务
│   └── business.js       # 工单服务 + 报工服务
├── api_doc/               # API接口文档
│   ├── 接口说明.txt
│   ├── 工单列表_BLACKLAKE-1686655055663532 (1).json
│   ├── 生产任务列表_BLACKLAKE-1681109889053785 (3).json
│   ├── 报工物料列表_BLACKLAKE-1681369551143844.json
│   ├── 批量报工_BLACKLAKE-1681109889053798 (1).json
│   └── 报工记录列表_BLACKLAKE-1681109889053794.json
└── README.md              # 项目文档
```

## 快速开始

### 1. 环境要求

- 现代浏览器 (Chrome 60+, Firefox 60+, Safari 12+)
- HTTP 服务器 (开发环境可使用本地服务器)

### 2. 安装运行

#### 方式一：直接打开

```bash
# 直接用浏览器打开 index.html
open index.html
```

**注意**: 由于浏览器的 CORS 限制，直接打开可能无法正常调用 API，建议使用本地服务器。

#### 方式二：本地服务器

```bash
# 使用Python启动本地服务器
cd web/report
python -m http.server 8000

# 或使用Node.js
npx serve .

# 访问 http://localhost:8000
```

#### 方式三：Live Server (推荐)

```bash
# 使用VS Code的Live Server插件
# 右键 index.html -> Open with Live Server
```

### 3. API 配置说明

系统已配置使用黑湖 API 服务器：`https://v3-feature.blacklake.cn/api`

#### 认证配置

认证信息配置在 `services/core.js` 文件顶部的 `AUTH_CONFIG` 对象中：

```javascript
const AUTH_CONFIG = {
  // 登录信息
  login: {
    type: 1,
    username: "cyy",
    code: "67768820",
    password:
      "794db7135639d5b59cd7e53b325f36a8f24fb906e95e403c49e89b99046654fae36a940c1e8496b75b9e69d4f79022c9123aa0d8cd665e2ea9cf584242a702664e77fdd2399c452bd03a174a3edbb41b86a406851b4da8b11b8faa7044925e3e9bffd4fd5afb14c70f592a2114ce5f45cf567e2e1f0d8688ef345ca28c2687c5",
  },
  // AppKey（用于获取user-access token）
  appKey: "cli_1764100796489835",
};
```

**重要**: 修改登录账号时，需要同时更新 `appKey`。

#### 认证流程

系统采用三级认证机制：

1. **优先级 1**: URL 参数中的 `code`

   ```
   https://your-domain.com/report?code=your_code_here
   ```

   如果 URL 中存在 `code`，直接使用该 `code` 获取 user-access token。

2. **优先级 2**: 自动登录流程
   - 调用登录接口获取登录 token
   - 使用登录 token 获取 `code`
   - 使用 `code` 和 `appKey` 获取 user-access token

所有业务接口使用 `X-AUTH` 头部，值为 user-access token。

## 使用说明

### 页面流程

1. **初始状态**: 显示"先选择工单"提示
2. **选择工单**: 点击工单区域，弹出工单列表
3. **搜索工单**: 在搜索框中输入工单编号进行过滤（支持防抖）
4. **确认选择**: 选中工单后点击"确定"
5. **查看物料**: 显示工单关联的物料信息和生产统计
6. **填写报工**: 录入数量、辅助数量
7. **提交报工**: 点击"提交"按钮完成报工

### 报工流程详解

根据 `api_doc/接口说明.txt`，报工流程如下：

1. **用户选择工单**: 从工单列表中选择一个工单
2. **显示物料信息**: 系统从工单数据中直接提取物料信息并展示
3. **用户填写报工数据**: 填写数量、辅助数量等信息
4. **构建报工参数**:
   - 通过工单 ID 获取生产任务列表
   - 从生产任务中获取 `taskId` 和 `executorIds`
   - 通过 `taskId` 获取报工物料列表
   - 选择主产出物料或第一个可用物料
   - 构建 `progressReportMaterial` 对象
5. **提交报工**: 调用批量报工接口提交数据
6. **清空表单**: 提交成功后清空表单，准备下次报工

## API 接口

详细的接口文档请参考 `api_doc` 目录下的 JSON 文件。以下是主要接口概览：

### 认证相关

#### 1. 登录接口

```
POST https://v3-feature.blacklake.cn/api/user/domain/web/v1/login
Content-Type: application/json

Body: {
    "type": 1,
    "username": "cyy",
    "code": "67768820",
    "password": "..."
}
```

#### 2. 获取 code

```
POST https://v3-feature.blacklake.cn/api/openapiadmin/domain/web/v1/access_token/_code
Headers: X-AUTH: {login_token}
```

#### 3. 获取 user-access token

```
POST https://v3-feature.blacklake.cn/api/openapi/domain/api/v1/access_token/_get_user_token_for_customized
Content-Type: application/json

Body: {
    "code": "...",
    "appKey": "cli_1764100796489835"
}
```

### 工单相关

#### 获取工单列表

```
POST /openapi/domain/web/v1/route/med/open/v2/work_order/base/_list
Headers: X-AUTH: {user_access_token}
Content-Type: application/json

Body: {
    "page": 1,
    "size": 50,
    "workOrderCode": "工单编号（可选，模糊查询）",
    "exactWorkOrderCode": "工单编号（可选，精确查询）",
    "workOrderStatusList": [状态列表],
    "pauseFlag": 0
}
```

详细接口文档：`api_doc/工单列表_BLACKLAKE-1686655055663532 (1).json`

### 报工相关

#### 1. 获取生产任务列表

```
POST /openapi/domain/web/v1/route/mfg/open/v1/produce_task/_list
Headers: X-AUTH: {user_access_token}
Content-Type: application/json

Body: {
    "page": 1,
    "size": 10,
    "workOrderIdList": [工单ID列表]
}
```

详细接口文档：`api_doc/生产任务列表_BLACKLAKE-1681109889053785 (3).json`

#### 2. 获取报工物料列表

```
POST /openapi/domain/web/v1/route/mfg/open/v1/progress_report/_list_progress_report_materials
Headers: X-AUTH: {user_access_token}
Content-Type: application/json

Body: {
    "taskId": 生产任务ID
}
```

详细接口文档：`api_doc/报工物料列表_BLACKLAKE-1681369551143844.json`

#### 3. 批量报工

```
POST /openapi/domain/web/v1/route/mfg/open/v1/progress_report/_progress_report
Headers: X-AUTH: {user_access_token}
Content-Type: application/json

Body: {
    "taskId": 生产任务ID,
    "progressReportMaterial": {
        "lineId": 物料行ID,
        "materialId": 物料ID,
        "reportProcessId": 报工工序ID
    },
    "qcStatus": 1,
    "reportType": 2,
    "progressReportItems": [{
        "executorIds": [执行人ID列表],
        "progressReportMaterialItems": [{
            "reportAmount": 报工数量,
            "reportUnitId": 报工单位ID,
            "remark": "备注（可选）"
        }]
    }],
    "storageLocationId": 1716848012872791
}
```

详细接口文档：`api_doc/批量报工_BLACKLAKE-1681109889053798 (1).json`

#### 4. 查询报工记录

```
POST /openapi/domain/web/v1/route/mfg/open/v1/progress_report/_list
Headers: X-AUTH: {user_access_token}
Content-Type: application/json

Body: {
    "page": 1,
    "size": 20,
    "taskIds": [生产任务ID列表],
    "workOrderIdList": [工单ID列表],
    "reportTimeFrom": 报工时间起始（时间戳，可选）,
    "reportTimeTo": 报工时间结束（时间戳，可选）,
    "processIdList": [工序ID列表，可选],
    "executorIdList": [执行人ID列表，可选],
    "qcStatusList": [质量状态列表，可选]
}
```

详细接口文档：`api_doc/报工记录列表_BLACKLAKE-1681109889053794.json`

## 代码结构说明

### services/core.js

包含核心服务：

- **ApiService**: HTTP 请求封装，自动处理认证、错误重试
- **AuthService**: 认证服务，处理 token 获取、存储和管理
  - `init()`: 初始化认证流程
  - `getUserAccessTokenByCode(code)`: 使用 code 获取 user-access token
  - `getCodeByLoginToken(loginToken)`: 使用登录 token 获取 code
  - `loginAndGetToken()`: 登录并获取登录 token

### services/business.js

包含业务服务：

- **WorkOrderService**: 工单相关 API

  - `getWorkOrderList(params)`: 获取工单列表
  - `processWorkOrderListResponse(response)`: 处理工单列表响应
  - `getMockWorkOrderList(params)`: 获取模拟工单数据（网络错误时使用）

- **ReportService**: 报工相关 API
  - `getReportRequiredParams(params)`: 获取报工必填参数
  - `buildReportRequestParams(requiredParams, formData)`: 构建报工请求参数
  - `submitReport(reportData)`: 提交报工
  - `getReportRecordsByTask(params)`: 查询报工记录

### js/common.js

包含通用代码：

- **工具函数**:

  - `getUrlParameter(name)`: 获取 URL 参数
  - `debounce(func, wait)`: 防抖函数
  - `showToast(message, type, duration)`: 显示 Toast 消息
  - `validateForm(data, rules)`: 表单验证
  - `deepClone(obj)`: 深拷贝对象

- **UI 组件**:
  - `HeaderNav`: 顶部导航栏
  - `WorkOrderSelector`: 工单选择器
  - `WorkOrderDropdown`: 工单下拉菜单（支持搜索）
  - `MaterialInfo`: 物料信息展示
  - `ReportForm`: 报工表单

### js/main.js

主应用逻辑：

- **MainApp**: 应用主类
  - 管理应用状态
  - 初始化组件
  - 处理用户交互
  - 协调各个服务

## 开发指南

### 修改登录账号

1. 打开 `services/core.js`
2. 修改 `AUTH_CONFIG` 对象中的登录信息
3. **重要**: 同时更新 `appKey`（不同账号对应不同的 appKey）

### 自定义主题色

主题色配置在 `css/custom.css` 中：

```css
:root {
  --primary-color: #02b980;
  --primary-hover: #029968;
  --primary-light: #e6f7f1;
  --primary-dark: #016b4a;
  --primary-shadow: rgba(2, 185, 128, 0.1);
}
```

修改这些变量即可改变整个应用的主题色。

### 添加新功能

1. **新增 API 接口**: 在 `services/business.js` 中对应的服务类中添加方法
2. **新增 UI 组件**: 在 `js/common.js` 中定义组件类
3. **集成到主应用**: 在 `js/main.js` 中集成新功能

### 调试模式

在开发环境下，所有服务都会暴露到全局，方便调试：

```javascript
// 在浏览器控制台中使用
MainApp.getState(); // 获取应用状态
authService.getToken(); // 获取当前token
workOrderService.getWorkOrderList(); // 获取工单列表
reportService.submitReport(); // 提交报工
```

## 部署说明

### 生产环境部署

1. **配置 API 地址**: 修改 `services/core.js` 中 `ApiService.getBaseURL()` 方法
2. **配置认证信息**: 修改 `services/core.js` 中的 `AUTH_CONFIG`
3. **上传文件**: 将整个 `web/report` 目录上传到服务器
4. **配置服务器**: 确保服务器支持静态文件服务
5. **HTTPS**: 建议使用 HTTPS 协议，特别是在移动端

### Nginx 配置示例

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /path/to/web/report;
    index index.html;

    # API代理（如果需要）
    location /api/ {
        proxy_pass https://v3-feature.blacklake.cn/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    # 静态文件
    location / {
        try_files $uri $uri/ /index.html;
    }

    # 缓存静态资源
    location ~* \.(js|css|png|jpg|jpeg|gif|ico|svg)$ {
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

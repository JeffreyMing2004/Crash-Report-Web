# MC Crash Analyzer — 前端

AI 驱动的 Minecraft 崩溃报告分析工具前端，基于 Vue 3 + Vite 构建。

## 技术栈

| 类别 | 技术 |
|------|------|
| 框架 | Vue 3.5（Composition API） |
| 构建 | Vite 5 |
| 路由 | Vue Router 4 |
| HTTP | Axios |
| 字体 | JetBrains Mono + IBM Plex Sans |
| 图标 | Lucide 风格 SVG（`h()` 渲染函数） |

## 项目结构

```
client/
├── index.html                 # 入口 HTML
├── package.json
├── vite.config.js             # Vite 配置（含 API 代理）
└── src/
    ├── main.js                # 应用入口，挂载 Vue + Router
    ├── App.vue                # 根组件（Header / Main / Footer / Auth）
    ├── api/
    │   └── index.js           # API 封装（分析、历史、认证、分享）
    ├── assets/
    │   ├── style.css          # 全局样式（OLED 暗色主题）
    │   └── icons.js           # SVG 图标组件（Lucide 风格）
    ├── components/
    │   ├── AuthModal.vue      # 登录/注册弹窗
    │   ├── CrashUploader.vue  # 文件拖拽上传
    │   ├── AnalysisResult.vue # AI 分析结果卡片
    │   └── HistoryList.vue    # 历史分析记录列表
    ├── router/
    │   └── index.js           # 路由配置
    └── views/
        ├── Home.vue           # 首页（上传/粘贴/分析/历史）
        └── SharedReport.vue   # 分享报告查看页
```

## 路由

| 路径 | 页面 | 说明 |
|------|------|------|
| `/` | Home | 首页，上传/粘贴崩溃报告进行分析 |
| `/crash/:shareId` | SharedReport | 查看他人分享的报告 |

## 快速开始

### 前置要求

- Node.js >= 18
- 后端服务运行在 `http://localhost:3000`（提供 `/api/*` 接口）

### 安装与运行

```bash
# 安装依赖
npm install

# 启动开发服务器（默认 http://localhost:5173）
npm run dev

# 生产构建
npm run build

# 预览生产构建
npm run preview
```

### API 代理

开发模式下，Vite 自动将 `/api/*` 请求代理到后端 `http://localhost:3000`，无需额外配置 CORS。

## 功能

- **上传分析** — 拖拽或选择 `.txt` / `.log` 文件上传崩溃报告
- **粘贴分析** — 直接粘贴崩溃报告文本进行分析
- **AI 分析结果** — 展示错误类型、严重程度、根本原因、解决方案
- **历史记录** — 查看、回顾、删除过往分析记录
- **分享报告** — 生成分享链接，他人可查看分析结果
- **用户系统** — 登录/注册（含微信扫码登录）

## 设计系统

采用 OLED 暗色主题 + 块状布局：

- **背景** `#020617`，卡片 `#1E293B`
- **主色调** `#22C55E`（代码绿），带辉光效果
- **标题字体** JetBrains Mono（等宽），**正文字体** IBM Plex Sans
- **图标** 全部使用 SVG 矢量图标，无 emoji
- **响应式** 适配 375px ~ 1440px

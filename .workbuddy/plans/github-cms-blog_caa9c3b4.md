---
name: github-cms-blog
overview: 为博客添加 GitHub API 后台管理系统，支持实时编辑文章、标签、个人信息，所有变更直接推送到 GitHub
todos:
  - id: create-github-api
    content: 创建 GitHub API 服务 (src/services/githubApi.ts)
    status: completed
  - id: modify-posts-data
    content: 修改 posts.ts 导出读写接口
    status: completed
    dependencies:
      - create-github-api
  - id: create-github-data
    content: 创建 GitHub 数据提供者 (src/data/githubData.ts)
    status: completed
    dependencies:
      - modify-posts-data
  - id: enhance-admin-page
    content: 增强 AdminPage 添加 Token 配置、关于页面编辑、同步功能
    status: completed
    dependencies:
      - create-github-data
  - id: test-workflow
    content: 测试完整流程
    status: completed
    dependencies:
      - enhance-admin-page
---

## 用户需求

为博客添加一个可以通过浏览器直接管理所有内容的后台系统，包括：文章管理（增删改）、标签管理、关于页面修改。数据存储使用 GitHub API 直接读写仓库中的 posts.ts 和 admin.ts 文件。

## 现有系统

- 技术栈：React 19 + TypeScript + Tailwind + Vite
- 仓库：qinxiushen/gerenboke
- 数据存储：src/data/posts.ts（静态 TypeScript 文件）
- 已有 AdminPage：仅支持 localStorage 操作

## 核心功能

1. GitHub API 认证（使用 Personal Access Token）
2. 后台配置 GitHub Token 并安全存储
3. 文章 CRUD 操作通过 GitHub API 写入 posts.ts
4. 标签管理同步到 posts.ts
5. 关于页面内容管理
6. 一键发布（修改后自动触发 GitHub Actions 重建）

## 技术方案

### 架构

```
用户浏览器
    ↓
AdminPage (配置 GitHub Token)
    ↓
GitHub API Service (读写仓库文件)
    ↓
GitHub API (https://api.github.com)
    ↓
qinxiushen/gerenboke 仓库
```

### 实现步骤

1. **创建 GitHub API 服务** (`src/services/githubApi.ts`)

- 使用 Fetch API 调用 GitHub REST API
- 获取/更新文件 SHA（需要知道文件 SHA 才能更新）
- Base64 编解码文件内容

2. **修改数据层** (`src/data/posts.ts`)

- 保留静态数据作为默认值
- 导出读写函数接口

3. **创建 GitHub 数据提供者** (`src/data/githubData.ts`)

- 实现与 localStorage 相同的接口
- 通过 GitHub API 读写 posts.ts

4. **增强 AdminPage**

- 添加 Token 配置界面
- 添加关于页面编辑
- 添加同步状态提示
- 添加"立即发布"按钮（触发 workflow_dispatch）

### GitHub API 端点

- 获取文件：`GET /repos/{owner}/{repo}/contents/{path}`
- 更新文件：`PUT /repos/{owner}/{repo}/contents/{path}`
- 触发 Actions：`POST /repos/{owner}/{repo}/actions/workflows/deploy.yml/dispatches`

### 安全考虑

- GitHub Token 存储在 localStorage（仅本地使用）
- 不暴露 Token 在代码中
- Token 仅用于读写自己的仓库
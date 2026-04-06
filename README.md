# Blog Workflow

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)

一个可复用的 GitHub Workflow，让任何 Markdown 仓库都能一键发布为博客站点。

## 简介

这个项目提供了一个可复用的 GitHub Workflow，让你只需要写 Markdown，就能自动构建并部署一个漂亮的博客到 GitHub Pages。

基于 [blog-generator](https://github.com/AlphaZx-CJY/blog-generator) 构建，支持：

- ✅ Markdown + YAML 前言写作
- ✅ 代码高亮
- ✅ 标签、分类、归档
- ✅ 目录导航、阅读进度条
- ✅ 深色/浅色主题
- ✅ 本地搜索
- ✅ Giscus 评论系统
- ✅ 移动端适配

## 快速开始

### 1. 创建你的博客仓库

新建一个 GitHub 仓库，添加你的 Markdown 文章：

```
my-blog/
├── .github/
│   └── workflows/
│       └── deploy.yml      # 部署配置
├── hello-world.md          # 你的文章
└── blog.config.yml         # 博客配置（可选）
```

### 2. 添加 Workflow 文件

创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy Blog

on:
  push:
    branches: [main]
  workflow_dispatch:

permissions:
  contents: read
  pages: write
  id-token: write

concurrency:
  group: "pages"
  cancel-in-progress: false

jobs:
  deploy:
    uses: AlphaZx/blog-workflow/.github/workflows/publish-blog.yml@main
    secrets:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### 3. 开启 GitHub Pages

进入仓库 **Settings → Pages**：
- Source: GitHub Actions

### 4. 提交并推送

```bash
git add .
git commit -m "Initial blog setup"
git push origin main
```

稍等片刻，你的博客就会部署到 `https://yourusername.github.io/my-blog/`

## 写作

### Markdown 文章格式

```markdown
---
title: 文章标题
date: 2026-04-06
tags: ["技术", "随笔"]
category: 技术
summary: 文章摘要（可选）
---

正文内容，支持 **Markdown** 语法。

```javascript
console.log('Hello World');
```
```

### 支持的 YAML 字段

| 字段 | 说明 | 必填 |
|------|------|------|
| `title` | 文章标题 | ✅ |
| `date` | 发布日期 | ✅ |
| `tags` | 标签数组 | ❌ |
| `category` | 分类 | ❌ |
| `summary` | 摘要 | ❌ |
| `published` | 是否发布（默认 true）| ❌ |
| `origin` | 原创/转载（original/repost）| ❌ |
| `source` | 转载来源链接 | ❌ |

## 配置博客

创建 `blog.config.yml` 自定义你的博客：

```yaml
# 博客标题
title: "我的技术博客"

# 关于我（可选）
about:
  avatar: "/images/avatar.png"
  description: "热爱技术的开发者，分享编程心得与技术思考。"

# 友情链接（可选）
friends:
  - name: "GitHub"
    url: "https://github.com"
  - name: "Stack Overflow"
    url: "https://stackoverflow.com"

# 社交链接（可选）
social:
  github: "https://github.com/username"
  email: "email@example.com"
  twitter: "https://twitter.com/username"
  rss: "/feed.xml"

# Giscus 评论（可选）
comments:
  provider: "giscus"
  repo: "username/blog-comments"
  repoId: "R_kgDOxxxxxx"
  category: "Announcements"
  categoryId: "DIC_kwDOxxxxxx"
```

## Workflow 参数

你可以自定义 workflow 的输入参数：

```yaml
jobs:
  deploy:
    uses: AlphaZx/blog-workflow/.github/workflows/publish-blog.yml@main
    with:
      node-version: '20'              # Node.js 版本
      posts-path: 'posts'             # Markdown 文件目录
      config-path: 'blog.config.yml'  # 配置文件路径
      blog-generator-version: 'main'  # blog-generator 版本
    secrets:
      GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```

### 参数说明

| 参数 | 类型 | 默认值 | 说明 |
|------|------|--------|------|
| `node-version` | string | `20` | Node.js 版本 |
| `posts-path` | string | `.` | Markdown 文件所在目录（相对仓库根目录）|
| `config-path` | string | `blog.config.yml` | 配置文件路径 |
| `blog-generator-version` | string | `main` | blog-generator 的版本/分支 |

## 目录结构示例

### 简单结构

```
my-blog/
├── .github/workflows/deploy.yml
├── blog.config.yml
├── hello-world.md
├── my-second-post.md
└── images/
    └── avatar.png
```

### 使用子目录管理文章

```
my-blog/
├── .github/workflows/deploy.yml
├── blog.config.yml
├── posts/                    # 文章放在 posts/ 目录
│   ├── 2024/
│   │   └── hello-world.md
│   └── 2025/
│       └── my-new-post.md
└── images/
    └── avatar.png
```

对应的 workflow：

```yaml
with:
  posts-path: 'posts'
```

### 多目录结构

workflow 会自动收集所有子目录中的 `.md` 文件，你可以自由组织：

```
my-blog/
├── .github/workflows/deploy.yml
├── blog.config.yml
├── tech/
│   ├── javascript-tips.md
│   └── typescript-guide.md
├── life/
│   └── travel-diary.md
└── images/
    └── avatar.png
```

## 图片和静态资源

将图片放在 `images/`、`assets/`、`static/` 或 `public/` 目录下：

```
my-blog/
├── images/
│   ├── avatar.png
│   └── posts/
│       └── screenshot.png
└── hello.md
```

在 Markdown 中引用：

```markdown
![截图](/images/posts/screenshot.png)
```

## 本地预览

如果你想在本地预览博客：

```bash
# 克隆 blog-generator
git clone https://github.com/AlphaZx-CJY/blog-generator.git
cd blog-generator

# 安装依赖
npm install

# 将你的文章复制到 content/posts/
cp /path/to/your/posts/*.md content/posts/

# 开发模式
npm run dev
```

访问 http://localhost:3000

## 自定义主题

目前不支持自定义主题样式。如需修改样式，建议直接 fork [blog-generator](https://github.com/AlphaZx-CJY/blog-generator) 并自行修改 CSS。

## 故障排查

### 部署失败

1. 检查 GitHub Pages 是否已开启（Settings → Pages → Source: GitHub Actions）
2. 检查 workflow 权限是否正确设置
3. 查看 Actions 日志获取详细错误信息

### 文章未显示

1. 确认 Markdown 文件有正确的 YAML 前言（`---` 包裹的部分）
2. 确认 `date` 格式正确（如 `2026-04-06`）
3. 检查文章是否被标记为 `published: false`

### 样式或脚本未加载

确保 `_config.yml` 中没有设置 `baseurl`（GitHub Pages 用户站点不需要）。

## 贡献

欢迎提交 Issue 和 Pull Request！

## 许可证

MIT License

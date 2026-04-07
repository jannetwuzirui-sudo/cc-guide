---
layout: ../../layouts/MainLayout.astro
title: GitHub Pages 部署
---

# GitHub Pages 部署

GitHub Pages 是最适合自由职业者的免费静态网站托管方案。零成本、自动部署、支持自定义域名。

## 一、GitHub Pages 简介

GitHub Pages 可以直接从 GitHub 仓库托管静态网站，支持 HTML、CSS、JavaScript 以及各种静态站点生成器（Astro、Hugo、Jekyll 等）。

免费额度：
- 仓库大小限制：1 GB
- 带宽限制：每月 100 GB
- 构建次数：每小时 10 次

对于大多数客户网站来说完全够用。

## 二、创建仓库并推送代码

### 步骤 1：在 GitHub 创建新仓库

```bash
# 在本地项目目录初始化 Git
git init

# 添加所有文件
git add .

# 提交代码
git commit -m "初始提交"

# 添加远程仓库
git remote add origin https://github.com/你的用户名/项目名.git

# 推送到 GitHub
git push -u origin main
```

### 步骤 2：确认项目结构

确保你的构建产物在正确的目录中。对于纯 HTML 项目，文件直接放在根目录即可。对于框架项目，需要构建后部署 `dist` 目录。

## 三、开启 GitHub Pages

1. 进入仓库 → Settings → Pages
2. Source 选择 "GitHub Actions"（推荐）或 "Deploy from a branch"
3. 如果选择分支部署，选择 `main` 分支和 `/ (root)` 或 `/docs` 目录

## 四、使用 GitHub Actions 自动部署

推荐使用 GitHub Actions，每次推送代码自动构建部署。

在项目根目录创建 `.github/workflows/deploy.yml`：

```yaml
name: Deploy to GitHub Pages

on:
  push:
    branches: [main]

permissions:
  contents: read
  pages: write
  id-token: write

jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install dependencies
        run: npm install

      - name: Build
        run: npm run build

      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./dist

  deploy:
    needs: build
    runs-on: ubuntu-latest
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

推送这个文件后，GitHub 会自动运行工作流：

```bash
git add .github/workflows/deploy.yml
git commit -m "添加自动部署工作流"
git push
```

## 五、配置自定义域名

### 步骤 1：在 GitHub 设置域名

进入仓库 → Settings → Pages → Custom domain，输入你的域名（如 `www.example.com`）。

### 步骤 2：配置 DNS 记录

在你的域名服务商处添加以下记录：

使用 CNAME（推荐用于 `www` 子域名）：

```
主机记录：www
记录类型：CNAME
记录值：你的用户名.github.io
```

使用 A 记录（用于根域名）：

```
主机记录：@
记录类型：A
记录值：
  185.199.108.153
  185.199.109.153
  185.199.110.153
  185.199.111.153
```

### 步骤 3：添加 CNAME 文件

在项目的构建输出目录中添加 `CNAME` 文件，内容为你的域名：

```bash
echo "www.example.com" > public/CNAME
```

对于 Astro 项目，放在 `public/` 目录下，构建时会自动复制到 `dist/`。

### 步骤 4：开启 HTTPS

在 Settings → Pages 中勾选 "Enforce HTTPS"。GitHub 会自动申请 Let's Encrypt 证书。

## 六、常见问题

### 404 错误？

- 检查构建输出目录是否正确
- 确认 `index.html` 文件存在
- 如果是 SPA 应用，添加 `404.html` 作为回退页面

### 自定义域名不生效？

- DNS 解析需要时间，等待几分钟到几小时
- 确认 CNAME 文件内容正确
- 在 Settings → Pages 中检查域名状态

### 构建失败？

```bash
# 本地先测试构建
npm run build

# 查看 GitHub Actions 日志
# 进入仓库 → Actions → 点击失败的工作流查看详情
```

### 部署后样式丢失？

检查项目的 `base` 配置。如果仓库名不是 `username.github.io`，需要设置 base path：

```javascript
// astro.config.mjs 示例
export default defineConfig({
  site: 'https://你的用户名.github.io',
  base: '/仓库名',
});
```

## 七、交付建议

1. 帮客户创建 GitHub 账号或使用客户自己的账号
2. 配置好 Actions 后，后续更新只需 `git push`
3. 提供简单的更新文档给客户
4. GitHub Pages 适合展示型网站、作品集、落地页等静态站点

GitHub Pages 是零成本起步的最佳选择，非常适合预算有限的客户项目。

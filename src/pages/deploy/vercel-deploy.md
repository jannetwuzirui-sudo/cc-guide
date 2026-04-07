---
layout: ../../layouts/MainLayout.astro
title: Vercel 部署
---

# Vercel 部署

Vercel 是前端项目部署的首选平台之一，特别适合 Next.js、Astro、Nuxt 等框架。自动部署、预览环境、全球 CDN，免费额度对个人和小型项目非常友好。

## 一、Vercel 简介

Vercel 的核心优势：

- 连接 GitHub 仓库后自动部署，推送即上线
- 每个 Pull Request 自动生成预览链接
- 全球边缘网络，访问速度快
- 免费 SSL 证书
- Hobby 计划免费，足够应付大多数客户项目

免费额度（Hobby 计划）：
- 每月 100 GB 带宽
- 每天 100 次部署
- 支持自定义域名

## 二、注册与连接仓库

### 步骤 1：注册 Vercel 账号

访问 `https://vercel.com`，推荐使用 GitHub 账号直接登录。

### 步骤 2：导入项目

1. 点击 "Add New..." → "Project"
2. 选择你的 GitHub 仓库
3. Vercel 会自动检测框架类型并配置构建命令

### 步骤 3：确认构建设置

Vercel 通常能自动识别项目类型，但你也可以手动调整：

| 设置项 | 说明 | 示例 |
|--------|------|------|
| Framework Preset | 框架类型 | Astro / Next.js / Other |
| Build Command | 构建命令 | `npm run build` |
| Output Directory | 输出目录 | `dist`（Astro）/ `.next`（Next.js） |
| Install Command | 安装命令 | `npm install` |

点击 "Deploy" 开始首次部署。

## 三、环境变量配置

如果项目需要环境变量（API 密钥、数据库连接等）：

1. 进入项目 → Settings → Environment Variables
2. 添加键值对，选择生效环境（Production / Preview / Development）

```
KEY=你的API密钥
DATABASE_URL=你的数据库连接地址
```

注意事项：
- 敏感信息绝对不要提交到代码仓库
- Vercel 的环境变量会在构建时注入
- 修改环境变量后需要重新部署才能生效

## 四、自定义域名

### 步骤 1：在 Vercel 添加域名

进入项目 → Settings → Domains，输入你的域名。

### 步骤 2：配置 DNS

Vercel 会提示你需要添加的 DNS 记录：

使用 CNAME（推荐）：

```
主机记录：www
记录类型：CNAME
记录值：cname.vercel-dns.com
```

使用 A 记录（根域名）：

```
主机记录：@
记录类型：A
记录值：76.76.21.21
```

### 步骤 3：等待验证

添加 DNS 记录后，Vercel 会自动验证并签发 SSL 证书。通常几分钟内完成。

## 五、预览部署

Vercel 的预览部署功能非常实用：

- 每次向非主分支推送代码，Vercel 自动生成一个预览链接
- 预览链接格式：`项目名-分支名-用户名.vercel.app`
- 可以把预览链接发给客户确认效果，确认后再合并到主分支上线

这个功能在与客户协作时特别有价值，客户可以在正式上线前预览修改效果。

## 六、使用 Vercel CLI

除了网页操作，也可以用命令行部署：

```bash
# 安装 Vercel CLI
npm i -g vercel

# 登录
vercel login

# 部署（首次会引导配置）
vercel

# 部署到生产环境
vercel --prod

# 查看部署列表
vercel ls
```

CLI 适合快速测试部署，不需要先推送到 GitHub。

## 七、常见问题

### 构建失败？

- 在本地先运行 `npm run build` 确认能正常构建
- 检查 Node.js 版本是否匹配（Settings → General → Node.js Version）
- 查看 Vercel 部署日志中的错误信息

### 部署成功但页面空白？

- 检查 Output Directory 设置是否正确
- 确认构建产物中有 `index.html`

### 域名配置后无法访问？

- 等待 DNS 生效（几分钟到几小时）
- 确认 DNS 记录填写正确
- 在 Vercel Domains 页面查看验证状态

## 八、交付建议

1. 推荐客户使用 Vercel 的 Hobby 计划，免费且够用
2. 如果客户项目流量较大，可以升级到 Pro 计划
3. 利用预览部署功能与客户沟通修改
4. Vercel 特别适合前端框架项目和 Jamstack 架构

Vercel 让部署变得极其简单，是自由职业者交付前端项目的利器。

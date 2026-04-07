---
layout: ../../layouts/MainLayout.astro
title: 5分钟快速上手
---

# 5分钟快速上手 Claude Code

本篇将带你从零开始，用最短的时间跑通 Claude Code 的核心流程，让你立刻具备接单开发的基本能力。

## 第一步：安装 Claude Code

确保你的系统已安装 Node.js 18 或更高版本，然后在终端执行：

```bash
npm install -g @anthropic-ai/claude-code
```

安装完成后，验证是否成功：

```bash
claude --version
```

看到版本号输出，说明安装没问题。

## 第二步：获取 API Key

1. 前往 [Anthropic Console](https://console.anthropic.com/) 注册账号
2. 进入 **API Keys** 页面，点击 **Create Key**
3. 复制生成的密钥，妥善保存

在终端中配置你的 API Key：

```bash
export ANTHROPIC_API_KEY="sk-ant-xxxxx"
```

建议将这行写入你的 `~/.zshrc` 或 `~/.bashrc`，避免每次都要手动设置：

```bash
echo 'export ANTHROPIC_API_KEY="sk-ant-xxxxx"' >> ~/.zshrc
source ~/.zshrc
```

## 第三步：开启第一次对话

进入你的项目目录，启动 Claude Code：

```bash
cd ~/my-project
claude
```

你会看到一个交互式终端界面。试着输入你的第一个指令：

```
帮我分析一下这个项目的目录结构，给出简要说明
```

Claude Code 会自动读取项目文件，给你一份清晰的项目概览。

## 第四步：掌握基本工作流

作为自由职业者，你的日常工作流大致如下：

### 接到新项目时

```bash
cd ~/client-project
claude
# 进入后输入：
> /init
```

`/init` 会自动生成 `CLAUDE.md` 文件，记录项目的关键信息，方便后续每次对话都能快速进入状态。

### 开发功能时

```bash
claude
> 根据 docs/requirements.md 中的需求，实现用户登录功能
```

Claude Code 会阅读需求文档，自动编写代码、创建文件，你只需要审查和确认。

### 代码审查时

```bash
claude
> /review
```

一键审查当前改动，帮你在提交给客户之前发现潜在问题。

### 提交代码时

```bash
claude
> 帮我提交这次的改动，commit message 用英文，描述清楚改了什么
```

## 实用小贴士

- **先跑 `/init`**：每个新项目第一件事就是初始化，让 Claude Code 了解项目背景
- **善用 `/compact`**：对话太长时执行压缩，节省 token 开销，降低接单成本
- **查看花费**：随时输入 `/cost` 查看当前会话消耗了多少费用，做好成本控制
- **保持项目目录干净**：在项目根目录启动 Claude Code 效果最好

## 下一步

恭喜你完成了快速上手！接下来建议阅读：

- [三种模式详解](/basics/three-modes) — 了解不同场景下该用哪种模式
- [核心 Slash 命令精选](/basics/slash-commands) — 掌握提升效率的关键命令
- [快捷键速查](/basics/shortcuts) — 让你的操作更加流畅

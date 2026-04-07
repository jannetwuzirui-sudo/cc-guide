---
layout: ../../layouts/MainLayout.astro
title: 实战：小型Web应用
---

# 实战：小型Web应用

> 接单价格区间：2000-8000 元 | 开发周期：3-10 天 | 难度：⭐⭐⭐⭐

小型 Web 应用是利润最高的接单类型，但也对技术能力要求最高。本篇以一个「表单提交管理系统」为例，演示如何用 Claude Code 完成全栈开发。

---

## 一、需求分析

### 典型客户需求

客户是一家培训机构，需要一个在线报名管理系统：

- 前台：学员填写报名表单（姓名、电话、课程选择、备注）
- 后台：管理员查看、筛选、导出报名数据
- 管理员登录认证
- 数据统计仪表盘（每日报名量、课程分布图表）
- 报名成功后发送短信/邮件通知
- 支持多个表单模板

### 技术栈选择

| 方案 | 前端 | 后端 | 数据库 | 适用场景 |
|------|------|------|--------|---------|
| 轻量方案 | HTML + Alpine.js | Node.js + Express | SQLite | 小数据量，快速交付 |
| 标准方案 | React/Vue | Node.js + Express | PostgreSQL | 中等规模，功能完整 |
| 全栈框架 | Next.js | Next.js API Routes | PostgreSQL | 前后端统一，开发效率高 |

本案例选用 Next.js + PostgreSQL + Prisma 的全栈方案。

---

## 二、Claude Code 提示词

### 第一步：项目初始化

```
用 Next.js 14 (App Router) 创建一个表单管理系统，要求：
1. 使用 TypeScript
2. 使用 TailwindCSS + shadcn/ui 组件库
3. 使用 Prisma 作为 ORM，PostgreSQL 数据库
4. 使用 NextAuth.js 做管理员认证
5. 创建基础的数据库模型：
   - User（管理员）
   - Form（表单模板）
   - Submission（提交记录）
```

### 第二步：前台表单页面

```
创建前台报名表单页面：
1. 根据 Form 模板动态渲染表单字段
2. 表单验证（必填项、手机号格式、邮箱格式）
3. 提交后显示成功页面
4. 防重复提交（按钮 loading 状态 + 接口防抖）
5. 手机端适配
6. 表单字段类型支持：文本、下拉选择、单选、多选、日期、文件上传
```

### 第三步：后台管理

```
创建后台管理系统：
1. 登录页面（邮箱 + 密码）
2. 仪表盘首页：
   - 今日/本周/本月提交数量卡片
   - 最近 7 天提交趋势折线图（用 Recharts）
   - 课程分布饼图
3. 提交记录列表：
   - 表格展示，支持分页
   - 按日期、课程、状态筛选
   - 搜索功能（按姓名或手机号）
   - 批量导出 Excel
   - 单条记录详情查看
4. 表单模板管理：
   - 创建/编辑/删除表单模板
   - 拖拽排序表单字段
```

### 第四步：通知和安全

```
添加以下功能：
1. 报名成功后发送邮件通知（使用 Resend 或 Nodemailer）
2. 接口限流（防止恶意刷表单）
3. CSRF 防护
4. 输入内容 XSS 过滤
5. 敏感数据（手机号）脱敏显示
6. 操作日志记录
```

---

## 三、开发过程

### 项目结构

```
form-system/
├── prisma/
│   ├── schema.prisma
│   └── seed.ts
├── src/
│   ├── app/
│   │   ├── (public)/          # 前台页面
│   │   │   └── form/[id]/
│   │   ├── (admin)/           # 后台页面
│   │   │   ├── dashboard/
│   │   │   ├── submissions/
│   │   │   └── forms/
│   │   └── api/               # API 路由
│   │       ├── auth/
│   │       ├── submissions/
│   │       └── forms/
│   ├── components/
│   │   ├── ui/                # shadcn 组件
│   │   ├── FormRenderer.tsx
│   │   ├── DataTable.tsx
│   │   └── Charts.tsx
│   ├── lib/
│   │   ├── prisma.ts
│   │   ├── auth.ts
│   │   └── utils.ts
│   └── types/
├── .env
├── package.json
└── next.config.js
```

### 数据库设计

```prisma
model Form {
  id          String       @id @default(cuid())
  title       String
  fields      Json         // 表单字段配置
  status      FormStatus   @default(ACTIVE)
  submissions Submission[]
  createdAt   DateTime     @default(now())
}

model Submission {
  id        String   @id @default(cuid())
  formId    String
  form      Form     @relation(fields: [formId], references: [id])
  data      Json     // 提交的表单数据
  status    Status   @default(PENDING)
  createdAt DateTime @default(now())
}
```

### 开发顺序

1. 数据库模型和 API 接口
2. 管理员认证流程
3. 前台表单渲染和提交
4. 后台列表和筛选
5. 数据统计图表
6. 导出和通知功能
7. 安全加固和测试

### 常见问题处理

- 文件上传 → 用阿里云 OSS 或腾讯云 COS，不要存本地
- 大量数据导出 → 后端流式生成 Excel，避免内存溢出
- 并发提交 → 数据库层面做唯一约束，接口做幂等处理
- 部署环境变量 → 数据库连接串、密钥等用环境变量管理

---

## 四、部署上线

### 推荐部署方案

```
帮我编写 Docker 部署配置：
1. Dockerfile（多阶段构建，优化镜像大小）
2. docker-compose.yml（包含 Next.js 应用 + PostgreSQL）
3. Nginx 反向代理配置
4. 自动备份数据库的 cron 脚本
```

### 部署方案对比

| 方案 | 月费用 | 适合场景 |
|------|--------|---------|
| Vercel + Supabase | 免费起 | 小规模，快速上线 |
| 轻量云服务器 + Docker | 50-100 元 | 中等规模，可控性强 |
| 阿里云函数计算 + RDS | 100-300 元 | 弹性伸缩，正式项目 |

### 上线检查清单

- [ ] 环境变量配置正确
- [ ] 数据库迁移执行完成
- [ ] HTTPS 证书配置
- [ ] 错误监控接入（Sentry）
- [ ] 数据库自动备份
- [ ] 日志收集配置

---

## 五、交付客户

### 交付物清单

- [ ] 完整项目源代码
- [ ] 线上系统地址和管理员账号
- [ ] 系统使用手册（后台操作说明）
- [ ] 部署文档（服务器信息、数据库信息）
- [ ] API 接口文档（如客户后续需要对接）

### 报价参考

| 项目内容 | 参考价格 |
|---------|---------|
| 基础表单 + 后台查看 | 2000-3000 元 |
| 含统计图表和导出 | 3000-5000 元 |
| 含通知和多模板 | 5000-6500 元 |
| 含权限管理和日志 | 6500-8000 元 |

### 维护和迭代

Web 应用的维护比静态站复杂，建议：

- 签订 3-6 个月的维护合同
- 月度维护费：300-500 元（含服务器监控、Bug 修复、小功能调整）
- 大功能迭代另行报价
- 提供 7x12 小时的故障响应

---

## 小结

小型 Web 应用是接单收入的主要来源。Claude Code 在全栈开发中的价值尤为突出——从数据库设计到前端交互，它能帮你快速生成大量样板代码。关键是把精力放在业务逻辑理解和系统架构设计上，这些是 AI 无法替代的核心竞争力。随着项目经验积累，你可以逐步提高报价，向 5000 元以上的项目迈进。

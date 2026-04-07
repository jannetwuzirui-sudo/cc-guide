---
layout: ../../layouts/MainLayout.astro
title: 域名购买与解析全流程
---

# 域名购买与解析全流程

作为自由职业者，给客户交付网站时，域名是绕不开的一环。这篇指南会带你走完从购买到解析的全部流程。

## 一、域名基础知识

域名就是网站的地址，比如 `example.com`。用户通过域名访问你部署好的网站。

常见的域名后缀：

- `.com` — 最通用，商业网站首选
- `.cn` — 中国国别域名，需要实名认证
- `.net` / `.org` — 也比较常见
- `.dev` / `.io` — 技术类项目常用

## 二、域名注册商推荐

### 1. Namesilo

- 官网：`https://www.namesilo.com`
- 优点：价格便宜，自带免费隐私保护，无套路续费
- 适合：个人项目、海外客户项目
- 注册流程：创建账号 → 搜索域名 → 加入购物车 → 支付（支持支付宝）

### 2. Cloudflare Registrar

- 官网：`https://dash.cloudflare.com`
- 优点：成本价销售（不赚差价），自带 CDN 和安全防护
- 适合：已经在用 Cloudflare 服务的用户
- 注册流程：注册 Cloudflare 账号 → 进入域名注册页面 → 搜索并购买

### 3. 阿里云（万网）

- 官网：`https://wanwang.aliyun.com`
- 优点：中文界面，客服支持好，`.cn` 域名方便备案
- 适合：国内客户项目、需要备案的网站
- 注册流程：注册阿里云账号 → 实名认证 → 搜索域名 → 购买

## 三、DNS 解析基础概念

购买域名后，需要通过 DNS（域名系统）把域名指向你的服务器或托管平台。

### 常用记录类型

| 记录类型 | 用途 | 示例 |
|---------|------|------|
| A 记录 | 将域名指向一个 IPv4 地址 | `example.com` → `192.168.1.1` |
| AAAA 记录 | 将域名指向一个 IPv6 地址 | `example.com` → `2001:db8::1` |
| CNAME 记录 | 将域名指向另一个域名 | `www.example.com` → `example.com` |
| TXT 记录 | 存储文本信息，常用于验证 | 域名所有权验证、SPF 记录 |
| MX 记录 | 邮件服务器指向 | 配置企业邮箱时使用 |

## 四、DNS 解析实操步骤

### 步骤 1：找到你的 DNS 管理面板

- Namesilo：登录后进入 Domain Manager → 点击域名 → DNS Records
- Cloudflare：登录后选择域名 → DNS → Records
- 阿里云：控制台 → 域名 → 解析设置

### 步骤 2：添加 A 记录

将根域名指向服务器 IP：

```
主机记录：@
记录类型：A
记录值：你的服务器 IP（如 123.45.67.89）
TTL：600（或默认值）
```

### 步骤 3：添加 CNAME 记录

将 `www` 子域名指向根域名：

```
主机记录：www
记录类型：CNAME
记录值：example.com
TTL：600（或默认值）
```

### 步骤 4：等待生效

DNS 解析修改后通常需要几分钟到 48 小时生效。大多数情况下 5-30 分钟即可。

可以用以下命令检查解析是否生效：

```bash
# 查询 A 记录
dig example.com A

# 查询 CNAME 记录
dig www.example.com CNAME

# Windows 用户使用 nslookup
nslookup example.com
```

## 五、不同部署平台的 DNS 配置

| 平台 | 记录类型 | 记录值 |
|------|---------|--------|
| GitHub Pages | CNAME | `username.github.io` |
| Vercel | CNAME | `cname.vercel-dns.com` |
| 自有服务器 | A | 服务器公网 IP |
| Cloudflare Pages | CNAME | `your-project.pages.dev` |

## 六、常见问题排查

### 解析不生效？

1. 确认 DNS 记录填写正确，没有多余的空格
2. 检查 TTL 设置，降低 TTL 可以加快生效
3. 清除本地 DNS 缓存：`sudo dscacheutil -flushcache`（macOS）
4. 使用 `https://dnschecker.org` 在线检查全球解析状态

### CNAME 和 A 记录冲突？

根域名（`@`）不能设置 CNAME 记录（部分服务商支持 CNAME 扁平化除外）。根域名用 A 记录，子域名用 CNAME。

### 域名转移注意事项

- 域名注册满 60 天后才能转移
- 转移前确保域名未被锁定（关闭 Transfer Lock）
- 获取转移授权码（Auth Code）
- 转移过程一般需要 5-7 天

## 七、给客户交付时的建议

1. 建议客户自己注册域名账号，你来协助配置
2. 如果代注册，务必用客户的信息进行实名认证
3. 交付时提供一份 DNS 配置文档，方便后续维护
4. 提醒客户注意域名续费时间，避免过期

掌握了域名购买和解析，你就打通了网站上线的第一步。接下来可以继续学习具体的部署方式。

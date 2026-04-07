---
layout: ../../layouts/MainLayout.astro
title: HTTPS/SSL 证书配置
---

# HTTPS/SSL 证书配置

HTTPS 是现代网站的标配。没有 HTTPS，浏览器会显示"不安全"警告，影响用户信任和 SEO 排名。这篇指南帮你在各种平台上配置 SSL 证书。

## 一、为什么需要 HTTPS

- 浏览器安全提示：Chrome 等浏览器会将 HTTP 网站标记为"不安全"
- SEO 加分：搜索引擎优先收录 HTTPS 网站
- 数据加密：保护用户数据在传输过程中不被窃取
- 客户信任：HTTPS 是专业网站的基本要求

## 二、SSL 证书类型

| 类型 | 说明 | 适合场景 |
|------|------|---------|
| DV（域名验证） | 只验证域名所有权，签发快 | 个人网站、博客、展示站 |
| OV（组织验证） | 验证企业身份 | 企业官网 |
| EV（扩展验证） | 最严格验证，地址栏显示企业名 | 金融、电商网站 |
| 通配符证书 | 覆盖所有子域名（`*.example.com`） | 多子域名项目 |

对于自由职业者的大多数项目，免费的 DV 证书完全够用。

## 三、Let's Encrypt 免费证书

Let's Encrypt 是最流行的免费证书颁发机构，证书有效期 90 天，支持自动续期。

### 在宝塔面板中申请

1. 进入网站设置 → SSL → Let's Encrypt
2. 选择域名，勾选需要申请证书的域名
3. 点击申请，等待验证完成
4. 开启"强制 HTTPS"

宝塔会自动配置续期任务。

### 使用 Certbot 手动申请

```bash
# 安装 Certbot
sudo apt install certbot python3-certbot-nginx

# 申请证书并自动配置 Nginx
sudo certbot --nginx -d example.com -d www.example.com

# 测试自动续期
sudo certbot renew --dry-run
```

Certbot 会自动修改 Nginx 配置并设置定时续期任务。

### 手动配置 Nginx SSL

如果需要手动配置：

```nginx
server {
    listen 443 ssl http2;
    server_name example.com;

    ssl_certificate /etc/letsencrypt/live/example.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/example.com/privkey.pem;

    # SSL 安全配置
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;

    root /www/wwwroot/example.com;
    index index.html;
}

# HTTP 重定向到 HTTPS
server {
    listen 80;
    server_name example.com www.example.com;
    return 301 https://$server_name$request_uri;
}
```

## 四、Cloudflare SSL

Cloudflare 提供免费的 SSL 服务，配置最简单。

### 配置步骤

1. 注册 Cloudflare 账号，添加你的域名
2. 将域名的 NS 记录修改为 Cloudflare 提供的地址
3. 在 SSL/TLS 设置中选择加密模式

### 加密模式说明

| 模式 | 说明 | 推荐场景 |
|------|------|---------|
| Flexible | Cloudflare 到用户加密，到服务器不加密 | 服务器无法安装证书时的临时方案 |
| Full | 全程加密，但不验证服务器证书 | 服务器有自签名证书 |
| Full (Strict) | 全程加密，验证服务器证书 | 推荐，最安全 |

推荐使用 Full (Strict) 模式，服务器端配合安装 Cloudflare Origin 证书。

### 申请 Origin 证书

1. SSL/TLS → Origin Server → Create Certificate
2. 选择域名，有效期最长 15 年
3. 下载证书和私钥，安装到服务器

## 五、各平台 SSL 配置

### GitHub Pages

- 自动配置，无需手动操作
- 在 Settings → Pages 中勾选 "Enforce HTTPS"
- 使用自定义域名时，GitHub 自动申请 Let's Encrypt 证书

### Vercel

- 自动配置，添加自定义域名后自动签发证书
- 默认强制 HTTPS
- 无需任何手动操作

### 宝塔面板 + Nginx

- 使用宝塔内置的 Let's Encrypt 申请功能
- 或手动安装证书：网站设置 → SSL → 其他证书 → 粘贴证书和私钥
- 开启强制 HTTPS 跳转

## 六、常见问题

### 证书过期怎么办？

- Let's Encrypt 证书 90 天过期，确保自动续期任务正常运行
- 检查续期任务：`sudo certbot renew --dry-run`
- 宝塔面板会自动处理续期

### 混合内容警告？

网页通过 HTTPS 加载，但其中引用了 HTTP 资源（图片、脚本等）。

解决方法：
- 将所有资源链接改为 HTTPS 或使用协议相对路径 `//`
- 添加 CSP 头：`Content-Security-Policy: upgrade-insecure-requests`

### SSL 证书不被信任？

- 确认证书链完整（fullchain 而非单独的证书文件）
- 检查证书是否过期
- 确认域名与证书匹配

### 重定向循环？

通常是 Cloudflare SSL 模式设置不当导致。如果服务器已有证书，使用 Full (Strict) 模式；如果没有，使用 Flexible 模式。

## 七、交付建议

1. 所有交付的网站必须配置 HTTPS
2. 优先使用平台自带的 SSL（GitHub Pages、Vercel）
3. 服务器部署时使用 Let's Encrypt + 自动续期
4. 交付前用 `https://www.ssllabs.com/ssltest/` 测试 SSL 配置评分
5. 提醒客户关注证书到期时间

HTTPS 配置是网站上线前的最后一步，确保每个项目都有安全的访问方式。

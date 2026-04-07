---
layout: ../../layouts/MainLayout.astro
title: 服务器部署（宝塔/Nginx）
---

# 服务器部署（宝塔/Nginx）

有些客户需要自己的服务器来运行动态网站、后端 API 或需要更多控制权的项目。这篇指南介绍如何使用宝塔面板和 Nginx 完成服务器部署。

## 一、什么时候需要服务器部署

- 客户需要运行后端程序（Node.js、PHP、Python 等）
- 需要数据库支持（MySQL、MongoDB 等）
- 客户要求数据存储在自己的服务器上
- 项目需要更高的自定义配置

## 二、购买云服务器

### 推荐服务商

| 服务商 | 适合场景 | 起步价格 |
|--------|---------|---------|
| 阿里云 ECS | 国内项目，需要备案 | 约 50 元/月起 |
| 腾讯云 CVM | 国内项目，活动价格优惠 | 约 45 元/月起 |
| Vultr / DigitalOcean | 海外项目，无需备案 | $5-6/月起 |

### 服务器配置建议

小型网站最低配置：
- CPU：1 核
- 内存：1-2 GB
- 硬盘：40 GB SSD
- 带宽：1-3 Mbps（国内）
- 系统：Ubuntu 22.04 或 CentOS 7

## 三、安装宝塔面板

宝塔面板（BT Panel）是国内最流行的服务器管理工具，提供可视化界面，大幅降低运维门槛。

### 安装步骤

通过 SSH 连接服务器后执行：

```bash
# Ubuntu/Debian 系统
wget -O install.sh https://download.bt.cn/install/install-ubuntu_6.0.sh && sudo bash install.sh ed8484bec

# CentOS 系统
yum install -y wget && wget -O install.sh https://download.bt.cn/install/install_6.0.sh && sh install.sh ed8484bec
```

安装完成后会显示：
- 面板访问地址（如 `http://服务器IP:8888/随机路径`）
- 默认用户名和密码

首次登录后建议立即修改密码和面板端口。

### 安装运行环境

首次进入宝塔面板会推荐安装 LNMP 或 LAMP 环境：

- LNMP（推荐）：Linux + Nginx + MySQL + PHP
- 选择 Nginx 1.24、MySQL 5.7 或 8.0、PHP 8.1

等待安装完成，通常需要 10-30 分钟。

## 四、Nginx 配置详解

### 基本概念

Nginx 是高性能的 Web 服务器，负责接收用户请求并返回网页内容。

### 静态网站配置

在宝塔面板中：网站 → 添加站点 → 填写域名 → 创建

宝塔会自动生成 Nginx 配置。手动配置示例：

```nginx
server {
    listen 80;
    server_name example.com www.example.com;

    root /www/wwwroot/example.com;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # 静态资源缓存
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg|woff2)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform";
    }
}
```

### Node.js 项目反向代理

如果部署 Node.js 应用，需要配置反向代理：

```nginx
server {
    listen 80;
    server_name api.example.com;

    location / {
        proxy_pass http://127.0.0.1:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

## 五、上传网站文件

### 方法 1：宝塔文件管理器

在宝塔面板 → 文件 → 进入网站目录 → 上传文件或压缩包

### 方法 2：使用 SCP 命令

```bash
# 上传整个 dist 目录到服务器
scp -r ./dist/* root@服务器IP:/www/wwwroot/example.com/
```

### 方法 3：使用 Git 拉取

```bash
# 在服务器上拉取代码
cd /www/wwwroot/example.com
git clone https://github.com/用户名/仓库名.git .
npm install
npm run build
```

## 六、设置虚拟主机（多站点）

一台服务器可以托管多个网站，通过 Nginx 虚拟主机实现：

在宝塔面板中直接添加多个站点即可，每个站点对应不同的域名和目录。

手动配置时，每个站点一个配置文件：

```bash
# 配置文件位置
/www/server/panel/vhost/nginx/站点名.conf
```

确保每个站点的 `server_name` 和 `root` 路径不同。

## 七、常见问题

### 网站无法访问？

1. 检查服务器安全组是否开放了 80 和 443 端口
2. 检查宝塔面板防火墙设置
3. 确认 Nginx 服务正在运行：`systemctl status nginx`
4. 查看 Nginx 错误日志：`tail -f /www/wwwlogs/站点名.error.log`

### 权限问题？

```bash
# 设置网站目录权限
chown -R www:www /www/wwwroot/example.com
chmod -R 755 /www/wwwroot/example.com
```

### Nginx 配置修改后不生效？

```bash
# 检查配置语法
nginx -t

# 重载配置
nginx -s reload
```

## 八、交付建议

1. 帮客户购买服务器并安装好宝塔面板
2. 创建独立的面板账号给客户，限制权限
3. 提供服务器基本操作文档
4. 设置好自动备份（宝塔面板 → 计划任务）
5. 国内服务器记得提醒客户完成 ICP 备案

服务器部署虽然比 Vercel 等平台复杂，但提供了最大的灵活性和控制权，适合有特殊需求的客户项目。

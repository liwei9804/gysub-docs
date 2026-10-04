# 🦆 gysub - 光鸭云盘自动订阅转存管理系统

<p align="center">
  <img src="https://img.shields.io/badge/Docker-Supported-blue?logo=docker" alt="Docker Supported" />
  <img src="https://img.shields.io/badge/Python-3.11-brightgreen?logo=python" alt="Python Version" />
  <img src="https://img.shields.io/badge/License-Protected-orange" alt="License" />
</p>

专为**光鸭云盘（G盘）**打造的高效自动订阅转存、追剧更新、智能重命名与媒体库整理工具。配合 Emby / Plex / Jellyfin 等家庭影音服务，实现追剧无感自动入库。

---

## ✨ 核心功能

- 📋 **订阅管理**：一键添加光鸭云盘公开分享链接或带密分享链接，全自动追踪更新。
- 🔄 **自动转存**：内置高并发转存引擎与定时调度器，发现更新秒级自动转存至个人网盘指定目录。
- 🏷️ **智能重命名与分集**：
  - 自动识别多达 10+ 种复杂文件名集数格式（`S01E01`、`E01`、`EP01`、`第01集`、中括号分集等）。
  - 支持转存后自动规范化重命名为媒体服务器（Emby / Jellyfin / Plex）标准命名。
- 🎬 **TMDB 媒体刮削支持**：支持对接 TMDB 智能识别影视信息、自动创建季目录（Season 1）。
- 📁 **自动子目录创建**：支持剧集自动创建专属父文件夹，避免网盘根目录混乱。
- 🔔 **多通道消息通知**：支持企业微信 Webhook、飞书 Bot 实时推送转存结果与异常告警。
- 🌐 **现代化 WebUI**：集成直观轻便的后台控制面板，支持查看转存历史、实时日志、手动一键检查。
- 🔐 **OAuth PKCE 安全授权**：原生支持光鸭云盘官方开放平台扫码/网页安全授权，Token 自动无感续期。

---

## 🐳 Docker 快速部署

### 方式一：Docker Compose（推荐）

1. 在宿主机创建工作目录并新建 `docker-compose.yml`：

```yaml
version: "3.8"
services:
  gysub:
    image: ghcr.io/liwei9804/gysub:latest
    container_name: gysub
    restart: unless-stopped
    ports:
      - "8004:8004"
    volumes:
      - ./data:/app/data
      - ./logs:/app/logs
      - /vol1/1000:/vol1/1000  # STRM 媒体库输出路径（直通宿主机存储目录，可按需修改）
    environment:
      - PORT=8004
      - AUTH_USER=admin
      - AUTH_PASS=admin
      - SECRET_KEY=gysub-random-secret-key
```

2. 启动容器：
```bash
docker compose up -d
```

---

### 方式二：Docker CLI 单命令运行

```bash
docker run -d \
  --name gysub \
  --restart unless-stopped \
  -p 8004:8004 \
  -v $(pwd)/data:/app/data \
  -v $(pwd)/logs:/app/logs \
  -v /vol1/1000:/vol1/1000 \
  -e PORT=8004 \
  -e AUTH_USER=admin \
  -e AUTH_PASS=admin \
  ghcr.io/liwei9804/gysub:latest
```

---

## ⚙️ 环境变量说明

| 环境变量 | 默认值 | 说明 |
| :--- | :--- | :--- |
| `PORT` | `8004` | 容器内 WebUI 服务监听端口 |
| `AUTH_USER` | `admin` | Web 界面初始登录用户名 |
| `AUTH_PASS` | `admin` | Web 界面初始登录密码 |
| `SECRET_KEY` | `change-this-secret-string` | Flask Session 安全密钥 |
| `DB_PATH` | `/app/data/gysub.db` | SQLite 数据库持久化路径 |

---

## 📖 使用指南

1. **登录系统**：
   - 浏览器打开 `http://<NAS_IP>:8004`，输入默认账号密码（`admin` / `admin`）登录。
2. **云盘授权**：
   - 首次进入系统，前往 **「设置」** 页面点击 **「授权光鸭云盘」**，完成官方扫码/登录授权。
3. **添加订阅**：
   - 点击 **「新建订阅」**，粘贴光鸭分享链接，指定转存目标目录及命名规则即可。
4. **媒体库联动**：
   - 转存目标目录配合 `Alist` / `CloudDrive2` / `OpenList` 挂载为本地目录，即可实现 Emby/Plex 自动扫描入库。

---

## 📂 目录持久化说明

- `/app/data`：存放 SQLite 数据库文件（订阅列表、转存记录、系统设置）。
- `/app/logs`：存放系统运行日志，方便排查转存异常。

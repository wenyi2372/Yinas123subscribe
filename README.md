# 📦 123云盘订阅管理系统

[![Docker](https://img.shields.io/badge/Docker-支持-blue)](https://hub.docker.com/)
[![Python](https://img.shields.io/badge/Python-3.11+-yellow)](https://www.python.org/)


> 一个强大的 123云盘分享链接自动化订阅管理工具，支持123云盘分享链接自动转存，115网盘、天翼网盘、夸克网盘、阿里云盘秒传至123云盘

## ✨ 功能特性

### 🔄 自动订阅转存
- 监控 123云盘分享链接更新
- 【2026.03.29新版已支持跨网盘任务，可添加115网盘、天翼网盘、夸克网盘分享链接】
- 自动转存新增文件到指定目录
- 支持多线程并发下载
- 支持保留原目录结构

### ⚡ 秒传功能
- 生成 123云盘秒传 JSON
- 支持115网盘、夸克网盘、天翼云盘、阿里云盘转 123云盘
- 支持 JSON 格式秒传链接
- 批量转存秒传文件

### 📺 OpenList 联动
- 监控 OpenList 目录变化
- 自动触发媒体库扫描
- 适用于飞牛等 NAS 系统

### 🎨 精美界面
- 现代化响应式 Web 界面
- 动画登录页面
- 实时任务状态监控
- 支持深色模式

## 🚀 快速开始

### Docker 部署（推荐）

```bash
# 1. 拉取镜像
docker pull wenyi2372/123pan-atuo-save:latest

# 2. 运行容器
docker run -d \
  --name 123subscribe \
  -p 24512:24512 \
  -e APP_USERNAME=admin \
  -e APP_PASSWORD=yourpassword \
  -v $(pwd)/conf:/app/conf \
  -v $(pwd)/logs:/app/logs \
  wenyi2372/123pan-atuo-save:latest

# 3. 访问 Web 界面
# http://localhost:24512
```

### Docker Compose

```yaml
version: '3.8'

services:
  123subscribe:
    image: wenyi2372/123pan-atuo-save:latest
    container_name: 123subscribe
    ports:
      - "24512:24512"
    environment:
      - APP_USERNAME=admin
      - APP_PASSWORD=yourpassword
    volumes:
      - ./conf:/app/conf
      - ./logs:/app/logs
    restart: unless-stopped
```


## ⚙️ 配置说明

### 基础配置 (conf/config.yaml)

```yaml
# 123云盘 API 配置
api:
  client_id: 'your_client_id'
  client_secret: 'your_client_secret'
  retry_attempts: 3

# 转存配置
sync:
  thread_pool_size: 5      # 并发线程数
  max_retries: 3           # 最大重试次数
  default_target_folder_id: 0  # 默认目标文件夹ID
  quark_cookie: ''         # 夸克网盘 Cookie

# 监控的分享链接
monitored_shares:
  - subject: '示例订阅'
    enabled: true
    url: 'https://www.123pan.com/s/xxxxx'
    target_folder_id: 12345678
    preserve_path: true    # 保留目录结构
    duplicate: 2           # 1=保留两者, 2=覆盖
    password: ''           # 提取码

# 调度配置
scheduler:
  interval_minutes: 60     # 检查间隔(分钟)

# OpenList Watcher 配置
openlist_watcher:
  enabled: false
  openlist_url: 'http://localhost:5244'
  token: 'your_token'
  watch_paths:
    - '/strm'
  poll_interval: 900       # 轮询间隔(秒)
  immediate_scan: true     # 启动时立即扫描
```

## 📖 使用指南

### 1. 首次使用

1. 访问 `http://你的IP:24512`
2. 使用设置的账号密码登录
3. 在"后台基础配置"中填写 123云盘 API 信息
4. 输入授权码激活软件
5. 添加订阅分享链接

### 2. 添加订阅

1. 点击"添加分享链接"
2. 粘贴 123云盘分享链接
3. 设置目标文件夹 ID
4. 启用订阅

### 3. 生成秒传

1. 切换到"秒传 JSON 生成"标签
2. 粘贴分享链接
3. 点击生成按钮
4. 复制或下载 JSON

### 4. 秒传转存

1. 切换到"秒传 123云盘"标签
2. 粘贴秒传链接或 JSON
3. 点击验证
4. 执行转存

## 🖼️ 界面预览

| 登录页面 | 主界面 |
|---------|--------|
| ![Login](/login.png) | ![Dashboard](/dashboard.png) |

| 秒传生成 | 秒传页面 |
|---------|---------|
| ![Rapid](/rapid.png) | ![Zero-second-Upload](/Zero-second-Upload.png) |

## 🛠️ 技术栈

- **后端**: Python 3.11 + Flask
- **前端**: HTML5 + CSS3 + JavaScript
- **任务调度**: APScheduler
- **HTTP 客户端**: Requests



## 🔧 环境变量

| 变量名 | 说明 | 默认值 |
|-------|------|-------|
| `APP_USERNAME` | 登录用户名 | - |
| `APP_PASSWORD` | 登录密码 | - |
| `SECRET_KEY` | Flask 密钥 | 自动生成 |

## 📝 常见问题

### Q: 如何获取 123云盘 API 密钥？
A: 访问 [123云盘开发者中心](https://www.123pan.com/) 申请。

### Q: 夸克网盘秒传需要什么？
A: 需要配置 quark_cookie，获取方法见文档。

### Q: 阿里云盘秒传需要什么？
A: 需要配置 阿里云盘Refresh Token，可从后台基础配置中跳转接口获取，并复制刷新令牌填写到配置中。（阿里云盘不支持从分享链接获取秒传信息，请先转存到自己的阿里云盘中，复制转存文件所在目录的URL进行JSON生成）

### Q: OpenList Watcher 有什么用？
A: 当 OpenList 挂载的网盘有新文件时，自动触发扫描更新媒体库。

### Q: 支持哪些云盘？
A: 目前支持 123云盘作为目标，支持从 123云盘、115网盘、夸克网盘、天翼云盘、阿里云盘生成秒传。


## 💖 支持

如果这个项目对你有帮助，欢迎给个 Star ⭐

## 📧 联系方式

- 问题反馈: [GitHub Issues](https://github.com/yinas/123subscribe/issues)

---

<p align="center">Made with ❤️ by Yinas</p>

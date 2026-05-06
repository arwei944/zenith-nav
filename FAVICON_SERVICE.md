# Favicon 服务部署指南

本文档介绍如何部署自建 favicon 服务作为备用方案。

## 方案对比

| 方案 | 优点 | 缺点 | 适用场景 |
|------|------|------|----------|
| **favicon.im** (默认) | 免费、Cloudflare CDN、99.9%可用、<100ms | 依赖第三方服务 | 日常使用 |
| **自建 besticon** | 完全可控、可定制、无限制 | 需要服务器、维护成本 | 高可用需求 |

## 快速开始

### 方案一：使用 favicon.im（已配置，无需操作）

当前代码已默认使用 favicon.im，无需任何配置即可工作。

```javascript
// 默认配置
const FAVICON_CONFIG = {
  primary: 'favicon.im',
  // selfHosted: null, // 未启用自建服务
};
```

### 方案二：部署自建 besticon 服务

#### 1. 使用 Docker 部署（推荐）

```bash
# 拉取镜像
docker pull matthiasluedtke/besticon:latest

# 运行服务
docker run -d \
  --name besticon \
  -p 8080:8080 \
  matthiasluedtke/besticon:latest

# 测试服务
curl "http://localhost:8080/icon?url=github.com&size=128"
```

#### 2. 使用 Docker Compose

创建 `docker-compose.yml`:

```yaml
version: '3.8'

services:
  besticon:
    image: matthiasluedtke/besticon:latest
    container_name: besticon
    ports:
      - "8080:8080"
    restart: unless-stopped
    environment:
      - PORT=8080
```

启动：

```bash
docker-compose up -d
```

#### 3. 部署到 HuggingFace Spaces

创建 `Dockerfile`:

```dockerfile
FROM matthiasluedtke/besticon:latest
EXPOSE 7860
ENV PORT=7860
CMD ["/besticon"]
```

创建 `README.md`:

```markdown
---
title: Besticon Service
emoji: 🎯
colorFrom: blue
colorTo: green
sdk: docker
pinned: false
---

# Besticon Favicon Service

自建 favicon 服务，用于 Zenith Nav 图标获取。

## API

- `GET /icon?url={domain}&size={size}` - 获取图标
- `GET /allicons.json?url={domain}` - 获取所有图标

## 示例

```
/icon?url=github.com&size=128
```
```

#### 4. 部署到 Vercel

```bash
# 克隆仓库
git clone https://github.com/mat/besticon.git
cd besticon

# 安装 Vercel CLI
npm i -g vercel

# 部署
vercel --prod
```

### 配置前端使用自建服务

修改 `preview.html` 中的配置：

```javascript
const FAVICON_CONFIG = {
  primary: 'favicon.im',
  // 启用自建服务，填写你的服务地址
  selfHosted: 'https://your-besticon-service.com/icon',
  // 或 HuggingFace Spaces 地址
  // selfHosted: 'https://your-username-besticon.hf.space/icon',
};
```

## API 参考

### besticon 接口

| 接口 | 说明 | 示例 |
|------|------|------|
| `/icon?url={domain}&size={size}` | 获取最佳图标 | `/icon?url=github.com&size=128` |
| `/allicons.json?url={domain}` | 获取所有图标列表 | `/allicons.json?url=github.com` |

### favicon.im 接口

| 接口 | 说明 | 示例 |
|------|------|------|
| `/{domain}` | 标准图标 | `/github.com` |
| `/{domain}?larger=true` | 高清图标(256px) | `/github.com?larger=true` |

## 故障排查

### 图标不显示

1. 检查浏览器控制台网络请求
2. 确认 favicon.im 是否可访问
3. 检查自建服务状态（如启用）

### 自建服务部署失败

```bash
# 查看日志
docker logs besticon

# 测试服务是否运行
curl -I http://localhost:8080/icon?url=github.com
```

## 推荐配置

### 纯前端方案（默认）

```javascript
const FAVICON_CONFIG = {
  primary: 'favicon.im',
  // 不配置 selfHosted，完全依赖第三方服务
};
```

### 高可用方案

```javascript
const FAVICON_CONFIG = {
  primary: 'favicon.im',
  selfHosted: 'https://your-backup-service.com/icon',
};
```

### 纯自建方案

```javascript
const FAVICON_CONFIG = {
  primary: 'self-hosted',
  selfHosted: 'https://your-service.com/icon',
};
```

## 相关链接

- [besticon GitHub](https://github.com/mat/besticon)
- [favicon.im 文档](https://favicon.im/)
- [favicon.im 日语文档](https://favicon.im/ja/)

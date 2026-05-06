# Favicon 服务部署指南

本文档介绍如何部署自建 favicon 服务作为备用方案。

## 方案对比与成功率

### 多源回退链成功率分析

```
┌─────────────────────────────────────────────────────────────────┐
│ 层级 │ 服务              │ 单源成功率 │ 累计成功率 │ 特点        │
├─────────────────────────────────────────────────────────────────┤
│ ①   │ favicon.im        │ ~95%      │ ~95%      │ 256px高清   │
│ ②   │ Google FaviconV2  │ ~60%      │ ~98%      │ 128px高清   │
│ ③   │ Google S2         │ ~40%      │ ~99.5%    │ 128px稳定   │
│ ④   │ DuckDuckGo        │ ~30%      │ ~99.8%    │ 隐私友好    │
│ ⑤   │ 自建besticon      │ ~70%      │ ~99.9%    │ 智能爬取    │
│ ⑥   │ 直接获取          │ ~50%      │ ~99.95%   │ 原始ico     │
│ ⑦   │ 首字母fallback    │ 100%      │ 100%      │ 始终可用    │
└─────────────────────────────────────────────────────────────────┘
```

> **说明**：成功率基于行业数据和实际测试估算，实际成功率因网站类型和地区而异。

### 方案对比

| 方案 | 优点 | 缺点 | 适用场景 |
|------|------|------|----------|
| **favicon.im** (默认) | 免费、Cloudflare CDN、99.9%可用、<100ms | 依赖第三方服务 | 日常使用 |
| **自建 besticon** | 完全可控、可定制、无限制、智能爬取 | 需要服务器、维护成本 | 高可用需求、隐私敏感 |

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

#### 方式1：HuggingFace Spaces（推荐，免费）

**步骤1：创建 GitHub 仓库**

```bash
# 已为你创建好
# https://github.com/arwei944/besticon-service
```

**步骤2：在 HuggingFace 创建 Space**

1. 访问 https://huggingface.co/spaces
2. 点击 "Create new Space"
3. 填写信息：
   - **Space name**: `besticon-service`
   - **License**: MIT
   - **Space SDK**: Docker
   - **Space hardware**: CPU basic (免费)
4. 选择 "Create Space"

**步骤3：连接 GitHub 仓库**

在 Space 设置中：
1. 选择 "Repository" 标签
2. 选择 "Link to GitHub"
3. 选择 `arwei944/besticon-service` 仓库
4. 点击 "Link"

**步骤4：等待部署**

HuggingFace 会自动构建和部署，约 2-5 分钟。

**步骤5：测试服务**

```bash
curl "https://arwei944-besticon-service.hf.space/icon?url=github.com&size=128"
```

#### 方式2：Docker 部署（自有服务器）

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

#### 方式3：Docker Compose

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

#### 方式4：Vercel 部署

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
  selfHosted: 'https://arwei944-besticon-service.hf.space/icon',
  // 或自有服务器
  // selfHosted: 'https://your-domain.com/icon',
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

### HuggingFace Space 构建失败

1. 检查 Dockerfile 是否正确
2. 查看 Space 的 "Logs" 标签
3. 确认端口配置（默认 7860）

## 推荐配置

### 纯前端方案（默认）

```javascript
const FAVICON_CONFIG = {
  primary: 'favicon.im',
  // 不配置 selfHosted，完全依赖第三方服务
};
```

### 高可用方案（推荐）

```javascript
const FAVICON_CONFIG = {
  primary: 'favicon.im',
  selfHosted: 'https://arwei944-besticon-service.hf.space/icon',
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
- [GitHub 仓库](https://github.com/arwei944/besticon-service)

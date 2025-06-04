# 🐳 Misonote Markdown 2.0 Docker 镜像使用说明

## 📦 预构建镜像

我们提供了预构建的 Docker 镜像，支持多种架构和部署模式：

- **AMD64** (x86_64) - 适用于大多数服务器和桌面环境
- **ARM64** (aarch64) - 适用于 Apple Silicon Mac、树莓派等

## ✨ 新功能亮点

### 🤖 AI 原生集成
- **MCP 协议支持** - 与 Cursor 编辑器深度集成
- **智能记忆系统** - AI 学习用户习惯和偏好
- **自然语言交互** - 通过对话管理文档

### 🔗 地址生成
- **自动链接生成** - 创建文档时自动生成访问地址
- **便于分享** - 一键获取文档分享链接

## 🚀 快速开始

### 1. 标准部署

```bash
# 使用默认临时密码 (admin123)
docker run -d \
  -p 3001:3001 \
  -v $(pwd)/docs:/app/docs \
  -v $(pwd)/data:/app/data \
  leeguo/misonote-markdown:latest

# 启动时设置自定义密码（推荐）
docker run -d \
  -p 3001:3001 \
  -e ADMIN_PASSWORD=admin123 \
  --name misonote-markdown \
  leeguo/misonote-markdown:latest

# 使用特定版本
docker run -d \
  -p 3001:3001 \
  -e ADMIN_PASSWORD=admin123 \
  --name misonote-markdown \
  leeguo/misonote-markdown:v3.0.0
```

### 2. 使用 Docker Compose (标准模式)

创建 `docker-compose.yml` 文件：

```yaml
services:
  misonote-markdown:
    image: leeguo/misonote-markdown:latest
    container_name: misonote-markdown
    ports:
      - "3001:3001"
    volumes:
      # 持久化文档目录
      - ./docs:/app/docs
      # 持久化数据目录
      - ./data:/app/data
      # 持久化日志目录
      - ./logs:/app/logs
    environment:
      - NODE_ENV=production
      # 设置管理员密码（推荐修改）
      - ADMIN_PASSWORD=admin123
      # 可选：自定义公开访问地址（也可在管理后台设置）
      # - NEXT_PUBLIC_BASE_URL=https://your-domain.com
    restart: unless-stopped
    networks:
      - markdown-network

networks:
  markdown-network:
    driver: bridge
```

然后运行：

```bash
docker-compose up -d
```

### 3. MCP 客户端配置（AI 功能）

**重要说明**: MCP 客户端不包含在 Docker 镜像中，需要单独安装到本地。

#### 步骤 1: 克隆 MCP 客户端

```bash
# 克隆 MCP 客户端到本地
git clone https://github.com/leeguooooo/misonote-mcp-client.git
cd misonote-mcp-client

# 安装依赖
npm install
```

#### 步骤 2: 配置 Cursor

在 Cursor 设置中添加 MCP 服务器配置：

```json
{
  "mcpServers": {
    "misonote-markdown": {
      "command": "node",
      "args": ["/path/to/misonote-mcp-client/misonote-mcp-client.js"],
      "env": {
        "MCP_SERVER_URL": "http://localhost:3001",
        "MCP_API_KEY": "your-api-key"
      }
    }
  }
}
```

#### 步骤 3: 创建 MCP API 密钥

在管理后台创建 MCP API 密钥：

```bash
# 1. 访问管理后台
open http://localhost:3001/admin

# 2. 登录管理员账号
# 3. 进入 "API 密钥管理" 页面
# 4. 点击 "创建新密钥" 按钮
# 5. 设置密钥名称和权限
# 6. 复制生成的 API 密钥
```

### 4. 环境变量配置

| 变量名 | 描述 | 默认值 |
|--------|------|--------|
| `NODE_ENV` | 运行环境 | `production` |
| `PORT` | 服务端口 | `3001` |
| `ADMIN_PASSWORD` | 管理员密码（明文，启动时自动加密） | `admin123` |
| `ADMIN_PASSWORD_HASH_BASE64` | 管理员密码哈希（Base64编码） | 自动生成 |
| `JWT_SECRET` | JWT 密钥 | 自动生成 |
| `NEXT_PUBLIC_BASE_URL` | 公开访问地址（可选，也可在管理后台设置） | 空 |

> **注意**: MCP API 密钥需要在管理后台创建，不再通过环境变量配置。

### 5. 数据持久化

建议挂载以下目录：

- `/app/docs` - Markdown 文档目录
- `/app/data` - 应用数据目录
- `/app/logs` - 日志文件目录

**注意**: AI 记忆系统数据存储在 `/app/docs/memories` 目录中，会随文档一起持久化。

### 6. 健康检查

容器提供健康检查端点：

```bash
# 主服务健康检查
curl http://localhost:3001/api/health

# MCP 服务能力检查
curl http://localhost:3001/api/mcp/capabilities
```

## 📋 可用标签

- `latest` - 最新稳定版本
- `v3.0.0` - 当前版本
- `2da3584` - Git 提交版本


## 🔧 故障排除

### 查看日志

```bash
docker logs misonote-markdown
```

### 进入容器

```bash
docker exec -it misonote-markdown sh
```

### 重启服务

```bash
docker restart misonote-markdown
```

## 📚 更多信息

- [项目主页](https://github.com/your-username/misonote-markdown)
- [Docker Hub](https://hub.docker.com/r/leeguo/misonote-markdown)
- [使用文档](./README.md)

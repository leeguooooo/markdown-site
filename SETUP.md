# 🚀 快速设置指南

## 📋 可用命令

### 开发命令
```bash
pnpm dev          # 启动开发服务器
pnpm build        # 构建生产版本
pnpm build:safe   # 安全构建（包含环境变量检查）
pnpm start        # 启动生产服务器
pnpm lint         # 代码检查
```

### PM2 部署命令
```bash
pnpm pm2:start    # 启动 PM2 服务
pnpm pm2:stop     # 停止 PM2 服务
pnpm pm2:restart  # 重启 PM2 服务
pnpm pm2:delete   # 删除 PM2 服务
pnpm pm2:logs     # 查看 PM2 日志
pnpm pm2:status   # 查看 PM2 状态
```

### 安全配置命令
```bash
pnpm security:setup   # 设置管理员密码
pnpm security:verify  # 验证管理员密码
```

### 清理命令
```bash
pnpm clean        # 清理构建文件和日志
```

## 🔧 首次部署

### 1. 安装依赖
```bash
pnpm install
```

### 2. 设置管理员密码
```bash
pnpm security:setup
```

### 3. 构建应用
```bash
pnpm build:safe
```

### 4. 启动服务
```bash
pnpm pm2:start
```

### 5. 查看状态
```bash
pnpm pm2:status
pnpm pm2:logs
```

## 🔄 更新部署

```bash
# 1. 拉取最新代码
git pull

# 2. 安全构建（会自动检查环境变量）
pnpm build:safe

# 3. 重启服务
pnpm pm2:restart
```

## 🛠️ 故障排除

### 环境变量问题
```bash
# 检查环境变量
node scripts/pre-build-check.js

# 重新设置密码
pnpm security:setup

# 验证密码
pnpm security:verify
```

### 构建问题
```bash
# 清理并重新构建
pnpm clean
pnpm build:safe
```

### PM2 问题
```bash
# 查看详细日志
pnpm pm2:logs

# 完全重启
pnpm pm2:delete
pnpm pm2:start
```

## 📁 项目结构

```
├── scripts/
│   ├── pre-build-check.js    # 构建前检查
│   ├── generate-password.js  # 密码生成工具
│   ├── verify-password.js    # 密码验证工具
│   └── safe-build.sh         # 安全构建脚本
├── src/
│   ├── app/                  # Next.js 应用目录
│   ├── components/           # React 组件
│   └── lib/                  # 工具库
├── .env                      # 环境变量配置
├── ecosystem.config.js       # PM2 配置
└── package.json              # 项目配置
```

## 🔐 安全说明

- 管理员密码使用 bcrypt 哈希存储
- 密码哈希使用 Base64 编码避免特殊字符问题
- JWT 密钥自动生成，确保安全性
- 所有敏感信息存储在 `.env` 文件中

## 💡 提示

- 首次部署时会自动生成安全密码
- 可以使用自定义密码或让系统自动生成
- 构建前会自动检查环境变量配置
- PM2 会自动管理进程重启和日志轮转

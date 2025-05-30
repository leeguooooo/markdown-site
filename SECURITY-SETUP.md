# 🔐 安全配置指南

## 问题诊断

你遇到的问题是：虽然在服务器上设置了 `.env` 文件，但密码仍然是默认的 `admin123`。

### 根本原因

1. **配置优先级问题**: `ecosystem.config.js` 中硬编码了环境变量，PM2 优先使用了配置文件中的值而不是 `.env` 文件
2. **弱密码哈希**: 原有的哈希值对应的是弱密码

## 🚀 解决方案

### 方法一：使用自动化脚本（推荐）

在你的服务器上 (`/srv/docs/markdown-site/`) 执行以下操作：

```bash
# 1. 上传更新的文件到服务器
# 将以下文件上传到你的服务器：
# - ecosystem.config.js (已更新，移除硬编码)
# - update-security.sh (自动化脚本)
# - generate-password-hash.js (密码生成工具)

# 2. 给脚本添加执行权限
chmod +x update-security.sh

# 3. 运行自动化脚本
./update-security.sh

# 4. 按提示操作
# - 脚本会要求你输入新的管理员密码
# - 需要输入两次进行确认
# - 密码至少需要6位字符
# - 脚本会自动生成密码哈希并更新配置
# - 自动重启 PM2 应用
```

### 方法二：手动更新

如果你不想使用自动化脚本，可以手动执行以下步骤：

```bash
# 1. 备份当前 .env 文件
cp .env .env.backup

# 2. 生成新的密码哈希
node generate-password-hash.js
# 按提示输入你的新密码，复制生成的哈希值

# 3. 更新 .env 文件内容
cat > .env << 'EOF'
# 管理员密码哈希 (使用上一步生成的哈希值)
ADMIN_PASSWORD_HASH=你生成的哈希值

# JWT 密钥 (自动生成的随机密钥)
JWT_SECRET=your-super-secret-jwt-key-change-in-production-min-32-chars-$(date +%s)

# 环境设置
NODE_ENV=production

# 服务端口
PORT=3001
EOF

# 4. 验证 .env 文件内容
cat .env
```

### 步骤 2: 更新 ecosystem.config.js

确保你的 `ecosystem.config.js` 文件不包含硬编码的敏感信息：

```javascript
module.exports = {
  apps: [
    {
      name: 'docs-platform',
      script: 'npm',
      args: 'start',
      cwd: './',
      instances: 1,
      autorestart: true,
      watch: false,
      max_memory_restart: '1G',
      env: {
        NODE_ENV: 'production',
        PORT: 3001,
        // 敏感信息从 .env 文件读取
      },
      env_production: {
        NODE_ENV: 'production',
        PORT: 3001,
        // 敏感信息从 .env 文件读取
      },
      error_file: './logs/err.log',
      out_file: './logs/out.log',
      log_file: './logs/combined.log',
      time: true
    }
  ]
};
```

### 步骤 3: 重启应用

```bash
# 重启 PM2 应用
pm2 restart docs-platform

# 查看应用状态
pm2 status

# 查看日志确认配置生效
pm2 logs docs-platform --lines 20
```

## 🔑 登录凭据

- **用户名**: `admin`
- **密码**: 你在脚本中设置的密码

> 💡 **提示**: 如果使用自动化脚本，密码就是你在运行脚本时输入的密码。如果手动更新，密码就是你在生成哈希时输入的密码。

## 🛡️ 安全建议

### 1. 生成你自己的密码

使用提供的工具生成新密码：

```bash
node generate-password-hash.js
```

### 2. 更强的 JWT 密钥

生成更安全的 JWT 密钥：

```bash
# 生成随机字符串
openssl rand -base64 32

# 或者使用 Node.js
node -e "console.log(require('crypto').randomBytes(32).toString('base64'))"
```

### 3. 定期更换密码

建议每3-6个月更换一次管理员密码。

### 4. 监控登录日志

定期检查应用日志，关注异常登录尝试：

```bash
pm2 logs docs-platform | grep -i "login\|auth\|password"
```

## 🔍 验证配置

### 检查环境变量是否生效

在应用启动后，查看日志中的调试信息：

```bash
pm2 logs docs-platform | grep "环境变量调试信息" -A 5
```

应该看到类似输出：
```
🔍 环境变量调试信息:
NODE_ENV: production
JWT_SECRET: 已设置
ADMIN_PASSWORD_HASH: 已设置
ADMIN_PASSWORD_HASH 长度: 60
```

### 测试登录

1. 访问管理界面 (http://localhost:3001)
2. 使用你设置的新密码登录
3. 确认登录成功并能正常访问管理功能

## 📋 故障排除

### 如果仍然无法登录

1. **检查 .env 文件权限**:
   ```bash
   ls -la .env
   chmod 600 .env  # 确保只有所有者可读写
   ```

2. **确认 PM2 读取了 .env 文件**:
   ```bash
   pm2 restart docs-platform --update-env
   ```

3. **检查应用日志**:
   ```bash
   pm2 logs docs-platform --lines 50
   ```

### 如果忘记密码

使用 `generate-password-hash.js` 工具重新生成密码哈希，然后更新 `.env` 文件并重启应用。

## 🚨 紧急恢复

如果完全无法访问，可以临时启用开发模式：

```bash
# 临时移除 ADMIN_PASSWORD_HASH 环境变量
mv .env .env.disabled
echo "NODE_ENV=development" > .env
pm2 restart docs-platform
```

这将启用默认密码 `admin123`，登录后立即重新配置安全密码。

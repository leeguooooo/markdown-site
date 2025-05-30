# ⚡ 4分钟快速开始

## 🎯 目标

在 4 分钟内完成 Markdown 文档系统的部署并开始使用。

## 📋 前置要求

- Node.js 18+ 
- pnpm (推荐) 或 npm

## 🚀 第一步：克隆和安装 (1分钟)

```bash
# 克隆项目
git clone https://github.com/leeguooooo/markdown-site.git
cd markdown-site

# 安装依赖
pnpm install
```

## 🔐 第二步：一键部署 (2分钟)

```bash
# 一键安全构建（自动配置环境变量）
pnpm build:safe
```

这个命令会：
- ✅ 自动检查环境变量
- ✅ 提示设置管理员密码（或自动生成）
- ✅ 生成安全的 JWT 密钥
- ✅ 构建生产版本

## 🚀 第三步：启动服务 (30秒)

```bash
# 启动 PM2 服务
pnpm pm2:start
```

## 🎉 第四步：开始使用 (30秒)

访问以下地址：

- **主页**: http://localhost:3001
- **管理后台**: http://localhost:3001/admin

使用刚才设置的管理员密码登录后台。

## 🔧 常用命令

```bash
# 查看服务状态
pnpm pm2:status

# 查看日志
pnpm pm2:logs

# 重启服务
pnpm pm2:restart

# 验证密码
pnpm security:verify
```

## 🛠️ 故障排除

### 问题：忘记管理员密码
```bash
pnpm security:setup
pnpm pm2:restart
```

### 问题：服务启动失败
```bash
# 检查环境变量
node scripts/pre-build-check.js

# 清理重建
pnpm clean
pnpm build:safe
```

### 问题：端口被占用
编辑 `.env` 文件，修改 `PORT=3001` 为其他端口。

## 📚 下一步

- 📖 查看 [完整文档](./README.md)
- 🔧 查看 [详细设置指南](./SETUP.md)
- 💬 [提交问题](https://github.com/leeguooooo/markdown-site/issues)

---

**🎉 恭喜！你已经成功部署了 Markdown 文档系统！**

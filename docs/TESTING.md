# 🧪 测试指南

本项目使用 Vitest 作为测试框架，确保代码质量和功能稳定性。

## 📋 测试策略

### 测试类型

1. **单元测试** - 测试独立的函数和模块
2. **集成测试** - 测试API路由和数据库交互
3. **构建测试** - 验证应用能够正确构建
4. **安全测试** - 检查依赖漏洞和安全问题

### 测试覆盖范围

- ✅ **API Key 管理** - 创建、验证、更新、删除
- ✅ **数据库操作** - 连接、事务、备份、清理
- ✅ **API 路由** - 认证、权限、错误处理
- 🔄 **系统设置** - 配置管理（计划中）
- 🔄 **用户认证** - JWT、密码验证（计划中）

## 🚀 运行测试

### 基本命令

```bash
# 运行所有测试
pnpm test

# 运行测试并生成覆盖率报告
pnpm test:coverage

# 监听模式运行测试
pnpm test:watch

# 运行测试UI界面
pnpm test:ui

# 运行一次性测试（CI环境）
pnpm test:run
```

### 运行特定测试

```bash
# 运行API Key相关测试
pnpm test api-keys

# 运行数据库测试
pnpm test database

# 运行API路由测试
pnpm test api/admin
```

## 📁 测试文件结构

```
tests/
├── setup.ts                    # 测试环境设置
├── utils/
│   └── test-database.ts       # 测试数据库工具
├── core/
│   ├── api/
│   │   └── api-keys.test.ts   # API Key单元测试
│   └── database/
│       └── database.test.ts    # 数据库功能测试
└── api/
    └── admin/
        └── api-keys.test.ts   # API路由集成测试
```

## 🔧 测试配置

### 环境变量

测试环境使用独立的配置：

```bash
NODE_ENV=test
JWT_SECRET=test-jwt-secret
ADMIN_PASSWORD_HASH_BASE64=<test-hash>
```

### 数据库

- 测试使用独立的SQLite数据库文件
- 每个测试前后自动清理数据
- 支持事务和外键约束测试

## ✍️ 编写测试

### 单元测试示例

```typescript
import { describe, it, expect, beforeEach } from 'vitest'
import { createApiKey } from '@/core/api/api-keys'
import { cleanTestDatabase } from '../utils/test-database'

describe('API Key 创建', () => {
  beforeEach(() => {
    cleanTestDatabase()
  })

  it('应该成功创建API密钥', () => {
    const result = createApiKey({
      name: 'Test Key',
      permissions: ['read', 'write']
    })

    expect(result.apiKey.name).toBe('Test Key')
    expect(result.secretKey).toMatch(/^mcp_[a-zA-Z0-9]{32}$/)
  })
})
```

### 集成测试示例

```typescript
import { describe, it, expect } from 'vitest'
import { NextRequest } from 'next/server'
import { POST } from '@/app/api/admin/api-keys/route'

describe('API Keys API', () => {
  it('应该创建新的API密钥', async () => {
    const request = new NextRequest('http://localhost/api/admin/api-keys', {
      method: 'POST',
      headers: { 
        'Authorization': `Bearer ${testToken}`,
        'Content-Type': 'application/json'
      },
      body: JSON.stringify({
        name: 'Test Key',
        permissions: ['read']
      })
    })

    const response = await POST(request)
    const data = await response.json()

    expect(response.status).toBe(201)
    expect(data.success).toBe(true)
  })
})
```

## 🎯 测试最佳实践

### 1. 测试命名

- 使用描述性的测试名称
- 采用 "应该...当..." 的格式
- 中文描述更清晰易懂

### 2. 测试隔离

- 每个测试前清理数据库
- 不依赖其他测试的状态
- 使用独立的测试数据

### 3. 断言清晰

- 一个测试只验证一个功能点
- 使用具体的断言而非通用检查
- 提供有意义的错误信息

### 4. 模拟和存根

- 对外部依赖进行模拟
- 使用真实数据库进行集成测试
- 避免网络请求和文件系统操作

## 🔍 调试测试

### 查看测试输出

```bash
# 详细输出
pnpm test --reporter=verbose

# 只显示失败的测试
pnpm test --reporter=basic

# 生成HTML报告
pnpm test:coverage
open coverage/index.html
```

### 调试单个测试

```bash
# 运行特定测试文件
pnpm test tests/core/api/api-keys.test.ts

# 运行特定测试用例
pnpm test -t "应该成功创建API密钥"
```

## 📊 CI/CD 集成

### GitHub Actions

测试在以下情况自动运行：

- 推送到 main/master 分支
- 创建或更新 Pull Request
- 手动触发工作流

### 测试阶段

1. **代码检查** - TypeScript 和 ESLint
2. **单元测试** - 核心功能测试
3. **集成测试** - API 和数据库测试
4. **构建测试** - 验证应用构建
5. **安全检查** - 依赖漏洞扫描

### 合并要求

所有测试必须通过才能合并到主分支：

- ✅ TypeScript 编译无错误
- ✅ ESLint 检查通过
- ✅ 单元测试通过率 100%
- ✅ 集成测试通过
- ✅ 构建成功
- ✅ 无高危安全漏洞

## 🚨 故障排除

### 常见问题

1. **数据库锁定**
   ```bash
   # 清理测试数据库
   rm -rf tests/data/
   ```

2. **端口占用**
   ```bash
   # 检查端口使用
   lsof -i :3001
   ```

3. **依赖问题**
   ```bash
   # 重新安装依赖
   rm -rf node_modules pnpm-lock.yaml
   pnpm install
   ```

### 获取帮助

- 查看测试日志了解失败原因
- 使用 `pnpm test:ui` 进行可视化调试
- 检查 GitHub Actions 的详细输出

## 📈 测试指标

目标测试覆盖率：

- **语句覆盖率**: > 80%
- **分支覆盖率**: > 75%
- **函数覆盖率**: > 85%
- **行覆盖率**: > 80%

当前重点测试模块：

- API Key 管理 (100% 覆盖)
- 数据库操作 (95% 覆盖)
- API 路由 (90% 覆盖)

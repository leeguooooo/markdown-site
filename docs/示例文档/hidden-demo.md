# 隐藏功能演示

这是一个演示隐藏功能的文档。

## 如何使用隐藏功能

1. **在管理界面中**：
   - 右键点击任何文件或文件夹
   - 选择"隐藏"选项
   - 文件将在公共视图中不可见

2. **隐藏状态指示**：
   - 隐藏的文件显示为灰色
   - 带有眼睛关闭的图标
   - 标记为"隐藏"

3. **管理员权限**：
   - 只有管理员可以看到隐藏文件
   - 可以随时切换隐藏/显示状态
   - 隐藏文件不会出现在公共 API 中

## 使用场景

- **草稿文档**: 未完成的文档可以隐藏
- **内部文档**: 仅供内部使用的文档
- **临时文件**: 临时性的文档
- **敏感信息**: 包含敏感信息的文档

## 技术实现

系统使用元数据文件来存储隐藏状态：

```json
{
  "isHidden": true,
  "lastModified": "2024-01-26T10:30:00.000Z"
}
```

这个文档本身可以被隐藏来演示功能！

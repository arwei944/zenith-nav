# 贡献指南 (Contributing Guide)

## Git 工作流程

本项目采用 **GitHub Flow** 工作流，简化开发流程。

### 分支策略

```
main (生产分支 - 始终可部署)
  │
  ├── feature/<功能描述>  → PR → Merge → 自动部署
  ├── fix/<修复描述>      → PR → Merge → 自动部署
  ├── docs/<文档描述>     → PR → Merge → 自动部署
  └── hotfix/<紧急修复>   → PR → Merge → 紧急部署
```

### 分支命名规范

| 类型 | 命名格式 | 示例 |
|------|----------|------|
| 新功能 | `feature/<描述>` | `feature/ios-desktop-style` |
| Bug 修复 | `fix/<描述>` | `fix/search-overlay-bug` |
| 文档更新 | `docs/<描述>` | `docs/update-readme` |
| 紧急修复 | `hotfix/<描述>` | `hotfix/broken-links` |

### 提交信息规范 (Conventional Commits)

所有提交信息必须遵循以下格式：

```
<type>(<scope>): <subject>

[body]

[footer]
```

#### Type 类型

| 类型 | 含义 | 版本影响 |
|------|------|----------|
| `feat` | 新功能 | MINOR (x.Y.z) |
| `fix` | 修复 Bug | PATCH (x.y.Z) |
| `docs` | 文档变更 | 无 |
| `style` | 代码格式（不影响功能） | 无 |
| `refactor` | 代码重构 | 无 |
| `perf` | 性能优化 | PATCH |
| `test` | 测试相关 | 无 |
| `chore` | 构建/工具/依赖 | 无 |
| `revert` | 回滚 | PATCH |

#### 示例

```bash
# 新功能
feat(ui): 添加 iOS 桌面风格导航

- 实现 4x6 图标网格布局
- 添加液态玻璃 Dock 效果
- 支持文件夹展开动画

Closes #5

# Bug 修复
fix(search): 修复搜索框在移动端无法聚焦的问题

- 添加 autofocus 属性
- 修复 iOS Safari 键盘弹出问题

# 性能优化
perf(animation): 优化文件夹展开动画性能

- 使用 transform 替代 top/left
- 添加 will-change 属性
```

### 版本管理 (Semantic Versioning)

版本号格式：`MAJOR.MINOR.PATCH`

| 版本变化 | 触发条件 | 示例 |
|----------|----------|------|
| MAJOR | 不兼容的 API 变更 | `1.0.0` → `2.0.0` |
| MINOR | 向下兼容的功能添加 | `1.0.0` → `1.1.0` |
| PATCH | 向下兼容的问题修复 | `1.0.0` → `1.0.1` |

### 开发流程

1. **创建功能分支**
   ```bash
   git checkout main
   git pull origin main
   git checkout -b feature/<描述>
   ```

2. **开发与提交**
   ```bash
   git add .
   git commit -m "feat(scope): 描述信息"
   ```

3. **推送到远程**
   ```bash
   git push origin feature/<描述>
   ```

4. **创建 Pull Request**
   - 在 GitHub 上创建 PR
   - 等待代码审查
   - 合并到 main

5. **打标签（维护者）**
   ```bash
   git checkout main
   git pull origin main
   git tag -a v1.0.0 -m "Release v1.0.0: 描述"
   git push origin v1.0.0
   ```

### 提交前检查清单

- [ ] 代码已测试通过
- [ ] 提交信息符合规范
- [ ] 分支命名符合规范
- [ ] 已同步最新 main 分支

---

## 版本历史

| 版本 | 日期 | 说明 |
|------|------|------|
| v1.0.0 | 2026-05-06 | iOS 桌面风格导航站正式发布 |

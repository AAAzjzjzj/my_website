# Git 代码更新注意事项

本文介绍使用 Git 进行代码更新时的注意事项和最佳实践。

## 基本工作流程

### 1. 更新本地代码

```bash
# 拉取最新代码
git pull origin main

# 或者先 fetch 再 merge
git fetch origin
git merge origin/main
```

### 2. 创建分支

```bash
# 创建并切换到新分支
git checkout -b feature/new-feature

# 或分两步
git branch feature/new-feature
git checkout feature/new-feature
```

### 3. 提交更改

```bash
# 查看状态
git status

# 添加文件
git add .
# 或添加特定文件
git add file1.py file2.py

# 提交
git commit -m "feat: add new feature"
```

### 4. 推送到远程

```bash
# 推送到远程分支
git push origin feature/new-feature

# 首次推送并设置上游分支
git push -u origin feature/new-feature
```

## 重要注意事项

### 1. 更新前先提交或暂存

```bash
# 如果有未提交的更改，先暂存
git stash

# 拉取最新代码
git pull

# 恢复暂存的更改
git stash pop
```

### 2. 解决冲突

```bash
# 拉取代码时出现冲突
git pull origin main

# 手动解决冲突后
git add .
git commit -m "resolve conflicts"
```

### 3. 避免直接在主分支开发

```bash
# 不推荐
git checkout main
# 直接修改代码...

# 推荐
git checkout -b feature/my-feature
# 在新分支上开发...
```

### 4. 定期同步主分支

```bash
# 在功能分支上
git checkout feature/my-feature

# 同步主分支最新代码
git fetch origin
git rebase origin/main

# 或使用 merge
git merge origin/main
```

## 常用命令

### 查看状态

```bash
# 查看工作区状态
git status

# 查看提交历史
git log

# 查看简洁日志
git log --oneline

# 查看分支
git branch -a
```

### 撤销操作

```bash
# 撤销工作区的修改
git checkout -- file.txt

# 撤销暂存区的文件
git reset HEAD file.txt

# 撤销最后一次提交（保留更改）
git reset --soft HEAD^

# 撤销最后一次提交（不保留更改）
git reset --hard HEAD^

# 修改最后一次提交
git commit --amend
```

### 分支操作

```bash
# 查看所有分支
git branch -a

# 删除本地分支
git branch -d feature/old-feature

# 强制删除
git branch -D feature/old-feature

# 删除远程分支
git push origin --delete feature/old-feature

# 重命名分支
git branch -m old-name new-name
```

### 标签操作

```bash
# 创建标签
git tag v1.0.0

# 创建带注释的标签
git tag -a v1.0.0 -m "Release version 1.0.0"

# 推送标签
git push origin v1.0.0

# 推送所有标签
git push origin --tags

# 删除标签
git tag -d v1.0.0
git push origin :refs/tags/v1.0.0
```

## 提交规范

### Commit Message 格式

```
<type>(<scope>): <subject>

<body>

<footer>
```

### Type 类型

- `feat`: 新功能
- `fix`: 修复 bug
- `docs`: 文档更新
- `style`: 代码格式调整
- `refactor`: 重构
- `test`: 测试相关
- `chore`: 构建/工具相关

### 示例

```bash
git commit -m "feat(user): add user login function"
git commit -m "fix(api): fix data parsing error"
git commit -m "docs(readme): update installation guide"
```

## 合并策略

### Merge

```bash
# 保留所有提交历史
git checkout main
git merge feature/my-feature
```

### Rebase

```bash
# 线性历史，更清晰
git checkout feature/my-feature
git rebase main
```

### Squash

```bash
# 合并多个提交为一个
git merge --squash feature/my-feature
git commit -m "feat: add complete feature"
```

## .gitignore 配置

```gitignore
# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
env/
venv/
*.egg-info/

# Node
node_modules/
npm-debug.log
yarn-error.log

# IDE
.vscode/
.idea/
*.swp
*.swo

# OS
.DS_Store
Thumbs.db

# 项目特定
config/local.py
*.log
.env
```

## 最佳实践

1. **频繁提交**：小步快跑，便于回滚
2. **有意义的提交信息**：清晰描述改动内容
3. **一个提交只做一件事**：便于代码审查
4. **提交前先拉取**：减少冲突
5. **不要提交敏感信息**：密码、密钥等
6. **使用分支**：隔离开发
7. **定期清理分支**：删除已合并的分支
8. **代码审查**：合并前进行 Code Review

## 常见问题

### 1. 误提交了敏感文件

```bash
# 从历史中删除文件
git filter-branch --force --index-filter \
  "git rm --cached --ignore-unmatch path/to/file" \
  --prune-empty --tag-name-filter cat -- --all

# 强制推送
git push origin --force --all
```

### 2. 合并冲突

```bash
# 查看冲突文件
git status

# 手动编辑冲突文件，选择保留的内容
# 标记为已解决
git add conflicted-file.txt

# 完成合并
git commit
```

### 3. 回退到某个版本

```bash
# 查看提交历史
git log

# 回退到指定提交
git reset --hard commit-hash

# 强制推送（谨慎使用）
git push -f origin main
```

## 团队协作

### Pull Request 流程

1. Fork 或创建分支
2. 开发功能
3. 提交代码
4. 创建 Pull Request
5. 代码审查
6. 合并到主分支

### Code Review 要点

- 代码风格是否统一
- 是否有潜在 bug
- 是否有性能问题
- 是否有安全隐患
- 测试是否充分

## 应用场景

- 版本控制
- 团队协作
- 代码审查
- 持续集成
- 发布管理
- 问题追踪

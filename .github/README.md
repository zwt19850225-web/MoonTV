# GitHub Actions 工作流说明

本项目配置了多个GitHub Actions工作流来自动化版本管理和发布流程。

## 工作流概览

### 1. CI (`.github/workflows/ci.yml`)
- **触发条件**: 推送到 `main` 或 `develop` 分支，或创建PR
- **功能**: 代码质量检查、测试、构建验证
- **包含任务**:
  - 代码检查 (linting)
  - 单元测试
  - 项目构建
  - 变更日志格式验证

### 2. Release (`.github/workflows/release.yml`)
- **触发条件**: 推送版本标签 (如 `v2.1.0`)
- **功能**: 自动发布新版本
- **包含任务**:
  - 运行 `convert-changelog.js` 脚本
  - 创建GitHub Release
  - 构建项目
  - 上传构建产物

### 3. Version Manager (`.github/workflows/version-manager.yml`)
- **触发条件**: 推送版本标签或手动触发
- **功能**: 专门负责版本管理
- **包含任务**:
  - 自动更新版本文件
  - 提交版本更新
  - 显示版本统计信息

## 使用方法

### 发布新版本

1. **更新 CHANGELOG 文件**
   ```markdown
   ## [2.2.0] - 2025-01-XX
   
   ### Added
   - 新功能1
   - 新功能2
   
   ### Changed
   - 改进功能1
   
   ### Fixed
   - 修复问题1
   ```

2. **创建并推送版本标签**
   ```bash
   git add CHANGELOG
   git commit -m "docs: update changelog for v2.2.0"
   git push origin main
   
   git tag v2.2.0
   git push origin v2.2.0
   ```

3. **GitHub Actions 自动执行**
   - 推送标签后，`Release` 和 `Version Manager` 工作流会自动触发
   - `convert-changelog.js` 脚本会自动运行并更新版本文件
   - 自动创建GitHub Release

### 手动触发版本管理

如果需要手动运行版本管理流程：

1. 进入GitHub仓库页面
2. 点击 "Actions" 标签
3. 选择 "Version Manager" 工作流
4. 点击 "Run workflow" 按钮
5. 选择分支并运行

## 脚本在GitHub Actions中的行为

当 `GITHUB_ACTIONS=true` 时，`convert-changelog.js` 脚本会：

1. **自动更新文件**:
   - `VERSION.txt` - 更新为最新版本号
   - `src/lib/version.ts` - 更新 `CURRENT_VERSION` 常量
   - `src/lib/changelog.ts` - 重新生成变更日志数据

2. **版本同步**:
   - 确保所有版本相关文件保持一致
   - 自动解析CHANGELOG格式
   - 生成TypeScript接口

## 注意事项

1. **权限要求**: 工作流需要 `GITHUB_TOKEN` 权限来推送代码和创建Release
2. **标签格式**: 版本标签必须遵循语义化版本格式 (如 `v2.1.0`)
3. **CHANGELOG格式**: 必须严格按照Markdown格式编写，脚本才能正确解析
4. **自动提交**: 版本更新后会自动提交并推送到仓库

## 故障排除

### 常见问题

1. **工作流未触发**
   - 检查标签格式是否正确
   - 确认推送到了正确的分支

2. **版本文件未更新**
   - 检查 `GITHUB_ACTIONS` 环境变量是否设置
   - 查看工作流日志中的错误信息

3. **权限错误**
   - 确认仓库设置中的Actions权限配置
   - 检查 `GITHUB_TOKEN` 是否有效

### 查看日志

每个工作流执行后，可以在Actions页面查看详细的执行日志，帮助诊断问题。

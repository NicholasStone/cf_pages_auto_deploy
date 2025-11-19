# Auto Sync Remote File

此仓库包含一个 GitHub Actions 工作流，用于自动监控并同步远程文件到本地仓库。

主要用于定期从指定的 URL 拉取最新的代码或配置（例如 Cloudflare Worker 脚本），并将其保存为根目录下的 `_workers.js`。如果内容发生变化，工作流会自动提交并推送到本仓库。

## 功能特性

- ⏰ **定时同步**：每天 UTC 时间 00:00 (北京时间 08:00) 自动运行。
- 🔄 **自动提交**：仅当远程文件内容与本地不一致时，才会创建新的 Commit。
- 🔒 **隐私保护**：通过 GitHub Secrets 管理源文件 URL，避免泄露敏感地址。
- 👆 **手动触发**：支持在 Actions 页面手动触发同步任务。

## 快速开始

### 1. 配置 GitHub Secrets

为了让工作流正常运行，您需要配置远程文件的下载地址：

1. 进入仓库的 **Settings** (设置)。
2. 在左侧菜单点击 **Secrets and variables** -> **Actions**。
3. 点击 **New repository secret** (新建仓库密钥)。
4. 填写以下信息：
   - **Name**: `SOURCE_URL`
   - **Secret**: 输入远程文件的完整下载地址 (例如: `https://raw.githubusercontent.com/user/repo/main/script.js`)。
5. 点击 **Add secret** 保存。

### 2. 启用工作流

如果您是 Fork 的仓库，默认情况下 GitHub Actions 可能是禁用的：

1. 点击仓库顶部的 **Actions** 标签。
2. 点击绿色按钮 **I understand my workflows, go ahead and enable them**。

## 工作流详情

文件路径：`.github/workflows/sync_worker.yml`

### 触发条件
- **Schedule**: `0 0 * * *` (每日一次)
- **Workflow Dispatch**: 允许手动点击运行

### 执行逻辑
1. 读取 `SOURCE_URL` 环境变量。
2. 使用 `curl` 下载内容到 `_workers.js`。
3. 使用 `git status` 检查文件是否有变动。
4. 如果有变动，以 `github-actions[bot]` 身份提交更改并推送到 `main` 分支。

## 常见问题

**Q: 为什么工作流运行失败？**
A: 请检查 `SOURCE_URL` Secret 是否已正确设置，并确保该 URL 是公开可访问的（或包含必要的访问令牌）。

**Q: 如何修改同步频率？**
A: 编辑 `.github/workflows/sync_worker.yml` 文件中的 `cron` 表达式。

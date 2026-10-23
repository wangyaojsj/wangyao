---
name: commit-project
description: 提交项目：将当前项目改动 git commit 并 push 到 Gitee 仓库 wangyao2026。当用户要求提交项目、推送代码、上传到 gitee 时使用。
---

# 提交项目（commit + push 到 Gitee）

将当前项目的改动提交并推送到 `https://gitee.com/neusoft_wangyao_jsj/wangyao2026.git`（master 分支）。

## 前置条件

- 认证用私人令牌存放在项目根目录的 `.gitee-token` 文件中（已被 .gitignore 排除，禁止提交、禁止写入其他任何被跟踪的文件）。
- 若 `.gitee-token` 不存在，请向用户索取新的令牌，不要凭记忆猜测。

## 执行步骤

1. **查看改动**（并行执行）：

   ```powershell
   git status
   git diff --stat
   git log --oneline -5
   ```

2. **暂存改动**：按文件名逐个 `git add`（不要用 `git add -A`/`git add .`），确认 `.gitee-token` 不在暂存区：

   ```powershell
   git add <文件1> <文件2> ...
   ```

   若没有任何可提交的改动，直接告知用户并结束。

3. **提交**：提交信息用中文，第一行概括本次改动内容（是什么、为什么），不加 Co-Authored-By 等额外署名，除非用户要求：

   ```powershell
   git commit -m "<提交信息>"
   ```

4. **推送**：读取令牌拼接推送地址（令牌只出现在命令行参数中，不写入任何文件）：

   ```powershell
   $wy_token = (Get-Content .gitee-token -Raw).Trim()
   git push -u https://neusoft_wangyao_jsj:$wy_token@gitee.com/neusoft_wangyao_jsj/wangyao2026.git master
   ```

5. **验证**：

   ```powershell
   git status
   git log --oneline -3
   ```

   确认工作区干净、提交已推送，然后向用户汇报：提交信息、推送的分支、涉及的文件数。

## 常见问题处理

- **推送被拒绝（non-fast-forward / diverged）**：说明远端 wangyao2026 上有本地没有的提交。先尝试 `git pull --no-rebase https://…wangyao2026.git master` 合并；若冲突或历史完全不相关（unrelated histories），停下来向用户说明情况，等用户决定（例如是否 `--allow-unrelated-histories`），**未经用户明确要求禁止 `--force` 推送**。
- **认证失败（403）**：令牌可能已失效或权限不足，告知用户需到 Gitee 重新生成私人令牌并更新 `.gitee-token`。
- **本地 origin 指向其他仓库**：本 skill 固定推送到 wangyao2026.git（按 URL 直推，不改 origin），不要动 origin 的配置。

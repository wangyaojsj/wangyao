---
name: commit-project
description: 提交项目：将当前项目改动 git commit 并 push 到 Gitee 仓库 wy026005。当用户要求提交项目、推送代码、上传到 gitee 时使用。
---

# 提交项目（commit + push 到 Gitee）

将当前项目的改动提交并推送到 `https://gitee.com/neusoft_wangyao_jsj/wy026005.git`（master 分支）。

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

4. **推送**：读取令牌拼接推送地址（令牌只出现在命令行参数中，不写入任何文件）。

   Gitee 令牌认证的**正确格式是 `oauth2:令牌`**（实测 2026-10-23：`用户名:令牌` 会报 "The token username invalid"，`令牌@` 空密码形式会报认证失败）。必须禁用凭据管理器与交互提示，否则 Windows 凭据管理器会弹 GUI 窗口：

   ```powershell
   $env:GIT_TERMINAL_PROMPT='0'; $env:GIT_ASKPASS='echo'
   $wy_token = (Get-Content .gitee-token -Raw).Trim()
   git -c credential.helper= push "https://oauth2:$wy_token@gitee.com/neusoft_wangyao_jsj/wy026005.git" master
   ```

   注意：**不要加 `-u` 参数**——它会把带令牌的 URL 写入 `.git/config` 的分支追踪配置，导致令牌落盘。若已误用，用 `git branch --unset-upstream master` 清除。

5. **验证**：

   ```powershell
   git status
   git log --oneline -3
   ```

   确认工作区干净、提交已推送，然后向用户汇报：提交信息、推送的分支、涉及的文件数。

## 常见问题处理

- **推送被拒（fetch first / non-fast-forward）**：远端 wangyao2026 上有本地没有的提交。先 fetch 查看远端内容（同样用 oauth2 格式与 `-c credential.helper=`）：

  ```powershell
  git -c credential.helper= fetch "https://oauth2:$wy_token@gitee.com/neusoft_wangyao_jsj/wy026005.git" master
  git log --oneline FETCH_HEAD -5
  git ls-tree -r --name-only FETCH_HEAD
  ```

  然后向用户说明远端提交内容，**由用户选择**：合并远端（`git pull --no-rebase`，历史不相关时需加 `--allow-unrelated-histories`）或强制覆盖（`git push --force`，会丢弃远端已有提交，必须用户明确批准）。
- **认证失败（403 / token invalid）**：令牌已失效、权限不足（需要 projects 读写权限）或格式错误。确认使用 `oauth2:令牌` 格式；仍失败则告知用户到 Gitee「设置 → 私人令牌」重新生成并更新 `.gitee-token`。
- **本地 origin 指向其他仓库**：本 skill 固定推送到 wy026005.git（按 URL 直推，不改 origin），不要动 origin 的配置。

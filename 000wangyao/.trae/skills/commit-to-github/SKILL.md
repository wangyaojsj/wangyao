---
name: commit-to-github
description: 将当前项目 commit 并 push 到 GitHub 仓库 wangyaojsj/wangyao，使用已配置的 SSH 公钥。当用户要求提交代码、push 到 GitHub、同步项目到远程仓库时使用。不用于其他仓库或一般 git 知识问答。
---

# 提交到 GitHub

将本仓库的改动 commit 并 push 到固定远程仓库，使用已设置的 SSH 公钥认证。环境为 Windows PowerShell。

## 远程仓库（固定目标）

- 本仓库双远程，`git remote -v` 先检查：
  - `origin` = Gitee `https://gitee.com/neusoft_wangyao_jsj/webprojectwangyao.git`（课程仓库，保持不动，用户未明确要求时不推送）
  - `github` = `ssh://git@ssh.github.com:443/wangyaojsj/wangyao.git`（即 https://github.com/wangyaojsj/wangyao ，SSH 公钥认证）
- 本网络封锁 github.com 的 22 端口，必须用上面的 443 端口形式；`git@github.com:...` 会报 Connection refused
- 若无 `github` 远程 → `git remote add github ssh://git@ssh.github.com:443/wangyaojsj/wangyao.git`
- 不得修改或删除 `origin`，不得改用 HTTPS + 账号密码/token 方式推送

## 流程

1. 并行执行 `git status`（不用 `-uall`）、`git diff`（含 `--staged`）、`git log --oneline -5`，了解改动内容和提交信息风格。
2. 检查是否有敏感或不应入库的文件（`.env`、密钥、凭据、`node_modules` 等）。若 `.gitignore` 未覆盖，先提醒用户；发现疑似敏感内容时不要提交。
3. 优先逐个 `git add <文件>` 暂存；仅当用户要求提交全部且无敏感文件时才可 `git add -A`。
4. 用 PowerShell here-string 创建新 commit，消息概括改动目的（why），遵循仓库既有风格：
   ```powershell
   git commit -m @'
   提交说明第一行

   补充描述
   '@
   ```
5. 推送：用 `git branch --show-current` 确认分支（通常为 `master`），执行 `git push github master`（不用 `-u`，避免改动默认上游）；仅当用户明确要求时才推送 Gitee。
6. 禁止事项：不使用 `--force`（除非用户明确要求）、不 `--no-verify` 跳过钩子、不默认 `--amend`。

## 失败处理

- 认证/连接失败：先 `ssh -T -o BatchMode=yes -p 443 git@ssh.github.com` 验证公钥（GitHub 正常时返回 exit 1 并输出 Hi <用户名>）；报 Connection refused 说明 22 端口被封，必须走 443 形式地址；仍失败则如实报告用户，不要切 HTTPS。
- 非 fast-forward（远程有新提交）：`git fetch origin` 后向用户报告分歧，由用户决定 rebase 或 merge，不要擅自强推。
- 其他错误：原样报告错误输出。

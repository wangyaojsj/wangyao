# AGENTS.md

课程作业仓库：纯静态 HTML/CSS/JS 练习页（《高级网页设计》课堂实践）。无构建、无测试、无 lint 工具链；页面直接用浏览器打开验证。环境为 Windows PowerShell。

## 命名与内容约定（用户强制规则）

- 所有 CSS 类名、id、JS 变量必须以 `wy_` 开头（如 `.wy_shell`、`--wy_ink`）。`index.html` 是更早的页面未遵循，新代码一律遵循。
- 每个新建 HTML 页面必须在 `index.html` 中加入超链接入口。
- 界面文案为中文（`lang="zh-CN"`）。
- 图标等第三方资源优先使用本地文件：Font Awesome / Lucide 已下载在 `cdn/`，自定义图标字体在 `font/`。不要引用外部 CDN。

## 提示词日志

用户规则：每次执行新指令前，向 `wangyao005_prompt.md` 以**追加**方式写入：时间、使用的模型、提示词内容。不要覆盖已有内容。

## 设计参考

做新页面或改视觉时，参考 `skills/frontend-design/SKILL.md`（避免 AI 模板化默认样式的设计指导）。

## Git / 远程仓库

双远程，提交信息用中文：

- `origin` → Gitee 课程仓库 `webprojectwangyao.git`（未明确要求不推送）
- `github` → `ssh://git@ssh.github.com:443/wangyaojsj/wangyao.git`。本网络封锁 github.com 的 22 端口，必须用 443 形式，不能用 `git@github.com:...` 或 HTTPS+密码。
- 另有一个 Gitee 目标仓库 `wy026005.git`，推送流程见 `skills/commit-project/SKILL.md`。

### 敏感凭据

- `.gitee-token` 存放 Gitee 私人令牌，已被 `.gitignore` 排除。**禁止提交、禁止写入任何被跟踪的文件**。令牌只出现在命令行参数中。若文件不存在，向用户索取，不要猜测。
- 推送到 Gitee 的令牌 URL 正确格式是 `oauth2:令牌`（`用户名:令牌` 会报错）。必须禁用凭据管理器并禁用 `-u`：

  ```powershell
  $env:GIT_TERMINAL_PROMPT='0'; $env:GIT_ASKPASS='echo'
  $wy_token = (Get-Content .gitee-token -Raw).Trim()
  git -c credential.helper= push "https://oauth2:$wy_token@gitee.com/neusoft_wangyao_jsj/wy026005.git" master
  ```

  不加 `-u`：它会把带令牌的 URL 写入 `.git/config`。若已误用 `git branch --unset-upstream master` 清除。

### 提交流程要点

- 逐个 `git add <文件>`，不要 `git add -A`/`git add .`，确认 `.gitee-token` 不在暂存区。
- 不用 `--force`、不 `--amend`、不 `--no-verify`（除非用户明确要求）。
- 推送报错先按 `skills/commit-project/SKILL.md` 和 `.trae/skills/commit-to-github/SKILL.md` 的「常见问题处理」排查（非 fast-forward 时由用户决定合并还是覆盖）。

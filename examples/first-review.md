# 第一次审阅演练 | First-review drill

从零到完成一次完整审阅，按顺序来（命令均出自上游 README，2026-11-14 口径）：

1. 装：`curl -fsSL https://plannotator.ai/install.sh | bash`（Windows 用 PowerShell 版）。装完按你 agent 的收尾表做完最后一步——Claude Code 要加插件市场并安装插件再重启；Codex、Gemini CLI 什么都不用做。
2. 先审 agent 上一条消息找手感：在 agent 里打 `/plannotator-last`（Codex 里用 `$plannotator-last`），浏览器会打开它上一条回复，在任意一段上圈一句、写条意见，点发送，看意见回到会话里。
3. 审一次计划：让 agent 用 plan mode 提出一个小计划（比如给现有项目加一个小功能），审阅页会自动弹出；试一次批准、一次打回并写明理由，看它按你的意见改计划后 diff 怎么标出变化。
4. 审一次代码：让 agent 改一处小代码，然后打 `/plannotator-review` 审未提交改动，在 diff 的某一行留一条意见发送回去。手头有现成的 GitHub PR 就拿链接练：`/plannotator-review <PR 链接>`。
5. 多 agent 并行时再开 Inbox：`plannotator inbox`，让两个会话各留一个问题在里面，你集中回一轮，确认回复各自回到对应会话。
6. 介意联网行为的，先做两个设置再正式用：审代码时带 `--no-git-remote-check`（或设 `PLANNOTATOR_GIT_REMOTE_CHECK=0`）；版本检查那一处目前关不掉，知道即可。用 SSH/远程环境时只走隧道转发端口，别把端口暴露在公网。

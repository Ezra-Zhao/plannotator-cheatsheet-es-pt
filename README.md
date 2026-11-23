# Plannotator 速查表 | Plannotator Cheatsheet | Guía rápida de Plannotator

> 📚 **Colección / Coleção**: esta hoja forma parte de [cheatsheets](https://github.com/Ezra-Zhao/cheatsheets) — 100 hojas de referencia en un solo repositorio. / Esta folha faz parte de [cheatsheets](https://github.com/Ezra-Zhao/cheatsheets) — 100 folhas de referência em um só repositório. ⭐

> 一页看懂 Plannotator：给编程 agent 装的一个本地浏览器审阅面——agent 提出计划、写出文档或 HTML 产物、改完代码之后，页面自动打开，你直接在计划、规格、Markdown、HTML 与 diff 上圈点批注，一键把意见送回 agent 让它照着改。agent 可以不停着干，你的意见随时插得进去。

- 上游项目：[backnotprop/plannotator](https://github.com/backnotprop/plannotator)（2026-11-14 实测 10,847 ★ / 807 forks，Apache-2.0 与 MIT 双许可；官网 plannotator.ai）
- 平台：macOS / Linux / Windows / WSL；一个安装脚本覆盖 Claude Code、Codex、Copilot CLI、Gemini CLI、OpenCode、Kiro、Droid、Amp、Pi 等十家编程 agent
- 本页结构：中文主文 → English → Español → Português

---

Apoyo y más: [GitHub Sponsors](https://github.com/sponsors/Ezra-Zhao) · [Gumroad — plantillas y cuadernos que uso](https://ezraflow28.gumroad.com) · [Ko-fi](https://ko-fi.com/ezra777zhao)

## 三条审阅路：先选哪条

| 你要审什么 | 走哪条 | 怎么触发 |
|---|---|---|
| **计划** | Plan mode | 不用敲命令：各家 agent 的钩子接好了，agent 一提出计划，审阅页自动弹出来；你批注、批准或打回，打回时意见结构化送回 agent 改 |
| **文件与产物** | Annotate | `/plannotator-annotate` 审本地文件、文件夹、网址，HTML 可直渲染着审；`/plannotator-last` 审 agent 上一条消息 |
| **代码改动** | Review | `/plannotator-review` 审未提交改动、GitHub PR、GitLab MR；GitButler 工作区走 `plannotator review --gitbutler` |

Codex 里把斜杠命令换成 `!plannotator …`（如 `!plannotator review`），或用 `$plannotator-review`、`$plannotator-annotate`、`$plannotator-last` 这组 skills，效果一样。

多 agent 并行时真正的瓶颈不是 agent 干得慢，是你审不过来——这三条路就是把「审」这件事从聊天框里搬到一个能圈点批注的页面上。

## 隐私卡（决定用不用之前先看）

本工具的卖点之一就是本地，但联网行为要先写清（原仓 Privacy 一节口径）：

- **不收遥测**：官方口径是不收集使用统计与分析；计划、diff、批注、草稿、历史与配置默认只存本地。
- **有一处关不掉的联网**：每个审阅页打开时，会向 GitHub 查一次有没有新版本——不发你的计划与审阅内容，GitHub 只收到一次普通请求，但**目前没有关闭开关**。
- **代码审阅会查远端默认分支**：本地代码审阅会对 origin 跑 `git ls-remote` 查默认分支与基线是否过期，**不发你的 diff**；介意可关：`plannotator review --no-git-remote-check`，或设 `PLANNOTATOR_GIT_REMOTE_CHECK=0`。
- **其余联网都是你主动用的功能**：审网址要去抓那个网址（默认经 Jina Reader）；审 GitHub/GitLab 的 PR/MR 用你自己的登录态取数；Ask AI 会把你选中的问题与相关上下文发给你的模型供应商；分享功能发的是你主动生成的链接。

## Inbox：多个 agent 的待审件收进一个窗口

新出的 Inbox 把多个 agent 的提问、决定与待审件收进一个本地窗口：

```bash
plannotator inbox
```

agent 把问题、文件、待审件留在 Inbox 里继续干自己的活，你有空再逐条回；你的回复直接成为对应会话的下一轮输入，agent 不用停着等你。Claude Code、Pi、OpenCode 走插件接入，其他 agent 走 `plannotator inbox mcp` 接入；Inbox 还接个人助理类 agent。

## 一行装法

```bash
# macOS / Linux / WSL
curl -fsSL https://plannotator.ai/install.sh | bash
```

```powershell
# Windows PowerShell
irm https://plannotator.ai/install.ps1 | iex
```

安装脚本装好二进制、自动认出你装了哪些 agent、逐个配好钩子与命令。装完各家的收尾动作不同，挑常用的记：

| Agent | 装完还要做什么 |
|---|---|
| Claude Code | `/plugin marketplace add backnotprop/plannotator` 再 `/plugin install plannotator@plannotator`，重启 |
| Copilot CLI | 同上但插件名是 `plannotator-copilot@plannotator`，重启；计划审阅在 plan mode（Shift+Tab）里触发 |
| Codex | 什么都不用做，计划审阅经实验性 Stop 钩子自动启用（原生 Windows 上钩子仍是实验状态，安装脚本会打印手动步骤） |
| Gemini CLI | 什么都不用做，钩子与命令自动配好（需 Gemini CLI 0.36.0 以上） |
| OpenCode | 在 `opencode.json` 里加 `"plugin": ["@plannotator/opencode@latest"]`，重启 |
| Pi | 不走安装脚本：`pi install npm:@plannotator/pi-extension` |

只想要二进制本体、不要任何钩子与配置：安装命令后加 `--minimal`，只把 `plannotator` 装到 `~/.local/bin`。卸载走 `plannotator uninstall`（默认保留你本地的计划与历史，加 `--purge` 才全删）。

## 终端版：不开浏览器也能批注

- **Herdr Annotate**：Plannotator 的终端插件，在 Herdr 里直接批注终端文本、整篇 Markdown 与 agent 的回复，意见同样直达 agent 会话：`herdr plugin install plannotator/herdr-annotate`。
- **Plannotator TUI**：独立版，不依赖 Herdr：`brew install plannotator/tap/plannotator-tui`。

批注存在同一个 Plannotator 数据目录里，终端审的和网页审的攒在一起。

## 命令速查

```text
/plannotator-annotate README.md        # 审本地文件（文件夹、网址同理）
/plannotator-annotate report.html --render-html   # HTML 直渲染着审
/plannotator-last                      # 审 agent 上一条消息
/plannotator-review                    # 审未提交改动
/plannotator-review <PR 或 MR 链接>     # 审 GitHub PR / GitLab MR
plannotator review --gitbutler         # 审 GitButler 工作区
plannotator inbox                      # 打开 Inbox 窗口
plannotator sessions                   # 看活跃的审阅会话（--open 1 重开某一个）
plannotator archive                    # 只读翻看存下来的计划决定
```

另有 VS Code 扩展（在编辑器标签里审计划、行间看 diff）与 Obsidian、Bear、Notion 的存档集成：批准的计划可自动存成笔记，配置在 Plannotator 的 Settings 面板里。

## 诚实标注区

- **版本检查关不掉**：见隐私卡第二条，这是当前版本的事实，不是配置问题；介意联网行为的人先知道再装。
- **远程模式没有登录**：SSH/devcontainer 场景下它会自动切固定端口（默认 19432），远程模式监听所有网卡且无登录验证——端口别暴露在不可信网络里，只走 SSH 隧道。
- **计划审阅的等待方式在变**：Claude Code 2.1.287 起，审阅不再让 Claude 干等——它先结束回合，你的决定之后以消息形式送到；想回到经典的阻塞式流程设 `PLANNOTATOR_CLAUDE_MOD=0`。
- **分享链接分两档**：小文档的分享内容压在链接片段里、不加密，有完整链接的人就能读；大文档与 HTML 走加密短链（AES-256-GCM、密钥在链接片段里、托管件 7 天过期）。团队协作主推的是托管产品 Workspaces，其数据口径与本地版不同，别混为一谈。
- **本页数字的时点**：星数为 2026-11-14 实测；命令与隐私口径以当日上游 README 为准，上游迭代很快（release 极多），装前扫一眼官方文档。

---

## English (summary)

Plannotator (by backnotprop, ~10.8k stars as of 2026-11-14, dual Apache-2.0/MIT) is a local, browser-based review surface for coding agents — Claude Code, Codex, Copilot CLI, Gemini CLI, OpenCode, Kiro, Droid, Amp, Pi, and more. When your agent proposes a plan, produces a document or HTML artifact, or finishes a code change, a review page opens automatically: you annotate the plan, spec, Markdown, HTML, or diff inline and send your feedback back to the agent in one click.

Three review paths: **Plan mode** needs no command (each agent's hooks pop the review page when a plan is proposed); **Annotate** via `/plannotator-annotate` (local files, folders, URLs, rendered HTML) and `/plannotator-last` (the agent's last message); **Review** via `/plannotator-review` for uncommitted changes, GitHub PRs, GitLab MRs, and GitButler workspaces. On Codex, use `!plannotator …` or the `$plannotator-*` skills. The newer **Inbox** (`plannotator inbox`) collects questions, decisions, and review items from several agents into one local window — agents keep working, your reply becomes the next turn of the session that asked. Terminal options: the Herdr Annotate plugin and the standalone Plannotator TUI.

Privacy, per upstream: no telemetry, and plans/diffs/annotations stay local by default — but every review page checks GitHub for a new release on load with currently no opt-out, and local code review runs `git ls-remote` against origin to find the default branch (it never sends your diff; disable with `--no-git-remote-check` or `PLANNOTATOR_GIT_REMOTE_CHECK=0`). Install is one line: `curl -fsSL https://plannotator.ai/install.sh | bash` (PowerShell equivalent on Windows).

---

## Español (guía rápida — traducción de estudio, pendiente de revisión por un hablante nativo)

Plannotator (de backnotprop, ~10,8 mil estrellas en noviembre de 2026, doble licencia Apache-2.0/MIT) es una superficie de revisión local, en el navegador, para agentes de programación: Claude Code, Codex, Copilot CLI, Gemini CLI, OpenCode, Kiro, Droid, Amp, Pi y más. Cuando tu agente propone un plan, produce un documento o un artefacto HTML, o termina un cambio de código, se abre una página de revisión: anotas sobre el plan, la especificación, el Markdown, el HTML o el diff, y envías tus comentarios al agente con un clic para que los aplique.

**Instalación (una línea):**

```bash
# macOS / Linux / WSL
curl -fsSL https://plannotator.ai/install.sh | bash
```

En Windows PowerShell: `irm https://plannotator.ai/install.ps1 | iex`. El instalador coloca el binario, detecta tus agentes y configura ganchos y comandos en cada uno. Después, según el agente: en Claude Code ejecuta `/plugin marketplace add backnotprop/plannotator` y `/plugin install plannotator@plannotator` y reinicia; en Copilot CLI lo mismo con `plannotator-copilot@plannotator`; en Codex y Gemini CLI no hay que hacer nada (Gemini exige la versión 0.36.0 o superior); en OpenCode añade `"plugin": ["@plannotator/opencode@latest"]` a `opencode.json`; en Pi no se usa el instalador: `pi install npm:@plannotator/pi-extension`. Con `--minimal` se instala solo el binario, sin ganchos ni configuraciones.

**Las tres rutas de revisión — cuál elegir:**

- **Revisar un plan → Plan mode.** Sin comandos: los ganchos de cada agente abren la página de revisión en cuanto el agente propone un plan. Anotas, apruebas o rechazas; si rechazas, tus comentarios vuelven al agente de forma estructurada para que revise el plan.
- **Revisar archivos y artefactos → Annotate.** `/plannotator-annotate` abre archivos locales, carpetas o URLs (el HTML se puede revisar renderizado con `--render-html`); `/plannotator-last` anota el último mensaje del agente.
- **Revisar código → Review.** `/plannotator-review` cubre cambios sin confirmar, PR de GitHub y MR de GitLab; para un espacio de GitButler, `plannotator review --gitbutler`.

En Codex, los comandos con barra se escriben `!plannotator …` (por ejemplo `!plannotator review`) o se usan las skills `$plannotator-review`, `$plannotator-annotate` y `$plannotator-last`.

**Inbox:** `plannotator inbox` abre una ventana local donde varios agentes dejan sus preguntas, decisiones y elementos por revisar mientras siguen trabajando. Tú respondes cuando puedes y tu respuesta se convierte en el siguiente turno de la sesión que preguntó. Claude Code, Pi y OpenCode se conectan por plugins; el resto, con `plannotator inbox mcp`. También acepta agentes asistentes personales.

**Versión de terminal:** el plugin Herdr Annotate (`herdr plugin install plannotator/herdr-annotate`) lleva la anotación al terminal dentro de Herdr, y Plannotator TUI (`brew install plannotator/tap/plannotator-tui`) funciona por separado, sin navegador.

**Tarjeta de privacidad (léela antes de decidir):** el proyecto declara que no recoge telemetría y que planes, diffs y anotaciones quedan en local por defecto. Pero hay dos salidas de red que debes conocer: cada página de revisión consulta a GitHub si hay una versión nueva al cargarse — no envía tu contenido, pero **hoy no existe opción para desactivarla** — y la revisión de código local ejecuta `git ls-remote` contra origin para detectar la rama por defecto (nunca envía tu diff; se apaga con `--no-git-remote-check` o `PLANNOTATOR_GIT_REMOTE_CHECK=0`). Lo demás sale solo cuando tú usas la función: anotar una URL la descarga, revisar un PR usa tu sesión de GitHub/GitLab, y Ask AI envía tu pregunta y su contexto a tu proveedor de modelos. En modo remoto (SSH/devcontainer) escucha en todas las interfaces sin inicio de sesión: no expongas el puerto fuera de tu túnel SSH.

---

## Português (guia rápido — tradução de estudo, aguardando revisão de um falante nativo)

O Plannotator (de backnotprop, ~10,8 mil estrelas em novembro de 2026, licença dupla Apache-2.0/MIT) é uma superfície de revisão local, no navegador, para agentes de programação: Claude Code, Codex, Copilot CLI, Gemini CLI, OpenCode, Kiro, Droid, Amp, Pi e mais. Quando seu agente propõe um plano, produz um documento ou um artefato HTML, ou termina uma mudança de código, uma página de revisão se abre: você anota sobre o plano, a especificação, o Markdown, o HTML ou o diff, e envia seus comentários ao agente com um clique para ele aplicar.

**Instalação (uma linha):**

```bash
# macOS / Linux / WSL
curl -fsSL https://plannotator.ai/install.sh | bash
```

No Windows PowerShell: `irm https://plannotator.ai/install.ps1 | iex`. O instalador coloca o binário, detecta seus agentes e configura ganchos e comandos em cada um. Depois, conforme o agente: no Claude Code rode `/plugin marketplace add backnotprop/plannotator` e `/plugin install plannotator@plannotator` e reinicie; no Copilot CLI o mesmo com `plannotator-copilot@plannotator`; no Codex e no Gemini CLI não há nada a fazer (o Gemini exige a versão 0.36.0 ou superior); no OpenCode adicione `"plugin": ["@plannotator/opencode@latest"]` ao `opencode.json`; no Pi não se usa o instalador: `pi install npm:@plannotator/pi-extension`. Com `--minimal` instala só o binário, sem ganchos nem configurações.

**Os três caminhos de revisão — qual escolher:**

- **Revisar um plano → Plan mode.** Sem comandos: os ganchos de cada agente abrem a página de revisão assim que o agente propõe um plano. Você anota, aprova ou rejeita; se rejeitar, seus comentários voltam ao agente de forma estruturada para ele revisar o plano.
- **Revisar arquivos e artefatos → Annotate.** `/plannotator-annotate` abre arquivos locais, pastas ou URLs (o HTML pode ser revisado renderizado com `--render-html`); `/plannotator-last` anota a última mensagem do agente.
- **Revisar código → Review.** `/plannotator-review` cobre mudanças não confirmadas, PRs do GitHub e MRs do GitLab; para um workspace do GitButler, `plannotator review --gitbutler`.

No Codex, os comandos com barra viram `!plannotator …` (por exemplo `!plannotator review`) ou usam-se as skills `$plannotator-review`, `$plannotator-annotate` e `$plannotator-last`.

**Inbox:** `plannotator inbox` abre uma janela local onde vários agentes deixam perguntas, decisões e itens para revisar enquanto continuam trabalhando. Você responde quando puder e sua resposta vira o próximo turno da sessão que perguntou. Claude Code, Pi e OpenCode conectam por plugins; os demais, com `plannotator inbox mcp`. Também aceita agentes assistentes pessoais.

**Versão de terminal:** o plugin Herdr Annotate (`herdr plugin install plannotator/herdr-annotate`) leva a anotação ao terminal dentro do Herdr, e o Plannotator TUI (`brew install plannotator/tap/plannotator-tui`) funciona separadamente, sem navegador.

**Cartão de privacidade (leia antes de decidir):** o projeto declara que não coleta telemetria e que planos, diffs e anotações ficam locais por padrão. Mas há duas saídas de rede que você precisa conhecer: cada página de revisão consulta o GitHub para saber se há versão nova ao carregar — não envia seu conteúdo, mas **hoje não existe opção para desativar** — e a revisão de código local roda `git ls-remote` contra o origin para detectar a branch padrão (nunca envia seu diff; desliga com `--no-git-remote-check` ou `PLANNOTATOR_GIT_REMOTE_CHECK=0`). O resto só sai quando você usa a função: anotar uma URL a baixa, revisar um PR usa sua sessão do GitHub/GitLab, e o Ask AI envia sua pergunta e o contexto dela ao seu provedor de modelos. No modo remoto (SSH/devcontainer) ele escuta em todas as interfaces sem login: não exponha a porta fora do seu túnel SSH.

---

## 来源与声明

- 灵感来源 / Source / Fuente / Fonte: [backnotprop/plannotator](https://github.com/backnotprop/plannotator)（官网 plannotator.ai，隐私口径出处为上游 README 的 Privacy and network behavior 一节与 plannotator.ai/privacy）
- 本仓是个人学习整理：内容用我自己的话总结自上游公开文档，不搬运原文；命令与参数以 2026-11-14 的上游 README 为准，上游迭代很快，请以官方文档为最终依据。
- 西语、葡语部分为学习译稿，待母语校对。Las secciones en español y portugués son traducciones de estudio pendientes de revisión nativa.
- License: MIT © 2026 Guangyi Zhao

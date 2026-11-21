<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-hero-zh-dark.webp">
  <img src="docs/assets/dsh-certification-hero-zh-light.webp" width="100%" alt="dsh-certification：五维认证注册表 —— data/attestations.json 记录 1 个已认证仓库（PerryLink/dsh-auto-review，等级 A，快照 2026-11-01），覆盖 manifest、构建卫生、供应链（Scorecard 4.1）、发布完整性与安装冒烟五个维度；check-freshness.mjs 断言时效、dsh-cert-mcp 镜像 sha256 与两个 README 标记">
</picture>

# dsh-certification

[English](README.md) · [简体中文](README-zh.md) · [Español](README-es.md) · [Português](README-pt.md) · [हिन्दी](README-hi.md)

</div>

> **更名自 `@perrylink/dsh-plugin-certification`。** 本仓库现为 **`dsh-certification`**；旧的带 scope 名称已弃用。本项目不向 npm 发布任何内容。
[![dshfind](https://dshfind.com/api/badge/PerryLink/dsh-certification?metric=downloads)](https://dshfind.com/plugins/PerryLink/dsh-certification?ref=badge)

面向 DeepSeek Harness 插件的社区认证：一份公开规范、一个机器可校验的评分模型，以及一个带徽章的独立注册表。这个项目之所以存在，是因为生态的权威清单明确声明自己“不是插件质量的裁判”、“收录不等于安全审查”——这样的检查应当由清单之外的第三方来执行，可复现，并且不声称对清单拥有裁判权。

**这是一个数据仓库，不是插件。** 它不包含任何 Cordis 插件，这里没有任何东西可以安装，也不向 npm 发布任何内容：`package.json` 的 `private` 为真，它存在只是为了让生态工具能读取元数据（`dsh.manifestVersion`、`engines.dsh`——与 `dsh-plugin-kit/data/peer-range.json` 记录为规范值的同一个三子句范围，这里逐字引用而非重新手打）。

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-flow-v5-dark.svg">
  <img src="docs/assets/dsh-certification-flow-v5-light.svg" width="100%" alt="dsh-certification 结构图：五个维度 A–E（manifest、构建卫生、供应链、发布完整性、安装冒烟）汇入 data/attestations.json；scripts/check-freshness.mjs 断言 30 天时效、dsh-cert-mcp 镜像 sha256 与 README 标记；scripts/badge.mjs 渲染 badges/<owner>__<repo>.svg，.github/workflows/registry.yml 每日重新渲染">
</picture>

*结构：五个维度 → data/attestations.json → check-freshness 门禁 → 徽章。图中的标注都是真实的脚本名、路径与 workflow 文件，因此一张图能服务全部五种语言。*

## 五个维度

![dsh-certification 真实终端运行 node scripts/check-freshness.mjs：freshness ok（最旧 generatedAt 2026-11-01，20 天，上限 30）、mirror ok（sha256 bab74bc817b1b511… 一致）、roster-count 98 ok、attested-count 1 ok、baseline-batch 正文 98/87/8/3/1 ok，最后 check-freshness: ok](docs/assets/dsh-certification-demo.gif)

*动图：本仓库门禁于 2026-11-21 的复跑，DSH_KIT_ROSTER 指向 dsh-kit 名册副本——真实 stdout，不联网。*

| 维度 | 证据来源 | 检查项 |
|---|---|---|
| A. manifest 合规 | 静态 | `dsh.bundle` manifest、LICENSE（SPDX）、keywords/topics 对齐、五语 README、engines |
| B. 构建卫生 | 静态 | `files` 允许清单完整性、依赖声明（peer/optional）、无恶意 `postinstall` 模式、存在 lint/typecheck 门禁 |
| C. 供应链 | OpenSSF Scorecard | 通过官方 Scorecard API 与徽章提供的 18 项上游检查 |
| D. 发布完整性 | npm provenance | 可用 `npm audit signatures` 验证的 SLSA 证明 |
| E. 安装冒烟 | dsh-test-drive 记录 | 在隔离的一次性 profile 中真实安装/加载/无密钥启动（四种状态：`ok` / `load-fail` / `install-fail` / `skip`） |

## 等级

- **A** —— 五项全部通过（E 必须为 `ok`），未命中任何否决项
- **B** —— E 通过，且 A/B/C/D 中至少三项通过
- **C** —— E 通过，其余不完整
- **D** —— 任一硬门禁失败（缺少 `dsh.bundle`、无许可证、命中恶意模式）
- **安全否决** —— 混淆代码、凭据外泄或安装期出现意外行为，立即评为 D，并公开理由
- **环境受限的 E** —— 当 E 仅因无人值守环境的门禁而收尾为 `install-fail`（例如 pnpm 的交互式 `approve-builds` 无法在沙箱中确认）时，该条目保留 **B** 级（若 A–D 通过），并记录 `environment-blocked` 与复现命令。环境类门禁绝不记为 `D`。

## 证据纪律（承重）

每个分数都必须来自真实、可复现的执行——一次探针结果、一次 Scorecard 运行、一次 provenance 校验，或一条 test-drive 记录。没有证据就是 `no-evidence`，绝不猜测。每个维度都记录带快照日期的审计链接。

## 注册表与徽章

- `data/attestations.json` —— 注册表：每个已认证仓库一条记录（各维度结果 + 证据链接 + 规范版本）。**CI 按每日定时任务从它重新渲染徽章（`.github/workflows/registry.yml`）；注册表条目本身由人工维护，不是 CI 输出。**
- 徽章产物是 `badges/` 下已提交的 SVG，由 `scripts/badge.mjs` 从注册表生成（注册表是唯一真源；脚本绝不编造等级）。
- **可用的徽章 URL**（产物文件名使用**双下划线** `<owner>__<repo>`）：

  ```
  https://raw.githubusercontent.com/PerryLink/dsh-certification/main/badges/<owner>__<repo>.svg
  ```

  可直接粘贴：

  ```markdown
  [![dsh-attested](https://raw.githubusercontent.com/PerryLink/dsh-certification/main/badges/<owner>__<repo>.svg)](https://github.com/PerryLink/dsh-certification#registry-and-badges)
  ```

- ⚠️ `https://perrylink.github.io/dsh-certification/badge/<owner>/<repo>.svg` 这一形式在 v0.2 之前有过文档，但**返回 404**：本仓库从未有过 GitHub Pages 部署（`registry.yml` 只渲染并提交 SVG）。在真正存在 Pages 部署之前，不要宣传那个 URL；要么使用上面的 raw 形式，要么启用 Pages 并把 `badges/<owner>__<repo>.svg` 发布到 `badge/<owner>/<repo>.svg`。
- 等级：A / B / C / D，外加灰色 `no-data` 表示任何不在注册表中的对象。
- 清单与市场只链接徽章——其“不背书”的立场保持不变。

## 门禁

`node scripts/check-freshness.mjs` 是本仓库的门禁（只用 `node:` 内置模块，零依赖），在每次 push、每个 pull request 以及每周运行：

- **freshness（时效）** —— 注册表的 `generatedAt` 与每个条目的 `snapshot` 不得超过 30 天。过期的认证证据会让构建失败，而不是悄悄按一个月前的样子描述某个插件。
- **mirror（镜像）** —— `dsh-cert-mcp` 提供的只读副本必须与 `data/attestations.json` 携带相同的注册表内容，这样实时 MCP URL 就永远不会提供本仓库已不再背书的等级。比较在 LF 规范化后的文本上进行，因为两个仓库是各自独立的克隆、各有自己的 `core.autocrlf`，按原始字节比较会报出并不存在的漂移。同级仓库缺席时，该检查报告 `SKIP`——明确表示“没有比较”，绝不算通过。
- **markers（标记）** —— 上面两个路线图计数会对照其来源断言，因此正文不会漂离名册。

`DSH_CERT_MIRROR` 与 `DSH_KIT_ROSTER` 可覆盖同级路径。`.github/workflows/checks.yml`（只读，`contents: read`）把 `dsh-cert-mcp` 与 `dsh-plugin-kit` 检出到 `_siblings/`，并通过这些开关传入，因此三条断言在每次 push、pull request 以及每周都会运行——同样的开关也是本地演练过期、漂移、畸形 JSON 与来源缺失等情形的方式。定时的 `registry.yml`（需要 `contents: write` 才能提交徽章）只运行 freshness 断言，因为该作业不检出其他内容。

## 与既有工具的关系

- `dsh-test-drive` 提供 E 维记录（已开放的 `test_drive` 域）
- `dsh-score` 把认证记录作为安装证据消费
- `dsh-skill-pack-security` 提供 B 维的恶意模式筛查
- OpenSSF Scorecard 整体提供 C 维——本项目不重新造一套
- 与 `dsh-plugin-scorecard`、`dsh-plugin-audit` 等的重叠：治理讨论已在 [issue #1](../../issues/1) 公开；目标是达成一个共识标准，而不是造第二十一个评分器

## 与 DSH 等级的关系

DSH 采用等级由本仓库之外的私有项目 `dsh-standards` 定义（撰写时仍是草案；访问权限请问维护者）。公开的教学副本维护在 [dsh-plugin-guide/guide/standards.md](https://github.com/PerryLink/dsh-plugin-guide/blob/main/guide/standards.md)：**DSH-ENTRY**（兼容基线）、**DSH-FLOW**（推荐采用）与 **DSH-BOOST**（引导实践）。它们不是本仓库的等级，本仓库也不为它们做任何认证。

- **等级 vs 层级。** `A`/`B`/`C`/`D` 是对上述五项检查的质量与证据等级；`ENTRY`/`FLOW`/`BOOST` 是采用深度。两条轴正交——一个插件可以评为 `A` 级，同时 `FLOW = not-run`。
- **ENTRY。** A 维（manifest）与 E 维（安装冒烟）——外加一条明确的安全否决，它是本仓库自身的前提条件而非 ENTRY 的要求——是 ENTRY `runtime` 的必要条件，但**并不充分**。标准里的 G0–G5 门禁仍决定该结果，本注册表不记录任何门禁结果。
- **FLOW 与 BOOST。** 本仓库没有任何维度运行 J1–J8 旅程或度量 LEV-1..LEV-8 实践；两者都在 [`data/standards-crosswalk.json`](./data/standards-crosswalk.json) 中记为 `not-covered`。

完整 crosswalk —— 每个维度证明什么、可以喂给哪道门禁、以及本仓库不会声称什么 —— 在 [`STANDARDS.md`](./STANDARDS.md)。

## 路线图

1. 发布规范 v1（本文档）
2. 注册表 + 徽章 CI
> **2026-11-01 更新：** 名册现在从 `dsh-plugin-kit/data/repos.json` 派生 **98** 个非 infra 仓库——**87 active / 8 frozen / 3 retired**——（该文件含 99 条，包括 kit 的 infra 行）。下面的数字已按该来源更新；旧的 94 / 86 / 5 / 3 一行是十月前批次的基线。

3. 认证 PerryLink 插件家族作为首个基线批次：家族名册中有 **98** 个插件仓库——**87** 个积极维护、**8** 个冻结、**3** 个退役，即所有 `role` 不是 `infra` 的条目——目前已认证 **1** 个。两个计数都是派生而非手写——`scripts/check-freshness.mjs` 会对照 `dsh-plugin-kit/data/repos.json` 与本注册表断言它们，以及这句话中的粗体数字。 <!-- roster-count: 98 --><!-- attested-count: 1 -->
   - ⚠️ **这个 98 的范围，以免被误读为家族规模** —— 权威表格在 [`dsh-kit` 的 README“Family counts — one definition per number”](https://github.com/PerryLink/dsh-kit#family-counts--one-definition-per-number)一节。这个数字是机器可读的名册文件：那里 99 条 = 这 98 条加上 `dsh-plugin-kit` 这条 infra 条目。它**不是** 45 行的家族表格（保留退役行、从不删除任何一行），也**不是**安装规格清单（排除已退役的名册成员）。
4. 与其他头部插件作者试点
5. 向权威清单与市场提议展示徽章（仅链接，不背书）

**DSH 家族线：** 本仓库属于 **PerryLink DeepSeek Harness 插件家族**，当前家族线为 `dsh-v0.2.2-alpha.3`。

## PerryLink DSH 插件家族

本项目是 [PerryLink](https://github.com/PerryLink) 维护的 **88 个积极维护**的 DeepSeek Harness 条目之一（87 个插件 + kit 的 infra 行）。家族名册共 **99** 条——**8 个冻结**、**3 个退役**——默认折叠在下方，让每份 README 都保持简短首屏。展开即可看到每一行，理由见「状态」列。

<details>
<summary>展开全部 99 个插件</summary>

| 插件 | 一句话简介 | 状态 |
|---|---|---|
| **[dsh-aqua-input-check](https://github.com/PerryLink/dsh-aqua-input-check)** | 水产养殖投入品记录核对（按投入品用量、休药期与起捕间隔核对记录自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-archive-check](https://github.com/PerryLink/dsh-archive-check)** | 档案归档完整性与保管期限核对（依据《档案法》及配套规定，仅提示差异，不作出定性结论） |  |
| **[dsh-auto-review](https://github.com/PerryLink/dsh-auto-review)** | 审批链上的第二模型自动审查，默认 fail-closed |  |
| **[dsh-autotier](https://github.com/PerryLink/dsh-autotier)** | 强/廉价模型层级的自动路由，带确定性风险守卫与 `/tier` 命令 | 🧊 已冻结 — 见仓库 README |
| **[dsh-background-agents](https://github.com/PerryLink/dsh-background-agents)** | 持久的后台子 agent，带 Web UI 侧栏、消息传递与中断 | 🚫 已退役 — 见仓库 README |
| **[dsh-bid-ca-precheck](https://github.com/PerryLink/dsh-bid-ca-precheck)** | 投标文件符合性预检（按招标文件逐条核对响应情况，仅提示差异，不作出定性结论） |  |
| **[dsh-bid-qual-check](https://github.com/PerryLink/dsh-bid-qual-check)** | 投标人资格条件核对（按资格条件与证明材料核对逐条响应，仅提示差异，不作出定性结论） |  |
| **[dsh-budget](https://github.com/PerryLink/dsh-budget)** | DeepSeek Harness 的成本治理：预算、碳排与延迟集于一个面板。 | 🧊 已冻结 — 见仓库 README |
| **[dsh-cert-mcp](https://github.com/PerryLink/dsh-cert-mcp)** | 只读 MCP 服务器，暴露认证注册表：等级、快照与五维证据 | 🧊 已冻结 — 见仓库 README |
| **[dsh-check-pack](https://github.com/PerryLink/dsh-check-pack)** | 一条命令装齐 53 个 PerryLink 合规核查器（dsh-checkers 套件），并插入 53 条 Cordis 行；覆盖报关、招投标、EHS、医疗、法律、财务等场景。 |  |
| **[dsh-checkpoint-rewind](https://github.com/PerryLink/dsh-checkpoint-rewind)** | 等价于 Claude Code /rewind：快照、会话分叉、一键恢复 |  |
| **[dsh-claude-move](https://github.com/PerryLink/dsh-claude-move)** | 把 Claude Code 的会话、记忆、技能与 CLAUDE.md 迁移进 DSH | 🧊 已冻结 — 见仓库 README |
| **[dsh-click](https://github.com/PerryLink/dsh-click)** | 面向 DeepSeek Harness 的跨平台原生桌面控制——Windows 优先。 |  |
| **[dsh-clinic](https://github.com/PerryLink/dsh-clinic)** | DeepSeek Harness 插件零依赖检测器：包结构静态检查（R）、cordis 契约扫描（K）、无键无头沙箱冒烟（D）与生态清单校验（CC）四层。 |  |
| **[dsh-codemod](https://github.com/PerryLink/dsh-codemod)** | 面向 DeepSeek Harness 插件作者的新一代单包升级技能：一份走廊索引读取调用方 peer 区间并路由到对应已封版升级卡片（dsh 0.1.3-alpha.1 → 0.1.5-rc.1 与 0.1.5-rc.2 → 0.1.6-alpha.2），零依赖接缝扫描器以 bundle 技能与 npx CLI 两种形态提供。 |  |
| **[dsh-community-bench](https://github.com/PerryLink/dsh-community-bench)** | DSH 应用内社区基准标签页：读取公开的、按任务条件计算的 registry，展示所有人看到的同一分数；不在本地算分。 | 🧊 已冻结 — 见仓库 README |
| **[dsh-composer-history](https://github.com/PerryLink/dsh-composer-history)** | Web 输入框的终端式输入历史：方向键、Ctrl+R 搜索 |  |
| **[dsh-contract-stance](https://github.com/PerryLink/dsh-contract-stance)** | 合同条款立场台账核对（按立场、风险等级与退让底线核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-customs-doc-check](https://github.com/PerryLink/dsh-customs-doc-check)** | 报关单证一致性核对（按公开的报关单填制规范核对单证间字段一致性，仅提示差异，不作出定性结论） |  |
| **[dsh-data-quality](https://github.com/PerryLink/dsh-data-quality)** | 数据集质量检查与引用交叉核对（此处消费的可选数值桥） |  |
| **[dsh-defend](https://github.com/PerryLink/dsh-defend)** | 面向 DeepSeek Harness 的提示注入、越狱与密钥泄露防护。 | 🧊 已冻结 — 见仓库 README |
| **[dsh-demurrage-ledger](https://github.com/PerryLink/dsh-demurrage-ledger)** | 滞箱费台账核对（按免箱期与费率核对超期天数与金额的自洽性，仅提示差异，不作出定性结论） |  |
| **[dsh-dida](https://github.com/PerryLink/dsh-dida)** | TickTick（滴答清单）任务桥：会话顶部任务面板（列表筛选、快速添加、完成、删除、截止日期、拖动排序）、十一个 ticktick_* 工具，以及基于官方 TickTick MCP 端点的插件设置卡。 |  |
| **[dsh-doublecheck](https://github.com/PerryLink/dsh-doublecheck)** | 工程纪律守卫：需求质询、测试门禁、对抗式评审 |  |
| **[dsh-draw](https://github.com/PerryLink/dsh-draw)** | 面向 DeepSeek Harness 的统一静态图像生成路由。 | 🧊 已冻结 — 见仓库 README |
| **[dsh-drill-script-check](https://github.com/PerryLink/dsh-drill-script-check)** | 应急演练脚本核对（按阶段、响应分级、指挥与资源要素核对脚本齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-eia-guide-check](https://github.com/PerryLink/dsh-eia-guide-check)** | 环评导则适用性核对（按要素、导则名称与执行标准核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-emergency-plan](https://github.com/PerryLink/dsh-emergency-plan)** | 应急预案要素齐备性与条款级页码定位（依据 GB/T 29639-2020 等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-essay-rubric-check](https://github.com/PerryLink/dsh-essay-rubric-check)** | 作文量表评分核对（按评分项、原文定位与分值算术核对评分自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-evidence-check](https://github.com/PerryLink/dsh-evidence-check)** | 证据清单齐备性核对（按待证事实与证据要素核对清单自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-export-ctl-check](https://github.com/PerryLink/dsh-export-ctl-check)** | 出口管制物项核对（按管制清单、最终用途与许可证核对台账齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-fast](https://github.com/PerryLink/dsh-fast)** | 面向 DeepSeek Harness 的只读性能诊断。 |  |
| **[dsh-fmea-table-check](https://github.com/PerryLink/dsh-fmea-table-check)** | FMEA 分析表要素齐备性与风险顺序数一致性核对（按公开的 FMEA 方法标准核对表内自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-forecast-penalty](https://github.com/PerryLink/dsh-forecast-penalty)** | 预测准确率考核核对（按准确率、门槛与考核单价核对考核费用的算术自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-fund-research](https://github.com/PerryLink/dsh-fund-research)** | 面向中国公募基金的确定性研究报告 |  |
| **[dsh-github-bot](https://github.com/PerryLink/dsh-github-bot)** | GitHub 集成：PR/issue/仓库/文件工具、评审与 CI 检查，每次写入都需人工审批，token 从不进日志。 |  |
| **[dsh-gongwen-flow-check](https://github.com/PerryLink/dsh-gongwen-flow-check)** | 公文流转与办理时限核对（按公开的公文处理规范核对环节完整性与时限，仅提示差异，不作出定性结论） |  |
| **[dsh-gongwen-word-check](https://github.com/PerryLink/dsh-gongwen-word-check)** | 公文格式要素核对（按公开的党政机关公文格式标准核对版头与主体要素，仅提示差异，不作出定性结论） |  |
| **[dsh-guard-plan-qc](https://github.com/PerryLink/dsh-guard-plan-qc)** | 劳动保护与安全防护用品配置表核对（按公开的配备标准核对品名规格与配置数量，仅提示差异，不作出定性结论） |  |
| **[dsh-hazchem-check](https://github.com/PerryLink/dsh-hazchem-check)** | 危险化学品临界量与重大危险源辨识核对（按储存量与临界量核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-hazplan-check](https://github.com/PerryLink/dsh-hazplan-check)** | HAZOP 分析表要素齐备性与偏差推导一致性核对（依据公开的危险与可操作性分析标准，仅提示差异，不作出定性结论） |  |
| **[dsh-hidden-risk-map](https://github.com/PerryLink/dsh-hidden-risk-map)** | 安全隐患台账→《工贸企业重大事故隐患判定标准》（应急管理部令第10号）条款映射提示（仅提示差异，不作出定性结论） |  |
| **[dsh-hs-classify](https://github.com/PerryLink/dsh-hs-classify)** | 商品归类层级一致性核对（按章、品目、子目与商品编号的层级关系核对自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-icd-rule-check](https://github.com/PerryLink/dsh-icd-rule-check)** | ICD 合并编码与星剑号配对提示（依据 GB/T 14396-2016 与公开的 ICD 使用说明，仅提示差异，不作出定性结论） |  |
| **[dsh-industry-research](https://github.com/PerryLink/dsh-industry-research)** | 行业研究编排，通过本插件的 `ctx.researchReport.assemble` 封存交付物 |  |
| **[dsh-lawcite-adapter](https://github.com/PerryLink/dsh-lawcite-adapter)** | 法条引用与文书要素核对（按名称、条号、原文与效力状态核对引用可核验性，仅提示差异，不作出定性结论） |  |
| **[dsh-laya](https://github.com/PerryLink/dsh-laya)** | Laya 类型化决策（`noul`/`choice`/`score`）作为一等 Cordis 服务与模型可见工具 |  |
| **[dsh-lc-doc-check](https://github.com/PerryLink/dsh-lc-doc-check)** | 信用证单据条款核对（按单据种类、份数、期限与不符点核对交单自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-learning-gap-check](https://github.com/PerryLink/dsh-learning-gap-check)** | 能力差距与培养计划核对（按能力等级差、培养措施与完成期限核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-library](https://github.com/PerryLink/dsh-library)** | 面向 DeepSeek Harness 的本地文档知识库。 |  |
| **[dsh-local-ai](https://github.com/PerryLink/dsh-local-ai)** | 面向 DeepSeek Harness 的本地模型（Ollama）集成。 |  |
| **[dsh-lsp-actions](https://github.com/PerryLink/dsh-lsp-actions)** | 基于语言服务器的 LSP 诊断、格式化、补全、代码操作与重命名 |  |
| **[dsh-mask](https://github.com/PerryLink/dsh-mask)** | PII 掩码中间件：在模型边界匿名化，在展示层还原 |  |
| **[dsh-mcp-panel](https://github.com/PerryLink/dsh-mcp-panel)** | 只读 MCP 运行时面板：/mcp 命令 + 设置页签，显示状态、工具与错误 |  |
| **[dsh-medrec-qc](https://github.com/PerryLink/dsh-medrec-qc)** | 病案首页形式质控与逻辑矛盾提示（依据国卫办医发〔2016〕24号等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-memento](https://github.com/PerryLink/dsh-memento)** | 经审批门禁的跨会话记忆：ctx.memory 接缝 + SQLite + memory 工具 |  |
| **[dsh-nsfc-form-check](https://github.com/PerryLink/dsh-nsfc-form-check)** | 国家自然科学基金申请书形式审查与匿名化提示（依据《国家自然科学基金条例》与申请人导入的当年指南，仅提示差异，不作出定性结论） |  |
| **[dsh-nurse-record-check](https://github.com/PerryLink/dsh-nurse-record-check)** | 护理记录时限、签名与评估表完整性校验（依据《病历书写基本规范》等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-observe](https://github.com/PerryLink/dsh-observe)** | 面向 DeepSeek Harness 的 OpenTelemetry 与 Langfuse 可观测性导出器。 |  |
| **[dsh-origin-rvc-check](https://github.com/PerryLink/dsh-origin-rvc-check)** | 原产地区域价值成分核对（按 RVC 定义式与门槛核对原产地判定自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-ota-review-check](https://github.com/PerryLink/dsh-ota-review-check)** | 在线点评回复台账核对（按回复时限、问题分类与整改状态核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-output-styles](https://github.com/PerryLink/dsh-output-styles)** | 等价于 Claude Code outputStyles 的运行时风格切换 |  |
| **[dsh-paper-doc-adapter](https://github.com/PerryLink/dsh-paper-doc-adapter)** | 试卷解析结构核对（按题型、答案、分值与 yotta 标识核对试卷结构自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-param-repair](https://github.com/PerryLink/dsh-param-repair)** | 在 11 家厂商之间映射请求参数，并按固定、可审计的规则修复坏掉的 JSON 工具输出；不联网、不调模型、不编造数据。 |  |
| **[dsh-permission-rules](https://github.com/PerryLink/dsh-permission-rules)** | Claude Code 风格的声明式 allow/deny/ask 权限规则，带审计 |  |
| **[dsh-permit-report-check](https://github.com/PerryLink/dsh-permit-report-check)** | 行政许可办理核对（按申请材料、法定时限与决定记录核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-phocinae](https://github.com/PerryLink/dsh-phocinae)** | 本地的非生成式决策层：phocinae_ask / phocinae_gate 两个工具，外加一个失效即拒的审批闸门。 |  |
| **[dsh-phone-inbox](https://github.com/PerryLink/dsh-phone-inbox)** | 会话的手机（微信桥）入站附件收件箱：列出本会话实际收到的附件，并按需采纳到工作区。 |  |
| **[dsh-pipeline-check](https://github.com/PerryLink/dsh-pipeline-check)** | 项目全流程文件链条核对（按环节先后与金额口径核对链条自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-pleading-draft](https://github.com/PerryLink/dsh-pleading-draft)** | 起诉状要素齐备性核对（按诉请、事实、证据、法条四要素核对文书齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-policy-brief-draft](https://github.com/PerryLink/dsh-policy-brief-draft)** | 政策专报要素核对（按观点、依据、出处与建议核对专报齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-power-loss-split](https://github.com/PerryLink/dsh-power-loss-split)** | 线损电量拆分与考核表核对（依据公开的线损计算口径，仅提示差异，不作出定性结论） |  |
| **[dsh-power-ticket-check](https://github.com/PerryLink/dsh-power-ticket-check)** | 电力工作票要素齐备性与签发许可顺序核对（依据现行电力安全工作规程，仅提示差异，不作出定性结论） |  |
| **[dsh-ppap-check](https://github.com/PerryLink/dsh-ppap-check)** | PPAP 提交要素齐备性核对（按公开的 PPAP 手册与提交等级核对要素齐备与版本填写，仅提示差异，不作出定性结论） |  |
| **[dsh-primer](https://github.com/PerryLink/dsh-primer)** | 把 DSH 插件开发指南做成可安装技能：打包、契约、检查，以及构建用的包校验器。 |  |
| **[dsh-protest-deadline](https://github.com/PerryLink/dsh-protest-deadline)** | 质疑与投诉期限核对（按公开的政府采购质疑投诉规定核对期限与台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-railway-window](https://github.com/PerryLink/dsh-railway-window)** | 铁路施工天窗台账核对（按时间窗、时长算术与命令号核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-reach](https://github.com/PerryLink/dsh-reach)** | 多渠道审批/提问桥：微信/Telegram/飞书，会话控制台 | 🧊 已冻结 — 见仓库 README |
| **[dsh-repair-order-qc](https://github.com/PerryLink/dsh-repair-order-qc)** | 维修工单结算核对（按项目金额、配件工时合计与结算总额核对算术自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-research-report](https://github.com/PerryLink/dsh-research-report)** | 可验证的研究报告引擎：内容寻址证据台账与封存版本 |  |
| **[dsh-review-reply-check](https://github.com/PerryLink/dsh-review-reply-check)** | 审稿意见逐条回应覆盖核对（按意见与回应一一对应核对覆盖完整性，仅提示差异，不作出定性结论） |  |
| **[dsh-rulefile-check](https://github.com/PerryLink/dsh-rulefile-check)** | 规则库文件自检核对（按条款引用纪律与效力上限核对规则库自身合规，仅提示差异，不作出定性结论） |  |
| **[dsh-safety-brief-check](https://github.com/PerryLink/dsh-safety-brief-check)** | 安全技术交底记录要素齐备性与签字闭环核对（依据公开的安全生产法规与检查标准，仅提示差异，不作出定性结论） |  |
| **[dsh-scaffold](https://github.com/PerryLink/dsh-scaffold)** | PerryLink DSH 插件共享零运行时依赖工具包：可插拔 Provider 注册表接缝、失效即拒的审批门与自适应会话事件门、机械校验脚本、共享的脱敏/计价/裁决模块，以及新插件骨架。 |  |
| **[dsh-score](https://github.com/PerryLink/dsh-score)** | 面向 DeepSeek Harness 插件的多维质量评分。 |  |
| **[dsh-security-pack](https://github.com/PerryLink/dsh-security-pack)** | 安全技能包 provider：预置带风险卡的安全评审技能，同时提供宿主侧与客户端侧两个面。 |  |
| **[dsh-session-pin](https://github.com/PerryLink/dsh-session-pin)** | 在 Web 侧栏固定会话，并保持持久排序 | 🚫 已退役 — 见仓库 README |
| **[dsh-session-sync](https://github.com/PerryLink/dsh-session-sync)** | 面向 DeepSeek Harness 的跨设备会话同步——会话存储的专用 git 镜像。 |  |
| **[dsh-site-log-check](https://github.com/PerryLink/dsh-site-log-check)** | 监理日志跨日连续性与旁站覆盖提示（依据 GB/T 50319-2013 等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-soe-decision-check](https://github.com/PerryLink/dsh-soe-decision-check)** | 三重一大决策台账核对（按前置研究、会议审议与决议记录核对程序留痕，仅提示差异，不作出定性结论） |  |
| **[dsh-soilwater-check](https://github.com/PerryLink/dsh-soilwater-check)** | 土壤与地下水监测台账核对（按点位、监测项目、限值与超标判定核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-sop-sync-check](https://github.com/PerryLink/dsh-sop-sync-check)** | 作业规程与 FMEA／控制计划一致性核对（按编号与版本核对三份文件是否同步，仅提示差异，不作出定性结论） |  |
| **[dsh-spc-gbt-adapter](https://github.com/PerryLink/dsh-spc-gbt-adapter)** | 控制图常数与过程能力指数核对（按 GB/T 4091 的常规控制图常数表核对控制限与 Cp/Cpk 的自洽性，仅提示差异，不作出定性结论） |  |
| **[dsh-talk](https://github.com/PerryLink/dsh-talk)** | 面向 DeepSeek Harness 的语音优先会话环：对它说话，听它回答。 |  |
| **[dsh-team-rooms](https://github.com/PerryLink/dsh-team-rooms)** | 跨会话团队房间：共享消息总线、任务板与时间线 | 🚫 已退役 — 见仓库 README |
| **[dsh-tender-extract](https://github.com/PerryLink/dsh-tender-extract)** | 招标文件条款摘录核对（按出处与原文逐字核对摘录完整性，仅提示差异，不作出定性结论） |  |
| **[dsh-tender-matrix](https://github.com/PerryLink/dsh-tender-matrix)** | 评标因素评分矩阵核对（按分值上限与合计核对矩阵自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-test-drive](https://github.com/PerryLink/dsh-test-drive)** | 面向 DeepSeek Harness 插件的隔离安装与冒烟试驾。 |  |
| **[dsh-warranty-calc](https://github.com/PerryLink/dsh-warranty-calc)** | 质保期与索赔金额核对（按质保期月数与里程、索赔金额算式核对自洽，仅提示差异，不作出定性结论） |  |

</details>

</details>

</details>

## 许可证

MIT。见 [LICENSE](./LICENSE)。


## HCB 数据

[HCB](https://github.com/PerryLink/dsh-hcb) 0.12.0 —— **136 项已检查 + 7 项已实测 / 3 条 FLOW `manual` / gate 2 受阻 · 31 条 review · 109 条 pilot · 1 条 `production-candidate`** —— 检查器**在 `dsh-standards@1.7.0` 上 143/143 行通过** —— 是声明式兼容性 / 轨道 / 风险的权威来源。7 条已实测行是 `dsh-plugin-guide@0.5.9`（唯一的 production-candidate）加上 `dsh-budget@0.4.26`、`dsh-defend@0.3.28`、`dsh-mask@0.2.25`、`dsh-memento@0.5.31`、`dsh-observe@0.2.28`、`dsh-test-drive@0.3.26`；其余 136 行未经实测，因此这并不使名册达到生产就绪。注册表可以链接 HCB 行；**认证 A–D 与 HCB gate 正交**。

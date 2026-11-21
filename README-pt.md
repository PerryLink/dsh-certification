<div align="center">

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-hero-en-dark.webp">
  <img src="docs/assets/dsh-certification-hero-en-light.webp" width="100%" alt="dsh-certification: um registro de atestações de cinco dimensões — data/attestations.json registra 1 repositório atestado (PerryLink/dsh-auto-review, grau A, snapshot 2026-11-01) em manifest, higiene de build, cadeia de suprimentos (Scorecard 4.1), integridade de release e fumaça de instalação; check-freshness.mjs verifica o frescor, o sha256 do espelho dsh-cert-mcp e os dois marcadores do README">
</picture>

# dsh-certification

[English](README.md) · [简体中文](README-zh.md) · [Español](README-es.md) · [Português](README-pt.md) · [हिन्दी](README-hi.md)

</div>

> **Renomeado de `@perrylink/dsh-plugin-certification`.** Este repositório agora é **`dsh-certification`**; o antigo nome com escopo está obsoleto. Nada é publicado no npm.
[![dshfind](https://dshfind.com/api/badge/PerryLink/dsh-certification?metric=downloads)](https://dshfind.com/plugins/PerryLink/dsh-certification?ref=badge)

Certificação comunitária para plugins do DeepSeek Harness: uma especificação pública, um modelo de pontuação verificável por máquina e um registro independente com badges. Este projeto existe porque a lista canônica do ecossistema diz explicitamente que "não é um juiz da qualidade dos plugins" e que "estar listado não é uma revisão de segurança": alguém fora das listas deve executar essa checagem, de forma reproduzível e sem reivindicar autoridade sobre elas.

**Este é um repositório de dados, não um plugin.** Ele não contém nenhum plugin do Cordis, nada aqui é instalável e nada é publicado no npm: `package.json` é `private` e existe apenas para que as ferramentas do ecossistema possam ler os metadados (`dsh.manifestVersion`, `engines.dsh` — o mesmo intervalo de três cláusulas que `dsh-plugin-kit/data/peer-range.json` registra como canônico, citado literalmente em vez de reescrito).

<picture>
  <source media="(prefers-color-scheme: dark)" srcset="docs/assets/dsh-certification-flow-v5-dark.svg">
  <img src="docs/assets/dsh-certification-flow-v5-light.svg" width="100%" alt="Diagrama de estrutura do dsh-certification: as cinco dimensões A–E (manifest, higiene de build, cadeia de suprimentos, integridade de release, fumaça de instalação) alimentam data/attestations.json; scripts/check-freshness.mjs verifica o frescor ≤ 30 dias, o sha256 do espelho dsh-cert-mcp e os marcadores do README; scripts/badge.mjs gera badges/<owner>__<repo>.svg e .github/workflows/registry.yml os re-renderiza diariamente">
</picture>

*Estrutura: cinco dimensões → data/attestations.json → portões check-freshness → badges. Os rótulos do diagrama são nomes reais de script, caminhos e arquivos de workflow, então uma única imagem serve aos cinco idiomas.*

## As cinco dimensões

![dsh-certification: execução real em terminal de node scripts/check-freshness.mjs — freshness ok (generatedAt mais antigo 2026-11-01, 20 dias, limite 30), mirror ok (sha256 bab74bc817b1b511… idêntico), roster-count 98 ok, attested-count 1 ok e a prosa do lote base 98/87/8/3/1 ok, terminando em check-freshness: ok](docs/assets/dsh-certification-demo.gif)

*Animação: node scripts/check-freshness.mjs reexecutado em 2026-11-21 com DSH_KIT_ROSTER apontando para o checkout do roster dsh-kit — stdout real, sem chamadas de rede.*

| Dimensão | Fonte de evidência | Verificações |
|---|---|---|
| A. Conformidade do manifest | Estático | manifest `dsh.bundle`, LICENSE (SPDX), alinhamento de keywords/topics, READMEs em cinco idiomas, engines |
| B. Higiene de build | Estático | lista de permissões `files` completa, declaração de dependências (peer/optional), ausência de padrões maliciosos em `postinstall`, portões de lint/typecheck presentes |
| C. Cadeia de suprimentos | OpenSSF Scorecard | as 18 verificações upstream, via API oficial do Scorecard e badges |
| D. Integridade de release | npm provenance | atestação SLSA verificável com `npm audit signatures` |
| E. Fumaça de instalação | registros do dsh-test-drive | instalação/carga/arranque sem chaves em um perfil descartável isolado (quatro estados: `ok` / `load-fail` / `install-fail` / `skip`) |

## Graus

- **A** — as cinco passam (E precisa ser `ok`), sem veto
- **B** — E passa e pelo menos três de A/B/C/D passam
- **C** — E passa e o restante fica incompleto
- **D** — qualquer portão rígido falha (`dsh.bundle` ausente, sem licença, padrão malicioso detectado)
- **Veto de segurança** — código ofuscado, exfiltração de credenciais ou comportamento surpreendente em tempo de instalação levam a D imediatamente, com o motivo publicado
- **E bloqueada pelo ambiente** — quando E termina em `install-fail` apenas por causa de um portão de ambiente não assistido (por exemplo, o `approve-builds` interativo do pnpm que não pode ser confirmado em um sandbox), a entrada mantém o grau **B** (se A–D passarem) e registra `environment-blocked` com o comando de reprodução. Portões ambientais nunca são registrados como `D`.

## Disciplina de evidência (estrutural)

Toda pontuação deve vir de execução real e reproduzível: o resultado de uma sonda, uma execução do Scorecard, uma verificação de provenance ou um registro de test-drive. Evidência ausente é `no-evidence`, nunca um palpite. Cada dimensão registra links de auditoria com data de snapshot.

## Registro e badges

- `data/attestations.json` — o registro: um registro por repositório atestado (resultados por dimensão + links de evidência + versão da especificação). **A CI re-renderiza os badges a partir dele em um cron diário (`.github/workflows/registry.yml`); as entradas do registro são curadoria humana, não saída da CI.**
- Os artefatos de badge são os SVGs versionados em `badges/`, gerados por `scripts/badge.mjs` a partir do registro (o registro é a única fonte de verdade; o script nunca inventa um grau).
- **URL de badge funcional** (o nome do artefato usa **sublinhado duplo** `<owner>__<repo>`):

  ```
  https://raw.githubusercontent.com/PerryLink/dsh-certification/main/badges/<owner>__<repo>.svg
  ```

  Pronta para colar:

  ```markdown
  [![dsh-attested](https://raw.githubusercontent.com/PerryLink/dsh-certification/main/badges/<owner>__<repo>.svg)](https://github.com/PerryLink/dsh-certification#registry-and-badges)
  ```

- ⚠️ A forma `https://perrylink.github.io/dsh-certification/badge/<owner>/<repo>.svg` foi documentada antes da v0.2 mas **retornava 404**: este repositório nunca teve um deploy de GitHub Pages (`registry.yml` apenas renderiza e faz commit dos SVGs). Não divulgue essa URL até existir de fato um deploy de Pages; use a forma raw acima ou habilite o Pages e publique `badges/<owner>__<repo>.svg` em `badge/<owner>/<repo>.svg`.
- Graus: A / B / C / D, mais um `no-data` cinza para tudo que não está no registro.
- Listas e marketplaces apenas vinculam o badge — a postura de "sem endosso" deles não muda.

## Checagens

`node scripts/check-freshness.mjs` é o portão deste repositório (apenas módulos embutidos de `node:`, zero dependências) e roda em cada push, em cada pull request e semanalmente:

- **freshness** — o `generatedAt` do registro e o `snapshot` de cada entrada devem ter no máximo 30 dias. Evidência de certificação obsoleta faz o build falhar em vez de descrever silenciosamente um plugin como ele era há um mês.
- **mirror** — a cópia somente leitura servida pelo `dsh-cert-mcp` deve conter o mesmo conteúdo de registro que `data/attestations.json`, para que a URL viva do MCP nunca sirva graus que este repositório já não sustenta. A comparação é feita sobre texto normalizado em LF, porque os dois repositórios são clones separados com seu próprio `core.autocrlf` e uma comparação de bytes crus reportaria uma divergência inexistente. Quando o repositório irmão está ausente, a verificação informa `SKIP` — um "nada foi comparado" explícito, nunca um aprovado.
- **markers** — as duas contagens do roadmap acima são aferidas contra suas fontes, para que a prosa não se afaste do roster.

`DSH_CERT_MIRROR` e `DSH_KIT_ROSTER` substituem os caminhos dos repositórios irmãos. `.github/workflows/checks.yml` (somente leitura, `contents: read`) faz checkout de `dsh-cert-mcp` e `dsh-plugin-kit` em `_siblings/` e os passa por esses switches, de modo que as três asserções rodam em cada push e pull request além de semanalmente; e os mesmos switches são a forma de exercitar localmente os casos de obsolescência, divergência, JSON malformado e fonte ausente. O `registry.yml` agendado (que precisa de `contents: write` para fazer commit dos badges) executa apenas a asserção de frescor, já que esse job não faz checkout de mais nada.

## Relação com as ferramentas existentes

- `dsh-test-drive` fornece os registros da dimensão E (domínio `test_drive` já aberto)
- `dsh-score` consome os registros de certificação como evidência de instalação
- `dsh-skill-pack-security` fornece a triagem de padrões maliciosos para a dimensão B
- O OpenSSF Scorecard fornece a dimensão C inteira — este projeto não a reinventa
- Sobreposição com `dsh-plugin-scorecard`, `dsh-plugin-audit` e similares: a discussão de governança está aberta na [issue #1](../../issues/1); o objetivo é um padrão acordado, não um vigésimo primeiro avaliador

## Relação com os níveis DSH

Os níveis de adoção DSH são definidos fora deste repositório, pelo projeto privado `dsh-standards` (um rascunho no momento em que isto foi escrito; peça acesso ao mantenedor). Uma cópia pública de ensino é mantida em [dsh-plugin-guide/guide/standards.md](https://github.com/PerryLink/dsh-plugin-guide/blob/main/guide/standards.md): **DSH-ENTRY** (linha de base de compatibilidade), **DSH-FLOW** (adoção recomendada) e **DSH-BOOST** (prática orientadora). Eles não são os graus deste repositório, e nada aqui os certifica.

- **Grau vs nível.** `A`/`B`/`C`/`D` é um grau de qualidade e evidência sobre as cinco verificações acima; `ENTRY`/`FLOW`/`BOOST` é profundidade de adoção. Os dois eixos são ortogonais: um plugin pode ter grau `A` e ainda assim ter `FLOW = not-run`.
- **ENTRY.** As dimensões A (manifest) e E (fumaça de instalação) — mais um veto de segurança claro, que é uma precondição deste repositório, não um requisito de ENTRY — são necessárias mas **não suficientes** para ENTRY `runtime`. Os portões G0–G5 dos padrões ainda decidem isso, e este registro não anota nenhum resultado de portão.
- **FLOW e BOOST.** Nenhuma dimensão aqui executa as jornadas J1–J8 nem mede as práticas LEV-1..LEV-8; ambas constam como `not-covered` em [`data/standards-crosswalk.json`](./data/standards-crosswalk.json).

O crosswalk completo — o que cada dimensão prova, a que portão ela pode alimentar e o que este repositório não vai reivindicar — está em [`STANDARDS.md`](./STANDARDS.md).

## Roteiro

1. Publicar a especificação v1 (este documento)
2. Registro + CI de badges
> **Atualização de 2026-11-01:** o roster agora deriva **98** repositórios não-infra — **87 active / 8 frozen / 3 retired** — de `dsh-plugin-kit/data/repos.json` (99 entradas incluindo a linha de infraestrutura do kit). Os números abaixo estão atualizados para essa fonte; a linha anterior de 94 / 86 / 5 / 3 era a base do lote de antes de outubro.

3. Atestar a família de plugins PerryLink como primeiro lote base: **98** repositórios de plugins no roster da família — **87** mantidos ativamente, **8** congelados e **3** retirados, ou seja, todas as entradas cujo `role` não é `infra` — e **1** atestado até agora. Ambas as contagens são derivadas, não digitadas à mão — `scripts/check-freshness.mjs` as afere, junto com os números em negrito desta frase, contra `dsh-plugin-kit/data/repos.json` e contra este registro. <!-- roster-count: 98 --><!-- attested-count: 1 -->
   - ⚠️ **Escopo destes 98, para que não seja lido como o tamanho da família** — a tabela autoritativa está no [README do `dsh-kit`, "Family counts — one definition per number"](https://github.com/PerryLink/dsh-kit#family-counts--one-definition-per-number). Este número é o arquivo de roster legível por máquina: 99 entradas lá = estes 98 mais a entrada de infraestrutura `dsh-plugin-kit`. **Não** são as tabelas familiares de 45 linhas (que mantêm as linhas retiradas e nunca apagam nenhuma) nem a lista de especificações de instalação (que exclui os membros retirados do roster).
4. Pilotar com outros autores de plugins de destaque
5. Propor a exibição do badge à lista canônica e aos marketplaces (apenas link, sem endosso)

**Linha da família DSH:** este repositório pertence à **família de plugins PerryLink DeepSeek Harness**, cuja linha atual é `dsh-v0.2.2-alpha.3`.

## PerryLink DSH Plugin Family

Este projeto é uma das **88 entradas** do DeepSeek Harness mantidas ativamente pela [PerryLink](https://github.com/PerryLink) (87 plugins + a linha de infraestrutura do kit). O roster da família soma **99** entradas no total — **8 congeladas**, **3 retiradas** — e permanece recolhido abaixo para que cada README mantenha uma primeira tela curta. Expanda-o para ver cada linha, com o motivo na coluna Status.

<details>
<summary>Expandir todos os plugins</summary>

| Plugin | Resumo | Status |
|---|---|---|
| **[dsh-aqua-input-check](https://github.com/PerryLink/dsh-aqua-input-check)** | 水产养殖投入品记录核对（按投入品用量、休药期与起捕间隔核对记录自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-archive-check](https://github.com/PerryLink/dsh-archive-check)** | 档案归档完整性与保管期限核对（依据《档案法》及配套规定，仅提示差异，不作出定性结论） |  |
| **[dsh-auto-review](https://github.com/PerryLink/dsh-auto-review)** | Second-model auto-review on the approval chain, fail-closed by default |  |
| **[dsh-autotier](https://github.com/PerryLink/dsh-autotier)** | Automatic strong/cheap model-tier routing with deterministic risk guards and a `/tier` command | 🧊 CONGELADO — ver o README do repositório |
| **[dsh-background-agents](https://github.com/PerryLink/dsh-background-agents)** | Durable background child agents with a Web UI sidebar, messaging and interrupt | 🚫 RETIRADO — ver o README do repositório |
| **[dsh-bid-ca-precheck](https://github.com/PerryLink/dsh-bid-ca-precheck)** | 投标文件符合性预检（按招标文件逐条核对响应情况，仅提示差异，不作出定性结论） |  |
| **[dsh-bid-qual-check](https://github.com/PerryLink/dsh-bid-qual-check)** | 投标人资格条件核对（按资格条件与证明材料核对逐条响应，仅提示差异，不作出定性结论） |  |
| **[dsh-budget](https://github.com/PerryLink/dsh-budget)** | Cost governance for DeepSeek Harness: budgets, carbon, and latency in one panel. | 🧊 CONGELADO — ver o README do repositório |
| **[dsh-cert-mcp](https://github.com/PerryLink/dsh-cert-mcp)** | Read-only MCP server exposing the certification registry: grades, snapshots and five-dimension evidence | 🧊 CONGELADO — ver o README do repositório |
| **[dsh-checkpoint-rewind](https://github.com/PerryLink/dsh-checkpoint-rewind)** | Claude Code /rewind-equivalent: snapshots, session forks, one-shot restore |  |
| **[dsh-claude-move](https://github.com/PerryLink/dsh-claude-move)** | Migrate Claude Code sessions, memory, skills and CLAUDE.md into DSH | 🧊 CONGELADO — ver o README do repositório |
| **[dsh-click](https://github.com/PerryLink/dsh-click)** | Cross-platform native desktop control for DeepSeek Harness — Windows first. |  |
| **[dsh-composer-history](https://github.com/PerryLink/dsh-composer-history)** | Terminal-style input history for the web composer: arrows, Ctrl+R search |  |
| **[dsh-contract-stance](https://github.com/PerryLink/dsh-contract-stance)** | 合同条款立场台账核对（按立场、风险等级与退让底线核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-customs-doc-check](https://github.com/PerryLink/dsh-customs-doc-check)** | 报关单证一致性核对（按公开的报关单填制规范核对单证间字段一致性，仅提示差异，不作出定性结论） |  |
| **[dsh-data-quality](https://github.com/PerryLink/dsh-data-quality)** | Dataset quality checks and citation cross-checks (the optional numeric bridge consumed here) |  |
| **[dsh-defend](https://github.com/PerryLink/dsh-defend)** | Prompt-injection, jailbreak, and secret-leak defense for DeepSeek Harness. | 🧊 CONGELADO — ver o README do repositório |
| **[dsh-demurrage-ledger](https://github.com/PerryLink/dsh-demurrage-ledger)** | 滞箱费台账核对（按免箱期与费率核对超期天数与金额的自洽性，仅提示差异，不作出定性结论） |  |
| **[dsh-doublecheck](https://github.com/PerryLink/dsh-doublecheck)** | Engineering-discipline guard: requirements grill, test gates, adversary review |  |
| **[dsh-draw](https://github.com/PerryLink/dsh-draw)** | Unified static-image generation routing for DeepSeek Harness. | 🧊 CONGELADO — ver o README do repositório |
| **[dsh-drill-script-check](https://github.com/PerryLink/dsh-drill-script-check)** | 应急演练脚本核对（按阶段、响应分级、指挥与资源要素核对脚本齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-eia-guide-check](https://github.com/PerryLink/dsh-eia-guide-check)** | 环评导则适用性核对（按要素、导则名称与执行标准核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-emergency-plan](https://github.com/PerryLink/dsh-emergency-plan)** | 应急预案要素齐备性与条款级页码定位（依据 GB/T 29639-2020 等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-essay-rubric-check](https://github.com/PerryLink/dsh-essay-rubric-check)** | 作文量表评分核对（按评分项、原文定位与分值算术核对评分自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-evidence-check](https://github.com/PerryLink/dsh-evidence-check)** | 证据清单齐备性核对（按待证事实与证据要素核对清单自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-export-ctl-check](https://github.com/PerryLink/dsh-export-ctl-check)** | 出口管制物项核对（按管制清单、最终用途与许可证核对台账齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-fast](https://github.com/PerryLink/dsh-fast)** | Read-only performance diagnostics for DeepSeek Harness. |  |
| **[dsh-fmea-table-check](https://github.com/PerryLink/dsh-fmea-table-check)** | FMEA 分析表要素齐备性与风险顺序数一致性核对（按公开的 FMEA 方法标准核对表内自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-forecast-penalty](https://github.com/PerryLink/dsh-forecast-penalty)** | 预测准确率考核核对（按准确率、门槛与考核单价核对考核费用的算术自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-fund-research](https://github.com/PerryLink/dsh-fund-research)** | Deterministic research reports for Chinese public mutual funds |  |
| **[dsh-github](https://github.com/PerryLink/dsh-github)** | GitHub PR/issues integration for DSH, every write gated by approval |  |
| **[dsh-gongwen-flow-check](https://github.com/PerryLink/dsh-gongwen-flow-check)** | 公文流转与办理时限核对（按公开的公文处理规范核对环节完整性与时限，仅提示差异，不作出定性结论） |  |
| **[dsh-gongwen-word-check](https://github.com/PerryLink/dsh-gongwen-word-check)** | 公文格式要素核对（按公开的党政机关公文格式标准核对版头与主体要素，仅提示差异，不作出定性结论） |  |
| **[dsh-guard-plan-qc](https://github.com/PerryLink/dsh-guard-plan-qc)** | 劳动保护与安全防护用品配置表核对（按公开的配备标准核对品名规格与配置数量，仅提示差异，不作出定性结论） |  |
| **[dsh-hazchem-check](https://github.com/PerryLink/dsh-hazchem-check)** | 危险化学品临界量与重大危险源辨识核对（按储存量与临界量核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-hazplan-check](https://github.com/PerryLink/dsh-hazplan-check)** | HAZOP 分析表要素齐备性与偏差推导一致性核对（依据公开的危险与可操作性分析标准，仅提示差异，不作出定性结论） |  |
| **[dsh-hidden-risk-map](https://github.com/PerryLink/dsh-hidden-risk-map)** | 安全隐患台账→《工贸企业重大事故隐患判定标准》（应急管理部令第10号）条款映射提示（仅提示差异，不作出定性结论） |  |
| **[dsh-hs-classify](https://github.com/PerryLink/dsh-hs-classify)** | 商品归类层级一致性核对（按章、品目、子目与商品编号的层级关系核对自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-icd-rule-check](https://github.com/PerryLink/dsh-icd-rule-check)** | ICD 合并编码与星剑号配对提示（依据 GB/T 14396-2016 与公开的 ICD 使用说明，仅提示差异，不作出定性结论） |  |
| **[dsh-industry-research](https://github.com/PerryLink/dsh-industry-research)** | Industry research orchestration that seals its deliverables through this plugin's `ctx.researchReport.assemble` |  |
| **[dsh-lawcite-adapter](https://github.com/PerryLink/dsh-lawcite-adapter)** | 法条引用与文书要素核对（按名称、条号、原文与效力状态核对引用可核验性，仅提示差异，不作出定性结论） |  |
| **[dsh-laya](https://github.com/PerryLink/dsh-laya)** | Laya typed decisions (`noul`/`choice`/`score`) as a first-class Cordis service and model-visible tools |  |
| **[dsh-lc-doc-check](https://github.com/PerryLink/dsh-lc-doc-check)** | 信用证单据条款核对（按单据种类、份数、期限与不符点核对交单自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-learning-gap-check](https://github.com/PerryLink/dsh-learning-gap-check)** | 能力差距与培养计划核对（按能力等级差、培养措施与完成期限核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-library](https://github.com/PerryLink/dsh-library)** | Local document knowledge base for DeepSeek Harness. |  |
| **[dsh-local-ai](https://github.com/PerryLink/dsh-local-ai)** | Local-model (Ollama) integration for DeepSeek Harness. |  |
| **[dsh-lsp-actions](https://github.com/PerryLink/dsh-lsp-actions)** | LSP diagnostics, formatting, completion, code actions and rename over language servers |  |
| **[dsh-mask](https://github.com/PerryLink/dsh-mask)** | PII masking middleware: anonymize at the model boundary, restore at the display layer |  |
| **[dsh-mcp-panel](https://github.com/PerryLink/dsh-mcp-panel)** | Read-only MCP runtime panel: /mcp command + Settings tab with status, tools and errors |  |
| **[dsh-medrec-qc](https://github.com/PerryLink/dsh-medrec-qc)** | 病案首页形式质控与逻辑矛盾提示（依据国卫办医发〔2016〕24号等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-memento](https://github.com/PerryLink/dsh-memento)** | Approval-gated cross-session memory: ctx.memory seam + SQLite + memory tool |  |
| **[dsh-nsfc-form-check](https://github.com/PerryLink/dsh-nsfc-form-check)** | 国家自然科学基金申请书形式审查与匿名化提示（依据《国家自然科学基金条例》与申请人导入的当年指南，仅提示差异，不作出定性结论） |  |
| **[dsh-nurse-record-check](https://github.com/PerryLink/dsh-nurse-record-check)** | 护理记录时限、签名与评估表完整性校验（依据《病历书写基本规范》等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-observe](https://github.com/PerryLink/dsh-observe)** | OpenTelemetry and Langfuse observability exporter for DeepSeek Harness. |  |
| **[dsh-origin-rvc-check](https://github.com/PerryLink/dsh-origin-rvc-check)** | 原产地区域价值成分核对（按 RVC 定义式与门槛核对原产地判定自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-ota-review-check](https://github.com/PerryLink/dsh-ota-review-check)** | 在线点评回复台账核对（按回复时限、问题分类与整改状态核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-output-styles](https://github.com/PerryLink/dsh-output-styles)** | Claude Code outputStyles-equivalent runtime style switching |  |
| **[dsh-paper-doc-adapter](https://github.com/PerryLink/dsh-paper-doc-adapter)** | 试卷解析结构核对（按题型、答案、分值与 yotta 标识核对试卷结构自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-permission-rules](https://github.com/PerryLink/dsh-permission-rules)** | Claude Code-style declarative allow/deny/ask permission rules with audit |  |
| **[dsh-permit-report-check](https://github.com/PerryLink/dsh-permit-report-check)** | 行政许可办理核对（按申请材料、法定时限与决定记录核对台账闭环，仅提示差异，不作出定性结论） |  |
| **[dsh-pipeline-check](https://github.com/PerryLink/dsh-pipeline-check)** | 项目全流程文件链条核对（按环节先后与金额口径核对链条自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-pleading-draft](https://github.com/PerryLink/dsh-pleading-draft)** | 起诉状要素齐备性核对（按诉请、事实、证据、法条四要素核对文书齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-plugin-doctor](https://github.com/PerryLink/dsh-plugin-doctor)** | Zero-dependency static + sandbox smoke detector for DSH plugins |  |
| **[dsh-plugin-guide](https://github.com/PerryLink/dsh-plugin-guide)** | Plugin-development knowledge base as an on-demand agent skill |  |
| **[dsh-plugin-kit](https://github.com/PerryLink/dsh-plugin-kit)** | Shared zero-runtime-dependency toolkit for the PerryLink DSH plugins |  |
| **[dsh-plugin-upgrade](https://github.com/PerryLink/dsh-plugin-upgrade)** | One-package, one-corridor-index plugin upgrade skill: routes a repository to the matching closed corridor card |  |
| **[dsh-policy-brief-draft](https://github.com/PerryLink/dsh-policy-brief-draft)** | 政策专报要素核对（按观点、依据、出处与建议核对专报齐备，仅提示差异，不作出定性结论） |  |
| **[dsh-power-loss-split](https://github.com/PerryLink/dsh-power-loss-split)** | 线损电量拆分与考核表核对（依据公开的线损计算口径，仅提示差异，不作出定性结论） |  |
| **[dsh-power-ticket-check](https://github.com/PerryLink/dsh-power-ticket-check)** | 电力工作票要素齐备性与签发许可顺序核对（依据现行电力安全工作规程，仅提示差异，不作出定性结论） |  |
| **[dsh-ppap-check](https://github.com/PerryLink/dsh-ppap-check)** | PPAP 提交要素齐备性核对（按公开的 PPAP 手册与提交等级核对要素齐备与版本填写，仅提示差异，不作出定性结论） |  |
| **[dsh-protest-deadline](https://github.com/PerryLink/dsh-protest-deadline)** | 质疑与投诉期限核对（按公开的政府采购质疑投诉规定核对期限与台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-railway-window](https://github.com/PerryLink/dsh-railway-window)** | 铁路施工天窗台账核对（按时间窗、时长算术与命令号核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-reach](https://github.com/PerryLink/dsh-reach)** | Multi-channel approval/question bridge: WeChat/Telegram/Feishu, session console | 🧊 CONGELADO — ver o README do repositório |
| **[dsh-repair-order-qc](https://github.com/PerryLink/dsh-repair-order-qc)** | 维修工单结算核对（按项目金额、配件工时合计与结算总额核对算术自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-research-report](https://github.com/PerryLink/dsh-research-report)** | Verifiable research-report engine: content-addressed evidence ledger and sealed versions |  |
| **[dsh-review-reply-check](https://github.com/PerryLink/dsh-review-reply-check)** | 审稿意见逐条回应覆盖核对（按意见与回应一一对应核对覆盖完整性，仅提示差异，不作出定性结论） |  |
| **[dsh-rulefile-check](https://github.com/PerryLink/dsh-rulefile-check)** | 规则库文件自检核对（按条款引用纪律与效力上限核对规则库自身合规，仅提示差异，不作出定性结论） |  |
| **[dsh-safety-brief-check](https://github.com/PerryLink/dsh-safety-brief-check)** | 安全技术交底记录要素齐备性与签字闭环核对（依据公开的安全生产法规与检查标准，仅提示差异，不作出定性结论） |  |
| **[dsh-score](https://github.com/PerryLink/dsh-score)** | Multi-dimensional quality scoring for DeepSeek Harness plugins. |  |
| **[dsh-session-pin](https://github.com/PerryLink/dsh-session-pin)** | Pin sessions in the Web sidebar with durable ordering | 🚫 RETIRADO — ver o README do repositório |
| **[dsh-session-sync](https://github.com/PerryLink/dsh-session-sync)** | Cross-device session sync for DeepSeek Harness — a dedicated git mirror of your session store. |  |
| **[dsh-site-log-check](https://github.com/PerryLink/dsh-site-log-check)** | 监理日志跨日连续性与旁站覆盖提示（依据 GB/T 50319-2013 等公开文件，仅提示差异，不作出定性结论） |  |
| **[dsh-skill-pack-security](https://github.com/PerryLink/dsh-skill-pack-security)** | Security-audit skill pack: secret scan, dependency and supply-chain review |  |
| **[dsh-soe-decision-check](https://github.com/PerryLink/dsh-soe-decision-check)** | 三重一大决策台账核对（按前置研究、会议审议与决议记录核对程序留痕，仅提示差异，不作出定性结论） |  |
| **[dsh-soilwater-check](https://github.com/PerryLink/dsh-soilwater-check)** | 土壤与地下水监测台账核对（按点位、监测项目、限值与超标判定核对台账自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-sop-sync-check](https://github.com/PerryLink/dsh-sop-sync-check)** | 作业规程与 FMEA／控制计划一致性核对（按编号与版本核对三份文件是否同步，仅提示差异，不作出定性结论） |  |
| **[dsh-spc-gbt-adapter](https://github.com/PerryLink/dsh-spc-gbt-adapter)** | 控制图常数与过程能力指数核对（按 GB/T 4091 的常规控制图常数表核对控制限与 Cp/Cpk 的自洽性，仅提示差异，不作出定性结论） |  |
| **[dsh-talk](https://github.com/PerryLink/dsh-talk)** | Voice-first session loop for DeepSeek Harness: talk to it, hear it answer. |  |
| **[dsh-team-rooms](https://github.com/PerryLink/dsh-team-rooms)** | Cross-session team rooms: shared message bus, task board and timeline | 🚫 RETIRADO — ver o README do repositório |
| **[dsh-tender-extract](https://github.com/PerryLink/dsh-tender-extract)** | 招标文件条款摘录核对（按出处与原文逐字核对摘录完整性，仅提示差异，不作出定性结论） |  |
| **[dsh-tender-matrix](https://github.com/PerryLink/dsh-tender-matrix)** | 评标因素评分矩阵核对（按分值上限与合计核对矩阵自洽，仅提示差异，不作出定性结论） |  |
| **[dsh-test-drive](https://github.com/PerryLink/dsh-test-drive)** | Isolated install-and-smoke test drives for DeepSeek Harness plugins. |  |
| **[dsh-ticktick](https://github.com/PerryLink/dsh-ticktick)** | TickTick/Dida365 task bridge: session-header panel + 11 tools |  |
| **[dsh-translate](https://github.com/PerryLink/dsh-translate)** | Vendor parameter translation and deterministic JSON repair for DeepSeek Harness. |  |
| **[dsh-warranty-calc](https://github.com/PerryLink/dsh-warranty-calc)** | 质保期与索赔金额核对（按质保期月数与里程、索赔金额算式核对自洽，仅提示差异，不作出定性结论） |  |

</details>

</details>

</details>

## Licença

MIT. Veja [LICENSE](./LICENSE).


## Dados HCB

[HCB](https://github.com/PerryLink/dsh-hcb) 0.12.0 — **136 checked + 7 tested / 3 FLOW `manual` / gate 2 blocked · 31 review · 109 pilot · 1 `production-candidate`** — checker **143/143 linhas em `dsh-standards@1.7.0`** — é a fonte canônica de compatibilidade / tracks / risco declarados. As 7 linhas testadas são `dsh-plugin-guide@0.5.9` (o único production-candidate) mais `dsh-budget@0.4.26`, `dsh-defend@0.3.28`, `dsh-mask@0.2.25`, `dsh-memento@0.5.31`, `dsh-observe@0.2.28`, `dsh-test-drive@0.3.26`; as outras 136 linhas não são testadas, então isso não torna o roster pronto para produção. O registro pode vincular linhas do HCB; **a certificação A–D e o portão HCB são ortogonais**.

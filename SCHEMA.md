---
type: guide
tags: [schema, wiki, knowledge-management]
updated: 2026-06-08
---

# SCHEMA —— 本知识库的操作规则

> 本库的结构和规则基于Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)的理念设计，目标是打造一个**适合心理学研究者使用的、可生长的**知识库。任何 Agent（Claude / Codex / 其它）执行 Ingest / Query / Lint 前，**必须先完整阅读本文件**。

## 0. 目标与边界

> [!info] **核心目标**
> 你负责维护本仓库中的 `wiki/` 知识层，并维护 `meta/` 中的索引与日志；包括创建页面、更新页面，以及按用户要求执行 Lint / 健康检查。将其打造成**可追溯、可复利、可生长**的知识库
> 
> - **可追溯**：每条重要知识都能回到来源页，并最终回到 `raw/` 原文或用户明确指定的 `Inbox/` 材料。
> - **可复利**：新页面要通过 wikilink、frontmatter、`meta/index.md` / `meta/log.md` 进入知识网络，方便后续查询和再利用。
> - **可生长**：允许先建立 stub，再随着新来源逐步补全；不要求一次写成最终版本。

> [!info] **边界**
> - `raw/` 是归档后的唯一事实来源，只读不改；用户明确指定用于 Ingest 的 `Inbox/` 材料也可作为事实依据。
> - 默认只在 `wiki/` 里创建、修改知识页面；维护索引和日志时，只修改 `meta/index.md` 与 `meta/log.md`。
> - 除 `wiki/` 与 `meta/` 外，不要修改其它目录；唯一例外是用户要求对 `Inbox/` 进行 Ingest 时，可按 §4.1 将已处理材料移动到 `raw/` 对应子目录。
> - 不做大规模自动改动；涉及批量重命名、移动、删除、结构调整时，先生成建议清单，经用户确认后再执行。
> - 不确定内容必须标 `needs-review`，不得写成定论。

  

## 1. 目录结构

```
raw/                  原始资料（只读不改）
  papers/             论文 PDF
  articles/           公众号文章 / 小红书 / 网页 Markdown
  notes/              课堂笔记 / 读书笔记 / 随想 idea（待 AI 提炼）
  media/              图片 / 视频素材
wiki/                 知识层（由 AI 根据 raw 提炼）
  sources/            单个来源的摘要页
  concepts/           概念页（按 kind 细分：理论/构念/测量/方法/概念，见 §2.5）
  entities/           实体页（人物、书籍、项目等实体页）
  comparisons/        比较、跨域页
  overview/           总览页（Overview）
meta/                 维护层（由 AI 维护，不作为知识来源）
  index.md            内容索引
  log.md              操作日志
Inbox/                待整理资料（仅在用户明确指定 Ingest 时读取，整理后移入 raw/）
```

### 1.1 `raw/notes/` 规则

`raw/notes/` 存放用户的课堂笔记、读书笔记、随想 idea 等原始输入，属于 raw 原始资料层。

- AI 只读不改；不得编辑、润色、重写或移动 `raw/notes/` 中的内容，除非用户明确要求。
- 需要 ingest 时，像处理论文一样读取笔记，把其中概念提炼进 `wiki/`，必要时新建 `sources/`、`concepts/`、`entities/` 等页面。
- 从 `raw/notes/` ingest 出来的 wiki 页面，`sources` 要链回对应 raw 笔记。
- `wiki/` 下不建 `notes/` 文件夹；wiki 只放从 raw 提炼出来的知识页。

## 2. 页面 frontmatter

所有 `wiki/` 下的 Markdown 页面都必须有 frontmatter。知识页面先写**通用字段**，再按页面类型添加专用字段；不要把不适用的专用字段硬塞进页面。

`type: overview` 是自动看板页，不承载具体知识来源；只需保留 `type`、`domain`、`summary`、`tags`、`updated` 等看板元数据，不强制填写 `source_type`、`status`、`sources`、`created`。

### 2.1 通用字段

```yaml
---
type: concept            # source | entity | concept | comparison | overview
domain: psychology       # ai | psychology | bridge
source_type:
  - real                 # real | ai-generated；通常只写一个
status: needs-review     # needs-review（待核实）| verified（已核实）
summary: "一句话核心"
sources: []
tags: []
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

- 新建页面时，`created` 和 `updated` 都写当天日期。
- 修改旧页面时，只更新 `updated`。
- AI 新建页面默认 `status: needs-review`；经用户肉眼核实后才可改 `verified`。AI 更新已有页面时按 §2.4 判断是否需要降级。
- `status: verified` 不代表内容来自现实资料；来源类型只由 `source_type` 表达。

### 2.2 按类型追加字段

`type: concept` 必须加：

```yaml
kind: construct          # theory | construct | method | measure | concept（兜底）；见 §2.5
```

`type: source` 建议加：

```yaml
feeds: []                # 本来源直接支撑 / 供料给哪些 wiki 知识页，如 [[攻击性]]
```

`feeds` 不是关键词；关键词写 `tags`。`feeds` 只写该 source 摘要明确服务的知识页，用于反向查询"哪些来源喂给这个概念 / 测验 / 对比页"。

`kind: measure` 必须加：

```yaml
operationalizes: []      # 本测量工具操作化了哪些 construct，如 [[攻击性]]
method: []               # 所属 method 节点，如 [[条件推理测验]]
```

如某个测验、方法或构念确实由某项理论 / 技术发展而来，可加：

```yaml
based_on: []             # 明确基于哪个 theory / method / technology，如 [[条件推理理论]]
```

`based_on` 是可选关系，不是完整性要求。只有存在明确的"由此发展而来 / 以此为理论或技术基础"关系时才填写；普通相关、同一研究脉络、或某个测验测量了某构念，都不要强行写 `based_on`。

其它类型不要添加 `kind`、`operationalizes`、`method` 等字段，除非后文规则明确要求。

### 2.3 `sources` 字段规则

`sources` 一律用 wikilink，不写纯文本路径。

- `wiki/sources/` 的 source 页：`sources` 链向 `raw/` 里的原始文件，PDF 必须带 `.pdf` 后缀，例如 `[[文件名.pdf]]`。
- `wiki/concepts/`、`wiki/entities/`、`wiki/comparisons/`：`sources` 链向 `wiki/sources/` 的来源摘要页，不带 `.md` 后缀，例如 `[[Conditional Reasoning as a Theoretical Perspective on Personality (2026)]]`。
- 文件名在库内应保持唯一；通常不写 `raw/papers/`、`wiki/sources/` 等路径前缀。

`feeds` 只用于 `wiki/sources/` 的 source 页：

- 一律用 wikilink 列表，指向本来源直接支撑、更新或供料的 `wiki/concepts/`、`wiki/entities/`、`wiki/comparisons/` 页面。
- `feeds` 表达"这篇来源主要喂给哪些知识页"，不是主题词穷举；只写主要目标页，不把文中所有提到的概念都塞进去。
- 若暂时不知道该来源会喂给哪些页面，可留空列表；不要为了完整性随意添加。

### 2.4 来源标注规则

`source_type` 和 `status` 管两件不同的事：

- **`source_type`（被 ingest 的资料是什么来源性质）**：
  - `real`：现实资料，如论文、公众号文章、网页、视频、访谈记录、用户自己写的原始笔记。
  - `ai-generated`：AI 生成或深度参与生成的资料，如用户与 AI 讨论后形成的研究现状总结、研究设计草案、idea 扩写文档。
  - 若同一页同时基于现实资料和 AI 生成资料，可同时写 `real` 与 `ai-generated`。
- **`status`（本 wiki 页面有没有经用户肉眼核实）**：`needs-review` / `verified`。
- AI ingest 后新建页面，默认 `status: needs-review`；只有用户肉眼看过并确认没问题后，才手动改为 `verified`。
- AI 更新已有页面时：
  - 若原页面已是 `verified`，且本次只是补充来源、例子、链接、事实性材料，不要把整页改回 `needs-review`；在 `meta/log.md` 记录本次新增内容即可。
  - 若本次改动涉及核心定义、分类、理论解释、争议判断、跨文献综合，或覆盖原有结论，则将页面改为 `status: needs-review`。
  - 若原页面本来就是 `needs-review`，继续保持 `needs-review`。
- `status: verified` 不会改变来源性质；例如 AI 生成资料被用户核实后，仍是 `source_type: ai-generated` + `status: verified`，不要因此往 `source_type` 里加 `real`。

- **硬规则**：`ai-generated` 的资料不得伪装成现实资料；拿不准时标 `needs-review`，并在正文说明不确定点。

### 2.5 kind：概念页的学科分类（心理学/科研骨架）
`type: concept` 的页面**必须**再标一个 `kind`，把泛泛的"概念"细分成研究本体里的角色（一小套、通用）：

| kind | 含义 | 例 |
|---|---|---|
| `theory` | 理论 / 理论视角 | 条件推理理论、社会认知理论 |
| `construct` | 要测量的潜在构念 / 特质 / 变量 | 攻击性、成就动机 |
| `method` | 测量方法 / 研究范式 / 采集技术 | 条件推理测验、评价中心、眼动技术 |
| `measure` | 具体的测验 / 工具 / 量表 / 问卷 | CRT-A、CRT-P、Buss-Perry 攻击问卷 |
| `concept` | 现象/框架/方法学概念等 | 作假、测量等价性 |

> [!info] 判断 `kind` 时按这个顺序：
> 1. 如果是具体命名的测验、量表、问卷、任务版本，选 `measure`。
> 2. 如果是测量方法、研究范式、采集技术，选 `method`。
> 3. 如果是理论或理论视角，选 `theory`。
> 4. 如果是研究中要解释、预测或测量的潜在变量 / 心理属性，选 `construct`。
> 5. 以上都不合适，选 `concept`。

> **原则**： `kind` 是定位标签，不是严格本体；拿不准时选 `concept`，不要为分类过度推断。
> **`construct` vs `concept`**：常被当作变量、维度、心理属性来测量或建模的，优先 `construct`；更像现象、原则、问题、方法学概念的，归 `concept`。例如"攻击性""成就动机"是 construct，"作假""测量等价性"是 concept。
> **`method` vs `measure`**：`method` 是测量方法 / 研究范式 / 采集技术这一层，如条件推理测验、SJT、自陈、眼动、ERP；`measure` 是该 method 下某一个具体测验，如 CRT-A、CRT-P、Buss-Perry。method 页是枢纽，具体测验用 `method` 字段链回它，见 §3.1。`method` 不再继续细分。

**三条可查询关系**（只给最有用的几条上字段，其余靠普通 `[[链接]]`）：
- `operationalizes`（写在 measure 上）：本测量工具操作化了哪个 / 哪些 **construct**，便于后续查询"哪些测验测量这个构念"。
- `based_on`（可写在 measure / method / construct 上）：它明确基于哪个 **theory / method / technology**。例如某 CRT 测验基于 `[[条件推理理论]]`，或某构念本身由某理论发展出来。没有明确"由此发展而来"关系时不要填写；不要因为普通相关、同属研究脉络、或某个测验测量了某构念，就给构念补 `based_on`。
- `method`（写在 measure 上）：本具体测验属于哪个 **method**，如 `"[[条件推理测验]]"`，便于后续查询"某个 method 下有哪些具体测验"。

> 这些字段用于关系查询和 Lint，不要求在每个 construct / method / theory 页内放 Dataview。需要自动列表时，可按具体页面需求另行添加。

## 3. 页面类型、目录与结构

本节决定页面落在哪个 `wiki/` 子目录、文件名怎么取、正文承担什么功能；frontmatter 字段写法见 §2。

| 类型         | 目录                  | 命名示例                                                                  | 内容要点                         |
| ---------- | ------------------- | ----------------------------------------------------------------------- | ---------------------------- |
| source     | `wiki/sources/`     | `A Conditional Reasoning Measure for Aggression (2005)`、`某公众号文章标题` | 来源信息、3–7 条要点、关键引文、关联链接 |
| concept    | `wiki/concepts/`    | `条件推理测验`、`攻击性`、`CRT-A`                                          | 定义、机制/步骤、应用、关联；用 `kind` 细分 |
| entity     | `wiki/entities/`    | `Lawrence R. James`、`某研究项目`、`某本书`                                | 只有当人物、机构、项目、书籍等本身需要反复引用或独立整理时才建 |
| comparison | `wiki/comparisons/` | `心理测量×AI评估`                                                        | 对象、相同点、不同点、结论；跨域页归此 |
| overview   | `wiki/overview/`    | `Psychology Overview`                                                    | 自动汇总、统计条、核实进度、卡片列表   |

> **通用命名原则**
> - 命名用自然名，不加类型前缀；不要写 `concept-攻击性`、`measure-CRT-A`。
> - 页面类型已由所在文件夹、frontmatter 的 `type`，以及 concept 页的 `kind` 表达。
> - 文件名稳定可读，优先使用研究者日后会搜索的名称。
> - 来源页文件名跟随来源本身的语言：英文文献用英文标题（不硬翻译），中文公众号/网页用中文标题。
>
> **来源页命名硬规则（必须执行）**
> - **英文论文 / 英文文章**：文件名一律用 **`Title (YYYY)`**。
>   - ✅ 正确：`A Conditional Reasoning Measure for Aggression (2005)`
>   - ✅ 正确：`Conditional Reasoning as a Theoretical Perspective on Personality (2026)`
>   - ❌ 错误：`James et al. 2005 - A Conditional Reasoning Measure for Aggression`
>   - ❌ 错误：`2005 - A Conditional Reasoning Measure for Aggression`
>   - ❌ 错误：`James - A Conditional Reasoning Measure for Aggression`
> - **中文来源**：文件名用**中文自然标题**；若日期对识别重要，可放末尾括号，如 `某公众号文章标题 (2024)`，但**不要**在前面加作者名/平台名作固定前缀，除非它本来就是标题的一部分。
> - **不要把作者名写进 source 文件名**，除非作者名本来就是标题的一部分。
> - **不要发明缩写标题**；优先用原始来源标题，可做轻微清理（如去掉文件系统不友好的符号），但不要改写成另一种引文格式。
>
> **Ingest 命名检查（落盘前必做）**
> - 新建 `wiki/sources/` 页面前，先检查文件名是否符合上面的规则。
> - 若是英文论文，**默认先假设正确文件名应为 `Title (YYYY)`，而不是作者-年份格式**。
> - 若发现自己写成了 `Author et al. YYYY - Title`、`YYYY - Title` 等引文式命名，**必须在完成 ingest 前立刻改正**。

### 3.1 measure 页结构规范

**一个具体测验 = 一个独立 measure 节点**。三层别混：

1. **测量方法 / 研究范式 / 采集技术** = `kind: method`，如 `条件推理测验`、`情境判断测验`、`自陈测验`、`眼动技术`、`ERP`。
2. **构念** = `kind: construct`，如 `权力动机`、`攻击性`。
3. **具体测验** = `kind: measure`，如 `CRT-P`、`CRT-A`、`Buss-Perry 攻击问卷`。

**硬性规则**：
- 每个 measure 必标 `method`，链回所属 method 节点，如 `[[条件推理测验]]`。
- 每个 measure 必标 `operationalizes`，链回它测量的 construct，如 `[[攻击性]]`。
- 若测验明确基于某 theory，再填 `based_on`，如 `[[条件推理理论]]`。
- measure 页要和 method 枢纽页分开：method 页放同一测量方法 / 研究范式 / 采集技术下的共性，measure 页放具体测验信息。

**建议正文小节**：
- **概况**：开发者 / 年份、题量、作答方式、计分方式。
- **测量构念与维度**：说明测量哪个 construct，并链过去。
- **题目/例题**
- **信度**
- **效度**：聚合 / 区分、效标 / 预测、增量。
- **作假抵抗**：链 [[作假]]。
- **跨文化/版本**：链 [[测量等价性]]。
- **局限/争议**
- **来源**

**测量构念与维度**：
- 若测验有维度结构，在「测量构念与维度」小节逐一列出维度名称，并链接其定义页（如有独立 construct/concept 页）。
- CRT 特例：CRT 没有维度，但其辩解机制（justification mechanisms, JM）可近似视为维度。CRT 类 measure 页应在「测量构念与维度」之后另起同级标题，固定写作 `## 辩解机制`，逐条摘录机制名称 + 一句话总结。

**题目/例题**：
- 文献中出现的公开题目 / 例题，应摘录到对应 measure 页的「题目/例题」小节。
- 同一测验的公开例题若散在多篇文献中，应尽量收齐，不要只放一道。
- 原文照录，不翻译。
- 绝不照录整套商业 / 受保护测验；只收已发表的公开题。
- 排版时去掉 PDF 硬换行：题干合成完整一段，每个选项用列表各占一行。

### 3.2 跨域页
AI × 心理 的交叉，作为一种 `comparison` 放 `wiki/comparisons/`，`domain: bridge`。

只有当连接能形成可复用的比较框架、研究问题、类比、假设或开放问题时，才新建跨域页；普通一句联想先写在相关 concept 页里，不单独立页。

基本写法：
- 用 `[[ ]]` 同时链向两边，如 `[[心理测量]] ↔ [[AI评估]]`。
- 正文至少写清：左域概念、右域概念、为什么能连、相似点、差异点、开放问题。
- 新发现的连接默认 `status: needs-review`。
- 若主要是 AI 综合或研究者 idea，`source_type` 用 `ai-generated`；若同时基于现实资料和 AI 综合，写 `real` + `ai-generated`。

### 3.3 领域总览页 Overview（需 Dataview + JS 查询）
`wiki/overview/` 下按需放领域总览页，用 DataviewJS 自动汇总对应 `domain` 的全部页面（统计条、核实进度、卡片列表）。Overview 页是自动看板，不手动维护条目；不要为尚未形成稳定内容的领域预建占位 overview。

- `Psychology Overview`：概念页按 `kind` 分组（理论 / 构念 / 测量工具 / 方法 / 概念），方便按心理学研究骨架浏览。
- 其它领域如需 overview，可仿照 `Psychology Overview` 新建；若该领域不适合按 `kind` 分组，可按 `type` 或更适合该领域的维度分组。
- 想要"跨领域看某一类"（如全部 measure）的特殊镜头时，按需求另建特殊看板；不必预先建一堆 kind 看板。

## 4. 工作流：Ingest / Query / Lint

### 4.1 Ingest（导入新资料）
当用户说"基于 `raw/xxx` ingest"或"对 `Inbox/xxx` ingest"时：

1. **读资料并确认 ingest 计划**：先读资料，提炼核心主题；在落盘前必须向用户简短确认拟建/拟改清单，包括：
   - 准备新建哪些页面；
   - 准备更新哪些已有页面；
   - 识别到哪些 theory / construct / method / measure；
   - 关键术语和页面命名；
   - 有哪些内容默认标 `needs-review`。
   用户确认或修正后，再创建 / 修改 wiki 页面。
2. **建立 / 更新 source 页**：在 `wiki/sources/` 新建或更新来源摘要页（深度随源伸缩：长论文写全，短视频两三行）。source 页命名必须符合 §3 的来源页命名规则。
3. **设置 frontmatter**：按 §2 填写 frontmatter。AI 新建页面默认 `status: needs-review`；更新已有页面时按 §2.4 判断是否需要降级。
4. **提炼知识节点**：据内容更新 / 新建相关 `concepts/`、`entities/`；跨域连接按 §3.2 判断是否写入 `comparisons/`。
5. **主动立节点**：文献里出现的理论 / 构念 / 测量方法 / 研究范式 / 采集技术 / 具体测验，只要是稳定、可独立指称的东西，就至少建一个 stub，并标好 `kind`。
6. **补关系字段**：给 measure 页补 `operationalizes`、`method` 等关系字段；`based_on` 只在确有理论 / 技术基础时填写，不因普通关联强行补齐。
7. **具体测验**：识别到 CRT-P、CRT-A、某问卷等具体测验时，按 §3.1 立独立 measure 节点；题目/例题按 §3.1 处理。
8. **实体页门槛**：不要因为论文出现作者名就自动建 entity；只有当人物、机构、项目、书籍本身是知识对象或会被反复引用时，才建 entity 页。
9. **维护 meta**：更新 `meta/index.md` 与 `meta/log.md`。
10. **处理 Inbox**：若源在 `Inbox/`，整理完移入 `raw/` 对应子目录；若源在 `raw/notes/`，只读不改，不移动。

### 4.2 Query（基于 wiki 回答）
当用户提出问题、要求综合已有知识，或让 AI 基于 wiki 回答时：

1. **定位候选页**：优先经 `meta/index.md`、frontmatter 的 `summary` / `tags` / `domain` / `kind` 定位候选页，再读取相关页面正文。
2. **必要时追溯来源**：默认基于 `wiki/` 回答；若 wiki 信息不足、用户要求核查原文，或涉及关键事实 / 争议判断，则顺着 `sources` 追溯到 `wiki/sources/` 或 `raw/`。
3. **标明可信状态**：综合时区分 `verified` 与 `needs-review`；来自 `needs-review` 页的内容不得写成定论。
4. **回答优先，不默认写回**：普通 Query 只回答，不修改文件。
5. **有价值的新综合**：若形成稳定、可复用的比较 / 分析 / 开放问题，先向用户提议写回 `comparisons/` 或相关 concept 页；确认后再写入，并标 `ai-generated` / `needs-review`。
6. **记录 log**：只有发生写回、结构调整、重要判断或用户明确要求记录时，才追加 `meta/log.md`；普通问答不记 log。

### 4.3 Lint（健康检查）
当用户要求 Lint / 健康检查时，只生成问题清单和修复建议，默认不直接改文件。

检查范围：
- **结构问题**：孤立页、缺失交叉引用、缺少 `meta/index.md` 条目、`meta/index.md` 中有已不存在页面。
- **frontmatter 问题**：知识页面缺字段、字段值不合法、`type: concept` 缺 `kind`、measure 缺 `method` / `operationalizes`；overview 例外见 §2。
- **来源问题**：`sources` 链接不符合 §2.3、`source_type` / `status` 使用不当。
- **内容问题**：矛盾、过时表述、`needs-review` 积压、不确定内容被写成定论。
- **节点问题**：回扫被埋没的 kind 节点；被反复提及却没有独立页的理论 / 构念 / 测量方法 / 研究范式 / 采集技术 / 具体测验，提议补建 stub。
- **source 命名违规**：扫描 `wiki/sources/` 下不符合 §3 来源页命名规则的文件，尤其是英文论文 / 英文文章没有采用 `Title (YYYY)` 的情况。

source 命名重点抓：
- `Author et al. YYYY - Title`
- `YYYY - Title`
- `Author - Title`
- 其它明显属于引文式、而非标题式的命名

输出格式：
- 按严重程度分组：必须修 / 建议修 / 可选优化。
- 每条建议说明：问题页面、问题类型、为什么是问题、建议动作。
- 涉及重命名时，先生成待重命名清单，并检查是否需要同步更新全库 wikilink；不要静默重命名。

**修复规则**：
- 默认只报告，不直接改。
- 经用户确认后再修复，并在完成后记 `meta/log.md`。

## 5. 按域策略（同流程，不同审查强度）
所有领域都走同一套流程：`raw/Inbox → wiki → meta`。区别只在 AI 的收敛程度和保守程度。

- **psychology**：用户专业域，最保守。定义、理论解释、测验结构、信效度、效标、争议等关键内容必须能追溯到来源；拿不准标 `needs-review`，不要把推测写成定论。
- **ai**：可做较积极的总结和结构化，但仍要标清 `source_type` / `status`，不要把二手资料或 AI 综合伪装成已核实事实。
- **bridge**：跨域连接默认更像假设 / 类比 / 开放问题；除非有明确来源支持，否则默认 `needs-review`。

## 6. 约定与风格

> [!tip] 操作原则
> 不确定时，先提议再执行，不做大规模自动改动。

- 内部链接用 `[[wikilink]]`；命名稳定可读。
- 查找笔记关系时，优先用 `rg`、frontmatter 和 wikilink 搜索；如本机已启用 Obsidian CLI，也可用它辅助查询。
- index/log 维护：`meta/index.md` 按 类型/领域 列每页一行；`meta/log.md` 以 `## [YYYY-MM-DD] <操作> | 摘要` 追加，不改旧条目。

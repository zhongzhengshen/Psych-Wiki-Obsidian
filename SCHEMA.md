---
type: guide
tags: [schema, wiki, knowledge-management]
updated: 2026-06-10
---

# SCHEMA —— 本知识库的操作规则

> 本库的结构和规则基于 Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 的理念设计，目标是打造一个**适合心理学研究者使用的、可生长的**知识库。任何 Agent（Claude / Codex / 其它）执行 Ingest / Query / Lint 前，**必须先完整阅读本文件**。

## 目录

- [[#0. 目标与边界|§0 目标与边界]] —— 你负责维护什么、不许碰什么
- [[#1. 目录结构|§1 目录结构]] —— 各文件夹的职责；raw 来源包怎么归档
- [[#2. 页面 frontmatter|§2 页面 frontmatter]] —— 字段写法；`origin` / `status` / `research_role` 的含义
- [[#3. 页面类型、命名与正文结构|§3 页面类型、命名与正文结构]] —— 页面放哪、文件名怎么取、正文写哪些小节
- [[#4. 工作流：Ingest / Query / Lint|§4 工作流]] —— Ingest / Query / Lint 三种任务的执行步骤
- [[#5. 按域策略|§5 按域策略]] —— 不同领域用不同的审查强度
- [[#6. 通用约定与 meta 维护|§6 通用约定与 meta 维护]] —— 链接、index、log 的维护格式

> 正文中的"见 §x.x"为纯文本引用，不做锚链接；仅本目录使用锚链接，标题改名时只需检查这一处。

## 0. 目标与边界

> [!info] **核心目标**
> 你负责维护本仓库中的 `wiki/` 知识层，并维护 `meta/` 中的索引与日志；包括创建页面、更新页面，以及按用户要求执行 Lint / 健康检查。将其打造成**可追溯、可复利、可生长**的知识库。
> 
> - **可追溯**：每条重要知识都能回到来源页，并最终回到 `raw/` 原文或用户明确指定的 `Inbox/` 材料。
> - **可复利**：新页面要通过 wikilink、frontmatter、`meta/index.md` / `meta/log.md` 进入知识网络，方便后续查询和再利用。
> - **可生长**：允许先建立 stub（雏形页 / 占位页：只含最低限度内容、待后续来源补全的页面，最低标准见 §4.1），再随着新来源逐步补全；不要求一次写成最终版本。stub 与正式页面的区别在展开程度，不在由谁创建；页面随 ingest 逐步从 stub 长成正式页面。

> [!info] **边界**
> - `raw/` 是归档后的唯一事实来源；归档后默认只读不改。用户明确指定用于 Ingest 的 `Inbox/` 材料也可作为事实依据，并可按 §1.2 / §4.1 归档或合并进 `raw/` 来源包。
> - 默认只在 `wiki/` 里创建、修改知识页面；维护索引和日志时，只修改 `meta/index.md` 与 `meta/log.md`。
> - 除 `wiki/` 与 `meta/` 外，不要修改其它目录；唯一例外是用户要求对 `Inbox/` 进行 Ingest，或补充同一来源的新载体时，可按 §1.2 / §4.1 将已处理材料移动、合并到 `raw/` 对应来源包。
> - 不做大规模自动改动；涉及批量重命名、移动、删除、结构调整时，先生成建议清单，经用户确认后再执行。
> - 不确定内容必须标 `needs-review`，不得写成定论。

## 1. 目录结构

```
raw/                  原始资料（归档后只读不改）
  papers/             论文来源包（PDF / Markdown / 图片 / 补充材料）
  articles/           文章来源包（公众号文章 / 小红书 / 网页 Markdown / 图片）
  notes/              课堂笔记 / 读书笔记 / 随想 idea（待 AI 提炼）
  media/              跨来源或独立媒体素材（不要存放某篇文献的配图）
wiki/                 知识层（由 AI 根据 raw 提炼）
  sources/            单个来源的摘要页
  concepts/           概念页（按 research_role 细分：理论/构念/测量/方法/概念，见 §2.5）
  entities/           实体页（人物、书籍、项目等实体页）
  comparisons/        比较、跨域页
  overview/           总览页（Overview）
meta/                 维护层（由 AI 维护，不作为知识来源）
  index.md            内容索引
  log.md              操作日志
Inbox/                待整理资料（仅在用户明确指定 Ingest 时读取，整理后移入 raw/）
```

结构图未列出的文件与目录（如 `Attachments/`、`README.md`、`AGENTS.md`、`sortspec.md` 等）与知识库流程无关：忽略它们，不要读取、修改，也不要在 Lint 中报告。

### 1.1 `raw/notes/` 规则

`raw/notes/` 存放用户的课堂笔记、读书笔记、随想 idea 等原始输入，属于 raw 原始资料层。

- AI 只读不改；不得编辑、润色、重写或移动 `raw/notes/` 中的内容，除非用户明确要求。
- 需要 ingest 时，像处理论文一样读取笔记，把其中概念提炼进 `wiki/`，必要时新建 `sources/`、`concepts/`、`entities/` 等页面。
- 从 `raw/notes/` ingest 出来的 wiki 页面，`sources` 要链回对应 raw 笔记。
- `wiki/` 下不建 `notes/` 文件夹；wiki 只放从 raw 提炼出来的知识页。

### 1.2 `raw/` 来源包规则

`raw/` 的基本归档单位是**一个来源包**，不是一个孤立文件。一个来源包对应一篇论文、一篇文章、一个网页、一个视频转写或一组用户明确指定为同一来源的材料；来源包可以包含 PDF、Markdown、图片、表格、补充材料等多个载体。

**硬性规则**：
- 新 ingest 的论文 / 文章默认在 `raw/papers/` 或 `raw/articles/` 下建立同名文件夹；即使用户只提供单个 PDF 或单个 Markdown，也要放进来源包文件夹。
- 若用户后来补充同一来源的其它载体（例如先前已有 PDF，后来又提供 MinerU 转出的 Markdown 和图片），必须合并进同一个来源包，不要散落为另一个 raw 文件或另一个来源。
- 来源包文件夹命名遵循 source 页命名规则：英文论文 / 英文文章用 `Title (YYYY)`；中文来源用中文自然标题，必要时在末尾加年份。
- 来源包内文件可保留原始文件名；若需要重命名以增强可追溯性，按以下约定命名：
  - 原版论文 / 文章 PDF 用 `Title (YYYY).pdf`，不加 `- fulltext`。
  - PDF 转出的全文 Markdown 用 `Title (YYYY) - fulltext.md`，避免和 `wiki/sources/Title (YYYY).md` 这个来源摘要页撞名。
  - 补充材料、附录、作者稿等按内容加描述后缀，如 `Title (YYYY) - supplementary.pdf`、`Title (YYYY) - appendix.pdf`、`Title (YYYY) - accepted-manuscript.pdf`。
- MinerU 等工具生成的图片目录和图片文件名可以保留原样，例如 `images/image-1.png`、`images/image-2.png`；不要为了美观强行改名。
- 图片、表格、附件必须放在同一来源包文件夹下；不得把同一篇文献的图片单独散放到 `raw/media/`。
- 旧库中已经直接放在 `raw/papers/` 的单 PDF 不要求立即批量迁移；Lint 可提出迁移建议，经用户确认后再整理。

示例：

```text
raw/papers/
  A Conditional Reasoning Measure for Aggression (2005)/
    A Conditional Reasoning Measure for Aggression (2005).pdf
    A Conditional Reasoning Measure for Aggression (2005) - fulltext.md
    images/
      image-1.png
      image-2.png
```

## 2. 页面 frontmatter

所有 `wiki/` 下的 Markdown 页面都必须有 frontmatter。知识页面先写**通用字段**，再按页面类型添加专用字段；不要把不适用的专用字段硬塞进页面。

`type: overview` 是自动看板页，不承载具体知识来源；只需保留 `type`、`domain`、`summary`、`tags`、`updated` 等看板元数据，不强制填写 `origin`、`status`、`sources`、`created`。

### 2.1 通用字段

```yaml
---
type: concept            # source | entity | concept | comparison | overview
domain: psychology       # 开放领域标签，如 psychology | ai | economics | sociology
origin:
  - solid                # solid | ai-generated；通常只写一个
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
- `status: verified` 不代表内容来自现实资料；来源性质只由 `origin` 表达。

### 2.2 按类型追加字段

`domain: psychology` 且 `type: concept` 的页面必须加：

```yaml
research_role: construct          # theory | construct | method | measure | concept（兜底）；见 §2.5
```

`research_role` 是心理学知识骨架的专用字段，不是所有领域的通用分类。非 `psychology` 领域的 concept 页默认不写 `research_role`；不要为了套格式把 AI、经济学、社会学等领域的概念强行归进 `theory` / `construct` / `method` / `measure`。

frontmatter 字段顺序固定为：先写 `type`、`domain`；若页面需要 `research_role`，紧跟在 `domain` 后；再写 `origin`、`status`、`summary`、`sources`、`feeds`、`method`、`tags`、`created`、`updated` 等其它字段。

`type: source` 建议加：

```yaml
feeds: []                # 本来源直接支撑 / 供料给哪些 wiki 知识页，如 [[攻击性]]
```

`feeds` 不是关键词；关键词写 `tags`。`feeds` 只写该 source 摘要明确服务的知识页，用于反向查询"哪些来源喂给这个概念 / 测验 / 对比页"。

心理学 `research_role: measure` 必须加：

```yaml
method: []               # 所属 method 节点，如 [[条件推理测验]]
```

测量了什么 construct、基于什么理论或技术，不再写入 frontmatter；放在正文的小节和 wikilink 中表达，例如「测量构念与维度」「理论基础」「来源」。

其它类型、以及非 `psychology` 领域的 concept 页，不要添加 `research_role`、`method` 等心理学专用字段，除非后文规则明确要求。

### 2.3 `sources` 与 `feeds` 字段规则

`sources` 一律用 wikilink，不写纯文本路径。后缀规则速查（写 `sources` 前先对照本表）：

| 哪个页面 | `sources` 指向 | 后缀 |
|---|---|---|
| `wiki/sources/` 的 source 页 | `raw/` 来源包内的原始文件 | **必须带** `.pdf` / `.md` |
| `wiki/concepts/` / `entities/` / `comparisons/` 页 | `wiki/sources/` 的来源摘要页 | **不带**后缀 |

#### `sources` 细则

- `wiki/sources/` 的 source 页：`sources` 链向 `raw/` 来源包里的主要原始文件。若有全文 Markdown，优先链接 `- fulltext.md`；若同时有 PDF，也应链接 `.pdf` 作为原版归档，例如 `[[A Conditional Reasoning Measure for Aggression (2005) - fulltext.md]]`、`[[A Conditional Reasoning Measure for Aggression (2005).pdf]]`。
- 若来源包只有 PDF，则链接 PDF，必须带 `.pdf` 后缀，例如 `[[文件名.pdf]]`；若来源包只有 Markdown，则链接 Markdown，必须带 `.md` 后缀。
- 图片、表格、附件通常不逐一写入 `sources`；除非某张图 / 表本身被重点引用，才在正文相应位置用 wikilink 或嵌入语法链接。
- `wiki/concepts/`、`wiki/entities/`、`wiki/comparisons/`：`sources` 链向 `wiki/sources/` 的来源摘要页，不带 `.md` 后缀，例如 `[[Conditional Reasoning as a Theoretical Perspective on Personality (2026)]]`。
- 纯 AI 综合 / 用户与 AI 讨论产生、没有对应 raw 来源的页面（`origin: ai-generated`），`sources` 允许为空列表 `[]`；Lint 不视为违规。一旦后续有实际来源支撑该页内容，应及时补链。
- 文件名在库内应尽量保持唯一；通常不写 `raw/papers/`、`wiki/sources/` 等路径前缀。若历史文件或同名附件导致歧义，可在正文说明来源包位置。

#### `feeds` 规则

`feeds` 只用于 `wiki/sources/` 的 source 页：

- 一律用 wikilink 列表，指向本来源直接支撑、更新或供料的 `wiki/concepts/`、`wiki/entities/`、`wiki/comparisons/` 页面。
- `feeds` 表达"这篇来源主要喂给哪些知识页"，不是主题词穷举；只写主要目标页，不把文中所有提到的概念都塞进去。
- 若暂时不知道该来源会喂给哪些页面，可留空列表；不要为了完整性随意添加。

### 2.4 来源标注规则

`origin` 和 `status` 管两件不同的事：

- **`origin`（被 ingest 的资料是什么来源性质）**：
  - `solid`：现实中可追溯的实际资料，如论文、公众号文章、网页、视频、访谈记录、用户自己写的原始笔记。
  - `ai-generated`：AI 生成或深度参与生成的资料，如用户与 AI 讨论后形成的研究现状总结、研究设计草案、idea 扩写文档。
  - 若同一页同时基于实际资料和 AI 生成资料，可同时写 `solid` 与 `ai-generated`。
- **`status`（本 wiki 页面有没有经用户肉眼核实）**：`needs-review` / `verified`。
- AI ingest 后新建页面，默认 `status: needs-review`；只有用户肉眼看过并确认没问题后，才手动改为 `verified`。
- AI 更新已有页面时：
  - 若原页面已是 `verified`，且本次只是补充来源、例子、链接、事实性材料，不要把整页改回 `needs-review`；在 `meta/log.md` 记录本次新增内容即可。
  - 若本次改动涉及核心定义、分类、理论解释、争议判断、跨文献综合，或覆盖原有结论，则将页面改为 `status: needs-review`。
  - 若原页面本来就是 `needs-review`，继续保持 `needs-review`。
  - **保守默认**：拿不准本次改动属于哪一类时，一律降级为 `needs-review`，并在 `meta/log.md` 说明降级理由。
- `status: verified` 不会改变来源性质；例如 AI 生成资料被用户核实后，仍是 `origin: ai-generated` + `status: verified`，不要因此往 `origin` 里加 `solid`。
- **硬规则**：`ai-generated` 的资料不得伪装成现实资料；拿不准时标 `needs-review`，并在正文说明不确定点。

### 2.5 research_role：心理学概念页的学科分类

`domain: psychology` 且 `type: concept` 的页面**必须**再标一个 `research_role`，把泛泛的"概念"细分成心理学研究本体里的角色（一小套、通用）。非 `psychology` 领域默认不使用本节分类。

| research_role | 含义 | 例 |
|---|---|---|
| `theory` | 理论 / 理论视角 | 条件推理理论、社会认知理论 |
| `construct` | 要测量的潜在构念 / 特质 / 变量 | 攻击性、成就动机 |
| `method` | 测量方法 / 研究范式 / 采集技术 | 条件推理测验、评价中心、眼动技术 |
| `measure` | 具体的测验 / 工具 / 量表 / 问卷 | CRT-A、CRT-P、Buss-Perry 攻击问卷 |
| `concept` | 现象/框架/方法学概念等 | 作假、测量等价性 |

> [!info] 判断 `research_role` 时按这个顺序：
> 1. 如果是具体命名的测验、量表、问卷、任务版本，选 `measure`。
> 2. 如果是测量方法、研究范式、采集技术，选 `method`。
> 3. 如果是理论或理论视角，选 `theory`。
> 4. 如果是研究中要解释、预测或测量的潜在变量 / 心理属性，选 `construct`。
> 5. 以上都不合适，选 `concept`。

补充判断规则：

- **原则**：`research_role` 是定位标签，不是严格本体；拿不准时选 `concept`，不要为分类过度推断。
- **注意同名不同层**：`research_role: concept` 与 `type: concept` 是两个不同层级的字段，值恰好同名；psychology 概念页两者**都要写**，不要因为 `type` 已是 `concept` 就略去 `research_role: concept`。
- **`construct` vs `concept`**：常被当作变量、维度、心理属性来测量或建模的，优先 `construct`；更像现象、原则、问题、方法学概念的，归 `concept`。例如"攻击性""成就动机"是 construct，"作假""测量等价性"是 concept。
- **`method` vs `measure`**：`method` 是测量方法 / 研究范式 / 采集技术这一层，如条件推理测验、SJT、自陈、眼动、ERP；`measure` 是该 method 下某一个具体测验，如 CRT-A、CRT-P、Buss-Perry。method 页是枢纽，具体测验用 `method` 字段链回它，见 §3.1。`method` 不再继续细分。

**一条可查询关系**（只给最稳定、最有用的关系上字段，其余靠正文 `[[链接]]`）：
- `method`（写在 measure 上）：本具体测验属于哪个 **method**，如 `"[[条件推理测验]]"`，便于后续查询"某个 method 下有哪些具体测验"。

> `method` 用于关系查询和 Lint，不要求在每个 construct / method / theory 页内放 Dataview。需要自动列表时，可按具体页面需求另行添加。

## 3. 页面类型、命名与正文结构

本节决定页面落在哪个 `wiki/` 子目录、文件名怎么取、正文承担什么功能；frontmatter 字段写法见 §2。

| 类型         | 目录                  | 命名示例                                                                  | 内容要点                         |
| ---------- | ------------------- | ----------------------------------------------------------------------- | ---------------------------- |
| source     | `wiki/sources/`     | `A Conditional Reasoning Measure for Aggression (2005)`、`某公众号文章标题` | 来源信息、3–7 条要点、关键引文、关联链接 |
| concept    | `wiki/concepts/`    | `条件推理测验`、`攻击性`、`CRT-A`                                          | 定义、机制/步骤、应用、关联；用 `research_role` 细分 |
| entity     | `wiki/entities/`    | `Lawrence R. James`、`某研究项目`、`某本书`                                | 只有当人物、机构、项目、书籍等本身需要反复引用或独立整理时才建 |
| comparison | `wiki/comparisons/` | `心理测量×AI评估`                                                        | 对象、相同点、不同点、结论；跨域页归此 |
| overview   | `wiki/overview/`    | `Psychology Overview`                                                    | 自动汇总、统计条、核实进度、卡片列表   |

#### 通用命名原则

- 命名用自然名，不加类型前缀；不要写 `concept-攻击性`、`measure-CRT-A`。
- 页面类型已由所在文件夹、frontmatter 的 `type`，以及 concept 页的 `research_role` 表达。
- 文件名稳定可读，优先使用研究者日后会搜索的名称。
- 来源页文件名跟随来源本身的语言：英文文献用英文标题（不硬翻译），中文公众号/网页用中文标题。

#### 来源页命名硬规则（必须执行）

- **英文论文 / 英文文章**：文件名一律用 **`Title (YYYY)`**。
  - ✅ 正确：`A Conditional Reasoning Measure for Aggression (2005)`
  - ✅ 正确：`Conditional Reasoning as a Theoretical Perspective on Personality (2026)`
  - ❌ 错误：`James et al. 2005 - A Conditional Reasoning Measure for Aggression`
  - ❌ 错误：`2005 - A Conditional Reasoning Measure for Aggression`
  - ❌ 错误：`James - A Conditional Reasoning Measure for Aggression`
- **中文来源**：文件名用**中文自然标题**；若日期对识别重要，可放末尾括号，如 `某公众号文章标题 (2024)`，但**不要**在前面加作者名/平台名作固定前缀，除非它本来就是标题的一部分。
- **不要把作者名写进 source 文件名**，除非作者名本来就是标题的一部分。
- **不要发明缩写标题**；优先用原始来源标题，可做轻微清理（如去掉文件系统不友好的符号），但不要改写成另一种引文格式。
- **在线先发表与正式刊期年份不一致时**：`YYYY` 用正式卷期 / Google Scholar 引用年份，不用 PDF 上标注的 advance online 年份；并在 source 页正文加一条年份说明，记录两个年份及取舍理由。
- **标题含非法字符时的清理约定**：冒号（`: `）一律替换为 ` - `（空格-连字符-空格）。
  - ✅ `Implicit and Explicit Personality: An Integrative Approach (2005)` → `Implicit and Explicit Personality - An Integrative Approach (2005)`
  - 其它文件系统不友好的符号（`/ \ ? * " < > |` 等）直接删除或用 `-` 替代；**不要**用全角符号（如 `：`）代替，不要让同一来源因清理方式不同产生两个文件名。
  - 本约定同样适用于 §1.2 的 raw 来源包文件夹及包内文件命名。

#### Ingest 命名检查（落盘前必做）

- 新建 `wiki/sources/` 页面前，先检查文件名是否符合上面的规则。
- 若是英文论文，**默认先假设正确文件名应为 `Title (YYYY)`，而不是作者-年份格式**。
- 若发现自己写成了 `Author et al. YYYY - Title`、`YYYY - Title` 等引文式命名，**必须在完成 ingest 前立刻改正**。

### 3.1 measure 页结构规范

**一个具体测验 = 一个独立 measure 节点**。三层别混：

1. **测量方法 / 研究范式 / 采集技术** = `research_role: method`，如 `条件推理测验`、`情境判断测验`、`自陈测验`、`眼动技术`、`ERP`。
2. **构念** = `research_role: construct`，如 `权力动机`、`攻击性`。
3. **具体测验** = `research_role: measure`，如 `CRT-P`、`CRT-A`、`Buss-Perry 攻击问卷`。

**硬性规则**：
- 每个 measure 必标 `method`，链回所属 method 节点，如 `[[条件推理测验]]`。
- measure 页要和 method 枢纽页分开：method 页放同一测量方法 / 研究范式 / 采集技术下的共性，measure 页放具体测验信息。
- 每个 measure 页正文必须写清测量构念；若有明确理论基础，也在正文写清并使用 wikilink。

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

### 3.2 construct / theory / method 页最低小节模板

适用于 `domain: psychology` 下对应 `research_role` 的页面（measure 页见 §3.1）。模板是**下限加建议**，不是上限：

- 标 ⭐ 的为必备小节；其余小节**有料才写**，没有内容就整节省略，不留空标题。
- 小节名按模板固定写法，便于跨页对照与 Lint；可按内容自由增加模板外的小节。
- stub 只需满足 §4.1 的 stub 最低标准；stub 充实为正式页面时，按本模板补齐必备小节。
- `research_role: concept`（兜底类）页面内容异质，不设模板。

**construct（构念）页**：
- ⭐ **定义**：构念是什么，一两句话核心定义；若文献存在不同定义取向，简列主要分歧。
- ⭐ **测量**：常用哪些 method / measure 测量本构念，用 wikilink 链过去（与 measure 页的「测量构念与维度」构成双向链）。
- **结构与维度**：单维还是多维；各维度名称 + 一句话说明。
- **行为表现**：典型行为指标与表现形式。
- **理论背景**：构念出自或主要服务于哪个理论，链 theory 页。
- **相关构念与区分**：与近邻构念的联系和区分（如攻击性 vs 愤怒 vs 敌意）。
- ⭐ **来源**

**theory（理论）页**：
- ⭐ **核心主张**：理论的基本命题与解释机制，用自己的话写 2–5 条。
- **发展脉络**：提出者与年代、关键修订、当前状态。
- **关键构念**：理论涉及或定义了哪些构念，链 construct 页。
- **衍生方法与测量**：基于该理论发展出的 method / measure，链过去。
- **证据与争议**：代表性支持证据、主要批评、未决问题。
- ⭐ **来源**

**method（方法）页**：
- ⭐ **定义与原理**：这是什么方法、靠什么机制得到测量或数据（如条件推理测验靠辩解机制驱动的选项偏好）。
- ⭐ **优势**
- ⭐ **局限**
- **适用场景**：擅长测什么、什么情况下选它。
- ⭐ **本方法下的具体测验**：列出库内该 method 下的 measure 页并链过去（method 页的枢纽功能，见 §2.5）；库内尚无对应 measure 页时可省略本节。
- **来源**：眼动、ERP 等采集技术页常为跨来源常识沉淀，故不设必备；有明确来源时应写。

### 3.3 跨域页

跨领域连接作为一种 `comparison` 放 `wiki/comparisons/`。不要另设专门的跨域占位值作为 `domain`；跨域性由页面类型、正文中的 wikilink、以及 tags 表达。

只有当连接能形成可复用的比较框架、研究问题、类比、假设或开放问题时，才新建跨域页；普通一句联想先写在相关 concept 页里，不单独立页。

`domain` 由页面的主要问题意识或最主要服务的知识域决定。例如 AI 面试评分若主要服务于人员测评 / 作假问题，可归 `psychology`；Prompt / Context / Harness Engineering 的比较可归 `ai`。若未来进入经济学、社会学等新领域，可直接使用新的简短领域标签，不需要先改 SCHEMA 枚举。

基本写法：
- 用 `[[ ]]` 同时链向两边，如 `[[心理测量]] ↔ [[AI评估]]`。
- 正文至少写清：左域概念、右域概念、为什么能连、相似点、差异点、开放问题。
- 新发现的连接默认 `status: needs-review`。
- 若主要是 AI 综合或研究者 idea，`origin` 用 `ai-generated`；若同时基于实际资料和 AI 综合，写 `solid` + `ai-generated`。

### 3.4 领域总览页 Overview（需 Dataview + JS 查询）

`wiki/overview/` 下按需放领域总览页，用 DataviewJS 自动汇总对应 `domain` 的全部页面（统计条、核实进度、卡片列表）。Overview 页是自动看板，不手动维护条目；不要为尚未形成稳定内容的领域预建占位 overview。

- `Psychology Overview`：概念页按 `research_role` 分组（理论 / 构念 / 测量工具 / 方法 / 概念），方便按心理学研究骨架浏览。
- 其它领域如需 overview，可仿照 `Psychology Overview` 新建；若该领域不适合按 `research_role` 分组，可按 `type` 或更适合该领域的维度分组。
- 想要"跨领域看某一类"（如全部 measure）的特殊镜头时，按需求另建特殊看板；不必预先建一堆 research_role 看板。

## 4. 工作流：Ingest / Query / Lint

### 4.1 Ingest（导入新资料）

当用户说"基于 `raw/xxx` ingest"或"对 `Inbox/xxx` ingest"时：

1. **读资料并确认 ingest 计划**：先读资料，提炼核心主题；在落盘前必须向用户简短确认拟建/拟改清单，包括：
   - 准备新建哪些页面；
   - 准备更新哪些已有页面；
   - 识别到哪些 theory / construct / method / measure；
   - 关键术语和页面命名；
   - 有哪些内容默认标 `needs-review`；
   - 拟执行的 raw 归档 / Inbox 移动计划（移动文件属于不易撤销的操作，纳入确认范围）。
   用户确认或修正后，再创建 / 修改 wiki 页面。
   **批量 ingest**（一次提供多份资料）时，可一次性提交整批的拟建 / 拟改清单，用户批准后逐篇执行，无需逐篇再次确认；执行中若发现与计划明显不符的情况，再回头向用户确认。
2. **归档 / 合并来源包**：按 §1.2 在 `raw/` 对应目录建立来源包文件夹；若同一来源已有来源包，把新提供的 PDF / Markdown / 图片 / 附件合并进去。不要只更新 wiki stub 而遗漏 raw 归档。
3. **选择读取优先级**：若来源包里有 Markdown，优先解析 Markdown；若 Markdown 引用图片，应同时查看相关图片，尤其是模型图、流程图、量表题目、表格、关键图注。PDF 作为原版核查和兜底：当 Markdown 疑似缺页、乱码、图表缺失、题目 / 表格不完整，或用户要求核对原文时，再返回 PDF。
4. **建立 / 更新 source 页**：在 `wiki/sources/` 新建或更新来源摘要页（深度随源伸缩：长论文写全，短视频两三行）。source 页命名必须符合 §3 的来源页命名规则，并在 `sources` 中链接来源包里的主要原始文件。
5. **设置 frontmatter**：按 §2 填写 frontmatter。AI 新建页面默认 `status: needs-review`；更新已有页面时按 §2.4 判断是否需要降级。
6. **提炼知识节点**：据内容更新 / 新建相关 `concepts/`、`entities/`；跨域连接按 §3.3 判断是否写入 `comparisons/`。
7. **主动立节点**：文献里出现的理论 / 构念 / 测量方法 / 研究范式 / 采集技术 / 具体测验，只要是稳定、可独立指称的东西，就至少建一个 stub，并标好 `research_role`。
   - **stub 最低标准**（是下限，不是模板）：完整 frontmatter + `summary` + 正文至少一句话定义 + 至少一个 wikilink（通常链回 source 页或相关概念）。不得创建空正文页面。
   - stub 及一般知识页**不设统一正文模板**（心理学 research_role 页除外：measure 见 §3.1，construct / theory / method 见 §3.2）；正文结构随资料类型和内容侧重伸缩。
8. **补关系字段**：给 measure 页补 `method` 字段；测量构念、理论基础等关系写入正文小节并用 wikilink 表达。
9. **具体测验**：识别到 CRT-P、CRT-A、某问卷等具体测验时，按 §3.1 立独立 measure 节点；题目/例题按 §3.1 处理。
10. **实体页门槛**：不要因为论文出现作者名就自动建 entity；只有当人物、机构、项目、书籍本身是知识对象或会被反复引用时，才建 entity 页。
11. **维护 meta**：更新 `meta/index.md` 与 `meta/log.md`。
12. **处理 Inbox / notes**：若源在 `Inbox/`，整理完移入或合并到 `raw/` 对应来源包；若源在 `raw/notes/`，只读不改，不移动。

### 4.2 Query（基于 wiki 回答）

当用户提出问题、要求综合已有知识，或让 AI 基于 wiki 回答时：

1. **定位候选页**：优先经 `meta/index.md`、frontmatter 的 `summary` / `tags` / `domain` / `research_role` 定位候选页，再读取相关页面正文。
2. **必要时追溯来源**：默认基于 `wiki/` 回答；若 wiki 信息不足、用户要求核查原文，或涉及关键事实 / 争议判断，则顺着 `sources` 追溯到 `wiki/sources/` 或 `raw/`。
3. **wiki 无相关内容时**：可基于模型自身知识回答，但必须明确声明"以下内容不来自库内资料"；模型知识与库内知识混用时，分别标明哪些有库内来源支撑。此类回答不自动写回 wiki（写回仍走第 7 条的提议-确认流程）。
4. **raw 读取优先级**：追溯到 raw 来源包时，优先检索 / 阅读 Markdown；若问题涉及图片、模型、表格、量表题目或 Markdown 中的图片引用，同步查看来源包里的相关图片。只有在 Markdown 不足、疑似转换错误、缺失原文细节，或需要核对原版时，再读取 PDF。
5. **标明可信状态**：综合时区分 `verified` 与 `needs-review`；来自 `needs-review` 页的内容不得写成定论。
6. **回答优先，不默认写回**：普通 Query 只回答，不修改文件。
7. **有价值的新综合**：若形成稳定、可复用的比较 / 分析 / 开放问题，先向用户提议写回 `comparisons/` 或相关 concept 页；确认后再写入，并标 `origin: ai-generated` / `status: needs-review`。
8. **记录 log**：只有发生写回、结构调整、重要判断或用户明确要求记录时，才追加 `meta/log.md`；普通问答不记 log。

### 4.3 Lint（健康检查）

当用户要求 Lint / 健康检查时，只生成问题清单和修复建议，默认不直接改文件。

检查范围：
- **结构问题**：孤立页、缺失交叉引用、缺少 `meta/index.md` 条目、`meta/index.md` 中有已不存在页面。
- **frontmatter 问题**：知识页面缺字段、字段值不合法、`domain: psychology` 的 concept 页缺 `research_role`、心理学 measure 缺 `method`、非 psychology 页误用心理学专用字段；overview 例外见 §2。
- **来源问题**：`sources` 链接不符合 §2.3、`origin` / `status` 使用不当。
- **来源包问题**：新来源未按 §1.2 建立来源包、同一来源的 PDF / Markdown / 图片散落在不同位置、MinerU 图片未随 Markdown 归档、source 页没有链接来源包中的主要原始文件。
- **内容问题**：矛盾、过时表述、`needs-review` 积压、不确定内容被写成定论。
- **节点问题**：在 `domain: psychology` 范围内回扫被埋没的 research_role 节点；被反复提及却没有独立页的理论 / 构念 / 测量方法 / 研究范式 / 采集技术 / 具体测验，提议补建 stub。
- **模板问题**：已展开（具备多个正文小节）的心理学 measure / construct / theory / method 页缺少 §3.1 / §3.2 的必备小节；此类问题归"建议修"档。stub 不按模板检查。
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
- 批量重命名 / 全库 wikilink 同步时：仅因链接同步被被动改动的页面**不更新** `updated`（避免一次重命名把几十个页面的 `updated` 刷成同一天，破坏该字段的信息量）；log 不逐页记录，记一条汇总即可（重命名清单 + 受影响页面数）。

**修复规则**：
- 默认只报告，不直接改。
- 经用户确认后再修复，并在完成后记 `meta/log.md`。

## 5. 按域策略

开放领域，同一流程，不同审查强度。所有领域都走同一套流程：`raw/Inbox → wiki → meta`。`domain` 是开放领域标签，不是固定枚举；`psychology`、`ai` 只是当前主要使用的例子。未来进入经济学、社会学等新内容时，AI 应根据材料主题和用户问题选择简短、稳定、可复用的领域标签。

- **psychology**：用户专业域，最保守。定义、理论解释、测验结构、信效度、效标、争议等关键内容必须能追溯到来源；拿不准标 `needs-review`，不要把推测写成定论。心理学 concept 页按 §2.5 使用 `research_role`。
- **ai**：当前重要辅助领域，可做较积极的总结和结构化，但仍要标清 `origin` / `status`，不要把二手资料或 AI 综合伪装成已核实事实。AI concept 页默认不写 `research_role`。
- **其它未来领域**：先使用该领域的自然名称作为 `domain`，保持轻量；除非用户明确要求，不要为新领域预设一套复杂 research_role 分类。
- **跨域连接**：用 `type: comparison`、正文链接和 tags 表达；`domain` 仍选择最主要服务的领域，不另设专门的跨域占位值。

## 6. 通用约定与 meta 维护

> [!tip] 操作原则
> 不确定时，先提议再执行，不做大规模自动改动。

#### 链接与搜索

- 内部链接用 `[[wikilink]]`；命名稳定可读。
- 查找笔记关系时，优先用 `rg`、frontmatter 和 wikilink 搜索；如本机已启用 Obsidian CLI，也可用它辅助查询。

#### index 维护

- `meta/index.md` 按类型分节、每页一行，行格式为 `- [[页面名]] —（域·research_role 中文缩写）一句话摘要`，例如 `- [[攻击性]] —（心理·构念）伤害他人的倾向/动机；由 CRT-A 内隐测量`；无 `research_role` 的页面只写域，如 `（心理）`、`（AI）`。
- **追加新行前必须先查看 index 中已有行的格式，并与之保持一致，不要发明新格式。**

#### log 维护

- `meta/log.md` 以 `## [YYYY-MM-DD] <操作> | 摘要` 追加，不改旧条目。
- `<操作>` 使用以下固定枚举（首字母大写），不要发明新词或用 `+` 拼接；一次操作涉及多类时选最主要的一个，细节写进摘要和正文：
  - `Ingest`：导入新资料（含 raw 归档、source / concept 等页面的创建与更新）。
  - `Update`：非 ingest 触发的知识页内容修改、Query 写回、补充材料。
  - `Lint`：健康检查报告及经用户确认的修复。
  - `Rename`：页面 / 文件重命名、移动及全库 wikilink 同步。
  - `Schema`：SCHEMA 规则变更。
  - `Maintain`：index / log / 结构层面的维护，如清理、回填、重组。
- 词表生效前的旧条目保持原样，不回头改写。

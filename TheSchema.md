---
type: guide
tags: [schema, wiki, knowledge-management]
updated: 2026-06-06
---

# TheSchema —— 本知识库的操作规则

> 本库的结构和规则基于Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)的理念设计，任何 Agent（Claude / Codex / 其它）执行 Ingest / Query / Lint 前，必须先完整读本文件。

## 0. 目标与边界

> [!info] 核心目标
> 维护 `wiki/` 目录，把它打造成**可重复利用的知识层**。

- **你负责** `wiki/` 里的页面：创建、更新、补链接、查矛盾这些"记账"活。
- **边界**：
  - 原始资料（`raw/`）是唯一事实来源，**只读不改**。
  - 只在 `wiki/` 里创建/修改 Markdown。
  - 不要动 `.obsidian/`、`Attachments/` 等其它目录。

## 1. 目录结构

```
raw/                  原始资料（你输入；AI 只读不改）
  papers/             论文 PDF
  articles/           公众号 / 小红书 / 网文
  notes/              你自己写的：上课 / 读书 / 随想 idea（属原始资料，待 AI 提炼）
  media/              图片 / 视频素材
wiki/                 知识层（AI 据 raw 提炼，你不必手写）
  index.md            内容索引
  log.md              操作日志
  sources/            来源摘要页
  concepts/           概念页（方法、理论、模型）
  entities/           实体页（人物、工具、测验、书）
  comparisons/        比较页 + 跨域页
  overview/           总览（Overview）+ 综述
Inbox/                随手丢、还没整理的素材
```

> **领域不进文件夹**：AI / 心理 等领域**不靠目录区分**，而靠 frontmatter 的
> `domain` 字段 + 标签（见 §2）。这样文件夹保持扁平、查资料不必层层下钻——
> 这是本库相对 blink 的关键定制，专为"好查"而设。

## 2. 页面 frontmatter

```yaml
---
type: concept            # source | entity | concept | comparison | overview
domain: psychology       # ai | psychology | bridge —— 用元数据代替领域文件夹
source_type:             # 列表：real（现实资料，含你 raw/notes 的笔记）/ ai-generated（AI 生成）
  - real                 #    一般只一个值；"现实资料+AI 综合"才写两个
status: verified          # verified（已核实）| needs-review（待核实）
summary: "一句话核心"
sources: ["[[某来源页]]"]   # 指回来源页或 raw/ 路径
tags: [tag1, tag2]
created: YYYY-MM-DD
updated: YYYY-MM-DD
---
```

### 2.1 来源标注制度

`source_type` 和 `status` 管两件不同的事，别混：
- **`source_type`（内容从哪来）**：`real`(源自现实资料——论文/文章，**也包括你 `raw/notes/` 里的笔记**) / `ai-generated`(AI 自己综合的)。是列表，通常只一个值；只有"现实文献 + AI 综合"才写两个 `[real, ai-generated]`。
- **`status`（有没有经你核实）**：`needs-review` / `verified`。
- 所以"**AI 生成、但我改过/核实过**"= `source_type: ai-generated` + `status: verified`，**不要**因此往 source_type 里加 `real`。

每页正文顶部还要加**可见徽标**：
- 现实资料：`> [!quote] 来源：<出处>（YYYY-MM-DD）`
- AI 生成 / 待核实：`> [!robot] AI 生成 · 待核实` + 说明源自哪次对话
- **硬规则**：`ai-generated` 的内容不得伪装成已证实事实，徽标必须保留，默认 `status: needs-review`；人核实后改 `verified`。

## 3. 页面类型与命名

> **命名用自然名，不加类型前缀**（类型已由"所在文件夹 + frontmatter 的 `type`"表达）。
> **来源页文件名跟随来源本身的语言**：英文文献用英文标题（不硬翻译），中文公众号/网页用中文标题。

| 类型         | 目录                  | 命名示例                                        | 内容要点                   |
| ---------- | ------------------- | ------------------------------------------- | ---------------------- |
| source     | `wiki/sources/`     | `Attention Is All You Need (2017)`、`某公众号-提示词工程` | 来源信息、3–7 条要点、关键引文、关联链接 |
| concept    | `wiki/concepts/`    | `费曼学习法`                                      | 定义、机制/步骤、应用、关联         |
| entity     | `wiki/entities/`    | `马斯克`、`Transformer`                         | 基本信息、特征、相关事件、来源        |
| comparison | `wiki/comparisons/` | `RAG vs 微调`                                  | 对象、相同点、不同点、结论；跨域页归此   |
| overview   | `wiki/overview/`    | `Psychology Overview`、`儿童自驱学习综述`            | 一句话结论、框架、支撑来源、未决问题     |

### 3.1 跨域页
AI × 心理 的交叉，作为一种 `comparison` 放 `wiki/comparisons/`，`domain: bridge`。
套路：`左域概念 ↔ 右域概念 ↔ 为什么能连 + 开放问题`，用 `[[ ]]` 同时链向两边。
新发现的连接默认 `needs-review`，等人核实。

### 3.2 自动领域总览 Overview（需 Dataview + JS 查询）
`wiki/overview/` 下放 `Psychology Overview`、`AI Overview`，用 DataviewJS 自动按类型汇总对应
`domain` 的全部页面（统计条 + 进度条 + 卡片）。扁平结构下也能"按领域浏览"，新页自动上榜，无需手动维护。
新增领域时复制一页、改顶部 `const DOMAIN = "..."` 一行即可。

### 3.3 你自己的笔记 → `raw/notes/`（不是 wiki）
上课 / 读书 / 随想 idea 是**你的原始输入、尚未经 AI 提炼**，按 Karpathy 理念属于原始资料，放 **`raw/notes/`**：
- 你写你改，**AI 只读不改**（和其它 raw 一样不可变）。
- 需要时像处理论文一样 ingest：AI 读你的笔记，把其中概念提炼进 `wiki/concepts/`（必要时建 `entities/`、`sources/` 摘要），并把 `sources` 链回该 raw 笔记。
- 因此 **wiki 里没有 notes 文件夹**——wiki 全是从 raw 提炼出来的东西。

## 4. 工作流：Ingest / Query / Lint

### 4.1 Ingest（导入新资料）
我说"基于 `raw/xxx` 或 Inbox ingest"时：
1. 读资料，提炼要点，**先与我简短确认重点**。
2. 在 `wiki/sources/` 新建/更新来源摘要页（深度随源伸缩：长论文写全，短视频两三行）。
3. 据内容更新/新建相关 `concepts/`、`entities/`；跨域则在 `comparisons/` 记一笔。
4. 更新 `wiki/index.md`（补条目）与 `wiki/log.md`（追加一行）。
5. 若源在 `Inbox/`，整理完移入 `raw/` 对应子目录。

### 4.2 Query（基于 wiki 回答）
1. 经 `index.md` / frontmatter 的 `summary` / 标签定位候选页 → 读 → 综合回答。
2. 有价值的新综合（比较/分析）可写回 `comparisons/` 或 `overview/`（标 `ai-generated`/`needs-review`）。
3. 重要动作记 `log.md`。

### 4.3 Lint（健康检查）
扫描矛盾、过时表述、孤立页、被反复提及却无独立页的概念、缺失交叉引用、`needs-review` 积压。
**生成建议清单，不直接大改**；经我确认后再动手并记 `log.md`。

## 5. 按域策略（同流程，不同审查强度）
两个域**都**走"raw → 你解读 → wiki"同一套流程，区别只在收敛程度：
- **AI 域**（二手知识）：可放开总结。
- **心理域**（用户的专业）：**更保守**——必引 raw 出处，拿不准标 `needs-review`，不把推测写成定论；用户会认真复核。

## 6. 约定与风格

> [!tip] 操作原则
> 不确定时，先提议再执行，不做大规模自动改动。

- 内部链接用 `[[wikilink]]`；命名稳定可读。
- 查找笔记关系时优先用 obsidian-cli（双链/标签/frontmatter 查询比纯文本搜索精确）。
- index/log 维护：`index.md` 按 类型/领域 列每页一行；`log.md` 以 `## [YYYY-MM-DD] <操作> | 摘要` 追加，不改旧条目。

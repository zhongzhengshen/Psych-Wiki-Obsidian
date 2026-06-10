# Psych-Wiki-Obsidian — 给心理学研究者的 AI 维护知识库

**简体中文** | [English](README.en.md)

> 你往 `raw/` 丢论文，AI 把它炼成一张可生长的知识网络——**AI 负责记账，你负责判断。**

这是一个基于 [Karpathy 的 LLM Wiki 构想](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)、**专为心理学研究者设计**的 Obsidian 知识库框架。它解决的问题很具体：文献读了就忘、笔记散落各处、AI 总结的内容不敢直接信。这个库用一套写给 AI 看的规则（[SCHEMA.md](SCHEMA.md)），让任何 agent（Claude Code / Codex / …）都能按同一套标准替你维护知识，同时把"信任判断权"牢牢留在你手里。

![三层架构与数据流](assets/fig-1-architecture.png)

## ✨ 三个核心特性

1. **心理学研究骨架**——概念不是平铺的，而是按 `theory / construct / method / measure` 四种研究角色组织，和你做研究的思维方式同构。
2. **双轨信任模型**——AI 写的每一页都标明"来源是什么性质"（`origin`）和"你核实过没有"（`status`），AI 生成内容永远不能伪装成已核实事实。
3. **一切可追溯**——每条知识都能沿着 wikilink 一路回溯：概念页 → 来源摘要页 → `raw/` 里的 PDF 原文。

## 🏛 设计理念

### 三层架构

| 层 | 职责 | 谁来写 |
|---|---|---|
| `raw/` | 原始资料归档，**只进不改**，是唯一事实来源 | 你 |
| `wiki/` | 从 raw 提炼出的知识层（来源摘要、概念、实体、比较、总览） | AI |
| `meta/` | 索引与操作日志，全库的目录和时间线 | AI |

### 三个原则

- **可追溯**：每条重要知识都能回到来源页，并最终回到 `raw/` 原文。
- **可复利**：新页面通过 wikilink、frontmatter、索引进入知识网络，越用越值钱。
- **可生长**：允许先建只有一句话定义的雏形页（stub），随新文献逐步长成正式页面，不要求一次写完。

### 一部"宪法"

[SCHEMA.md](SCHEMA.md) 是唯一权威规则文件：目录怎么组织、页面怎么命名、frontmatter 怎么填、AI 在什么情况下必须先问你——全部写死在里面。任何 agent 接手前必须先完整读它。换 AI 不换规则，知识库的一致性不依赖任何特定工具。

## 🔬 心理学特色：研究角色骨架

这是本库与通用知识库最大的差别。心理学研究者面对一篇文献时，脑子里天然区分四种东西——理论、构念、方法、测验。本库把这个区分做进了知识结构：每个心理学概念页都必须标注一个 `research_role`：

![心理学知识骨架](assets/fig-2-psychology-backbone.png)

| research_role | 含义 | 例子 |
|---|---|---|
| `theory` | 理论 / 理论视角 | 条件推理理论、社会认知理论 |
| `construct` | 要测量的潜在构念 / 特质 / 变量 | 攻击性、成就动机 |
| `method` | 测量方法 / 研究范式 / 采集技术 | 条件推理测验、评价中心、眼动技术 |
| `measure` | 具体的测验 / 工具 / 量表 | CRT-A、Buss-Perry 攻击问卷 |
| `concept` | 兜底：现象 / 框架 / 方法学概念 | 作假、测量等价性 |

四种角色由可查询的关系连成网。以本库现有内容为例：

> **条件推理理论**（theory）主张人格塑造无意识的辩解机制 → 这些机制让**攻击性**（construct）变得可测 → 测它的具体工具是 **CRT-A**（measure）→ CRT-A 隶属于**条件推理测验**（method）这个方法枢纽，和 CRT-P、CRT-RMS 等兄弟测验共享开发规范。

这套骨架带来的实际好处：

- **按研究思维浏览**：总览看板自动按角色分组，找"所有测攻击性的工具"或"条件推理测验下有哪些具体量表"是一次查询的事。
- **每种角色有最低模板**：construct 页必有"定义 / 测量 / 来源"，measure 页必有"测量构念与维度 / 信效度 / 题目例题"，theory 页必有"核心主张"——AI 写页时按模板补齐，不同文献炼出的页面结构可对照。
- **拿不准就兜底**：分类有明确的判断顺序，判断不了的归 `concept`，不为分类过度推断。

## 🔒 双轨信任模型

AI 时代知识管理最大的坑：AI 写的东西看起来都很对。本库用两个正交字段把"来源性质"和"核实状态"分开管理：

![双轨信任模型](assets/fig-3-trust-matrix.png)

| | `status: needs-review` | `status: verified` |
|---|---|---|
| **`origin: solid`**（真实资料） | AI 刚从论文提炼，待你过目 | 你核实过的文献知识 ✅ |
| **`origin: ai-generated`**（AI 综合） | AI 的综合 / 你与 AI 讨论的产物，最需要警惕 | 你认可的 AI 综合（仍标明是 AI 生成）|

硬规则：AI 新建页面一律 `needs-review`；只有你肉眼看过才能改 `verified`；AI 生成的内容**永远不能**通过核实"洗白"成真实资料——`verified` 改变的是核实状态，不是来源性质。

## 🔁 三个工作流

![三个工作流](assets/fig-4-workflows.png)

| 工作流 | 你说一句 | AI 做的事 |
|---|---|---|
| **Ingest** | "ingest 一下 `Inbox/` 这篇 PDF" | 读文献 → 向你确认拟建页面清单 → 归档原文到 `raw/` 来源包 → 写来源摘要页 → 提炼概念页（自动识别 theory/construct/method/measure 并建节点）→ 更新索引和日志 |
| **Query** | "基于库里的知识，CRT 的抗作假证据怎么样？" | 经索引定位相关页 → 必要时回溯 raw 原文 → 回答时区分已核实/待核实内容 → 不擅自写回 |
| **Lint** | "给知识库做个健康检查" | 全库扫描：frontmatter 合规、链接断链、命名违规、模板缺节、孤立页 → 生成分级问题清单 → 经你确认后修复 |

所有破坏性操作（批量重命名、移动、删除）都必须先列清单、经你确认。

## 🚀 快速开始

### 1. 拿到这个库

把本仓库克隆或用作模板，用 [Obsidian](https://obsidian.md) 打开文件夹。

### 2. 启用总览看板（可选但推荐）

总览页用到 Dataview：安装 **Dataview** 插件 → 在其设置中开启 **JavaScript 查询** → 启用 CSS 片段 `.obsidian/snippets/overview-cards.css`。

### 3. 接入一个 AI agent（二选一）

这个库本质是纯文本，**任何能读写文件夹、照 SCHEMA 办事的 agent 都能用**：

- **方式 A · Obsidian 内**：安装第三方插件 Claudian，把 Claude Code / Codex 的 CLI 路径填进设置，在侧边栏聊天面板下指令。
- **方式 B · 终端**：以本库为工作目录启动 Claude Code / Codex，根目录的 `CLAUDE.md` / `AGENTS.md` 会自动把 agent 引向 SCHEMA。

### 4. 日常三步

1. **收**：看到好文献 → 丢进 `Inbox/`；自己写的笔记 → `raw/notes/`。
2. **炼**：跟 AI 说"ingest 一下 Inbox"。它会先给你一份拟建/拟改清单，你点头后才动手。
3. **看**：`wiki/overview/` 按角色分组浏览；Obsidian 关系图谱看知识网络；`meta/index.md` 查全量目录。

## 📁 目录结构

```
raw/                  原始资料（归档后只读）
  papers/             论文来源包（每篇一个文件夹：PDF + 全文 MD + 图片）
  articles/           文章来源包（公众号 / 网页等）
  notes/              你自己的课堂笔记 / 读书笔记 / 随想
  media/              跨来源的独立媒体素材
wiki/                 知识层（AI 维护）
  sources/            来源摘要页（一篇文献一页）
  concepts/           概念页（按 research_role 细分）
  entities/           实体页（人物、书籍、项目）
  comparisons/        比较页 / 跨域连接页
  overview/           自动总览看板
meta/                 维护层（AI 维护）
  index.md            全库索引
  log.md              操作日志（谁在什么时候改了什么）
Inbox/                待整理资料（处理后归档进 raw/）
SCHEMA.md             ⭐ 唯一权威规则，AI 的"宪法"
CLAUDE.md / AGENTS.md 各家 agent 的入口门牌
```

## 🙏 致谢

- 结构理念源自 [Andrej Karpathy 的 LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。
- 感谢 [Claude Code](https://claude.com/claude-code) 与 [Codex](https://openai.com/codex)——本库的规则迭代与日常维护由它们完成。

## 📄 License

本项目以 [MIT License](LICENSE) 发布。

---

*规则的唯一权威是 [SCHEMA.md](SCHEMA.md)；本文件只负责"给人讲清楚"。*

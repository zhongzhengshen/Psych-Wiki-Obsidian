---
type: guide
---

# LLM Wiki for Obsidian

> 一个基于 **Karpathy「LLM Wiki」理念**的 Obsidian 知识库模板：
> **你往 `raw/` 丢原始资料，让 AI 提炼成 `wiki/` 里的结构化知识**——你负责判断，AI 负责记账。

> 📌 本项目在开源项目 [iBlinkQ/llm-wiki-obsidian-blink](https://github.com/iBlinkQ/llm-wiki-obsidian-blink)（MIT License）的基础上修改而成，沿用并扩展了 Andrej Karpathy 的 [LLM Wiki](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f) 理念。

这是一个**空模板**（不含任何个人笔记），克隆后即可作为一个 Obsidian 库开始用。

## 核心理念
传统 wiki 难维护，是因为人不擅长那些繁琐的"记账"活（补交叉引用、查一致性、更新）——而 LLM 恰好擅长。于是把知识库分三层：

- **`raw/`** 原始资料（不可变，你输入；AI 只读不改）
- **`wiki/`** 知识层（AI 据 raw 提炼，你不必手写）
- **`TheSchema.md`** 配置层（定义规则与工作流，是给 AI 读的唯一权威）

## 目录结构
```
raw/        原始资料（papers / articles / notes / media）
wiki/       AI 提炼的知识层（sources / concepts / entities / comparisons / overview + index/log）
Inbox/      还没分类的，先丢这儿
TheSchema.md          AI 的操作规则（唯一权威）
CLAUDE.md / AGENTS.md  给 Claude Code / Codex 的入口门牌（都指向 TheSchema）
.obsidian/snippets/    排版与卡片样式（typography.css / overview-cards.css）
```
> **领域不靠文件夹**：AI / 心理 等领域靠 frontmatter 的 `domain` 字段区分，目录保持扁平、好查。

## 快速开始
1. **克隆**本仓库，用 Obsidian「打开文件夹作为库」打开它。
2. **装插件**：[Dataview](https://github.com/blacksmithgu/obsidian-dataview)（并在其设置里开启 **JavaScript 查询**）。总览页 `wiki/overview/` 依赖它。
3. **启用样式**：设置 → 外观 → CSS 片段 → 打开 `typography` 和 `overview-cards`。
4. **接入 AI（二选一）**：
   - **Claudian 插件**——在 Obsidian 侧边栏直接对 AI 下指令；
   - **外部 agent**（Claude Code / Codex 等）——把本库当工作目录启动，根目录的 `CLAUDE.md` / `AGENTS.md` 会引导它读 `TheSchema`。
5. **开始用**：往 `raw/` 丢资料 → 跟 AI 说「ingest 一下」→ 它读、提炼、建页、更新 index/log。

## 日常三步
- **收**：刷到好东西丢 `Inbox/` 或 `raw/` 对应子文件夹；自己写的笔记 → `raw/notes/`。
- **炼**：跟 AI 说「ingest 一下 Inbox / 某篇」→ 自动生成 `wiki/` 页面。
- **看**：领域总览（`wiki/overview/`，卡片+统计）· 关系图谱 · 全量目录（`wiki/index`）。

## 自定义
- **领域**：示例自带 `psychology` / `ai` 两个领域的 Overview 页。新增领域时复制一页、改顶部 `const DOMAIN = "..."` 一行即可。
- **字体**：默认用 Claude 桌面端的 `Anthropic Serif`（没装 Claude 桌面端会自动回退到系统字体）；想换字体改 `typography.css` 的 `--font-text` 一行。
- **规则**：所有约定都在 `TheSchema.md`，按你的工作流自行调整。

## 致谢
- 结构与 schema 参考自 [iBlinkQ/llm-wiki-obsidian-blink](https://github.com/iBlinkQ/llm-wiki-obsidian-blink)。
- 理念出自 Andrej Karpathy 的 [LLM Wiki gist](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)。

## License
[MIT](LICENSE)

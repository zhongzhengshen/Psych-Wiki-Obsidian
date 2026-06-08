---
type: overview
domain: psychology
summary: "自动汇总所有 domain: psychology 的页面：concept 按 kind 分组，其余按 type 分组"
tags:
  - psychology
updated: 2026-06-08
---

> [!info] 本页说明
> 本页会**自动汇总**所有 `domain: psychology` 的笔记：概念页按**种类**（理论 / 构念 / 测量工具 / 方法 / 概念）分组，再列来源、比较等。以后新增同领域笔记会自动出现，无需手动维护。

> [!note]- 首次使用需打开以下设置（点击展开）
> 1. **安装 Dataview 插件**：设置 → 第三方插件 → 浏览 → 搜 “Dataview” → 安装并启用。
> 2. **开启 JS 查询**：设置 → 第三方插件 →（在 Dataview 那行点齿轮进设置）→ 打开「Enable JavaScript Queries」。
> 3. **启用卡片样式**：设置 → 外观 → 下滑到「CSS 片段」→ 打开 `overview-cards` 开关（旁边刷新图标可重载）。

```dataviewjs
// ====== 配置：本页领域 ======
const DOMAIN = "psychology";

const pages = dv.pages('"wiki"').where(p => p.domain === DOMAIN && p.type !== "overview");

// ====== 统计条 ======
const total = pages.length;
const verified = pages.where(p => String(p.status) === "verified").length;
const review = total - verified;
const pct = total ? Math.round(verified / total * 100) : 0;

const stat = (parent, num, label) => {
  const box = parent.createDiv({ cls: "ov-stat" });
  box.createDiv({ cls: "ov-stat-num", text: String(num) });
  box.createDiv({ cls: "ov-stat-label", text: label });
};
const stats = dv.container.createDiv({ cls: "ov-stats" });
stat(stats, total, "总笔记");
stat(stats, verified, "已核实");
stat(stats, review, "待核实");

const prog = dv.container.createDiv({ cls: "ov-progress" });
const bar = prog.createDiv({ cls: "ov-bar" });
bar.createDiv({ cls: "ov-bar-fill" }).style.width = pct + "%";
prog.createDiv({ cls: "ov-progress-label", text: `核实进度 ${pct}%` });

// ====== 卡片渲染助手 ======
const renderCards = (group) => {
  const grid = dv.container.createDiv({ cls: "ov-cards" });
  for (const p of group) {
    const card = grid.createDiv({ cls: "ov-card" });
    if (String(p.status) === "needs-review") card.addClass("is-review");
    const a = card.createEl("a", { cls: "internal-link ov-card-title", text: p.file.name });
    a.dataset.href = p.file.path; a.href = p.file.path;
    if (p.summary) card.createDiv({ cls: "ov-card-sum", text: String(p.summary) });
    const foot = card.createDiv({ cls: "ov-card-foot" });
    const ok = String(p.status) === "verified";
    foot.createSpan({ cls: "ov-badge " + (ok ? "ok" : "warn"), text: ok ? "✓ 已核实" : "🕓 待核实" });
  }
};
const section = (title, group) => {
  if (group.length === 0) return;
  dv.header(3, `${title} (${group.length})`);
  renderCards(group.sort(p => p.file.name));
};

// ====== 概念页：按 kind 分组（研究骨架）======
const KINDS = { theory: "理论", construct: "构念", measure: "测量工具", method: "方法", concept: "概念" };
const concepts = pages.where(p => p.type === "concept");
for (const k of Object.keys(KINDS)) section(KINDS[k], concepts.where(p => p.kind === k));
// 没标 kind / kind 不在表中的概念页：兜底列出，提醒补标
section("⚠️ 未分类（待补 kind）", concepts.where(p => !Object.keys(KINDS).includes(String(p.kind))));

// ====== 其它类型 ======
section("来源", pages.where(p => p.type === "source"));
section("比较 / 跨域", pages.where(p => p.type === "comparison"));
section("实体", pages.where(p => p.type === "entity"));

if (total === 0) dv.paragraph("*（本领域暂无笔记，ingest 后会自动出现）*");
```

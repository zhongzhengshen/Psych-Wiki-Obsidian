---
type: overview
domain: ai
summary: "自动汇总所有 domain: ai 的页面，按类型分组"
tags: [map, ai]
updated: 2026-06-05
---

> [!info] 本页说明
> 本页会**自动汇总**所有 `domain: ai` 的笔记，按类型（概念 / 来源 / 实体 …）分组成卡片。以后新增同领域笔记会自动出现，无需手动维护。

> [!note]- 首次使用需打开以下设置（点击展开）
> 1. **安装 Dataview 插件**：设置 → 第三方插件 → 浏览 → 搜 “Dataview” → 安装并启用。
> 2. **开启 JS 查询**：设置 → 第三方插件 →（在 Dataview 那行点齿轮进设置）→ 打开「Enable JavaScript Queries」。
> 3. **启用卡片样式**：设置 → 外观 → 下滑到「CSS 片段」→ 打开 `overview-cards` 开关（旁边刷新图标可重载）。

```dataviewjs
// ====== 配置：本页领域 ======
const DOMAIN = "ai";

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

// ====== 卡片（按类型分组）======
const TYPES = { concept: "概念", source: "来源", entity: "实体", comparison: "比较" };
for (const t of Object.keys(TYPES)) {
  const group = pages.where(p => p.type === t).sort(p => p.file.name);
  if (group.length === 0) continue;
  dv.header(3, `${TYPES[t]} (${group.length})`);
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
}
if (total === 0) dv.paragraph("*（本领域暂无笔记，ingest 后会自动出现）*");
```

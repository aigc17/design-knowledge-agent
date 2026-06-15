# Design Knowledge Agent

业务知识库驱动的设计方案自动化。18 篇 Markdown 知识文档 × 6 业务域 × Claude Code / Codex / Cursor 三端适配。

## 可用 Skills

- **biz-solution** — 设计方案产出：输入需求描述，4 阶段产出 9+1 槽位设计方案 HTML
- **biz-review** — 设计方案评审：对照知识库检查已有设计的一致性
- **biz-analyze** — 需求拆解分析：定位业务域、梳理关联流程、盘点可复用资产

## 关键路径

- `业务知识库/manifest.json` — 知识路由索引（Agent 先读这个）
- `业务知识库/设计方案汇报模板.html` — 产出模板
- `业务知识库/ai/{域}/{文档}.md` — 知识文档（按 sectionSchema 分章节读取）
- `业务知识库/.biz-session.json` — 运行时状态（不入库）

## 注意事项

1. KB 文档按 manifest `sectionSchema` 分章节读取，不要一次读全文
2. Slot I 分为 I-gallery（追加）+ I-summary（定稿），永远不要替换 I-gallery 全部内容
3. 每条设计内容标注来源：📗 KB / 📘 跨域参考 / 🔮 推理
4. 设计尺寸 iOS 375 × 812 pt

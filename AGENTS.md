# Design Knowledge Agent

业务知识库驱动的设计方案自动化。18 篇 Markdown 知识文档 x 6 业务域 x Claude Code / Codex / Cursor 三端适配。

## 可用 Skills

- **biz-solution** — Codex 交互式主入口，按 `.biz-session.json` 在对话中连续推进 P1 ~ P4
- **biz-p1** — P1 需求理解：Slot A-D + 知识库预检
- **biz-p2** — P2 页面清单：Slot E-F + 流程同构
- **biz-p3** — P3 逐页设计：Slot G（工作台 Tab）
- **biz-p4** — P4 整体评审：Slot summary + H + I-summary

## 模板结构（双 Tab）

产出模板包含两个视图 Tab：
- **汇报 Tab**：领导视角。决策摘要(结论先行) + 项目概览 + 设计规划 + 验证 + 命中与采用
- **工作台 Tab**：设计师视角。无限画布(pan/zoom) + 页面设计稿(matrix布局) + 跨域参考候选池

## 关键路径

- `业务知识库/manifest.json` — 知识路由索引（Agent 先读这个）
- `业务知识库/设计方案汇报模板.html` — 双 Tab 产出模板
- `业务知识库/ai/{域}/{文档}.md` — 知识文档（按 sectionSchema 分章节读取）
- `业务知识库/.biz-session.json` — 运行时状态（不入库）

## 注意事项

1. 知识库文档按 manifest `sectionSchema` 分章节读取，不要一次读全文
2. Slot I 分为 I-gallery（工作台 Tab，追加）+ I-summary（汇报 Tab，定稿），永远不要替换 I-gallery 全部内容
3. `slot-summary` 是 P4 填充的决策摘要，KV 表格格式，位于汇报 Tab 第一屏
4. 每条设计内容标注来源：知识库 / 跨域参考 / 推理
5. 设计尺寸 iOS 375 x 812 pt
6. 工作台 Tab 的 slot-g 推荐 matrix 布局（每页一列）
7. Codex 使用 `biz-solution` 时不要让用户手动调用 `biz-p1` ~ `biz-p4`；主入口根据 phase 执行当前阶段，阶段结束等待用户确认后续跑

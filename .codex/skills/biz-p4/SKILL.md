# biz-p4 — 整体评审

P4 阶段：填充 Slot summary（决策摘要）+ Slot H（四维自检）+ 定稿 Slot I-summary（证据汇总）。

## Trigger Keywords

评审, P4, 自检, 证据汇总, 定稿, biz-p4

## Prerequisites

`业务知识库/.biz-session.json` 必须存在且 `phase: "page-design"`。若不满足，交还 `biz-solution` 主入口判断当前阶段，不要求用户手动调用底层 Skill。

## Instructions

### 1. 读取状态

读取 `.biz-session.json`，获取全部信息。

### 2. 读取知识库章节

按 `sectionSchema.P4` 重新读命中文档的「五、黄金流程完整描述」「六、经验沉淀」，提取业务规则和异常路径。

### 3. 填充 Slot summary — 决策摘要（汇报 Tab 末尾）

定位 `id="slot-summary"`（位于 04 命中与采用之后、footer 之前），替换 placeholder。用 KV 表格呈现：
- **本轮结论**：一句话说明方案核心
- **核心理由**：为什么这样设计
- **复用边界**：哪些有知识库支撑，哪些是新增
- **最终产物**：交付物说明

### 4. 填充 Slot H — 四维自检

- **流程完整性**：主链路 + 异常路径是否覆盖
- **业务规则一致性**：每条规则标注 [知识库] 来源
- **UI 规范合规**：尺寸、组件、视觉一致性
- **信息不足项**：知识库缺失的信息，需人工补充
- 使用 `.check-item` 逐条呈现，成功项用纯文本 `通过` 或 `.status.ok`，风险/缺口项用纯文本 `待确认` 或 `.status.warn`；不要使用 emoji 或符号图标。
- 同步更新 `slot-gap-check`：若有信息不足项，取消 `display:none`，用 `.info-table` 列出 `决策点 | 知识库覆盖 | 缺口`，并在末尾追加 `.callout.warn`。

定位 `id="slot-h"`，替换 placeholder。

### 5. 定稿 I-summary

定位 `id="slot-i-summary"`（位于汇报 Tab），替换 placeholder，写入：

- 标题「知识库精确命中」，`.ledger` 表格：`文档 | 章节 | Slot | 贡献`。
- 标题「跨域参考采纳」，`.ledger` 表格：`来源 | 页面 | 目标页 | 采纳? | 说明`。
- 标题「推理清单」，`.ledger` 表格：`Slot | 内容 | 推理原因`。
- 标题「知识库建议补充」，`.ledger` 表格：`缺失项 | 影响`。
- 矛盾检测：若存在矛盾，用 `.callout.warn` 明确写出；若不存在，写一句「未发现关键矛盾」。
- 所有来源标识使用纯文本 `[知识库]`、`[跨域参考]`、`[推理]`，不要使用 emoji。
- adopted 状态用 `.status.ok` / `.status.warn`，不要只写 true/false。

**注意**：只操作 `id="slot-i-summary"`，不触碰 `id="slot-i-gallery"`（位于工作台 Tab）。

### 6. 保存状态

更新 `phase: "completed"`。

### 7. 产出

输出最终 report HTML 路径，告知用户全部槽位已填满（含决策摘要）。

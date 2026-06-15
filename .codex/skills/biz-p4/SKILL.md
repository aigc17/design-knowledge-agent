# biz-p4 — 整体评审

P4 阶段：填充 Slot summary（决策摘要）+ Slot H（四维自检）+ 定稿 Slot I-summary（证据汇总）。

## Trigger Keywords

评审, P4, 自检, 证据汇总, 定稿, biz-p4

## Prerequisites

`业务知识库/.biz-session.json` 必须存在且 `phase: "page-design"`。若不满足，提示用户先运行 `$biz-p3`。

## Instructions

### 1. 读取状态

读取 `.biz-session.json`，获取全部信息。

### 2. 读取知识库章节

按 `sectionSchema.P4` 重新读命中文档的「五、黄金流程完整描述」「六、经验沉淀」，提取业务规则和异常路径。

### 3. 填充 Slot summary — 决策摘要

定位 `id="slot-summary"`，替换 placeholder。用 KV 表格呈现：
- **本轮结论**：一句话说明方案核心
- **核心理由**：为什么这样设计
- **复用边界**：哪些有知识库支撑，哪些是新增
- **最终产物**：交付物说明

### 4. 填充 Slot H — 四维自检

- **流程完整性**：主链路 + 异常路径是否覆盖
- **业务规则一致性**：每条规则标注 📗 来源
- **UI 规范合规**：尺寸、组件、视觉一致性
- **信息不足项**：知识库缺失的信息，需人工补充

定位 `id="slot-h"`，替换 placeholder。

### 5. 定稿 I-summary

定位 `id="slot-i-summary"`（位于汇报 Tab），替换 placeholder，写入：

- 📗 精确命中汇总表：文档 → 章节 → Slot → 贡献类型
- 📘 跨域参考汇总表：文档 → 页面 → adopted? → 参考了什么
- 🔮 纯推理清单：Slot → 内容 → 推理原因
- 知识库建议：知识库应补充的内容
- ⚠️ 矛盾检测：不同文档间的数据矛盾

**注意**：只操作 `id="slot-i-summary"`，不触碰 `id="slot-i-gallery"`（位于工作台 Tab）。

### 6. 保存状态

更新 `phase: "completed"`。

### 7. 产出

输出最终 report HTML 路径，告知用户全部槽位已填满（含决策摘要）。

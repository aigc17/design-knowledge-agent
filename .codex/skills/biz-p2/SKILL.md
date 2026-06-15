# biz-p2 — 页面清单

P2 阶段：填充 Slot E-F（设计流程 + 页面清单 + 流程同构分析）。

## Trigger Keywords

页面清单, P2, 设计流程, 流程分析, biz-p2

## Prerequisites

`业务知识库/.biz-session.json` 必须存在且 `phase: "understanding"`。若不存在，提示用户先运行 `$biz-p1`。

## Instructions

### 1. 读取状态

读取 `.biz-session.json`，获取 `kbHits`、`crossRefs`、`reportFile`。

### 2. 读取 KB 章节

按 `sectionSchema.P2` 读命中文档的「四、流程与页面识别总表」「五、黄金流程完整描述」。

### 3. 填充 Slot E — 设计流程

- 主链路流程节点（标注新增/复用，📗/🔮）
- 分支/异常路径
- **流程同构分析**：将需求流程与 KB 同原型流程并排对比

  ```
  原型流程: 步骤A → 步骤B → 步骤C → 步骤D
  本次流程: 步骤1 → 步骤2 → [省略] → 步骤3
  映射关系说明
  ```

定位 `id="slot-e"`，替换 placeholder。

### 4. 填充 Slot F — 页面清单

表格：页面名称 | 类型(new/reuse) | 参考来源(📗/📘/🔮) | 状态

定位 `id="slot-f"`，替换 placeholder。

### 5. crossRefs 标记采纳

对 `.biz-session.json` 中 `crossRefs[]` 每条标记：
- `adopted: true` — 该截图/模式被实际采纳
- `adopted: false` + `reason` — 未采纳原因

### 6. I-gallery 追加

本阶段新发现的跨域截图追加到 `id="slot-i-gallery"`。

### 7. 保存状态

更新 `phase: "inventory"`，写入 `inventory[]`。

### 8. 产出

输出 report HTML 路径，告知用户 Part 2 已完成。

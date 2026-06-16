# biz-p2 — 页面清单

P2 阶段：填充 Slot E-F（设计流程 + 页面清单 + 流程同构分析）。

## Trigger Keywords

页面清单, P2, 设计流程, 流程分析, biz-p2

## Prerequisites

`业务知识库/.biz-session.json` 必须存在且 `phase: "understanding"`。若不满足，交还 `biz-solution` 主入口判断当前阶段，不要求用户手动调用底层 Skill。

## Instructions

### 1. 读取状态

读取 `.biz-session.json`，获取 `kbHits`、`crossRefs`、`reportFile`。

### 2. 读取知识库章节

按 `sectionSchema.P2` 读命中文档的「四、流程与页面识别总表」「五、黄金流程完整描述」。

### 3. 填充 Slot E — 设计流程

- 必须先输出「完整流程」流程图，再写流程说明。
- 流程图使用模板内置 Mermaid，放在 `<div class="flowchart-panel">` 中，标题为「完整流程」。
- 流程图必须覆盖主链路、取消/返回分支、成功结果、失败结果和最终承接页，不要只写线性步骤。
- 节点需标注 `REUSE` / `NEW`，复用页用绿色样式，新页面用蓝色样式，失败结果用橙色样式。
- 分支边上写清触发动作，如「点击退款」「取消退款」「确认退款」「成功」「失败」。
- 流程图后补充主链路流程节点（标注新增/复用，[知识库]/[推理]）和分支/异常路径。
- **流程同构分析**：将需求流程与知识库同原型流程并排对比

推荐结构：

```html
<div class="flowchart-panel">
  <h4>完整流程</h4>
  <div class="mermaid">
flowchart TD
  A["1. 订单记录（待使用）<br/>REUSE"] -->|点击退款| B["2. 退款确认页<br/>NEW"]
  B -->|取消退款| C["返回订单记录<br/>保留权益"]
  B -->|确认退款| D{"系统处理"}
  D -->|成功| E["3a. 退款结果页（成功）<br/>NEW"]
  D -->|失败| F["3b. 退款结果页（失败）<br/>NEW"]
  E --> G["返回订单记录"]
  F --> H["重试 / 返回"]
  classDef reuse fill:#ecfdf5,stroke:#059669,color:#1e2533;
  classDef new fill:#eff6ff,stroke:#2563eb,color:#1e2533;
  classDef fail fill:#fffbeb,stroke:#d97706,color:#1e2533;
  class A,C reuse;
  class B,E new;
  class F fail;
  </div>
  <p class="flowchart-note">按本次需求替换节点名和分支，不要照抄示例。</p>
</div>
```

定位 `id="slot-e"`，替换 placeholder。

### 4. 填充 Slot F — 页面清单

表格：页面名称 | 类型(new/reuse) | 参考来源([知识库]/[跨域参考]/[推理]) | 状态

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

# biz-p3 — 逐页设计

P3 阶段：填充 Slot G（375×812 iOS 设计稿）。

## Trigger Keywords

逐页设计, P3, UI设计, 设计稿, 手机页面, biz-p3

## Prerequisites

`业务知识库/.biz-session.json` 必须存在且 `phase: "inventory"`。若不满足，提示用户先运行 `$biz-p2`。

## Instructions

### 1. 读取状态

读取 `.biz-session.json`，获取 `inventory[]`、`crossRefs[]`（含 adopted 标记）、`reportFile`。

### 2. 读取知识库章节

按 `sectionSchema.P3` 读命中文档的「四、流程与页面识别总表」中对应页面的视觉描述和交互规则。

### 3. 对 inventory 中所有页面一次性设计

每页流程：
1. 读知识库中该页的视觉描述、交互规则
2. 检查 `crossRefs[]` 中 `adopted: true` 的条目，引用截图 URL 作参考
3. 生成 iOS 375×812 pt UI（phone-frame 容器）
4. 附设计说明 + 来源标注（📗/📘/🔮）

### 4. 填充 Slot G（位于「工作台」Tab）

所有页面的设计稿嵌入 `id="slot-g"`，替换 placeholder。
推荐 matrix 布局：每页一列（node-card → phone-frame → evidence-shot），并排对比。
也可用 phone-grid > phone-wrap > phone-frame > phone-screen 结构。

### 5. I-gallery 追加（位于「工作台」Tab）

设计中新引用的跨域截图追加到 `id="slot-i-gallery"`。
推荐用 candidate-board 暗色面板分组展示。

### 6. 保存状态

更新 `phase: "page-design"`，所有 inventory 项 `status: "completed"`。

### 7. 产出

输出 report HTML 路径，告知用户 Part 3 设计全部完成。

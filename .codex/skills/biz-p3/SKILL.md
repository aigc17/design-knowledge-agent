# biz-p3 — 逐页设计

P3 阶段：填充 Slot G（375×812 iOS 设计稿）。

## Trigger Keywords

逐页设计, P3, UI设计, 设计稿, 手机页面, biz-p3

## Prerequisites

`业务知识库/.biz-session.json` 必须存在且 `phase: "inventory"`。若不满足，交还 `biz-solution` 主入口判断当前阶段，不要求用户手动调用底层 Skill。

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
4. 附设计说明 + 来源标注（[知识库]/[跨域参考]/[推理]）

工作台中的 `phone-frame` 是平面设计稿画板，不是手机设备壳。不得添加黑色描边、设备圆角、刘海/外壳装饰；保持 `border: none`、`border-radius: 0`。

### 4. 填充 Slot G（位于「工作台」Tab）

所有页面的设计稿嵌入 `id="slot-g"`，替换 placeholder。
推荐 matrix 布局：每页一列（node-card → phone-frame → evidence-note），并排对比。
也可用 phone-grid > phone-wrap > phone-frame > phone-screen 结构。

每一页必须包含三层结构：
- `node-card`：页面名、NEW/REUSE 标签、一句话说明该页职责。
- `phone-frame > phone-screen ps`：375×812 pt 平面画板。
- `evidence-note`：至少三行，分别写「来源」「关键决策」「复用边界」，来源使用 [知识库]/[跨域参考]/[推理]。

推荐结构：

```html
<div class="matrix">
  <div class="flow-column">
    <div class="node-card">
      <strong>{页面名}</strong>
      <p><span class="page-tag tag-new">NEW</span> {页面职责}</p>
    </div>
    <div class="down-arrow"></div>
    <div class="phone-wrap">
      <div class="phone-frame">
        <div class="phone-screen ps">
          <!-- 375x812 页面内容 -->
        </div>
      </div>
    </div>
    <div class="evidence-note">
      <div><span>来源</span><p>[知识库] {文档/章节} + [跨域参考] {页面}</p></div>
      <div><span>决策</span><p>{关键设计判断}</p></div>
      <div><span>边界</span><p>{复用什么，不复用什么}</p></div>
    </div>
  </div>
</div>
```

不得输出只有截图、只有空白卡片、只有描述没有画板的 Slot G。

### 5. I-gallery 追加（位于「工作台」Tab）

设计中新引用的跨域截图追加到 `id="slot-i-gallery"`。
推荐用 candidate-board 暗色面板分组展示。

### 6. 保存状态

更新 `phase: "page-design"`，所有 inventory 项 `status: "completed"`。

### 7. 产出

输出 report HTML 路径，告知用户 Part 3 设计全部完成。

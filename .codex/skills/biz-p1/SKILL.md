# biz-p1 — 需求理解

P1 阶段：读取知识库，填充 Slot A-D + 知识库覆盖度预检。

## Trigger Keywords

需求理解, P1, 需求背景, 产品目标, 目标用户, biz-p1

## Instructions

### 1. 启动

1. 读取 `业务知识库/manifest.json`
2. 读取 `业务知识库/设计方案汇报模板.html`
3. 检查 `业务知识库/.biz-session.json`
   - 不存在 → 用用户输入的需求创建新会话，执行 P1
   - 存在且由 `biz-solution` 主入口续跑 → 不重复执行 P1，交还主入口按 phase 推进

### 2. 知识路由

**第一遍 — 精确命中**
匹配 manifest `keywords` + `match` + `summary`，选中 1-3 篇文档。
按 `sectionSchema.P1` 只读「一、项目目标」「二、目标用户特征」章节。
仅当用户主动提及指标/数据需求时，才读「三、数据指标」章节。

**第二遍 — 跨域相似匹配**
- 页面模式匹配：从需求提取模式词，扫描全部 `documents[].pages[]`
- 流程模式匹配：扫描 `flowArchetype` + `flowSteps[]` + `flowExceptions[]`
- 7 种原型：交易购买 | 注销退出 | 注册登录 | 借款申请 | 身份认证 | 账单支付 | 服务开通
- 对匹配的页面只读「页面识别总表」中该行，提取截图 URL + 视觉描述

### 3. 复制模板

复制 `业务知识库/设计方案汇报模板.html` 到 `业务知识库/output/{简称}-report.html`

### 4. 填充 Slot A-D

- **A 需求背景** — 业务痛点（[知识库]/[推理]）
- **B 产品目标** — 目标 + 衡量方式（[知识库]/[推理]）
- **C 目标用户** — 用户群特征（[知识库]/[推理]）
- **D 关键指标** — 默认跳过（`display:none`），仅当用户主动提及指标时才填充并显示

#### P1 HTML 输出契约

P1 是汇报首屏，必须使用模板组件做排版，不允许输出连续裸段落。

- Slot A：使用 1-3 个 `.content-item`，每个 item 内正文后跟 `.source-note`。
- Slot B：使用 2-4 个 `.content-item`，目标名用 `<strong>1. 目标名</strong> — 说明`，正文后跟 `.source-note`。
- Slot C：优先使用 `.info-table`，列为 `客群 | 特征 | 场景`；表格后追加 `.source-note`。
- Slot D：仅用户主动要求指标时显示，使用 `.metric-grid` + `.info-table`。
- 来源只写在 `.source-note` 中，不要把 `[知识库]`、`[跨域参考]`、`[推理]` 直接拼在正文末尾。
- 不要在 Slot A-D 内直接输出裸 `<p>` 作为主要内容。

推荐结构：

```html
<div class="slot filled" id="slot-a">
  <h3>项目背景与业务痛点</h3>
  <div class="content-item">
    {背景/痛点正文}
    <div class="source-note">[知识库] {文档/章节} + [推理] {说明}</div>
  </div>
  <div class="content-item">
    {第二个判断}
    <div class="source-note">[推理] {说明}</div>
  </div>
</div>

<div class="slot filled" id="slot-b">
  <h3>产品目标</h3>
  <div class="content-item">
    <strong>1. {目标名}</strong> — {目标说明}
    <div class="source-note">[知识库] {文档/章节}</div>
  </div>
</div>

<div class="slot filled" id="slot-c">
  <h3>目标用户</h3>
  <table class="info-table">
    <thead><tr><th>客群</th><th>特征</th><th>场景</th></tr></thead>
    <tbody>
      <tr><td>{客群}</td><td>{特征}</td><td>{场景}</td></tr>
    </tbody>
  </table>
  <div class="source-note" style="margin-top:8px;">[知识库] {文档/章节}</div>
</div>
```

定位 `id="slot-a"` ~ `id="slot-d"`，替换 placeholder，badge 改为 filled。

### 5. I-gallery 追加

跨域参考截图卡片追加到 `id="slot-i-gallery"` 的闭合标签前。只追加，不替换已有内容。

### 6. 知识库覆盖度预检

扫描需求涉及的关键决策点，对照知识库：
- 有精确文档：[知识库]
- 仅跨域参考：[跨域参考]
- 纯推理：[推理]，知识库未覆盖

写入 `id="slot-gap-check"`（去掉 `style="display:none"`）。

### 7. 保存状态

写入 `业务知识库/.biz-session.json`，至少包含：

```json
{
  "sessionId": "ses_YYYYMMDD_slug",
  "phase": "understanding",
  "requirement": "用户原始需求",
  "kbHits": ["业务知识库/ai/...md"],
  "crossRefs": [],
  "inventory": [],
  "decisions": [],
  "reportFile": "业务知识库/output/{slug}-report.html"
}
```

### 8. 产出

输出 report HTML 路径，告知用户在浏览器打开查看 Part 1。

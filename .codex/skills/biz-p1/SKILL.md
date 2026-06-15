# biz-p1 — 需求理解

P1 阶段：读取知识库，填充 Slot A-D + 知识库覆盖度预检。

## Trigger Keywords

需求理解, P1, 需求背景, 产品目标, 目标用户, biz-p1

## Instructions

### 1. 启动

1. 读取 `业务知识库/manifest.json`
2. 读取 `业务知识库/设计方案汇报模板.html`
3. 检查 `业务知识库/.biz-session.json`
   - 存在且 phase 不是 understanding → 提示用户当前已在后续阶段
   - 不存在 → 用用户输入的需求创建新会话

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

- **A 需求背景** — 业务痛点（📗/🔮）
- **B 产品目标** — 目标 + 衡量方式（📗/🔮）
- **C 目标用户** — 用户群特征（📗/🔮）
- **D 关键指标** — 默认跳过（`display:none`），仅当用户主动提及指标时才填充并显示

定位 `id="slot-a"` ~ `id="slot-d"`，替换 placeholder，badge 改为 filled。

### 5. I-gallery 追加

跨域参考截图卡片追加到 `id="slot-i-gallery"` 的闭合标签前。只追加，不替换已有内容。

### 6. 知识库覆盖度预检

扫描需求涉及的关键决策点，对照知识库：
- ✅ 📗 有精确文档
- ⚠️ 📘 仅跨域参考
- ❌ 🔮 纯推理，知识库未覆盖

写入 `id="slot-gap-check"`（去掉 `style="display:none"`）。

### 7. 保存状态

写入 `业务知识库/.biz-session.json`，`phase: "understanding"`。

### 8. 产出

输出 report HTML 路径，告知用户在浏览器打开查看 Part 1。

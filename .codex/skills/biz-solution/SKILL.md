# biz-solution — 设计方案产出

基于业务知识库（18 篇 Markdown 文档 × 6 业务域），产出一份可交付的设计方案汇报 HTML。最终产物 = 填满 9+1 个槽位的 HTML 模板。

## Trigger Keywords

设计方案, 知识库, biz-solution, 需求分析, 页面设计, UI设计, 退款, 还借宝, 注销, 授信, 用信, 还款

## Quick Start

输入一句需求描述，例如：`还借宝新增退款确认页`

## Workflow

### 启动协议

1. **读取路由索引** — `业务知识库/manifest.json`
2. **读取汇报模板** — `业务知识库/设计方案汇报模板.html`
   - 9+1 个槽位：A-D (P1)、E-F (P2)、G (P3)、H (P4)、I-gallery + I-summary (附录)
   - **Slot I-gallery = 追加区**（跨域截图画廊，每阶段只追加不替换）
   - **Slot I-summary = 定稿区**（P4 最终写入证据汇总表）
3. **检查会话状态** — `业务知识库/.biz-session.json`
   - 存在 → 读取，从 `phase` 继续
   - 不存在且有需求 → 新建会话，进入 P1
4. **知识路由（两遍扫描）**

   **第一遍 — 业务域精确命中**
   匹配 manifest `keywords` + `match` + `summary`，选中 1-3 篇。
   **按章节读取**：参照 manifest 的 `sectionSchema`，P1 只读目标/用户/指标章节，P2 只读流程/页面章节。

   **第二遍 — 跨域相似匹配**

   **A. 页面模式匹配**
   从需求提取模式词（确认/结果/列表/状态/鉴权/弹窗……），扫描全部 `documents[].pages[]`。
   对每个匹配到的页面：只读该文档「页面识别总表」中该行。
   提取：截图 URL + 视觉描述摘要 + Figma 链接 → 记入 `crossRefs[]`。

   **B. 流程模式匹配**
   提取流程动作词，扫描 `flowArchetype` + `flowSteps[]` + `flowExceptions[]`。
   7 种流程原型：交易购买 | 注销退出 | 注册登录 | 借款申请 | 身份认证 | 账单支付 | 服务开通

5. **创建输出** — 复制模板到 `业务知识库/output/{简称}-report.html`

### 来源标注规则

- **📗 KB** = 精确命中文档（标注文档+章节）
- **📘 跨域参考** = 跨域相似页面参考（标注来源文档+页面名+截图链接）
- **🔮 推理** = Agent 推理（KB 无覆盖）

### 槽位操作规则

每个阶段 = 定位 `id="slot-X"` 的 `<div class="slot">`，将 `<div class="placeholder">` 替换为实际内容，`badge-pending` 改为 `badge-filled`，class 加 `filled`。

**I-gallery 追加协议**（防覆盖）：
- `id="slot-i-gallery"` = 只追加，不替换。在闭合标签前追加新卡片。
- `id="slot-i-summary"` = P4 定稿时一次性写入。

### P1 · 需求理解 → Slot A/B/C/D + KB 预检

- **A 需求背景** — 业务痛点
- **B 产品目标** — 目标 + 衡量方式
- **C 目标用户** — 用户群特征
- **D 关键指标** — 指标名/定义/基线
- **I-gallery 追加** — 跨域参考截图卡片
- **KB 预检** — 扫描决策点覆盖度，写入 `id="slot-gap-check"`

检查点：请用户确认 Part 1 是否准确。

### P2 · 页面清单 → Slot E/F

- **E 设计流程** — 主链路 + 分支 + 流程同构分析
- **F 页面清单** — 名称 | 类型 | 参考来源 | 状态
- crossRefs 标记 `adopted: true/false` + `reason`

检查点：请用户确认页面清单。

### P3 · 逐页设计 → Slot G

- inventory 中所有页面一次性设计
- 生成 iOS 375×812 pt UI
- 引用 adopted=true 的跨域截图

检查点：请用户确认设计方案。

### P4 · 整体评审 → Slot H + I-summary

- **H 设计自检** — 流程完整性 / 业务规则一致性 / UI 合规 / 信息不足
- **I-summary** — 📗/📘/🔮 三类证据汇总 + KB 建议 + 矛盾检测

检查点：全部槽位填满，请用户最终确认。

## References

- `业务知识库/manifest.json` — 知识路由索引
- `业务知识库/设计方案汇报模板.html` — 9+1 槽位 HTML 模板
- `业务知识库/ai/` — 18 篇知识文档（6 业务域）

## Session State

```json
{
  "sessionId": "ses_{YYYYMMDD}_{shortId}",
  "phase": "understanding | inventory | page-design | review | completed",
  "requirement": "需求原文",
  "kbHits": ["精确命中文档路径"],
  "crossRefs": [{ "targetPage", "refPage", "refDoc", "pattern", "imageUrl", "visualDesc", "adopted", "reason" }],
  "reportFile": "output/{简称}-report.html",
  "inventory": [{ "id", "name", "type", "status", "reference" }]
}
```

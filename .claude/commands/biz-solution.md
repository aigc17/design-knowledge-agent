# /biz-solution — 设计方案产出 v3

> 基于业务知识库，产出一份可交付的设计方案汇报。
> 最终产物 = `设计方案汇报模板.html` 的 10+1 个槽位被逐步填满。

## 模板结构（双 Tab）

模板包含两个视图，切换通过顶部 Tab：

**Tab 1「汇报」**（给领导看的干净版）：
- 决策摘要（slot-summary，P4 填充，末尾）
- 01 项目概览（slot-a ~ slot-d）
- 02 设计规划（slot-e + slot-f + 跳转工作台按钮）
- 03 设计验证（slot-h + slot-gap-check）
- 04 命中与采用（slot-i-summary）

**Tab 2「工作台」**（给设计师看的工作版）：
- 无限画布（pan/zoom）
- slot-g：页面设计稿（matrix 或 phone-grid 布局）
- slot-i-gallery：跨域参考截图（候选池，追加区）

## 需求输入

$ARGUMENTS

---

## 启动协议

1. **读取路由索引** — `业务知识库/manifest.json`
2. **读取汇报模板** — `业务知识库/设计方案汇报模板.html`
   - 双 Tab 结构：「汇报」Tab（A-D, E-F, H, summary, I-summary）+「工作台」Tab（G, I-gallery）
   - **slot-summary = 决策摘要**（P4 填充，KV 表格：结论/理由/复用边界/产物，位于汇报 Tab 末尾）
   - **Slot I-gallery = 追加区**（工作台 Tab 中，每阶段只追加不替换）
   - **Slot I-summary = 定稿区**（汇报 Tab 中，P4 一次性写入）
3. **检查会话状态** — `业务知识库/.biz-session.json`
   - 存在 → 读取，从 `phase` 继续
   - 不存在且有需求 → 新建会话，进入 P1
   - 不存在且无输入 → 提示输入需求
4. **知识路由（两遍扫描）**

   **第一遍 — 业务域精确命中**
   匹配 manifest `keywords` + `match` + `summary`，选中 1-3 篇。
   **按章节读取**：参照 manifest 的 `sections` 字段，P1 只读目标/用户/指标章节，P2 只读流程/页面章节，避免一次读全文。

   **第二遍 — 跨域相似匹配**

   **A. 页面模式匹配**
   从需求提取模式词（确认/结果/列表/状态/鉴权/弹窗……），扫描全部 `documents[].pages[]`。
   对每个匹配到的页面：只读该文档「页面识别总表」中该行（不读全文）。
   提取：截图 URL + 视觉描述摘要 + Figma 链接 → 记入 `crossRefs[]`。

   **B. 流程模式匹配**
   提取流程动作词，扫描 `flowArchetype` + `flowSteps[]` + `flowExceptions[]`。
   7 种流程原型：交易购买 | 注销退出 | 注册登录 | 借款申请 | 身份认证 | 账单支付 | 服务开通
   匹配逻辑：正/逆关系、步骤同构、异常模式相似。

5. **创建输出** — 复制模板到 `业务知识库/output/{简称}-report.html`

---

## 来源标注规则

每条内容必须标注来源：
- **[知识库]** = 精确命中文档（标注文档+章节）
- **[跨域参考]** = 跨域相似页面参考（标注来源文档+页面名+截图链接）
- **[推理]** = Agent 推理（知识库无覆盖）
- 输出到 HTML 的来源标识一律使用纯文本方括号，不使用 emoji。

---

## 槽位操作规则

### 模板驱动
每个阶段的工作 = 用 Edit 工具定位对应 `id="slot-X"` 的 `<div class="slot">`，将 `<div class="placeholder">` 替换为实际内容，并将 `badge-pending` 改为 `badge-filled`、`border: dashed` 改为 `border: solid`（class 加 `filled`）。

### I-gallery 追加协议（防覆盖核心机制）
- **I-gallery**（`id="slot-i-gallery"`）= 只追加，不替换
  - 每阶段发现跨域参考时，在 I-gallery 的 `</div>` 闭合标签前**追加** crossref-card
  - 永远不要用 Edit 的 old_string 匹配 I-gallery 的全部内容去替换
  - 操作方式：定位 `slot-i-gallery` 最后一个 `</div>` 前插入新卡片
- **I-summary**（`id="slot-i-summary"`）= P4 定稿时一次性写入
  - 只在 P4 操作，替换 placeholder 为证据汇总表

### Edit 安全规则
- 使用足够长的上下文字符串确保 old_string 唯一
- 优先用 `id="slot-X"` 作为定位锚点
- 对 Slot I-gallery 只做追加操作

---

## P1 · 需求理解 → Slot A/B/C + 知识库预检

**读取范围**（按 manifest sections 定位）：
- 命中文档的「项目目标」「目标用户特征」章节
- 仅当用户主动提及指标/数据需求时，才读「数据指标」章节

**填充 Slot A-C**：
- **A 需求背景** — 业务痛点（[知识库]/[推理]）
- **B 产品目标** — 目标 + 衡量方式（[知识库]/[推理]）
- **C 目标用户** — 用户群特征（[知识库]/[推理]）
- **D 关键指标** — 默认跳过（`display:none`），仅当用户主动提及指标时才填充并显示

**I-gallery 追加**：
- 本阶段发现的跨域参考截图卡片追加到 I-gallery

**★ 知识库覆盖度预检**（P1 末尾新增）：
填完 A-D 后，扫描需求涉及的关键决策点，对照知识库命中情况：

| 决策点 | 知识库覆盖 | 缺口 |
|--------|---------|------|
| {决策1} | [知识库] {文档}/{章节} | — |
| {决策2} | [跨域参考] 仅跨域参考 | 无精确文档 |
| {决策3} | [推理] 纯推理 | 知识库未覆盖 |

- 如有 [推理] 项，显示黄色 gap-alert 提醒用户
- 写入 HTML 的 `id="slot-gap-check"`（去掉 `style="display:none"`）

**产出**：输出 HTML（A-D 已填充 + gap 预检），浏览器打开。

**保存状态**：`phase: "understanding"`

**★ 检查点**：
> Part 1（需求认知）+ 知识库覆盖度预检已完成。理解是否准确？确认后进入 P2。

---

## P2 · 页面清单 → Slot E/F

**读取范围**：
- 命中文档的「页面识别总表」「黄金流程」章节

**填充 Slot E**：
- 必须先输出「完整流程」流程图，再写流程说明。
- 流程图使用模板内置 Mermaid，放在 `<div class="flowchart-panel">` 中，标题为「完整流程」。
- 流程图必须覆盖主链路、取消/返回分支、成功结果、失败结果和最终承接页，不要只写线性步骤。
- 节点需标注 `REUSE` / `NEW`，复用页用绿色样式，新页面用蓝色样式，失败结果用橙色样式。
- 分支边上写清触发动作，如「点击退款」「取消退款」「确认退款」「成功」「失败」。
- 流程图后补充主链路流程节点（标注新增/复用）和分支/异常路径。
- **流程同构分析**：必须使用 `.ledger` 表格，将需求流程与知识库中同原型流程并排对比
- 同构表列为：`环节 | 知识库原型流程 | 本次需求流程 | 映射说明`
- 同构表必须覆盖入口、确认、鉴权/处理、结果、异常/返回；不适用环节写 `省略` 并说明原因

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

<h4 style="font-size:13px;color:var(--sub);margin:16px 0 8px;">流程同构分析</h4>
<table class="ledger">
  <thead><tr><th>环节</th><th>知识库原型流程</th><th>本次需求流程</th><th>映射说明</th></tr></thead>
  <tbody>
    <tr><td>入口</td><td>[知识库] 原有入口</td><td>[推理] 本次入口</td><td>说明正向/逆向关系</td></tr>
    <tr><td>结果</td><td>[知识库] 原结果页</td><td>[跨域参考] 本次结果页</td><td>说明复用边界</td></tr>
  </tbody>
</table>
```

**填充 Slot F**：
- 页面清单表格：名称 | 类型(new/reuse) | 参考来源([知识库]/[跨域参考]/[推理]) | 状态

**crossRefs 标记采纳**：
- 对 `crossRefs[]` 中每个条目，标记 `adopted: true/false` + `reason`
- adopted=true：该截图/模式被实际采纳进设计
- adopted=false + reason：说明为何不采纳（模式不匹配/业务语义不同等）

**I-gallery 追加**：
- 本阶段新发现的跨域截图追加到 I-gallery

**产出**：更新 HTML（A-F 已填充）。

**保存状态**：`phase: "inventory"`，写入 `inventory[]`

**★ 检查点**：
> Part 2（设计规划）已填充。页面清单是否需要调整？确认后 P3 设计全部页面。

---

## P3 · 逐页设计 → Slot G

**对 inventory 中所有页面一次性设计**（不逐页确认）：

**每页流程：**
1. 读取知识库中该页的视觉描述、交互规则
2. 检查 `crossRefs[]` 中 adopted=true 的条目，引用截图 URL 作参考
3. 生成 iOS 375×812 pt UI（phone-frame 容器）
4. 附设计说明 + 来源标注

**填充 Slot G**（位于「工作台」Tab）：
- 推荐用 matrix 布局：每页一列（node-card → phone-frame → evidence-note），并排对比
- 也可用 phone-grid 平铺展示
- 每页附设计决策说明（[知识库]/[跨域参考]/[推理]）
- 每页必须包含 `node-card`、`phone-frame > phone-screen ps`、`evidence-note` 三层结构
- `evidence-note` 至少包含「来源」「关键决策」「复用边界」
- phone-frame 是平面画板，不是设备壳；不得添加黑色描边、设备圆角、刘海/外壳装饰

**I-gallery 追加**（位于「工作台」Tab）：
- 设计过程中新引用的跨域截图追加到 I-gallery
- 推荐用 candidate-board 暗色面板分组展示

**产出**：更新 HTML（A-G 已填充）。

**保存状态**：`phase: "page-design"`

**★ 检查点**：
> Part 3（设计方案）全部完成。是否需要修改？确认后进入 P4。

---

## P4 · 整体评审 → Slot H + summary + I-summary 定稿

**读取**：重新读取命中文档的业务规则、异常路径

**填充 Slot summary** — 决策摘要（位于汇报 Tab 末尾）：
- 定位 `id="slot-summary"`，替换 placeholder
- 用 KV 表格呈现：本轮结论 / 核心理由 / 复用边界 / 最终产物
- 位于 04 命中与采用之后、footer 之前

**填充 Slot H** — 四维度自检：
- 流程完整性（主链路 + 异常覆盖）
- 业务规则一致性（每条标注 [知识库]）
- UI 规范合规
- 信息不足项（知识库缺失，需人工补充）
- 使用 `.check-item` 输出四维自检；如有缺口，同步显示 `slot-gap-check`，用表格列出决策点、覆盖情况、缺口

**定稿 I-summary** — 替换 `slot-i-summary` 的 placeholder，写入：
- 标题「知识库精确命中」，`.ledger` 表格：文档 → 章节 → Slot → 贡献
- 标题「跨域参考采纳」，`.ledger` 表格：来源 → 页面 → 目标页 → 采纳? → 说明
- 标题「推理清单」，`.ledger` 表格：Slot → 内容 → 推理原因
- 标题「知识库建议补充」，`.ledger` 表格：缺失项 → 影响
- 矛盾检测：如发现知识库内不同文档间的数据矛盾，用 `.callout.warn` 提醒；若没有则写明未发现关键矛盾

**注意**：I-summary 只操作 `id="slot-i-summary"`，不触碰 `id="slot-i-gallery"`。

**产出**：最终版 HTML，全部槽位填满。

**★ 检查点**：
> 汇报已完成，全部槽位填满。是否通过？
> - 通过 → `phase: "completed"`
> - 不通过 → 指定修改内容

---

## 产出约束

| 约束 | 规格 |
|------|------|
| 最终产物 | `业务知识库/output/{简称}-report.html` |
| 模板来源 | `业务知识库/设计方案汇报模板.html`（双 Tab：汇报+工作台） |
| 设计尺寸 | iOS 375 × 812 pt |
| 来源标注 | [知识库] / [跨域参考] / [推理] |
| Slot I | gallery(工作台Tab追加) + summary(汇报Tab定稿) 双区隔离 |
| Slot summary | 决策摘要，P4 填充，KV 表格，汇报 Tab 末尾 |

## 会话状态

```json
{
  "sessionId": "ses_{YYYYMMDD}_{shortId}",
  "phase": "understanding | inventory | page-design | review | completed",
  "requirement": "需求原文",
  "kbHits": ["精确命中文档路径"],
  "crossRefs": [
    {
      "targetPage": "要设计的页面",
      "refPage": "知识库中相似页面",
      "refDoc": "来源文档标题",
      "refDocPath": "来源文档路径",
      "pattern": "相似模式",
      "imageUrl": "COS截图链接",
      "visualDesc": "视觉描述摘要",
      "adopted": false,
      "reason": "未采纳原因（adopted=false时填写）"
    }
  ],
  "reportFile": "output/{简称}-report.html",
  "inventory": [
    { "id": "p1", "name": "页面名", "type": "new|reuse", "status": "pending|completed", "reference": "参考来源" }
  ]
}
```

会话状态精简原则：HTML 是 source of truth（slots 内容、kbAudit 都体现在 HTML 中），session 只追踪阶段、命中和引用。

## 核心原则

1. **模板驱动** — Skill 的工作 = 找到正确的 slot div → Edit 替换 placeholder → 下一个
2. **I-gallery 只追加不替换** — 防止 P4 定稿时覆盖 P1 的截图画廊
3. **来源透明** — [知识库]/[跨域参考]/[推理] 三级标注
4. **按需读取** — 利用 manifest sections 定位章节，不一次读全文
5. **渐进可见** — 每阶段更新 HTML，浏览器实时预览
6. **可回退** — 用户指定槽位重新填充，不影响其他

# biz-solution — 交互式设计方案工作流

基于业务知识库（18 篇 Markdown x 6 业务域），以一次 Codex 会话连续推进 4 个阶段，产出设计方案 HTML。

## Trigger Keywords

设计方案, 知识库, biz-solution, 需求分析, 页面设计

## 模板结构（双 Tab）

模板包含两个视图 Tab：
- **汇报 Tab**：领导视角。决策摘要 + 项目概览 + 设计规划 + 验证 + 命中与采用
- **工作台 Tab**：设计师视角。无限画布 + 页面设计稿 + 跨域参考候选池

## 工作流定位

`biz-solution` 是 Codex 侧主入口。用户只需要围绕这个 Skill 持续对话；Agent 根据 `业务知识库/.biz-session.json` 的 `phase` 判断当前应执行哪个阶段，每阶段完成后停下来等待用户确认、补充或修改，再继续下一阶段。

底层阶段仍拆为 4 个独立 Skill，用于保持职责清晰：

```
biz-p1 → Slot A-D（需求理解 + 知识库预检）       [汇报 Tab]
biz-p2 → Slot E-F（页面清单 + 流程同构）         [汇报 Tab]
biz-p3 → Slot G（逐页设计 375x812）              [工作台 Tab]
biz-p4 → Slot summary + H + I-summary（评审）    [汇报 Tab]
```

## 交互式推进规则

### 1. 启动或续跑

1. 读取 `业务知识库/manifest.json`
2. 读取 `业务知识库/设计方案汇报模板.html`
3. 检查 `业务知识库/.biz-session.json`
   - 不存在：把用户本轮需求作为新需求，执行 P1
   - 存在且用户明确说继续/确认/下一步：按当前 `phase` 执行下一阶段
   - 存在且用户提出修改意见：先更新当前阶段产物和 session，再保持在当前阶段或进入下一阶段
   - 存在且用户给出全新需求：询问是否覆盖旧会话；用户确认后先删除旧 `.biz-session.json`，再新建 session

### 1.1 运行态隔离

`业务知识库/.biz-session.json` 是唯一可用于判断续跑阶段的运行态文件。`业务知识库/output/` 只是历史产物目录，绝不能用其中已有 HTML 判断是否续跑。

启动新项目时必须遵守：
- 若用户确认新建/覆盖项目，先删除旧 `业务知识库/.biz-session.json`
- 新报告使用本次需求生成新的唯一 `reportFile`
- 不读取、复用或续写旧 `output/*.html`，除非用户明确指定要基于某个旧报告修改
- 如果新报告 slug 与旧文件重名，先覆盖该目标文件；不要把旧内容当作当前阶段来源

### 2. Phase 状态机

`phase` 只允许使用以下枚举：

```
new -> understanding -> inventory -> page-design -> completed
```

推进关系：

| 当前状态 | 用户意图 | 执行动作 | 写入状态 |
|---|---|---|---|
| 无 session / new | 提供需求 | 执行 P1 | understanding |
| understanding | 确认继续 | 执行 P2 | inventory |
| inventory | 确认继续 | 执行 P3 | page-design |
| page-design | 确认继续 | 执行 P4 | completed |
| completed | 新需求 | 询问是否新建会话 | new |

如果发现旧 session 使用了非枚举状态（例如 `review`），根据已存在字段修正：
- 有 `inventory[]` 且页面均 `completed`：视为 `page-design`
- 有 `inventory[]` 但未全部完成：视为 `inventory`
- 只有 `kbHits/crossRefs/reportFile`：视为 `understanding`
- 无法判断时，向用户说明状态异常并请求确认从哪一阶段继续

### 3. 阶段输出契约

每阶段必须写入下一阶段需要的最小状态：

```json
{
  "sessionId": "ses_YYYYMMDD_slug",
  "phase": "understanding | inventory | page-design | completed",
  "requirement": "用户原始需求",
  "kbHits": ["业务知识库/ai/...md"],
  "crossRefs": [],
  "inventory": [],
  "decisions": [],
  "reportFile": "业务知识库/output/{slug}-report.html"
}
```

- P1 必须写入：`sessionId`、`phase`、`requirement`、`kbHits`、`crossRefs`、`reportFile`
- P2 必须写入：`phase`、`inventory`，并给 `crossRefs[]` 标记 `adopted/reason`
- P3 必须写入：`phase`，并把 `inventory[].status` 更新为 `completed`
- P4 必须写入：`phase: "completed"`，并保留所有证据字段供复盘

### 4. 用户确认点

每阶段结束后必须停止继续执行，并用一句话询问用户是否进入下一阶段：

- P1 后：确认需求理解和知识库覆盖度，是否进入页面清单？
- P2 后：确认流程和页面清单，是否进入逐页设计？
- P3 后：确认设计稿方向，是否进入整体评审？
- P4 后：告知最终 HTML 路径和完成状态

除非用户明确要求“一次性跑完”，否则不要跨确认点自动进入下一阶段。

## 共享规则

### 来源标注
- **[知识库]** = 精确命中文档（标注文档+章节）
- **[跨域参考]** = 跨域相似页面（标注来源+截图链接）
- **[推理]** = 知识库无覆盖，Agent 推理
- 输出到 HTML 的来源标识一律使用纯文本方括号，不使用 emoji。

### 槽位操作
- 定位 `id="slot-X"` → 替换 placeholder
- **slot-d**（关键指标）= 默认 `display:none`，仅用户主动提及指标时才填充并显示
- **slot-summary** = P4 填充决策摘要（KV 表格，汇报 Tab 末尾）
- **I-gallery**（`id="slot-i-gallery"`）= 只追加不替换（工作台 Tab）
- **I-summary**（`id="slot-i-summary"`）= P4 定稿时写入（汇报 Tab）

### 按章节读取
参照 manifest `sectionSchema`，按阶段只读对应章节，不读全文。

### 状态文件是唯一真相源
跨轮对话不要依赖记忆判断进度。每次响应前先读取 `.biz-session.json`，再决定当前阶段、可修改范围和下一步。

## References

- `业务知识库/manifest.json` — 知识路由索引
- `业务知识库/设计方案汇报模板.html` — 双 Tab 模板（汇报+工作台）
- `业务知识库/ai/` — 18 篇知识文档

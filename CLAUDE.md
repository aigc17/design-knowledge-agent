# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## 项目定位

业务知识库驱动的设计方案自动化 — 18 篇 Markdown 知识文档 + Claude Code/Codex/Cursor 入口 + HTML 模板。设计师输入一句需求，在同一段对话中按确认点推进 4 阶段，产出带来源追溯的设计方案 HTML。

## 快速开始

```bash
claude                                    # 打开 Claude Code
# 输入：/biz-solution 还借宝新增退款确认页
# Codex 中使用 biz-solution skill 后，回复“继续”即可按 phase 进入下一阶段
open 业务知识库/output/{简称}-report.html   # 浏览器预览产物
```

## 架构：知识路由驱动

```
用户需求（自然语言）
  ↓
Skill 入口 (.claude/commands/)
  ├─ /biz-solution  — 4 阶段设计方案产出（主力，有状态机）
  ├─ /biz-review    — 设计方案评审（单次执行，无状态）
  └─ /biz-analyze   — 需求拆解分析（单次执行，无状态）
  ↓
manifest.json 知识路由（~300 token）
  ├─ 第一遍：keywords/match 精确命中 1-3 篇文档
  └─ 第二遍：pages[]/flowArchetype 跨域相似匹配
  ↓
知识文档 ai/{域}/*.md（按 sectionSchema 分章节读取，不读全文）
  ↓
HTML 模板填槽 → output/{简称}-report.html
```

三个 Skill 共享同一套 `manifest.json` 路由和 HTML 模板。`/biz-solution` 是唯一使用 `.biz-session.json` 状态持久化的 Skill。

## 模板结构（双 Tab × 槽位系统）

| Tab | 受众 | 槽位 |
|-----|------|------|
| 汇报 | 领导 | A-D（概览） + E-F（规划） + H（验证） + I-summary（命中汇总） + slot-summary（决策摘要，末尾） |
| 工作台 | 设计师 | G（页面设计稿，matrix 布局） + I-gallery（跨域参考候选池，追加区） |

**关键隔离**：Slot I 物理拆分为 `I-gallery`（工作台 Tab，只追加不替换）+ `I-summary`（汇报 Tab，P4 一次性定稿）。

**隐藏槽位**：`slot-d`（关键指标）和 `slot-gap-check`（待确认事项）默认 `display:none`，有内容时由 Skill 显示。

## 工作流：4 阶段填槽

```
P1 需求理解 → Slot A-D + gap 预检        读 sectionSchema.P1（一~三章）  [汇报 Tab]
P2 页面清单 → Slot E-F + 流程同构        读 sectionSchema.P2（四~五章）  [汇报 Tab]
P3 逐页设计 → Slot G（375×812 iOS）      读 sectionSchema.P3（四章）     [工作台 Tab]
P4 整体评审 → slot-summary + H + I-summary 读 sectionSchema.P4（五~六章） [汇报 Tab]
```

每阶段产出写入 `output/{简称}-report.html`，浏览器实时预览。阶段间暂停确认。

## 知识路由核心机制

`manifest.json` 是整个系统的路由层（~300 token，18 篇文档 × 6 域）：

- **sectionSchema** — 按阶段映射章节号，**按需读取，不读全文**
- **documents[].keywords/match** — 精确命中规则
- **documents[].pages[]** — 跨域页面模式匹配（提取截图 URL + 视觉描述）
- **documents[].flowArchetype** — 7 种流程原型：交易购买 | 注销退出 | 注册登录 | 借款申请 | 身份认证 | 账单支付 | 服务开通
- **documents[].relatedFlows** — 上下游关联文档 ID

### 知识文档标准结构（6 章）

每篇 `ai/{域}/*.md` 遵循统一章节：

```
## 一、项目目标        ← P1 读取
## 二、目标用户特征    ← P1 读取
## 三、数据指标        ← P1 读取
## 四、流程与页面识别总表  ← P2/P3 读取
## 五、黄金流程完整描述    ← P2/P4 读取
## 六、经验沉淀           ← P4 读取
```

新增知识文档：在 `ai/{域}/` 下建 `.md`（遵循此 6 章结构），然后注册到 `manifest.json` 的 `documents[]`。

## 来源标注

每条设计内容必须标注三级来源：
- **📗 知识库** — 精确命中文档（标注文档+章节）
- **📘 跨域参考** — 跨域相似页面截图/模式
- **🔮 推理** — 知识库无覆盖，Agent 独立推理

## AI 开发注意事项

1. **I-gallery 只追加不替换** — 永远不要用 Edit 替换 I-gallery 全部内容，每阶段在 `</div>` 闭合标签前追加 crossref-card
2. **Edit HTML 用 `id="slot-X"` 定位** — 不要用通用闭合标签，HTML 中相同结构大量重复会导致 Edit 多处匹配失败
3. **知识库按 sectionSchema 分章节读取** — 不要一次读全文，18 篇文档含大量 Gemini 视觉描述会撑爆上下文
4. **`.biz-session.json` 是运行时状态** — 不入库，HTML 是 source of truth
5. **`output/` 下的 HTML 豁免 800 行限制** — 填槽生成的报告文件，行数由内容决定
6. **`examples/` 是验证案例** — 物理隔离，不在 manifest 索引范围，Agent 不应将其作为业务知识来源
7. **HTML 模板唯一外部依赖是 Mermaid CDN** — 其余全部内联（CSS + JS）
8. **工作台手机稿是平面画板** — `.phone-frame` 不画设备壳，不加黑色描边，不加圆角；页面内部组件可保留自身圆角
9. **运行态隔离** — 新需求不要读取 `output/` 历史 HTML 判断进度；只以 `.biz-session.json` 为续跑依据，用户确认新项目后先删除旧 session

## 目录结构

```
.claude/commands/       - Skill 入口（3 个：biz-solution / biz-review / biz-analyze）
业务知识库/
  manifest.json         - 知识路由索引（18 篇文档 × 6 业务域）
  设计方案汇报模板.html  - 双 Tab HTML 模板（汇报 + 工作台，~500 行）
  ai/                   - 知识文档区（6 域：平台/支付还款/普惠/海外/消金/风控）
  output/               - 运行时输出（.gitignore，不入库）
  examples/             - 验证案例（物理隔离，不被 AI 误命中）
```

## 踩坑记录

### 2026-06-15 Slot I 覆盖事故
- **现象**：P4 定稿 Slot I 时，Edit 替换了整个 Slot I 内容，P1 写入的跨域截图画廊被覆盖消失
- **根因**：一个 slot 同时承担 append-only 和 replace-once 双职责
- **解决**：物理拆分为 I-gallery（追加区）+ I-summary（定稿区）
- **教训**：一个 slot 不能同时是追加和替换，必须物理隔离

### 2026-06-15 Edit 多处匹配失败
- **现象**：用 `</div>\n    </div>` 做 old_string 定位时匹配到 3 处，Edit 拒绝执行
- **解决**：使用 `id="slot-X"` 等唯一属性作为 Edit 定位锚点
- **教训**：Edit slot 内容时 old_string 必须包含该 slot 的 id 属性

### 2026-06-15 一次性读全文撑爆上下文
- **现象**：P1 读取知识库全文导致后续阶段上下文耗尽
- **解决**：manifest 新增 `sectionSchema`，按阶段只读对应章节
- **教训**：知识库文档要按章节精确读取，永远不要一次 Read 全文

[PROTOCOL]: 变更时更新此头部，然后检查 CLAUDE.md

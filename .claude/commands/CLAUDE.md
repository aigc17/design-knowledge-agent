# Folder: .claude/commands
> L2 | 父级: .claude/

> 业务知识库 Skill 入口层 — 三个场景化 Skill，共享 manifest.json 知识路由

## 成员清单
- `biz-solution.md`: 设计方案产出 v3，双 Tab 模板驱动 + Slot I 双区隔离 + slot-summary 决策摘要 + KB 预检 + 按章节读取，四阶段（P1→P4）
- `biz-review.md`: 设计方案评审，对照知识库业务规则/异常路径/经验沉淀进行一致性检查
- `biz-analyze.md`: 需求拆解分析，定位业务域、梳理关联流程、盘点可复用资产、拆解设计任务

## 架构位置

```
用户 → /biz-solution | /biz-review | /biz-analyze
         ↓
   manifest.json（知识路由，~300 token）
         ↓
   业务知识库/ai/{域}/{文档}.md（按需读取 1-3 篇）
```

## 共享协议
- 知识路由：三个 Skill 均先读 `业务知识库/manifest.json` 匹配文档
- 产出约束：iOS 375×812 pt，Pencil MCP 生成 .pen 设计文件
- 汇报模板：`设计方案汇报模板.html`（双 Tab：汇报 + 工作台，10+1 槽位）
- 状态持久化：`.biz-session.json`（仅 /biz-solution 使用）

**⚠️ 自指声明**：一旦本文件夹新增/删除/修改 Skill 或职责变动，请立即更新本文档。

[PROTOCOL]: 变更时更新此头部，然后检查 CLAUDE.md

## 踩坑记录

### 2026-06-15 Slot I 覆盖事故
- **现象**：P4 定稿 Slot I 时，Edit 替换了整个 Slot I 内容，P1 写入的紫色跨域截图画廊被覆盖消失
- **根因**：Slot I 承担双重职责（运行日志 + 最终汇总），P4 的 Edit old_string 匹配了全部 I 区内容
- **解决**：将 Slot I 拆为 `I-gallery`（追加区，永不替换）+ `I-summary`（P4 定稿区），写入模板 HTML
- **教训**：一个 slot 不能同时是 append-only 和 replace-once，必须物理隔离

### 2026-06-15 Edit 多处匹配失败
- **现象**：用 `</div>\n    </div>` 做 old_string 定位时匹配到 3 处，Edit 拒绝执行
- **根因**：HTML 中相同闭合标签结构大量重复
- **解决**：使用 `id="slot-X"` 等唯一属性作为 Edit 定位锚点
- **教训**：Edit slot 内容时，old_string 必须包含该 slot 的 id 属性确保唯一

### 2026-06-15 一次性读全文撑爆上下文
- **现象**：P1 读取 KB 全文（含所有页面的 Gemini 视觉描述），导致后续阶段上下文耗尽
- **根因**：KB 文档含大量图片描述（每页 5-10 行），18 篇文档总计数千行
- **解决**：manifest 新增 `sectionSchema`，Skill 按阶段只读对应章节（P1 读一~三，P2 读四~五）
- **教训**：KB 文档要按章节精确读取，永远不要一次 Read 全文

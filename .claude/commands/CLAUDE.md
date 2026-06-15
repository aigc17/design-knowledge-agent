# Folder: .claude/commands
> L2 | 父级: .claude/

> 业务知识库 Skill 入口层 — 三个场景化 Skill，共享 manifest.json 知识路由

## 成员清单
- `biz-solution.md`: 设计方案产出，四阶段状态机（P1需求理解→P2页面清单→P3逐页设计→P4整体评审），人机协同迭代
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
- 汇报模板：`设计方案汇报模板.html`（9 槽位 × 4 阶段映射）
- 状态持久化：`.biz-session.json`（仅 /biz-solution 使用）

**⚠️ 自指声明**：一旦本文件夹新增/删除/修改 Skill 或职责变动，请立即更新本文档。

[PROTOCOL]: 变更时更新此头部，然后检查 CLAUDE.md

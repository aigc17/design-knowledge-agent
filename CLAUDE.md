# 业务知识库 × Claude Code Skills — 设计方案自动化
Markdown 知识文档 + Claude Code Skill + HTML 模板

## 快速开始

```bash
# 1. clone 仓库
git clone <repo-url> && cd 03-知识库应用再测试

# 2. 用 Claude Code 打开
claude

# 3. 输入需求，Skill 自动 4 阶段产出设计方案
/biz-solution 还借宝新增退款确认页
```

## 目录结构

```
.claude/commands/       - Skill 入口（3 个场景化命令）
业务知识库/
  manifest.json         - 知识路由索引（18 篇文档 × 6 业务域）
  设计方案汇报模板.html  - 9+1 槽位 HTML 模板（Skill 的产出骨架）
  ai/                   - 知识文档区（6 域：平台/支付还款/普惠/海外/消金/风控）
  output/               - 运行时输出（.gitignore，不入库）
设计方案汇报模板.html    - 早期版本（已废弃，以 业务知识库/ 下的为准）
知识库应用架构讨论.md    - 架构设计文档
```

## 工作流：/biz-solution

```
P1 需求理解 → Slot A-D（背景/目标/用户/指标）+ KB 预检
P2 页面清单 → Slot E-F（流程/清单）+ 流程同构分析
P3 逐页设计 → Slot G（375×812 iOS 设计稿）
P4 整体评审 → Slot H（四维自检）+ Slot I-summary（证据汇总）
```

每阶段产出写入 `output/{简称}-report.html`，浏览器实时预览。

## 来源标注

- 📗 KB — 精确命中知识文档
- 📘 跨域参考 — 跨业务域的相似页面截图/模式
- 🔮 推理 — KB 无覆盖，Agent 独立推理

## 知识文档结构（统一 6 章）

| 章节 | 用于阶段 |
|------|----------|
| 一、项目目标 | P1 |
| 二、目标用户特征 | P1 |
| 三、数据指标 | P1 |
| 四、流程与页面识别总表 | P2, P3 |
| 五、黄金流程完整描述 | P2, P4 |
| 六、经验沉淀 | P4 |

## 配置文件

- `.gitignore` — 排除 `.biz-session.json` 和 `output/`
- `manifest.json` — 知识路由，含 `sectionSchema`（按阶段读取章节映射）

## AI 开发注意事项

1. Slot I 分为 `I-gallery`（追加区）+ `I-summary`（定稿区），永远不要用 Edit 替换 I-gallery 全部内容
2. Edit HTML 时用 `id="slot-X"` 作为定位锚点，不要用通用闭合标签
3. KB 文档按 `sectionSchema` 分章节读取，不要一次读全文
4. `.biz-session.json` 是运行时状态，不入库，HTML 是 source of truth

[PROTOCOL]: 变更时更新此头部，然后检查 CLAUDE.md

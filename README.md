# Design Knowledge Agent

> 业务知识库驱动的设计方案自动化工具 — 18 篇业务知识文档变成可交付的设计方案。支持 Claude Code / Codex CLI / Cursor 三端。

## 这是什么

设计师输入一句需求描述，AI 自动完成 4 个阶段，产出一份带完整来源追溯的设计方案 HTML（双 Tab：汇报视图 + 工作台视图）：

```
/biz-solution 还借宝新增退款确认页
```

```
P1 需求理解 → 背景 / 目标 / 用户 / 指标（+ 知识库覆盖度预检）     [汇报 Tab]
P2 页面清单 → 设计流程 / 页面清单 / 流程同构分析                 [汇报 Tab]
P3 逐页设计 → 375x812 iOS 设计稿（无限画布 + matrix 布局）      [工作台 Tab]
P4 整体评审 → 决策摘要 + 四维自检 + 知识库证据汇总               [汇报 Tab]
```

每条设计决策标注来源：知识库精确命中 / 跨域相似参考 / AI 推理。

## 快速开始

### Claude Code

```bash
git clone https://github.com/aigc17/design-knowledge-agent.git
cd design-knowledge-agent
claude
# 输入：/biz-solution 还借宝新增退款确认页
```

### Codex CLI

```bash
git clone https://github.com/aigc17/design-knowledge-agent.git
cd design-knowledge-agent
codex "使用 biz-solution skill，需求：还借宝新增退款确认页"
```

### Cursor

```
1. 用 Cursor 打开 clone 下来的项目
2. .cursor/rules/biz-solution.mdc 会被自动加载
3. 在 Agent 模式对话：帮我产出设计方案，需求是还借宝新增退款确认页
```

## 知识库覆盖范围

6 个业务域，18 篇流程文档：

| 业务域 | 文档 | 负责人 |
|--------|------|--------|
| 平台 | 注销流程、注册登录流程 | @emericshao |
| 风控 | 还借宝、还款减压、提额降息 | @sheepxia @elevenruan |
| 消金 | 用信流程、授信流程 | @uluckliu @jaceywang |
| 支付还款 | 还款流程 | @toriliang |
| 普惠 | 授信、用信 | @bearloli |
| 海外 | 墨西哥(登录/授信/用信/还款)、印尼(登录/授信/用信/还款) | @cherryzheng |

## 如何贡献新知识文档

### 1. 按模板写文档

在 `业务知识库/ai/{业务域}/` 下新建 `.md` 文件，遵循统一 6 章结构：

```markdown
## 一、项目目标
## 二、目标用户特征
## 三、数据指标
## 四、流程与页面识别总表
### 页面识别总表
| 序号 | 页面名称 | Figma 截图 | Gemini 视觉描述 | Figma 链接 |
## 五、黄金流程完整描述
### 1. 流程一句话概述
### 2. 主链路（成功路径）
### 3. 关键业务状态与对象
### 4. 分支与异常路径
### 5. 给其他 AI 的使用提示
## 六、经验沉淀
```

关键：**页面识别总表**中的截图 URL 和视觉描述是跨域参考的核心素材。

### 2. 注册到 manifest.json

在 `业务知识库/manifest.json` 的 `documents[]` 中添加一条：

```json
{
  "id": "your-doc-id",
  "title": "文档标题",
  "domain": "业务域id",
  "path": "业务知识库/ai/{域}/{编号}-{名称}.md",
  "keywords": ["关键词1", "关键词2"],
  "match": "什么需求会命中这篇文档",
  "summary": "一句话摘要",
  "pages": ["页面1", "页面2"],
  "flowArchetype": "交易购买|注销退出|注册登录|借款申请|身份认证|账单支付|服务开通",
  "flowSteps": ["步骤1", "步骤2"],
  "flowExceptions": ["异常1"],
  "relatedFlows": ["关联文档id"]
}
```

### 3. 提交 PR

```bash
git add 业务知识库/ai/... 业务知识库/manifest.json
git commit -m "feat: 新增{文档名}知识文档"
git push
```

## 项目结构

```
.claude/commands/              Claude Code Skill
  biz-solution.md              设计方案产出（主力）
  biz-review.md                设计方案评审
  biz-analyze.md               需求拆解分析

.codex/skills/                 Codex CLI Skill（4 阶段拆分）
  biz-solution/SKILL.md        总览
  biz-p1/ ~ biz-p4/            4 个独立阶段 Skill

.cursor/rules/                 Cursor Rules
  biz-solution.mdc             同工作流，被动规则格式

CLAUDE.md                      Claude Code 项目指令
AGENTS.md                      Codex CLI 项目指令

业务知识库/
  manifest.json                知识路由索引
  设计方案汇报模板.html         双 Tab HTML 模板（汇报 + 工作台）
  ai/                          知识文档（6 域 x 18 篇）
  output/                      运行时输出（不入库）
  examples/                    验证案例（物理隔离）
```

## 工作原理

```
用户输入需求
    ↓
manifest.json 路由 ──→ 精确命中 1-3 篇文档（读对应章节）
    ↓                  ──→ 跨域扫描全部 pages[]（找相似页面截图）
    ↓
4 阶段填充 HTML 模板 ──→ 每阶段暂停确认 ──→ 浏览器预览
    ↓
output/{名称}-report.html（最终产物）
```

## License

Internal use only.

# 风控代还刷脸鉴权 - 知识库验证案例

本目录存放一次真实项目验证产物，用于证明业务知识库、视觉证据和 Skills 可以被 Agent 读取并组合成方案。

## 文件

- `risk-face-auth-report.html`: 汇报报告，说明需求、边界、流程、复用依据、证据链和验收口径。
- `risk-face-auth-canvas.html`: 横向方案画布，展示 375px 页面、相似页面比对、搜索台账和候选池。

## 使用方式

1. 先读 `knowledge-assets/agent-skills/product-solution-generation/SKILL.md`。
2. 再读本目录两个 HTML，理解真实需求如何从知识库命中到页面输出。
3. 生成新方案时，沿用“需求进入 -> 知识命中 -> 证据比对 -> 方案输出 -> 规范自检”的工作流。

## 注意

这里是验证案例，不是业务知识本体。正式业务规则仍以 `knowledge-assets/业务知识库/ai/...` 与 `visual-evidence.json` 为准。

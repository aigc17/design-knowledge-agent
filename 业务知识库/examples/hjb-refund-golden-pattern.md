# 还借宝退款确认页黄金样例模式

> 本文件是输出形态参考，不是业务知识文档，不进入 `manifest.json`。  
> 目的：把已验证的 `hjb-refund-report.html` 结构沉淀为模板约束，避免后续产物退化。

## 适用边界

- 适用：验证 biz-solution 的 P2/P3/P4 输出结构。
- 不适用：作为知识库内容被路由、引用或推理。
- 真相源：`业务知识库/设计方案汇报模板.html` + `.codex/skills/biz-p*.md`。

## P1 项目概览

Slot A/B/C 是汇报首屏，必须使用模板组件，不输出裸段落。

Slot A 使用 `.content-item` 分段：

```html
<div class="content-item">
  {背景与业务痛点正文}
  <div class="source-note">[知识库] {文档/章节} + [推理] {说明}</div>
</div>
```

Slot B 使用 `.content-item` 输出目标：

```html
<div class="content-item">
  <strong>1. {目标名}</strong> — {目标说明}
  <div class="source-note">[知识库] {文档/章节}</div>
</div>
```

Slot C 使用 `.info-table` 输出目标用户：

```html
<table class="info-table">
  <thead><tr><th>客群</th><th>特征</th><th>场景</th></tr></thead>
  <tbody>
    <tr><td>{客群}</td><td>{特征}</td><td>{场景}</td></tr>
  </tbody>
</table>
<div class="source-note" style="margin-top:8px;">[知识库] {文档/章节}</div>
```

来源只放在 `.source-note` 中，不要把 `[知识库]`、`[跨域参考]`、`[推理]` 混进正文。

## P2 设计规划

Slot E 必须是两段式：

1. `flowchart-panel`：标题为「完整流程」，用 Mermaid 展示完整链路。
2. `ledger`：标题为「流程同构分析」，对比知识库原型流程与本次需求流程。

流程图必须包含：

- 主链路：入口页 -> 确认页 -> 系统处理 -> 结果页
- 分支：取消/返回、成功、失败、最终承接页
- 节点标签：`REUSE` / `NEW`
- 样式：复用绿色、新增蓝色、失败橙色

同构表必须覆盖：

| 环节 | 知识库原型流程 | 本次需求流程 | 映射说明 |
|------|----------------|--------------|----------|
| 入口 | [知识库] 原入口 | [推理] 本次入口 | 说明关系 |
| 确认 | [知识库] 原确认 | [推理] 本次确认 | 说明差异 |
| 处理 | [知识库] 原处理 | [推理] 本次处理 | 不适用写省略 |
| 结果 | [知识库] 原结果 | [跨域参考] 本次结果 | 说明复用边界 |

## P3 工作台

Slot G 推荐使用 `.matrix`，每页一列：

```html
<div class="flow-column">
  <div class="node-card">
    <strong>{页面名}</strong>
    <p><span class="page-tag tag-new">NEW</span> {页面职责}</p>
  </div>
  <div class="down-arrow"></div>
  <div class="phone-wrap">
    <div class="phone-frame">
      <div class="phone-screen ps">
        <!-- 375x812 页面内容 -->
      </div>
    </div>
  </div>
  <div class="evidence-note">
    <div><span>来源</span><p>[知识库] ... + [跨域参考] ...</p></div>
    <div><span>决策</span><p>关键设计判断</p></div>
    <div><span>边界</span><p>复用什么，不复用什么</p></div>
  </div>
</div>
```

`phone-frame` 是平面画板，不是设备壳：不加黑色描边、设备圆角、刘海或外壳装饰。

## P4 汇报定稿

Slot H 使用 `.check-item` 输出四维自检：

- 流程完整性
- 业务规则一致性
- UI 规范合规
- 信息不足项

Slot I-summary 使用四组表格：

1. 知识库精确命中：`文档 | 章节 | Slot | 贡献`
2. 跨域参考采纳：`来源 | 页面 | 目标页 | 采纳? | 说明`
3. 推理清单：`Slot | 内容 | 推理原因`
4. 知识库建议补充：`缺失项 | 影响`

如有知识库矛盾，用 `.callout.warn` 单独提示；如无矛盾，写明未发现关键矛盾。

[PROTOCOL]: 变更时更新此头部，然后检查 CLAUDE.md

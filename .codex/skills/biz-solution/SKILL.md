# biz-solution — 设计方案产出（总览）

基于业务知识库（18 篇 Markdown x 6 业务域），4 阶段产出设计方案 HTML。

## Trigger Keywords

设计方案, 知识库, biz-solution, 需求分析, 页面设计

## 模板结构（双 Tab）

模板包含两个视图 Tab：
- **汇报 Tab**：领导视角。决策摘要 + 项目概览 + 设计规划 + 验证 + 命中与采用
- **工作台 Tab**：设计师视角。无限画布 + 页面设计稿 + 跨域参考候选池

## 四阶段工作流

拆为 4 个独立 Skill，用户依次调用：

```
$biz-p1 {需求描述}    → Slot A-D（需求理解 + KB 预检）       [汇报 Tab]
$biz-p2               → Slot E-F（页面清单 + 流程同构）       [汇报 Tab]
$biz-p3               → Slot G（逐页设计 375x812）            [工作台 Tab]
$biz-p4               → Slot summary + H + I-summary（评审）  [汇报 Tab]
```

每个阶段独立执行、独立产出，用户确认后再调下一阶段。

## 共享规则

### 来源标注
- **KB** = 精确命中文档（标注文档+章节）
- **跨域参考** = 跨域相似页面（标注来源+截图链接）
- **推理** = KB 无覆盖，Agent 推理

### 槽位操作
- 定位 `id="slot-X"` → 替换 placeholder
- **slot-summary** = P4 填充决策摘要（KV 表格，汇报 Tab 第一屏）
- **I-gallery**（`id="slot-i-gallery"`）= 只追加不替换（工作台 Tab）
- **I-summary**（`id="slot-i-summary"`）= P4 定稿时写入（汇报 Tab）

### 按章节读取
参照 manifest `sectionSchema`，按阶段只读对应章节，不读全文。

## References

- `业务知识库/manifest.json` — 知识路由索引
- `业务知识库/设计方案汇报模板.html` — 双 Tab 模板（汇报+工作台）
- `业务知识库/ai/` — 18 篇知识文档

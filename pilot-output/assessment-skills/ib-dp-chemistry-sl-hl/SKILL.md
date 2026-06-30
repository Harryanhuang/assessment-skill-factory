# ib-dp-chemistry-sl-hl

## 适用考试

- IB DP Chemistry (Diploma Programme)
- First assessment 2025
- Published February 2023
- Standard Level (SL) / Higher Level (HL)

## 使用前必须读取的文件

1. `metadata.json` — assessment identity 和官方来源。
2. `topics.json` — topic tree、SL/HL/AHL 边界和分类坐标。
3. `assessment.json` — paper 结构、AO、题型、难度阈值、data booklet 规则。
4. `examples.md` — 分类样例，特别注意 SL vs HL/AHL 边界。
5. `references/page-map.json` — 关键结论回到 PDF 页码的映射。
6. `references/sl-hl-ahl-boundaries.json` — 边界验证表。

## 支持的使用模式

- 题库分类（route + topic classification）
- 校对 / out-of-scope 判断
- v1 不启用 teacher/student/parent-facing 模式

## 输出格式

分类结果至少包含：

```json
{
  "skill_id": "ib-dp-chemistry-sl-hl",
  "topic_id": "...",
  "level_scope": "SL_HL_only | SL_HL_plus_AHL | AHL_only",
  "confidence": 0.0,
  "evidence": ["..."],
  "flags": [],
  "verdict": "in_scope / out_of_scope / extension / review"
}
```

## 处理规则

- 必须区分 SL/HL 共同内容（SL_HL_only / SL_HL_plus_AHL）与仅 HL 的 AHL 内容（AHL_only）。
- 题目若只涉及 pre-DP 科学、普通化学常识、AP Chemistry 专属框架，判为 out_of_scope。
- 题目若要求 AHL 概念（如熵与自发性、质量光谱、高级 VSEPR、SN1/SN2 机理、标准电极电位详细计算等），仅当目标 level 为 HL 时判为 in_scope；若目标 level 为 SL，判为 out_of_scope 或 extension。
- 跨 topic 题需给出 primary_topic_id 和 secondary_topic_ids，并在 evidence 中说明主次原因。
- confidence < 0.75 时标记 `review`。
- 实验探究（scientific investigation）属于内部评估，不进入外部试卷 topic 分类，但可作为 assessment.json 的 IA 引用。

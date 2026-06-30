# ap-physics-1-2024

## 适用考试

- AP Physics 1: Algebra-Based
- Course and Exam Description, Effective Fall 2024
- College Board

## 使用前必须读取的文件

1. `metadata.json` — assessment identity 和官方来源。
2. `topics.json` — topic tree 和分类坐标（8 个 units + science practices）。
3. `assessment.json` — paper 结构、science practices、题型、难度阈值。
4. `examples.md` — 分类样例。
5. `references/page-map.json` — 关键结论回到 PDF 页码的映射。

## 支持的使用模式

- 题库分类（route + topic classification）
- 校对 / out-of-scope 判断
- v1 不启用 teacher/student/parent-facing 模式

## 输出格式

分类结果至少包含：

```json
{
  "skill_id": "ap-physics-1-2024",
  "topic_id": "...",
  "confidence": 0.0,
  "evidence": ["..."],
  "flags": [],
  "verdict": "in_scope / out_of_scope / extension / review"
}
```

## 处理规则

- 题目若涉及微积分、AP Physics 2 专题（热力学、静电学、电路、磁学、光学、近代物理）或大学物理内容，判为 `out_of_scope`。
- 题目若要求使用微积分推导或求解，判为 `out_of_scope`；AP Physics 1 只要求代数、几何和三角函数。
- 跨 topic 题需给出 `primary_topic_id` 和 `secondary_topic_ids`。
- `confidence < 0.75` 时标记 `review`。
- 分类时必须同时考虑 content topic 和 science practice/skill 维度（见 `assessment.json`）。

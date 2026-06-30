# caie-igcse-accounting-0452

## 适用考试

- Cambridge IGCSE Accounting (0452)
- Valid for exams in 2023, 2024 and 2025
- Version 1, published September 2020

## 使用前必须读取的文件

1. `metadata.json` — assessment identity 和官方来源。
2. `topics.json` — topic tree 和分类坐标。
3. `assessment.json` — paper 结构、AO、题型、难度阈值。
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
  "skill_id": "caie-igcse-accounting-0452",
  "topic_id": "...",
  "confidence": 0.0,
  "evidence": ["..."],
  "flags": [],
  "verdict": "in_scope / out_of_scope / extension / review"
}
```

## 处理规则

- 题目若涉及 A-Level / 大学会计内容，判为 out_of_scope。
- 题目若要求 partnership admission/departure/dissolution 或 cumulative preference shares，判为 out_of_scope（ syllabus 明确排除）。
- 跨 topic 题需给出 primary_topic_id 和 secondary_topic_ids。
- confidence < 0.75 时标记 `review`。

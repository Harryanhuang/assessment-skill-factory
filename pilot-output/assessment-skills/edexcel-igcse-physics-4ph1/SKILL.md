# edexcel-igcse-physics-4ph1

## 适用考试

- Pearson Edexcel International GCSE Physics (4PH1)
- First teaching September 2017, first examination June 2019
- Issue 4, September 2024 (current source version)

## 使用前必须读取的文件

1. `metadata.json` — assessment identity 和官方来源。
2. `topics.json` — topic tree 和分类坐标（8 个一级 topic）。
3. `assessment.json` — paper 结构、AO、题型、难度阈值、command words。
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
  "skill_id": "edexcel-igcse-physics-4ph1",
  "topic_id": "...",
  "confidence": 0.0,
  "evidence": ["..."],
  "flags": [],
  "verdict": "in_scope / out_of_scope / extension / review"
}
```

## 处理规则

- **Paper scope 规则**：
  - Paper 1 只覆盖非粗体、非 'P' reference 的内容（占 61.1%，2 小时，110 分）。
  - Paper 2 覆盖全部内容，包括粗体 / 'P' reference 的 Physics-only 内容（占 38.9%，1 小时 15 分，70 分）。
- **Double Award 内容**：本 skill 不覆盖 International GCSE Science (Double Award)。
- 题目若涉及 A-Level / 大学物理内容，判为 `out_of_scope`。
- 跨 topic 题需给出 `primary_topic_id` 和 `secondary_topic_ids`。
- confidence < 0.75 时标记 `review`。

# aqa-gcse-biology-8461

## 适用考试

- AQA GCSE Biology (8461)
- 适用于 2016 年 9 月起教学、2018 年起考试的版本及后续 ongoing 版本
- Version 1.0, 21 April 2016

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
  "skill_id": "aqa-gcse-biology-8461",
  "topic_id": "...",
  "confidence": 0.0,
  "evidence": ["..."],
  "flags": [],
  "verdict": "in_scope / out_of_scope / extension / review"
}
```

## 处理规则

- 题目若涉及 A-Level Biology 内容，判为 out_of_scope。
- 题目若来自 AQA Combined Science: Trilogy 但不属于 GCSE Biology 8461 范围，判为 out_of_scope。
- Foundation/Higher Tier 必须显式处理：Higher-only 内容不能作为 Foundation 题目；Foundation 内容可以出现在 Higher 试卷中。
- Paper 1 覆盖 Topics 4.1-4.4；Paper 2 覆盖 Topics 4.5-4.7。Topic 4.8 Key ideas 为跨主题综合，不单独对应某一张 paper。
- 跨 topic 题需给出 primary_topic_id 和 secondary_topic_ids。
- Working scientifically (WS1-WS4) 和 required practicals 作为 skill/assessment 链接，不当作普通 content topic。
- confidence < 0.75 时标记 `review`。

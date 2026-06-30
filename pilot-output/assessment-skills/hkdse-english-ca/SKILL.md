# hkdse-english-ca

## 适用考试

- HKDSE English Language
- Senior Secondary (S4-S6)
- Curriculum and Assessment Guide 2021 edition, effective from Secondary 4 in the 2021/22 school year
- CDC / HKEAA

## 使用前必须读取的文件

1. `metadata.json` — assessment identity 和官方来源。
2. `topics.json` — topic tree 和分类坐标（strand / learning objective / module / text type 粒度）。
3. `assessment.json` — paper 结构、AO、题型、难度阈值、SBA 规则。
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
  "skill_id": "hkdse-english-ca",
  "topic_id": "...",
  "confidence": 0.0,
  "evidence": ["..."],
  "flags": [],
  "verdict": "in_scope / out_of_scope / extension / review"
}
```

## 处理规则

- DSE English C&A Guide 不是传统 content syllabus，分类粒度以 strand、learning objective、language form/function、module、text type 为主，不强行拆成细粒度知识点。
- 题目若涉及 HKDSE 以外的英语考试（如 IELTS / TOEFL / 大学学术英语）或超出 S4-S6 课程范围，判为 out_of_scope。
- 题目若要求评估未在 C&A Guide 中列出的文本类型或语言项目，判为 out_of_scope 或 extension。
- 跨 topic 题需给出 primary_topic_id 和 secondary_topic_ids。
- confidence < 0.75 时标记 `review`。

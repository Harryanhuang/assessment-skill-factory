# Package Standard

> 本文件是 `Assessment Skill Registry与Builder执行标准.md` 中第 3 节“通用包结构”的只读投影。

## 通用包结构

```text
<skill_id>/
  SKILL.md
  metadata.json
  topics.json
  assessment.json
  examples.md
  references/        # 可选
  scripts/           # 可选
```

## SKILL.md

智能体入口说明。必须简洁，说明这个 skill 适用于哪个考试、考局、学科、spec 和使用模式。

必须包含：

- skill 名称和触发说明。
- 使用前必须读取的结构化文件。
- 支持的使用模式。
- 输出 verdict 或分类结果的基本格式。
- 低置信度、跨 topic、out-of-scope 时的处理规则。

## metadata.json

记录 assessment identity 和来源。必填字段：

- `schema_version`
- `id`
- `assessment_type`（v1 只启用 `syllabus`）
- `system`
- `board_or_owner`
- `subject`
- `level`
- `assessment_code`
- `valid_from` / `valid_to`
- `package_version`
- `source_provenance`（含 `source_document_type`、`source_layout_profile`、`local_archive_ref`、`file_checksum_sha256`、`document_version`、`page_refs`、`page_ref_convention`、`physical_page_ref_note` 等）

禁止写入 `status`、`active`、`registry_status`。

## 字段分层

- `assessment_type` 是 skill package 的注册分类，v1 只能写 `syllabus`。
- `source_provenance[].source_document_type` 才记录原始官方文件类型，例如 `course_and_exam_description`、`subject_guide`、`course_and_assessment_guide`、`exam_specification`。
- AP CED、IB subject guide、DSE C&A Guide、Edexcel/AQA specification 生成出来的 assessment skill，在 v1 metadata 中仍然使用 `assessment_type=syllabus`。
- validator 必须打回把 `course_and_exam_description`、`subject_guide`、`course_and_assessment_guide`、`exam_specification` 写入 `assessment_type` 的 package。

## Source Bundle 准入

builder 只能消费 extractor 产出的 source evidence bundle。消费前必须确认：

- `source-index.md` 已记录 assessment_identity、source_document_type、source_layout_profile、checksum、total_pages、document_version 和 page reference convention。
- `page-map.json` 对 topics / assessment / scope / out-of-scope 关键结论有 page_refs。
- `accuracy-verdict.json` 无 blocking_risks。
- `source-review/review-verdict.json` 不为 `fail`。
- 若 `dual_pass_checked=false`、reviewer 与 extractor 为同一执行者，或 high-impact rendered pages 未记录，registry patch 最高只能建议 `validated` 或 `reviewed`，不得建议 `active`。

## topics.json

记录 topic tree 和分类坐标。每个 topic 至少包含：

- `topic_id`（skill 内唯一且稳定）
- `topic_name`
- `parent_id`
- `scope`
- `out_of_scope`
- `classification_hints`
- `common_misclassifications`
- `prerequisite_topics`
- `overlap_links`

## assessment.json

记录考试结构、paper、题型、能力要求和难度规则。建议字段：

- `papers`
- `assessment_objectives`
- `question_styles`
- `difficulty_ladder`
- `marking_guidance`
- `lesson_use`（含 `supported`、`modes`、`teacher_use_contract_ref`、`student_use_contract_ref`、`parent_use_contract_ref`）
- `generation_constraints`

## examples.md

保存 golden examples。分类/校对/ coverage 用途至少包含：

- 3 个正确分类样例。
- 2 个易错分类样例。
- 2 个 out-of-scope 样例。
- 1 个跨 topic 样例。

若启用 teacher/student/parent-facing 模式，需按模式补充样例。

## references/

可选。放较长的结构化参考资料、topic 说明或官方文档摘录索引。不放官方全文，不放大段版权文本。

## scripts/

可选。放确定性校验脚本、转换脚本或 registry 工具。必须有最小测试，不把平台专用流程写死。

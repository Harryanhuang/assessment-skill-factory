# Validation Rubric

> validator 分级标准。与主标准第 5.7 节一致。

## P0 — 阻断注册和 active

存在以下任何一项时，skill 只能保持 `draft`，不能进入 registry：

- 来源缺失或 source review verdict 为 fail。
- `assessment_type` 不是 `syllabus`（v1）。
- 把 `course_and_exam_description`、`subject_guide`、`course_and_assessment_guide`、`exam_specification` 等 source document type 写入 `assessment_type`。
- topic tree 缺失。
- JSON 无法解析。
- `topic_id` 重复。
- 平台专用硬依赖（写死 EduFlow、Feishu、ClaudeTeam、Codex 等）。
- 多个 assessment 混在一个 skill。
- registry 与 metadata 版本不一致。
- `lesson_use.supported=true` 但 `modes=[]`。
- `lesson_use.supported=false` 但 `modes` 非空。

## P1 — 阻断 validated/reviewed

存在以下任何一项时，不能进入 `validated`/`reviewed`：

- 分类用途 examples 不足（缺正确/易错/out-of-scope/跨 topic）。
- 已声明教师/学生/家长模式但缺对应 contract 或 examples。
- out-of-scope 不清楚。
- assessment model 不完整（缺 paper/AO/question style/difficulty）。
- 版本字段缺失。
- primary PDF 缺页码/章节定位。
- PDF 抽取未复核或 extraction confidence 为 low。
- 缺人工复核包。
- accuracy verdict 存在 blocking risks。
- source-index 未说明 printed/physical page reference convention。
- 本地文件名与官方标题页、document_version 或 valid years 冲突且未解释。
- 已建议 registry_status=`active`，但 source bundle 的 `dual_pass_checked=false` 或缺独立 reviewer 留痕。

## P2 — 建议优化

存在以下情况时可进入 reviewer 语义验收，但必须记录优化建议：

- 命名说明不够清楚。
- classification hints 偏少。
- 已启用用户模式的样例覆盖不足但不缺失。
- references 索引不够细。
- PDF page-map 覆盖不够细。
- extraction confidence 为 medium 且 reviewer 已接受风险。
- rendered-pages 已生成但 reviewed_pages 留痕不完整。

## validated 准入条件

- P0 清零。
- P1 清零。
- P2 可存在但必须记录。

## active 准入条件

- 通过 reviewer 语义验收（verdict = 通过）。
- 所有 P0/P1 清零。
- source bundle 已完成独立 dual-pass review：`dual_pass_checked=true`，high-impact rendered pages 已复核，reviewed_pages 有实际页面记录。
- `lesson_use.supported=true` 或 `modes` 非空时，对应 Pilot 2 启用模式必须通过。

## 退出码建议（如实现 CLI）

- `0`：无 P0/P1，可直接进入 reviewer。
- `1`：存在 P1。
- `2`：存在 P0。

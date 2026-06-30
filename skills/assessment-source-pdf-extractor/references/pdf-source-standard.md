# PDF Source Standard

> 本文件说明 assessment source 的 PDF 处理原则。与主标准第 3.2.1 节一致。

## 来源权威

- 官方 PDF 或官方页面是 primary source。
- 二手网页、培训机构整理表、模型记忆只能作为辅助线索，不能替代 primary source。

## 字段分层

- `assessment_type` 是生成后 skill package 的注册分类。v1 只启用 `syllabus`，即使来源文件名叫 CED、subject guide、specification 或 curriculum guide，metadata 中也必须保持 `assessment_type=syllabus`。
- `source_document_type` 是原始官方文件的形态分类，可以是 `syllabus`、`subject_guide`、`course_and_exam_description`、`course_and_assessment_guide`、`exam_specification`、`specimen_paper` 或 `mark_scheme`。
- `source_layout_profile` 是抽取策略分类，例如 `standard_syllabus`、`ap_ced_unit_guide`、`ib_dp_subject_guide`、`edexcel_specification`。它决定 must_extract / must_not_extract / visual_review_required_pages。
- 禁止把 `source_document_type` 的值写入 metadata 的 `assessment_type`。

## 保存原则

- skill package 中保存结构化提炼、短 evidence、页码/章节定位和来源元数据。
- **不保存大段 PDF 原文**。
- 每个 topic tree、assessment model、scope/out-of-scope 的关键判断都应能追溯到 `source_provenance.page_refs` 或 references 中的页码索引。

## PDF 类型与抽取画像

| source_document_type | 典型样本 | 主要抽取对象 | 不能直接假设 |
| --- | --- | --- | --- |
| `syllabus` | CAIE syllabus | subject content、assessment objectives、paper structure、grade/series rules | 不能把简介、认可说明、行政说明抽成 topic |
| `subject_guide` | IB DP subject guide | syllabus outline、SL/HL distinction、syllabus content、skills/tools/inquiry、assessment outline、external/internal assessment、command terms | 不能把 learner profile、TOK/ATL/programme intro 或 teaching background 自动当成 examinable content |
| `course_and_exam_description` | AP CED | units、topics、science practices/skills、exam weighting、sample question model | 不能只抽 unit 名称而漏掉 practice/skill 维度 |
| `course_and_assessment_guide` | DSE English C&A Guide | curriculum aims、strands、learning objectives、assessment guidance、appendix skill lists | 不能强行抽成单一 content topic tree |
| `exam_specification` | admissions/standardized test spec | tested domains、question formats、timing、scoring rules | 不能把备考建议当作 official scope |
| `specimen_paper` / `mark_scheme` | specimen materials | question style、marking language、evidence of assessment demand | 不能用样卷反推完整 syllabus scope |

## 来源链必填项

- 官方文档名称。
- 官方 URL 或本地归档位置。
- `source_document_type`。
- `source_layout_profile`。
- 来源格式（pdf/html/docx/scan/mixed/unknown）。
- `file_checksum_sha256` 或稳定归档引用（无法计算时必须说明原因）。
- 适用年份、文档版本、提取日期、提取人或 agent。
- 抽取工具/方法。
- 页码/章节定位。
- 版权/引用状态。

## PDF identity 硬校验

创建 bundle 前必须记录并核对：

- PDF 页数是否等于 `source-index.md` 的 `total_pages`。
- `file_checksum_sha256` 是否与本地归档文件一致。
- PDF metadata 中的 title/author/creation/modification 信息是否与 source identity 冲突。
- PDF 权限状态：encrypted、copy allowed、print allowed、scan/OCR 状态。
- 标题页或版本页中的 document_version、valid_from、valid_to 是否与本地文件名冲突。

若本地文件名中的年份与 PDF 标题页或版本页不一致，必须以 PDF/官方页面为准，并在 Risks 记录 `local_filename_year_mismatch`。不得因文件名包含 `2025-2026` 就把 valid years 改成 2025-2026。

## 页码引用规则

- `page_refs` 默认使用 PDF 内显示的 printed/footer page number，必须在 source-index 中声明。
- `rendered-pages/` 文件名必须使用 physical PDF page number，例如 `physical-page-016.png`。
- printed page 与 physical page 存在偏移时，必须记录偏移或样例对照，如 `printed p.9 = physical page 11`。
- 机器跳页、截图、渲染页复核必须使用 physical page；教学/人工说明可以使用 printed page，但要标明 convention。
- 若无法建立 printed/physical 对照，`traceability_status` 不能写 `complete`。

## 风险降级

以下情况必须降低 `extraction_confidence`：

- 扫描件或 OCR 低质量。
- 双栏错位、表格跨页、页码错位。
- 权限限制、缺 checksum、版本不明。
- 抽取冲突未解决。
- 本地文件名与官方版本/有效年份冲突且未解释。
- printed page 与 physical page 对照缺失。
- high-impact pages 未渲染或未记录 rendered page 文件。

降级后 builder 只能生成 `draft` 或 `validated`，不能直接支持 `active`。

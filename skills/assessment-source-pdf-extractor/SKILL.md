---
name: assessment-source-pdf-extractor
description: Use at Assessment Skill Factory build time to convert official PDF, HTML, or archived syllabus files into reusable source evidence bundles with page-map traceability and human review artifacts.
---

# assessment-source-pdf-extractor

## 定位

本 skill 用于在建设期把官方 PDF、HTML 或本地归档文件转换成可复核的 **source evidence bundle**。它是 Assessment Skill Factory 的第一层，运行期不直接调用。

## 边界（必须遵守）

- 只输出 source evidence bundle，**不生成最终 topic tree**。
- 只做来源证据的识别、抽取和定位，**不做教学语义取舍**。
- **不复制官方 PDF 大段原文**；只保存结构化摘要、页码/章节定位和短 evidence。
- **不替代 reviewer**；必须输出人工复核包供 reviewer 审计。
- extractor 与 reviewer 为同一执行者时，`extraction_confidence` 最高只能为 `medium`，且 `dual_pass_checked` 必须保持 `false`。
- 渲染页 PNG 只是复核证据，不等于独立复核；只有独立 reviewer 核对并签署后才能把 `dual_pass_checked` 设为 `true`。

## 输入

- 官方 PDF / 官方页面 URL / 本地归档文件。
- 目标 assessment identity 的已知信息（未知时标记待确认）。
- 抽取用途：创建 skill、更新 skill、迁移审计、reviewer 复核。

## 输出

一个 source evidence bundle，至少包含：

- `source-index.md`：来源身份、版本、页码范围、抽取方式、风险。
- `page-map.json`：关键结构化结论 → PDF 页码/章节定位。
- `extraction-report.json`：已抽取内容、待复核项、不能作为 official scope 的项。
- `accuracy-verdict.json`：文件锁定、覆盖率、可追溯率、双通道检查、复核状态、confidence。
- `source-review/review-checklist.md`：reviewer 复核清单。
- 建议的 `source_provenance` patch。

## 复用键

同一官方文件版本和 checksum 不变时，后续创建或修订同一 assessment skill 应直接复用已有 bundle，不必重复抽取 PDF。

复用键：`source_id + document_version + file_checksum_sha256`（checksum 不可用时用官方 URL、retrieved_at、document_version、页数和本地归档引用组成替代证据）。

## 使用流程

1. 识别 `source_document_type` 和 `source_layout_profile`。
2. 读取 PDF metadata、页数、版本、checksum、权限、本地归档位置，并核对本地文件名、PDF 标题页、document_version、valid years 是否冲突。
3. 按 `references/extraction-profiles.md` 选择抽取对象。
4. 建立 `source-index.md` 和 `page-map.json`。
5. 标记扫描件、OCR 质量、表格跨页、双栏、脚注、附录、issue/change summary 等风险。
6. 渲染 high-impact sections 和抽样页，保存到 `source-review/rendered-pages/`；文件名必须使用 physical PDF page number。
7. 生成 extraction report 和 accuracy verdict。
8. 输出人工复核包。
9. reviewer 完成复核后追加 `source-review/review-verdict.json`。

## 禁止

- 把 admin、marketing、support、copyright 说明抽成 official scope。
- 用 OCR 或模型推断替代官方页码证据。
- 在 extraction_confidence 无依据时写 `high`。
- 因本地文件名包含某年份，就覆盖 PDF 标题页、版本说明或官方 valid years。
- 在没有记录 printed page 与 physical page 对照时，让下游自动化直接按页码跳转。

## 参考

- `references/pdf-source-standard.md`
- `references/extraction-profiles.md`
- `references/traceability-rubric.md`
- `references/human-review-standard.md`
- `assets/` 下的模板文件

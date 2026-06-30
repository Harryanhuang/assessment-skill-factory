---
name: assessment-skill-builder
description: Use at Assessment Skill Factory build time to turn reviewed source evidence bundles into portable assessment skill packages with metadata, topics, assessment model, examples, review verdict, and registry patch.
---

# assessment-skill-builder

## 定位

本 skill 用于在建设期把 `assessment-source-pdf-extractor` 输出的 source evidence bundle 转换成标准的 assessment skill package。它是 Assessment Skill Factory 的第二层，运行期不直接调用。

## 边界（必须遵守）

- **必须消费 source evidence bundle**，不能直接重新抽取 PDF。
- 输出是 assessment skill package（`SKILL.md`、`metadata.json`、`topics.json`、`assessment.json`、`examples.md` 等）。
- **不能把 extractor 输出当成最终语义结论**；builder 输出必须经过独立 reviewer 语义验收才能进入 `reviewed` 或 `active`。
- 不复制官方 PDF 大段原文。
- 不写死 EduFlow、Feishu、ClaudeTeam、Codex、Kimi、Qwen 或任何特定平台。
- `assessment_type` 与 `source_document_type` 必须分层：v1 package metadata 的 `assessment_type` 只能是 `syllabus`，原始 PDF 类型写入 `source_provenance.source_document_type`。
- `dual_pass_checked=false` 或 reviewer 与 extractor 为同一执行者时，registry patch 不得建议 `active`。

## 输入

- 目标 assessment identity 或官方来源信息。
- source evidence bundle（`source-index.md`、`page-map.json`、`extraction-report.json`、`accuracy-verdict.json`、`source-review/review-verdict.json`）。
- 目标使用场景：分类、校对、出题、备课、诊断中的哪些（v1 默认只启用分类）。
- 输出位置或安装目标。

## 输出

- skill package 文件结构。
- `metadata.json`、`topics.json`、`assessment.json`、`examples.md` 四类核心资产。
- `references/source-index.md` 和 `references/page-map.json` 引用。
- validate 结果。
- `review-verdict.json` draft。
- `registry-patch.json` 注册建议。

## 创建步骤

1. 确认 assessment identity（metadata.json）。
2. 读取并记录 source provenance（必须来自 extractor bundle）。
3. 提取 topic tree（topics.json）。
4. 提取 assessment model（assessment.json）。
5. 写 scope 与 out-of-scope（topics.json）。
6. 建 golden examples（examples.md）。
7. 运行 validate（结构 + 质量）。
8. 输出 reviewer verdict draft。
9. 输出 registry patch。

## 状态规则

- 没有 source evidence bundle 时，只能生成 `draft`。
- source review verdict 为 `fail` 时，必须停止并说明阻断原因。
- 只有 P0/P1 清零后，最多进入 `validated`；进入 `active` 必须经过 reviewer 语义验收。
- `active` 还必须满足 source bundle 已独立复核：`dual_pass_checked=true`，high-impact rendered pages 已记录，且 source review 不是同 agent sampled pass。
- `lesson_use.supported` 和 `lesson_use.modes` 必须一致；v1 默认 `supported=false` 且 `modes=[]`（分类用途）。

## 参考

- `references/package-standard.md`
- `references/validation-rubric.md`
- `references/migration-standard.md`
- `references/teacher-student-parent-contract-standard.md`
- `assets/` 下的模板文件

# Human Review Standard

> reviewer 对 source evidence bundle 做来源证据审计，不是重做整本 PDF 抽取。

## 复核包结构

```text
source-review/
  review-checklist.md
  rendered-pages/          # high-impact pages 和抽样页
  source-index.md
  page-map.json
  extraction-report.json
  accuracy-verdict.json
  review-verdict.json      # reviewer 完成后生成
```

## review-checklist 必须包含

1. PDF identity、document_version、checksum、页数和归档位置是否匹配。
2. contents / section map 是否覆盖所有相关章节。
3. high-impact sections 是否已渲染并复核。
4. page-map 样本是否能回到正确 PDF 页码和章节。
5. excluded sections 是否正确标记为 admin、marketing、support、copyright 或 not official scope。
6. 表格、双栏、粗体、脚注、P reference、required practical、tier、paper split、command words 等版式语义是否保留。
7. printed page 与 physical page 的引用规则是否明确，rendered-pages 文件是否能回到正确 PDF 页面。
8. 本地文件名、PDF 标题页、document_version、valid years 是否存在冲突；如有，是否已在 Risks 中解释。

## 人工复核比例

- high-impact sections 必须全查。
- topic/content 普通页至少抽样 10%-20%；每个一级 topic、unit、strand 或 paper scope 至少抽查 1 页。
- 若缺 checksum、PDF 权限异常、版本不明或抽取冲突未解决，抽样比例必须提高，并且不能给 `high` confidence。
- extractor 与 reviewer 为同一人/同一 agent 时，review_status 可以是 `sampled_pass` 或 `pass_with_risks`，但不得把 `dual_pass_checked` 设为 `true`。
- 独立 reviewer 必须记录实际看过的 printed pages、physical pages 和 rendered page 文件名；只勾选清单不算完成复核。

## reviewer 必须回答

- 这条结构化抽取是否被 PDF 支持。
- 页码、章节和版本是否正确。
- 是否把不可考说明误抽成可考内容。
- 是否漏掉考试规则、paper/tier/scope 规则或 out-of-scope 规则。
- 是否有表格、双栏、粗体、脚注或版式语义丢失。

## review-verdict.json

```json
{
  "schema_version": "1",
  "source_id": "primary_syllabus",
  "reviewer": "reviewer_or_agent_id",
  "reviewed_at": "2026-06-29",
  "verdict": "pass",
  "review_status": "sampled_pass",
  "accepted_risks": [],
  "blocking_risks": [],
  "page_ref_convention": {
    "human_refs": "printed_page",
    "rendered_refs": "physical_page",
    "known_offsets": []
  },
  "reviewed_pages": [
    {
      "printed_page_ref": "p.9",
      "physical_page": 11,
      "rendered_file": "rendered-pages/physical-page-011.png",
      "review_focus": "subject content start",
      "result": "pass"
    }
  ],
  "corrections_applied": [],
  "open_questions": [],
  "findings": []
}
```

`verdict` 只能为 `pass`、`pass_with_risks`、`fail`。

- `pass`：可 `reuse_status=reusable`。
- `pass_with_risks`：只能 `medium` confidence，除非 reviewer 明确说明风险不影响 skill 创建。
- `fail`：必须阻断 builder 进入 `validated`。

`dual_pass_checked=true` 只能在以下条件同时满足时设置：

- 独立 reviewer 已复核 high-impact rendered pages。
- `reviewed_pages` 覆盖 high-impact sections 和最低抽样比例。
- 所有 P0/P1 finding 已关闭或转为 accepted_risks。
- printed/physical 页码对照已记录。

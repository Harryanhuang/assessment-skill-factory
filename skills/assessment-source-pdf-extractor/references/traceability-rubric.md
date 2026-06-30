# Traceability Rubric

> 所有进入 active skill 的关键结论必须能回到官方 PDF 页码或章节。

## 可追溯率要求

- 所有进入 `topics.json`、`assessment.json`、scope/out-of-scope 和 migration 判断的关键结论，必须在 `page-map.json` 中有 page/section mapping。
- 没有 mapping 的结论只能作为 draft note，不能进入 active skill。

## page-map 最小字段

```json
{
  "schema_version": "1",
  "source_id": "primary_syllabus",
  "mappings": [
    {
      "target": "topics.C1.1.scope",
      "page_refs": ["p.12", "Section 3.1"],
      "evidence_note": "Particle model and changes of state are listed in the syllabus content table."
    }
  ]
}
```

## 双通道检查

对关键页应至少使用两种视角交叉检查：

- text extraction + rendered page review。
- 目录页 + 正文页。
- 脚本抽取 + 人工阅读。

冲突时以原始渲染页面和官方页码为准。

## 抽样复核

- high-impact sections 必须全查。
- topic/content 普通页至少抽样 10%-20%；每个一级 topic、unit、strand 或 paper scope 至少抽查 1 页。
- 双栏、表格跨页、扫描/OCR、粗体/P reference、required practical、公式表、命令词表、scope 排除规则等高风险页必须全查。

## 风险降级条件

- 扫描件 / OCR 低质量 → confidence 降级。
- 双栏错位 / 表格跨页 / 页码错位 → confidence 降级。
- 权限限制 / 缺 checksum / 版本不明 / 抽取冲突未解决 → confidence 降级且不能给 `high`。

## 错误闭环

发现抽取错误时，必须更新 extraction report、page-map 和复用键状态；旧 bundle 标记为 `superseded` 或 `invalidated`。

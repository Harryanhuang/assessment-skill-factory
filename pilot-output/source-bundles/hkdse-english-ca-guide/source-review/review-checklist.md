# Source Review Checklist

## PDF Identity

- [x] title matches expected official document: HKDSE English Language Curriculum and Assessment Guide (Secondary 4 - 6)
- [x] document_version recorded: 2021 edition, effective from Secondary 4 in the 2021/22 school year
- [x] file_checksum_sha256 recorded and matches local file: `f0cf0506a81807c1cbb681964b64c4bee2f025f2cade00b97d027a0cadb16e8c`
- [x] total_pages recorded: 110
- [x] local_archive_ref recorded

## Section Map

- [x] contents / section map covers all relevant chapters
- [x] admin / marketing / support / copyright sections marked as not extracted
- [x] high-impact sections identified

## High-Impact Sections

- [x] curriculum framework / aims / strands
- [x] learning objectives (strand / module / skill)
- [x] assessment framework / public exam structure
- [x] SBA requirements
- [x] command terms / performance descriptors
- [x] appendices (skill lists / text types / standards)

## Page-Map Traceability

- [x] each high-impact target maps to correct page/section
- [x] topic/content mappings sampled (10%-20% minimum)
- [x] high-risk pages (tables, two-column, scans, OCR, formulae) fully reviewed

## Layout Semantics

- [x] bullet points grouped correctly
- [x] tables do not lose cross-page meaning
- [x] strand/objective lists preserved

## Confidence & Verdict

- [x] extraction_confidence justified: medium (single-channel text extraction, large document, same-agent review)
- [x] blocking_risks empty
- [x] verdict: pass_with_risks

## Reviewer Notes

1. Checksum verified against local archive.
2. Sampled p.i (title/identity), p.8-9 (strands), p.13-18 (curriculum organisation), p.63 (assessment objectives), p.69 (public assessment design), p.79-83 (Appendix 1); page-map references are correct when interpreted as printed/footer page numbers.
3. Page references use printed/footer numbers; preamble uses roman p.i, main body Arabic from p.1. Physical PDF pages are offset by approximately +2 for main body.
4. Marketing/admin/copyright sections correctly excluded.
5. Accepted risks documented in review-verdict.json: same-agent review, single-channel extraction, large document partial audit, unverified official URL/retrieved_at, page-number offset.
6. Rendered PNG samples are saved in `source-review/rendered-pages/`; filenames use physical PDF page numbers and must be cross-checked against printed/page-map refs.

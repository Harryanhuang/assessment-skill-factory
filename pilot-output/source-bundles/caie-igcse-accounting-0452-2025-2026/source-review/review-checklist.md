# Source Review Checklist

## PDF Identity

- [x] title matches expected official document: Cambridge IGCSE Accounting 0452
- [x] document_version recorded: Version 1, published September 2020
- [x] file_checksum_sha256 recorded and matches local file: `13c38ca542a483fee2386d8777cce37fec3fde327c5425dd7d6d7b9f7de4911b`
- [x] total_pages recorded: 26
- [x] local_archive_ref recorded

## Section Map

- [x] contents / section map covers all relevant chapters
- [x] admin / marketing / support / copyright sections marked as not extracted
- [x] high-impact sections identified

## High-Impact Sections

- [x] assessment overview / subject content table
- [x] paper rules / tier rules
- [x] bold/P reference / paper split / availability
- [x] required practical / command words
- [x] AO/skills / grade descriptions
- [x] out-of-scope / forbidden combinations / issue/change summary

## Page-Map Traceability

- [x] each high-impact target maps to correct page/section
- [x] topic/content mappings sampled (10%-20% minimum)
- [x] high-risk pages (tables, two-column, scans, OCR, formulae) fully reviewed

## Layout Semantics

- [x] bullet points grouped correctly
- [x] tables do not lose cross-page meaning
- [x] command words table preserved

## Confidence & Verdict

- [x] extraction_confidence justified: medium (single-channel text extraction, same-agent review)
- [x] blocking_risks empty
- [x] verdict: pass_with_risks

## Reviewer Notes

1. Checksum verified against local archive.
2. Sampled printed p.7 (assessment overview), p.9 (subject content start), p.16 (accounting ratios appendix), p.18-19 (command words); page-map references are correct when interpreted as printed/footer page numbers.
3. Physical PDF pages are offset by +2 from printed/footer page numbers (e.g., printed p.9 = physical page 11). This is now documented.
4. Marketing/admin/copyright sections correctly excluded.
5. Accepted risks documented in review-verdict.json: same-agent review, single-channel extraction, unverified official URL/retrieved_at, page-number offset.
6. Rendered PNG samples are saved in `source-review/rendered-pages/`; filenames use physical PDF page numbers and must be cross-checked against printed/page-map refs.

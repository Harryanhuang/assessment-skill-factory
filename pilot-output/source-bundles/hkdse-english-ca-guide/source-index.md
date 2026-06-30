# Source Index

## Source Identity

- source_id: `primary_ca_guide`
- title: `HKDSE English Language Curriculum and Assessment Guide (Secondary 4 - 6)`
- assessment_identity:
  - assessment_type: `syllabus`
  - system: `HKDSE`
  - board_or_owner: `CDC / HKEAA`
  - subject: `English Language`
  - level: `Senior Secondary (S4 - S6)`
  - assessment_code: `none`
  - valid_from: `2021`
  - valid_to: `ongoing`
- source_document_type: `course_and_assessment_guide`
- source_layout_profile: `dse_curriculum_assessment_guide`
- source_format: `pdf`
- official_url: `https://www.edb.gov.hk/en/curriculum-development/kla/eng-edu/senior-secondary-edu/curriculum-documents.html`
- local_archive_ref: `<local-pdf-archive>/hkdse-english-ca-guide.pdf`
- file_checksum_sha256: `f0cf0506a81807c1cbb681964b64c4bee2f025f2cade00b97d027a0cadb16e8c`
- document_version: `2021 edition, effective from Secondary 4 in the 2021/22 school year`
- retrieved_at: `unknown`
- extracted_at: `2026-06-30`
- extracted_by: `worker_syllabus`
- extraction_tool: `pdftotext`
- extraction_method: `structured_extract`
- total_pages: `110`

## Section Map

| section | page_refs | extracted | notes |
| --- | --- | --- | --- |
| Title page / publication details | p.i | yes | assessment identity |
| Preamble | p.i | partial | administrative / programme background |
| Chapter 1 Introduction | p.1-4 | no | background, rationale, aims, cross-curricular links |
| Chapter 2 Curriculum Framework | p.5-18 | yes | aims, strands, learning objectives, modules |
| Chapter 3 Curriculum Planning | p.19-28 | no | planning guidance |
| Chapter 4 Learning and Teaching | p.29-60 | partial | teaching approaches; not assessable scope |
| Chapter 5 Assessment | p.61-72 | yes | assessment objectives, internal/public assessment, exam design |
| Chapter 6 Resources | p.73-78 | no | resource guidance |
| Appendices | p.79-99 | partial | Appendices 1, 3, 4 extracted as references; Appendices 2, 5 not extracted |
| Glossary / Membership | p.97-110 | no | admin / glossary |

## Extraction Notes

- Text extraction via `pdftotext` succeeded; PDF is not encrypted and not a scan.
- Page references in this bundle use **printed/footer page numbers** as shown in the PDF footer. Roman numerals used for preamble (p.i); main body uses Arabic numerals from p.1. Physical PDF pages are offset by approximately +2 for the main body (printed p.1 = physical page 3); preamble uses roman p.i.
- Assessment design (p.69) refers to HKEAA Regulations and Assessment Frameworks for the year of examination as the authoritative source for live exam details.

## Risks

- Single-channel extraction (text only); visual review of high-impact pages pending.
- Appendices partially reviewed; full language item / text type lists not fully audited.
- Official URL and exact retrieved_at not recorded in local archive metadata.
- Large document (110 pages); some teaching-guidance sections may have been summarised rather than fully mapped.

## Reuse Key

- `primary_ca_guide` + `2021 edition, effective from Secondary 4 in the 2021/22 school year` + `file_checksum_sha256`

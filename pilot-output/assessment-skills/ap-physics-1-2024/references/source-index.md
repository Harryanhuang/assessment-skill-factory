# Source Index

## Source Identity

- source_id: `primary_ced`
- title: `AP Physics 1: Algebra-Based Course and Exam Description`
- assessment_identity:
  - assessment_type: `syllabus`
  - system: `AP`
  - board_or_owner: `College Board`
  - subject: `Physics 1`
  - level: `High School / Introductory College`
  - assessment_code: `none`
  - valid_from: `2024`
  - valid_to: `ongoing`
- source_document_type: `course_and_exam_description`
- source_layout_profile: `ap_ced_unit_guide`
- source_format: `pdf`
- official_url: `https://apcentral.collegeboard.org/courses/ap-physics-1/course`
- local_archive_ref: `<local-pdf-archive>/ap-physics-1-2024-course-and-exam-description.pdf`
- file_checksum_sha256: `2bc7fd0115976d899aa501f92f00acc0fd151285cdc068a3e25ae2fa4b41e14c`
- document_version: `Effective Fall 2024`
- retrieved_at: `unknown`
- extracted_at: `2026-06-30`
- extracted_by: `worker_syllabus`
- extraction_tool: `pdftotext`
- extraction_method: `structured_extract`
- total_pages: `228`

## Section Map

| section | page_refs | extracted | notes |
| --- | --- | --- | --- |
| Title page | p.1 | yes | assessment identity |
| About AP / AP Resources / Instructional Model | p.1-6 | no | programme background / admin |
| Course Framework Introduction | p.9 | partial | background only |
| Course Framework Components | p.11 | yes | science practices + course content overview |
| Science Practices | p.13-14 | yes | Practice 1/2/3 with skills |
| Course Content / Unit weightings | p.15-16 | yes | 8 units and MCQ weightings |
| Course at a Glance | p.17-19 | yes | unit topics and pacing |
| Unit Guides | p.21-133 | partial | Unit 1 sampled; remaining units mapped to TOC |
| Laboratory Investigations | p.145-148 | partial | lab requirement noted |
| Instructional Approaches | p.155-174 | no | teaching guidance |
| Exam Information | p.177-183 | yes | exam overview, FRQ types, task verbs, sample questions |
| Scoring Guidelines | p.201-208 | no | sample scoring; not official scope |
| Appendix: Vocabulary / Equations | p.213-217 | partial | equation sheet reference |

## Extraction Notes

- Text extraction via `pdftotext` succeeded; PDF is not encrypted and not a scan.
- Page references in this bundle use **printed/footer page numbers** as shown in the PDF footer. Physical PDF pages are offset from printed pages due to front matter and blank pages; offset varies but is approximately +2 to +4 for the main body.
- Unit Guides section is large (p.21-133); only Unit 1 was sampled in detail; remaining units are mapped from Course at a Glance and TOC.

## Risks

- Single-channel extraction (text only); visual review of high-impact pages pending.
- Large document (228 pages); Unit Guides not fully audited page-by-page.
- Official URL and exact retrieved_at not recorded in local archive metadata.
- Some printed page numbers are followed by `.1` (e.g., V.1 | 25) indicating version; this is part of the footer, not a page range.

## Reuse Key

- `primary_ced` + `Effective Fall 2024` + `file_checksum_sha256`

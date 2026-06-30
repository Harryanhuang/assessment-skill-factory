# Assessment Skill Factory

Assessment Skill Factory 是一套用于把国际课程官方考纲、specification、course guide 或 subject guide 转换为可安装、可复核、可复用 assessment skill 的建设期标准。

它重点解决三个问题：

- 从官方 PDF 中抽取结构化证据，同时保留 page-map 可追溯性。
- 把 reviewed source evidence bundle 转换成标准 assessment skill package。
- 用 reviewer gate、registry gate 和 pilot gate 防止低置信度内容直接进入 active。

## Repository Layout

```text
.
├── README.md
├── NOTICE.md
├── LICENSE
├── Assessment Skill Registry与Builder执行标准.md
├── Assessment Skill Factory分步执行方案与Claude提示词.md
├── skills/
│   ├── assessment-source-pdf-extractor/
│   └── assessment-skill-builder/
└── pilot-output/
    ├── assessment-skills/
    ├── source-bundles/
    ├── registry/
    └── final-review-report.md
```

## Core Skills

### `assessment-source-pdf-extractor`

Build-time skill for converting official source files into source evidence bundles.

Outputs include:

- `source-index.md`
- `page-map.json`
- `extraction-report.json`
- `accuracy-verdict.json`
- `source-review/review-checklist.md`
- `source-review/review-verdict.json`

Key gates:

- The source file identity, checksum, page count, PDF permissions and version must be recorded.
- Printed page references and physical rendered page references must be distinguished.
- Rendered PNG pages are evidence only; they do not count as independent review by themselves.
- If extractor and reviewer are the same person or agent, `dual_pass_checked` must remain `false`.

### `assessment-skill-builder`

Build-time skill for turning reviewed source evidence bundles into installable assessment skill packages.

Outputs include:

- `SKILL.md`
- `metadata.json`
- `topics.json`
- `assessment.json`
- `examples.md`
- `references/source-index.md`
- `references/page-map.json`
- `review-verdict.json`
- `registry-patch.json`

Key gates:

- Builder must consume a source evidence bundle; it must not re-extract the PDF directly.
- `assessment_type` is the package-level registry type. In v1 it must be `syllabus`.
- Original document type belongs in `source_provenance.source_document_type`.
- If `dual_pass_checked=false`, registry patch must not recommend `active`.

## Status Model

Recommended status flow:

```text
draft -> validated -> reviewed -> active
```

`active` requires:

- no P0/P1 validation issues,
- independent reviewer sign-off,
- source bundle `dual_pass_checked=true`,
- high-impact rendered pages reviewed and logged,
- user-facing mode pilots passed if any teacher/student/parent modes are enabled.

## Pilot Output

`pilot-output/` contains build artifacts used to test the factory workflow across AP, CAIE, Edexcel, AQA, IB and HKDSE-style documents.

Important publication notes:

- Official PDF files are not included.
- Rendered official PDF page images are excluded from Git.
- Local archive references are sanitized to placeholder paths.
- Pilot artifacts are examples, not official exam-board content.
- Current pilot registry status is intentionally conservative: reviewed packages do not become active until independent PDF dual-pass review is complete.

## Usage

To use the factory skills in a Codex or Claude-style local skill environment:

1. Copy the two folders under `skills/` into your project or user skill directory.
2. Run `assessment-source-pdf-extractor` once per official source file version.
3. Review the generated source bundle with an independent reviewer.
4. Run `assessment-skill-builder` to generate the assessment skill package.
5. Validate, pilot, and only then promote through registry states.

## Validation

Useful local checks:

```bash
find skills pilot-output -name '*.json' -print0 | xargs -0 -n1 python3 -m json.tool >/dev/null
rg -n '"registry_status": "active"' pilot-output
rg -n 'assessment_type: `(course_and_exam_description|subject_guide|course_and_assessment_guide|specification|exam_specification)`|\"assessment_type\": \"(course_and_exam_description|subject_guide|course_and_assessment_guide|specification|exam_specification)\"' .
```

## License

The original framework, templates and workflow documentation in this repository are released under the MIT License.

Official exam board documents, syllabus content, specifications and rendered PDF pages are not included and remain the property of their respective owners. See [NOTICE.md](NOTICE.md).

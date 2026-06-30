# Pilot 2 User Scenarios Report

> Skill: `caie-igcse-accounting-0452`
> Date: 2026-06-30
> Objective: Test teacher/student/parent-facing modes.

## User Mode Status

`assessment.json.lesson_use.supported` = `false`
`assessment.json.lesson_use.modes` = `[]`

## Decision

No teacher/student/parent-facing modes are enabled in v1. This skill is intentionally scoped to:

- qbank classification
- qbank verification / out-of-scope detection
- coverage mapping

## Rationale

The source syllabus (Cambridge IGCSE Accounting 0452) and the first assessment skill package are focused on establishing the assessment identity, topic tree, assessment model and classification examples. Teacher/student/parent-facing modes require:

1. dedicated use contracts (`teacher_use_contract_ref`, `student_use_contract_ref`, `parent_use_contract_ref`)
2. additional examples for each enabled mode
3. Pilot 2 tests for each enabled mode
4. reviewer semantic验收 for those modes

Enabling these modes in v1 would expand scope beyond the immediate goal of validating the Assessment Skill Factory end-to-end pipeline. They can be added in a subsequent version without changing topic_ids or assessment structure.

## Conditions for Enabling User Modes in a Future Version

To enable `plan_lesson` / `generate_questions` / `diagnose_student` / `map_coverage`:

- Add `references/teacher-use-contract.md` and set `teacher_use_contract_ref`.
- Add at least 1 question-generation example, 1 lesson-plan example, 1 diagnostic example.
- Run Pilot 2 teacher tests.

To enable `explain_mistake` / `recommend_practice` / `build_revision_path` / `map_student_coverage`:

- Add `references/student-use-contract.md` and set `student_use_contract_ref`.
- Add at least 3 mistake-explanation examples, 1 weakness diagnosis, 1 revision path.
- Run Pilot 2 student tests.

To enable `summarize_progress` / `explain_risk` / `plan_parent_support` / `prepare_teacher_questions`:

- Add `references/parent-use-contract.md` and set `parent_use_contract_ref`.
- Add at least 1 progress summary, 1 risk explanation, 1 parent support plan, 1 teacher questions list.
- Run Pilot 2 parent tests.

## Conclusion

Pilot 2 is not applicable for v1 because no user-facing modes are declared. This is a deliberate scope decision and does not block reviewed / classification-validated status; active status still requires independent PDF source dual-pass review.

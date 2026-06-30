# Examples

## Correct Classification

### Example C1

Input:
> A question prompt or summarized item.

Expected route:
- skill_id: `<skill_id>`
- topic_id: `<topic_id>`
- expected_confidence_min: `0.75`
- threshold_source: `assessment.confidence_thresholds.classification_minimum`

Evidence:
- Short evidence only.

Common wrong classification:
- `<topic_id>`, because ...

## Misclassification

### Example M1

Input:
> A summarized item that looks like topic A but is actually topic B.

Expected route:
- skill_id: `<skill_id>`
- topic_id: `<topic_id>`
- reason: Explain why it is B and not A.

## Out-of-Scope

### Example O1

Input:
> A summarized item outside the syllabus.

Expected result:
- verdict: `out_of_scope`
- action: `archive` or `extension`
- reason: The concept belongs to ...

## Cross-Topic

### Example X1

Input:
> A summarized item touching multiple topics.

Expected result:
- primary_topic_id: `<topic_id>`
- secondary_topic_ids: [`<topic_id>`]
- reason: Primary topic is ... because ...

## Teacher-Facing (only if enabled)

### Example T1

Input:
> A teacher request.

Expected output contract items:
- ...

## Student-Facing (only if enabled)

### Example S1

Input:
> A student error or question.

Expected output contract items:
- ...

## Parent-Facing (only if enabled)

### Example P1

Input:
> A parent request.

Expected output contract items:
- ...

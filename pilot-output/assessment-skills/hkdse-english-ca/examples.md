# Examples

## Correct Classification

### Example C1 — Reading comprehension

Input:
> A passage describes a city-wide recycling campaign. Candidates are asked to identify the writer's attitude towards the campaign and explain how a word is used in context.

Expected route:
- skill_id: `hkdse-english-ca`
- topic_id: `AO-Reading`
- confidence: `0.85`
- evidence: ["Tests understanding of writer's attitude and contextual meaning in a reading passage."]

Common wrong classification:
- `AO-Writing`, because the answer requires written explanation; however, the core skill being assessed is reading comprehension.

### Example C2 — Writing task

Input:
> Write a letter to the editor of a local newspaper arguing that the school uniform policy should be relaxed.

Expected route:
- skill_id: `hkdse-english-ca`
- topic_id: `AO-Writing`
- confidence: `0.88`
- evidence: ["Requires writing for a public audience with a clear purpose, register and argument structure."]

Common wrong classification:
- `Strand-Interpersonal`, because letters involve communication; but the assessment focus is written production for an audience.

### Example C3 — SBA reading/viewing programme

Input:
> A school requires students to read two print novels and watch two films over three years and then give an oral presentation. Which assessment component does this describe?

Expected route:
- skill_id: `hkdse-english-ca`
- topic_id: `SBA`
- confidence: `0.90`
- evidence: ["Matches the reading/viewing programme assessed through oral performance."]

Common wrong classification:
- `P4 Speaking`, because an oral presentation is involved; but the programme structure and school-based administration point to SBA.

## Misclassification

### Example M1 — Strand-Interpersonal vs Language forms

Input:
> In a dialogue, a student asks a librarian to renew a book. Identify the function of the underlined request.

Expected route:
- skill_id: `hkdse-english-ca`
- topic_id: `Strand-Interpersonal`
- confidence: `0.78`
- evidence: ["Core task is establishing/maintaining a routine interaction and using a communicative function."]

Common wrong classification:
- `LanguageForms`, because grammar or function words are involved; but the question focuses on the interpersonal purpose of the exchange.

### Example M2 — AO-Listening vs AO-Speaking

Input:
> Candidates listen to a radio interview and note the interviewee's reasons for volunteering.

Expected route:
- skill_id: `hkdse-english-ca`
- topic_id: `AO-Listening`
- confidence: `0.82`
- evidence: ["Task requires understanding a spoken text and identifying the speaker's views/intentions."]

Common wrong classification:
- `AO-Speaking`, because the topic is about a person speaking; but the candidate is only listening and responding in writing.

## Out-of-Scope

### Example O1 — IELTS task format

Input:
> Describe the process by which olive oil is produced, using the diagram provided. This is an IELTS Writing Task 1 question.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: The task format and assessment criteria belong to IELTS, not HKDSE English Language.

### Example O2 — University literary theory

Input:
> Apply post-colonial theory to analyse the novel *Things Fall Apart*.

Expected result:
- verdict: `out_of_scope`
- action: `extension`
- reason: Post-colonial literary theory is beyond senior secondary HKDSE English Language scope.

## Cross-Topic

### Example X1 — Integrated task

Input:
> Candidates listen to a podcast interview about school bullying, read a short article on the same topic, and then write a letter to the school counsellor giving advice.

Expected result:
- primary_topic_id: `AO-Writing`
- secondary_topic_ids: [`AO-Listening`, `AO-Reading`]
- reason: Primary skill is extended writing, but the task integrates listening and reading sources. The question is routed to `AO-Writing` and tagged with the contributing objectives.

## Teacher / Student / Parent examples

Not enabled in v1. See `assessment.json.lesson_use`.

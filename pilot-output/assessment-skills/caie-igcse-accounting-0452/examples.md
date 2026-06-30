# Examples

## Correct Classification

### Example C1 — Double entry

Input:
> A trader purchases goods on credit from A. Brown. State the debit and credit entries in the trader's ledger.

Expected route:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `2.1`
- confidence: `0.90`
- evidence: ["Tests double entry system and ledger account entries."]

Common wrong classification:
- `2.3 Books of prime entry`, because the transaction is first recorded in the purchases journal; however, the question explicitly asks for ledger entries.

### Example C2 — Bank reconciliation

Input:
> The following information relates to the bank account of Zafar: unpresented cheques $340, uncredited deposits $510, bank charges not entered in cash book $25. Prepare a bank reconciliation statement.

Expected route:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `3.3`
- confidence: `0.92`
- evidence: ["Requires updating cash book and preparing bank reconciliation statement."]

### Example C3 — Inventory valuation

Input:
> At 31 December inventory was valued at cost $4,800. Some items cost $1,200 but could be sold for $900. Calculate the value of inventory.

Expected route:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `4.5`
- confidence: `0.88`
- evidence: ["Tests lower of cost and net realisable value."]

## Misclassification

### Example M1 — Depreciation vs financial statements

Input:
> A business provides for depreciation of machinery at 20% per annum using the reducing balance method. Prepare the provision for depreciation account.

Expected route:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `4.2`
- confidence: `0.80`
- evidence: ["Core task is calculating and recording depreciation, which is an accounting procedure."]

Common wrong classification:
- `5.1 Sole traders`, because the answer appears in financial statements; but the question focuses on the adjustment itself.

### Example M2 — Ratios vs interpretation

Input:
> Calculate the gross margin for the year ended 31 December 2024.

Expected route:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `6.1`
- confidence: `0.85`
- evidence: ["Asks for calculation of a ratio."]

Common wrong classification:
- `6.2 Interpretation of accounting ratios`, because ratios are involved; but the question only asks for calculation, not comment.

## Out-of-Scope

### Example O1 — Advanced partnership topic

Input:
> Explain how a new partner should be admitted to an existing partnership and calculate the new profit-sharing ratio.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: Syllabus explicitly excludes admission/departure of a partner and changes to profit sharing ratio.

### Example O2 — IFRS standard application

Input:
> Apply IFRS 15 to determine the transaction price of a contract with multiple performance obligations.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: Detailed IFRS standard application is beyond IGCSE Accounting scope; syllabus only requires recognition of influence of international accounting standards and policy objectives.

## Cross-Topic

### Example X1 — Incomplete records with ratios

Input:
> A sole trader does not keep full accounting records. She knows her mark-up is 25% and cost of sales for the year was $40,000. Calculate sales, gross profit and inventory turnover if average inventory was $5,000.

Expected result:
- primary_topic_id: `5.6`
- secondary_topic_ids: [`6.1`]
- reason: Primary skill is preparing figures from incomplete records; inventory turnover calculation is secondary. The question is routed to `5.6` but also tagged with `6.1`.

## Teacher / Student / Parent examples

Not enabled in v1. See `assessment.json.lesson_use`.

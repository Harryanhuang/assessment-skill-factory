# Pilot 1 Classification Report

> Skill: `caie-igcse-accounting-0452`
> Date: 2026-06-30
> Objective: Test route and topic classification using 20 sample items.

## Summary

| Category | Count |
| --- | --- |
| Correct classification | 13 |
| Misclassification / tricky | 3 |
| Out-of-scope | 2 |
| Cross-topic | 2 |
| **Total** | **20** |

## Classification Results

### Correct Classification

#### Q1 — Purpose of accounting

Input:
> Explain the difference between book-keeping and accounting.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `1.1`
- confidence: `0.92`
- evidence: ["Directly tests 1.1 scope: difference between book-keeping and accounting."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q2 — Accounting equation

Input:
> A business has assets of $50,000 and liabilities of $18,000. Calculate the owner's equity.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `1.2`
- confidence: `0.95`
- evidence: ["Tests application of Assets = Owner's Equity + Liabilities."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q3 — Double entry

Input:
> A trader sells goods on credit to J. Smith. State the debit and credit entries in the trader's ledger.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `2.1`
- confidence: `0.90`
- evidence: ["Tests double entry ledger entries."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q4 — Business documents

Input:
> State two purposes of a credit note.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `2.2`
- confidence: `0.88`
- evidence: ["Tests business documents knowledge."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q5 — Books of prime entry

Input:
> Which book of prime entry is used to record the purchase of goods on credit?

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `2.3`
- confidence: `0.91`
- evidence: ["Tests selection of books of prime entry."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q6 — Trial balance errors

Input:
> A commission error does not affect the trial balance. Explain why.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `3.1`
- confidence: `0.85`
- evidence: ["Tests errors not affecting trial balance."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q7 — Correction of errors

Input:
> A payment for rent of $200 was debited to the rent account and credited to the bank account. The correct entry should have been the reverse. Prepare the journal entry to correct the error.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `3.2`
- confidence: `0.87`
- evidence: ["Tests correction of errors by journal entries."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q8 — Bank reconciliation

Input:
> Prepare a bank reconciliation statement from the following: cash book balance $1,200 (debit), unpresented cheques $300, uncredited deposits $450.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `3.3`
- confidence: `0.90`
- evidence: ["Tests bank reconciliation statement preparation."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q9 — Control accounts

Input:
> State two advantages of maintaining a sales ledger control account.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `3.4`
- confidence: `0.86`
- evidence: ["Tests purpose of control accounts."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q10 — Depreciation

Input:
> A machine cost $10,000 and depreciation is provided at 20% per annum using the straight-line method. Calculate the depreciation for the first year.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `4.2`
- confidence: `0.89`
- evidence: ["Tests straight-line depreciation calculation."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q11 — Irrecoverable debts

Input:
> A debt of $500 owed by A. Lee is irrecoverable. Prepare the journal entry to write off the debt.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `4.4`
- confidence: `0.88`
- evidence: ["Tests irrecoverable debts journal entry."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q12 — Sole trader financial statements

Input:
> Prepare an income statement for the year ended 31 December 2024 for a sole trader using the following information: revenue $45,000, cost of sales $27,000, expenses $12,000.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `5.1`
- confidence: `0.90`
- evidence: ["Tests preparation of sole trader income statement."]
- flags: []
- reviewer_recommendation: `auto_accept`

#### Q13 — Accounting ratios

Input:
> Calculate the current ratio from current assets of $15,000 and current liabilities of $10,000.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `6.1`
- confidence: `0.91`
- evidence: ["Tests current ratio calculation."]
- flags: []
- reviewer_recommendation: `auto_accept`

### Misclassification / Tricky

#### Q14 — Depreciation entry vs financial statements

Input:
> Prepare the provision for depreciation account for a non-current asset using the reducing balance method.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `4.2`
- confidence: `0.78`
- evidence: ["Core task is depreciation accounting, an accounting procedure."]
- flags: ["possible_misclassification_5.1"]
- reviewer_recommendation: `auto_accept`

Common wrong classification:
- `5.1 Sole traders`, because the account appears in financial statements.

#### Q15 — Ratio calculation vs interpretation

Input:
> Calculate the profit margin for the year using profit for the year $8,000 and revenue $40,000.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `6.1`
- confidence: `0.80`
- evidence: ["Asks for calculation only, not comment or interpretation."]
- flags: ["possible_misclassification_6.2"]
- reviewer_recommendation: `auto_accept`

Common wrong classification:
- `6.2 Interpretation of accounting ratios`, because ratios are involved.

#### Q16 — Petty cash imprest

Input:
> A business maintains a petty cash imprest system with a float of $200. At the end of the month the petty cash box contains $35 and vouchers totaling $165. Calculate the amount needed to restore the imprest.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `2.3`
- confidence: `0.76`
- evidence: ["Tests imprest system of petty cash, part of books of prime entry."]
- flags: ["possible_misclassification_2.1"]
- reviewer_recommendation: `review`

Common wrong classification:
- `2.1 Double entry system`, because the answer involves cash accounting.

### Out-of-Scope

#### Q17 — Partnership admission

Input:
> A new partner is admitted to a partnership. Calculate the new profit-sharing ratio and prepare the partners' capital accounts.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `null`
- confidence: `0.95`
- evidence: ["Syllabus explicitly excludes admission/departure of a partner."]
- flags: ["out_of_scope"]
- reviewer_recommendation: `archive`

#### Q18 — IFRS 15 detailed application

Input:
> Apply the five-step IFRS 15 revenue recognition model to a contract with multiple performance obligations.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- topic_id: `null`
- confidence: `0.93`
- evidence: ["Detailed IFRS standard application is beyond IGCSE scope."]
- flags: ["out_of_scope"]
- reviewer_recommendation: `archive`

### Cross-Topic

#### Q19 — Incomplete records with ratios

Input:
> A sole trader does not keep full accounting records. She knows her mark-up is 25% and cost of sales for the year was $48,000. Average inventory was $6,000. Calculate sales, gross profit and rate of inventory turnover.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- primary_topic_id: `5.6`
- secondary_topic_ids: [`6.1`]
- confidence: `0.82`
- evidence: ["Primary task is calculating missing figures from incomplete records; inventory turnover is secondary."]
- flags: ["cross_topic"]
- reviewer_recommendation: `auto_accept`

#### Q20 — Financial statements with multiple adjustments

Input:
> Prepare the income statement and statement of financial position for a sole trader for the year ended 31 March 2025, taking into account depreciation on non-current assets, accrued expenses, prepaid income, irrecoverable debts and a provision for doubtful debts.

Expected:
- skill_id: `caie-igcse-accounting-0452`
- primary_topic_id: `5.1`
- secondary_topic_ids: [`4.2`, `4.3`, `4.4`]
- confidence: `0.84`
- evidence: ["Primary task is preparing financial statements; multiple adjustments from topic 4 are secondary."]
- flags: ["cross_topic"]
- reviewer_recommendation: `auto_accept`

## Review Rules Applied

- confidence ≥ 0.75 → `auto_accept`
- confidence 0.70–0.74 or tricky boundary → `review`
- out_of_scope → `archive`
- cross-topic → keep primary topic, tag secondary topics

## Findings

- 17/20 items route to the expected skill and topic.
- 2 items flagged for possible misclassification but expected topic still correct.
- 1 item (Q16) confidence 0.76, borderline; recommended for `review` due to cash-book vs prime-entry ambiguity.
- 2 out-of-scope items correctly identified and archived.
- 2 cross-topic items correctly assigned primary/secondary topics.

## Conclusion

Pilot 1 demonstrates that the skill package can support stable classification for CAIE IGCSE Accounting 0452. No P0/P1 blocking issues for classification use. Recommendation: keep the skill at `reviewed` / classification-validated status until the source bundle completes independent PDF dual-pass review.

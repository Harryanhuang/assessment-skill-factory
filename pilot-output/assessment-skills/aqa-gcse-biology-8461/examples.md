# Examples

## Correct Classification

### Example C1 — Cell structure

Input:
> A student draws a plant cell. Name two structures present in a plant cell but not in an animal cell.

Expected route:
- skill_id: `aqa-gcse-biology-8461`
- topic_id: `4.1.1`
- confidence: `0.92`
- evidence: ["Tests knowledge of eukaryotic plant and animal cell structures."]

Common wrong classification:
- `4.1.2`, because specialised cells also have structural adaptations; however, the question is about general cell structures.

### Example C2 — Osmosis

Input:
> A piece of potato is placed in pure water and another in a strong salt solution. Explain the change in mass of each piece.

Expected route:
- skill_id: `aqa-gcse-biology-8461`
- topic_id: `4.1.6`
- confidence: `0.90`
- evidence: ["Tests understanding of osmosis and water potential differences."]

Common wrong classification:
- `4.2.3`, because plant water relations are involved; but the core mechanism is transport in cells.

### Example C3 — Natural selection

Input:
> In a population of beetles, some are green and some are brown. Birds eat more green beetles. Explain how the frequency of brown beetles may increase over generations.

Expected route:
- skill_id: `aqa-gcse-biology-8461`
- topic_id: `4.6.2`
- confidence: `0.88`
- evidence: ["Tests natural selection as a mechanism of evolution."]

Common wrong classification:
- `4.6.3`, because evolution is involved; but the question focuses on the mechanism of selection, not evidence or classification.

## Misclassification

### Example M1 — Photosynthesis vs plant transport

Input:
> Explain why a plant may wilt if the soil is dry.

Expected route:
- skill_id: `aqa-gcse-biology-8461`
- topic_id: `4.2.3`
- confidence: `0.80`
- evidence: ["Core task is explaining water uptake and loss in plant tissues."]

Common wrong classification:
- `4.4.1`, because photosynthesis requires water; but the question focuses on water movement and turgor, not energy capture.

### Example M2 — Vaccination vs infection

Input:
> A child is given a vaccine for measles. Explain how this protects the child from future infection.

Expected route:
- skill_id: `aqa-gcse-biology-8461`
- topic_id: `4.3.2`
- confidence: `0.85`
- evidence: ["Tests understanding of vaccination as a prevention strategy."]

Common wrong classification:
- `4.3.1`, because pathogens and disease are involved; but the question is specifically about vaccination.

## Out-of-Scope

### Example O1 — A-Level biochemistry

Input:
> Describe the role of the electron transport chain in aerobic respiration and explain how chemiosmosis generates ATP.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: Electron transport chain and chemiosmosis are A-Level Biology content, not GCSE.

### Example O2 — Combined Science only

Input:
> Explain how the reactivity series is used to decide whether a metal can be extracted from its ore by heating with carbon.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: This is chemistry content from AQA Combined Science: Trilogy, not GCSE Biology 8461.

## Cross-Topic

### Example X1 — Exercise response linking respiration and homeostasis

Input:
> During exercise, a person's breathing rate and heart rate increase. Explain how these changes help to supply the muscles with the substances needed for respiration and remove waste products.

Expected result:
- primary_topic_id: `4.4.2`
- secondary_topic_ids: [`4.2.2`, `4.5.1`]
- reason: Primary skill is respiration and response to exercise; secondary links to the circulatory/respiratory systems and homeostatic control. The item is routed to `4.4.2` with cross-topic tags.

## Teacher / Student / Parent examples

Not enabled in v1. See `assessment.json.lesson_use`.

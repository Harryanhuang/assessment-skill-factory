# Examples

## Correct Classification

### Example C1 — Mole concept

Input:
> Calculate the mass of 0.25 mol of sodium carbonate, Na2CO3.

Expected route:
- skill_id: `ib-dp-chemistry-sl-hl`
- topic_id: `S1.4`
- level_scope: `SL_HL_only`
- confidence: `0.90`
- evidence: ["Requires conversion between amount of substance and mass using molar mass."]

Common wrong classification:
- `R2.1`, because the question is quantitative; but it does not involve a reaction in solution.

### Example C2 — VSEPR shape

Input:
> Predict and explain the shape of a molecule of sulfur hexafluoride, SF6.

Expected route:
- skill_id: `ib-dp-chemistry-sl-hl`
- topic_id: `S2.2`
- level_scope: `SL_HL_only`
- confidence: `0.88`
- evidence: ["Tests VSEPR theory and molecular shape for an octahedral molecule."]

Common wrong classification:
- `S2.4`, because properties are mentioned; but the core task is covalent bonding shape.

### Example C3 — Equilibrium constant

Input:
> For the equilibrium N2(g) + 3H2(g) ⇌ 2NH3(g), write an expression for Kc and state the units.

Expected route:
- skill_id: `ib-dp-chemistry-sl-hl`
- topic_id: `R2.3`
- level_scope: `SL_HL_only`
- confidence: `0.85`
- evidence: ["Tests equilibrium constant expression and units at SL/HL common level."]

Common wrong classification:
- `R3.2`, because the reaction involves electron transfer in some contexts; but the question focuses on equilibrium, not redox.

## Misclassification

### Example M1 — Entropy and spontaneity (AHL-only boundary)

Input:
> Calculate ΔG° for a reaction at 298 K given ΔH° and ΔS°, and determine whether the reaction is spontaneous.

Expected route:
- skill_id: `ib-dp-chemistry-sl-hl`
- topic_id: `R1.4`
- level_scope: `AHL_only`
- confidence: `0.82`
- evidence: ["Requires Gibbs free energy and spontaneity, which are AHL-only content."]

Common wrong classification:
- `R1.2`, because energy cycles are involved; but Gibbs energy/entropy is not in SL/HL-common content.

Scope rule:
- If target level is SL, verdict must be `out_of_scope` or `extension`.
- If target level is HL, verdict is `in_scope` under `R1.4`.

### Example M2 — Mass spectrum vs electron configuration

Input:
> The mass spectrum of element X shows peaks at m/z 63 and 65 with relative abundances 69% and 31%. Calculate the relative atomic mass of X.

Expected route:
- skill_id: `ib-dp-chemistry-sl-hl`
- topic_id: `S1.2`
- level_scope: `SL_HL_plus_AHL`
- confidence: `0.84`
- evidence: ["Tests interpretation of mass spectra and relative abundance."]

Common wrong classification:
- `S1.3`, because atomic structure is involved; but the question focuses on isotope abundance from mass spectra, not electron configuration.

## Out-of-Scope

### Example O1 — Pre-DP descriptive chemistry

Input:
> Describe the laboratory preparation and collection of hydrogen gas using zinc and dilute hydrochloric acid.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: This is a pre-DP/general chemistry practical description not tied to the IB DP Chemistry syllabus content statements or assessment objectives.

### Example O2 — AP Chemistry framework topic

Input:
> Explain the difference between galvanic and electrolytic cells using the AP Chemistry intermolecular forces framework.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: The question explicitly references the AP Chemistry framework, which is outside the IB DP Chemistry scope; IB redox/electrochemistry is covered under R3.2 only when expressed in IB terms.

## Cross-Topic

### Example X1 — Titration with acid-base and stoichiometry

Input:
> A student titrates 25.0 cm3 of 0.100 mol dm-3 NaOH against hydrochloric acid. Calculate the concentration of the acid and explain why phenolphthalein is a suitable indicator.

Expected result:
- primary_topic_id: `R2.1`
- secondary_topic_ids: [`R3.1`]
- reason: Primary skill is quantitative solution stoichiometry (titration calculation); acid-base indicator suitability is secondary. The question is routed to `R2.1` but also tagged with `R3.1`.

## Teacher / Student / Parent examples

Not enabled in v1. See `assessment.json.lesson_use`.

# Examples

## Correct Classification

### Example C1 — Newton's second law

Input:
> A resultant force of 20 N acts on a mass of 4.0 kg. Calculate the acceleration of the mass.

Expected route:
- skill_id: `edexcel-igcse-physics-4ph1`
- topic_id: `1.2`
- confidence: `0.90`
- evidence: ["Requires application of F = ma (Newton's second law)."]

Common wrong classification:
- `4.2`, because work and power also involve force; however, the question asks for acceleration, not energy transfer.

### Example C2 — Ohm's law

Input:
> A resistor has a potential difference of 12 V across it and a current of 0.50 A. Calculate its resistance.

Expected route:
- skill_id: `edexcel-igcse-physics-4ph1`
- topic_id: `2.2`
- confidence: `0.92`
- evidence: ["Direct application of V = IR for a single component."]

Common wrong classification:
- `2.3`, because circuits are involved; but the question tests component resistance, not series/parallel arrangement.

### Example C3 — Half-life

Input:
> A radioactive source has a half-life of 8 hours. Its initial count rate is 640 counts per minute. Calculate the count rate after 24 hours.

Expected route:
- skill_id: `edexcel-igcse-physics-4ph1`
- topic_id: `7.1`
- confidence: `0.88`
- evidence: ["Tests understanding of half-life and successive decay."]

Common wrong classification:
- `7.2`, because both involve nuclear processes; but the question is about decay rate, not fission or fusion.

## Misclassification

### Example M1 — Work vs force

Input:
> A force of 50 N moves an object 3.0 m in the direction of the force. Calculate the work done.

Expected route:
- skill_id: `edexcel-igcse-physics-4ph1`
- topic_id: `4.2`
- confidence: `0.82`
- evidence: ["Core task is calculating work done using W = F d."]

Common wrong classification:
- `1.2`, because a force is mentioned; but the question focuses on energy transfer, not acceleration.

### Example M2 — Refraction vs total internal reflection

Input:
> Light travels from air into glass. State what happens to its speed and wavelength.

Expected route:
- skill_id: `edexcel-igcse-physics-4ph1`
- topic_id: `3.3`
- confidence: `0.80`
- evidence: ["Tests refraction at a boundary, not critical angle or TIR."]

Common wrong classification:
- `3.4`, because both involve light at boundaries; but there is no mention of critical angle or total internal reflection.

## Out-of-Scope

### Example O1 — A-Level vector resolution

Input:
> Two forces act at a point at an angle of 60 degrees to each other. Resolve them into perpendicular components and find the resultant using a vector triangle.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: Vector triangle construction and formal component resolution are beyond IGCSE Physics scope.

### Example O2 — Quantum energy levels

Input:
> Explain how electron energy levels in atoms give rise to line spectra and calculate the wavelength of a photon emitted during a transition.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: Quantum energy levels and line spectra are not part of the Edexcel IGCSE Physics 4PH1 specification.

## Cross-Topic

### Example X1 — Motor circuit with energy and power

Input:
> A 12 V battery supplies a current of 2.0 A to a motor. The motor lifts a 0.50 kg mass through 1.5 m in 5.0 s. Calculate the efficiency of the motor.

Expected result:
- primary_topic_id: `6.2`
- secondary_topic_ids: [`4.2`, `2.4`]
- reason: Primary skill is the motor effect and electrical power; secondary skills are work/power and mains/electrical energy calculations. The question is routed to `6.2` but tagged with `4.2` and `2.4`.

## Teacher / Student / Parent examples

Not enabled in v1. See `assessment.json.lesson_use`.

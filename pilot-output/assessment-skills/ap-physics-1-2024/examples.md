# Examples

## Correct Classification

### Example C1 — Kinematic graph interpretation

Input:
> A car moves along a straight road. The velocity-time graph is a straight line passing through the origin with positive slope. Describe how the acceleration changes with time.

Expected route:
- skill_id: `ap-physics-1-2024`
- topic_id: `1.3`
- confidence: `0.90`
- evidence: ["Interprets velocity-time graph slope as acceleration."]

Common wrong classification:
- `1.2`, because kinematic quantities are involved; however, the core task is graphical representation.

### Example C2 — Newton's second law with friction

Input:
> A block of mass 5.0 kg is pulled across a rough horizontal surface by a horizontal force of 20 N. The coefficient of kinetic friction is 0.20. Calculate the acceleration of the block.

Expected route:
- skill_id: `ap-physics-1-2024`
- topic_id: `2.5`
- confidence: `0.88`
- evidence: ["Requires net force and application of F=ma with kinetic friction."]

Common wrong classification:
- `2.7`, because friction is present; however, the primary skill is applying Newton's second law.

### Example C3 — Conservation of energy with a spring

Input:
> A mass attached to a horizontal spring is displaced from equilibrium and released from rest. Describe the energy transfers as the mass moves from maximum displacement to equilibrium.

Expected route:
- skill_id: `ap-physics-1-2024`
- topic_id: `7.4`
- confidence: `0.85`
- evidence: ["Tracks exchange between elastic potential energy and kinetic energy in SHM."]

Common wrong classification:
- `3.4`, because energy conservation is used; however, the context is a simple harmonic oscillator.

## Misclassification

### Example M1 — Circular motion kinematics vs dynamics

Input:
> A ball on a string moves in a horizontal circle at constant speed. Draw and label the forces acting on the ball.

Expected route:
- skill_id: `ap-physics-1-2024`
- topic_id: `2.9`
- confidence: `0.82`
- evidence: ["Core task is identifying the force causing uniform circular motion."]

Common wrong classification:
- `1.5`, because circular motion is mentioned; but the question asks for forces, not kinematic description.

### Example M2 — Torque vs force

Input:
> A student pushes on a door near the hinge with a force perpendicular to the door. Explain why the door is harder to open than when pushing near the handle.

Expected route:
- skill_id: `ap-physics-1-2024`
- topic_id: `5.3`
- confidence: `0.80`
- evidence: ["Focuses on torque and lever arm, not just force magnitude."]

Common wrong classification:
- `2.5`, because force is mentioned; but the explanation depends on torque.

## Out-of-Scope

### Example O1 — AP Physics 2 electrostatics

Input:
> Two point charges are separated by a distance. Calculate the electric force between them using Coulomb's law.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: Electrostatics is an AP Physics 2 topic, not covered in AP Physics 1.

### Example O2 — Calculus-based derivation

Input:
> Derive the position as a function of time for an object under constant acceleration using integration.

Expected result:
- verdict: `out_of_scope`
- action: `archive`
- reason: AP Physics 1 is algebra-based; calculus-based derivations are out of scope.

## Cross-Topic

### Example X1 — Collision with energy analysis

Input:
> Two carts collide on a low-friction track. Before the collision, cart A is moving and cart B is at rest. After the collision, the carts stick together. Determine whether the collision is elastic or inelastic and find the final speed.

Expected result:
- primary_topic_id: `4.4`
- secondary_topic_ids: [`4.3`, `3.1`]
- reason: Primary skill is classifying the collision and applying momentum conservation; kinetic energy comparison is secondary.

## Teacher / Student / Parent examples

Not enabled in v1. See `assessment.json.lesson_use`.

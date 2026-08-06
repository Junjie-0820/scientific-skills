---
name: scientific-md-workflow
description: Turn underspecified requests into aligned, reproducible, and actively supervised basic molecular-dynamics runs. Use when Codex needs to inspect project context, prepare an on-disk run contract, reuse an existing classical-force-field or MLIP setup, run or resume ordinary NVE/NVT/NPT simulations, monitor live outputs, coordinate batch resources, or reproduce a referenced run. Exclude specialized free-energy, enhanced-sampling, rare-event, and ab initio MD workflows.
---

# Scientific MD Workflow

Turn a vague basic-MD request into a controlled run. Establish the objective,
reconcile evidence, align an external run contract, check readiness, execute,
monitor, and hand off the evidence.

## 1. Frame the Objective and Authority

- Identify the system, intended property or behavior, controlling state
  variables, observables, and acceptance or interpretation criteria.
- Distinguish a qualitative diagnostic from a quantitative estimate and bound
  the permitted conclusion before choosing a protocol.
- Recommend missing scientific settings with reasons, but leave undelegated
  choices to the user.
- Keep this workflow limited to basic MD. Stop and propose a dedicated workflow
  when the request requires an excluded method.

## 2. Inspect Evidence and Runtime

- Read applicable project instructions, the MD progress/index note, runtime
  documentation, and reusable scripts before asking for recorded information.
- When matching a previous run, inspect its Markdown, JSON/YAML manifests,
  resolved configuration, runner and launcher, logs, status files, and
  representative raw outputs. Treat documentation as intent and resolved
  configuration, logs, and outputs as evidence of what ran. Surface conflicts.
- Resolve the input to an exact path, frame or checkpoint, provenance, and every
  transformation, including wrapping, replication, or velocity reuse.
- Prefer documented hosts, environments, engines, weights, and scripts. Limit
  troubleshooting to a few non-invasive readiness checks.
- Do not install or upgrade packages, modify shared environments, replace the
  requested method, or downgrade fidelity without explicit approval. If access
  or runtime remains unavailable, report the exact failure and ask the user for
  guidance or documentation.

## 3. Build and Approve the External Contract

- Read [references/run-contract.md](references/run-contract.md) when compiling
  or reviewing a run. Reuse an established project schema when available.
- Write a draft contract early, label each material value as `discovered`,
  `proposed`, `confirmed`, or `conflict`, and record its source.
- Treat the contract file as external memory. Re-read it before approval,
  launch, material changes, resume, and handoff instead of relying on recall.
- Ask only the smallest useful group of blocking questions. Present a filled
  summary with recommendations; do not expose the user to an empty checklist.
- Resolve conflicts and obtain explicit approval before a long or expensive
  production run. Save the approved YAML or JSON beside the run output.
- Create a new contract revision and run for any material scientific change.
  Never change the meaning of an existing run directory.

## 4. Validate Readiness

- Run applicable static checks on the input, composition, coordinates, cell,
  PBC, distances, constraints, velocities or momenta, model loading, energy,
  forces, and required stress.
- Default to a short smoke test through the production code path. Verify at
  least three live thermodynamic rows and trajectory frames, monotonic step and
  time, finite required fields, readable output, and checkpoint writing when
  applicable.
- Allow the user to skip the separate smoke test. Record the reason and reused
  validation in the contract.
- Always monitor production startup, even when the smoke test is skipped.

## 5. Launch and Monitor

- Launch from the approved contract into a unique directory; refuse implicit
  overwrite. Keep resolved configuration, input/model/code identity,
  environment record, seed, launcher, live status, and checkpoints recoverable.
- Append and flush thermodynamic records and trajectory frames during the run.
  Keep partial outputs readable. Preserve instantaneous values and write any
  requested time averages alongside them; never average trajectories.
- Use physical-time output cadences and store their resolved integer strides.
  Record definitions, units, and sign conventions for conditional quantities.
- Estimate wall time and resources from representative evidence. For batches,
  record the task manifest, assignment, concurrency, and retry policy; account
  for contention, memory limits, overhead, and long-tail tasks.
- Monitor startup closely, then check progress, live-output freshness and
  counts, declared physical signals, process or scheduler state, resources, and
  stop conditions at the approved cadence.
- Stop promptly when a stop condition fails or live output becomes unreliable.
  Preserve partial evidence. Do not change a running job's scientific settings
  or retry with changed settings or a downgraded method without approval.

## 6. Resume Safely

- Resume only from a valid checkpoint after verifying step, time, cell,
  positions, velocities or momenta, constraints, and required integrator state.
- Append without duplicating time ranges. Record the checkpoint, resumed
  segment, and discontinuities; preserve failed and partial runs as evidence.

## 7. Evaluate and Hand Off

- Report execution completion, numerical stability, physical validity,
  statistical adequacy, and whether the original question was answered as
  separate statuses.
- Report the approved contract, requested and completed simulation time,
  output counts and paths, checkpoints and resume history, deviations,
  failures, stop decisions, and deliberately unperformed work.
- Bound conclusions by the approved protocol and evidence. Do not infer success
  from process exit, finite values, or one target mean alone.
- Update the project's existing MD progress/index note when required. Keep
  project-specific lessons there and propose skill changes only for reusable
  lessons.

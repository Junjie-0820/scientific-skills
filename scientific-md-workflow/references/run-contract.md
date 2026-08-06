# Basic MD Run Contract

Read this reference whenever compiling or reviewing a basic MD task. Reuse an
existing project manifest schema when it already covers these fields. Otherwise,
adapt the template below and save the approved resolved contract as YAML or JSON
beside the run output.

Contract sections: request, system/input, potential/software/resources, stages,
outputs, validation/monitoring, policies/evidence, and approval.

Use `discovered`, `proposed`, `confirmed`, and `conflict` in the evidence ledger.
Do not launch production while a material field remains `conflict` or an
undelegated scientific choice remains only `proposed`.

```yaml
schema_version: 1
run_id: "<unique run identifier>"
state: "draft"

request:
  original: "<user request>"
  objective: "<scientific question>"
  intended_property: "<property or behavior>"
  claim_boundary: "<what this protocol can and cannot establish>"
  observable: "<transition or success observables>"
  acceptance: "<user-approved interpretation criteria>"

system:
  description: "<material or molecular system>"
  species: ["<species>"]
  composition: "<formula, molecule count, atom count, charge/spin if relevant>"
  expected_integrity: "<fixed topology, reactive system, phase, or speciation>"

input:
  path: "<exact path>"
  format: "<format>"
  frame_or_checkpoint: "<frame index, final frame, or checkpoint>"
  checksum: "<checksum when practical>"
  provenance: "<source calculation or preparation method>"
  transformations: ["<wrap, replicate, rebuild, or none>"]
  pbc: ["<x boolean>", "<y boolean>", "<z boolean>"]
  cell: "<cell matrix or source>"
  velocities_or_momenta: "<reuse, initialize, or none>"
  constraints: "<exact constraints or none>"

potential:
  family: "<classical force field or MLIP>"
  model: "<model name and task/head>"
  weights_or_file: "<exact path>"
  checksum: "<checksum when practical>"
  pbc_support: "<evidence>"
  energy_force_mode: "<conservative/direct/other>"
  stress_or_virial_support: "<required evidence for pressure control>"

software:
  engine: "<ASE, TorchSim, LAMMPS, or other>"
  engine_version: "<version>"
  runner: "<exact script and revision>"
  launcher: "<exact launcher or scheduler manifest>"
  host_or_cluster: "<target>"
  environment_activation: "<documented activation command>"
  environment_record: "<lock, freeze, image, or environment path>"

resources:
  cpu: "<count>"
  gpu: "<count and type if relevant>"
  memory: "<request or limit>"
  concurrency: "<tasks per resource>"
  estimated_walltime: "<estimate and evidence>"

stages:
  # Repeat this item for equilibration, heating, holding, cooling, or production.
  - name: "<stage name>"
    ensemble: "<NVE/NVT/NPT>"
    duration: "<physical time>"
    timestep: "<physical time>"
    steps: "<resolved integer>"
    temperature: "<constant or schedule>"
    pressure_or_stress: "<constant, schedule, or not applicable>"
    thermostat: "<type, parameters, damping, or none>"
    barostat: "<type, parameters, damping, coupling, or none>"
    seed: "<seed or not applicable>"
    starts_from: "<input, previous stage, or exact checkpoint>"

outputs:
  directory: "<unique output directory>"
  resolved_contract: "<YAML or JSON path>"
  thermo:
    path: "<live thermodynamic file>"
    format: "<CSV, text, or engine format>"
    cadence_physical: "<requested cadence>"
    stride_steps: "<resolved integer>"
    core_instantaneous_fields:
      - step
      - time
      - stage
      - temperature
      - potential_energy
      - kinetic_energy
      - total_energy
    conditional_instantaneous_fields:
      - target_temperature
      - volume
      - density
      - total_pressure
      - pressure_or_stress_components
      - cell
    conditional_quantity_conventions: "<definitions, units, and signs>"
    averaging:
      enabled: false
      source_stride_steps: "<sampling stride>"
      window_steps: "<window>"
      emit_stride_steps: "<output stride>"
      fields: []
  trajectory:
    path: "<live trajectory path or segment pattern>"
    format: "<format>"
    cadence_physical: "<requested cadence>"
    stride_steps: "<resolved integer>"
  checkpoint:
    path_or_pattern: "<path or pattern>"
    cadence_physical: "<cadence>"
    stride_steps: "<resolved integer>"
  live_flush: true
  status_file: "<live state/progress/error record>"

validation:
  static_checks: ["<checks and results>"]
  separate_smoke_test: "planned"
  smoke_reference: "<new smoke output or prior validation>"
  skip_reason: "<required when skipped>"
  startup_monitoring: "required"

monitoring:
  startup_window: "<steps or physical time>"
  steady_interval: "<monitoring cadence>"
  signals:
    - scheduler_or_process_state
    - completed_simulation_time
    - thermo_and_trajectory_freshness
    - thermo_rows_and_trajectory_frames
    - latest_temperature_energy_pressure_volume_cell
    - resource_pressure
  stop_conditions: ["<explicit numeric or structural conditions>"]

policies:
  overwrite: false
  resume: "<checkpoint and append policy>"
  retry: "<same contract only; changed parameters require a new run>"
  environment_failure: "report and ask; do not modify or downgrade"

evidence:
  - field: "<contract field>"
    value: "<resolved value>"
    status: "<discovered/proposed/confirmed/conflict>"
    source: "<file, raw artifact, command result, or user approval>"
    note: "<discrepancy or rationale>"

approval:
  approved: false
  approved_by: "<user>"
  approved_at: "<timestamp>"
  material_exceptions: ["<skipped smoke or other approved exception>"]
```

Before launch, present a compact alignment summary rather than the full file.
Show the scientific claim, exact input, potential/runtime, stage table, output
and averaging cadence, resource estimate, smoke decision, stop conditions,
output directory, and unresolved conflicts. Save the complete resolved contract
after approval.

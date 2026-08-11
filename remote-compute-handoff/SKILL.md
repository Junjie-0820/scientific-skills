---
name: remote-compute-handoff
description: "Operate and hand off remote scientific-computing infrastructure safely: resolve known hosts and site documentation, connect and transfer data, reuse or isolate runtimes, prepare and submit direct or scheduled jobs, monitor startup, diagnose infrastructure failures, resume without duplication, and record verified machine state. Use when MD, DFT, feature-generation, or model-training work needs remote machines, clusters, GPUs, SSH, storage, Conda/vendor/container environments, Kubernetes, Slurm, job monitoring, recovery, or infrastructure troubleshooting."
---

# Remote Compute Handoff

Act as the infrastructure layer for a scientific workflow. Preserve the calling workflow's scientific method, inputs, and numerical settings. Never make a silent scientific change or downgrade to solve an infrastructure problem.

## Resolve Context

Use `~/.config/scientific-skills/remote-compute-profile.md` as the sole persistent inventory of personal machines and local conventions. Read only its shared rules and matching host section. If it is missing or the host is absent, ask for current machine information; do not reconstruct an inventory from project notes.

Current user instructions define intent, current site documentation defines platform procedure, the profile records stable personal facts, and live read-only checks establish current state. Report conflicts instead of choosing silently.

Load references conditionally:

- Read [access-and-transfer.md](references/access-and-transfer.md) for SSH, tunnels, persistent sessions, SCP/rsync, or object storage.
- Read [environments-and-images.md](references/environments-and-images.md) for Conda, vendor overlays, packed environments, Docker, CUDA, or storage pressure.
- Read [schedulers-and-jobs.md](references/schedulers-and-jobs.md) for Kubernetes, Slurm, platform jobs, direct processes, resources, monitoring, or recovery.

Do not load unrelated references.

## Work to the Requested Outcome

1. Identify the requested infrastructure outcome, host/platform, project, workload, and relevant site documentation.

2. Inspect the minimum current state without mutation. Keep remote commands short and single-purpose; use a reviewable script for multi-step work. Do not repeat an unchanged failed operation.

3. Load only the relevant reference and perform the requested access, transfer, runtime, or job operation. Use the cheapest meaningful preflight and confirm scope before a costly action.

4. Only when execution is requested, build the launch contract from [schedulers-and-jobs.md](references/schedulers-and-jobs.md), submit after target and resources are clear, then monitor startup and durable progress. Diagnose before changing one variable; check for existing work before retrying.

5. Hand off the target, status or identifier, runtime/resources, persistent paths, checks, risks, and exact safe next action. Propose profile updates only for stable verified infrastructure facts; keep run-specific state in project records.

Stop when the requested infrastructure outcome is reached; do not force the remaining workflow.

## Stop Conditions

Stop and ask for help when access or platform documentation remains unavailable after bounded checks, credentials or resource allocation require user action, a global/shared environment must change, a destructive transfer or cleanup is proposed, or the only remaining option materially changes the requested computation.

Never place passwords, private keys, access keys, tokens, or temporary credentials in the skill, profile, project notes, shell history, logs, or version control.

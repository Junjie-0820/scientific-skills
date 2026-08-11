# Schedulers and Jobs

Read this reference only when allocating resources, submitting, monitoring, stopping, or resuming work.

## Identify the Host Role

Do not use site terms such as "resident" or "ordinary" without the site's definition. Establish which model applies:

- **Login node:** prepare files and submit; do not run heavy computation.
- **Scheduled compute node:** exists for an allocation; scratch and processes may disappear afterward.
- **Container job:** image-based and usually ephemeral; only documented mounted paths persist.
- **Stable direct SSH compute host:** may permit `tmux` or `nohup`, but ownership and GPU allocation still apply.

Site documentation overrides generic advice.

## Common Submission Gate

Resolve before any scheduler-specific action:

- approved script/command and inputs;
- queue, partition, namespace, account, or platform project;
- CPU, GPU, memory, wall time, worker count, and concurrency;
- image/runtime, accelerator compatibility, and mounts;
- persistent output, logs, checkpoints, and temporary scratch;
- completion and failure signals;
- retry limit, idempotency, and duplicate-work prevention.

Use a dry run or smallest configuration validation before submission when the scheduler supports it. Record the exact rendered job artifact.

## Kubernetes

Prefer a `Job` for finite batch work rather than a bare Pod. Inspect the site's known-good template and active context/namespace before rendering a new job.

Typical read-only or validation sequence:

```bash
kubectl config current-context
kubectl apply --dry-run=client -f <job.yaml>
kubectl get jobs
kubectl get pods -l <project-label>
kubectl logs <pod> --tail=200
kubectl describe pod <pod>
```

Apply only after the rendered YAML, namespace, resource requests, mounts, command, and output path are approved. A Job can retry failed Pods, so the workload must tolerate restart or use explicit retry policy. Capture the Job and Pod names. Do not delete a Job merely to make the status cleaner; deletion can remove Pods and evidence.

Official reference:

- [Kubernetes Jobs](https://kubernetes.io/docs/concepts/workloads/controllers/job/)

## Slurm

Use the site's template and partition/account rules. Validate the script's `#SBATCH` resource directives, output/error paths, working directory, environment activation, and launch command.

Typical sequence:

```bash
sbatch <job.sh>
squeue -j <job-id>
sacct -j <job-id>
```

`sbatch` returning a job ID means the controller accepted the script; it does not mean resources were granted or execution began. Use `squeue`, `sacct`, and the scheduler log to distinguish pending, running, completed, cancelled, timeout, and failure states.

Official reference:

- [Slurm sbatch](https://slurm.schedmd.com/sbatch.html)

## Web or API Platforms

Read the platform's own user document before generic Kubernetes or Docker documentation. Confirm:

- login/tunnel and authentication method;
- image source and private-registry convention;
- worker and GPU limits;
- injected distributed variables;
- shared-storage mapping inside the container;
- job states, log API, stop/delete behavior, and output retention.

Do not run computation on the login host when the platform expects job submission. Do not copy useful outputs to an ephemeral container path. Treat current quota, default image, node count, and availability as mutable observations.

## Direct SSH Processes

Use a direct process only on a host documented for direct computation and when resources are assigned. Prefer a versioned launcher that writes its PID, log, status, and outputs to the project area. `tmux` is appropriate for interactive supervision; `nohup` is appropriate for a simple non-interactive launcher with explicit redirection.

Do not treat a surviving SSH connection as job management. Confirm the real process, GPU allocation, log growth, exit status, and output heartbeat. Avoid untracked background pipelines.

## Request User Scheduling

Ask the user to allocate or choose resources when:

- the target GPU or node is not assigned;
- the host is occupied and sharing policy is unclear;
- quota, namespace, account, partition, or residency is missing;
- the task needs multi-node, exclusive, high-memory, or many-GPU resources;
- expected runtime or resource demand is materially above the validated pilot;
- a job remains unscheduled for a site-dependent interval;
- proceeding would require bypassing the scheduler or interfering with another workload.

Provide the user with the smallest actionable request: workload, tested resource minimum, preferred target, estimated duration, and blocking observation.

## Monitor and Recover

Monitor submission, scheduling, container startup, application startup, first durable output, and steady progress as separate states. Preserve scheduler events and application logs.

Before retrying:

1. Verify that no earlier copy is still queued or running.
2. Inspect partial outputs and claims.
3. Determine whether restart is safe and idempotent.
4. Change one diagnosed variable.
5. Use a new job/run identifier while retaining the failed evidence.

Stop and escalate when credentials, mounts, platform control plane, global runtime, quota, or hardware allocation require authority the current task does not provide.

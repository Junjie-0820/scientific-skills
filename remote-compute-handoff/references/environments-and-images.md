# Environments and Images

Read this reference only when selecting, inspecting, reproducing, or repairing a runtime.

## Inspect First

Identify the exact host and execution surface, then inspect only what is needed:

```bash
uname -m
command -v python
python --version
python -c "import sys; print(sys.executable)"
conda info --envs
nvidia-smi
```

Use the profile's validated interpreter or activation script instead of whichever `python` happens to appear first. Check required imports, executable versions, CUDA visibility, model/weight path, and a minimal operation before production.

Do not mutate an environment merely to learn what it contains.

## Runtime Preference

Prefer, in order:

1. A validated project environment or immutable image.
2. A validated vendor overlay on a compatible base.
3. A separate project-owned environment or derived image.
4. A newly assembled stack only with explicit approval.

Record the base interpreter, platform/architecture, important package versions, accelerator runtime, overlay path, model weights, activation command, and validation evidence.

## Conda Safety

- Treat `base`, shared environments, and another workflow's environment as read-only.
- Install Conda packages before pip packages when building a new environment.
- Run pip through the selected interpreter, for example `python -m pip`, so the target is unambiguous.
- After pip has modified an environment, avoid later Conda mutations; rebuild from a specification when practical.
- Prefer a lock or explicit specification for reproducibility. Use a human-maintained environment YAML for intent.
- Place environments and package caches on local or suitable POSIX storage, not an object-store mount.
- Never use broad cleanup commands such as `conda clean --all` without checking ownership, recoverability, and expected space savings.

Official reference:

- [Conda environment management](https://docs.conda.io/projects/conda/en/stable/user-guide/tasks/manage-environments.html)

## Vendor Overlays

Use a vendor overlay when a validated base is nearly sufficient and the missing packages can be isolated safely. Keep the overlay immutable for a run and prepend it only in the task process, normally through a reviewed activation script.

Store the archive and its checksum on durable storage. Extract it into node-local scratch. Verify Python ABI, architecture, base packages, compiled libraries, and imports before use. Do not assume that a vendor tree prepared for one Python/CUDA/PyTorch combination works with another.

An overlay is not a license to mask arbitrary dependency conflicts. If it replaces core packages or changes the numerical stack, create an isolated environment or image instead.

## Packing Conda Environments

Use a lock/specification when the target can recreate the environment. Use `conda-pack` when deployment needs a relocatable archive and source/target OS and architecture are compatible.

Do not copy a Conda directory directly. When using `conda-pack`, record source prefix, platform, archive checksum, target path, and required `conda-unpack` step. Build the archive from an actively maintained environment whose package cache is intact.

Official reference:

- [conda-pack documentation](https://conda.github.io/conda-pack/)

## Containers and Images

- Treat an image as immutable. Make a derived image with a new tag instead of entering a shared container and modifying it by hand.
- Put stable dependencies in the image; mount code, data, model weights, secrets, and outputs according to site policy.
- Use a versioned requirements/lock file and run `pip check` plus minimal imports during image validation.
- A job-start `pip install` is acceptable only when the platform workflow explicitly uses an immutable base plus a complete, tested lock. It trades startup time and network dependence for fewer stored images.
- Use multi-stage builds and a minimal runtime stage when they materially reduce image size.
- Keep build caches only where useful and owned. Never run broad `docker system prune` on a shared host without explicit authorization.
- Test CUDA execution on an actual GPU allocation. A successful CPU-side image build/import does not validate the NVIDIA container runtime or GPU kernels.
- On a platform without Docker/Podman/Buildah on the login host, follow its image manager or Kaniko workflow rather than installing a daemon.

Official references:

- [Docker build best practices](https://docs.docker.com/build/building/best-practices/)
- [Docker multi-stage builds](https://docs.docker.com/build/building/multi-stage/)
- [Docker bind mounts](https://docs.docker.com/engine/storage/bind-mounts/)

## Save Space Deliberately

Estimate the size of environments, package caches, images, archives, raw outputs, and checkpoints before cleanup. Prefer these non-destructive design choices:

- one compatible base image plus task-specific locks;
- one durable archive plus per-node scratch extraction;
- mounted weights rather than copying them into every image;
- compact final artifacts separate from raw calculation trees;
- incremental transfer rather than repeated full copies;
- expiry policies for explicitly disposable scratch.

Delete caches, images, environments, or archives only after resolving exact ownership and confirming that a reproducible source remains.

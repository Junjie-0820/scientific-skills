# Access and Transfer

Read this reference only for remote access, connection persistence, tunneling, or file transfer.

## SSH Access

Prefer a named entry in the local SSH config and test it with one short command:

```bash
ssh <host-alias> hostname
```

Use key authentication when it is already configured. Do not add a password to a command, URI, Markdown file, or Git-tracked configuration. Use `sshpass` only when the user explicitly requires automation against a password-only legacy host. In that exceptional case, obtain the approved secret source and keep it out of arguments, history, logs, and durable files.

Do not disable host-key verification to bypass a warning. Resolve a changed host key with the user or authoritative platform record.

For repeated connections, configure connection reuse per host in `~/.ssh/config`:

```sshconfig
Host <host-alias>
    ControlMaster auto
    ControlPath ~/.ssh/cm-%C
    ControlPersist 10m
    ServerAliveInterval 30
    ServerAliveCountMax 3
```

Connection reuse reduces repeated authentication; it does not keep a computation alive. Use `tmux` on a stable host for a reconnectable interactive shell. Use the site scheduler for production computation.

VS Code Remote-SSH is appropriate for editing and terminals on a stable SSH host. It installs a VS Code Server remotely, so confirm that the host policy and storage allow it. Do not expect it to attach to an ephemeral platform job container without a supported SSH endpoint.

For a tunnel, use a reviewed `LocalForward` entry or a dedicated `ssh -N -L` session. Confirm the local bind address and remote destination from the site document.

Official references:

- [OpenSSH client configuration](https://man.openbsd.org/ssh_config)
- [VS Code Remote-SSH](https://code.visualstudio.com/docs/remote/ssh)

## Avoid Fragile Remote Commands

Keep a direct SSH command limited to a small inspection or one known script. Avoid a single command that combines shell initialization, Conda activation, exported variables, nested quotes, pipelines, backgrounding, and application arguments.

For a multi-step operation:

1. Reuse an existing versioned script when available.
2. Otherwise prepare a small local script for review.
3. Transfer it into the authorized project area.
4. Run it by path and capture its log and exit status.

Do not create helper scripts in global directories or overwrite an existing remote script without inspecting it.

## Choose a Transfer Method

| Situation | Preferred tool | Reason |
| --- | --- | --- |
| One or a few small files over SSH | `scp` | Simple one-shot copy |
| Directory tree or repeated synchronization over SSH | `rsync -a` with progress | Incremental transfer and restartability |
| Native object-store URL such as `tos://...` | Object-store CLI such as `tosutil` | Uses object-store APIs and multipart behavior |
| Platform login storage mapped into containers | Platform-documented `rsync`/upload path | Preserves the platform's path mapping |

Resolve trailing-slash semantics before using `rsync`. Start with a dry run when the destination contains valuable files. Do not add deletion flags unless deletion was explicitly requested and separately reviewed.

Before a bulk transfer, estimate source size and file count, destination capacity or quota, likely duration, and any egress constraint. Ask before proceeding when the cost is material or the destination cannot be verified.

For important transfers, record source, destination, total size/file count, tool, completion status, and a suitable verification such as checksums or manifest comparison.

## Object-Store Rules

Treat mounted object storage as an object namespace, not a low-latency POSIX disk:

- Avoid creating Conda environments or package caches on it.
- Avoid extracting or compressing large small-file trees directly on the mount.
- Avoid recursive listing as a first diagnostic when it is known to be slow or unsupported.
- Keep a durable archive in object storage, copy it to node-local scratch, and unpack there.
- Package useful results on local scratch before uploading when that reduces object count.
- Test the CLI version, endpoint, credentials, permission, and a small prefix before a bulk operation.

The presence of `tosutil` does not prove that its AK/SK is configured or authorized. A failed `ls` may reflect credentials, permissions, endpoint configuration, or prefix semantics. Do not convert such a failure into a destructive workaround.

Official reference:

- [Volcengine tosutil quick start](https://www.volcengine.com/docs/6349/152743?lang=en)

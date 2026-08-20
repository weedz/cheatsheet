# restic

[restic](https://restic.readthedocs.io/en/stable/index.html)

If you are talking `https` over a local network (or without "valid" TLS certificates) If you are getting the following error:

> Fatal: unable to open repository at rclone:some-repo: error talking HTTP to rclone: exit status 1

Set the env variable `RCLONE_NO_CHECK_CERTIFICATE` to `true`:

```console
export RCLONE_NO_CHECK_CERTIFICATE=true
```

## Restore

Ref: <https://restic.readthedocs.io/en/stable/050_restore.html>

List snapshots:

```console
restic --repo "[repo]" snapshots
```

Restore:

```console
restic --repo "[repo]" restore [snapshot id] --include [path <optional>] --target [where files should go]
```

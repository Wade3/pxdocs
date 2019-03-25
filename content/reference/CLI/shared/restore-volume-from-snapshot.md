---
title: Restoring Volumes from Snapshots
hidden: true
keywords: portworx, kubernetes
description: Learn how to restore volumes from snapshots
---

In order to restore a volume from snapshot, Use `pxctl volume restore` command.

```text
/opt/pwx/bin/pxctl volume restore -h
```

```
NAME:
   pxctl volume restore - Restore volume from snapshot

USAGE:
   pxctl volume restore [command options] volume-name-or-ID

OPTIONS:
   --snapshot value, -s value  snapshot-name-or-ID
```

In the below example parent volume `myvol` is restored from its snapshot `mysnap`. Make sure volume is detached in order to restore from the snapshot.

```text
pxctl volume restore --snapshot mysnap myvol
```

```
Successfully started restoring volume myvol from mysnap.
```

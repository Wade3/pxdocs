---
title: Microsoft SQL Server
keywords: portworx, px-developer, microsoft sql server, database, cluster, storage
description: Follow this guide to learn how use Microsoft SQL Server with Portworx.
weight: 2
noicon: true
---


This page explains how to deploy Microsoft SQL Server with Portworx via Docker.

### Create a high availability storage volume for SQL Server
To create a high availability storage volume for SQL Server, use 'docker run' with the '-v' option:

```text
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=P@ssw0rd' \
      -p 1433:1433 --name sqlserver
      --volume-driver=pxd \
      -v name=mssqlvol,size=10,repl=3:/var/opt/mssql \
      -d microsoft/mssql-server-linux
```

This command runs an instance of `mssql-server-linux` with a high availability 10 GiB volume, created without having to provision storage in advance. Specifying `repl=3` guarantees the persistent data will replicate on three separate nodes.

{{% info %}}
You can run multiple instances of SQL Server on the same host, each with its own unique persistent volume mapped, and each with its own unique IP address published.
{{% /info %}}

### Connect to SQL Server
Connect to SQL Server's sqlcmd CLI with `docker exec`.

```text
docker exec -it sqlserver /opt/mssql-tools/bin/sqlcmd -S localhost -U SA -P 'P@ssw0rd'
```

### Create a database storage volume snapshot

To take a point-in-time recovery snapshot of the `mssql-server-linux` instance, use  `pxctl volume snapshot create`.

```
$ pxctl volume snapshot create --name mssqlvol_snap_0628
Volume snap successful: 342580301989879504
```

The string of digits in the output is the volume ID of the new snapshot. Use the ID (`342580301989879504`), or the name (`mssqlvol_snap_0628`) to refer to the snapshot in subsequent `pxctl` commands.

### Start another instance of SQL Server from a snapshot

The `mssqlvol_snap_0628` snapshot can be used to start another instance of SQL Server on a different node. This is because Portworx volume snapshots are read-write by default, and are visible globally throughout a cluster.

To list snapshots, use `pxctl volume list --snapshot`.

```
$ pxctl volume list --snapshot
ID			NAME			SIZE	HA	SHARED	ENCRYPTED  COMPRESSED	IO_PRIORITY	SCALE	STATUS
342580301989879504	mssqlvol_snap_0628	10 GiB	3	no	no  no		LOW		0	up - detached
```
Start a new `mssql-server-linux` instance, specifying the snapshot with `-v`.

```text
docker run -e 'ACCEPT_EULA=Y' -e 'SA_PASSWORD=P@ssw0rd' \
       -p 1433:1433 --volume-driver=pxd \
       -v mssqlvol_snap_0628:/var/opt/mssql \
       -d microsoft/mssql-server-linux
```

Confirm the new instance of SQL Server is running with `docker ps`.
```
$ docker ps
CONTAINER ID        IMAGE                          COMMAND                  CREATED             STATUS              PORTS                    NAMES
46eff5a9cbd6        microsoft/mssql-server-linux   "/bin/sh -c /opt/mssq"   4 minutes ago       Up 4 minutes        0.0.0.0:1433->1433/tcp   compassionate_perlman
0636d98250c4        portworx/px-dev                "/docker-entry-point."   2 hours ago         Up 2 hours                                   portworx.service
```

Microsoft has [additional detail on running SQL Server container images with Docker](https://docs.microsoft.com/en-us/sql/linux/quickstart-install-connect-docker?view=sql-server-linux-2017#a-idpersista-persist-your-data).

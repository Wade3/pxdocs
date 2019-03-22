---
title: Create and Manage Volumes
keywords: portworx, pxctl, command-line tool, cli, reference
description: This guide shows you how to use the PXCL CLI to create and manage volumes.
weight: 12
---

To create and manage volumes, use `pxctl volume`. You can use the created volumes directly with Docker with the `-v` option.

```text
/opt/pwx/bin/pxctl volume -h
```

```
Manage volumes

Usage:
  pxctl volume [flags]
  pxctl volume [command]

Aliases:
  volume, v

Examples:
pxctl volume create -s 100 myVolName

Available Commands:
  clone                Create a clone volume
  create               Create a volume
  delete               Delete a volume
  ha-update            Update volume HA level
  import               Import data into a volume
  inspect              Inspect a volume
  list                 List volumes in the cluster
  locate               Locate volume
  requests             Show all pending requests
  restore              Restore volume from snapshot
  snap-interval-update Update volume configuration
  snapshot             Manage volume snapshots
  stats                Volume Statistics
  update               Update volume settings
  usage                Show volume usage information

Flags:
  -h, --help   help for volume

Global Flags:
      --ca string       path to root certificate for ssl usage
      --cert string     path to client certificate for ssl usage
      --color           output with color coding
      --config string   config file (default is $HOME/.pxctl.yaml)
  -j, --json            output in json
      --key string      path to client key for ssl usage
      --port string     Portworx Management port (default: 9001)
      --raw             raw CLI output for instrumentation
      --ssl             ssl enabled for portworx
```

### Create volumes {#create-volumes}

Portworx creates volumes from the global capacity of a cluster. You can expand capacity and throughput by adding a node to the cluster. Portworx protects storage volumes from hardware and node failures through automatic replication.

* Durability: Set replication through policy, using the High Availability setting.
* Each write is synchronously replicated to a quorum set of nodes.
* Any hardware failure means that the replicated volume has the latest acknowledged writes.
* Elastic: Add capacity and throughput at each layer, at any time.
* Volumes are thinly provisioned, only using capacity as needed by the container.
* You can expand and contract the volume’s maximum size, even after data has been written to the volume.

A volume can be created before use by its container or by the container directly at runtime. Creating a volume returns the volume’s ID. This same volume ID is returned in Docker commands \(such as `Docker volume ls`\) as is shown in `pxctl` commands.

Example of creating a volume through `pxctl`, where the volume ID is returned:

```text
/opt/pwx/bin/pxctl volume create myVol
```

```
3903386035533561360
```

Throughput is controlled per container and can be shared. Volumes have fine-grained control, set through policy.

* Throughput is set by the IO Priority setting. Throughput capacity is pooled.
* Adding a node to the cluster expands the available throughput for reads and writes.
* The best node is selected to service reads, whether that read is from a local storage devices or another node’s storage devices.
* Read throughput is aggregated, where multiple nodes can service one read request in parallel streams.
* Fine-grained controls: Policies are specified per volume and give full control to storage.
* Policies enforce how the volume is replicated across the cluster, IOPs priority, filesystem, blocksize, and additional parameters described below.
* Policies are specified at create time and can be applied to existing volumes.

Set policies on a volume through the options parameter. These options can also be passed in through the scheduler or using the inline volume spec. See the section _Inline volume spec_ below for more details.

Show the available options through the –help command, as shown below:

```text
/opt/pwx/bin/pxctl volume create -h
```

```
NAME:
   pxctl volume create - Create a volume
USAGE:
   pxctl volume create [command options] volume-name
OPTIONS:
   --shared                                      make this a globally shared namespace volume
   --secure                                      encrypt this volume using AES-256
   --secret_key value                            secret_key to use to fetch secret_data for the PBKDF2 function
   --use_cluster_secret                          Use cluster wide secret key to fetch secret_data
   --label pairs, -l pairs                       list of comma-separated name=value pairs
   --size value, -s value                        volume size in GB (default: 1)
   --fs value                                    filesystem to be laid out: none|xfs|ext4 (default: "ext4")
   --block_size size, -b size                    block size in Kbytes (default: 32)
   --repl factor, -r factor                      replication factor [1..3] (default: 1)
   --scale value, --sc value                     auto scale to max number [1..1024] (default: 1)
   --io_priority value, --iop value              IO Priority: [high|medium|low] (default: "low")
   --journal                                     Journal data for this volume
   --io_profile value, --prof value              IO Profile: [sequential|random|db|db_remote] (default: "sequential")
   --sticky                                      sticky volumes cannot be deleted until the flag is disabled [on | off]
   --aggregation_level level, -a level           aggregation level: [1..3 or auto] (default: "1")
   --nodes value                                 comma-separated Node Ids
   --zones value                                 comma-separated Zone names
   --racks value                                 comma-separated Rack names
   --group value, -g value                       group
   --enforce_cg, --fg                            enforce group during provision
   --periodic mins,k, -p mins,k                  periodic snapshot interval in mins,k (keeps 5 by default), 0 disables all schedule snapshots
   --daily hh:mm,k, -d hh:mm,k                   daily snapshot at specified hh:mm,k (keeps 7 by default)
   --weekly weekday@hh:mm,k, -w weekday@hh:mm,k  weekly snapshot at specified weekday@hh:mm,k (keeps 5 by default)
   --monthly day@hh:mm,k, -m day@hh:mm,k         monthly snapshot at specified day@hh:mm,k (keeps 12 by default)
   --policy value, --sp value                    policy names separated by comma
```

#### Using the --nodes Argument

Adding `--nodes=LocalNode` argument while creating a volume using `pxctl` will place at least one replica of the volume on the node where the command is run.

This is useful when using a script to create a volume locally on a node.

**Command**

```text
pxctl volume create --nodes=LocalNode localVolume
```

```
Volume successfully created: 756818650657204847
```

Now inspect the volume and check that the volume's replica is on the node where the command was run. The replicas are visible in the _"Replica sets on nodes"_ section.

```text
pxctl volume inspect localVolume
```

```
Volume  :  756818650657204847
        Name                     :  localVolume
        Size                     :  1.0 GiB
        Format                   :  ext4
        HA                       :  1
        IO Priority              :  LOW
        Creation time            :  Mar 20 00:30:05 UTC 2019
        Shared                   :  no
        Status                   :  up
        State                    :  detached
        Reads                    :  0
        Reads MS                 :  0
        Bytes Read               :  0
        Writes                   :  0
        Writes MS                :  0
        Bytes Written            :  0
        IOs in progress          :  0
        Bytes used               :  340 KiB
        Replica sets on nodes:
                Set 0
                  Node           : 70.0.29.90 (Pool 1)
        Replication Status       :  Detached
```

#### Create with Docker {#create-with-docker}

All `docker volume` commands are reflected into Portworx storage. For example, a `docker volume create`command provisions a storage volume in a Portworx storage cluster.

```text
docker volume create -d pxd --name <volume_name>
```

As part of the `docker volume` command, you can add optional parameters through the `--opt` flag. The option parameters are the same, whether you use Portworx storage through the Docker volume or the `pxctl`commands.

Example of options for selecting the container’s filesystem and volume size:

```text
docker volume create -d pxd --name <volume_name> --opt fs=ext4 --opt size=10G
```

### Inline volume spec {#inline-volume-spec}

PX supports passing the volume spec inline along with the volume name. This is useful when creating a volume with your scheduler application template inline and you do not want to create volumes before hand.

For example, a PX inline spec can be specified as the following:

```text
docker volume create -d pxd io_priority=high,size=10G,repl=3,snap_schedule="periodic=60#4;daily=12:00#3",name=demovolume
```

This is useful when you need to create a volume dynamically while using docker run. For example, the following command will create a volume and launch the container dynamically:

```text
docker run --volume-driver pxd -it -v io_priority=high,size=10G,repl=3,snap_schedule="periodic=60#4;daily=12:00#3",name=demovolume:/data busybox sh
```

The above command will create a volume called demovolume with an initial size of 10G, HA factor of 3, snap scheudle with periodic and daily snapshot creation and a IO priority level of high and start the busybox container.

Each spec key must be comma separated. The following are supported key value pairs:

```
IO priority      - io_priority=[high|medium|low]
Volume size      - size=[1..9][G|M|T]
HA factor        - repl=[1,2,3]
Block size       - bs=[4096...]
Shared volume    - shared=true
File System      - fs=[xfs|ext4]
Encryption       - passphrase=secret
snap_schedule    - "periodic=mins#k;daily=hh:mm#k;weekly=weekday@hh:mm#k;monthly=day@hh:mm#k" where k is the number of snapshots to retain.
```

These inline specs can be passed in through the scheduler application template. For example, below is a snippet from a marathon configuration file:

```
"parameters": [
	{
		"key": "volume-driver",
		"value": "pxd"
	},
	{
		"key": "volume",
		"value": "size=100G,repl=3,io_priority=high,name=mysql_vol:/var/lib/mysql"
	}],
```

### Global Namespace \(Shared Volumes\) {#global-namespace-shared-volumes}

To use Portworx volumes across nodes and multiple containers, [click here](/concepts/shared-volumes).

### Delete volumes {#delete-volumes}

Volumes can be deleted with:

```text
/opt/pwx/bin/pxctl volume delete myOldVol
```

```
Delete volume 'myOldVol', proceed ? (Y/N): y
Volume myOldVol successfully deleted.
```

### Import volumes {#import-volumes}

Files can be imported from a directory into an existing volume. Files already existing on the volume will be retained or overwritten.

```text
/opt/pwx/bin/pxctl volume import --src /path/to/files myVol
```

```
Starting import of  data from /path/to/files into volume myVol...Beginning data transfer from /path/to/files myVol
Imported Bytes :   0% [>---------------------------------------------------------------------------------------------------------------------------------------] 14ms
Imported Files :   0% [>---------------------------------------------------------------------------------------------------------------------------------------] 16ms

Volume imported successfully
```

### Inspect volumes {#inspect-volumes}

Volumes can be inspected for their settings and usage using the `pxctl volume inspect` sub menu.

```text
/opt/pwx/bin/pxctl volume inspect clitest
```

```
Volume	:  970758537931791410
	Name            	 :  clitest
	Size            	 :  1.0 GiB
	Format          	 :  ext4
	HA              	 :  1
	IO Priority     	 :  LOW
	Creation time   	 :  Feb 26 16:29:53 UTC 2019
	Shared          	 :  no
	Status          	 :  up
	State           	 :  detached
	Reads           	 :  0
	Reads MS        	 :  0
	Bytes Read      	 :  0
	Writes          	 :  0
	Writes MS       	 :  0
	Bytes Written   	 :  0
	IOs in progress 	 :  0
	Bytes used      	 :  33 MiB
	Replica sets on nodes:
		Set  0
			Node 	 :  10.99.117.133
	Replication Status	 :  Detached
```

You can also inspect multiple volumes in one command.

To inspect the volume in `json` format, use the `-j` flag. Following is a sample output of:

Following is a sample output of the json volume inspect.

```text
/opt/pwx/bin/pxctl -j v i 486256711004992211
```

```
[{
 "id": "486256711004992211",
 "source": {
  "parent": "",
  "seed": ""
 },
 "readonly": false,
 "locator": {
  "name": "pvc-2910e5ab-1b5e-11e8-97a3-0269077ba1bd",
  "volume_labels": {
   "namespace": "default",
   "pvc": "px-nginx-shared-pvc"
  }
 },
 "ctime": "2018-02-27T01:33:16Z",
 "spec": {
  "ephemeral": false,
  "size": "1073741824",
  "format": "ext4",
  "block_size": "65536",
  "ha_level": "2",
  "cos": "low",
  "io_profile": "sequential",
  "dedupe": false,
  "snapshot_interval": 0,
  "volume_labels": {
   "kubectl.kubernetes.io/last-applied-configuration": "{\"apiVersion\":\"v1\",\"kind\":\"PersistentVolumeClaim\",\"metadata\":{\"annotations\":{\"volume.beta.kubernetes.io/storage-class\":\"px-nginx-shared-sc\"},\"name\":\"px-nginx-shared-pvc\",\"namespace\":\"default\"},\"spec\":{\"accessModes\":[\"ReadWriteOnce\"],\"resources\":{\"requests\":{\"storage\":\"1Gi\"}}}}\n",
   "repl": "2",
   "shared": "true",
   "volume.beta.kubernetes.io/storage-class": "px-nginx-shared-sc",
   "volume.beta.kubernetes.io/storage-provisioner": "kubernetes.io/portworx-volume"
  },
  "shared": true,
  "aggregation_level": 1,
  "encrypted": false,
  "passphrase": "",
  "snapshot_schedule": "",
  "scale": 0,
  "sticky": false,
  "group_enforced": false,
  "compressed": false,
  "cascaded": false,
  "journal": false,
  "nfs": false
 },
 "usage": "33964032",
 "last_scan": "2018-02-27T01:33:16Z",
 "format": "ext4",
 "status": "up",
 "state": "detached",
 "attached_on": "",
 "attached_state": "ATTACH_STATE_INTERNAL_SWITCH",
 "device_path": "",
 "secure_device_path": "",
 "replica_sets": [
  {
   "nodes": [
    "k2n3",
    "k2n1"
   ]
  }
 ],
 "runtime_state": [
  {
   "runtime_state": {
    "FullResyncBlocks": "[{0 0} {1 0} {-1 0} {-1 0} {-1 0}]",
    "ID": "0",
    "ReadQuorum": "1",
    "ReadSet": "[0 1]",
    "ReplNodePools": "1,1",
    "ReplRemoveMids": "",
    "ReplicaSetCurr": "[0 1]",
    "ReplicaSetCurrMid": "k2n3,k2n1",
    "ReplicaSetNext": "[0 1]",
    "ReplicaSetNextMid": "k2n3,k2n1",
    "ResyncBlocks": "[{0 0} {1 0} {-1 0} {-1 0} {-1 0}]",
    "RuntimeState": "clean",
    "TimestampBlocksPerNode": "[0 0 0 0 0]",
    "TimestampBlocksTotal": "0",
    "WriteQuorum": "2",
    "WriteSet": "[0 1]"
   }
  }
 ],
 "error": ""
}]
```

### Volume list {#volume-list}

This will list all of the volumes on the cluster.

```text
/opt/pwx/bin/pxctl volume list
```

```
ID			NAME		SIZE	HA	SHARED	ENCRYPTED	IO_PRIORITY	STATUS				SNAP-ENABLED
951679824907051932	objectstorevol	10 GiB	1	no	no		LOW		up - attached on 192.168.99.101	no
810987143668394709	testvol		1 GiB	1	no	no		LOW		up - detached			no
1047941676033657203	testvol2	1 GiB	1	no	no		LOW		up - detached			no
800735594334174869	testvol3	1 GiB	1	no	no		LOW		up - detached			no
```

### Volume locate {#volume-locate}

This will show where a given volume is mounted on containers running on the node.

```text
/opt/pwx/bin/pxctl volume locate 794896567744466024
```

```
host mounted:
  /directory1
  /directory2
```

In the example above, the volume represented by ID 794896567744466024 is mounted in two containers on the mountpoints shown.

### Volume snapshots {#volume-snapshots}

You can take snapshots of PX volumes. Snapshots are thin and do not take additional space. PX snapshots use branch-on-write so that there is no additional copy when a snapshot is written to. This is done through B+ Trees.

```text
/opt/pwx/bin/pxctl volume snapshot
```

```
NAME:
   pxctl volume snapshot - Manage volume snapshots

USAGE:
   pxctl volume snapshot command [command options] [arguments...]

COMMANDS:
     create, c  Create a volume snapshot

OPTIONS:
   --help, -h  show help
```

Snapshots are read-only. To restore a volume from a snapshot, use the `pxctl volume restore` command.

### Volume Clone {#volume-clone}

In order to create a volume clone from volume/snapshot, Use `pxctl volume clone` command.

```text
/opt/pwx/bin/pxctl voliume clone -h
```

```
NAME:
   pxctl volume clone - Create a clone volume

USAGE:
   pxctl volume clone [command options] volume-name-or-ID

OPTIONS:
   --name value             user friendly name
   --label pairs, -l pairs  list of comma-separated name=value pairs
```

In the below example, `myvol_clone` is the clone from the parent volume `myvol`

```text
/opt/pwx/bin/pxctl volume clone -name myvol_clone myvol
```

```
Volume clone successful: 55898055774694370
```

### Volume Restore {#volume-restore}

{{% content "reference/CLI/shared/restore-volume-from-snapshot.md" %}}

### Update the snap interval of a volume {#volume-siu}

```
Flags:
  -p, --periodic string   periodic snapshot interval in mins,k (keeps 5 by default), 0 disables all schedule snapshots
      --policy string     policy names separated by comma
  -d, --daily strings     daily snapshot at specified hh:mm,k (keeps 7 by default)
  -w, --weekly strings    weekly snapshot at specified weekday@hh:mm,k (keeps 5 by default)
  -m, --monthly strings   monthly snapshot at specified day@hh:mm,k (keeps 12 by default)
```

Please see the documentation for (snapshots)[https://docs.portworx.com/reference/cli/snapshots/] for more details.

### Volume stats {#volume-stats}

Shows realtime read/write IO throughput.

```text
/opt/pwx/bin/pxctl volume stats mvVol
```

```
TS			Bytes Read	Num Reads	Bytes Written	Num Writes	IOPS		IODepth		Read Tput	Write Tput	Read Lat(usec)	Write Lat(usec)
2019-3-4:11 Hrs		0 B		0		0 B		0		0		0		0 B/s		0 B/s		0		0
```

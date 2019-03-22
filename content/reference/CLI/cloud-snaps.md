---
title: Cloud Snapshots and Recovery of PX Volumes
linkTitle: Cloud Snaps
keywords: portworx, container, Kubernetes, storage, Docker, k8s, flexvol, pv, persistent disk, snapshots, stork, clones, cloud, cloudsnap
description: Learn to take a cloud snapshot of a Portworx volume using pxctl and use that snapshot
weight: 3
---

## Overview of cloud backups

This document outlines how PX volumes can be backed up to different cloud provider's object storage including any S3-compatible object storage. If a user wishes to restore any of the backups, they can restore the volume from that point in the timeline. This enables administrators running persistent container workloads on-prem or in the cloud to safely backup their mission critical database volumes to cloud storage and restore them on-demand, enabling a seamless DR integration for their important business application data.

### Supported Cloud Providers

Portworx PX-Enterprise supports the following cloud providers

1. Amazon S3 and any S3-compatible Object Storage
2. Azure Blob Storage
3. Google Cloud Storage

### Backing up a PX Volume to cloud storage

The first backup uploaded to the cloud is a full backup. After that, subsequent backups are incremental.
After 6 incremental backups, every 7th backup is a full backup.

### Restoring a PX Volume from cloud storage

Any PX Volume backup can be restored to a PX Volume in the cluster. The restored volume inherits the attributes such as file system, size and block size from the backup. Replication level of the restored volume defaults to 1 irrespective of the replication of the volume that was backed up. Users can increase replication factor once the restore is complete on the restored volume.

## Performing cloud Backups of a PX Volume

Performing cloud backups of a PX Volume is available via `pxctl cloudsnap` command. This command has the following operations available for the full lifecycle management of cloud backups.

```text
/opt/pwx/bin/pxctl cloudsnap --help
```
```
Backup and restore snapshots to/from cloud

Usage:
  pxctl cloudsnap [flags]
  pxctl cloudsnap [command]

Aliases:
  cloudsnap, cs

Available Commands:
  backup       Backup a snapshot to cloud
  backup-group Backup a group of snapshot for a given group id or labels to cloud
  catalog      Display catalog for the backup in cloud
  delete       Delete a cloudsnap from the objectstore. This is not reversible.
  history      Show history of cloudsnap operations
  list         List snapshot in cloud
  restore      Restore volume to a cloud snapshot
  schedules    Manage schedules for cloud-snaps
  status       Report status of active backups/restores
  stop         stop an active backup/restore

Flags:
  -h, --help   help for cloudsnap
```

### Set the required cloud credentials

For this, we will use `pxctl credentials create` command. These cloud credentials are stored in an external secret store. Before you use the command to create credentials, ensure that you have [configured a secret provider of your choice](/key-management).

```text
pxctl credentials create
```

```
NAME:
   pxctl credentials create - Create a credential for cloud-snap

USAGE:
   pxctl credentials create [command options] <name>

OPTIONS:
   --provider value                            Object store provider type [s3, azure, google]
   --s3-access-key value
   --s3-secret-key value
   --s3-region value
   --s3-endpoint value                         Endpoint of the S3 server, in host:port format
   --s3-disable-ssl
   --azure-account-name value
   --azure-account-key value
   --google-project-id value
   --google-json-key-file value
   --encryption-passphrase value,
   --enc value  Passphrase to be used for encrypting data in the cloudsnaps
```

For Azure:

```text
pxctl credentials create --provider azure --azure-account-name portworxtest --azure-account-key zbJSSpOOWENBGHSY12ZLERJJV my-azure-cred
```

For AWS:

By default, Portworx creates a bucket (ID same as cluster UUID) to upload cloudsnaps. With Portworx version 1.5.0 onwards,uploading to a pre-created bucket by a user is supported. Thus the AWS credential provided to Portworx should either have the capability to create a bucket or the bucket provided to Portworx at minimum must have the permissions mentioned below. If you prefer that a user specified bucket be used for cloudsnaps, specify the bucket id with `--bucket` option while creating the credentials.

With user specified bucket (applicable only from 1.5.0 onwards):
```text
pxctl credentials create --provider s3  --s3-access-key AKIAJ7CDD7XGRWVZ7A --s3-secret-key mbJKlOWER4512ONMlwSzXHYA --s3-region us-east-1 --s3-endpoint s3.amazonaws.com --bucket bucket-id my-s3-cred
```
User created/specified bucket at minimum must have following permissions: Replace `<bucket-name>` with name of your user-provided bucket.
```json
{
     "Version": "2012-10-17",
     "Statement": [
			{
				"Sid": "VisualEditor0",
				"Effect": "Allow",
				"Action": [
					"s3:ListAllMyBuckets",
					"s3:GetBucketLocation"
				],
				"Resource": "*"
			},
			{
				"Sid": "VisualEditor1",
				"Effect": "Allow",
				"Action": "s3:*",
				"Resource": [
					"arn:aws:s3:::<bucket-name>",
					"arn:aws:s3:::<bucket-name/*"
				]
			}
		]
 }
```

Without user specified bucket:
```text
pxctl credentials create --provider s3  --s3-access-key AKIAJ7CDD7XGRWVZ7A --s3-secret-key mbJKlOWER4512ONMlwSzXHYA --s3-region us-east-1 --s3-endpoint s3.amazonaws.com my-s3-cred
```

For Google Cloud:

```text
pxctl credentials create --provider google --google-project-id px-test --google-json-key-file px-test.json my-google-cred
```
`pxctl credentials create` enables the user to configure the credentials for each supported cloud provider.

An additional encryption key can also be provided for each credential. If provided, all the data being backed up to the cloud will be encrypted using this key. The same key needs to be provided when configuring the credentials for restore to be able to decrypt the data succesfuly.

These credentials can only be created once and cannot be modified. In order to maintain security, once configured, the secret parts of the credentials will not be displayed.

### List the credentials to verify

Use `pxctl credentials list` to verify the credentials supplied.

```text
# pxctl credentials list

S3 Credentials
UUID						NAME		REGION			ENDPOINT						ACCESS KEY			SSL ENABLED		ENCRYPTION		BUCKET		WRITE THROUGHPUT (KBPS)
af563a4d-afd7-48df-90f7-8e8f9414ff77		my-s3-cred	us-east-1		70.0.99.121:9010,70.0.99.122:9010,70.0.99.123:9010	AB6R80F3SY0VW9NS6HYQ		false			false			<nil>		1979

Google Credentials
UUID						NAME			PROJECT ID		ENCRYPTION		BUCKET		WRITE THROUGHPUT (KBPS)
6585cf56-4ccf-42cc-a235-76aaf6fb10f4		my-google-cred		235475231246		false			<nil>		1502

Azure Credentials
UUID						NAME			ACCOUNT NAME		ENCRYPTION		BUCKET		WRITE THROUGHPUT (KBPS)
1672e1c9-c513-44db-b8b5-b59e3d35a3a2		my-azure-cred		pwx-test		false			<nil>		724
```

`pxctl credentials list`  only displays non-secret values of the credentials. Secrets are neither stored locally nor displayed.  These credentials will be stored as part of the secret endpoint given for PX for persisting authentication across reboots. Please refer to `pxctl secrets` help for more information.

The [Credentials](/reference/cli/credentials) will also have more details about this command.

### Perform Cloud Backup of single volumes

The actual backup of the PX Volume is done via the `pxctl cloudsnap backup` command

```text
pxctl cloudsnap backup
```

```
NAME:
   pxctl cloudsnap backup - Backup a snapshot to cloud

USAGE:
   pxctl cloudsnap backup [command options] [arguments...]

OPTIONS:
   --volume value, -v value       source volume
   --full, -f                     force a full backup
   --cred-id value, --cr value    Cloud credentials ID to be used for the backup

```

This command is used to backup a single volume to the cloud provider using the specified credentials.
This command decides whether to take a full or incremental backup depending on the existing backups for the volume.
If it is the first backup for the volume it takes a full backup of the volume. If its not the first backup, it takes an incremental backup from the previous full/incremental backup.

```text
pxctl cloudsnap backup volume1 --cred-id 82998914-5245-4739-a218-3b0b06160332
```

Users can force the full backup any time by giving the --full option.
If only one credential is configured on the cluster, then the cred-id option may be skipped on the command line.

Here are a few steps to perform cloud backups successfully

* List all the available volumes to choose the volume to backup

    ```text
    pxctl volume list
    ```
    ```
    ID			NAME	SIZE	HA	SHARED	ENCRYPTED	IO_PRIORITY	SCALE	STATUS
    56706279008755778	NewVol	4 GiB	1	no	no		LOW		1	up - attached on 70.0.9.73
    980081626967128253	evol	2 GiB	1	no	no		LOW		1	up - detached
    ```

* List the configured credentials

    ```text
    pxctl cloudsnap credentials list
    ```
    ```
    Azure Credentials
    UUID						ACCOUNT NAME		ENCRYPTION
    ef092623-f9ba-4697-aeb5-0d5d6d9b5742		portworxtest		false
    ```

* Login to the secrets database to authenticate Portworx with the credentials
    {{<info>}}**Kubernetes users:** This is not required if you have are using Portworx 2.0 and higher on Kubernetes and you have -secret_type as k8s in Daemonset{{</info>}}

    ```text
    pxctl secrets kvdb login
    ```
    ```
    Successful Login to Secrets Endpoint!
    ```

* Now issue the backup command

    Note that in this particular example,  since only one credential is configured, there is no need to specify the credentials on the command line

    ```text
    pxctl cloudsnap backup NewVol
    ```
    ```
    Cloudsnap backup started successfully with id: 3f4f0a67-e12a-4d35-81ad-985657757352
    ```

* Watch the status of the backup

    ```text
    pxctl cloudsnap status
    ```
    ```
    NAME					SOURCEVOLUME									STATE		NODE		BYTES-PROCESSED	TIME-ELAPSED	COMPLETED
    39f66859-14b1-4ce0-a4c0-c858e714689e	2e4d4b67-95d7-481e-aec5-14223ac55170/590114184663672482-951325819047337066-incr	Backup-Done	70.0.73.246	420044800	17.460186585s	Wed, 16 Jan 2019 22:27:30 UTC
    3f4f0a67-e12a-4d35-81ad-985657757352	2e4d4b67-95d7-481e-aec5-14223ac55170/56706279008755778-725134927222077463	Backup-Active	70.0.73.246	1247805440	10.525438874s
    ```

    You could also watch the status of single cloudsnap command through task-id that was returned on successful execution of the cloudsnap command.

    ```text
    pxctl cloudsnap status -n 3f4f0a67-e12a-4d35-81ad-985657757352
    ```
    ```
    NAME					SOURCEVOLUME									STATE		NODE		BYTES-PROCESSED	TIME-ELAPSED	COMPLETED
    3f4f0a67-e12a-4d35-81ad-985657757352	2e4d4b67-95d7-481e-aec5-14223ac55170/56706279008755778-725134927222077463	Backup-Active	70.0.73.246	1840250880	16.57831394s
    ```

    Once the volume is backed up to the cloud successfully, listing the remote cloudsnaps will display the backup that just completed.

* List the backups in cloud

    ```text
    pxctl cloudsnap list
    ```
    ```
    SOURCEVOLUME					SOURCEVOLUMEID			CLOUD-SNAP-ID										CREATED-TIME				TYPE		STATUS
    volume20190116214922				590114184663672482		2e4d4b67-95d7-481e-aec5-14223ac55170/590114184663672482-619248560586769719		Wed, 16 Jan 2019 21:51:53 UTC		Manual		Done
    volume20190116214922				590114184663672482		2e4d4b67-95d7-481e-aec5-14223ac55170/590114184663672482-951325819047337066-incr		Wed, 16 Jan 2019 22:27:13 UTC		Manual		Done
    NewVol						56706279008755778		2e4d4b67-95d7-481e-aec5-14223ac55170/56706279008755778-725134927222077463		Thu, 17 Jan 2019 00:03:59 UTC		Manual		Done
    ```

### Perform Cloud Backup of a group of volumes

Portworx 2.0.3 and higher supports backing up multiple volumes to cloud at the same consistency point.

```text
pxctl cloudsnap backup-group --help
```
```
Backup a group of snapshot for a given group id or labels to cloud

Usage:
  pxctl cloudsnap backup-group [flags]

Aliases:
  backup-group, bg

Flags:
      --full                Force a full backup
      --group string        group id
      --cred-id string      Cloud credentials ID to be used for the backup
      --label pairs         list of comma-separated name=value pairs
  -v, --volume_ids string   list of comma-separated volume IDs
  -h, --help                help for backup-group

```

#### Examples

In below example, we are taking a group cloud backup of volumes *vol1* and *vol2*.

```text
pxctl cloudsnap backup-group  -v vol1,vol2
```

```
Group Cloudsnap backup started successfully with groupID:a1c8ba67-90e1-4c58-acbe-8eaca61a02ae
```

You can use the groupID in the output above to check the status of the group cloud snapshot. The status will show status of each cloud snapshot in the group.

```text
pxctl cs status -n a1c8ba67-90e1-4c58-acbe-8eaca61a02ae
```
```
NAME                                    SOURCEVOLUME                                                                    STATE           NODE            BYTES-PROCESSED TIME-ELAPSED    COMPLETED
29bf533d-1469-4610-953e-bd24f945e6de    fb468067-d7aa-40ff-992d-8f40a9e51c9a/201412281295404839-463199598055620776-incr Backup-Done     192.168.56.92   0 B             1.627836177s    Fri, 08 Mar 2019 22:12:14 UTC
650e26f3-f7c9-42c5-b830-2601da6d5fff    fb468067-d7aa-40ff-992d-8f40a9e51c9a/592806372953104727-884041223239759095-incr Backup-Done     192.168.56.92   0 B             1.629703129s    Fri, 08 Mar 2019 22:12:14 UTC
```

You can also take a group cloud backup by selecting the volumes using their labels. In below example, we have 2 volumes with the label *app=mysql*.

```text
 pxctl volume list -l app=mysql
```
```
ID                      NAME    SIZE    HA      SHARED  ENCRYPTED       IO_PRIORITY     STATUS          SNAP-ENABLED
592806372953104727      vol1    1 GiB   1       no      no              LOW             up - detached   no
201412281295404839      vol2    1 GiB   1       no      no              LOW             up - detached   no
```

To take a group cloud backup,

```text
pxctl cloudsnap backup-group --label app=mysql
```
```
Group Cloudsnap backup started successfully with groupID:3b1de846-1078-40e6-ac1a-2e66ef3986d1
```

### Extent based cloudsnaps

{{<info>}}This feature is not available in versions prior to 2.0.{{</info>}}

With PX-Enterprise 2.0, Portworx has enhanced the way cloud backups are done. Now, users can resume interrupted backups or restores.

For example, if the node performing backups or restores restarts, the backup/restore will resume once that node becomes operational.

This feature is also available for cloud backups of aggregated volumes. Here are a few points to consider in this regard:

*   For aggregated volumes, aggregated parts are backed up/restored sequentially.

*   Each aggregated part is backed up/restored on one of the nodes where the replica of that aggregated part is provisioned.

*   If not enough nodes are available to create the required aggregation level, aggregated volumes are restored to a non-aggregated volume(i.e. `aggregation=1`).

### Restore from a Cloud Backup

Use `pxctl cloudsnap restore` to restore from a cloud backup.

Here is the command syntax.

```text
pxctl cloudsnap restore
```
```
NAME:
   pxctl cloudsnap restore - Restore volume to a cloud snapshot

USAGE:
   pxctl cloudsnap restore [command options] [arguments...]

OPTIONS:
   --snap value, -s value         Cloud-snap id
   --node value, -n value         Optional node ID for provisioning restore volume storage
   --cred-id value, --cr value    Cloud credentials ID to be used for the restore

```

This command is used to restore a successful backup from cloud. It requires the cloudsnap ID which can be used to restore and credentials for the cloud storage provider or the object storage. Restore happens on any node where storage can be provisioned. In this release restored volume will have a replication factor of 1. The restored volume can be updated to different replication factors using `pxctl volume ha-update` command.

The command usage is as follows.
```text
pxctl cloudsnap restore --snap cs30/669945798649540757-864783518531595119 --cred-id 82998914-5245-4739-a218-3b0b06160332
```
```
Cloudsnap restore started successfully on volume: 104172750626071399 with task name:59c4cfd5-4160-45db-b326-f37b327d9225
```

Upon successful start of the command it returns the volume id created to restore the cloud snap and the task-id which can used to get status.
If the command fails to succeed, it shows the failure reason.

The restored volume will not be attached or mounted automatically.

### List Cloud Backups

Use `pxctl cloudsnap list` to list the available backups.

`pxctl cloudsnap list` helps enumerate the list of available backups in the cloud. This command assumes that you have all the credentials setup properly. If the credentials are not setup, then the backups available in those clouds won't be listed by this command.

```text
pxctl cloudsnap list
```
```
SOURCEVOLUME					SOURCEVOLUMEID			CLOUD-SNAP-ID										CREATED-TIME				TYPE		STATUS
volume20190116214922				590114184663672482		2e4d4b67-95d7-481e-aec5-14223ac55170/590114184663672482-619248560586769719		Wed, 16 Jan 2019 21:51:53 UTC		Manual		Done
volume20190116214922				590114184663672482		2e4d4b67-95d7-481e-aec5-14223ac55170/590114184663672482-951325819047337066-incr		Wed, 16 Jan 2019 22:27:13 UTC		Manual		Done
NewVol						56706279008755778		2e4d4b67-95d7-481e-aec5-14223ac55170/56706279008755778-725134927222077463		Thu, 17 Jan 2019 00:03:59 UTC		Manual		Done
```

Choose one of them to restore

```text
pxctl cloudsnap restore -s 2e4d4b67-95d7-481e-aec5-14223ac55170/56706279008755778-725134927222077463
```
```
Cloudsnap restore started successfully on volume: 104172750626071399 with task name:59c4cfd5-4160-45db-b326-f37b327d9225
```

`pxctl cloudsnap status` gives the status of the restore processes as well.

```text
pxctl cloudsnap status
```
```
NAME					SOURCEVOLUME									STATE		NODE		BYTES-PROCESSED	TIME-ELAPSED	COMPLETED
3f4f0a67-e12a-4d35-81ad-985657757352	2e4d4b67-95d7-481e-aec5-14223ac55170/56706279008755778-725134927222077463	Backup-Done	70.0.73.246	11988570112	3m29.825766964s	Thu, 17 Jan 2019 00:07:29 UTC
39f66859-14b1-4ce0-a4c0-c858e714689e	2e4d4b67-95d7-481e-aec5-14223ac55170/590114184663672482-951325819047337066-incr	Backup-Done	70.0.73.246	420044800	17.460186585s	Wed, 16 Jan 2019 22:27:30 UTC
59c4cfd5-4160-45db-b326-f37b327d9225	2e4d4b67-95d7-481e-aec5-14223ac55170/212160250617983239-283838486341798860	Restore-Done	70.0.73.246	1079287808	3.174541219s	Thu, 17 Jan 2019 00:15:19 UTC
```

### Deleting a Cloud Backup

{{<info>}}This is only supported from PX version 1.4 onwards{{</info>}}

You can delete backups from the cloud using the `/opt/pwx/bin/pxctl cloudsnap delete` command. The command will mark a cloudsnap for deletion and a job will take care of deleting objects associated with these backups from the objectstore.

Only cloudsnaps which do not have any dependant cloudsnaps (ie incrementals) can be deleted. If there are dependant cloudsnaps then the command will throw an error with the list of cloudsnaps that need to be deleted first.

{{<info>}} Starting with PX version 1.7.8 and 2.1, Delete requests are queued and processed in the background. Since querying cloud to figure out dependent backups can take a while, user requests to delete the backups are added to a queue and immediate response is returned to the user. If a cloud backup could not be deleted because of other dependent backups, an alert is logged and this will be deleted when all other dependent backups are deleted by the user.{{</info>}}

An example to delete the backup `pqr9-cl1/538316104266867971-807625803401928868` is below:

```text
pxctl cloudsnap delete --snap pqr9-cl1/538316104266867971-807625803401928868
```
```
Cloudsnap deleted successfully
pxctl cloudsnap list
SOURCEVOLUME 	CLOUD-SNAP-ID					CREATED-TIME			STATUS
dvol		pqr9-cl1/520877607140844016-50466873928636534	Fri, 07 Apr 2017 20:22:43 UTC	Done
```

### Cloud Backup schedules

Cloud Backup schedules allow backups to be uploaded to cloud at periodic intervals of time. These schedules can be managed through `pxctl`

```text
pxctl cloudsnap schedules --help
```
```
Manage schedules for cloud-snaps

Usage:
  pxctl cloudsnap schedules [flags]
  pxctl cloudsnap schedules [command]

Aliases:
  schedules, sched

Available Commands:
  create       Create a cloud-snap schedule
  delete       Delete a cloud-snap schedule
  list         List the configured cloud-snap schedules

Flags:
  -h, --help   help for schedules

Global Flags:
  --ca string        path to root certificate for ssl usage
  --cert string      path to client certificate for ssl usage
  --color            output with color coding
  --config string    config file (default is $HOME/.pxctl.yaml)
  --context string   context name that overrides the current auth context
  -j, --json             output in json
  --key string       path to client key for ssl usage
  --raw              raw CLI output for instrumentation
  --ssl              ssl enabled for portworx

  Use "pxctl cloudsnap schedules [command] --help" for more information about a command.
```

### Creating a Cloud Backup Schedule

```text
pxctl cloudsnap schedules create  --help
```
```
Create a cloud-snap schedule

Usage:
  pxctl cloudsnap schedules create [flags]

  Aliases:
    create, c

Flags:
  -f, --full              Force scheduled backups to be full always
  -v, --volume string     Volume ID to set the cloud-snap schedule
  -p, --periodic string   Cloudsnap interval in minutes (default "0")
  --cred-id string    Cloud credentials ID to be used for the backup
  -x, --max uint          Maximum number of cloud snaps to maintain, default 7 (default 7)
  -d, --daily strings     Daily snapshot at specified hh:mm (UTC)
  -w, --weekly strings    Weekly snapshot at specified weekday@hh:mm (UTC)
  -m, --monthly strings   Monthly snapshot at specified day@hh:mm (UTC)
  -h, --help              help for create

Global Flags:
  --ca string        path to root certificate for ssl usage
  --cert string      path to client certificate for ssl usage
  --color            output with color coding
  --config string    config file (default is $HOME/.pxctl.yaml)
  --context string   context name that overrides the current auth context
  -j, --json             output in json
  --key string       path to client key for ssl usage
  --raw              raw CLI output for instrumentation
  --ssl              ssl enabled for portworx
```

The following example creates a daily schedule that retains maximum of 15 backups in the cloud. `--max` parameter indicates number of backups to retain in cloud. Most recent `--max` number of backups are retained and older backups are deleted periodically. Note that sometime while listing cloud backups you may see more than `--max` number of backups and this is due to incremental nature of backups. We may need to retain more than `--max` backups in order to allow `--max` backups to be restored at any given time.

```text
 pxctl cloudsnap schedules create testVol --daily 21:00 --max 15 --cred-id cc84ef11-6d94-4c20-b4b9-01615119a442
 ```
 ```
 Cloudsnap schedule created successfully
```

### Listing Cloud Backup Schedules
Currently configured backup schedules can be listed using following pxctl command.

```text
pxctl cloudsnap schedules list
```
```
UUID						VOLUMEID			MAX-BACKUPS		FULL		SCHEDULE(UTC)
078557a3-26c7-49b1-9822-34e6f816c2d1		648038464574631167		15			false		daily @21:00
```


### Deleting a Cloud Backup Schedule
Backup schedules can be deleted using following pxctl command.

```text
pxctl cloudsnap schedules  delete --uuid 078557a3-26c7-49b1-9822-34e6f816c2d1
```

```
Cloudsnap schedule deleted successfully
```

---
title: Release Notes
weight: 99
description: Notes on Portworx enterprise releases
keywords: portworx, release notes
series: reference
---

## 2.0.3.2

### Key Fixes

* PWX-8062 - PX cluster running on k8s does not report volumes metrics
* PWX-8136 - Disable kvproxy audit as it causes the etcd client to trigger unnecessary API requests
* PWX-8098 - PX fails to start after reboot on system with LVM drives and auto-configured journal device

### Errata

* PWX-8161 - If a LVM partition is added as journal device after node initialization, any subsequent system reboot will need the LVM partition to be made visible before starting PX. This can be done by running "partprobe"

## 2.0.3.1

### Key Fixes

* PWX-8063 - Startup issue with 4.19.0-0.bpo.2-cloud-amd64                                                                      
* PWX-8060 - Cloud backup restore fails with json unmarshall error                                                  
* PWX-7989 - Fix licensing issue which was leading to reducing the number of nodes allowed in the cluster. Differentiate between NEW and PRE-EXISTING node failing to join the cluster, and do not cleanup if PRE-EXISTING nodes were the ones causing the failures.                       
* PWX-7980 - Do not cleanup CloudDrives when the drives are initialized and have labels
* PWX-7968 - Cloudsnaps cleanup fails because of missing start timestamp in metadata                                                         
* PWX-7963 - Cloudsnap restore for xfs volumes has wrong fs type                                        
* PWX-7794 - LIC: Aggressive node-decomissions and "Cluster max capacity" error handling

## 2.0.3

### Key Features and Enhancements

* SharedV4 support for DC/OS
* SharedV4 volume encryption support
* Support DC/OS Zookeeper for discovery service when using internal kvdb in DC/OS configurations
* Volume Policy Management Support
* Support Azure Key Vault for Secret Store
* Fix Kubernetes CVE for RUNC

### Key Fixes

* PWX-5657 - Fix a corner case where increasing the replication factor of a volume can take much longer when there are multiple levels of volume clones
* PWX-5762 - Add support for Azure Key Vault
* PWX-6868 - Prometheus framework update  to add Portworx support
* PWX-7448 - Show proper error for incorrect pxctl commands
* PWX-7468 - node-wiper script to wipe the namespace created by k8s secrets
* PWX-7481 - Shared volume failed to detach with an error that the Volume is mounted while Volume was not mounted 
* PWX-7485 - Display appropriate message when cluster wide diags can not be collected
* PWX-7491 - Drive provisioning fixes for issues where extra drives were created than what was specified in the spec.
* PWX-7512 - Speed up portworx install in DC/OS clusters by installing in each node in parallel.
* PWX-7513 - In DC/OS, Portworx tasks should restart if they go in LOST state
* PWX-7516 - The portworx-prometheus framework version need to be corrected.
* PWX-7571 - CloudSnap : Restore fails sometimes with "failed to get metadata of the backup from cloud"
* PWX-7595 - Handle spurious storage pool Full/offline condition
* PWX-7596 - PX creates node labels for every PVs, causing prometheus federation scraping issues
* PWX-7604 - Anonymize the secrets for Key Management Systems 
* PWX-7605 - DCOS Portworx-prometheus pod replace does not work as expected
* PWX-7619 - Make KVDB urls optional
* PWX-7628 - Alertmanager does not run after installing PX 2.0.2
* PWX-7639 - DCOS portworx framework should install with default options from config.json.
* PWX-7650 - Portworx install errors w/ "tar: .: file changed as we read it"
* PWX-7656 - Shared v4 failover operation fails if management and data interface of px service is different
* PWX-7661 - [stork] Snapshot status not being updated for all cloudsnaps in groupsnapshot
* PWX-7686 - Enable Portworx install in AWS instances when auto journaling is enabled.
* PWX-7743 - Prevent PX install if only the journal disk is given in the install script and no data disks were given.
* PWX-7766 - When a groupsnapshot request times out, allow for the snapshot scheduler to retry in the next interval or ask the user to retry if it is a manual request
* PWX-7773 - runc CVE-2019-5736 fix #3169

## 2.0.2.3

### Key Fixes

* PWX-7919 - Geography updates loading etcd causing high cpu usage

## 2.0.2.2

### Key Fixes

* PWX-7664 - When a node running 1.7 with empty journal log gets upgraded to 2.0 and the upgraded node is restarted, the node doesn't fully restart on next boot

## 2.0.2.1

### Key Fixes

* PWX-7510 - Remove any secretes info from the diags
* PWX-7214 - licensing engine config update improvements

## 2.0.2

### Key Features and Enhancements

* PWX-7207 - Allow docker with selinux for newer Kubernetes versions
* PWX-7208 - Google Cloud KMS integration

### Key Fixes

* PWX-6770 - Restart docker apps using shared volumes on DCOS
* PWX-7006 - Cloud migration cancel didn't cancel all the volume migrations
* PWX-7007 - Add an alert when Cloud migration task is cancelled
* PWX-7179 - Pool io priority for KOPS io1 volume should be correctly displayed
* PWX-7199 - Enable capacity usage command for centos kernel >= 3.10.0-862
* PWX-7226 - DCOS Portworx: Manually updated values in /etc/pwx/config.json does not persists
* PWX-7267 - Hide unknown/non-handled licenses
* PWX-7271 - 'pxctl secrets gcloud list-secrets' shows unnessassary line in console output
* PWX-7280 - Logs getting flooded with "18 is not 14len(values)" after upgrading the kernel to 4.20.0-1
* PWX-7304 - Handle journal device "read-only" cases
* PWX-7348 - Handle journal device "offline" cases
* PWX-7364 - Px boot stuck at ns mount
* PWX-7366 - Portworx service restart issues including "missing mountpoint", or "cannot open file/directory"
* PWX-7407 - OCI Monitor: Initiates cordoning even when px.ko was not loaded
* PWX-7466 - K8S/Upgrade: Talisman does not support CRI/Containerd

## 2.0.1.1

### Key Fixes

* PWX-7431 - Strip the labels on a config map to fit 63 characters.
* PWX-7411 - PX does not come up after upgrade to 2.0.1, when auto-detecting network intf

## 2.0.1

### Key Fixes

* PWX-7159 - Persist kvdb backups outside of the host filesystem
* PWX-7225 - AMI based ASG install does not pick up user config
* PWX-7097 - `pxctl service kvdb` should display correct cluster status after nodes are decommissioned
* PWX-7124 - Volume migration fails when the volume has an attached snapshot policy
* PWX-7101 - Enable task ID based sorting for `pxctl cm` commands
* PWX-7121 - Creating a paired cluster results in core files in the destination cluster
* PWX-7110 - Delete paired cluster credentials when the cluster pair is deleted
* PWX-7031 - Cluster migration restore status does not reflect the cloudsnap status when cloudsnap has failed
* PWX-7090 - Core files generated when a node is decommisionned with replicas on the node
* PWX-7211 - Fix daemonset affinity in openshift specs
* PWX-6836 - Don't allow deletion of the PX configuration data when the PX services are still running in the system
* PWX-7134 - SSD/NVME drives are displayed as STORAGE_MEDIUM_MAGNETIC
* PWX-7089 - Intermittent failures in `pxctl cloudsnap list`
* PWX-6852 - If PX starts before Docker is started, the `SchedulerName` field in pxctl CLI shows as N/A
* PWX-7129 - Add an option to improve filesystem space utilization in case of SSD/NVMe drives
* PWX-7011 - Cluster pairing for cluster migratino fails when one of the nodes in the destination cluster is down 
* PWX-7120 - Cloudsnap restore failures cannot be viewed through `pxctl cloudsnap status`

## 2.0.0.1

### Key Fixes

* PWX-7131 - Fix an issue with some of the alerts IDs mismatching with the description as part of the upgrade 
  from 1.x versio to 2.0.
* PWX-7122 - Volume restores would occasionally fail when restoring from backups that were done with PX 1.x versions.

## 2.0.0

### Key Features

* PX-Motion - Migration of applications and data between clusters. Application migration is Kubernetes only.
* PX-Central - Single pane of glass for management, monitoring and metadata services across multiple 
   PX clusters on Kubernetes
* Lighthouse 2.0 - Supports PX-motion with connection to Kubernetes cluster for application and namespace migration. 
* Shared volumes (v4) for Kubernetes
* Support Cloudsnaps for Aggregated volumes
* ‘Extent’ based cloudsnaps - Restartable Cloudsnaps if a large volume cloudsnap gets interrupted 
* Support Journal device for Repl=1 volumes 
* PX-kvdb (etcd) supported internally with PX cluster deployment 
   
### Key Fixes

* PWX-6458: When decreasing HA of a volume, recover snapshot space unused.
* PWX-5686: Implement accounting and display of space utilized by snapshots and clones.
* PWX-6949: Decommissioned node getting listed from one node in the cluster and not from the other 
* PWX-6617: PDM: Dump the cloud drive keys when PX loses kvdb connectivity.
* PWX-5876: Volume should get detached when out of quorum or pool down.

### Errata 

* PWX-7011: Cluster pair creation failing, because of destination PX node is marked down
  Workaround: Restart the PX node and attempt the cluster pairing again

* PWX-7041: CloudSnap Backup Failed for Pause/Resume by PX Restart - All replicas are down

  Workaround: This is a variant of the previous errata. 
  For volume with replication factor set to 1, Cloudsnap backup does not resume after the node with replica goes down.

## 1.7.7

* PWX-7315 - Fix a corner case where increasing the replication factor of a volume can take much longer when there are multiple levels of volume clones

## 1.7.6

* PWX-7304 - PX keeps restarting, if journal device made read-only
* PWX-7348 - PX keeps restarting, VM reboot after journal device made “offline”
* PWX-7453 - cloudsnap cleanup didn't complete properly in cases where errors were encountered when tranmitting the diffs
* PWX-7481 - Shared volume mounts fail when clients connections abruptly lost and not cleaned up properly
* PWX-7600 - Volume mount status might be incorrectly displayed when the node where the volume is attached hits a storage full condition and replicas on that node are moved to a new node

## 1.7.5

* PWX-7364 Namespace stuck volume issue 
* PWX-7299 export pool_status as a stat for prometheus
* PWX-7267 LIC: Hide unknown/non-handled licenses
* PWX-7212 Cloudsnap-Restore: Increase restore verbose level for error cases
* PWX-7179 io1 volume added to KOPS cluster gets displayed as STORAGE_MEDIUM_MAGNETIC
* PWX-7033 Objectstore endpoint failover not happening

## 1.7.4

* PWX-7292 For all storage errors retry 3 times before making pool offline
* PWX-7291 Detect ssd based pools and mount with nossd if kernel version is less than 4.15
* PWX-7214 LIC: Goroutine leak at license watch re-supscription
* PWX-7143 LIC: Should hard-code "absolute maximums" into License evaluations
* PWX-7142 LIC: SuperMicro misinterpreted as VM [roblox]

## 1.7.3

* Provide a runtime option to enable more compact data out of flash media to avoid disk fragementation
* Fix an issue with NVMe/SSD disks being shown as Magnetic disks 

## 1.7.2

### Key Features and Enhancements
* Default queue depth for all volumes (new, coming from older release) set to 128
* Advanced runtime options for write amplification reduction

### Key Fixes
* PWX-6928: Store bucket name in cloudsnap object
* PWX-6904: Fix bucket name for cloudsnap ID while reporting status
* PWX-7071: Do not use GFP_ATOMIC allocation

## 1.7.1.1
* Fix to add/remove node labels in Kubernetes to indicate where volume replicas are placed

## 1.7.1
* Restart docker containers using shared volumes for DC/OS to enable automatic re-attach of the containers on PX upgrades
* Preserve Kubernetes agent node ids across agent restarts when kubernetes agents are running statelessly in
  auto-scaling based environments

## 1.7.0

### Key Features and Enhancements
* IBM Kubernetes Service (IKS) Support
* IBM Key Protect Support for Encrypted Volumes
* Containerd runtime Interface (CRI) support
* Automatic VM Datastore provisioning for CentOS ESXi VMs
* Tiered Snapshots for storing volume snapshots on only lower cost media
* Encryption support for shared volumes

### Key Fixes
* PWX-6616 - Fix shared volume mounts going readonly kubernetes in few corner cases
* PWX-6551 - px_volume_read_bytes and px_volume_written_bytes are not available in 1.6.2
* PWX-6479 - Debian 8: PX fails to come up if sharedv4 is enabled
* PWX-6560 - PVC creation fails with "Already exists" perpetually
* PWX-6527 - Clean up orphaned volume paths as PVC are attached and detached over a period of time
* PWX-6425 - Cloudnsap schedule option to do full backup always.
* PWX-6408 - Node alerts: Include hostname/IP in addition to node id
* PWX-5963 - Report volumes with no snapshots

## 1.6.1.4
This is a minor patch release with the following fixex/enhancements.

* PWX-6655 - Fix to allow storageless nodes to reuse their node ids in k8s
* PWX-6410 - Fix a bug where PX may detach unused loopback devices that are not owned by PX on restarts.
* PWX-6713 - Allow update of per volume queue depth

## 1.6.1.3
 This is a minor patch release with the following fixex/enhancements.

 * PWX-6697: Add support for automatic provisioning of disks on VMware virtual machines on non-Kubernetes clusters and Kubernetes clusters without vSphere Cloud Provider

## 1.6.1.2

This is a minor patch release with This is a minor patch release with the following fixes/enhancements.

* PWX-6567 - Provide a parameter to disable discards during volume create
* PWX-6559 - Provide ability to map services listening on port 9001 to another port

## 1.6.1.1

This is a minor patch release with fixes issues around volume unmounts as well as pending commands to docker.

* PWX-6494 - Fix rare spurious volume unmounts of attached volumes in case of Portworx service restart under heavy load
* PWX-6559 - Add a timeout for all commands to docker so they timeout if docker hangs or crashes.

### Key Fixes

* PWX-6494

## 1.6.1


### Key Features

* Per volume queue depth to ensure volume level quality of service
* Large discard sizes up to 10MB support faster file deletes. NOTE: You will need a px-fuse driver update to use
  this setting.  PX 1.6.1 will continue to work with old discard size of 1MB if no driver update was done. This is a
  backwards compatible change
* Enable option to always perform a full clone back up for Cloudsnap
* Reduce scheduled snapshot intervals to support snapping every 15 mins from the current limit of 1 hour


### Key Fixes

* Fix replica provisioning across availability zones for clusters running on DC/OS in a public cloud

## 1.6.0

### Key Features:

* OpenStorage SDK support. Link to [SDK](https://libopenstorage.github.io/w/)
* Dynamic VM datastore provisioning support Kubernetes in vSphere/ESX environment
* Pivotal Kubernetes Service (PKS) support with automated storage management for [PKS](/portworx-install-with-kubernetes/cloud/install-pks)

### Errata

* PWX-6198 - SDK Cloud backup and credentials services is still undergoing tests
* PWX-6159 - Intermittent detach volume error seen by when calling the SDK Detach call
* PWX-6056 - Expected error not found when using Stats on a non-existent volume


## 1.5.1

### Key Fixes:

* PWX-6115 - Consul integration fixes to reduce CPU utilization
* PWX-6049 - Improved detection and handling cloud instance store drives in AWS
* PWX-6197 - Fix issues with max drive per zone in GCP
* When a storagless node loses connectivity to the remaining nodes, it should bring itself down.
* PWX-6208 - Fix GCP provider issues for dynamic disk provisioning in GCP/GKE
* PWX-5815 - Enable running `pxctl` from oci-monitor PODs in k8s
* PWX-6295 - Fix LocalNode provisioning pattern when provisioning volumes with greater than 1 replication factor
* PWX-6277 - PX fails to run sharedv4 volume support for Fedora
* PWX-6268 - PX does not come up in Amazon Linux V2 AMIs
* PWX-6229 - PX does not initialize fully in a GKE multi-zone cluster during a fresh install


## 1.5.0

### Important note: Consul integration with 1.5.0 has a bug which results in PX querying a Consul Cluster too often for a non-existent key. We will be pushing out a 1.5.1 release with a fix by 08/31/2018

### Key Features:

* Eliminate private.json for stateless installs
* Handle consul leader failures when running with consul as the preferred k/v store
* When a node is offline for longer than user configured timeout, move the replicas in that node out to
  other nodes with free space
* Improvements to AWS Auto-scaling Group handling with KOPS
* Lighthouse Volume Analyzer View Support
* Enable volume resize for volumes that are not attached
* Periodic, light-weight pool rebalance for proactive capacity management

### Key Fixes

 * PWX-5800 - In AWS Autoscaling mode, PX nodes with no storage should always try to attach available drives on restart
 * PWX-5827 - Allow adding cloud drives using pxctl service drive add commands
 * PWX-5915 - Add PX-DO-NOT-DELETE prefix to all cloud drive names
 * PWX-6117 - Fix `pxctl cloudsnap s --local` command failing to execute
 * PWX-5919 - Improve node decommission handling for volumes that are not in quorum
 * PWX-5824 - Improve geo variable handling for kubernetes and DC/OS
 * PWX-5902 - Support SuSE CaaS platform
 * PWX-5815 - Enable diags collection via oci-monitor when shell access to the minions not allowed
 * PWX-5816 - Incorrect bucket names will force a full backup instead of incremental backup
 * PWX-5904 - Remove db_remote and random profiles from io_profile help
 * PWX-5821 - Fix panics seen zone and rack labels are supplied on volume create



## 1.4.2.2

This is a patch release that adds capability to switch from shared to sharedv4 one volume at a time. Please contact portworx support before switching the volume types.


## 1.4.2

Use http://install.portworx.com/1.4/ for K8S spec generation.

* PWX-5681 - PX service to handle journald restarts
* PWX-5814 - Fix automatic diag uploads
* PWX-5818 - Fix diag uploads via `pxctl service diags` when running under k8s environments

## 1.4.0

If you are on any of the 1.4 RC builds, you will need to do a fresh install. Please reach out to us at support@portworx.com or on the slack to help assess upgrade options from 1.4 RC builds.

All customers on 1.3.x release will be able to upgrade to 1.4

All customers on 1.2.x release will be able to upgrade to 1.4 but in a few specific cases might need a node reboot after the upgrade. Please reach out to support for help with an upgrade or if there are any questions if you are running 1.2.x in production.

### Notes

* The kubernetes spec generator for 1.4 can be accessed [here](http://install.portworx.com/1.4/)


### Key Features

* 3DSnaps - Ability to take [application-consistent](/portworx-install-with-kubernetes/storage-operations/create-snapshots)
  snapshots cluster wide (Available in 05/14 GA version)
  * Volume Group snapshots - Ability to take crash-consistent snapshots on group of volumes based on a user-defined label
* GCP/GKE automated disk management based on [disk templates](/portworx-install-with-kubernetes/cloud/gke)
* [Kubernetes per volume secret support](/portworx-install-with-kubernetes/storage-operations/create-pvcs/create-encrypted-pvcs) to enable
  volume encryption keys per Kubernetes PVC and using the Kubernetes secrets for key storage
* DC/OS vault integration - Use [Vault integrated with DC/OS](/install-with-other/dcos)
* Support Pool Resize - Available in Maintenance Mode only
* Container Storage Interface (CSI) [Tech Preview](/portworx-install-with-kubernetes/storage-operations/csi)
* Support port mapping used by PX from 9001-9015 to a custom port number range by passing the starting
  port number in [install arguments](/install-with-other/docker/standalone)
* Provide ability to do a [license transfer](/reference/knowledge-base/px-licensing) from one cluster to another cluster
* Add support for [cloudsnap deletes](/reference/cli/cloud-snaps#pxctl-cloudsnap-delete)

### Key Fixes:

* PWX-5360 - Handle disk partitions in node wipe command
* PWX-5351 - Reduce the `pxctl volume list` time taken when a large number of volumes are present
* PWX-5365 - Fix cases where cloudsnap progress appears stopped because of time sychronization
* PWX-5271 - Set default journal device size to 2GB
* PWX-5341 - Prune out trailing `/` in storage device name before using it
* PWX-5214 - Use device uuid when checking for valid mounts when using device mapper devices instead of the device names
* PWX-5242 - Provide facility to add metadata journal devices to an existing cluster
* PWX-5287 - Clean up px_env variables as well when using node wipe command
* PWX-5322 - Unmount shared volume on shared volume source mount only on PX restarts
* PWX-5319 - Use excl open for open device checks
* PWX-4897 - Allow more time for resync to complete before changing the replication status
* PWX-5295 - Fix a nil pointer access during cloudsnap credential delete
* PWX-5006 - Tune data written between successive syncs depending on ingress write speed
* PWX-5203 - Cancel any in-progress ha increase operations that are pending on the node if the node is decommission
* PWX-5138 - Add startup options for air-gapped deployments
* PWX-4816 - Check for and add lvm devices when handling -a option for device list
* PWX-4609 - Allow canceling of replcition increase operations for attached volumes
* PWX-4765 - Fix resource contention issues when running heavy load on multiple shared volumes on many nodes
* PWX-5039 - Fix PX OCI uninstall when shared volumes are in use
* PWX-5153 - In Rancher, automatically manage container volume mounts if one of the cluster node restarts

### 1.3.1.4 {#1314}

This is a minor update that improves degraded cluster performance when one or more nodes are down for a long time and brought back online that starts the resync process

### 1.3.1.2 {#1312}

This is a minor update to fix install issues with RHEL Atomic and other fixes.

* RHEL Atomic install fixes
* Clean up any existing diag files before running diags command again
* `pxctl upgrade` fixes to pull the latest image information from install.portworx.com
* improvements in attached device detection logic in some cloud environments

### 1.3.1.1 {#1311}

This is a minor update to the previous 1.3.1 release

* Fix to make node resync process yield better to application I/O when some of nodes are down for a longer period of time and brought back up thereby triggering the resync process.

### 1.3.1 {#131}

This is a patch release with shared volume performance and stability fixes

#### Key Fixes: {#key-fixes-1}

* Fix namespace client crashes when client list is generated when few client nodes are down.
* Allow read/write snapshots in k8s annotations
* Make adding and removing k8s node labels asynchronous to help with large number volume creations in parallel
* Fix PX crash when a snapshot is taken at the same time as node being marked down because of network failures
* Fix nodes option in docker inline volume create and supply nodes value as semicolon separated values

### 1.3.0.1 {#1301}

This is a patch update with the following fix

* PWX-5115 - Fix `nodes` option in [docker inline volume create](/install-with-other/docker/how-to/volume-plugin) and supply nodes value as semicolon separated values

### 1.3.0 {#130}

_**Upgrade Note 1**_: Upgrade to 1.3 requires a node restart in non-k8s environments. In k8s environments, the cluster does a rolling upgrade

_**Upgrade Note 2**_: Ensure all nodes in PX cluster are running 1.3 version before increasing replication factor for the volumes

_**Upgrade Note 3**_: Container information parsing code has been disabled and hence the PX-Lighthouse up to 1.1.7 version will not show the container information page. This feature will be back in future releases and with the new lighthouse

#### Feature updates and noteworthy changes {#feature-updates-and-noteworthy-changes}

* Volume create command additions to include volume clone command and integrate snap commands
* Improved snapshot workflows
  * Clones - full volume copy created from a snapshot
  * Changes to snapshot CLI.
  * Creating scheduled snapshots policies per volume
  * _**Important**_ From 1.3 onwards, all snapshots are readonly. If the user wishes to create a read/write snapshot, a volume clone can be created from the snapshot
* Improved resync performance when a node is down for a long time and restarted with accumulated data in the surviving nodes
* Improved performance for database workloads by separating transaction logs to a seperate journal device
* Added PX signature to drives so drives cannot be accidentally re-used even if the cluster has been deleted.
* Per volume cache attributes for shared volumes
* https support for API end-points
* Portworx Open-Storage scaling groups support for AWS ASG - Workflow improvements
  * Allow specifying input EBS volumes in the format “type=gp2,size=100”. \(this is documented\)
  * Instead of adding labels to EBS volumes, PX now stores all the information related to them in kvdb. All the EBS volumes it creates and attaches are listed in kvdb and this information is then used to find out EBS volumes being used by PX nodes
  * Added command `pxctl cloud list` to list all the drives created via ASG
* Integrated kvdb - Early Access - Limited Release for small clusters less than 10 nodes

#### New CLI Additions and changes to existing ones {#new-cli-additions-and-changes-to-existing-ones}

* Added `pxctl service node-wipe` to wipe PX metadata from a decommisioned node in the cluster
* Change `snap_interval` parameter to `periodic` in `pxctl volume` commands
* Add schduler information in `pxctl status` display
* Add info about cloud volumes CLI [k8s](/cloud-references/auto-disk-provisioning/gcp) , [others](/portworx-install-with-kubernetes/cloud/aws/aws-asg)
* `pxctl service add --journal -d <device>` to add journal device support

#### Issues addressed {#issues-addressed}

* PWX-4518 - Add a confirmation prompt for `pxctl volume delete` operations
* PWX-4655 - Improve “PX Cluster Not In Quorum” Message in `pxctl status` to give additional information.
* PWX-4504 - Show all the volumes present in the node in the CLI
* PWX-4475 - Parse io\_profile in inline volume spec
* PWX-4479 - Fix io\_priority versions when labeling cloudsnaps
* PWX-4378 - Add read/write latency stats to the volume statistics
* PWX-4923 - Add vol\_ prefix to read/write volume latency statistics
* PWX-4288 - Handle app container restarts attached to a shared volume if the mountpath was unmounted via unmount command
* PWX-4372 - Gracefully handle trial license expiry and PX cluster reinstall
* PWX-4544 - PX OCI install is unable to proceed with aquasec container installed
* PWX-4531 - Add OS Distribution and Kernel version display in `pxctl status`
* PWX-4547 - cloudsnap display catalog with volume name hits “runtime error: index out of range”
* PWX-4585 - handle kvdb server timeouts with improved retry mechanism
* PWX-4665 - Do not allow drive add to a pool if a rebalance operation is already in progress
* PWX-4691 - Do not allow snapshots on down nodes or if the node is maintenance mode
* PWX-4397 - Set the correct zone information for all replica-sets
* PWX-4375 - Add `pxctl upgrade` support for OCI containers
* PWX-4733 - Remove Swarm Node ID check dependencies for PX bring up
* PWX-4484 - Limit replication factor increases to a limit of three at a time within a cluster and one per node
* PWX-4090 - Reserve space in each pool to handle rebalance operations
* PWX-4544 - Handle ./aquasec file during OCI-Install so PX can be installed in environments with aquasec
* PWX-4497 - Enable minio to mount shared volumes
* PWX-4551 - Improve `pxctl volume inspect` to show pools on which volumes are allocated, replica nodes and replication add
* PWX-4884 - Prevent replication factor increases if all the nodes in the cluster are not running 1.3.0
* PWX-4504 - Show all the volumes present on a node in CLI with a `--node` option
* PWX-4824 - `pxctl volume inspect` doesn’t show replication set information properly when one ndoe is out of quorum
* PWX-4784 - Support SELinux in 4.12.x kernels and above by setting SELinux context correctly
* PWX-4812 - Handle Kernel upgrades correctly
* PWX-4814 - Synchronize snapshot operations per node
* PWX-4471 - Enhancements to OCI Mount propogation to automount relevant scheduler dirs
* PWX-4721 - When large number of volumes are cloudsnapped at the same time, PX container hits a panic
* PWX-4789 - Handle cloudsnaps errors when the schedule has been moved or deleted
* PWX-4709 - Support for adding CloudDrive \(EBS volume\) to an existing node in a cluster
* PWX-4777 - Fix issues with `pxctl volume inspect` on shared volumes hanging when a large number of volume inspects are done
* PWX-4525 - `pxctl status` shows invalid cluster summary in some nodes when performing an upgrade from 1.2 to 1.3
* PWX-3071 - Provide ability to force detach a remote mounted PX volume from a single node when node is down
* PWX-4772 - Handle storage full conditions more gracefully when the backing store for a px volume gets full
* PWX-4757 - Improve PX initialization during boot to handle out of quorum volumes gracefully.
* PWX-4747 - Improve simultaneous large number of volume creates and volume attach/detach in multiple nodes
* PWX-4467 - Fix hangs when successive volume inspects come to the same volume with cloudsnap in progress
* PWX-4420 - Fix race between POD delete and volume unmounts
* PWX-4206 - Under certain conditions, creating a snap using k8s PVC creates a new volume instead of snapshot
* PWX-4207 - Fix nil pointer dereferences when creating snapshots via k8s

#### Errata {#errata}

* PWX-3982 After putting a node into maintenance mode, adding drives, and then running “pxctl service m –exit”, the message “Maintenance operation is in progress, cancel operation or wait for completion” doesn’t specify which operation hasn’t completed. Workaround: Use pxctl to query the status of all three drive operations \(add, replace, rebalance\). pxctl then reports which drive operations are in progress and allows exiting from maintenance mode if all maintenance operations are completed.
* PWX-4016 When running under Kubernetes, adding a node label for a scheduled cloudsnap fails with the error “Failed to update k8s node”. A node label isn’t needed for cloudsnaps because they are read-only and used only for backup to the cloud.
* PWX-4021 In case of a failure while a read-only snapshot create operation is in progress, Portworx might fail to come back up. This can happen if the failure coincides with snapshot creation’s file system freeze step, which is required to fence incoming IOs during the operation. To recover from this issue, reboot the node.
* PWX-4027 Canceling a service drive replace operation fails with the message “Replace cancel failed - Not in progress”. However, if you try to exit maintenance mode, the status message indicates that a maintenance operation is in progress. Workaround: Wait for the drive replace operation to finish. The replace operation might be in a state where it can’t be canceled. Cancel operations are performed when possible.
* PWX-4039 When running Ubuntu on Azure, an XFS volume format fails. Do not use XFS volumes when running Ubuntu on Azure.
* PWX-4043 When a Portworx POD gets deleted in Kubernetes, no alerts are generated to indicate the POD deletion via kubectl.
* PWX-4050 For a Portworx cluster that’s about 100 nodes or greater: If the entire cluster goes down with all the nodes off line, as nodes come on line a few nodes get restarted because they are marked offline. A short while after, the system converges and the entire cluster becomes operational. No user intervention required.
* Key Management with AWS KMS doesn’t work anymore because of API changes on the AWS side. Will be fixed in an upcoming release. Refer to this link for additional details. https://github.com/aws/aws-cli/issues/1043
* When shared volumes are configured with io\_profile=cms, it results in the px-ns process restarting occasionally.

### 1.2.23.0 {#12230}

This is a minor update that fixes an panic seen in some k8s environments when the user upgraded from a older version of PX to 1.2.22

PWX-5107 - Check if node spec is present before adding the node for volume state change events

### 1.2.22.0 {#12220}

* Support SELinux enable in kernels 4.12.x and above
* Support automatic kernel upgrades. If you expect your environment to upgrade kernels automatically, Portworx recommends to uprade to 1.2.22.0

### 1.2.20.0 {#12200}

* Minor update to enhance write performance for remote moounts with shared volumes
* 4.15.3 Linux kernel support

#### Errata \(Errata remains the same from 1.2.11.0 release\) {#errata-errata-remains-the-same-from-12110-release}

* PWX-3982 After putting a node into maintenance mode, adding drives, and then running “pxctl service m –exit”, the message “Maintenance operation is in progress, cancel operation or wait for completion” doesn’t specify which operation hasn’t completed. Workaround: Use pxctl to query the status of all three drive operations \(add, replace, rebalance\). pxctl then reports which drive operations are in progress and allows exiting from maintenance mode if all maintenance operations are completed.
* PWX-4014 The pxctl cloudsnap schedule command creates multiple backups for the scheduled time. This issue has no functional impact and will be resolved in the upcoming release.
* PWX-4016 When running under Kubernetes, adding a node label for a scheduled cloudsnap fails with the error “Failed to update k8s node”. A node label isn’t needed for cloudsnaps because they are read-only and used only for backup to the cloud.
* PWX-4017 An incremental cloudsnap backup command fails with the message “Failed to open snap for backup”. Logs indicates that the backup wasn’t found on at least on one of the nodes where the volume was provisioned. Workaround: Trigger another backup manually on the nodes that failed.
* PWX-4021 In case of a failure while a read-only snapshot create operation is in progress, Portworx might fail to come back up. This can happen if the failure coincides with snapshot creation’s file system freeze step, which is required to fence incoming IOs during the operation. To recover from this issue, reboot the node.
* PWX-4027 Canceling a service drive replace operation fails with the message “Replace cancel failed - Not in progress”. However, if you try to exit maintenance mode, the status message indicates that a maintenance operation is in progress. Workaround: Wait for the drive replace operation to finish. The replace operation might be in a state where it can’t be canceled. Cancel operations are performed when possible.
* PWX-4039 When running Ubuntu on Azure, an XFS volume format fails. Do not use XFS volumes when running Ubuntu on Azure.
* PWX-4043 When a Portworx POD gets deleted in Kubernetes, no alerts are generated to indicate the POD deletion via kubectl.
* PWX-4050 For a Portworx cluster that’s about 100 nodes or greater: If the entire cluster goes down with all the nodes off line, as nodes come on line a few nodes get restarted because they are marked offline. A short while after, the system converges and the entire cluster becomes operational. No user intervention required.
* Key Management with AWS KMS doesn’t work anymore because of API changes on the AWS side. Will be fixed in an upcoming release. Refer to this link for additional details. https://github.com/aws/aws-cli/issues/1043
* PWX-4721 - When cloud-snap is performed on large number of volumes, it results in a PX container restart. Workaround is to run cloudsnaps on up to 10 volumes concurrently.

### 1.2.18.0 {#12180}

#### Fixed issues {#fixed-issues}

* Improve file import and untar performance when shared volumes are used by Wordpress and tune for wordpress plugin behavior

#### Errata \(Errata remains the same from 1.2.11.0 release\) {#errata-errata-remains-the-same-from-12110-release-1}

* PWX-3982 After putting a node into maintenance mode, adding drives, and then running “pxctl service m –exit”, the message “Maintenance operation is in progress, cancel operation or wait for completion” doesn’t specify which operation hasn’t completed. Workaround: Use pxctl to query the status of all three drive operations \(add, replace, rebalance\). pxctl then reports which drive operations are in progress and allows exiting from maintenance mode if all maintenance operations are completed.
* PWX-4014 The pxctl cloudsnap schedule command creates multiple backups for the scheduled time. This issue has no functional impact and will be resolved in the upcoming release.
* PWX-4016 When running under Kubernetes, adding a node label for a scheduled cloudsnap fails with the error “Failed to update k8s node”. A node label isn’t needed for cloudsnaps because they are read-only and used only for backup to the cloud.
* PWX-4017 An incremental cloudsnap backup command fails with the message “Failed to open snap for backup”. Logs indicates that the backup wasn’t found on at least on one of the nodes where the volume was provisioned. Workaround: Trigger another backup manually on the nodes that failed.
* PWX-4021 In case of a failure while a read-only snapshot create operation is in progress, Portworx might fail to come back up. This can happen if the failure coincides with snapshot creation’s file system freeze step, which is required to fence incoming IOs during the operation. To recover from this issue, reboot the node.
* PWX-4027 Canceling a service drive replace operation fails with the message “Replace cancel failed - Not in progress”. However, if you try to exit maintenance mode, the status message indicates that a maintenance operation is in progress. Workaround: Wait for the drive replace operation to finish. The replace operation might be in a state where it can’t be canceled. Cancel operations are performed when possible.
* PWX-4039 When running Ubuntu on Azure, an XFS volume format fails. Do not use XFS volumes when running Ubuntu on Azure.
* PWX-4043 When a Portworx POD gets deleted in Kubernetes, no alerts are generated to indicate the POD deletion via kubectl.
* PWX-4050 For a Portworx cluster that’s about 100 nodes or greater: If the entire cluster goes down with all the nodes off line, as nodes come on line a few nodes get restarted because they are marked offline. A short while after, the system converges and the entire cluster becomes operational. No user intervention required.
* Key Management with AWS KMS doesn’t work anymore because of API changes on the AWS side. Will be fixed in an upcoming release. Refer to this link for additional details. https://github.com/aws/aws-cli/issues/1043

### 1.2.16.2 {#12162}

* This is a minor update that fixes volume size not updating whenever the content of the encrypted volume is deleted

### 1.2.16.1 {#12161}

This is a minor update which adds a new flag to limit or disable the generation of core files \(`-e PXCORESIZE=<size>`\). A value of 0 will disable cores

### 1.2.16.0 {#12160}

This is a minor update with performance enhancements for shared volumes to support large number of directories and files.

#### Fixed issues {#fixed-issues-1}

* Shared volume access latency improvements when managing filesystems with large number of directories and files

#### Errata \(Errata remains the same from 1.2.11.0 release\) {#errata-errata-remains-the-same-from-12110-release-2}

* PWX-3982 After putting a node into maintenance mode, adding drives, and then running “pxctl service m –exit”, the message “Maintenance operation is in progress, cancel operation or wait for completion” doesn’t specify which operation hasn’t completed. Workaround: Use pxctl to query the status of all three drive operations \(add, replace, rebalance\). pxctl then reports which drive operations are in progress and allows exiting from maintenance mode if all maintenance operations are completed.
* PWX-4014 The pxctl cloudsnap schedule command creates multiple backups for the scheduled time. This issue has no functional impact and will be resolved in the upcoming release.
* PWX-4016 When running under Kubernetes, adding a node label for a scheduled cloudsnap fails with the error “Failed to update k8s node”. A node label isn’t needed for cloudsnaps because they are read-only and used only for backup to the cloud.
* PWX-4017 An incremental cloudsnap backup command fails with the message “Failed to open snap for backup”. Logs indicates that the backup wasn’t found on at least on one of the nodes where the volume was provisioned. Workaround: Trigger another backup manually on the nodes that failed.
* PWX-4021 In case of a failure while a read-only snapshot create operation is in progress, Portworx might fail to come back up. This can happen if the failure coincides with snapshot creation’s file system freeze step, which is required to fence incoming IOs during the operation. To recover from this issue, reboot the node.
* PWX-4027 Canceling a service drive replace operation fails with the message “Replace cancel failed - Not in progress”. However, if you try to exit maintenance mode, the status message indicates that a maintenance operation is in progress. Workaround: Wait for the drive replace operation to finish. The replace operation might be in a state where it can’t be canceled. Cancel operations are performed when possible.
* PWX-4039 When running Ubuntu on Azure, an XFS volume format fails. Do not use XFS volumes when running Ubuntu on Azure.
* PWX-4043 When a Portworx POD gets deleted in Kubernetes, no alerts are generated to indicate the POD deletion via kubectl.
* PWX-4050 For a Portworx cluster that’s about 100 nodes or greater: If the entire cluster goes down with all the nodes off line, as nodes come on line a few nodes get restarted because they are marked offline. A short while after, the system converges and the entire cluster becomes operational. No user intervention required.

### 1.2.14.0 {#12140}

This is a minor update to support the older linux kernel versions \(4.4.0.x\) that ships with Ubuntu distributions

### 1.2.12.1 {#12121}

This is a minor update to support Openshift with SELinux enabled as well as verify with SPECTRE/Meltdown kernel patches

* Verified with the latest kernel patches for SPECTRE/Meltdown issue for all major Linux distros

### 1.2.12.0 {#12120}

This is a minor update to enhance meta data performance on a shared namespace volume.

#### Fixed issues {#fixed-issues-2}

* Readdir performance for directories with a large number of files \(greater 128K file count in a single directory\)
* PX running on AWS AutoScalingGroup now handles existing devices attached with names such as `/dev/xvdcw` which have an extra letter at the end.
* Occasionally, containers that use shared volumes could get a “transport end point disconnected” error when PX restarts. This has been resolved.
* Fixed an issue where Portworx failed to resolve Kubernetes services by their DNS names if user sets the Portworx DaemonSet DNS Policy as `ClusterFirstWithHostNet`.
* PWX- 4078 When PX runs in 100s of nodes, a few nodes show high memory usage.

### 1.2.11.10 {#121110}

This is a minor update to address an issue with installing a reboot service while upgrading a runC container.

#### Fixed issues {#fixed-issues-3}

* When upgrading a runC container, the new version will correctly install a reboot service. A reboot service \(systemd service\) is needed to reduce the wait time before a PX device returns with a timeout when the PX service is down. Without this reboot service, a node can take 10 minutes to reboot.

### 1.2.11.9 {#12119}

#### Fixed issues {#fixed-issues-4}

Pass volume name as part of the metrics end point so Prometheus/Grafana can display with volume name

* Add current ha level of the volume and io\_priority of the volumes to the metrics endpoint
* Abort all pending I/Os the the pxd device during a reboot so speed up reboots
* Move the px-ns internal port from 7000 to 9013
* Remove the unnecessary warning string “Data is not local to the node”
* Add px\_ prefix to all volume labels

#### Errata {#errata-1}

* Do not manually unmount a volume by using linux `umount` command for shared volume mounts. This errata applies to the previous versions of PX as well.

### 1.2.11.8 {#12118}

#### Fixed issues {#fixed-issues-5}

* Fix resync mechanism for read-only snapshots
* Improve log space utilization by removing old log files based on space usage

#### Errata {#errata-2}

* Do not manually unmount a volume by using linux `umount` command for shared volume mounts. This errata applies to the previous versions of PX as well.

### 1.2.11.7 {#12117}

#### Fixed issues {#fixed-issues-6}

* Suppress un-necessary log prints about cache flush
* PWX-4272 Handle remote host shutdowns gracefully for shared volumes. In the past this could leave stray TCP connections.

#### Errata {#errata-3}

* Do not manually unmount a volume by using linux `umount` command for shared volume mounts. This errata applies to the previous versions of PX as well.

### 1.2.11.6 Release notes {#12116-release-notes}

#### Fixed issues {#fixed-issues-7}

* Provide capability to drop system cache on-demand \(for a select workloads and large memory system\) and turn it off by default

### 1.2.11.5 Release notes {#12115-release-notes}

#### Key Features and Changes {#key-features-and-changes}

* PWX-4178 Perform snapshots in kubernetes via [annotations](/portworx-install-with-kubernetes/storage-operations/create-snapshots/snaps-annotations)

### 1.2.11.4 Release notes {#12114-release-notes}

#### Key Features and Changes {#key-features-and-changes-1}

* PX-Enterprise container is now available in [OCI Format](/install-with-other/docker/standalone)
* Enhancements for db workloads to handle slow media

#### Fixed issues {#fixed-issues-8}

* PWX-4224 Ignore `sticky` flag when purging old snapshots after a cloudsnap is completed.
* PWX-4220 `pxctl status` shows the first interface IP address instead of the mgmt. IP

### 1.2.11.3 Release notes {#12113-release-notes}

#### Fixed Issues {#fixed-issues-9}

* Shared volume performance improvements
* Do not take a a inline snap in k8s when no valid candidate pvcs are found

### 1.2.11.2 Release notes {#12112-release-notes}

#### Fixed Issues {#fixed-issues-10}

* Increase file descriptors to support large number of shared volumes

### 1.2.11.1 Release notes {#12111-release-notes}

#### Fixed Issues {#fixed-issues-11}

* Fix file descriptors not being released after reporting containers attached to a shared volume

### 1.2.11.0 Release notes {#12110-release-notes}

#### Key Features and Changes {#key-features-and-changes-2}

* You can now update volume labels. The pxctl volume update command has a new option, –label pairs. Specify a list of comma-separated name=value pairs. For example, if the current labels are x1=v1,x2=v2:

  The option “–labels x1=v4” results in the labels x1=v4,x2=v2.

  The option “–labels x1=” results in the labels x2=v2 \(removes a label\).

* Improvements to alerts:
  * Additional alerts indicate the cluster status in much more finer detail. This document has more details on all the alerts posted by PX: [Here](/install-with-other/operate-and-maintain/monitoring/alerting)
  * Rate limiting for alerts so that an alert isn’t repeatedly posted within a short timeframe.
* You can now update the io\_profile field by using the `pxctl volume update` command so the parameter can be enabled for existing volumes.

#### Fixed Issues {#fixed-issues-12}

* PWX-3146 Portworx module dependencies fail to load for openSUSE Leap 42.2, Kernel 4.4.57-18.3-default.
* PWX-3362 If a node is in maintenance mode because of disk errors, the node isn’t switched to a storage-less node. As a result, other resources on the node \(such as CPU and memory\) aren’t usable.
* PWX-3448 When Portworx statistics are exported, they include the volume ID instead of the volume name.
* PWX-3472 When snapshots are triggered on large number of volumes at the same time, the snap operation fails.
* PWX-3528 Volume create option parsing isn’t unified across Kubernetes, Docker, and pxctl.
* PWX-3544 Improvements to PX Diagnostics - REST API to retrieve and upload diagnostics for a node or cluster. Diagnostics run using the REST API includes vmstat output and the output of pxctl cluster list and pxctl -j volume list. The diagnostics also include netstat -s before the node went down.
* PWX-3558 px-storage dumps core while running an HA increase on multiple volumes during stress.
* PWX-3577 When Portworx is running in a container environment, it should allow mounts on only those directories which are bind mounted. Otherwise, Portworx hangs during a docker stop.
* PWX-3585 If Portworx stops before a container that’s using its volume stops, the container mght get stuck in the D state \(I/O in kernel\). As a result ‘systemctl stop docker’ takes 10 minutes as does system shutdown. The default PXD\_TIMEOUT to error out IOs is 10 minutes, but should be configurable.
* PWX-3591 Storage isn’t rebalanced after a drive add operation and before exiting maintenance mode.
* PWX-3600 Volume HA update operations on snapshots cannot be canceled.
* PWX-3602 Removing a node from a cluster fails with the message “Could not find any volumes that match ID\(s\)”.
* PWX-3606 Portworx metrics now include the following: Disk read and write latency stats, volume read and write latency stats, and per process stats for CPU and virtual/resident memory.
* PWX-3612 When creating or updating a volume, disallow ability to set both the “shared” and “scale” options.
* PWX-3614 A volume inspect returns the wrong error message when one node in the cluster is down: Could not find any volumes that match ID\(s\).
* PWX-3620 The volume inspect command doesn’t show the replication set status, such as whether the replication set has down members or is in a clean or resync state.
* PWX-3632 After a Kubernetes pod terminates and the Portworx volume unmount/cleanup fails, the kubelet logs include “Orphaned pod &lt;name&gt; found, but volume paths are still present on disk.”
* PWX-3648 After all nodes in a cluster go offline: If a node doesn’t restart when the other nodes restart, the other restarting nodes don’t mark that node as offline.
* PWX-3665 The Portworx live core collection hangs sometimes.
* PWX-3666 The pxctl service diags command doesn’t store all diagnostics for all nodes in the same lcoation. All diagnostics should appear in /var/cores.
* PWX-3672 The watch function stops after a large time change, such as 7 hours, on the cluster.
* PWX-3678 The pxctl volume update command interprets the -s option as -shared instead of -size and displays the message “invalid shared flag”.
* PWX-3700 Multiple alerts appear after a drive add succeeds.
* PWX-3701 The alert raised when a node enters maintenance mode specifes the node index instead of the node ID.
* PWX-3704 After backing up a volume that’s in maintenance mode to the cloud, restoring the volume to any online node fails.
* PWX-3709 High CPU usage occurs while detaching a volume with MySQL in Docker Swarm mode.
* PWX-3743 In the service alerts output in the CLI, the Description items aren’t aligned.
* PWX-3746 When a Portworx upgrade requires a node reboot, the message “Upgrade done” shouldn’t print.
* PWX-3747 When a node exits from maintenance mode, it doesn’t generate an alert.
* PWX-3764 The px-runc install command on a coreOS node fails to configure the PX OCI service and generates the error “invalid cross-device link”.
* PWX-3777 When running under Kubernetes, pods using a shared volume aren’t available after the volume becomes read-only.
* PWX-3778 After adding a drive to a storage-less node fails: A second attempt succeeds but there is no message that the drive add succeeded.
* PWX-3793 When running in Kubernetes, if an unmount fails for a shared volume with the error “volume not mounted”, the volume is stuck in a terminating state.
* PWX-3817 When running under Kubernetes, a WordPress pod is stuck in terminating for almost ten minutes.
* PWX-3820 When running Portworx as a Docker V2 plugin: After a service create –replicas command, a volume is mounted locally on a MySQL container instead of a Portworx container. The Swarm service fails with the error “404 Failed to locate volume: Cannot locate volume”. To avoid this issue, you can now specify the volume-driver with the service create command.
* PWX-3825 When a node is in a storage down state because the pool is out of capacity: A drive add fails with the error “Drive add start failed. drive size &lt;size&gt; too big” during an attempt to add the same size disk.
* PWX-3829 Container status in the Portworx Lighthouse GUI isn’t updated properly from Portworx nodes.
* PWX-3843 Portworx stats include metrics for utilized and available bytes, but not for total bytes \(px\_cluster\_disk\_total\_bytes\). As a result, alerts can’t be generated in Prometheus for storage utilization.
* PWX-3844 When you add a snapshot schedule to a volume, the alert type is “Snapshot Interval update failure” instead of “Snapshot interval update success”.
* PWX-3850 If the allocated io\_priority differs from the requested io\_priority, no associated alert is generated.
* PWX-3851 When two Postgres pods attempted to use the same volume, one of the Postgres pods mounted a local volume instead of a Portworx volume.
* PWX-3859 After adding a volume template to an Auto Scaling Group and Portworx adds tags to the volume: If you stop that cluster and then start a new cluster with the same volume, without removing the tags, a message indicates that the cluster is already initialized. The message should indicate that it failed to attach template volumes because the tag is already used. You can then manually remove the tags from the stopped cluster.
* PWX-3862 A volume is stuck in the detaching state indefinitely due to an issue in etcd.
* PWX-3867 When running under Kubernetes, a pod using namespace volumes generates the messages “Orphaned pod &lt;pod&gt; found, but volume paths are still present on disk”.
* PWX-3868 A PX cluster shows an extra node when running with ASG templates enabled if the AWS API returns an error when the PX container is booting up.
* PWX-3871 Added support for dot and hyphen in source and destination names in Kubernetes inline spec for snapshots.
* PWX-3873 When running under Kubernetes, a volume detach fails on a regular volume, with the message “Failed to detach volume: Failed with status -16”, and px-storage dumps core.
* PWX-3875 After volume unmount and mount commands are issued in a quick succession, sometimes the volume mount fails.
* PWX-3878 When running under Kubernetes, a Postgres pod gets stuck in a terminating state during when the POD gets deleted.
* PWX-3879 During volume creation on Kubernetes, node labels aren’t applied on Kubernetes nodes.
* PWX-3888 An HA increase doesn’t use the node value specified in the command if the node is from a different region.
* PWX-3895 The pxctl volume list command shows a volume but volume inspect cannot find it.
* PWX-3902 If a Portworx container is started with the API\_SERVER pointing to Lighthouse and etcd servers are also provided, the Portworx container doesn’t send statistics to Lighthouse.
* PWX-3906 Orphaned pod volume directories can remain in a Kubernetes cluster after an unmount.
* PWX-3912 During a container umount, namespace volumes might show the error “Device or resource busy”.
* PWX-3916 Portworx rack information isn’t updated when labels are applied to a Kubernetes node.
* PWX-3933 The size of a volume created by using a REST API call isn’t rounded to the 4K boundary.
* PWX-3935 Lighthouse doesn’t show container information when Portworx is run as a Docker V2 plugin.
* PWX-3936 A volume create doesn’t ignore storage-less nodes in a cluster and thus fails, because it doesn’t allocate the storage to available nodes.
* PWX-3946 On a node where a cloudsnap schedule is configured: If the node gets decommissioned, the schedule isn’t configured for the new replica set.
* PWX-3947 Simultaneous mount and unmount likely causes a race in teardown and setup.
* PWX-3968 If Portworx can’t find a volume template in an Auto Scaling Group, it dumps core and keeps restarting.
* PWX-3971 Portworx doesn’t install on an Azure Ubuntu 14 Distro with 3.13.0-32-generic kernel.
* PWX-3972 When you start a multi-node, multi-zone Auto Scaling Group with a max-count specified, Portworx doesn’t start on all nodes.
* PWX-3974 When running under Kubernetes, a WordPress app writes data to the local filesystem after a shared volume remount failure \(due to RPC timeout errors\) during node start.
* PWX-3997 When running under Kubernetes, deleting Wordpress pods results in orphaned directories.
* PWX-4000 A drive add or replace fails when Portworx is in a storage full/pool offline state.
* PWX-4012 When using shared volumes: During a WordPress plugin installation, the WordPress pod prompts for FTP site permissions. Portworx now passes the correct GID and UUID to WordPress.
* PWX-4049 Adding and removing Kubernetes node labels can fail during node updates.
* PWX-4051 Previous versions of Portworx logged too many “Etcd did not return any transaction responses” messages. That error is now rate-limited to log only a few times.
* PWX-4083 When volume is in a down state due to a create failure, but is still attached without a shared volume export, the detach fails with the error “Mountpath is not mounted”.
* PWX-4085 When running under Kubernetes, too many instances of this message get generated: “Kubernetes node watch channel closed. Restarting the watch..”
* PWX-4131 Specifying -a or -A for providing disks to PX needs to handle mpath & raid drives/partitions as well

  **Errata**

* PWX-3982 After putting a node into maintenance mode, adding drives, and then running “pxctl service m –exit”, the message “Maintenance operation is in progress, cancel operation or wait for completion” doesn’t specify which operation hasn’t completed. Workaround: Use pxctl to query the status of all three drive operations \(add, replace, rebalance\). pxctl then reports which drive operations are in progress and allows exiting from maintenance mode if all maintenance operations are completed.
* PWX-4014 The pxctl cloudsnap schedule command creates multiple backups for the scheduled time. This issue has no functional impact and will be resolved in the upcoming release.
* PWX-4016 When running under Kubernetes, adding a node label for a scheduled cloudsnap fails with the error “Failed to update k8s node”. A node label isn’t needed for cloudsnaps because they are read-only and used only for backup to the cloud.
* PWX-4017 An incremental cloudsnap backup command fails with the message “Failed to open snap for backup”. Logs indicates that the backup wasn’t found on at least on one of the nodes where the volume was provisioned. Workaround: Trigger another backup manually on the nodes that failed.
* PWX-4021 In case of a failure while a read-only snapshot create operation is in progress, Portworx might fail to come back up. This can happen if the failure coincides with snapshot creation’s file system freeze step, which is required to fence incoming IOs during the operation. To recover from this issue, reboot the node.
* PWX-4027 Canceling a service drive replace operation fails with the message “Replace cancel failed - Not in progress”. However, if you try to exit maintenance mode, the status message indicates that a maintenance operation is in progress. Workaround: Wait for the drive replace operation to finish. The replace operation might be in a state where it can’t be canceled. Cancel operations are performed when possible.
* PWX-4039 When running Ubuntu on Azure, an XFS volume format fails. Do not use XFS volumes when running Ubuntu on Azure.
* PWX-4043 When a Portworx POD gets deleted in Kubernetes, no alerts are generated to indicate the POD deletion via kubectl.
* PWX-4050 For a Portworx cluster that’s about 100 nodes or greater: If the entire cluster goes down with all the nodes off line, as nodes come on line a few nodes get restarted because they are marked offline. A short while after, the system converges and the entire cluster becomes operational. No user intervention required.

### 1.2.10.2 Release notes {#12102-release-notes}

#### Key Features {#key-features-1}

None

#### Key Changes and Issues Addressed {#key-changes-and-issues-addressed}

* Fix boot issues with Amazon linux
* Fix issues with shared volume mount and unmount with multiple containers with kubernetes

### 1.2.10 Release notes {#1210-release-notes}

#### Key Features {#key-features-2}

None

#### Key Changes and Issues Addressed {#key-changes-and-issues-addressed-1}

* Fix issue when a node running PX goes down, it never gets marked down in the kvdb by other nodes.
* Fix issue when a container in Lighthouse UI always shows as running even after it has exited
* Auto re-attach containers mounting shared volumes when PX container is restarted.
* Add Linux immutable \(CAP\_LINUX\_IMMUTABLE\) when PX is running as Docker V2 Plugin
* Set autocache parameter for shared volumes
* On volume mount, make the path read-only if an unmount comes in if the POD gets deleted or PX is restarted during POD creation. On unmount, the delete the mount path.
* Remove the volume quorum check during volume mounts so the mount can be retried until the quorum is achieved
* Allow snapshot volume source to be provided as another PX volume ID and Snapshot ID
* Allow inline snapshot creation in Portworx Kubernetes volume driver using the Portworx Kubernetes volume spec
* Post log messages indicating when logging URL is changed
* Handle volume delete requests gracefully when PX container is starting up
* Handle service account access when PX is running as a container instead of a daemonset when running under kubernetes
* Implement a global lock for kubernetes filter such that all cluster-wide Kubernetes filter operations are coordinated through the lock
* Improvements in unmount/detach handling in kubernetes to handle different POD clean up behaviors for deployments and statefulsets

#### Errata {#errata-5}

* If two containers using the same shared volume are run in the same node using docker, when one container exits, the container’s connection to the volume will get disrupted as well. Workaround is to run containers using shared volume in two different portworx nodes

### 1.2.9 Release notes {#129-release-notes}

{{<info>}}
**Important:**  
If you are upgrading from an older version of PX \(1.2.8 or older\) and have PX volumes in attached state, you will need node reboot after upgrade in order for the new version to take effect properly.
{{</info>}}

#### Key Features {#key-features-3}

* Provide ability to cancel a replication add or HA increase operation
* Automatically decommision a storageless node in the cluster if it has been offline for longer than 48 hours
* [Kubernetes snapshots driver for PX-Enterprise](/portworx-install-with-kubernetes/storage-operations/create-snapshots)
* Improve Kubernetes mount/unmount handling with POD failovers and moves

#### Key Issues Addressed {#key-issues-addressed}

* Correct mountpath retrieval for encrypted volumes
* Fix cleanup path maintenance mode exit issue and clean up alerts
* Fix S3 provider for compatibility issues with legacy object storage providers not supporting ListObjectsV2 API correctly.
* Add more cloudsnap related alerts to indicate cloudsnap status and any cloudsnap operation failures.
* Fix config.json for Docker Plugin installs
* Read topology parameters on PX restart so RACK topology information is read correctly on restarts
* Retain environment variables when PX is upgraded via `pxctl upgrade` command
* Improve handling for encrypted scale volumes

#### Errata {#errata-6}

* When PX-Enterprise is run on a large number of nodes, there is potential memory leak and a few nodes show high memory usage. This issue is resolved in 1.2.12.0 onwards. Workaround is to restart the PX-Enterprise container

### 1.2.8 Release notes {#128-release-notes}

#### Key Features {#key-features-4}

* License Tiers for PX-Enterprise

#### Key Issues Addressed {#key-issues-addressed-1}

NONE

### 1.2.5 Release notes {#125-release-notes}

#### Key Features {#key-features-5}

* Increase volume limit to 16K volumes

#### Key Issues Addressed {#key-issues-addressed-2}

* Fix issues with volume CLI hitting a panic when used the underlying devices are from LVM devices
* Fix px bootstrap issues with pre-existing snapshot schedules
* Remove alerts posted when volumes are mounted and unmounted
* Remove duplicate updates to kvdb

### 1.2.4 Release notes {#124-release-notes}

#### Key Features {#key-features-6}

* Support for –racks and –zones option when creating replicated volumes
* Improved replication node add speeds
* Node labels and scheduler convergence for docker swarm
* Linux Kernel 4.11 support
* Unique Cluster-specifc bucket for each cluster for cloudsnaps
* Load balanced cloudsnap backups for replicated PX volumes
* One-time backup schedules for Cloudsnap
* Removed the requirement to have /etc/pwx/kubernetes.yaml in all k8s nodes

#### Key Issues Addressed {#key-issues-addressed-3}

* `pxctl cloudsnap credentials` command has been moved under `pxctl credentials`
* Docker inline volume creation support for setting volume aggregation level
* –nodes support for docker inline volume spec
* Volume attach issues after a node restart when container attaching to a volume failed
* PX Alert display issues in Prometheus
* Cloudsnap scheduler display issues where the existing schedules were not seen by some users.
* Removed snapshots from being counted into to total volume count
* Removed non-px related metrics being pushed to Prometheus
* Added CLI feedback and success/failure alerts for `pxctl volume update` command
* Fixed issues with Cloudsnap backup status updates for container restarts

### 1.2.3 Release notes {#123-release-notes}

#### Key Features {#key-features-7}

No new features in 1.2.3. This is a patch release.

#### Key Issues Addressed {#key-issues-addressed-4}

* Performance improvements for database workloads

### 1.2.2 Release notes {#122-release-notes}

#### Key Features {#key-features-8}

No new features in 1.2.2. This is a patch release.

#### Key Issues Addressed {#key-issues-addressed-5}

* Fix device detection in AWS autenticated instances

### 1.2.1 Release notes {#121-release-notes}

#### Key Features {#key-features-9}

No new features in 1.2.1. This is a patch release.

#### Key Issues Addressed {#key-issues-addressed-6}

* Fix issues with pod failovers with encrypted volumes
* Improve performance with remote volume mounts
* Add compatbility for Linux 4.10+ kernels

### 1.2 Release notes {#12-release-notes}

#### Key Features {#key-features-10}

* [AWS Auto-scaling integration with Portworx](/portworx-install-with-kubernetes/cloud/aws/aws-asg) managing EBS volumes for EC2 instances in AWS ASG
* [Multi-cloud Backup and Restore](/reference/cli/cloud-snaps) of Portworx Volumes
* [Encrypted Volumes](/reference/cli/encrypted-volumes) with Data-at-rest and Data-in-flight encryption
* Docker V2 Plugin Support
* [Prometheus Integeration](/install-with-other/operate-and-maintain/monitoring/prometheus)
* [Hashicorp Vault](/key-management/vault), [AWS KMS integration](/portworx-install-with-kubernetes/cloud/aws) and Docker Secrets Integration
* [Dynamically resize](/reference/cli/updating-volumes) PX Volumes with no application downtime
* Security updates improve PX container security

#### Key Issues Addressed {#key-issues-addressed-7}

* Issues with volume auto-attach
* Improved network diagnostics on PX container start
* Added an alert when volume state transitions to read-only due to loss of quorum
* Display multiple attached hosts on shared volumes
* Improve shared volume container attach when volume is in resync state
* Allow pxctl to run as normal user
* Improved pxctl help text for commands like pxctl service

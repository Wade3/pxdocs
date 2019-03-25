---
title: Basics Operations Using pxctl
description: General reference for CLI, Volumes and other resources.
keywords: portworx, containers, storage, volumes, CLI
weight: 1
linkTitle: Basics
---

In this document, we are going to explore the basic operations available through the Portworx command-line tool- `pxctl`.

By default, the CLI displays the information in human readable form. Here is a simple example:

```text
pxctl status
```

```
Status: PX is operational
Node ID: ef6e125d-2137-4048-9c69-0a7067605fae
	IP: 70.0.29.70
 	Local Storage Pool: 2 pools
	POOL	IO_PRIORITY	RAID_LEVEL	USABLE	USED	STATUS	ZONE	REGION
	0	HIGH		raid0		32 GiB	3.7 GiB	Online	default	default
	1	HIGH		raid0		381 GiB	12 GiB	Online	default	default
<Output truncated>
```
In addition, every command takes in a `-j` which converts the output to machine parsable `JSON` format. You can do something like the following to save the information from above in `JSON` format:

```text
pxctl status -j > status.json
```

In most production deployments, you will provision volumes directly using _Docker_ or your scheduler \(such as a _Kubernetes_ pod spec\). However, `pxctl` also lets you directly provision and manage storage. In addition, `pxctl` has a rich set of cluster-wide management features which are explained in this document.

All operations available through `pxctl` are reflected back into the containers that use Portworx storage. In addition to what is exposed in Docker volumes, `pxctl`:

*   Gives access to Portworx storage-specific features, such as cloning a running container’s storage.
*   Shows the connection between containers and their storage volumes.
*   Lets you control the Portworx storage cluster, such as adding nodes to the cluster. \(The Portworx tools refer to servers managed by Portworx storage as _nodes_.\)

The scope of the `pxctl` command is global to the cluster. Running `pxctl` on any node within the cluster, therefore, shows the same global details. But `pxctl` also identifies details specific to that node.

The current release of `pxctl` is located in the `/opt/pwx/bin/` directory of every **worker node** and requires that you run as it as a privileged user. To run `pxctl` without typing the full directory path each time, add `pxctl` to your PATH as follows:

```text
sudo su
export PATH=/opt/pwx/bin:$PATH
```

Let's look at some simple commands.

#### Version {#version}

Here's how to find out the current version:

```text
sudo /opt/pwx/bin/pxctl -v
```

```
pxctl version 2.2.0-555ffff
```

#### Help {#help}

To learn more about the available commands, type `pxctl help`:

```text
sudo /opt/pwx/bin/pxctl help
```

```
NAME:
   pxctl - px cli

USAGE:
   pxctl [global options] command [command options] [arguments...]

VERSION:
   1.2.0-75d0dbb

COMMANDS:
     status         Show status summary
     volume, v      Manage volumes
     snap, s        Manage volume snapshots
     cluster, c     Manage the cluster
     service, sv    Service mode utilities
     host           Attach volumes to the host
     secrets        Manage Secrets
     upgrade        Upgrade PX
     eula           Show license agreement
     cloudsnap, cs  Backup and restore snapshots to/from cloud
     objectstore    Manage the object store
     help, h        Shows a list of commands or help for one command

GLOBAL OPTIONS:
   --json, -j     output in json
   --color        output with color coding
   --raw, -r      raw CLI output for instrumentation
   --help, -h     show help
   --version, -v  print the version
```

{{<info>}}
As seen above, `pxctl` provides the capabilities to perform fine-grained control of the PX resources cluster-wide. Also, it lets the user manage volumes, snapshots, cluster resources, hosts in the cluster and software upgrade in the cluster.
{{</info>}}

#### Status {#status}

The status command gives a summary like node details, cluster members,  global storage capacity, etc.

The following example shows how the output looks like if the global capacity for the Docker containers is 128 GB.

```text
/opt/pwx/bin/pxctl status
```

```
Status: PX is operational
Node ID: 0a0f1f22-374c-4082-8040-5528686b42be
	IP: 172.31.50.10
 	Local Storage Pool: 2 pools
	POOL	IO_PRIORITY	SIZE	USED	STATUS	ZONE	REGION
	0	LOW		64 GiB	1.1 GiB	Online	b	us-east-1
	1	LOW		128 GiB	1.1 GiB	Online	b	us-east-1
	Local Storage Devices: 2 devices
	Device	Path		Media Type		Size		Last-Scan
	0:1	/dev/xvdf	STORAGE_MEDIUM_SSD	64 GiB		10 Dec 16 20:07 UTC
	1:1	/dev/xvdi	STORAGE_MEDIUM_SSD	128 GiB		10 Dec 16 20:07 UTC
	total			-			192 GiB
Cluster Summary
	Cluster ID: 55f8a8c6-3883-4797-8c34-0cfe783d9890
	IP		ID					Used	Capacity	Status
	172.31.50.10	0a0f1f22-374c-4082-8040-5528686b42be	2.2 GiB	192 GiB		Online (This node)
Global Storage Pool
	Total Used    	:  2.2 GiB
	Total Capacity	:  192 GiB
```

#### Upgrade related operations {#upgrade-related-operations}

`pxctl` provides access to several upgrade related operations. You can get details on how to use it and of the available flags by running:

```text
sudo /opt/pwx/bin/pxctl upgrade --help
```

```
Container Name or ID to upgrade is only required for Non-OCI upgrades.

Usage:
  pxctl upgrade [flags]

Flags:
  -l, --tag string   Specify a PX Docker image tag
  -h, --help         help for upgrade

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

##### Running pxctl upgrade {#running-pxctl-upgrade}

`pxctl upgrade` upgrades the PX version on a node. Let's suppose you want to upgrade PX to version _1.1.16_. If so, you would then type the following command:

```text
sudo /opt/pwx/bin/pxctl upgrade --tag 1.1.6 my-px-enterprise
```

```
Upgrading my-px-enterprise to version: portworx/px-enterprise:1.1.6
Downloading PX portworx/px-enterprise:1.1.6 layers...
<Output truncated>
```

{{<info>}}
**Note:**
The container name also needs to be specified.
{{</info>}}

It is recommended to upgrade the nodes in a **staggered manner**. This way, the quorum and the continuity of IOs will be maintained.

#### Login/Authentication {#loginauthentication}

You must make PX login to the secrets endpoint when using encrypted volumes and ACLs.

`pxctl secrets` can be used to configure authentication credentials and endpoints.
Currently, Vault, Amazon KMS, and KVDB are supported.

##### Vault example

Here's an example of configuring PX with Vault:

```text
sudo /opt/pwx/bin/pxctl secrets vault login --vault-address http://myvault.myorg.com --vault-token myvaulttoken
```

```
Successfully authenticated with Vault.
```

{{<info>}}
**Note:**
To install and configure Vault, peruse [this link](https://www.vaultproject.io/docs/install/index.html)
{{</info>}}

##### AWS KMS example

To configure PX with Amazon KMS, type the following command:

```text
sudo /opt/pwx/bin/pxctl secrets aws login
```

Then, you will be asked a few questions:

```
Enter AWS_ACCESS_KEY_ID [Hit Enter to ignore]: ***
Enter AWS_SECRET_ACCESS_KEY [Hit Enter to ignore]: ***
Enter AWS_SECRET_TOKEN_KEY [Hit Enter to ignore]: ***
Enter AWS_CMK [Hit Enter to ignore]: mykey
Enter AWS_REGION [Hit Enter to ignore]: us-east-1b
```

Finally, a success message will be displayed:

```
Successfully authenticated with AWS.
```

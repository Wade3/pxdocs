---
layout: page
title: "Etcd for Portworx"
keywords: etcd, portworx, maintenance, kvdb
description: Instructions on installing and configuring an external ETCD cluster for Portworx
linkTitle: Etcd for Portworx
series: kb
weight: 1
---

Portworx requires a key-value database such as etcd for configuring storage. A highly available clustered etcd with persistent storage is preferred.

This page list various approaches for installing an external ETCD cluster and provides recommendations on best practices.

{{<info>}}With Portworx 2.0 and above, you can use Internal KVDB during installation. In this mode, Portworx will create and manage an internal key-value store (kvdb) cluster.{{</info>}}

### Requirements

For production Portworx clusters we recommend the following configuration of an etcd cluster:

1. Etcd Version > 3.1.x
2. Minimum 3 nodes
3. Minimum 8G of memory dedicated to each etcd node.
4. Each Etcd node in the etcd cluster backed with storage disks (minimum 100GB)

More detailed set of hardware requirements as recommended by etcd can be found [here](https://coreos.com/etcd/docs/latest/op-guide/hardware.html#example-hardware-configurations)

### Setup

You can use one of the following methods to setup an etcd cluster

#### Setup ETCD cluster with static set of nodes

If you have 3 static nodes where you want to run etcd follow [this](/reference/knowledge-base/etcd-quick-setup) guide to setup systemd services for an etcd cluster.

#### Setup ETCD cluster using CoreOS documentation

Follow [this](https://coreos.com/etcd/docs/latest/op-guide/clustering.html) detailed step by step process provided by etcd to setup a brand new multi-node cluster.

#### Setup ETCD cluster using Ansible Playbook

Follow [this](https://github.com/portworx/px-docs/blob/gh-pages/etcd/ansible/index.md) ansible playbook to install a 3 node etcd cluster.


### Tuning Etcd

Etcd provides multiple knobs to fine tune the cluster based on your needs. We recommend fine tuning the following three settings.

#### Compaction

etcd keeps an exact history of its keyspace, this history should be periodically compacted to avoid performance degradation and eventual storage space exhaustion. Regular compaction ensures that the memory usage of the etcd process is under check.
The keyspace can be compacted automatically with etcd's time windowed history retention policy, or manually with ``etcd``

We recommend keeping history for last 3 hours. While setting up etcd you can specify the retention policy in the following way:

```text
etcd --auto-compaction-retention=3
```

#### Database Size (Space Quota)

The space quota in etcd ensures the cluster operates in a reliable fashion. Without a space quota, etcd may suffer from poor performance if the keyspace grows excessively large, or it may simply run out of storage space, leading to unpredictable cluster behavior.

We recommend setting the space quota to max value of 8Gi. While setting up etcd you can specify the space quota in the following way:

```text
etcd --quota-backend-bytes=$((8*1024*1024*1024))
```

#### Snapshot Policy

Etcd can take periodic snapshots of its keyspace which can be used to restore the etcd cluster in case of a complete disaster. By default etcd takes a snapshot after every 10,000 changes to its key value space. If you want the snapshot strategy to be more aggressive you can tune the frequency in the following way:

```text
etcd --snapshot-count=5000
```

For a more detailed setup, maintenance and tuning information refer the following coreos etcd reference docs.

* [Maintenance](https://coreos.com/etcd/docs/latest/op-guide/maintenance.html)
* [Tuning](https://coreos.com/etcd/docs/latest/tuning.html)
* [Troubleshooting](https://coreos.com/etcd/docs/latest/op-guide/recovery.html)
* [Consul](https://www.consul.io/intro/getting-started/join.html) 

### Securing with certificates in Kubernetes

SSL certificates for etcd can be stored as Kubernetes secrets. Three files are required - in this example, the CA certificate is `etcd-ca.crt`, the etcd certificate `etcd.crt` and the etcd key `etcd.key`. These files should be copied to a directory on the Kubernetes master (`etcd-secrets`). Next, create a secret from these files:

```text
kubectl -n kube-system create secret generic px-etcd-certs --from-file=etcd-secrets/
```
```
secret/px-etcd-certs created
```
```text
kubectl -n kube-system describe secret px-etcd-certs
```
```
Name:         px-etcd-certs
Namespace:    kube-system
Labels:       <none>
Annotations:  <none>

Type:  Opaque

Data
====
etcd-ca.crt:      1679 bytes
etcd.crt:  1680 bytes
etcd.key:  414  bytes
```

Use the [spec generator](https://install.portworx.com/), selecting "Certificate Auth" under the etcd section, ensuring the filenames match those specified.

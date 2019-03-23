---
title: Snapshot single PVCs
hidden: true
keywords: portworx, container, Kubernetes, storage, Docker, k8s, flexvol, pv, persistent disk, snapshots, stork, clones
description: Instructions on taking snapshots of single PVCs and restoring PVCs from the snapshots
series: k8s-local-snap
weight: 8
---

This document will show you how to create snapshot of a PVC backed by a Portworx volume.

## Creating snapshot within a single namespace

If you have a PVC called jenkins-home-jenkins-master-0, in the jenkins namespace, you can create a snapshot
for that PVC by using the following spec:

```text
apiVersion: volumesnapshot.external-storage.k8s.io/v1
kind: VolumeSnapshot
metadata:
  name: jenkins-home-jenkins-master-0
  namespace: jenkins
spec:
  persistentVolumeClaimName: jenkins-home-jenkins-master-0
```

Once you apply the above object you can check the status of the snapshots using `kubectl`:

```text
kubectl get -n jenkins volumesnapshot
```
```
NAME                                                      AGE
jenkins-jobs-jenkins-master-0-snapshot-2019-03-20-snap1   6m
```
```text
kubectl get -n jenkins volumesnapshotdatas
```
```

NAME                                                       AGE
k8s-volume-snapshot-ab059f02-4b5e-11e9-bca9-0242ac110002   8m
```

The creation of the volumesnapshotdatas object indicates that the snapshot has
been created. If you describe the volumesnapshotdatas object you can see the
Portworx Volume Snapshot ID and the PVC for which the snapshot was created.

```text
kubectl describe volumesnapshotdatas
```
```
Name:         k8s-volume-snapshot-ab059f02-4b5e-11e9-bca9-0242ac110002
Namespace:
Labels:       <none>
Annotations:  <none>
API Version:  volumesnapshot.external-storage.k8s.io/v1
Kind:         VolumeSnapshotData
Metadata:
  Creation Timestamp:  2019-03-20T22:22:37Z
  Generation:          1
  Resource Version:    56596513
  Self Link:           /apis/volumesnapshot.external-storage.k8s.io/v1/volumesnapshotdatas/k8s-volume-snapshot-ab059f02-4b5e-11e9-bca9-0242ac110002
  UID:                 ab07a5c9-4b5e-11e9-9693-0cc47ab5f9a2
Spec:
  Persistent Volume Ref:
    Kind:  PersistentVolume
    Name:  pvc-9b609a88-3f5e-11e8-83b6-0cc47ab5f9a2
  Portworx Volume:
    Snapshot Id:    411710013297550893
    Snapshot Type:  local
  Volume Snapshot Ref:
    Kind:  VolumeSnapshot
    Name:  jenkins/jenkins-jobs-jenkins-master-0-snapshot-2019-03-20-snap1-aa53d9a3-4b5e-11e9-9693-0cc47ab5f9a2
Status:
  Conditions:
    Last Transition Time:  2019-03-20T22:22:37Z
    Message:               Snapshot created successfully and it is ready
    Reason:
    Status:                True
    Type:                  Ready
  Creation Timestamp:      <nil>
Events:                    <none>
```

In addition, you can use storkctl to verify that the snapshot was created successfully:

```text
storkctl -n jenkins get snap
```
```
NAME                                                      PVC                             STATUS    CREATED               COMPLETED             TYPE
jenkins-jobs-jenkins-master-0-snapshot-2019-03-20-snap1   jenkins-jobs-jenkins-master-0   Ready     20 Mar 19 15:22 PDT   20 Mar 19 15:22 PDT   local
```

To create PVCs from existing snapshots, read [Creating PVCs from snapshots](/portworx-install-with-kubernetes/storage-operations/create-snapshots/snaps-local#pvc-from-snap).

## Creating snapshots across namespaces

* When creating snapshots, you can provide comma separated regexes with `stork/snapshot-restore-namespaces` annotation to specify which namespaces the snapshot can be restored to.
* When creating PVC from snapshots, if a snapshot exists in another namespace, the snapshot namespace should be specified with `stork/snapshot-source-namespace` annotation.

Let's take an example where we have 2 namespaces _dev_ and _prod_. We will create a PVC and snapshot in the _dev_ namespace and then create a PVC in the _prod_ namespace from the snapshot.

Step 1: Create the namespaces

```text
apiVersion: v1
kind: Namespace
metadata:
  name: dev
  labels:
    name: dev
---
apiVersion: v1
kind: Namespace
metadata:
  name: prod
  labels:
    name: prod
```

Step 2: Create the PVC

```text
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: mysql-data
  namespace: dev
  annotations:
    volume.beta.kubernetes.io/storage-class: px-mysql-sc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
---
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: px-mysql-sc
provisioner: kubernetes.io/portworx-volume
parameters:
  repl: "2"
```

Step 3: Create the snapshot

```text
apiVersion: volumesnapshot.external-storage.k8s.io/v1
kind: VolumeSnapshot
metadata:
  name: mysql-snapshot
  namespace: dev
  annotations:
    stork/snapshot-restore-namespaces: "prod"
spec:
  persistentVolumeClaimName: mysql-data

```

Step 4: Create a PVC in a different namespace from the snapshot

```text
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mysql-clone
  namespace: prod
  annotations:
    snapshot.alpha.kubernetes.io/snapshot: mysql-snapshot
    stork/snapshot-source-namespace: dev
spec:
  accessModes:
     - ReadWriteOnce
  storageClassName: stork-snapshot-sc
  resources:
    requests:
      storage: 2Gi
```

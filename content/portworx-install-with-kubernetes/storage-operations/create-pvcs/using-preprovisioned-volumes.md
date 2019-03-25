---
title: Using Pre-provisioned Volumes
weight: 10
keywords: portworx, pre-provisioned volumes, container, Kubernetes, storage, Docker, k8s, flexvol, pv, persistent disk, StatefulSets
description: Learn how to use a pre-provisioned Portworx volume in Kubernetes
series: k8s-vol
---

This document describes how to use a pre-provisioned volume in your Kubernetes cluster.

### Creating Portworx volume using pxctl {#creating-portworx-volume-using-pxctl}

First create a volume using Portworx CLI. On one of the nodes with Portworx installed run the following command:

```text
/opt/pwx/bin/pxctl volume create testvol --size 2
```

For more details on creating volumes using pxctl, [click here](/concepts).

Alternatively, you can also use snapshots that you created previously. To learn more, [click here](/reference/cli/snapshots/).

### Using the Portworx volume {#using-the-portworx-volume}

Once you have a Portworx volume, you can use it in 2 different ways:

#### 1. Using the Portworx volume directly in a pod {#1-using-the-portworx-volume-directly-in-a-pod}

You can create a pod that directly uses a Portworx volume as follows:

```text
apiVersion: v1
kind: Pod
metadata:
   name: nginx-px
spec:
   containers:
   - image: nginx
     name: nginx-px
     volumeMounts:
     - mountPath: /test-portworx-volume
       name: testvol
   volumes:
   - name: testvol
     # This Portworx volume must already exist.
     portworxVolume:
       volumeID: testvol
```

{{<info>}}The _name_ and _volumeID_ above must be the same and should be the name of the Portworx volume created using pxctl.{{</info>}}

#### 2. Using the Portworx volume by creating a PersistentVolume & PersistentVolumeClaim {#2-using-the-portworx-volume-by-creating-a-persistentvolume--persistentvolumeclaim}

**Creating PersistentVolume**

First create a `PersistentVolume` that references the Portworx volume. Following is an example spec.

```text
apiVersion: v1
kind: PersistentVolume
metadata:
  name: testvol
  labels:
    name: testvol
spec:
  capacity:
    storage: 2Gi
  accessModes:
    - ReadWriteOnce
  persistentVolumeReclaimPolicy: Retain
  portworxVolume:
    volumeID: testvol
```

Above `PersistentVolume` references an existing Portworx volume `testvol` \(Notice that metadata.name and spec.portworxVolume.volumeID must be volume-name-or-ID\) created using pxctl. Also note that it also has labels. We’ll soon see how they can be useful.

**Creating PersistentVolumeClaim**

Now create a `PersistentVolumeClaim` that will claim the above created volume. Following is an example spec.

```text
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: testvol-pvc
spec:
  selector:
    matchLabels:
      name: testvol
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 2Gi
```

Notice how we use a label selector to select the right `PersistentVolume` using it’s label.

{{<info>}}If you are planning to use the `PersistentVolumeClaim` in a pod in a non-default namespace, the `PersistentVolumeClaim` needs to created in that namespace.
{{</info>}}

**Creating a pod using the PersistentVolumeClaim**

Now you can create a pod that references the above `PersistentVolumeClaim`. Below is an example.

```text
apiVersion: v1
kind: Pod
metadata:
   name: nginx-px
spec:
  containers:
  - image: nginx
    name: nginx-px
    volumeMounts:
    - mountPath: /test-portworx-volume
      name: testvol
  volumes:
  - name: testvol
    persistentVolumeClaim:
      claimName: testvol-pvc
```

{{<info>}}To access PV/PVCs with a non-root user refer [here](/portworx-install-with-kubernetes/storage-operations/create-pvcs/access-via-non-root-users){{</info>}}

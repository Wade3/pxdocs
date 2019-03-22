---
title: GKE
logo: /logos/gke.png
weight: 1
keywords: portworx, container, Kubernetes, storage, Docker, k8s, pv, persistent disk, gke, gce
description: This page describes how to setup a production ready Portworx cluster in a Google Kubernetes Engine (GKE).
noicon: true
---

This topic explains how to install Portworx with Google Kubernetes Engine (GKE). Follow the steps in this topic in order.

## Create a GKE cluster {#create-a-gke-cluster}

{{<info>}} **Image type**: Portworx is supported on GKE cluster provisioned on [Ubuntu Node Images](https://cloud.google.com/kubernetes-engine/docs/node-images). So it is important to specify the node image as **Ubuntu** when creating clusters. {{</info>}}

{{<info>}}**Resource requirements**: Portworx requires that each node in the Kubernetes cluster has at least 4 CPUs and 4 GB memory for Portworx. It is important to keep this in mind when selecting the machine types during cluster creation.{{</info>}}

### Configure gcloud

If this is your first time running with Google Cloud, please follow this quickstart to install gcloud shell and configure your project and compute zone. If you already have gcloud setup, you can skip this.

```text
export PROJECT_NAME=<PUT-YOUR-PROJECT-NAME-HERE>
```


```text
gcloud config set project $PROJECT_NAME
gcloud config set compute/region us-east1
gcloud config set compute/zone us-east1-b
gcloud components update
```

### Create your GKE cluster using gcloud

You have 2 options in the type of cluster you create: Regional or Zonal. Read [Regional Clusters](https://cloud.google.com/kubernetes-engine/docs/concepts/regional-clusters) to help you make this decision

#### Create a zonal cluster

Below command creates a 3-node zonal cluster in us-east1-a with auto-scaling enabled.

```text
gcloud container clusters create px-demo \
    --zone us-east1-b \
    --disk-type=pd-ssd \
    --disk-size=50GB \
    --labels=portworx=gke \
    --machine-type=n1-highcpu-8 \
    --num-nodes=3 \
    --image-type ubuntu \
    --scopes compute-rw \
    --enable-autoscaling --max-nodes=6 --min-nodes=3
```

#### Create a regional cluster

Below command creates a 3-node regional in us-east1 cluster with auto-scaling enabled.

```text
gcloud container clusters create px-demo \
     --region us-east1 \
     --node-locations us-east1-b,us-east1-c,us-east1-d \
     --disk-type=pd-ssd \
     --disk-size=50GB \
     --labels=portworx=gke \
     --machine-type=n1-highcpu-8 \
     --num-nodes=3 \
     --image-type ubuntu \
     --scopes compute-rw \
     --enable-autoscaling --max-nodes=6 --min-nodes=3
```

### Set your default cluster

After the above GKE cluster completes, let’s make sure and set it up as our default cluster while using the gcloud.

```text
gcloud config set container/cluster px-demo
gcloud container clusters get-credentials px-demo
```

To make sure we open access to the Compute API, run the following command.

```text
gcloud services enable compute.googleapis.com
```

### Provide permissions to Portworx

Portworx requires a ClusterRoleBinding for your user to deploy the specs. You can do this using:

```text
kubectl create clusterrolebinding myname-cluster-admin-binding \
    --clusterrole=cluster-admin --user=`gcloud info --format='value(config.account)'`
```

## Install

{{<info>}} **Who provisions the storage?**

Portworx gets its storage capacity from block storage mounted in the nodes and aggregates capacity across all the nodes to create a global storage pool. In this tutorial, Portworx uses Persistent Disks (PD) as that block storage, where Portworx adds PD automatically as the Kubernetes scales-out and moves PD attachment as nodes exit the cluster or get replaced.{{</info>}}

Continue below to generate the Portworx specs.

{{% content "portworx-install-with-kubernetes/shared/1-generate-the-spec-footer.md" %}}

{{% content "portworx-install-with-kubernetes/shared/4-apply-the-spec.md" %}}

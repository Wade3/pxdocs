---
title: Portworx on Rancher 2.x
linkTitle: Rancher 2.x
keywords: portworx, PX-Developer, container, Rancher, storage
description: Instructions on installing Portworx using public catalog (Helm Chart) on Rancher 2.x 
weight: 6
series: px-rancher
noicon: true
---

This section covers information on installing Portworx using public catalog (Helm Chart) on Rancher 2.x.

## Step 1: Install Rancher

Follow the instructions for installing [Rancher 2.x](https://rancher.com/docs/rancher/v2.x/en/installation/).

## Step 2: Prerequisites - Choosing the right worker node flavor for your Rancher 2.x Kubernetes cluster for Portworx

Portworx is a highly available software-defined storage solution that you can use to manage persistent storage for your containerized databases or other stateful apps in your Rancher 2.x Kubernetes cluster. To make sure that your cluster is set up with the compute resources that are required for Portworx, review the FAQs in this step.

Portworx pre-requisites [here](/start-here-installation/#installation-prerequisites)

**NOTE:** </br>
 
{{<info>}}

* Currently `RancherOS` distro is not supported for Portworx. 

* Portworx requires that Rancher hosts have at least one non-root disk or partition to contribute.{{</info>}}

**How can I make sure that my data is stored highly available?** </br>
You need at least 3 worker nodes in your Portworx cluster so that Portworx can replicate your data across nodes. By replicating your data across worker nodes, Portworx can ensure that your stateful app can be rescheduled to a different worker node in case of a failure without losing data. 

## Step 3: Creating or preparing your cluster for Portworx

To install Portworx, you must have an Rancher Kubernetes cluster that runs Kubernetes version 1.10 or higher. To make sure that your cluster is set up with worker nodes that offer best performance for you Portworx cluster, review Step 1: Choosing the right worker node flavor for your Rancher cluster for Portworx.

Every Portworx cluster must be connected to a key-value store to store Portworx metadata. The Portworx key-value store serves as the single source of truth for your Portworx storage layer. If the key-value store is not available, then you cannot work with your Portworx cluster to access or store your data. Existing data is not changed or removed when the Portworx database is unavailable.

## Step 4: Installing Portworx on Rancher 2.x

Portworx provides a helm chart for Rancher 2.x that is available in the public catalog. The Helm chart deploys a trial version of the Portworx enterprise edition `px-enterprise` that you can use for 30 days. After the trial version expires, you must [purchase a Portworx license](/reference/knowledge-base/px-licensing/) to continue to use your Portworx cluster. In addition, [Stork](https://docs.portworx.com/portworx-install-with-kubernetes/) is installed on your Kubernetes cluster. Stork is the Portworx storage scheduler and allows you to co-locate pods with their data, and create and restore snapshots of Portworx volumes.

* To install the Portworx Helm chart, navigate to your cluster, select System namespace.  Search for Portworx catalog in the load application section and select View Details to start the Helm chart form.  The contents of the answer file are located in the appendix called answer.yml.

![Get K8S Version](/img/px-rancher-1.png)

### Key Value Store Parameters
From version 2.0, Portworx can be installed with built-in internal kvdb. By selecting the internal kvdb option true, It removes the requirement of an external kvdb such as etcd or consul to be installed along side of Portworx. Portworx will automatically deploy an internal kvdb cluster on a set of 3 nodes within the Portworx cluster. 

If you plan to use the external kvdb option, Under kvdb configuration enter your Etcd information.  This is a list separated by semicolons ie: For example 
`etcd://myetc1.company.com:2379;etcd://myetc2.company.com:2379;etcd://myetc3.company.com:2379`  

![Get K8S Version](/img/px-rancher-2.png)

### Storage Parameters
In your environment set the drives field to the any block device that you will be using for Portworx storage.   A recommended practice is to add a separate SSD block device as a Journal drive.  If you have one available enter auto in the Journal device section.

### Network Parameters
In your environment, you will put the interface dedicated to Portworx traffic in the Data Network Interface field, and enter the Kubernetes host interface in the Management Network Interface.

### Advanced Parameters

Set the Install Stork and Lighthouse fields to true.  Define a Portworx Cluster Name that is relevant to your environment.  Set the following version information:

* Stork version: 2.1.0
* Lighthouse version:	2.0.2 
* Portworx version:	2.0.3.1

Once completed with the form select Launch.  Depending on your Internet speed and the performance of your systems it will take 5-20 minutes to install.  Once completed all process for Portworx will be green.

![Get K8S Version](/img/px-rancher-3.png)

## Step 5: Post-Install

Once you have a running Portworx installation, below sections are useful.

{{<homelist series2="k8s-postinstall">}}

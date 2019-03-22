---
title: "On-Prem Lighthouse Release Notes"
keywords: portworx, px-enterprise, px-lighthouse, release notes
meta-description: "Release notes for Lighthouse, the Portworx UI."
---

## Lighthouse 2.0.3 Release notes

### Key Fixes

* LD-391 - Lighthouse shows SHARED: FALSE for shared v4 volumes

## Lighthouse 2.0.2 Release notes

### Key Fixes

* LD-393: Lighthouse Failed to load UI if any of the nodes were in maintenance mode.

## Lighthouse 2.0.1 Release notes

* Adds Cluster Migration to Kubernetes clusters for PX Clusters with version 2.0.1+
Note: Requires kubeconfig upload and cannot migrate to non Kubernetes clusters

Fixes:
* Reverse Proxied lighthouses had issues logging in

## Lighthouse 2.0 Release notes

* Adds Cluster Migration to non Kubernetes clusters for PX Clusters with version 2.0+ 
Note: Cannot migrate to Kubernetes Clusters

Fixes:
* Now outputs logs to the container logs.
* Minor UI Fixes

## Lighthouse 1.5 Release notes

* Adds a volume analyzer feature for Portworx 1.5.0 and above. 
  Note: It won't show files which are 0.00033% of the total storage. 
* In k8s, now service DNS name can also be given as cluster endpoint 

## Lighthouse 1.4 Release notes

Lighthouse 1.4 is a new GUI refresh for lighthouse. Follow this [link](/reference/lighthouse) to install Lighthouse in your environment. Please note that Lighthouse 1.4 only works with PX 1.4 and above.

To get access to Portworx PX-Lighthouse docker repository, contact us at support@portworx.com from your company email address.

## Lighthouse 1.1.11 Release notes

* Monitoring-only version of lighthouse with create/delete/snap/node decommissioning disabled


## Lighthouse 1.1.10 Release notes

### Key Fixes

* Fixed an issue where with Portworx 1.2.11, all volumes were showing as attached in UI

## Lighthouse 1.1.9 Release notes

### Key Fixes

* Fixed an issue where deleting user group was crashing UI

## Lighthouse 1.1.8 Release notes

* Added in support for the latest Openstorage api's and made Lighthouse work with these.
* Added creation of the influx px_stats database if not present.
* Added kubernetes commands for spinning up a px node/cluster
* Added feature where the user can specify admin email and company.
* Added feature where the user can specify which port Lighthouse would be running on.

### Key Fixes

* Fixed an issue where volumes would always show as attached
* Fixed an issue where in different timezones last communication date would show 2017 years ago.

## Lighthouse 1.1.6 Release notes

* Updated dashboard with better representation of different IO Priority pools
* Dashboard updates for node status and cluster map
* Improved performance charting in dashboard and performance page
* Shortcuts to performance page from node and volume pages
* Added alerting fade-aways to give better feedback for user actions
* Faster response time for page transitions


## Lighthouse 1.1.3 Release notes

In order to use Lighthouse 1.1.3 with authentication, please download and install PX-Enterprise 1.1.2 as well. 

### Key Features

* Support for authenticated etcd2.

### Known issues

* This version does not support clear alerts. This will be fixed in the upcoming release along with the addition of “Clear All Alerts” capability
* Email server setup with TLS does not work with certificates signed by private CA
* Lighthouse container requires restart, if it has lost connectivity to etcd2

## Lighthouse 1.1.2 Release notes


### Key Fixes

* Handle kvdb access failures and show banner alerts to indicate access issues
* Handle influxDB access timeouts and show banner alerts to indicate access issues
* Improve feedback on Consul and influxDB container restarts
* Fix panics around influx or consul being down and when customer does first login after initial setup
* Fix inability to create new clusters after a node is added to a cluster

### Known issues

* This version does not support clear alerts. This will be fixed in the upcoming 1.1.3 release along with the addition of "Clear All Alerts" capability.


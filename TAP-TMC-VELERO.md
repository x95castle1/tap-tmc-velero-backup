# Velero Backups and TAP

This document will walk you through different considerations when using Velero to backup TAP. Using Velero to backup and recover TAP is currently NOT tested or supported as of TAP 1.6.

## Assumptions

* You are using a multi-cluster reference architecture.
* You are using GitOps RI to install and manage your TAP clusters.

## Known Issues

* The Pod Convention Services need to be restarted to pick up new certicates. 
* Builds for existing Workloads are in a stuck status.
* New workloads applied to the cluster work successfully after fixing certificate issues.
* TMC will no longer be managing TAP on the new cluster (If TMC installed TAP)
* Unidentified issues with TBS and old build runs (Need to research)
* Velero won't restore cluster level resources if you recover only a namespace.

## Unknown Issues

* How will the IRSA/Service Account setup work on a recovered cluster?
* What are the impacts to the GitOps RI on the new cluster?

## Cluster Types and Recovery

Each cluster type will have it's own backup/recover needs along with RPO/RTO metrics.

### View Cluster

Considerations for the View Cluster include the installed databases, workshops, and accelerators: 

* Are the databases for metadata store and TAP GUI externalized? If externalized then Velero isn't needed.
* What are their backup/snapshot schedules?
* How are you installing/managing Accelerators? GitOps?
* How are you installing/managing Workshops? GitOps?

### Build Cluster

Considerations for the Build Cluster include the following:

* Do you need to maintain build history?
* How are you managing the creation of developer namespaces? Can you recover them via GitOps?
* Can the sizing/throughput of your Build Cluster manage rebuilding all workloads?

### Run Clusters

Considerations for the Run Cluster include the following:

* Do you have an Active/Active, Active/Passive, etc. setup?
* Are your kubernetes resources (Deliverables) managed via GitOps? Can you use GitOps to recover workloads?
* How are you managing and installing configurations/secrets? GitOps? Can you use GitOps to recover configurations?
* What are the Backup/Recovery needs for Shared Services installed on the Cluster? Do these have their own backup mechanisms? 
* PersistentVolume recover should be considered if used by applications via Velero.

### Iterate Cluster

Considerations for the Iterate Cluster include the following:

* Are the databases for metadata store and TAP GUI externalized? If externalized then Velero isn't needed.
* What are their backup/snapshot schedules?
* How are you installing/managing Accelerators? GitOps?
* How are you installing/managing Workshops? GitOps?
* Do you need to maintain build history?
* How are you managing the creation of developer namespaces? Can you recover them via GitOps?
* Can the sizing/throughput of your Build Cluster manage rebuilding all workloads?


## Certificates

It is recommended to exclude certain cert manager resources when backing up and recovering. You can find guidance here:

* https://cert-manager.io/docs/tutorials/backup/#velero

## When To Use Velero vs GitOps

* If you've automated your installation of TAP via GitOps and the management of your Workloads, Deliverables, and Configurations via GitOps then it's best to consider allow GitOps to reconcile your cluster and workloads to a healthy state. 

* Velero should be considered for backups and recovery of resources like PVC's or certain applications/namespaces, but shouldn't be considered for full cluster recovery.



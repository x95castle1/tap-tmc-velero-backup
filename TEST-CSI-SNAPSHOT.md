# Test a CSI Volume Snapshot with TMC

You can test the CSI Volume Snapshot works by create a PVC and a Pod and then create a backup. This guide will walk you through how to quickly test this out.

## Create a PVC and a Pod

Make sure you have the right Storage Class setup first before applying these 

```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: ebs-claim
spec:
  accessModes:
    - ReadWriteOnce
  storageClassName: gp3
  resources:
    requests:
      storage: 2Gi
---
apiVersion: v1
kind: Pod
metadata:
  name: app
spec:
  containers:
  - name: app
    image: centos
    command: ["/bin/sh"]
    args: ["-c", "while true; do echo $(date -u) >> /data/out.txt; sleep 5; done"]
    volumeMounts:
    - name: persistent-storage
      mountPath: /data
  volumes:
  - name: persistent-storage
    persistentVolumeClaim:
      claimName: ebs-claim
```


kubectl exec -it app -- cat /data/out.txt
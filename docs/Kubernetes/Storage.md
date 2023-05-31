# Kubernetes Storage

## Persistent volume

A PersistentVolume (PV) is a piece of storage in the cluster that has been provisioned by an administrator or dynamically provisioned using Storage Classes. 

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
  name: volume
  labels:
    app: test-vol
spec:
  accessMode:
    - ReadWriteOnce
  capacity:
    storage: 10Gi
  storageClassName: <storage class name>
  hostPath:
    path: "/mnt/data"
```

## Persistent volume claim

A PersistentVolumeClaim (PVC) is a request for storage by a user. It is similar to a Pod. Pods consume node resources and PVCs consume PV resources. Pods can request specific levels of resources (CPU and Memory). Claims can request specific size and access modes (e.g., they can be mounted ReadWriteOnce, ReadOnlyMany or ReadWriteMany)


```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: task-pv-claim
spec:
  storageClassName: manual
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 3Gi
```

## Using PVC in Pod

Once you create a PVC use it in a POD definition file by specifying the PVC Claim name under persistentVolumeClaim section in the volumes section like this:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: pod-pvc
  labels:
    app: pod-pvc
spec:
  containers:
  - name: nginx
    image: nginx
    volumeMounts:
    - name: test-vol
      mountPath: /mnt
  volumes:
  - name: test-vol
    persistentVolumeClaim:
      claimName: pvc-vol
  volumeName: test-vol # when you want to map pvc to specific pv
```

The same is true for ReplicaSets or Deployments. Add this to the pod template section of a Deployment on ReplicaSet.


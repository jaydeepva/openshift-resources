# Introduction
Your application will have different storage requirements based on problem being solved. I will not be discussing storage options, storage classes as there is a lot of information available. Rather I will discuss about storage usage scenarios

- Your container needs dedicated storage
- Two or more containers needs to share storage
- Storage is shared between containers and external users. This is typically used when end user is required to inject files for processing

## Container dedicated storage
This is pretty standard stuff and can be achieved using creating a PV and PVC. The PVC can then be specified in your pod specification.

## Shared storage between Containers

### Sharing non-persistent storage
Here is a nice example of how to share non-persistant data between containers
https://www.stratoscale.com/blog/kubernetes/kubernetes-how-to-share-disk-storage-between-containers-in-a-pod/#:~:text=To%20share%20data%20between%20such,the%20pod%20that%20encloses%20it.

### Sharing persistent storage
You would start by defining a PVC first
```
kind: PersistentVolumeClaim
apiVersion: v1
metadata:
  name: my-pvc
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: myvolume
  resources:
    requests:
      storage: 1Gi
```

You can then mount this on multiple pods to share data

pod-1
```
apiVersion: v1
kind: Pod
metadata:
  name: my-app1
spec:
  containers:
...
  volumeMounts:
	- mountPath: /data
      name: data
      subPath: app1
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: 'my-pvc'
```

pod-2
```
apiVersion: v1
kind: Pod
metadata:
  name: my-app2
spec:
  containers:
...
  volumeMounts:
    - mountPath: /data
      name: data
      subPath: app2
  volumes:
    - name: data
      persistentVolumeClaim:
        claimName: 'my-pvc'
```

## Shared storage between Containers and external users
You would follow the same approach as in "Sharing persistent storage", but ensure that the persistent storage supports network filesystem like NFS, GlusterFS. External entitoes then can connect to NFS or GlusterFS using appropriate clients or linbraries.

### NFS
From https://kubernetes.io/docs/concepts/storage/volumes/#nfs

An nfs volume allows an existing NFS (Network File System) share to be mounted into your Pod. Unlike emptyDir, which is erased when a Pod is removed, the contents of an nfs volume are preserved and the volume is merely unmounted. This means that an NFS volume can be pre-populated with data, and that data can be "handed off" between Pods. NFS can be mounted by multiple writers simultaneously.

Here is an example of NFS PV
```
apiVersion: v1
kind: PersistentVolume
metadata:
  name: my-nfs-pv
spec:
  capacity:
    storage: 5Gi
  accessModes:
    - ReadWriteMany
  mountOptions:
    - hard
    - nfsvers=4.1
  nfs:
    server: 172.17.0.2
    path: /data
```

Here is an example of PVC to use NFS server
```
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: my-nfs-pvc
spec:
  accessModes:
    - ReadWriteMany
  volumeName: my-nfs-pv
  resources:
    requests:
      storage: 1Mi
```
You can omit the `volumeName: my-nfs-pv` if you wish to let Openshift pick up PV dynamically based on selection criteria like request storage, access mode, labels etc.

Here is a nice example of how to run NFS within k8s and directly mount NFS share within pod without using PV/PVC

https://matthewpalmer.net/kubernetes-app-developer/articles/kubernetes-volumes-example-nfs-persistent-volume.html

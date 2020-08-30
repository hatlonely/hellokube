# kubernetes persistent volume nfs

## PersistentVolume

```shell script
cat > slow.yaml <<EOF
apiVersion: v1
kind: PersistentVolume
metadata:
  name: jenkins
spec:
  capacity:
    storage: 50Gi
  volumeMode: Filesystem
  accessModes:
    - ReadWriteMany
  persistentVolumeReclaimPolicy: Recycle
  storageClassName: slow
  mountOptions:
    - hard
    - nfsvers=3
  nfs:
    path: /nfs/data/slow
    server: 192.168.0.101
EOF
kubectl apply -f slow.yaml
```

## StorageClass

```shell script
cat > slow.yaml <<EOF
apiVersion: storage.k8s.io/v1
kind: StorageClass
metadata:
  name: jenkins
provisioner: cluster.local/nfs-client-provisioner # deployment's env PROVISIONER_NAME'
parameters:
  archiveOnDelete: "false"
EOF
kubectl apply -f slow.yaml
```


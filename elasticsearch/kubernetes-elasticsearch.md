# kubernetes elasticsearch

## 安装


1. 创建 `PersistentVolume`
    ```shell script
    cat > pv-elasticsearch-master.yaml <<EOF
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: elasticsearch-master
    spec:
      capacity:
        storage: 5Gi
      volumeMode: Filesystem
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Recycle
      storageClassName: elasticsearch-master
      mountOptions:
        - hard
        - nfsvers=3
      nfs:
        path: /nfs/data/elasticsearch/master
        server: 192.168.0.101
    EOF
    kubectl apply -f pv-elasticsearch-master.yaml
   
   cat > pv-elasticsearch-data.yaml <<EOF
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: elasticsearch-data
    spec:
      capacity:
        storage: 50Gi
      volumeMode: Filesystem
      accessModes:
        - ReadWriteMany
      persistentVolumeReclaimPolicy: Recycle
      storageClassName: elasticsearch-data
      mountOptions:
        - hard
        - nfsvers=3
      nfs:
        path: /nfs/data/elasticsearch/data
        server: 192.168.0.101
    EOF
    kubectl apply -f pv-elasticsearch-data.yaml
    ```
3. 安装
    ```shell script
    helm install -n prod elasticsearch stable/elasticsearch \
        --set backup.image.repo=docker.elastic.co/elasticsearch/elasticsearch \
        --set backup.image.tag=7.9.0 \
        --set data.replicas=2 \
        --set data.persistence.enabled=true \
        --set data.persistence.storageClass=elasticsearch \
        --set data.persistence.size=45G \
        --set data.persistence.accessMode=ReadWriteMany \
        --set master.replicas=2 \
        --set master.persistence.enabled=true \
        --set master.persistence.storageClass=elasticsearch \
        --set master.persistence.size=4G \
        --set master.persistence.accessMode=ReadWriteMany \
        --set client.ingress.enabled=true \
        --set client.ingress.hosts={k8s.elasticsearch.hatlonely.com} \
        --set client.ingress.tls\[0\].secretName=k8s-secret \
        --set client.ingress.tls\[0\].hosts={k8s.elasticsearch.hatlonely.com}
    ```
4. 卸载
    ```shell script
    helm delete -n prod elasticsearch
    kubectl delete -n prod pvc data-elasticsearch-master-0
    kubectl delete -n prod pvc data-elasticsearch-data-0
    kubectl delete -n prod pv elasticsearch
    ```

## 链接

- helm chart: <https://github.com/helm/charts/tree/master/stable/elasticsearch>
# kubernetes elasticsearch

## 安装


1. 创建 `PersistentVolume`
    ```shell script
    kubectl apply -f - <<EOF
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: elasticsearch-master
    provisioner: cluster.local/nfs-client-provisioner # deployment's env PROVISIONER_NAME'
    parameters:
      archiveOnDelete: "true"
    EOF
    
    kubectl apply -f - <<EOF
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: elasticsearch-data
    provisioner: cluster.local/nfs-client-provisioner # deployment's env PROVISIONER_NAME'
    parameters:
      archiveOnDelete: "true"
    EOF
    ```
2. 安装
    ```shell script
    helm install -n prod elasticsearch stable/elasticsearch \
        --set backup.image.repo=docker.elastic.co/elasticsearch/elasticsearch \
        --set backup.image.tag=6.8.2 \
        --set data.replicas=2 \
        --set data.persistence.enabled=true \
        --set data.persistence.storageClass=elasticsearch-data \
        --set data.persistence.size=50G \
        --set data.persistence.accessMode=ReadWriteOnce \
        --set master.replicas=2 \
        --set master.persistence.enabled=true \
        --set master.persistence.storageClass=elasticsearch-master \
        --set master.persistence.size=4G \
        --set master.persistence.accessMode=ReadWriteOnce \
        --set client.ingress.enabled=true \
        --set client.ingress.hosts={k8s.elasticsearch.hatlonely.com} \
        --set client.ingress.tls\[0\].secretName=k8s-secret \
        --set client.ingress.tls\[0\].hosts={k8s.elasticsearch.hatlonely.com}
    ```
3. 卸载
    ```shell script
    helm delete -n prod elasticsearch
    kubectl delete -n prod pvc data-elasticsearch-master-0
    kubectl delete -n prod pvc data-elasticsearch-data-0
    kubectl delete sc elasticsearch-data elasticsearch-master
    ```

## 链接

- helm chart: <https://github.com/helm/charts/tree/master/stable/elasticsearch>
- elasticsearch：<https://www.elastic.co/guide/en/elasticsearch/reference/7.9/install-elasticsearch.html>

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
    helm repo add elastic https://helm.elastic.co
    helm install -n prod elasticsearch elastic/elasticsearch \
        --set image=docker.elastic.co/elasticsearch/elasticsearch \
        --set imageTag=7.9.0 \
        --set securityContext.runAsNonRoot=false \
        --set podSecurityContext.runAsUser=0 \
        --set podSecurityContext.fsGroup=0
    ```
3. 卸载
    ```shell script
    helm delete -n prod elasticsearch
    kubectl delete pvc elasticsearch-master-elasticsearch-master-0 -n prod
    kubectl delete pvc elasticsearch-master-elasticsearch-master-1 -n prod
    kubectl delete pvc elasticsearch-master-elasticsearch-master-2 -n prod
    ```

## 链接

- helm chart: <https://github.com/elastic/helm-charts/tree/master/elasticsearch>
- elasticsearch：<https://www.elastic.co/guide/en/elasticsearch/reference/7.9/install-elasticsearch.html>

# kubernetes elasticsearch

## 安装


1. 创建 `PersistentVolume`
    ```shell script
    cat > pv-elasticsearch.yaml <<EOF
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: elasticsearch
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
        path: /nfs/data/elasticsearch
        server: 192.168.0.101
    EOF
    kubectl apply -f pv-elasticsearch.yaml
    ```
2. 创建 `PersistentVolumeClaim`
    ```shell script
    cat > pv-elasticsearch.yaml <<EOF
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      namespace: prod
      name: elasticsearch-pvc
    spec:
      accessModes:
        - ReadWriteMany
      volumeMode: Filesystem
      resources:
        requests:
          storage: 50Gi
      storageClassName: slow
      selector:
    EOF
    kubectl apply -f pvc-elasticsearch.yaml
    ```
3. 安装
    ```shell script
    helm install -n prod elasticsearch stable/elasticsearch \
        --set backup.image.repo=docker.elastic.co/elasticsearch/elasticsearch \
        --set backup.image.tag=7.9.0 \
        --set master.replicas=2 \
        --set persistence.existingClaim=elasticsearch-pvc \
        --set client.ingress.enabled=true \
        --set client.ingress.hosts={k8s.elasticsearch.hatlonely.com} \
        --set client.ingress.tls\[0\].secretName=k8s-secret \
        --set client.ingress.tls\[0\].hosts={k8s.elasticsearch.hatlonely.com}
    ```
4. 卸载
    ```shell script
    helm delete elasticsearch -n prod
    ```

## 链接

- helm chart: <https://github.com/helm/charts/tree/master/stable/elasticsearch>
# kubernetes hadoop

## 安装

1. 安装
    ```shell script
    helm install -n prod hadoop stable/hadoop \
        --set persistence.nameNode.enabled=true \
        --set persistence.nameNode.storageClass=nfs-client \
        --set persistence.dataNode.enabled=true \
        --set persistence.dataNode.storageClass=nfs-client
    ```
2. 卸载
    ```shell script
    helm delete -n prod hadoop
    ```

## 链接

- helm: <https://hub.helm.sh/charts/stable/hadoop>

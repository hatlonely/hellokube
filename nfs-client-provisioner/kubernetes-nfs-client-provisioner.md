# kubernetes nfs client provisioner

## 安装

1. 安装
    ```shell script
    helm install -n kube-system nfs-client-provisioner stable/nfs-client-provisioner \
        --set nfs.server=192.168.0.101 \
        --set nfs.path=/nfs/data \
        --set nfs.mountOptions\[0\]=hard \
        --set nfs.mountOptions\[1\]="nfsvers=3"
    ```
2. 卸载
    ```shell script
    helm delete -n kube-system nfs-client-provisioner
    ```

## 链接

- helm chart: <https://github.com/helm/charts/tree/master/stable/nfs-client-provisioner>
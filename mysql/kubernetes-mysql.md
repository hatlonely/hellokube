# kubernetes mysql

## 安装

1. 安装
    ```shell script
    helm install -n prod mysql stable/mysql \
        --set image=mysql \
        --set imageTag=5.7.30 \
        --set busybox.image=busybox \
        --set busybox.tag=1.32 \
        --set persistence.enabled=true \
        --set persistence.size=30Gi \
        --set metrics.enabled=true \
        --set metrics.image=prom/mysqld-exporter \
        --set metrics.imageTag=v0.10.0 \
        --set securityContext.enabled=true \
        --set securityContext.fsGroup=50 \
        --set securityContext.runAsUser=1000
    ```
2. 获取 root 密码
    ```shell script
    MYSQL_ROOT_PASSWORD=$(kubectl get secret -n prod mysql -o jsonpath="{.data.mysql-root-password}" | base64 -d)
    ```
3. 卸载
    ```shell script
    helm delete -n prod mysql
    ```

## 链接

- helm: <https://github.com/helm/charts/tree/master/stable/mysql>

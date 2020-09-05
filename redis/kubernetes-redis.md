# kubernetes redis

## 安装

1. 安装
    ```shell script
    helm install -n prod redis stable/redis \
        --set image.repository=bitnami/redis \
        --set image.tag=6.0 \
        --set cluster.enabled=true \
        --set cluster.slaveCount=1 \
        --set usePassword=true \
        --set securityContext.enabled=true \
        --set securityContext.fsGroup=1001 \
        --set securityContext.runAsUser=1001 \
        --set metrics.enabled=true
    ```
2. 获取 redis 密码
    ```shell script
    REDIS_PASSWORD=$(kubectl get secret --namespace prod redis -o jsonpath="{.data.redis-password}" | base64 --decode)
    ```
3. 卸载
    ```shell script
    helm delete -n prod redis
    ```

## 链接

- helm: <https://github.com/helm/charts/tree/master/stable/redis>


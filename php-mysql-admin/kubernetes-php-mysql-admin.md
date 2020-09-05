# kubernetes php mysql admin

## 安装

1. 安装
    ```shell script
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm install -n prod phpmyadmin bitnami/phpmyadmin \
        --set db.host=mysql \
        --set image.repository=bitnami/phpmyadmin \
        --set image.tag=5.0.2-debian-10-r146 \
        --set ingress.enabled=true \
        --set ingress.hosts\[0\].name=k8s.phpmyadmin.hatlonely.com \
        --set ingress.hosts\[0\].tls=true \
        --set ingress.hosts\[0\].tlsSecret=k8s-secret \
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

- helm: <https://github.com/bitnami/charts/tree/master/bitnami/phpmyadmin>


# kubernetes zeppelin

## 安装

1. `stble/zeppelin` 不兼容 k8s 1.17，需手动下载兼容版本
    ```shell script
    git clone https://github.com/lucaskjaero/charts.git
    ```
2. 安装
    ```shell script
    cd charts/stable/zeppelin
    helm upgrade -n prod zeppelin . -f values.yaml \
        --set zeppelin.image=a3cf6vw7.mirror.aliyuncs.com/dylanmei/zeppelin:0.7.3 \
        --set hadoop.useConfigMap=true \
        --set hadoop.configMapName=hadoop-hadoop \
        --set ingress.enabled=true \
        --set ingress.hosts\[0\]=k8s.zeppelin.hatlonely.com \
        --set ingress.tls\[0\].secretName=k8s-secret \
        --set ingress.tls\[0\].hosts={k8s.zeppelin.hatlonely.com}
    ```
3. 卸载
    ```shell script
    helm delete -n prod zeppelin
    ```

## 链接

- helm: <https://hub.helm.sh/charts/stable/zeppelin>
- helm 修复: <https://github.com/lucaskjaero/charts/tree/zeppelin-uses-latest-api-versions>

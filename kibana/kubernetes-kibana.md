# kubernetes kibana

## 安装

1. 安装
    ```shell script
    helm repo add elastic https://helm.elastic.co
    helm install -n prod kibana elastic/kibana \
        --set image=docker.elastic.co/kibana/kibana \
        --set imageTag=7.9.0 \
        --set elasticsearchHosts=http://elasticsearch-master:9200 \
        --set securityContext.runAsNonRoot=false \
        --set podSecurityContext.runAsUser=0 \
        --set podSecurityContext.fsGroup=0 \
        --set ingress.enabled=true \
        --set ingress.hosts={k8s.kibana.hatlonely.com} \
        --set ingress.tls\[0\].secretName=k8s-secret \
        --set ingress.tls\[0\].hosts={k8s.kibana.hatlonely.com}
    ```
2. 卸载
    ```shell script
    helm delete -n prod kibana
    ```

## 链接

- helm: <https://github.com/elastic/helm-charts/tree/master/kibana>

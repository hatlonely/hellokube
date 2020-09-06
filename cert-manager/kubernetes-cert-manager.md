# kubernetes cert manager

## 安装

1. 安装资源
    ```shell script
    kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.1/cert-manager.crds.yaml
    ```
2. 安装
    ```shell script
    helm repo add jetstack https://charts.jetstack.io
    helm install -n prod cert-manager jetstack/cert-manager \
        --set image.repository=quay.io/jetstack/cert-manager-controller \
        --set image.tag=v0.6.2 \
        --set ingressShim.defaultIssuerName=letsencrypt-prod \
        --set ingressShim.defaultIssuerKind=ClusterIssuer
    ```
3. 卸载
    ```shell script
    helm delete -n prod cert-manager
    ```

## 链接

- helm: <https://hub.helm.sh/charts/jetstack/cert-manager>

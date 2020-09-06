# kubernetes cert manager

## 安装

1. 安装资源
    ```shell script
    kubectl apply --validate=false -f https://github.com/jetstack/cert-manager/releases/download/v1.0.1/cert-manager.crds.yaml
    ```
2. 安装
    ```shell script
    helm repo add jetstack https://charts.jetstack.io
    kubectl create namespace cert-manager
    helm install -n cert-manager cert-manager jetstack/cert-manager \
        --set image.repository=quay.io/jetstack/cert-manager-controller \
        --set image.tag=v0.6.2 \
        --set ingressShim.defaultIssuerName=letsencrypt-prod \
        --set ingressShim.defaultIssuerKind=ClusterIssuer
    ```
3. 部署 issuer 服务
    ```shell script
    kubectl apply -n cert-manager -f - <<EOF
    apiVersion: cert-manager.io/v1alpha2
    kind: ClusterIssuer
    metadata:
      name: letsencrypt-prod
    spec:
      acme:
        server: https://acme-v02.api.letsencrypt.org/directory
        email: hatlonely@foxmail.com
        privateKeySecretRef:
          name: letsencrypt-prod
        solvers:
        - http01:
           ingress:
             class: nginx
    EOF
    ```
4. 卸载
    ```shell script
    kubectl delete ClusterIssuer letsencrypt-prod
    helm delete -n cert-manager cert-manager
    ```

## 链接

- helm: <https://hub.helm.sh/charts/jetstack/cert-manager>
- acme: <https://akomljen.com/get-automatic-https-with-lets-encrypt-and-kubernetes-ingress/>

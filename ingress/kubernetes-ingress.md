# kubernetes ingress

## helm 安装

1. 安装
    ```sh
    helm install -n kube-system nginx-ingress stable/nginx-ingress \
      --set controller.metrics.enabled=true \
      --set rbac.create=true \
      --set defaultBackend.image.repository=docker.io/mirrorgooglecontainers/defaultbackend-amd64 \
      --set controller.hostNetwork=true
    ```

## 链接

- helm chart: <https://github.com/helm/charts/tree/master/stable/nginx-ingress>
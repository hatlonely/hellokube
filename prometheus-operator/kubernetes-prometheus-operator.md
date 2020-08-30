# kubernetes prometheus operator

## 安装

1. 生成证书
    ```shell script
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./k8s.key -out ./k8s.crt -subj "/CN=192.168.0.10,192.168.0.11,192.168.0.12"
    kubectl -n kube-system create secret tls k8s-secret --key ./k8s.key --cert ./k8s.crt
    ```
2. 安装
    ```shell script
    kubectl create namespace monitoring
    helm install -n monitoring prometheus-operator stable/prometheus-operator \
      --set grafana.ingress.enabled=true \
      --set grafana.ingress.hosts={k8s.grafana.hatlonely.com} \
      --set grafana.ingress.tls\[0\].secretName=k8s-secret \
      --set grafana.ingress.tls\[0\].hosts={k8s.grafana.hatlonely.com}
    ```
3. Mac 打开【钥匙串访问】，切换到【系统】面板，将 `k8s.crt` 证书拖进去
4. `kubectl get ingress -A` 查看 ingress 的外部端点
    ```shell script
    monitoring    prometheus-operator-grafana          k8s.grafana.hatlonely.com     192.168.0.11   80, 443   3m46s
    ```
5. 设置 host，`sudo vi /etc/hosts`，添加行 `192.168.0.11 k8s.grafana.hatlonely.com`
6. 浏览器打开 `k8s.grafana.hatlonely.com` 即可访问
7. 获取账号密码
    ```shell script
    username=$(kubectl get secrets prometheus-operator-grafana -n monitoring -o json | jq -r '.data."admin-user"' | base64 --decode)
    password=$(kubectl get secrets prometheus-operator-grafana -n monitoring -o json | jq -r '.data."admin-password"' | base64 --decode)
    echo "username: $username"
    echo "password: $password"
    ```
8. 卸载
    ```shell script
    helm delete k8s-dashboard -n monitoring
    ```

## 链接

- helm chart: <https://github.com/helm/charts/tree/master/stable/prometheus-operator>

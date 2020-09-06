# kubernetes dashboard

## yaml 安装

1. 安装
    ```sh
    kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
    ```
2. 本地执行 `kubectl proxy`，
3. 浏览器打开 <http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/.>
4. 获取凭证
    ```sh
    kubectl create sa dashboard-admin -n kube-system
    kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
    ADMIN_SECRET=$(kubectl get secrets -n kube-system | grep dashboard-admin | awk '{print $1}')
    DASHBOARD_LOGIN_TOKEN=$(kubectl describe secret -n kube-system ${ADMIN_SECRET} | grep -E '^token' | awk '{print $2}')
    echo ${DASHBOARD_LOGIN_TOKEN}
    ```
5. 卸载
    ```sh
    kubectl delete -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.0/aio/deploy/recommended.yaml
    ```

## helm 安装

1. 生成证书
     ```sh
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./k8s.key -out ./k8s.crt -subj "/CN=192.168.0.10,192.168.0.11,192.168.0.12"
    kubectl -n kube-system create secret tls k8s-secret --key ./k8s.key --cert ./k8s.crt
    ```
2. 安装
```shell script
helm repo add kubernetes-dashboard https://kubernetes.github.io/dashboard/

helm upgrade -n kube-system k8s-dashboard kubernetes-dashboard/kubernetes-dashboard \
    --set image.tag=v2.0.1 \
    --set replicaCount=2 \
    --set ingress.enabled=true \
    --set ingress.hosts={k8s.dashboard.hatlonely.com} \
    --set ingress.tls\[0\].secretName=kubernetes-dashboard-tls \
    --set ingress.tls\[0\].hosts={k8s.dashboard.hatlonely.com} \
    --set ingress.annotations."kubernetes\.io/ingress\.class"=nginx \
    --set ingress.annotations."kubernetes\.io/tls-acme"=\"true\" \
    --set ingress.annotations."nginx\.ingress\.kubernetes\.io/backend-protocol"=HTTPS \
    --set rbac.create=true \
    --set rbac.clusterAdminRole=true
```
3. Mac 打开【钥匙串访问】，切换到【系统】面板，将 `k8s.crt` 证书拖进去
4. `kubectl get ingress -A` 查看 ingress 的外部端点
    ```sh
    kube-system   k8s-dashboard-kubernetes-dashboard   k8s.dashboard.hatlonely.com   192.168.0.11   80, 443   9h
    ```
5. 设置 host，`sudo vi /etc/hosts`，添加行 `192.168.0.11 k8s.dashboard.hatlonely.com`
6. 浏览器打开 `k8s.dashboard.hatlonely.com` 即可访问
7. 获取凭证
    ```sh
    kubectl create sa dashboard-admin -n kube-system
    kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
    ADMIN_SECRET=$(kubectl get secrets -n kube-system | grep dashboard-admin | awk '{print $1}')
    DASHBOARD_LOGIN_TOKEN=$(kubectl describe secret -n kube-system ${ADMIN_SECRET} | grep -E '^token' | awk '{print $2}')
    echo ${DASHBOARD_LOGIN_TOKEN}
    ```
8. 卸载
    ```sh
    helm delete k8s-dashboard -n kube-system
    ```    

## 链接

- Web UI 官网: <https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/>
- 官网 helm: <https://hub.helm.sh/charts/k8s-dashboard/kubernetes-dashboard>
- github: <https://github.com/kubernetes/dashboard>

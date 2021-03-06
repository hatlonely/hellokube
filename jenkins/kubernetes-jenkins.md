# kubernetes jenkins

## 安装

1. 生成证书
    ```shell script
    openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout ./k8s.key -out ./k8s.crt -subj "/CN=192.168.0.10,192.168.0.11,192.168.0.12"
    kubectl -n kube-system create secret tls k8s-secret --key ./k8s.key --cert ./k8s.crt
    ```
2. 创建 `PersistentVolume`
    ```shell script
    kubectl apply -f - <<EOF
    apiVersion: storage.k8s.io/v1
    kind: StorageClass
    metadata:
      name: jenkins
    provisioner: cluster.local/nfs-client-provisioner # deployment's env PROVISIONER_NAME'
    parameters:
      archiveOnDelete: "true"
    EOF
    ```
3. 创建 `PersistentVolumeClaim`
    ```shell script
    kubectl apply -f - <<EOF
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      namespace: prod
      name: jenkins-pvc
    spec:
      accessModes:
        - ReadWriteMany
      volumeMode: Filesystem
      resources:
        requests:
          storage: 50Gi
      storageClassName: jenkins
      selector:
    EOF
    ```
4. 安装
    ```shell script
    kubectl create namespace prod
    helm install -n prod jenkins stable/jenkins \
        --set backup.image.repo=registry.docker.com/jenkins \
        --set backup.image.tag=2.60.3 \
        --set persistence.existingClaim=jenkins-pvc \
        --set master.ingress.enabled=true \
        --set master.ingress.hostName=k8s.jenkins.hatlonely.com \
        --set master.ingress.tls\[0\].secretName=k8s-secret \
        --set master.ingress.tls\[0\].hosts={k8s.jenkins.hatlonely.com}
    ```
5. Mac 打开【钥匙串访问】，切换到【系统】面板，将 `k8s.crt` 证书拖进去
6. `kubectl get ingress -A` 查看 ingress 的外部端点
    ```shell script
    prod          jenkins                              k8s.jenkins.hatlonely.com     192.168.0.11   80, 443   6m52s
    ```
7. 设置 host，`sudo vi /etc/hosts`，添加行 `192.168.0.11 k8s.jenkins.hatlonely.com`
8. 浏览器打开 `k8s.jenkins.hatlonely.com` 即可访问
9. 获取账号密码
    ```shell script
    username=$(kubectl get secrets jenkins -n prod -o json | jq -r '.data."jenkins-admin-user"' | base64 --decode)
    password=$(kubectl get secrets jenkins -n prod -o json | jq -r '.data."jenkins-admin-password"' | base64 --decode)
    echo "username: $username"
    echo "password: $password"
    ```
10. 卸载
    ```shell script
    helm delete -n prod jenkins
    kubectl delete pvc -n prod jenkins-pvc
    kubectl delete sc jenkins
    ```

## 链接

- helm chart: <https://hub.helm.sh/charts/stable/jenkins>

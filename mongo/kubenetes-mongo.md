# kubernetes mongo

## 安装

1. 安装
    ```shell script
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm install -n prod mongo bitnami/mongodb \
        --set architecture=standalone
    ```
2. mongo 访问地址
    ```
    mongo-mongodb.prod.svc.cluster.local
    ```
3. 密码 
    ```
    export MONGODB_ROOT_PASSWORD=$(kubectl get secret --namespace prod mongo-mongodb -o jsonpath="{.data.mongodb-root-password}" | base64 --decode)
    ```
4. 链接数据库
    ```
    kubectl run --namespace prod mongo-mongodb-client --rm --tty -i --restart='Never' --env="MONGODB_ROOT_PASSWORD=$MONGODB_ROOT_PASSWORD" --image docker.io/bitnami/mongodb:4.4.3-debian-10-r0 --command -- bash

    mongo admin --host "mongo-mongodb" --authenticationDatabase admin -u root -p $MONGODB_ROOT_PASSWORD
    ```
5. 卸载
    ```
    helm delete mongo -n prod
    ```
6. 清理 pvc
    ```
    kubectl delete pvc mongo-mongodb -n prod
    ```

## 链接



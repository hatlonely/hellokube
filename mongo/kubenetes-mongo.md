# kubernetes mongo

## 安装

1. 安装
    ```shell script
    helm repo add bitnami https://charts.bitnami.com/bitnami
    helm install -n prod mongo bitnami/mongodb \
        --set architecture=replicaset \
        --set replicaCount=3 \
        --set persistence.enbale=true \
        --set metrics.enabled=true \
        --set podSecurityContext.runAsUser=501 \
        --set containerSecurityContext.runAsUser=501
    ```
2. mongo 访问地址
    ```
    mongo-mongodb-0.mongo-mongodb-headless.prod.svc.cluster.local:27017
    mongo-mongodb-1.mongo-mongodb-headless.prod.svc.cluster.local:27017
    mongo-mongodb-2.mongo-mongodb-headless.prod.svc.cluster.local:27017
    ```
3. 密码 
    ```
    export MONGODB_ROOT_PASSWORD=$(kubectl get secret --namespace prod mongo-mongodb -o jsonpath="{.data.mongodb-root-password}" | base64 --decode)
    ```
4. 链接数据库
    ```
    kubectl run --namespace prod mongo-mongodb-client --rm --tty -i --restart='Never' --env="MONGODB_ROOT_PASSWORD=$MONGODB_ROOT_PASSWORD" --image docker.io/bitnami/mongodb:4.4.3-debian-10-r0 --command -- bash

    mongo admin --host "mongo-mongodb-0.mongo-mongodb-headless.prod.svc.cluster.local:27017,mongo-mongodb-1.mongo-mongodb-headless.prod.svc.cluster.local:27017,mongo-mongodb-2.mongo-mongodb-headless.prod.svc.cluster.local:27017" --authenticationDatabase admin -u root -p $MONGODB_ROOT_PASSWORD
    ```
5. 卸载
    ```
    helm delete mongo -n prod
    ```

## 链接



# RedisSyncer TPaaS 集成项目

* tpaas 测试环境登录地址
https://idaas-sso.jdcloud.com/login/tpaas 

# 上传镜像

```shell
docker pull jiashiwen/redissyncer-dashboard
docker pull jiashiwen/redissyncer
docker tag 7459055c9735 tpaas-registry-itg.jdcloud.com/redissyncer/redissyncer-dashboard:latest
docker push tpaas-registry-itg.jdcloud.com/redissyncer/redissyncer-dashboard:latest
docker tag 259820d5e5c5 tpaas-registry-itg.jdcloud.com/redissyncer/jiashiwen/redissyncer-server:latest
docker push tpaas-registry-itg.jdcloud.com/redissyncer/jiashiwen/redissyncer-server:latest
```

# 上传chart 到helm

* 打包

```shell
cd redissyncer-chart
helm package .
helm repo index .
```

* 上传
  
```shell
helm plugin install https://github.com/chartmuseum/helm-push.git
helm push redissyncer-1.0.0.tgz itg
```

# 安装redissyncer

* 为redissyncer 新建namespace

```shell
kubectl create namespace redissyncer
```

* 创建secret

```shell
kubectl create secret docker-registry redissyncer-secret-key --docker-server=tpaas-registry-itg-vpc.jdcloud.com --docker-username=jiashiwen1 --docker-password=git785230root --docker-email=jiashiwen@jd.com --namespace redissyncer
```

* 配置hosts

```shell
114.67.232.31 redissyncer.web
114.67.232.31 redissyncer.server
```

* 安装redissyncer
本地安装

```shell
helm install redissyncer . --namespace redissyncer
```

通过仓库安装

```shell
helm install myredissyncer itg/redissyncer-chart --namespace redissyncer
```

* 验证安装成果

```shell
kubectl get pod --namespace redissyncer
NAME                             READY   STATUS    RESTARTS   AGE
myredissyncer-6f6567f4c5-9w4g5   2/2     Running   0          13d
```

* 卸载redissyncer

```shell
helm uninstall redissyncer --namespace redissyncer
```

# 测试场景

## 测试过程

* 安装redis

```shell
helm repo add bitnami https://charts.bitnami.com/bitnami
helm install myredis1 bitnami/redis --set replica.replicaCount=1 --set image.tag=5.0.13
helm install myredis2 bitnami/redis --set replica.replicaCount=1 --set image.tag=5.0.13
```

* 获取redis密码

```shell
kubectl get secret --namespace default myredis1 -o jsonpath="{.data.redis-password}" | base64 --decode
kubectl get secret --namespace default myredis2 -o jsonpath="{.data.redis-password}" | base64 --decode
```

* 连接redis
  * 通过kubectl 映射端口

  ```shell
  # 连接service
  kubectl port-forward --namespace default svc/myredis1-master 16379:6379
  kubectl port-forward --namespace default svc/myredis2-master 16380:6379
  # 连接pod
  kubectl port-forward --namespace default pod/myredis1-master-0 16379:6379
  kubectl port-forward --namespace default pod/myredis2-master-0 16380:6379
  ```

  * 通过redis客户端连接redis

  ```shell
  redis-cli -p 16379 -a xyALNFRSVg
  redis-cli -p 16380 -a xxxx

  ```

* 通过redissyncer进行数据同步
  * 下载redissyncer-cli

  ```shell
  wget https://github.com/TraceNature/redissyncer-cli/releases/download/v0.2.0/redissyncer-cli-v0.2.0.tar.gz

  ```

  * 同步任务json
  
  ```json
  {
  "sourcePassword": "XVRBS8ACEM",
  "sourceRedisAddress": "192.168.1.248:6379",
  "targetRedisAddress": "192.168.1.251:6379",
  "targetPassword": "r7a2I63qhU",
  "taskName": "in_k8s",
  "targetRedisVersion": 4.0,
  "autostart": true,
  "afresh": true,
  "batchSize": 500
  }

  ```


  ```json
  {
  "sourcePassword": "redistest0102",
  "sourceRedisAddress": "114.67.76.82:16374",
  "targetRedisAddress": "192.168.1.251:6379",
  "targetPassword": "r7a2I63qhU",
  "taskName": "out2k8s",
  "targetRedisVersion": 4.0,
  "autostart": true,
  "afresh": true,
  "batchSize": 500
  }

  ```

  
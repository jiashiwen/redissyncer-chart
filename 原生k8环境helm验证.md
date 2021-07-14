# 原生k8s环境helm验证

## kubeasz 安装

* 参考文档

```shell
https://github.com/easzlab/kubeasz/blob/master/docs/setup/quickStart.md
```

* 在管控机上安装kubectl
  * 参考文档

    ```shell
    https://kubernetes.io/zh/docs/tasks/tools/install-kubectl-linux/
    ```

  * 配置kubectl

    ```shell
    mkdir ~/.kube
    cd ~/.kube
    scp k8s-single:/root/.kube/config . 
    ```

* 安装helm
  * 参考文档
  
    ```shell
    https://helm.sh/zh/docs/intro/install/
    ```

    ```shell
    curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
    sudo apt-get install apt-transport-https --yes
    echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
    sudo apt-get update
    sudo apt-get install helm
    ```

* install helm for mac

```
brew install helm
```
  

## Todo

- [ ] 编写redissyncer chart
- [ ] 实验nginx ingress
- [ ] traefik ingress
- [ ] 单pod集成web和server

## nginx ingress

### kubeasz 单节点安装 k8s 版本 1.8

* 下载工具脚本ezdown，举例使用kubeasz版本3.0.0
  
  ```shell
  export release=3.0.0
  curl -C- -fLO --retry 3 https://github.com/easzlab/kubeasz/releases/download/${release}/ezdown
  chmod +x ./ezdown
  ```

* 下载部署所需软件包
  
  ```shell
  ./ezdown -D -k v1.18.17
  ```

* 下载离线系统包
  
  ```shell
  ./ezdown -P
  ```

* 容器化运行 kubeasz

  ```shell
  ./ezdown -S
  ```

* 安装单机k8s
  
  ```shell
  docker exec -it kubeasz ezctl start-aio
  ```

### 安装nginx ingress conroller

* 通过helm安装 nginx ingress
  
  ```shell
  helm install ingress-nginx nginx-stable/nginx-ingress \
             --set controller.service.type=NodePort    \
             --set controller.service.httpPort.nodePort=30080 \
             --set controller.service.httpsPort.nodePort=30443
  ```

## traefik ingress 部署

### 清理kubeasz 节点

* 清理集群
  
  ```shell
  docker exec -it kubeasz ezctl destroy default
  ```

* 清理运行的容器
  
  ```shell
  ./ezdown -C
  ```

* 清理容器镜像
  
  ```sehll
  docker system prune -a
  ```

* 清理下载文件

  ```shell
  rm -fr /etc/kubeasz
  ```

### 重新安装k8s

* 下载部署所需软件包
  
  ```shell
  ./ezdown -D 
  ```

* 下载离线系统包
  
  ```shell
  ./ezdown -P
  ```

* 容器化运行 kubeasz

  ```shell
  ./ezdown -S
  ```

* 安装单机k8s
  
  ```shell
  docker exec -it kubeasz ezctl start-aio
  ```

### 通过helm 安装 traefik ingress

* 安装 traefik ingress
  
  ```shell
  helm repo add traefik https://helm.traefik.io/traefik
  helm repo update
  helm install traefik traefik/traefik
  ```

* 验证安装是否成功

```shell
kubectl port-forward $(kubectl get pods --selector "app.kubernetes.io/name=traefik" --output=name) 9000:9000
http://127.0.0.1:9000/dashboard/

```

---


```
helm install  \
    --set="dashboard.enabled=true,service.type=NodePort" \
    traefik traefik/traefik


helm install traefik traefik/traefik \
  --namespace kube-system \
  --set kubernetes.ingressClass=traefik \
  --set kubernetes.ingressEndpoint.useDefaultPublishedService=true \
  --set rbac.enabled=true \
  --set service.type=NodePort
  


helm install traefik traefik/traefik \
  --set kubernetes.ingressClass=traefik \
  --set kubernetes.ingressEndpoint.useDefaultPublishedService=true \
  --set rbac.enabled=true 
  --set service.type=NodePort


helm upgrade --install traefik \
    --set dashboard.enabled=true \
    --set rbac.enabled=true \
    --set="additionalArguments={--api.dashboard=true,--log.level=INFO,--providers.kubernetesingress.ingressclass=traefik-internal,--serversTransport.insecureSkipVerify=true}" \
    traefik/traefik 

helm repo add nginx-stable https://helm.nginx.com/stable
helm repo update
helm install ingress-nginx nginx-stable/nginx-ingress \
             --set controller.service.type=NodePort    \
             --set controller.service.httpPort.nodePort=30080 \
             --set controller.service.httpsPort.nodePort=30443


curl -i -H "Host: redissyncer.io" http://10.0.0.120:30282/

curl -i -H "Host: server.redissyncer.io" http://10.0.0.120:31829/

curl -H server.redissyncer.io --request POST \
  --url http://10.68.130.133/api/v2/listtasks \
  --header 'Content-Type: application/json' 


curl -H server.redissyncer.io --request POST \
  --url http://127.0.0.1:8000/api/v2/listtasks 


curl --request POST \
  --url http://server.redissyncer.io:32701/api/v2/listtasks \
  --header 'Content-Type: application/json' \
  --data '{
	"regulation":"all"
}
'


curl -H server.redissyncer.io --request POST   --url http://10.0.0.120:32701/api/v2/listtasks 

curl -H server.redissyncer.io --request POST   --url http://127.0.0.1:32515/api/v2/listtasks

curl  --request POST   --url http://server.redissyncer.io:31164/api/v2/listtasks


curl -k http://10.0.0.120:31506 -v -H 'Host: server.redissyncer.io'

spring.datasource.url=jdbc:sqlite:/redissyncer/syncer.db

kubectl apply -f redissyncer-server.yml

kubectl expose deployment redissyncer-server --type=NodePort --name=redissyncer-nodeport-service

kubectl exec -it redissyncer-server-569755ff67-djjh2 -- /bin/bash

kubectl apply -f pv-volume.yaml

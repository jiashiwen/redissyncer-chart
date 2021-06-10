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
- [ ] 实验使用helm安装redis
- [ ] 查看redis chart
- [ ] 编写redissyncer chart
- [ ] traefik ingress


# traefik ingress 部署

```shell
# Setup domain, user and password first.
$ export USER=user
$ export DOMAIN=ingress.feisky.xyz
$ htpasswd -c auth $USER
New password:
Re-type new password:
Adding password for user user
$ PASSWORD=$(cat auth| awk -F: '{print $2}')

# Deploy with helm.
helm install stable/traefik --name --namespace kube-system --set rbac.enabled=true,acme.enabled=true,dashboard.enabled=true,acme.staging=false,acme.email=admin@$DOMAIN,dashboard.domain=ui.$DOMAIN,ssl.enabled=true,acme.challengeType=http-01,dashboard.auth.basic.$USER=$PASSWORD

```


spring.datasource.url=jdbc:sqlite:/redissyncer/syncer.db

kubectl apply -f redissyncer-server.yml
kubectl expose deployment redissyncer-server --type=NodePort --name=redissyncerk8-service --port 8080
kubectl exec -it redissyncer-server-569755ff67-djjh2 -- /bin/bash

kubectl apply -f pv-volume.yaml

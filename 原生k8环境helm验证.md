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
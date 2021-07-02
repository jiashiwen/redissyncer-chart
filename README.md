# RedisSyncer TPaaS 集成项目

* tpaas 测试环境登录地址
https://idaas-sso.jdcloud.com/login/tpaas 

# 上传镜像

```
docker pull jiashiwen/redissyncer-dashboard
docker pull jiashiwen/redissyncer
docker tag 7459055c9735 tpaas-registry-itg.jdcloud.com/redissyncer/redissyncer-dashboard:latest
docker push tpaas-registry-itg.jdcloud.com/redissyncer/redissyncer-dashboard:latest
docker tag 259820d5e5c5 tpaas-registry-itg.jdcloud.com/redissyncer/jiashiwen/redissyncer-server:latest
docker push tpaas-registry-itg.jdcloud.com/redissyncer/jiashiwen/redissyncer-server:latest
```

# 为redissyncer 新建namespace
```
kubectl create namespace redissyncer
```
创建secret
```
kubectl create secret docker-registry redissyncer-secret-key --docker-server=tpaas-registry-itg-vpc.jdcloud.com --docker-username=jiashiwen1 --docker-password=git785230root --docker-email=jiashiwen@jd.com
```
tpaas 镜像仓库用户名密码
helm 仓库用户名及密码
redissyncer 安装到哪个namespace 
tpaas pv 如何集成
ingress 如何集成
部署完成的访问入口是什么
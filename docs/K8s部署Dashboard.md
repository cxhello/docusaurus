# K8s部署Dashboard

## 下载yml配置文件

```
kubectl version
```

![image-20220613113756819](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220613113756819.png)

根据K8s版本查询对应的dashboard版本

> https://github.com/kubernetes/dashboard/releases?page=2

![image-20220613113918725](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220613113918725.png)

```bash
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.3/aio/deploy/recommended.yaml
```

## 修改配置文件

下载完成后对配置文件进行修改

```bash
vim recommended.yaml
```

![image-20220613114133451](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220613114133451.png)

将type改为NodePort，这样可以对外访问，修改完成进行保存。

```bash
# 启动dashboard
kubectl apply -f recommended.yaml
# 查看dashboard的pod
kubectl get pods -n kubernetes-dashboard
# 查看dashboard的对外端口
kubectl get services -n kubernetes-dashboard
```

![image-20220613114514778](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220613114514778.png)

## 访问dashboard

> https://192.168.72.4:30691

![image-20220613114620250](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220613114620250.png)

```bash
# 创建一个叫dashboard-admin的账号，并指定命名空间为kube-system
kubectl create serviceaccount dashboard-admin -n kube-system
# 创建一个关系，关系名为dashboard-admin，角色为cluster-admin，账户为kube-system命名空间下的dashboard-admin账号
kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
# 查看Token的编号（第二列）
kubectl get secrets --all-namespaces | grep -i dashboard-admin
# 查看指定Token的编号的具体token值
# kubectl describe secret/dashboard-admin-token-mbx68 -n kube-system
```

![image-20220613114854462](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220613114854462.png)

```
eyJhbGciOiJSUzI1NiIsImtpZCI6ImdrWDh3X2xVZlhXVWZ4VjZocURWQUNZeVBqSnNNemdSYm85aVhiNlhuODAifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkYXNoYm9hcmQtYWRtaW4tdG9rZW4tbWJ4NjgiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGFzaGJvYXJkLWFkbWluIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiYTUxZGYzNjUtYmFkMC00ZmNiLTgyM2MtMTQ1NjM5MTU2NjNhIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmRhc2hib2FyZC1hZG1pbiJ9.LvRv4mO-zGGsEn8eDwo875MY5xUTLFs2lmmiKio7mijsmOdWyevNOVEw_FQS9M-f9EwL70vpdHCwh6Z3PQxxf1hBBZhufiUg6bgTkzOXHqt1ljZWYPVbzIh7KRHCCz9bT0hBGg3-SdP0-G6q4TWeWN08bJe6o7Q7rgIJpkS2NCueVwm40fXbyyx6ijuXAtnrslOuAYxFcdHG8DVc7x54VlB-GL6fYR8CDaIXRUp4fdPxZQFntqnuze_l1t5SvDKcYniF7mC0es16zB3dXMB_K2s-XJ-XVLaLUTqVLG5vK7dM1GdzDXA98kXRYHwbUIhHx2bc1KCWlL_qzQIV2nONRg
```

复制token值进行登录就可以访问了。

![image-20220613115055934](https://cxhello.oss-cn-beijing.aliyuncs.com/image/image-20220613115055934.png)

## 参考文章

> https://blog.csdn.net/MssGuo/article/details/122784433
## dashboard折腾admin权限

### 直接使用rancher商店安装dashboard
参考地址：https://www.cnblogs.com/horizonli/p/10572834.html

在使用rancher安装dashboard的时候，在rancher网页配置界面是可以选择跳过认证和https加密设置的，直接拿到admin权限。


### 给dashboard加admin角色控制
参考地址：https://www.cnblogs.com/RainingNight/p/deploying-k8s-dashboard-ui.html

在安装好dashboard后，登陆网页时候会出现权限问题。这时候可以给dashboard角色控制admin权限。

```
# admin-user.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
  
# 运行admin-user.yaml
kubectl create -f admin-ser.yaml

# admin-user-role-binding.yaml
apiVersion: rbac.authorization.k8s.io/v1beta1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system

# 运行admin-user-role-binding.yaml
kubectl create -f admin-user-role-binding.yaml

```

现在我们需要找到新创建的用户的Token，以便用来登录dashboard：

```
kubectl -n kube-system describe secret $(kubectl -n kube-system get secret | grep admin-user | awk '{print $1}')
```

把name是admin-user的token用来登录，就有admin控制权了。


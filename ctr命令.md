# ctr命令

```javascript
crictl images                查看仓库镜像
ctr i list                   查看
ctr -n k8s.io images ls      查看
ctr i import --all-platforms 镜像包.tar       导入到本地containerd
ctr i push --plain-http=true 镜像:版本        推送到harbor仓库
ctr -n k8s.io i import 镜像包.tar              直接推送到仓库不用导入到docker
ctr -n k8s.io images pull --user tes_puller:6h70Kx2^1Nmx
ctr -n k8s.io images export encrypt-gateway.tar registry.teambition.net/open/tbs-encrypt-gateway:v0.4.3
find . -type f -name "*.tar" -exec ctr -n k8s.io i import {} \;
for i in *.tar; do [ -f "$i" ] && ctr -n k8s.io i import "$i"; done
for i in *.tar; do [ -f "$i" ] && docker load -i "$i"; done
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXQkKBLNVOX19/img/dda0f19b-b641-48d6-bc66-5a7a377ca15b.png)
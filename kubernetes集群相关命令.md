# kubernetes集群相关命令

**kubeadm token create --print-join-command      ##查看本机的json用于加入集群**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4j6OJQNaw4Vgq3p8/img/f4c1e2c8-cfdf-4996-9002-d2dfc57bab75.png)

**kubectl cluster-info        ##获取集群信息**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4j6OJQNaw4Vgq3p8/img/e2a5e5c4-0a89-4a08-b107-c7efb1d36b77.png)

**kubectl get pod -o wide        ##显示pod加入到的节点**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4j6OJQNaw4Vgq3p8/img/70a7bae7-ee96-4b30-ad15-c2a7fcc6e961.png)

**kubectl expose deploy/my-nginx --port 80**  

**##这条命令的意思是在deployment的空间下有个叫my-nginx的服务暴露它的80端口给master主节点，这样外网就可以通过service指定的IP地址和端口来访问nginx服务**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4j6OJQNaw4Vgq3p8/img/fab23f1a-55fa-42c3-b626-5b58eec5a169.png)

**kubectl get svc my-nginx -o go-template --template '{{ .spec.clusterIP }}'**

**##这条命令是用于打印service里的my-nginx的内部IP地址到命令行上**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4j6OJQNaw4Vgq3p8/img/9185c48b-f24f-46a0-939b-c89f253bb1f5.png)

**kubectl explain**

**##这条命令可以看资源清单**
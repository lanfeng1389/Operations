# k8s+v1.16.9升级到v1.16.15

[请至钉钉文档查看附件《upgrade-k8s-1.16.15.tar.gz》。](https://alidocs.dingtalk.com/i/nodes/qnYMoO1rWxD1MedZSOoZKwzeW47Z3je9?doc_type=wiki_doc&iframeQuery=anchorId%3DX02lwsm389xkwnhu1tztv)

```javascript
1. 列出可升级版本
yum list --showduplicates kubeadm --disableexcludes=kubernetes
2. 列出需要下载的镜像
kubeadm config images list --kubernetes-version= v1.16.15
3. 导入到容器里
for i in `ls ./images/*` ;do docker load -i $i; done
4. 安装
yum -y localinstall 3696890bc43ba8aa59f4158b8f3fb23d6f99883d03867f99929c380dd40f99e1-kubelet-1.16.15-0.x86_64.rpm
yum -y localinstall 5323af1096a6deff135ecbc56dbdf8ca305e2ff8e6666d42a8673852f106d1d2-kubectl-1.16.15-0.x86_64.rpm
yum -y localinstall
c0dee0d6cbf2a2269e4d84f7c6e08849f80959a1c3b1425d26535df7b027d7d8-kubeadm-1.16.15-0.x86_64.rpm
yum -y localinstall 14083ac8b11792469524dae98ebb6905b3921923937d6d733b8abb58113082b7-kubernetes-cni-1.1.1-0.x86_64.rpm
yum -y localinstall e382ead81273ab8ebcddf14cc15bf977e44e1fd541a2cfda6ebe5741c255e59f-cri-tools-1.25.0-0.x86_64.rpm
5. 从集群节点移除
kubectl drain master1.teambition.local --ignore-daemonsets --force
6. 检查无报错即可升级
kubeadm upgrade plan
"如果遇见coredns的报错用以下命令忽略"
kubeadm upgrade plan v1.16.15 --ignore-preflight-errors=CoreDNSUnsupportedPlugins
7. 升级
kubeadm upgrade apply v1.16.15
8. 恢复调度
kubectl uncordon master1.teambition.local
9. 添加最大pod数
echo 'KUBELET_EXTRA_ARGS="--fail-swap-on=false --max-pods=500"' > /etc/sysconfig/kubelet
"不然会有以下的报错信息"
Pod Node didn't have enough resource: pods, requested: 1, used: 110, capacity: 110
10. 重启
systemctl daemon-reload && systemctl restart kubelet
11. 检查
kubectl get pod -n kube-system
kubectl version
kubeadm version
```

**从1.16.15升级到1.17.17**

**从1.17.17升级到1.18.20**

**脚本里注意修改镜像地址****registry.teambition.net/library**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/yBRq1Myr2BmWqdv1/img/f8561fce-2263-4e5e-bace-4123b5a52679.png)
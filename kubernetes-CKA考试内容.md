# kubernetes-CKA考试内容

**近两年的题目，该题来自2023年**

1.  **RBAC的创建**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/reference/access-authn-authz/rbac/
    1. 创建指定命名空间
    kubectl create ns app-team1
    
    2. 创建clusterrole指定名字，给予创建权限，可以创建deploy、sts、ds
    kubectl create clusterrole deployment-clusterrole --verb=create --resource=deployments,statefulsets,daemonsets
    kubectl describe clusterrole deployment-clusterrole  ##查看详细
    
    3. 创建serviceaccount权限认证
    kubectl create sa cicd-token -n app-team1
    
    4. 最后绑定到一起
    kubectl create rolebinding cicd-token-binding --clusterrole=deployment-clusterrole --serviceaccount=app-team1:cicd-token --namespace app-team1
    ```
    
2.  **节点维护**
    
    **考试过程中部分题目需要切换到不同的集群来做对应的操作请注意！！！**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#drain
    1. 设置节点为不可调度状态
    kubectl cordon ek8s-node-1
    2. 驱除当前节点的所以pod
    kubectl drain ek8s-node-1 --delete-emptydir-data --ignore-daemonsets --force
    ```
    
3.  **k8s版本升级**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/kubeadm/kubeadm-upgrade/
    根据题目要求升级平面组件、kubelet、kubectl；不升级工作组件etcd、CNI、DNS等
    1. 设置节点为维护状态
    kubectl get no
    kubectl cordon k8s-master
    kubectl drain k8s-master --delete-emptydir-data --ignore-daemonsets --force
    
    2. 升级kubeadm
    ssh k8s-master
    apt update
    apt-cache madison kubeadm ｜ grep 1.19.0
    apt-gat install -y kubeadm=1.19.0-00
    
    3. 验证是否升级成功
    kubectl upgrade plan
    
    4. 开始升级master1节点
    kubeadm upgrade apply v1.19.0 --etcd-upgrade=fales  ##表示不升级etcd
    apt-gat install -y kubectl=1.19.0-00 kubelet-1.19.0-00
    systemctl daemon-reload
    systemctl restart kubelet
    
    5. 放开节点维护并验证
    kubectl uncordon k8s-master
    kubectl get no
    ```
    
4.  **etcd备份和还原**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/tasks/administer-cluster/configure-upgrade-etcd/
    1. 备份etcd
    export ETCDCTL_API=3 
    etcdctl --endpoints=https://127.0.0.1:2379 \
    --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
    snapshot save <backup-file-location>
    ##备份保存到指定的路径和证书的路径
    
    2. 还原
    mkdir -p /opt/backup
    cd /etc/kubernetes/manifests && mv kube-* /opt/backup
    export ETCDCTL_API=3
    etcdctl --endpoints=https://127.0.0.1:2379 \
    --cacert=<trusted-ca-file> --cert=<cert-file> --key=<key-file> \
    snapshot restore <backup-file-location> --date-dir=/var/lib/etcd-restore
    ##回复完成某个数据目录，重新保存到新的数据目录
    
    3. 还原k8s组件
    cd /opt/backup
    vim etcd.yaml
    将volume配置的path路径改成上面指定的新数据目录
    mv /opt/backup/* /etc/kubernetes/manifests/
    systemctl restart kubelet
    ```
    
5.  **NetWorkpolicy网络策略**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/concepts/services-networking/network-policies/
    1.根据题目要求创建指定的目录和命令空间，对ingress做限定，允许所有pod的入站内访问9000端口
    kubectl create ns internel
    vim NetWorkpolicy.yaml
    
    2.编写yaml文件
    apiversion: networking.k8s.io/v1
    kind: NetworkPolicy
    metadata:  
      name: all-port-from-namespace  
      namespace: internel
    spec:  
      ingress:  
      - from:    
        - podSelector: {}    
        ports:    
        - protocol: TCP    
          port: 9000  
      podSelector: {}
      policyTypes:  
      - Ingress
    kubectl apply -f NetWorkpolicy.yaml
    ```
    
6.  **四层负载均衡Service**
    
    ```javascript
    1. 根据题目要求在原来已存在的deploy的文件添加一个80端口，别名http，协议TCP
    kubectl edit deploy front-end
    添加如下配置，主要在name为nginx的容器下
    ports：
    - containerPort: 80
      name: http
      protocol: TCP
    
    2. 暴露deploy下的端口给svc，类型是NodePort
    kubectl expose deploy front-end --name=front-end-svc --port=80 --target-port=http --type=NodePort
    ```
    
7.  **七层负载均衡Ingress**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/concepts/services-networking/ingress/
    1. 根据题目要求名字为pong，命令空间是ing-internal,规则是http路径是/hi，映射到svc的是hi端口是5678
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: pong
      namespace: ing-internal
    spec:
      rules:
      - http:
          paths:
          - path: /hi
            pathType: Prefix
            backend:
              service:
                name: hi
                port:
                  number: 5678
    
    2. 最后运行并验证
    kubectl apply -f 文件
    curl -kl <INTERNAL_IP>/hi
    ```
    
8.  **Deployment扩缩容**
    
    ```javascript
    两种方式修改成题目要求的数量
    kubectl edit deploy loadbalancer
    kubectl scale --replicas=6 deploy loadbalancer
    ```
    
9.  **pod指定节点部署**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes/
    1. 根据题目要求调度到具有disk=spinning这个标签的node上
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx-kusc00401
    spec:
      containers:
      - name: nginx
        image: nginx
        imagePullPolicy: IfNotPresent
      nodeSelector:
        disk: spinning
    kubectl apply -f 文件
    ```
    
10.  **检查node节点健康状态**
    
    ```javascript
    1. 查看节点ready状态
    kubectl get node | grep -i ready   ##-i参数忽略大小写
    2. 查看带有污点的node节点
    kubectl describe node | grep Taint | grep NoSchedule
    他俩总和相减，放到指定目录
    echo 总数 >> /opt/KUSC00402/kusc00402.txt
    ```
    
11.  **一个pod封装多个容器**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/assign-pods-nodes/
    1. 根据题目要求创建多个容器
    apiVersion: v1
    kind: Pod
    metadata:
      name: kusc1
    spec:
      containers:
      - name: nginx
        image: nginx
      - name: redis
        image: redis
      - name: memcached
        image: memcached
      - name: consul
        image: consul
    kubectl apply -f 文件
    ```
    
12.  **持久化存储卷PersistentVolume**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/
    1. 根据题目要求
    apiVersion: v1
    kind: PersistentVolume
    metadata:
      name: app-config
      labels:
        type: local
    spec:
      capacity:
        storage: 2Gi
      accessModes:
        - ReadWriteMany
      hostPath:
        path: "/srv/app-config"
    kubectl apply -f 文件
    ```
    
13.  **PVC挂载**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/tasks/configure-pod-container/configure-persistent-volume-storage/
    1. 首先创建pvx
    apiVersion: v1
    kind: PersistentVolumeClaim
    metadata:
      name: pv-volume
    spec:
      storageClassName: csi-hostpath-sc
      accessModes:
        - ReadWriteOnce
      resources:
        requests:
          storage: 10Mi
    kubectl apply -f 文件
    
    2. 创建pod
    apiVersion: v1
    kind: Pod
    metadata:
      name: web-server
    spec:
      volumes:
        - name: pv-volume
          persistentVolumeClaim:
            claimName: task-pv-claim
      containers:
        - name: nginx
          image: nginx
          volumeMounts:
            - mountPath: "/usr/share/nginx/html"
              name: pv-volume
    kubectl apply -f 文件
    
    3. 扩容大小，扩充存储到70Mi
    kubectl edit pvc pv-vloume
    kubernetes.io/change-cause: 'resize'  ##注解，如果没有加上意思是允许进行修改
    或者是
    kubectl patch pvc pv-volume -p '{"spec":{"resources":{"requests":{"storage": "70Mi"}}}}' --record
    ##-p意思是修改 --record是记录的意思
    ```
    
14.  **监控pod日志**
    
15.  **Sidecar代理**
    
    ```javascript
    参考官方文档
    https://kubernetes.io/zh-cn/docs/concepts/cluster-administration/logging/
    1. 创建pod批量监控日志，执行个命令，挂载临时目录
    apiVersion: v1
    kind: Pod
    metadata:
      name: legacy-app
    spec:
      containers:
      - name: count
        image: busybox
        args:
        - /bin/sh
        - -c
        - >
          i=0;
          while true;
          do
            echo "$i: $(date)" >> /var/log/legacy-app.log;
            i=$((i+1));
            sleep 1;
          done      
        volumeMounts:
        - name: logs
          mountPath: /var/log
      - name: count-log-1
        image: busybox
        args: [/bin/sh, -c, 'tail -n+1 -F /var/log/1.log']
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      - name: count-log-2
        image: busybox
        args: [/bin/sh, -c, 'tail -n+1 -F /var/log/2.log']
        volumeMounts:
        - name: varlog
          mountPath: /var/log
      volumes:
      - name: logs
        emptyDir: {}
    
    ```
    
16.  **监控pod度量指标**
    
    ```javascript
    1. 根据题目要求把占用率最大的pod追加到一个文件里
    kubectl top pod -A -l name=cpu-user 
    ##top是查看cpu和内存使用情况的
    echo "cordens-xxxxxx" >> /opt/KUTR00401/KUTR00401.txt
    ```
    
17.  **集群故障排查—kubelet故障**
    

```javascript
systemctl status kubelet
systemctl start kubelet
systemctl enable kubelet
```
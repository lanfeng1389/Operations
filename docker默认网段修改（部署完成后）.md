# docker默认网段修改（部署完成后）

```javascript
一、默认网桥修改docker0
1. 备份原有的json文件
cp -r /etc/docker/daemon.json ~/
2. 原有文件添加参数
"bip": "10.20.0.1/16",
3. 停止全部容器
systemctl stop kubelet  ##这一步必须做不然容器会自动重启
docker stop $(docker ps -aq)
docker ps  ##查看是否全部停止
systemctl stop docker
4. 删除默认网桥
ip link del dev docker0
5. 重建网卡，重启容器
systemctl daemon-reload
systemctl restart docker
docker network inspect bridge  ##查看是否成功
systemctl restart kubelet

二、nginx-br修改
6. 删除默认nginx网桥
docker network ls
docker network rm nginx_default
7. 删除原本容器以及镜像
docker rm ID
docker rmi NAME:版本
8. 新建网桥
docker network create --driver bridge --subnet=10.21.0.1/16 nginx_default
9. TES脚本
bash install.sh.v1.0.9-v12.113 
10. 查看是否生效
docker network inspect nginx_default
```
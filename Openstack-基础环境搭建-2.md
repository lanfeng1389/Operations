# 基础环境搭建-2

1.  **三台机器****\-centos8**
    

[**https://docs.openstack.org/install-guide/environment-ntp.html**](https://docs.openstack.org/install-guide/environment-ntp.html)

**官方文档基础环境搭建步骤**

**一、网络规划**

```javascript
是为了每台机器互通能访问外网
每台服务器需要准备两个网卡，必须是有线的桥接，不是Wi-Fi的无限桥接
1. 设置主机名
hostnamectl set-hostname master1/node1/node2
2. 设置IP地址
TYPE=Ethernet
NAME=eth32
DEVICE=eth32
ONBOOT=yes
IPADDR=192.168...
NETMASK=255.255.255.0
GATEWAY=192.168...
DNS1=114.114.114.114
nmcli con reload
nmcli con up eth32
ifconfig eth32
3. 设置host解析
vim /etc/hosts   IP+主机名
4. 关闭防火墙和selinux
systemctl disable firewalld --now
sed -i 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/selinux/config
setenforce 0
5. 配置密钥解析(控制节点)
ssh-keygen
ssh-copy-id -i /root/.ssh/id_rsa.pub master1/node1/node2   ##自己也给个
```
```javascript
是为了保障三台服务器时间一致
1. 安装启动
yum -y install chrony
systemctl enable chronyd --now
2. 修改配置文件
vim /etc/chrony.conf
----------(控制节点)----------
注释Please consider joining the pool第三行配置，上层服务器
打开Allow NTP client access from local network下的配置，设置成哪些计算机能向我同步
打开Serve time even if not synchronized to a time source下的注释时间以自己为准
systemctl restart chronyd
----------(计算节点)----------
打开第三行上层服务器
pool master1 iburst        ##表示以控制节点时间为准
systemctl restart chronyd
chronyc sources -v         ##查看时间源
```
```javascript
安装库才能安装使用openstack各个组件
1. 安装U版
yum -y install centos-release-openstack-ussuri   ##拉取库
yum config-manager --set-enabled powertools      ##开启powertools库
yum -y upgrade
reboot
2. 安装客户端和selinux
yum -y install python3-openstackclient
yum -y install openstack-selinux
```
```javascript
是因为所以的元数据都存储在数据库里
1. 安装
yum -y install mariadb mariadb-server python2-PyMySQL
2. 配置
vim /etc/my.cnf.d/openstack.cnf
[mysqld]
bind-address = 192.168...

default-storage-engine = innodb         ##开启最大连接数和字符集
innodb_file_per_table = on
max_connections = 4096
collation-server = utf8_general_ci
character-set-server = utf8
3. 启动
systemctl enable mariadb.service --now
4. 安全加固
mysql_secure_installation
一、请输入root密码：
二、是否需要输入密码：
三、请输入密码：
四、是否移除匿名用户：y
五、不允许远程用户登录mysql：y
六、是否移除测试数据库：y
七、是否加权特权的表：y
systemctl restart mariadb.service
```
```javascript
是因为请求会很多需要消息队列功能来排队
1. 安装、启动
yum -y install rabbitmq-server
systemctl enable rabbitmq-server.service --now
2. 创建用户
rabbitmqctl add_user openstack 密码
rabbitmqctl list_users                                 ##查看用户
rabbitmqctl set_permissions openstack ".*" ".*" ".*"   ##配置权限
rabbitmqctl list_permissions 
```
```javascript
主要是用于keystone身份验证做token缓存用的
1. 安装
yum -y install memcached python3-memcached
2. 修改配置添加监听
vim /etc/sysconfig/memcached 
OPTIONS="-l 127.0.0.1,::1,master1"            ##添加解析过的主机名
systemctl enable memcached.service --now
```
```javascript
作用是用来动态更新所有的信息来保障数据的一致性
1. 安装
yum -y install etcd
2. 修改配置
vim /etc/etcd/etcd.conf
#[Member]
ETCD_DATA_DIR="/var/lib/etcd/default.etcd"
  ETCD_LISTEN_PEER_URLS="http://192.168...:2380"          ##通信监听地址
ETCD_LISTEN_CLIENT_URLS="http://192.168...:2379"          ##监听的客户端URL
ETCD_NAME="master1"
#[Clustering]
ETCD_INITIAL_ADVERTISE_PEER_URLS="http://192.168...:2380" ##通告地址
ETCD_ADVERTISE_CLIENT_URLS="http://192.168:2379"      ##对外公告该节点客户端地址
ETCD_INITIAL_CLUSTER="master1=http://192.168...:2380"     ##初始配置
ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster-01"              ##集群的ID
ETCD_INITIAL_CLUSTER_STATE="new"                          ##初始状态
systemctl enable etcd --now
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/XNkOMMZ3oJo8OY74/img/dab3b6c9-443f-4fd6-a1da-4e52f46d11b2.png)
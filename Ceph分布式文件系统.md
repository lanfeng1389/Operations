# Ceph分布式文件系统

1.  **简介**
    
    _Ceph是一种为优秀的性能、可靠性和可扩展性而设计的统一的、_[_分布式文件系统_](https://baike.baidu.com/item/%E5%88%86%E5%B8%83%E5%BC%8F%E6%96%87%E4%BB%B6%E7%B3%BB%E7%BB%9F/1250388?fromModule=lemma_inlink)_。使用C++语言开发，遵循LGPL协议开源_
    
2.  **优势**
    

**高延展性**：使用普通的x86服务器，支持10～1000台服务器，支持TB到EB级别的扩展

**高可靠性**：没有单点故障，多数据副本，自动管理，自动修复

**高性能**：数据分布均衡

Ceph可用于块设备存储，对象存储，文件系统存储

**3.Ceph架构**

**rados：**基础存储系统RADOS，所有存储在Ceph系统中的用户数据事实上最终都是由这一层来存储的，优势都是由这一层所提供的，++rados是一个对象存储系统，所以librados实现的API也只是针对对象存储系统++

**librados：**基础库这一层功能是对rados进行抽象和封装，并向上提供API，以便基于rados进行应用开发

**高层应用接口**

**radosgw：**对象网关接口（对象存储）

**rbd：**块存储

**cephfs：**文件系统存储

其作用是这librados库的基础上提供抽象层次更高，更便于应用或客户端使用的上层接口

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/pgQoWkQJxFxDjnQM/6bb912c2b9694622bf539c777598fe293437.png)

**4.Ceph基本组件**

**一. OSD**

主要是用于集群中所有数据与对象存储。处理集群数据的复制，恢复，回填，再均衡。并向其他osd守护进程发送心跳，然后向mon提供监控信息。

至少要有两个osd守护进程，集群状态才能达到active+clean状态

**二. MDS（可选）**

为Ceph文件系统提供元数据计算，缓存与同步。元数据也存储在OSD节点中，mds类似于元数据的缓存代理器。但不是必须的，只有文件系统存储才需要配置MDS状态

**三. Monitor**

监控集群的整个状态，维护集群的cluster MAP二进制表，保证集群数据的一致性。

cluster MAP里面存储的是一些OSD的网络地址，各个OSD的状态

**四. Manager（ceph-mgr）**

用于收集ceph集群的状态，运行指标，比如存储的利用率，当前性能指标和系统负载。相当于提供了一个web UI的图形界面

**Ceph结构包含两个部分**

**ceph client：**访问ceph底层服务或组件，对外提供各种接口

**ceph node：**底层服务提供端，也就是ceph存储集群

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/pgQoWkQJxFxDjnQM/d4659a44bfae4763ae54e1e8233fab473437.png)

**5. Ceph工作原理**

file文件相当于要存储的++**数据（DATA）**++，存储数据时会分成++**对象（Object）**++，分成的大小通常是由管理员调整的。每一个对象会分一个++**oid（对象ID）**++，由ino（文件ID全局的唯一标识）和ono（分片的编号）结合

通常数据不会直接存储到OSD会很慢，如果损坏了无法进行数据迁移，所以就引用了PG的概念

++**PG是一个逻辑概念**++，在linux中能直接看见对象但无法看见PG。当我们想寻找一个对象时，只需要找到这个对象所属的PG然后遍历这个PG就可以了，无需遍历所有对象。在做数据迁移时，也是以++PG作为基本的单元进行迁移，Ceph不会直接操作对象++

_**对象是如何映射到PG里的？**_

是通过哈希算法得出一个值对OID做hash取得特征码，用特征码与PG的数量去模，得出的序号就是pgid。PG就会根据管理员设置的副本数量进行一个复制，通过CRUSH这个算法储存到不同的OSD上面形成一个主副关系

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/pgQoWkQJxFxDjnQM/0627878ebadb4245bfc83e50d95d91bf3437.png)

**6. 安装Ceph集群**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/pgQoWkQJxFxDjnQM/b46f62decb29489c9977c44f2e9b13b63437.png)

```javascript
1. 至少需要四台机器，每台机器准备一个空闲盘供OSD存储
2. 控制节点安装ceph-deploy命令自动去node节点安装一些命令，其中一台node节点同时安装mon监控，mgr UI界面dashboard和osd存储。和最后一个客户端
3. 相互做解析。修改主机名
vim /etc/hosts
hostnamectl set-hostname 主机名
4. 关闭防火墙和selinux
systemctl stop firewalld && systemctl disable firewalld
sed -ri '/^SELINUX=/cSELINUX=disabled' /etc/selinux/config 
setenforce 0
5. 所以节点创建普通用户并设置密码，设置root权限
useradd cephu
echo 1 | passwd --stdin cephu
visudo添加
cephu ALL=(root) NOPASSWD:ALL
6.设置免密登录
su - cephu
ssh-keygen
ssh-copy-id cephu@各个主机名
```

```javascript
1. 添加配置文件并做设置，就能用ceph-deploy命令了
mkdir ~/.ssh
vim ~/.ssh/config
在里面添加以下内容控制node节点
Host node1
Hostname node1
User cepuh    ##依次类推有几个node节点就填几个
2. 配置yum源,安装ceph-deploy
vim /etc/yum.repo.d/cuphu.repo
[cuph-noarch]
name=cuph-noarch
baseurl=https://download.ceph.com/rpm-luminous/el7/noarch
enabled=1
gpgcheck=1
type=rpm-md
gpgkey=https://download.ceph.com/keys/release.asc
yum makecache  ##建立源
yum update -y  ##更新源
vim /etc/yum.conf  ##开启缓存
keepcache=1
yum -y install ceph-deploy
3. 配置ntp时间同步
yum -y install ntp
vim /etc/ntp.conf
##有4行server位置，把它注释掉添加下面两行
server 127.127.1.1 #loacl clock
fudge  127.127.1.1 stratum 10
systemctl start ntpd && systemctl enable ntpd
##其他所有节点添加客户端
yum -y install ntpdate
ntpdate 时间服务器IP地址
```

```javascript
1.创建cephu操作目录
su - cephu
mkdir my-cluster
##之后所有的ceph-deploy命令都在该目录下执行
2.创建集群
##下载python
https://files.pythonhosted.org/packages/5f/ad/1fde06877a8d7d5c9b60eff7de2d452f639916ae1d48f0b8f97bf97e570a/distribute-0.7.3.zip
unzip distribute-0.7.3.zip && cd distribute-0.7.3
sudo python setup.py install ##安装
cd my-cluster/
ceph-deploy new node1  ##创建集群
官网脚本自动安装ceph和ceph-radosgw主包
ceph-deploy install --release luminous node1 node2 ceph-client
ceph --version  ##查看版本
3.初始化mon
ceph-deploy mon create-initial
4.赋予各个节点使用命令免用户名权限
ceph-deploy admin node1 node2 ceph-client
5.安装ceph-mgr 只有luminous才有，为dashboard做准备
ceph-deploy mgr create node1
6.添加osd
ceph-deploy osd create --data /dev/sdb node1
ceph-deploy osd create --data /dev/sdb node2
ssh node1 lsblk -f ##查看
ssh node1 sudo ceph -s  ##查看集群状态
```

```javascript
###mgr配置到了哪就在哪配置dashboard，所以在node1
1.创建管理域密钥
sudo ceph auth get-or-create mgr.node1 mon 'allow profile mgr' osd 'allow *' mds 'allow *'
创建成功会生成一个key
2.开启ceph-mgr管理域
sudo ceph-mgr -i node1
sudo ceph status  ##确认mgr状态为active
3.打开dashboard模块,绑定ceph-mgi的IP地址
sudo ceph mgi module enable dashboard
sudo ceph config-key set mgr/dashboard/node1/server_addr/192.168.20.231
ceph-mgi的IP➕7000端口即可访问dashboard前台
到这ceph分布式集群部署完成
```

```javascript
##配置客户端使用rbd也就是块存储，存储相关要在mon的节点也就是node1节点
1.创建存储池
sudo ceph osd pool create rbd 128 128
##设置PG的数量根据osd大小可设置128，512，4096等等
2.初始化存储池
sudo rbd pool init rbd
3.为client安装ceph
yum -y install python-setuptools
vim /etc/yum.conf  ##开启缓存
keepcache=1
yum clean all
4.修改client下该文件的读权限
sudo chmod +r /etc/ceph/ceph.client.admin.keyring
5.修改client的ceph配置文件：这一步是为了解决映射镜像是出错
sudo vim /etc/ceph/ceph.conf
增加rbd_default_features = 1
6.创建块设备镜像
rbd create foo --size 4096
7.映射镜像到主机
sudo rbd map foo --name client.admin
8.格式化块设备
sudo mkfs.ext4 -m 0 /dev/rbd/rbd/foo
9.client节点mount块设备
sudo mkdir /mnt/ceph-block-device
sudo mount /dev/rbd/rbd/foo /mnt/ceph-block-device
客户端如果重启了要重新做映射，不然会很卡很慢
```

_**Ansible管理Ceph分布式文件系统**_

```javascript
1. 初始化
关闭防火墙和selinux
配置硬盘fdisk /dev/vda
创建普通用户cephu和student   配置密码
授予root权限，配置visudo
ssh-keygen      ssh-copy-id student@node1,node2,ceph-client  ##在控制节点做
ssh-keygen      ssh-copy-id cephu@node1,node2,ceph-client
登录student配置免密
su - student
for i in master1 node1 node2 ceph-client ; do ssh-copy-id student@$i ; ssh-copy-id cephu@$i ; done   ##循环

2. 安装配置ansible相关文件
yum -y install epel-release
yum -y install ansible
配置资产清单文件
在/etc/ansible/hosts
[mons]
node[1:2]

[mgrs]
node[1:2]
ansible -m ping mons  ##ping一下是否是通的
```
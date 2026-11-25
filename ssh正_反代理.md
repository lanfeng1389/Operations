# ssh正/反代理

++**该案例是**++++**北京站**++++**点有两台机器想访问到**++++**黑龙江站**++++**点的10.44.1.2这台服务器该怎么实现**++

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Lk3lbVGXxo7plm96/img/a91daedd-2698-4d3a-bc30-4303c055690a.png)

```javascript
##SNAT源IP地址的转换，DNAT目标IP地址的转换
1. 配置源101.163.9.100的路由器SNAT
sys
//进入全局配置模式
int ge2/0
//进入接口 GE2/0 的配置模式，这个接口连接了北京站点的内部网络
ip add 172.16.33.100 24
//设置地址
acl basic 2000
//创建一个基本 ACL（访问控制列表）编号为2000
rule permit source 172.16.0.0  0.0.255.255
//设置规则允许172.16.0.0的网段段数据包通过
int g1/0
nat outbound 2000
save
//ACL2000应用于接口 G1/0 的出站流量，保存。
nat server protocol tcp globol 101.163.9.100 22222 inside 172.16.33.1 22
//该命令是通过地址转换从外网的IP+端口转发到内网的地

2. 配置源服务器centos8网段
nmcli con add con-name static ifname eth0 type ethernet ipv4.addresses 172.16.33.1/24 ipv4.gateway 172.16.33.100 ipv4.dns 114.114.114.114 ipv4.method man
//配置地址、网关、DNS
nmcli con up static
//启动
ip a show eth0
ip -4 route show
cat /etc/resolv.conf
//检查

3. 配置源服务器windows10网段
//打开设置——控制面板——网络和Internet——网络共享中心——以太网——属性——找到Internet协议版本4（TCP/ipv4）——最后设置地址、掩码、网关、DNS
##打开cmd，ping网络通即可

4. 配置目标路由器、jump、centos8
##jump-server堡垒机需要配置两个地址
一、外网地址
nmcli con add con-name static ifname eth0 type ethernet ipv4.addresses 172.16.34.101/24 ipv4.gateway 172.16.34.100 ipv4.dns 114.114.114.114 ipv4.method man
nmcli con up static
二、内网地址
nmcli con add con-name internel ifname eth0 type ethernet ipv4.addresses 10.44.1.1/24 ipv4.method man
nmcli con up internel
//内网不需要网关和DNS所以不需要配置

##centos8的配置
nmcli con add con-name static ifname eth0 type ethernet ipv4.addresses 10.44.1.2/24 ipv4.method man
nmcli con up static
##路由配置同上面👆源一样
```
```javascript
#前面已经配置了源和目录的NAT转换
#172.16.33.1这台北京的服务器可以通过ssh连接到101.163.8.107:52013会直接转发到172.16.34.101:22堡垒机这台服务器，所以即可
登录到北京这台服务器
ssh -L 55555:10.44.1.2:22 -p 52013 root@101.163.8.107
//-L意思是端口映射，这段命令的解释是把目标要连的10.44.1.2:22封装到了101.163.8.107:52013端口上面，这样会在本机开一个55555的端口（本机的这个端口随意起即可只要没有被占用）
ssh 127.0.0.1 -p 55555
//这样就可以通过该端口映射到目标10.44.1.2的22端口上即可实现访问，实际是jump-server通过代理的方式帮我去建立了连接进而访问目标服务器
##这块就会有个问题，如果jump-server断开了一退出那北京那台服务器就连不上黑龙江的服务器了，会在反向代理这块讲解怎么处理
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Lk3lbVGXxo7plm96/img/dbf2186e-0e56-4272-be6b-10ff122d1366.png)

```javascript
##反向代理有个前提是内网要能访问Internet
##部分客户认为在公网开一个端口会不安全有风险，这时候就要用到了反向代理相对更安全
登录到jump-server服务器
ssh -fCNR 12345:10.44.1.2:22 -p 22222 root@101.163.9.100
//-f表示在后台执行（加了这个参数就算断开退出了也不会有影响），-R指定地址，-N不执行远程操作（常用于端口转发），-C表示请求所有流量
//这段命令的解释是在本机开了一个12345的端口用于代理，反向代理到了源目标主机也就是172.16.33.1这台服务器
ssh -p 12345 127.0.0.1
//这样连接本机的12345端口就会反向代理到目标的10.44.1.2:22服务器，也是jump-server代我去建立的连接
##这块还要一个问题，虽然代理放在了后台执行，但服务器一重启就会断开
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Lk3lbVGXxo7plm96/img/26a1e3d5-6ab0-46a7-8236-47b3ceed9b9a.png)

```javascript
在目标服务器上配置也就是jump-server，服务器就算重启代理也不会挂掉
1. 配置免密
ssh-keygen
ssh-copy-id -p 22222 root@101.163.9.100
2. 创建systemd文件
vim /usr/lib/systemd/system/ssh-agent.service
[Unit]
Description=ssh agent
After=network.target

[Service]
Type=forking
ExecStart=/bin/sh -c '/usr/bin/ssh -fNCR 12345:10.44.1.2:22 -p 22222 root@101.163.9.100'

[Install]
WantedBy=multi-user.target
3. 启动服务
systemctl daemon-reload
systemctl enable ssh-agent --now
systemctl status ssh-agent
netstat -tunlp
```
```javascript
1. openssh-clients使用
#配置文件在/etc/ssh/ssh_config
#openssh-server配置文件在/etc/ssh/sshd_config
默认时候有的linux系统没有～/.ssh文件需要创建
mkdir ~/.ssh && chmod 700 ~/.ssh
touch ~/.ssh/config && chmod 600 ~/.ssh/config
#在某些场景下客户这边没有DNS服务器，没办法做解析，想通过ssh连接该怎么办？
Host 主机
  user 用户名
  hostname IP地址
  port 端口
//这是最简单的使用方法，ping是不通的因为没有做解析，但ssh直接连主机是可以的

2. 常用参数讲解
ProxyJump 
#用于指定一个或多个跳板服务器，以便 SSH 客户端可以通过这些服务器跳转到最终的目标服务器
StrictHostKeyChecking
ask //默认参数询问用户是否信任未知的主机密钥，同时远程服务器公钥改变会拒绝连接
yes //不会自动增加到～/.ssh/known_hosts，同时远程服务器公钥改变会拒绝连接
no  //自动增加新的主机键到～/.ssh/known_hosts
#用于控制SSH客户端在连接到未知主机时的行为，特别是在首次连接时
//截图示例我们需要登录到61.162.220.50这台服务器，很明显内网网段不一样是不能直接ssh连过去，通过proxyjump这个参数让centos1帮我们去连centos2

3. 扩展
~/.kube/config是跟k8s相关的证书，访问apiserver需要证书
/etc/kubernetes/admin.conf
#跟这个目录下是一样的，但有时会存在没有权限的情况。所以可以看本机的.kube
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Lk3lbVGXxo7plm96/img/c7bae13b-4f34-492b-954e-3dbebd8fd191.png)
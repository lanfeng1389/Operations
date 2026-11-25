# IP地址的配置

**Windows系统配置IP地址**

```javascript
1、按下键盘的“Win键+R键”，在弹出的“运行”对话框中输入“ncpa.cpl”回车。
2、在新窗口中查找“本地连接”或者“以太网”图标，右键点击该图标，点击“属性”选项。
3、在新窗口中查找“Internet 协议版本4 （TCP/IPv4）”项目，双击，选择“使用下面的IP地址”，按照格式填入IP地址，子网掩码，默认网关，然后同理填入DNS服务器地址。
```

 **Linux配置IP地址**

```javascript
1. 第一种方式
前提是NetworkManager运行nmcli命令才能用
nmcli con add con-name network ifname eth0 type ethernet ipv4.addresses 172.24.10.150/24  ipv4.gateway  172.24.10.100  ipv4.dns 172.24.10.254   ipv4.method man
#配置名字、eth0、类型、IP、子网掩码、网关、DNS
nmcli con up network        #加载生效
  
2. 第二种方式
cd /etc/sysconfig/network-scripts/  网卡配置文件，找到对应的网卡进行修改，主要修改以下：
TYPE=Ethernet
BOOTPROTO=static
ONBOOT=yes
IPADDR
NETMASK
GATEWAY
DNS1
修改完以后systemctl restart network 重启网卡服务

3. 第三种方式
ifconfig  eth0 down/up    停止/启动
ifconfig eth0 192.168.1.100 netmask 255.255.255.0 gateway 192.168.1.1    配置
```
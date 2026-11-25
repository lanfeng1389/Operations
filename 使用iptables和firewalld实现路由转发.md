# 使用iptables和firewalld实现路由转发

**该案例在两个地域假设蓝色的在北京，橙色的在上海**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/54Lq3Rkv9orgn7Ed/img/fb386a30-d77d-4573-a686-562df71f7b20.png)

```javascript
##使用iptables实现SNAT功能并持久化
//该操作在101.163.9.102路由服务器
1. 关闭防火墙
systemctl disable firewalld --now       
2. 开启路由转发功能
echo "net.ipv4.ip_forward = 1" >> /etc/sysctl.conf
sysctl -w net.ipv4.ip_forward=1
3. 设置规则
iptables -t nat -A POSTROUTING -s 172.16.12.0/24 -o eth0 -j SNAT --to-source 101.163.9.102
//该命令是数据包在离开前把源IP地址转换成目标IP，该方式适合于'固定公网IP'
iptables -t nat -A POSTROUTING -s 172.16.12.0/24 -o eth0 -j MASQUERADE
##MASQUERADE意思是伪装，他的作用是从服务器的网卡上，自动获取当前IP地址来做NAT
//该方式适合于'动态公网IP'
iptables -L -t nat -n                    #查看
4. 持久化
iptables-save > /etc/sysconfig/iptables  
yum -y install iptables.service          #centos8以后的版本需要安装
systemctl enable iptables.service --now
//如果是Centos-stream2想访问公网需要经过两次NAT地址转换，这时候就需要在Centos-stream1这台服务器上配置iptables规则和开启路由转换功能，这样172.16.13.2就会转换成172.16.12.1在转换成101.163.9.102即可访问到公网

##使用firewalld实现SNAT功能并持久化
//该操作在101.163.9.101路由服务器
firewall-cmd --add-masquerade --permanent      #永久生效
firewall-cmd --reload                          #立即生效
//firewall会自动开启路由转换功能
```
```javascript
##使用iptables实现DNAT功能，允许公网用户访问到内网的两台机器
//该操作在101.163.9.102路由服务器
1. 通过配置DNATssh访问到172.16.12.1
iptables -t nat -A PREROUTING -d 101.163.9.102 -p tcp --dport 22222 -j DNAT --to-destination 172.16.12.1:22
2. 通过配置静态路由访问到172.16.13.2
nmcli com modify eth1-static +ipv4.routes "172.16.13.0/24 172.16.12.1"
//因为这俩服务器之间没有任何连接，需要指定下一跳，这样数据包将首先发送到这个路由器，然后再由该路由器负责将数据包转发到目标子网
nmcli com up eth1-static
ip route show 

##使用firewalld实现DNAT功能
//该操作在101.163.9.101路由服务器
firewall-cmd --add-forward-port=port=22222:proto=tcp:toport=22:toaddr=172.16.14.1 --per
//表示添加一条转发端口，源端口22222，协议tcp，转发到目标172.16.14.1:22端口并永久生效
firewall-cmd --reload
```
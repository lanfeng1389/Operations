# win共享存储方式

前提：windows和linux系统之间的网络互通

```javascript
1. 可以互相ping一下
2. linux服务器上telnet winIP 445
3. 确保关闭了防火墙和selinux
systemctl disable firewalld --now
setenforce 0
```

**一、windows系统操作流程**

1.  **开启共享**
    
    **步骤：**
    
    找到需要共享的磁盘—右键属性—共享—高级共享—选择共享此文件夹—填入共享名字—划分所需要的权限读写
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyeZVzpRRonLb/img/9371507c-a59c-4b33-8f75-2cccb6b06659.png)
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyeZVzpRRonLb/img/fc7db232-c7d1-4c8c-b5f3-0ed5b794446e.png)
    
2.  **启动SMB功能**
    
    **步骤：**
    
    打开控制面板—程序和功能—启动或关闭windows功能——找到SMB勾选
    
    此时会提示让重启电脑，需要重启一下（如果SMB已开启则不需要）
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyeZVzpRRonLb/img/4e9c22b0-1097-4532-9c8e-1da70273a83f.png)
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyeZVzpRRonLb/img/492c3cc8-c6fb-482f-b07a-b1c47773df00.png)
    
3.  **其他需要注意的事项**
    

```javascript
打开cmd命令行
1. 查看共享资源
ipconfig
net view \本机IP

2. 确保server和workstation开启状态
net query LanmanServer
net query LanmanWorkstation

3. 查看防火墙
确保windows防火墙上允许SMB流量通过
可以通过Windows Defender防火墙—高级设置添加入站规则允许TCP端口445通过
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyeZVzpRRonLb/img/23cc091b-b827-493c-b510-c6173eb22eb2.png)

**二、linux系统操作流程**

```javascript
1. 安装cifs
apt-get update
apt install -y cifs-utils  smbclient

2. 测试是否通
smbclient -L //winIP -U 登录账号%登录密码

3. 创建凭据文件
vim /root/smbcredentials
username=Administrator
password=qw@123     //文件内容
chmod 600 /root/smbcredentials

4. 挂载
mkdir /data    //挂载目录自定义
mount -t cifs //192.168.0.141/windowsD /data -o credentials=/root/smbcredentials
df -Th | grep data

5. 永久挂载
vim /etc/fstab
//192.168.0.141/windowsD /data cifs credentials=/root/smbcredentials,iocharset=utf8,file_mode=0777,dir_mode=0777 0 0
mount -a   //加载
6. 查看
df -Th | grep 挂载路径名称
```

![image.jpeg](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyeZVzpRRonLb/img/ce5c01d2-5025-4156-b6c6-0a5ee65a78f2.jpeg)
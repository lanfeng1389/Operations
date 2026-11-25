# Openssh远程连接

1.  _**Openssh有两种连接方式密码和密钥**_
    

**yum -y install openssh-server安装**

_**一.密码**_

```javascript
连接方式：ssh username@目标IP  -p 端口
ssh默认端口是22    在/etc/ssh/sshd_config可以进行修改端口号
systemctl restart sshd    重启
netstat -anput | grep sshd    查看
```

_默认加密都是SHA256方式，会提示出现交互，相比密钥的方式不安全，易泄漏暴力破解密码_

客户端使用w命令能查看远程主机谁连接的我，或ps -ef ｜ grep sshd查看全部sshd的信息

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/6EZVP8NgNhVbBWA9/58459be9dfb04f4bb70fafa52192e02e3437.png)

_**不管有没有连接上系统会默认在～/.ssh/下生成一个known\_host的文件夹，存放的是要连接主机的记录表**_

_**二.密钥**_

**支持rsa和dsa加密**

**客户端配置加密方法**    ++生成密钥：ssh-keygen++

                     ++上传密钥：ssh-copy-id   -i   公钥  remoteUSER@remoteIP++

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/6EZVP8NgNhVbBWA9/2b43dfb414e545efa6daa289979107433437.png)

_**其中id\_rsa私钥，id\_rsa.pub公钥**_

**注意**

**成功连接上以后服务端在～/.ssh/会生成authorized\_keys文件，里面的内容跟客户端的公钥是一模一样**

**如何是考虑安全问题只需要两台机器相互远程办公连接可以吧PermitEmptyPasswords参数打开，不然会存在安全隐患**

**总结**

**只有持有私钥的用户才能远程登录目标主机不需要输入密码验证，相比密码要安全更加灵活可靠**
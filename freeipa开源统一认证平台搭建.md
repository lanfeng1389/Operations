# freeipa开源统一认证平台搭建

```javascript
setenforce 0
sed -i 's/^SELINUX=.*/SELINUX=permissive/g' /etc/selinux/config
systemctl disable firewalld --now
echo "192.168.20.207 ipa.zla.com" >> /etc/hosts
hostnamectl set-hostname ipa.zla.com
yum -y update
yum -y install @idm:DL1        ##认证服务里的一个
yum -y install freeipa-server
ipa-server-install
```
```javascript
yum -y install ipa-client
echo "192.168.20.207 ipa.zla.com" >> /etc/hosts
hostnamectl set-hostname centos.zla.com
ipa-server-install  --hostname=`hostname -f` \
--mkhomedir \   ##本地有用户自动创建家目录
--server=ipa.zla.com \
--domain zla.com \
--realm ZLA.com
```
```javascript
命令行方式管理在IPA server添加用户
kinit admin
klist
ipa config-mod --defaultshell=/bin/bash
ipa user-add NAME --first=Test --last=User --password

卸载
ipa-server-install --uninstall
ipa-client-install --uninstall 
```
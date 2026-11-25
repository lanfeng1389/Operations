# 升级linux内核版本

```javascript
1.查看当前内核版本
uname -r
2.导入key
rpm --import https://www.elrepo.org/RPM-GPG-KEY-elrepo.org
3.安装elrepo的yum源
rpm -Uvh http://www.elrepo.org/elrepo-release-7.0-2.el7.elrepo.noarch.rpm
4.查看可用的系统内核包
yum --disablerepo="*" --enablerepo="elrepo-kernel" list available
5.安装对应的内核版本
yum --enablerepo=elrepo-kernel install kernel-ml-devel kernel-ml -y
6.查看内核默认启动顺序
awk -F\' '$1=="menuentry " {print $2}' /etc/grub2.cfg
7 默认启动顺序是0，要重新选择0
grub2-set-default 0
reboot
uname -r
yum -y remove kernel ##删除旧的内核版本
```
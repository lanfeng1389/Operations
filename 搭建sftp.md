# 搭建sftp

```javascript
1. 安装
yum/apt install -y openssh-server

2. 创建用户/组
groupadd sftp  //名字随意
useradd -m -g sftp sftp //加入到组里
passwd  sftp

3. 编辑配置文件
vim /etc/ssh/sshd_config //末尾添加以下内容
Match Group sftp
    ChrootDirectory %h
    ForceCommand internal-sftp
    AllowTcpForwarding no
    X11Forwarding no
systemctl restart sshd

4. 增加权限
mkdir -p /home/sftp/upload
chown root:root /home/sftp
chmod 755 /home/sftp
chown sftp:sftp /home/sftp/upload
chmod 700 /home/sftp/upload

5. 测试
sftp sftp@hostIP
```
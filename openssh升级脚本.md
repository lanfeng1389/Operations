# openssh升级脚本

```javascript
which    //检查一下路径在看是否需要配置环境变量
export PATH=/usr/bin:/usr/local/bin:$PATH 
source ~/.bash_profile
```
```javascript
#!/bin/bash
#openssh升级流程,适用于首次升级使用
#推荐系统centos7.9，需要能够访问internet
#openssh下载地址http://www.openssh.com/portable.html
#openssl下载地址https://github.com/openssl/openssl/releases
CCFLAGS="-I/usr/local/include"
LDFLAGS="-L/usr/local/lib64"
function check()
{
if [ $? = 0 ]
then
echo -e "\033[32m${1}======>success\033[0m"
sleep 3
else
echo -e "\033[31m${1}======>fail\033[0m"
exit 1
fi
}
echo '1. 升级openssl到3.2.0'
wget https://github.com/openssl/openssl/releases/download/openssl-3.2.0/openssl-3.2.0.tar.gz
tar xf ~/openssl-3.2.0.tar.gz -C /opt/ && cd /opt/openssl-3.2.0
check '从internet拉取最新版本'
yum install -y perl-CPAN perl-IPC-Cmd openssl-devel
perl -MCPAN -e shell                       #这块需要手动执行install IPC/Cmd.pm 最后exit退出
check '安装所需依赖'
./config --prefix=/usr/local/openssl-3.2.0 shared
make
make test
make install
check '编译、安装'
mv /usr/bin/openssl /usr/bin/openssl.bak
check '备份'
ln -s /usr/local/openssl-3.2.0/bin/openssl /usr/bin/openssl
ln -s /usr/local/openssl-3.2.0/include/openssl /usr/include/openssl
check '软连接'
echo "/usr/local/openssl/lib64" >> /etc/ld.so.conf
check '更新动态链接库数据'
echo 'export LD_LIBRARY_PATH=/usr/local/openssl-3.2.0/lib64:$LD_LIBRARY_PATH' >> ~/.bash_profile
source ~/.bash_profile
check '配置环境变量'
ldconfig -v
check '加载配置'
openssl version
check '版本查询'
echo '--------openssl升级完成--------'
sleep 3
###
echo '2. 开始升级openssh到9.6p1'
wget https://mirrors.aliyun.com/pub/OpenBSD/OpenSSH/portable/openssh-9.6p1.tar.gz
tar xf openssh-9.6p1.tar.gz -C /usr/local && cd /usr/local/openssh-9.6p1
check '下载、解压' 
yum group list                #查看工具组
sleep 3
yum -y group install "Development Tools" 
#vim INSTALL                    #查看安装文档
check '安装依赖'
mv /etc/ssh /etc/ssh.bak
mv /usr/bin/ssh /usr/bin/ssh.bak
mv /usr/sbin/sshd /usr/sbin/sshd.bak
check '备份'
systemctl disable sshd --now
check '停止old systemd进程'
./configure \
    CCFLAGS="$CCFLAGS" \
    LDFLAGS="$LDFLAGS" \
    --sysconfdir=/etc/ssh \
    --with-zlib \
    --with-ssl-dir=/usr/local/openssl-3.2.0
make                           
make install
chmod 600 /etc/ssh/*
check '编译、安装'
sed -i 's/^#PermitRootLogin.*/PermitRootLogin yes/g' /etc/ssh/sshd_config
sed -i 's/^#PasswordAuthentication.*/PasswordAuthentication yes/g' /etc/ssh/sshd_config
check '放开root权限'
cd /usr/lib/systemd/system/
cp sshd.service sshd96.service
cat >/usr/lib/systemd/system/sshd96.service <<END
[Unit]
Description=OpenSSH server daemon
Documentation=man:sshd(8) man:sshd_config(5)
After=network.target sshd-keygen.service
Wants=sshd-keygen.service

[Service]
Environment="LD_LIBRARY_PATH=/usr/local/openssl-3.2.0/lib64"
Type=simple
ExecStart=/usr/local/sbin/sshd -D
KillMode=process
Restart=on-failure
RestartSec=42s

[Install]
WantedBy=multi-user.target
END
systemctl daemon-reload
systemctl enable sshd96 --now
check '设置systemd管理'
systemctl status sshd96
check 'systemctl status sshd96查看状态'
sshd -V
ssh -V
check '版本查询'
echo '--------openssh升级完成--------'
source ~/.bash_profile
```
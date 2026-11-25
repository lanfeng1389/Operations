# Openssl升级和自签

1.  _**官网下载安装包**_
    
    **https://www.openssl.org/source/    手动下载tar包上传到服务器**
    
    **wget https://www.openssl.org/source/openssl-3.1.4.tar.gz --no-check-certificate**
    
    [请至钉钉文档查看附件《openssl-3.1.4.tar.gz》。](https://alidocs.dingtalk.com/i/nodes/93NwLYZXWyg1jOdaUd0r17XzJkyEqBQm?doc_type=wiki_doc&iframeQuery=anchorId%3DX02lopjvi1bdnif7mxq1z)
    
2.  _**解压，编译，安装，备份**_
    

```javascript
  tar xf openssl-3.1.4.tar.gz -C /opt/ && cd /opt/openssl-3.1.4
1 # 配置前要需安装perl-CPAN缺少IPC/Cmd.pm模块，不然会报错
  yum install -y perl-CPAN
  yum -y install perl-IPC-Cmd
  #全部默认（回车）
  perl -MCPAN -e shell
  #在shell中安装缺少的模块
  cpan[1]> install IPC/Cmd.pm
  #安装完成输入q退出
  
2 ./config --prefix=/usr/local/openssl shared    配置
  make    编译
  make  test   运行
  make  install   安装

3 #备份之前的ssl
  which openssl   ##看一下命令在哪个目录很重要
  mv /usr/bin/openssl /usr/bin/openssl.bak
  mv /usr/include/openssl /usr/include/openssl.bak
  #将默认的openssl命令指向新的
  ln -s /usr/local/openssl/bin/openssl /usr/bin/openssl
  ln -s /usr/local/openssl/include/openssl /usr/include/openssl
  #
  echo "/usr/local/openssl/lib64" >> /etc/ld.so.conf
  #加载配置
  ldconfig -v
  #查看是否升级成功
  [root@master1 openssl-3.1.4]# openssl version
  OpenSSL 3.1.4 24 Oct 2023 (Library: OpenSSL 3.1.4 24 Oct 2023)
```

_**3.openssh源码编译升级**_

[请至钉钉文档查看附件《openssh-9.6p1.tar》。](https://alidocs.dingtalk.com/i/nodes/93NwLYZXWyg1jOdaUd0r17XzJkyEqBQm?doc_type=wiki_doc&iframeQuery=anchorId%3DX02lw63bsrz2r20wt3kzal)

**wget** [**http://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.6p1.tar.gz**](http://cdn.openbsd.org/pub/OpenBSD/OpenSSH/portable/openssh-9.6p1.tar.gz)

```javascript
-------------------前提是升级过openssl
1.安装必要依赖
yum install wget gcc openssl-devel pam-devel rpm-build zlib-devel -y
rpm -qa | grep openssh       ##查看当前安装包
ssh -V
2.备份
mv /etc/ssh/ /etc/ssh.bak
mv /usr/bin/ssh /usr/bin/ssh.bak
mv /usr/sbin/sshd /usr/sbin/sshd.bak
mv /etc/init.d/sshd /etc/init.d/sshd.bak      ##这个第一次升级没有不影响
3.卸载原有的
rpm -e --nodeps $(rpm -qa |grep openssh)
rpm -qa | grep openssh
4.解压、编译、安装
mv openssh-9.6p1.tar.gz /usr/local/
tar xf openssh-9.6p1.tar.gz
cd openssh-9.6p1
CCFLAGS="-I/usr/local/include" \
LDFLAGS="-L/usr/local/lib64" \
./configure \
--sysconfdir=/etc/ssh \
--with-zlib \
--with-ssl-dir=/usr/bin/openssl
make -j 4
make install
5.授权
chmod 600 /etc/ssh/ *
6.负责配置文件
cp -rf /usr/local/sbin/sshd /usr/sbin/sshd
cp -rf /usr/local/bin/ssh /usr/bin/ssh
cp -rf /usr/local/bin/ssh-keygen /usr/bin/ssh-keygen
cp -ar /usr/local/openssh-9.6p1/contrib/redhat/sshd.init /etc/init.d/sshd
cp -ar /usr/local/openssh-9.6p1/contrib/redhat/sshd.pam /etc/pam.d/sshd.pam
7.修改配置允许root用户远程登录
cat >>/etc/ssh/sshd_config <<END
PermitRootLogin yes
PasswordAuthentication yes
END
sed -i "s/^#Port/Port/g" /etc/ssh/sshd_config
chmod 755 /etc/init.d/sshd
8.启用sshd，生成服务配置文件，并重启服务
chkconfig --add sshd
systemctl enable sshd
systemctl restart sshd
systemctl status sshd
ssh -V
```

_**4.nginx源码编译升级**_

[请至钉钉文档查看附件《nginx-1.20.2.tar.gz》。](https://alidocs.dingtalk.com/i/nodes/93NwLYZXWyg1jOdaUd0r17XzJkyEqBQm?doc_type=wiki_doc&iframeQuery=anchorId%3DX02lopjxsyp3q6u99tua0l)

**wget** [**https://nginx.org/download/nginx-1.20.2.tar.gz**](https://nginx.org/download/nginx-1.20.2.tar.gz)

```javascript
1.安装编译所需的依赖包
yum -y install libxml2 libxml2-dev libxslt-devel gcc gcc-c++ make zlib-devel pcre pcre-devel openssl-devel perl-devel perl-ExtUtils-Embed gd-devel gperftools --skip-broken

yum -y install gcc gcc-c++ make zlib-devel pcre-devel openssl-devel gd-devel
2.解压，进入编译目录
tar xf nginx-1.20.2.tar.gz -C /opt/
cd /opt/nginx-1.20.2
3.编译
./configure --prefix=/etc/nginx --sbin-path=/usr/sbin/nginx --modules-path=/usr/lib64/nginx/modules --conf-path=/etc/nginx/nginx.conf --error-log-path=/var/log/nginx/error.log --http-log-path=/var/log/nginx/access.log --pid-path=/var/run/nginx.pid --lock-path=/var/run/nginx.lock --http-client-body-temp-path=/var/cache/nginx/client_temp --http-proxy-temp-path=/var/cache/nginx/proxy_temp --http-fastcgi-temp-path=/var/cache/nginx/fastcgi_temp --http-uwsgi-temp-path=/var/cache/nginx/uwsgi_temp --http-scgi-temp-path=/var/cache/nginx/scgi_temp --user=nginx --group=nginx --with-compat --with-file-aio --with-threads --with-http_addition_module --with-http_auth_request_module --with-http_dav_module --with-http_flv_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_mp4_module --with-http_random_index_module --with-http_realip_module --with-http_secure_link_module --with-http_slice_module --with-http_ssl_module --with-http_stub_status_module --with-http_sub_module --with-http_v2_module --with-mail --with-mail_ssl_module --with-stream --with-stream_realip_module --with-stream_ssl_module --with-stream_ssl_preread_module --with-cc-opt='-O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' --with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie' --with-openssl=/opt/openssl-3.2.0
4.备份，安装，重启nginx
make  ##编译
mv /usr/sbin/nginx /usr/sbin/nginx.bak  ##备份
cp objs/nginx /usr/sbin/
make install  ##安装
systemctl restart nginx
5.查看版本是否升级成功
nginx -V
```

_**5.关于ssl算法涉及的漏洞**_

++默认的，SSH 会使用 3DES 算法，但des会被某漏洞利用这可使远程攻击者通过Sweet32攻击，获取纯文本数据++

**sshd -T |grep ciphers    可查看ssh所提供的加密算法或直接去sshd配置文件查看**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/p2yM4kbxgF7rqVBg/ffd82f7577e745a98f5459fb166133b33437.png)

_**6.Openssl生成证书及私钥文件**_

```javascript
1. # 创建存放证书和私钥的目录
mkdir  /opt/cert
2. # 生成私钥文件
openssl genrsa 1024 > /opt/cert/server.key
3. # 使用私钥文件生成证书-申请书
openssl req -new -key /opt/cert/server.key > /opt/cert/server.csr
openssl x509 -text -in server.crt    ###查看申请书的详细信息
4. # 同意申请，生成证书
openssl req -x509 -days 365 -key /opt/cert/server.key -in /opt/cert/server.csr > /opt/cert/server.crt
注释：-x509证书格式固定的  -days证书有效期
     key指定证书文件      in指定证书申请书
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/p2yM4kbxgF7rqVBg/33f8cab9184949e8901d1409c4b8d2343437.png)

++_申请书需要填写的内容  国家，省会，城市，组织名，组织单位名，服务器名，邮箱，密码_++
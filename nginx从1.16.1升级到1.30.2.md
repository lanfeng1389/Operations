# nginx从1.16.1升级到1.30.2

# nginx从1.16.1升级到1.30.2

客户反馈nginx版本有漏洞要升级nginx

近期我方收到关于Nginx基于堆的缓冲区溢出漏洞（CVE-2026-9256，别名 "nginx-poolslip"）的预警提示。攻击者可以通过发送构造的恶意 HTTP 请求，利用此漏洞造成服务崩溃，或者通过上传任意代码造成数据泄露。

漏洞等级：高危

影响范围：Nginx 1.30.1及以下版本   所以要升级到1.30.2版本；

# 一、备份

# 备份配置文件  
tar -czvf nginx\_conf\_backup\_$(date +%F).tar.gz /etc/nginx/  
# 备份二进制文件（假设默认路径）  
cp /usr/sbin/nginx /usr/sbin/nginx.bak.1.16.1  
# 记录当前编译参数（用于重新编译时保持一致）  
nginx -V 2>&1 | tee /tmp/nginx\_old\_compile\_args.txt

nginx -t

# 二、nginx升级资源准备

# CentOS 7 基础源若不可用，请先切换到 vault 源  
yum install -y gcc gcc-c++ make pcre-devel zlib-devel perl-ExtUtils-Embed libxslt-devel gd-devel geoip-devel perl-IPC-Cmd perl-Data-Dumper

cd /usr/local/src  
# 使用国内镜像下载  
wget https://mirrors.huaweicloud.com/nginx/nginx-1.30.2.tar.gz  
wget https://mirrors.huaweicloud.com/openssl/source/openssl-3.5.2.tar.gz  # 推荐搭配版本  
tar -xzf nginx-1.30.2.tar.gz  
tar -xzf openssl-3.5.2.tar.gz

cd /usr/local/src/nginx-1.30.2

nginx -V 2>&1 | tee /tmp/nginx\_old\_compile\_args.txt这一步生成的文件，要和下面的configure配置要整合在一起；全部丢给ai让ai去整合好再操作

cd /usr/local/src/nginx-1.30.2  
  
./configure \  
\--prefix=/etc/nginx \  
\--sbin-path=/usr/sbin/nginx \  
\--conf-path=/etc/nginx/nginx.conf \  
\--error-log-path=/var/log/nginx/error.log \  
\--http-log-path=/var/log/nginx/access.log \  
\--pid-path=/run/nginx.pid \  
... (此处粘贴你备份的旧版本编译参数) ... \  
\--with-http\_ssl\_module \  
\--with-http\_v2\_module \  
\--with-openssl=../openssl-3.5.2

./configure \  
\--prefix=/etc/nginx \  
\--sbin-path=/usr/sbin/nginx \  
\--modules-path=/usr/lib64/nginx/modules \  
\--conf-path=/etc/nginx/nginx.conf \  
\--error-log-path=/var/log/nginx/error.log \  
\--http-log-path=/var/log/nginx/access.log \  
\--pid-path=/var/run/nginx.pid \  
\--lock-path=/var/run/nginx.lock \  
\--http-client-body-temp-path=/var/cache/nginx/client\_temp \  
\--http-proxy-temp-path=/var/cache/nginx/proxy\_temp \  
\--http-fastcgi-temp-path=/var/cache/nginx/fastcgi\_temp \  
\--http-uwsgi-temp-path=/var/cache/nginx/uwsgi\_temp \  
\--http-scgi-temp-path=/var/cache/nginx/scgi\_temp \  
\--user=nginx \  
\--group=nginx \  
\--with-compat \  
\--with-file-aio \  
\--with-threads \  
\--with-http\_addition\_module \  
\--with-http\_auth\_request\_module \  
\--with-http\_dav\_module \  
\--with-http\_flv\_module \  
\--with-http\_gunzip\_module \  
\--with-http\_gzip\_static\_module \  
\--with-http\_mp4\_module \  
\--with-http\_random\_index\_module \  
\--with-http\_realip\_module \  
\--with-http\_secure\_link\_module \  
\--with-http\_slice\_module \  
\--with-http\_ssl\_module \  
\--with-http\_stub\_status\_module \  
\--with-http\_sub\_module \  
\--with-http\_v2\_module \  
\--with-mail \  
\--with-mail\_ssl\_module \  
\--with-stream \  
\--with-stream\_realip\_module \  
\--with-stream\_ssl\_module \  
\--with-stream\_ssl\_preread\_module \  
\--with-cc-opt='-O2 -g -pipe -Wall -Wp,-D\_FORTIFY\_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -fPIC' \  
\--with-ld-opt='-Wl,-z,relro -Wl,-z,now -pie' \  
\--with-openssl=../openssl-3.5.2

make -j$(nproc)

./objs/nginx -t -c /etc/nginx/nginx.conf

# 三、升级nginx

# 1. 备份旧二进制  
mv /usr/sbin/nginx /usr/sbin/nginx.old  
  
# 2. 复制新二进制  
cp ./objs/nginx /usr/sbin/nginx  
  
# 3. 发送 USR2 信号，启动新 master 进程  
kill -USR2 $(cat /run/nginx.pid)  
  
# 4. 验证新旧 master 共存  
ps aux | grep nginx  
# 应看到两组 master/worker 进程  
  
# 5. 优雅关闭旧 worker  
kill -WINCH $(cat /run/nginx.pid.oldbin)  
  
# 6. 确认新进程正常工作后，关闭旧 master  
kill -QUIT $(cat /run/nginx.pid.oldbin)  
  
# 7. 最终验证  
nginx -v  
curl -I http://localhost

# 四、验证

nginx -v 查看nginx版本；

重启nginx  

systemctl restart nginx

# 五、回滚方案

# 停止新 nginx  
systemctl stop nginx  
  
# 恢复二进制  
mv /usr/sbin/nginx.old /usr/sbin/nginx  
  
# 恢复配置（如有修改）  
tar -xzvf nginx\_conf\_backup\_\*.tar.gz -C /  
  
# 启动  
systemctl start nginx  
nginx -v  # 确认回到 1.16.1
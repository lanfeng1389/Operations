# haproxy+keepalived+tengine部署

1.  **openssl版本编译升级**
    
    [请至钉钉文档查看附件《tengine.zip》。](https://alidocs.dingtalk.com/i/nodes/AR4GpnMqJzM1N7dmhXYoByQMVKe0xjE3?doc_type=wiki_doc&iframeQuery=anchorId%3DX02me0pav5hrk6lzjcj6fj)
    
    ```javascript
    1. 备份
    cp -v /usr/bin/openssl /usr/bin/openssl.bak
    cp -v /etc/pki/tls/openssl.cnf /etc/pki/tls/openssl.cnf.bak
    2. 解压
    tar -zxf openssl-1.1.1w.tar.gz
    cd openssl-1.1.1w
    3. 编译
    ./config --prefix=/usr/local/openssl-1.1.1w \
             --openssldir=/usr/local/openssl-1.1.1w/ssl \
             -march=armv8-a+crypto \
             -Wa,--noexecstack \
             shared \
             zlib-dynamic
    
    grep "linux-aarch64" configdata.pm
    应输出：my $target="linux-aarch64";
    4.安装
    make -j$(nproc)
    make test
    make install
    
    5.配置环境变量（可选）
    export PATH="/usr/local/openssl-1.1.1w/bin:$PATH"
    export LD_LIBRARY_PATH="/usr/local/openssl-1.1.1w/lib:$LD_LIBRARY_PATH"
    exec "$@"
    6.提权
    chmod +x /usr/local/openssl-1.1.1w
    7.查看版本
    file /usr/local/openssl-1.1.1w/bin/openssl
    /usr/local/bin/openssl-1.1.1w/bin/openssl version
    8.软链
    ln -sf /usr/local/openssl-1.1.1w/lib/libssl.so.1.1 /usr/lib64/libssl.so.1.1
    ln -sf /usr/local/openssl-1.1.1w/lib/libcrypto.so.1.1 /usr/lib64/libcrypto.so.1.1
    9.加载
    ldconfig
    ```
    
2.  **安装tengine**
    
    [https://www.hikunpeng.com/document/detail/zh/kunpengwebs/ecosystemEnable/Tengine/kunpengtengine\_02\_0012.html](https://www.hikunpeng.com/document/detail/zh/kunpengwebs/ecosystemEnable/Tengine/kunpengtengine_02_0012.html)下载地址
    
    ```javascript
    1. rpm安装
    rpm -ivh --nodeps --force --replacefiles tengine-2.2.21.el7_4.ngx.aarch64.rpm
    2. systemd管理
    vim /etc/systemd/system/tengine.service
    [Unit]
    Description=Tengine
    Documentation=http://tengine.taobao.org/
    After=network.target
    
    [Service]
    Type=forking
    User=root
    Group=root
    PIDFile=/usr/local/tengine-nginx/logs/nginx.pid
    ExecStartPre=/usr/local/tengine-nginx/sbin/nginx -t
    ExecStart=/usr/local/tengine-nginx/sbin/nginx
    ExecReload=/usr/local/tengine-nginx/sbin/nginx -s reload
    ExecStop=/usr/local/tengine-nginx/sbin/nginx -s quit
    PrivateTmp=true
    LimitNOFILE=65535
    Restart=on-failure
    RestartSec=5
    
    [Install]
    WantedBy=multi-user.target
    3. 增加大小限制
    vim /usr/local/tengine-nginx/conf/nginx.conf
    http里面添加check_shm_size 5m;
    4. 加载启动
    systemctl daemon-reload
    systemctl enable tengine --now
    5. 测试
    curl http://ip
    
    ```
    
3.  **安装haproxy**
    
    ```javascript
    两台相同的配置
    1. 上传安装
    tar xf haproxy包
    yum -y install *.rpm
    
    2. 创建用户
    useradd -r -s /sbin/nologin haproxy 2>/dev/null || true
    3. 创建目录
    mkdir -p /run/haproxy
    chown haproxy:haproxy /run/haproxy
    chmod 755 /run/haproxy
    mkdir -p /var/run/haproxy
    chown haproxy:haproxy /var/run/haproxy
    chmod 755 /var/run/haproxy
    
    4. 修改配置文件
    vi /etc/haproxy/haproxy.cfg
    
    global
        log /dev/log    local0
        log /dev/log    local1 notice
        chroot /var/lib/haproxy
        pidfile /var/run/haproxy.pid
        stats socket /run/haproxy/admin.sock mode 660 level admin
        stats timeout 30s
        user haproxy
        group haproxy
        daemon
    
    defaults
        log     global
        mode    http
        option  httplog
        option  dontlognull
        timeout connect 5000
        timeout client  50000
        timeout server  50000
    
    frontend k8s-api
        bind *:16443
        mode tcp
        option tcplog
        default_backend k8s-api-be
    
    backend k8s-api-be
        mode tcp
        balance roundrobin
        server master1 192.168.0.54:16443 check
        server master2 192.168.0.53:16443 check
    
    6. 启动
    systemctl enable haproxy --now
    systemctl status haproxy
    
    
    ```
    
4.  **安装keepalived**
    

```javascript
1. 上传安装
tar xf haproxy包
yum -y install *.rpm

2. 修改配置文件（主）
vi /etc/keepalived/keepalive.conf

global_defs {
    router_id LVS_DEVEL_MASTER
}

vrrp_script chk_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
}

vrrp_instance VI_1 {
    state MASTER
    interface eth0
    virtual_router_id 51
    priority 100
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass MySecretPass
    }
    virtual_ipaddress {
        192.168.0.48
    }
    track_script {
        chk_haproxy
    }
}

3. 修改配置文件（从）
global_defs {
    router_id LVS_DEVEL_BACKUP
}

vrrp_script chk_haproxy {
    script "killall -0 haproxy"
    interval 2
    weight 2
}

vrrp_instance VI_1 {
    state BACKUP
    interface eth0
    virtual_router_id 51
    priority 90
    advert_int 1
    authentication {
        auth_type PASS
        auth_pass MySecretPass
    }
    virtual_ipaddress {
        192.168.0.48
    }
    track_script {
        chk_haproxy
    }
}

4. 启动
systemctl enable keepalived --now
systemctl status keepalived --now

5. 测试
curl http://192.168.0.48
```
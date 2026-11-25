# DMZ squid安装

1）安装

```shell
yum -y install squid
```

2）修改配置文件

vi /etc/squid/squid.conf

```shell
# 允许来自后端服务器的请求
acl backend_servers src 10.18.11.84
acl backend_servers src 10.18.11.85
acl backend_servers src 10.18.11.86

# 钉钉 API 域名（注意：以 . 开头表示匹配所有子域）
acl allowed_domains dstdomain .dingtalk.com
acl allowed_domains dstdomain .mq-internet.aliyuncs.com


# === 允许访问的端口（通常 HTTP/HTTPS）===
acl safe_ports port 80 443
```

3）设置访问规则

```shell
# 拒绝非安全端口
http_access deny !Safe_ports

# 只允许 backend_servers 访问 allowed_domains
http_access allow backend_servers allowed_domains

# 拒绝其他所有请求
http_access deny all

# 监听端口
http_port 3128
```

4）启动

```shell
systemctl enable squid --now
```

5）验证squid服务是否启动成功

```shell
1. 执行命令查看8128端口是否正常监听
netstat -tunlp | grep 3128
```
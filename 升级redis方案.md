# 升级redis方案

**下载地址：**[**https://download.redis.io/releases/**](https://download.redis.io/releases/)

**当前版本redis存在漏洞需要升级**

```javascript
  1. 检查环境
netstat -anput | grep redis
which redis-cli       //根据路径判断编译到哪个目录下
systemctl cat redis && systemctl status redis
# /etc/systemd/system/redis.service
[Unit]
Description=Redis In-Memory Data Store
After=network.target

[Service]
ExecStart=/usr/local/bin/redis-server /root/redis-7.2.11/redis.conf
ExecStop=/usr/local/bin/redis-cli -p 46379 shutdown
User=root
Group=root

[Install]
WantedBy=multi-user.targe

docker ps
redis-server --version
//主要看一下redis启的什么端口、什么版本、运行在什么环境下

2. 进redis检查备份文件、配置文件位置
redis-cli -p 46379
auth 密码
keys *          //检查有多少数据
config get dir  //备份路径
config get dbfilename  //备份文件名
127.0.0.1:46379> CONFIG GET config_file  //有没有加载配置文件
(empty array)

3. 确认好后编译安装升级
tar xf redis.tar && cd redis
mv /usr/local/bin/redis*  ~/redis-7.0.11  //备份
make PREFIX=/bin install
make test
//最后检查
```
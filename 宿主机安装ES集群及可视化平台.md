# 安装ES集群及可视化平台

# 1、安装es集群

```javascript
1、设置虚拟内存大小（三台）
sysctl -w vm.max_map_count=1048576
sysctl --system

2、关闭交换分区（三台）
swapoff -a

3、下载RPM包并安装（三台）
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.19.23-x86_64.rpm
wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-8.19.23-x86_64.rpm.sha512
sha512sum -c elasticsearch-8.19.23-x86_64.rpm.sha512
rpm --install elasticsearch-8.19.23-x86_64.rpm

4、编辑配置文件
vim /etc/elasticsearch/elasticsearch.yml
[es1]
cluster.name: cluster
network.host: 0.0.0.0
transport.host: 0.0.0.0
node.name: es1
cluster.initial_master_nodes: ["es1"]
[es2]
cluster.name: cluster
network.host: 0.0.0.0
transport.host: 0.0.0.0
node.name: es2
[es3]
cluster.name: cluster
network.host: 0.0.0.0
transport.host: 0.0.0.0
node.name: es3

5、启动（es1）
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service

6、生成密码（es1）
/usr/share/elasticsearch/bin/elasticsearch-reset-password -u elastic

7、验证es1单节点运行状态
curl --cacert /etc/elasticsearch/certs/http_ca.crt \
-u elastic:EG+T9*drm3+LUXVaiKse \
https://localhost:9200

8、生成令牌（es1）
有效期30分钟、且一个token只有用一次！
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node

9、其他节点加入（es2）
/usr/share/elasticsearch/bin/elasticsearch-reconfigure-node --enrollment-token "生成的token"

10、启动（es2）
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service

11、生成令牌（es1）
有效期30分钟、且一个token只有用一次！
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s node

12、其他节点加入（es3）
/usr/share/elasticsearch/bin/elasticsearch-reconfigure-node --enrollment-token "生成的token"

13、启动（es3）
systemctl daemon-reload
systemctl enable elasticsearch.service
systemctl start elasticsearch.service

14、验证集群（任意节点）
curl --cacert /etc/elasticsearch/certs/http_ca.crt \
-u elastic:EG+T9*drm3+LUXVaiKse \
https://localhost:9200/_cat/nodes?v

15、修改配置
vim /etc/elasticsearch/elasticsearch.yml
[es1、2、3]
注释掉cluster.initial_master_nodes
打开discovery.seed_hosts: ["172.28.148.134:9300", "172.28.148.133:9300", "172.28.148.132:9300"]

16、挨个重启
systemctl restart elasticsearch.service

17、其他
一、查看报错日志
tail -n 80 /var/log/elasticsearch/cluster.log
二、修改堆栈大小
vim /etc/elasticsearch/jvm.options
-Xms3g
-Xmx3g

```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jKqmJQ7N1vENYOw1/img/58484a39-5cac-416b-8133-9b9fff8cf62b.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jKqmJQ7N1vENYOw1/img/6d5c741d-8d08-4a1a-aa7d-401c90701744.png)

# 2、安装kibana服务

```javascript
1、下载安装
rpm --import https://artifacts.elastic.co/GPG-KEY-elasticsearch
wget https://artifacts.elastic.co/downloads/kibana/kibana-8.19.23-x86_64.rpm
rpm --install kibana-8.19.23-x86_64.rpm

2、启动
sudo systemctl daemon-reload
sudo systemctl enable kibana.service
sudo systemctl start kibana.service

3、生成令牌
/usr/share/elasticsearch/bin/elasticsearch-create-enrollment-token -s kibana

4、编辑配置文件
vim /etc/kibana/kibana.yml
server.host: "0.0.0.0"
重启服务

5、访问登录
一、需要输入第三步生成的token
http://120.26.146.100:5601/
二、输入验证码
journalctl -u kibana.service -f 查看
三、输入es账号密码

6、开启监控
菜单 → Management → Dev Tools
输入以下命令
PUT _cluster/settings
{
  "persistent": {
    "xpack.monitoring.collection.enabled": true
  }
}

7、生成密钥
/usr/share/kibana/bin/kibana-encryption-keys generate
写到配置文件中/etc/kibana/kibana.yml
systemctl restart kibana.service
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jKqmJQ7N1vENYOw1/img/87c21c05-dfc2-4f84-b871-fb9e4c587193.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jKqmJQ7N1vENYOw1/img/aacd87f7-47c1-4a4e-a7ab-592cd8dafdd2.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/jKqmJQ7N1vENYOw1/img/31f2a54a-771d-4753-8f35-574628ffec43.png)

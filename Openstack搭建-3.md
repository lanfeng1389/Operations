# openstack搭建-3

**每个版本的安装地址：**[**https://docs.openstack.org/install-guide/openstack-services.html#minimal-deployment-for-ussuri**](https://docs.openstack.org/install-guide/openstack-services.html#minimal-deployment-for-ussuri)

**keystone除了做鉴权、身份验证还记录了所有组件的地址信息，都存储在keystone的数据库里，当nova-api把请求丢给某个服务时需要知道该组件的地址信息是多少，这时就会去问keystone**

```javascript
1. 创建库
mysql -u root -p'密码'
CREATE DATABASE keystone;
2. 赋权
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'localhost' \
IDENTIFIED BY '密码';
GRANT ALL PRIVILEGES ON keystone.* TO 'keystone'@'%' \
IDENTIFIED BY '密码';
show grants;
表示本机和所有IP有权限对keystone的表进行增删改查操作
3. 安装keystone组件
yum -y install openstack-keystone httpd python3-mod_wsgi
4. 修改配置
vim /etc/keystone/keystone.conf
[database]
connection = mysql+pymysql://keystone:密码@master1/keystone
找到database段落，配置连接数据库
[token]
provider = fernet          ##配置token
5. 生成表结构
su -s /bin/sh -c "keystone-manage db_sync" keystone
6. 创建openstack的keystone组
keystone-manage fernet_setup --keystone-user keystone --keystone-group keystone
keystone-manage credential_setup --keystone-user keystone --keystone-group keystone
7. 初始化服务
keystone-manage bootstrap --bootstrap-password 密码 \
--bootstrap-admin-url http://master1:5000/v3/ \
--bootstrap-internal-url http://master1:5000/v3/ \
--bootstrap-public-url http://master1:5000/v3/ \
--bootstrap-region-id RegionOne
第一段是配置超级管理员的登录密码
第二～四段是keystone自己的地址
第五段是配置区域名字
8. 配置http、软链
vim /etc/httpd/conf/httpd.conf
ServerName controller                 ##找到这个配置本机地址
ln -s /usr/share/keystone/wsgi-keystone.conf /etc/httpd/conf.d/
systemctl enable httpd.service --now
9. 配置环境变量
vim admin-openrc
export OS_USERNAME=admin
export OS_PASSWORD=密码
export OS_PROJECT_NAME=admin
export OS_USER_DOMAIN_NAME=Default
export OS_PROJECT_DOMAIN_NAME=Default
export OS_AUTH_URL=http://master1:5000/v3
export OS_IDENTITY_API_VERSION=3
--------------------------------以下是示例-----------------------------------
10. 创建域
source admin-openrc 
openstack domain create --description "An Example Domain" example
创建一个名为 "example" 的新"domain"域，并描述
11. 创建项目
openstack project create --domain default \
--description "Service Project" service
在'defalut'域下，创建一个名为"service"的项目，并描述
openstack project create --domain default \
--description "Demo Project" myproject
12. 创建用户
openstack user create --domain default \
--password-prompt myuser
13. 创建角色
openstack role create myrole
openstack role add --project myproject --user myuser myrole
```
```javascript
1. 创建数据库和赋权
mysql -u root -p'密码'
CREATE DATABASE glance;
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'localhost' \
IDENTIFIED BY '密码';
GRANT ALL PRIVILEGES ON glance.* TO 'glance'@'%' \
IDENTIFIED BY '密码';
2. 读取环境变量文件
source admin-openrc                  ##临时的方法
3. 创建openstack用户
openstack user create --domain default --password-prompt glance
4. 分配权限
openstack role add --project service --user glance admin
给名为 "glance" 的用户在名为 "service" 的项目中分配 "admin" 管理权限
5. 创建服务
openstack service create --name glance \
--description "OpenStack Image" image
创建一个名为 "glance" 的服务，描述为 "OpenStack Image"，类型为 "image"
6. 发布endpoint地址
openstack endpoint create --region RegionOne \
image public http://controller:9292
openstack endpoint create --region RegionOne \
image internal http://controller:9292
openstack endpoint create --region RegionOne \
image admin http://controller:9292
###创建的地址会存在keystone库里有个endpoint表里
openstack endpoint list              ##查看所有的地址
7. 安装组件
yum -y install openstack-glance
8. 修改配置文件
vim /etc/glance/glance-api.conf
[database]
connection = mysql+pymysql://glance:密码@controller/glance

[keystone_authtoken]
www_authenticate_uri  = http://controller:5000
auth_url = http://controller:5000
memcached_servers = controller:11211
auth_type = password
project_domain_name = Default
user_domain_name = Default
project_name = service
username = glance
password = 密码                      ##这里指的是数据库的用户名和密码

[paste_deploy]
flavor = keystone

[glance_store]
stores = file,http
default_store = file
filesystem_store_datadir = /var/lib/glance/images/  ##默认镜像存储在本地
9. 生成数据库表
su -s /bin/sh -c "glance-manage db_sync" glance
10. 启动服务
systemctl enable openstack-glance-api.service --now
11. 测试上传镜像验证
source  admin-openrc
wget http://download.cirros-cloud.net/0.4.0/cirros-0.4.0-x86_64-disk.img
glance image-create --name "cirros" \
--file cirros-0.4.0-x86_64-disk.img \
--disk-format qcow2 --container-format bare \
--visibility=public
##用glance租户上传镜像，磁盘格式是qcow2，可以装在裸设备上并且是公共的所有租户都能访问
openstack image list                       ##查看
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR3g4x9pqLbj/img/5f122bc0-5a21-4df6-971e-d66f7e05187d.png)
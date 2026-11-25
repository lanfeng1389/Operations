# 部署mysql一主双从集群

```javascript
1. 清理服务器自带的mysql（三台机器）
[root@localhost /]# rpm -qa|grep mariadb
mariadb-libs-5.5.68-1.el7.x86_64
[root@localhost /]# rpm -e --nodeps mariadb-libs-5.5.68-1.el7.x86_64
[root@localhost /]# rpm -qa|grep mariadb

2. 上传离线包（三台机器）
yum -y install *.rpm

3. 启动并修改密码（三台机器）
systemctl enable mysqld --now
//两种方法
grep password /var/log/mysqld.log
一、mysqladmin -p'Ojfem%J%9wDq' password'Dingdoc@999'
二、mysql -uroot -p'Ojfem%J%9wDq'
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Dingdoc@999';
UPDATE mysql.user SET password_expired='N' WHERE User='root' AND Host='localhost';

4. 修改配置文件（三台机器）
vim /etc/my.cnf
//添加以下内容
log_bin
server-id=2    //注意id
gtid_mode=ON
enforce_gtid_consistency=1
port=3306
max_connections=6000
innodb_file_per_table=1
lower_case_table_names=1
innodb_large_prefix=ON
character_set_server=utf8mb4
symbolic-links=0

5. 配置授权用户（主）
mysql -uroot -p'Dingdoc@999'
grant replication slave,replication client on *.* to 'rep'@'172.28.144.%' identified by 'Dingdoc@999';
flush privileges;

6. 额外可选
mysqldump -p'QianFeng@123' --all-databases --single-transaction --master-data=2 --flush-logs > `date +%F-%H`-mysql-all.sql

7. 设置主服务器（两台从）
mysql -uroot -p'Dingdoc@999'
change master to master_host='172.28.144.2',master_user='rep',master_password='Dingdoc@999',master_auto_position=1;
start slave;
show slave status\G;

8. 开启远程访问权限
use mysql
update user set user.Host='%' where user.User='root';
flush privileges;

9. 验证
//登录主服务器创建库、表，到从服务器上查看是否复制过去
SELECT User, Host FROM mysql.user; //查看用户
```
```javascript
//如果数据库主从数据不一致连不上
1. 主库备份
mysqldump -p'Dingdoc@999' --all-databases --single-transaction --master-data=2 --flush-logs > ~/`date +%F-%H`-mysql-all.sql
2. 拷贝到从库
scp 备份文件的名字 root@xxx:~/

3. 删除从库数据
systemctl stop mysqld
sudo rm -rf /var/lib/mysql/*
mysqld --initialize --user=mysql
//etc/my.cnf先注释掉以下两个参数
gtid_mode=ON
enforce_gtid_consistency=1
systemctl start mysqld

4. 重建数据
grep password /var/log/mysqld.log
mysql -uroot -p'密码'
ALTER USER 'root'@'localhost' IDENTIFIED BY 'Dingdoc@999';
UPDATE mysql.user SET password_expired='N' WHERE User='root' AND Host='localhost';

5. 导入数据
mysql -uroot -p'Dingdoc@999' < 2025-05-21-15-mysql-all.sql

6. 打开配置参数
//etc/my.cnf以下两个参数打开
gtid_mode=ON
enforce_gtid_consistency=1
systemctl restart mysqld

7. 从新校准
//进库
change master to master_host='172.28.144.2',master_user='rep',master_password='Dingdoc@999',master_auto_position=1;
start slave;
show slave status\G;
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOL5vkM84N4lpz/img/b2c8912e-1e11-4874-ac81-21eeca33470c.png)

[https://github.com/openark/orchestrator/releases/tag/v3.2.6](https://github.com/openark/orchestrator/releases/tag/v3.2.6)地址

```javascript
SELECT User, Host FROM mysql/XMLSchema
yum -y install jq
rpm ivh orchestrator-3.2.6-1.x86_64.rpm
create database orchestrator;
CREATE USER 'orchestrator_user'@'127.0.0.1' IDENTIFIED BY 'orchestrator_Pwd123';
GRANT ALL ON orchestrator.* TO 'orchestrator_user'@'127.0.0.1';

CREATE USER 'orchestrator'@'%' IDENTIFIED BY 'orchestrator_Pwd123';
GRANT SUPER, PROCESS, REPLICATION SLAVE, RELOAD ON *.* TO 'orchestrator'@'%';
GRANT SELECT ON mysql.slave_master_info TO 'orchestrator'@'%';
GRANT SELECT ON meta.* TO 'orchestrator'@'%';

nohup ./orchestrator --debug --config=/etc/mysql/orchestrator.conf.json http &
pkill -f orchestrator
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/eYVOL5vkM84N4lpz/img/9f0d810b-c622-4afd-ab2c-4807dc6179cf.png)
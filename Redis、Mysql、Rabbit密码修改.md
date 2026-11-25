# Redis、Mysql、Rabbit密码修改

1.  **redis**
    

```javascript
1.##配置文件位置
/etc/redis/redis.conf
requirepass里面有个参数是配置密码的
2.进入数据库
kubectl exec -it redis-master1.teambition.local sh -n kube-system
redis-cli
auth "现有密码"
3.查看当前密码信息
CONFIG GET requirepass
4.修改密码
CONFIG SET requirepass "gNyG0WxtdFQ9LSqcjubYXLrOAn4w0P"
##示例
```

**2.mysql**

```javascript
1 登录
docker exec -it mysql bash
mysql -uroot -p "密码"
2 修改密码
SET PASSWORD FOR 'root' = PASSWORD('密码');  ##修改mysql密码
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('密码');  ##修改本地密码
3 修改公网访问
use mysql;
update user set host="%" where user = 'root';
flush privileges; #刷新配置,立即生效
```

**3.rabbitmq**

```javascript
##进容器，修改密码
docker exec -it myrabbitmq bash
rabbitmqctl change_password 用户 'NewPassword'
```
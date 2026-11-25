# mongo建只读账号

```shell
1、登录数据库
mongo -u root -p 密码 --authenticationDatabase admin
use admin

2、创建只读账号
db.createUser({
  user: "dzr_user",
  pwd: "dzrmongo139",
  roles: [
    { role: "readAnyDatabase", db: "admin" }
  ]
})

db.dropUser("用户名")  //删除用户命令

3、连接方式
mongodb://dzr_user:dzrmongo139@172.16.10.90:27017/?replicaSet=rs0&authSource=admin
用户：dzr_user
密码：dzrmongo139
端口：27017
```
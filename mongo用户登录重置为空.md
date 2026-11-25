# mongo用户登录重置为空

```javascript
1、登录tb服务器进入数据库
mongo -uroot -p 密码 admin
2、切换库
use teambition
3、查看有多少未重置密码的用户
db.users.find({abnormalLogin: "force-reset-password"}).count(）
4、重置为空值
 db.users.updateMany({abnormalLogin: "force-reset-password"}, {$set: {abnormalLogin: ""} })
5、退出库
exit
//谨慎操作数据库
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Mp7ld7bJaE5M8OBQ/img/8a22de86-b870-4d89-9700-a45cf51134ca.png)
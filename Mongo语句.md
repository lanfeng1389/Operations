# Mongo语句

1.  **基础命令**
    
    ```javascript
    1. 基础语法
    mongo有一个特点，当我们真正往库了插数据了才能查询到该库
    db              //显示当前所在库
    show dbs        //查看所有库
    use name        //切换进库
    show tables/collections     //查看所有表
    cls             //清屏
    db.showdownServer() //停止mongo
    
    2. 函数语句
    #/逗号做分割可以插入多条数据
    #/一般调用Many都是数组[]大括号
    find()          //查看
    db.users.find({level: 3},{name: 1, email: 1, _id:0})
    //表示查询级别为3的名字和邮箱，不显示_id
    limit()         //限制查询返回结果的数量
    sort()          //排序查询结果
    子函数调用 1 表示升序查询 -1 表示倒序查询
    db.users.find().sort({level: 1})
    db.users.find().sort({level: 1}).limit(2)  //表示倒序查询前两位
    skip()          //跳过一些查询结果
    db.users.find().sort({level: 1}).limit(2).skip(1) //跳过第一位
    count()         //统计所有数据
    db.dropDatabse()//删除当前库
    db.集合.drop()   //删除某个集合
    db.stats()      //库状态
    db.version()    //库版本
    db.getMongo()   //库连接地址
    
    3. $符的使用
    $gt             //大于
    $lt             //小于
    $gte            //大于等于
    $lte            //小于等于
    $ne             //不等于
    db.users.find({level: {$gt: 3}})
    db.users.find({level: {$gt: 3, $lt: 5}})
    db.users.find({$and: [{level: {$gte: 3},{$lte: 5}}]})
    $in             //匹配数组中任意一个值
    $nin            //相反字段值不在指定数组
    db.users.find({level: {$in: [1,3]}})
    $exists         //判断某个字段是否存在
    db.users.find({email: {$exists: ture/1}})     //列出所有包含邮箱的用户
    db.users.find({email: {$exists: false/0}})
    $not            //反转查询条件
    db.users.find({level: {$not: {$eq: 3}}})      //表示查询级别不是3的用户
    $regex          //正则表达式
    db.users.find({name: {$regex: /张/}})         //查询所有包含张的用户
    $options一般跟正则使用
    参数有：'i'忽略大小写，'m'多行模式，'s'单行模式
    db.users.find({name: {$regex: /张/, $options: 'i'}})
    db.texts.find({"content": {"$regex": "^W", "$options": "m" }})
    
    
    4. 增删改查
    -----One/Many-----       //一条或多条
    insert          //插入数据
    db.users.insertOne({name: "zhulianao"})
    db.users.insertMany([{name: "李四"},{name: "李五"}])
    update          //更新数据
    db.aliens.updateOne({"_userId" : ObjectId("61ec0340dd95f4a2d4ed163a")},{$set: {"extra.unionid": "ONJ05iifKgVJosY5POOBalQiEiE"}})
    delete          //删除数据
    
    5. 导出数据转化成json
    bsondump --outFile=NAME.js  bson文件名
    ```
    
2.  **安全隐患**
    
    ```javascript
    1. 修改内核参数
    /etc/rc.d/rc.local        //linux启动会加载的内核文件
    echo "never" > /sys/kernel/mm/transparent_hugepage/enabled
    echo "never" > /sys/kernel/mm/transparent_hugepage/defrag
    chmod +x /etc/rc.d/rc.local
    source /etc/rc.d/rc.local //立即生效
    
    2. 建议用普通用户运行mongo
    You are running this process as the root user, which is not recommended.
    
    3. 免费监控提示
    db.disableFreeMonitoring()            //关闭
    db.enableFreeMonitoring()             //启动
    
    4. 建议mongo目录用xfs的文件系统
    Using the XFS filesystem is strongly recommended with the WiredTiger storage engine See http://dochub.mongodb.org/core/prodnotes-filesystem
    ```
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/ybEnB6dMEom3nP13/img/fdcff413-b222-4129-91f0-6617556cea5b.png)
    
3.  **副本集**
    

```javascript
https://www.mongodb.com/zh-cn/docs/manual/tutorial/deploy-replica-set/
rs.initiate( {
   _id : "rs0",
   members: [
      { _id: 0, host: "mongodb0.example.net:27017" },
      { _id: 1, host: "mongodb1.example.net:27017" },
      { _id: 2, host: "mongodb2.example.net:27017" }
   ]
})
rs.conf()      //配置
rs.status()    //状态
rs.isMaster()  //基本信息
rs.printSlaveReplicationInfo()  //查看从库状态
从节点操作
"~/.mongorc.js" 文件是 MongoDB 客户端（mongo shell）的一个初始化脚本文件。每当您启动 mongo shell 时，它会自动运行这个文件中的 JavaScript 代码
echo "rs.secondaryOk()" > ~/.mongorc.js //开启命令

2. 权重调整
myconfig=rs.conf()               //设置变量
myconfig.member[2].priority=100  //配置_id为2的成员的优先级为100
rs.reconfig(myconfig)            //加载配置
rs.add(IP+端口)                   //加入到副本集
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/ybEnB6dMEom3nP13/img/920a62e8-23de-4d80-8985-2a1bcc263394.png)
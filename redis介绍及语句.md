# redis介绍及语句

_**一**_**、基础用法**

1.  _**redis属于Nosql型数据库，通过内存的方式加载数据所以读取速度很快，解决了最初始时像mysql关系型数据库随着数据扩大导致的速度慢，存储方式是以键值对的方式，是单线程的，解决高并发场景**_
    
2.  _**常用命令**_
    
    **redis-cli控制台形式**
    
    **redis也有图形化界面Redislnsight**
    
    ```javascript
    redis中是区分字母大小写的，都是以二进制的方式存储，默认不支持中文
    key  value  ##键和值
    redis-cli  ##登录  加--raw表示以原文的方式显示键值对
    dbsize   ##查看当前库中总共的key
    keys *   ##列出当前库中所有的值，支持正则
    EXISTS  key   ##判断这个键是否存在，存在返回1，不存在返回"0"
    type key ##查看values是什么类型
    
    6. 发布订阅模式
    ##存在局限性，消息无法持久化，无法记录历史消息
    PUBLISH  ##能将消息发送到指定的频道
    SUBSCRIBE  ##订阅某个频道
    ```
    
3.  **常用的元素**
    
    ```javascript
    1. SET用法
    SET  key  zla/25/ZLA      ##设置名为name的键
    MSET key value key value  ##设置多个
    setrange key 5 x          ##表示在该key下的第五个字后面追加x
    2. GET用法
    GET  key      ##查看值
    MGET key key  ##查看多个
    getrange key 0 5          ##表示截取查看出来该key的1～6的数
    append key 值 ##附加追加内容
    strlen key    ##返回value长度
      
    DEL  key      ##删除
    flushdb       ##删除当前数据库所有数据
    FLUSHALL      ##删除库中所有的键
    rename        ##重命名key
    renamenx      ##推荐用这个，如果新的key是原本存在的则不会更新重命名key，会返回0
    set nx        ##原理一样
    //类似于密码锁定时长
    TTL  ##能够设置过期时间如果输出的是-1表示没有设置过期时间-2表示已经过期
    EXPIRE key 10      ##表示设置10秒的过期时间
    PEXPIRE key 9000   ##表示设置9000千毫秒，也就是9秒
    EXPIREAT key 时间戳
    //类似于国服排行榜
    incr key      ##增1
    decr key      ##减1
    incrby key 整数值   ##增多个
    decrby key 整数值   ##减多个
    incrbyfloat key 浮点数
    decrbyfloat key 浮点数
    
    ```
    ```javascript
    //列表中的元素是可以重复的
    LPUSH key a  ##表示在列表的头部添加字母a
    RPUSH  ##尾部添加
    LPOP   ##删除头部元素
    RPOP   ##删除尾部元素，支持删除多个元素，用阿拉伯数字表示
    LLEN   ##表示查看列表中的长度
    BLPOP/BRPOP key 时间    ##表示阻塞读取删除，过了超时时间则不会执行  
    LRANGE key 0 -1        ##表示读取显示从第一个到最后一个，也就是列表中所有的元素
    ```
    ```javascript
    //集合中的元素是不可以重复的
    SADD key value    ##添加值一个或多个 
    SMEMBERS          ##列出所有值
    SISMEMBER         ##用于判断这个值是否在集合中，返回0表示不在，返回1表示在
    SREM              ##删除值
    SCARD             ##返回总数，类似mongo中的count函数
    SINTER key1 key2  ##求两个集合中的交集，也就是相同的两边都有
    SDIFF             ##差集，表示集合1里面有的，集合2里没有的列出来
    SUNION            ##并集，两边所以集合列出来，重复的不会列出来
    ```
    ```javascript
    //排序都是从小到大
    ZADD result 680 李四  ##每个元素由分数和成员组成
    ZREM                 ##删除
    ZRANGE result 0 -1 WITHSCORES  ##查看所有成员和分数 
    ZSCORE result 李四    ##查看某个成员的分数
    ZRANK result 李四     ##查看李四的排序信息
    ZCARD key             ##返回总数，类似mongo中的count函数
    ZCOUNT key mix max    ##统计给定范围内的所有元素的个数，分数值
    ZINCRBY key 值 李四    ##表示增加分数值或降
    ZRANGE key mix max    ##按照分数排名后获取指定范围，按照名次
    ZRANGEBYSCORE         ##按照分数排名，指定分数范围内的元素
    ZINTER、ZDIFF、SUNION  ##交集、差集、并集 
    // 在Z后面添加REV表示降序
    ```
    ```javascript
    //示例key field value
    HSET person key laoyang / age 100 ##设置以人的key的键值对
    HGET person key / age ##查看值
    HDEL person age   ##删除人的键值对
    HEXISTS           ##判断这个值是否存在
    HLEN  person      ##获取人的哈希中所有的数量
    hkeys             ##列出所有key
    hvals             ##列出所有value值
    hgetall           ##列出所有key和value
    hgetnx  key filed values     ##判断filed字段是否存在，存在不创建返回0，不存在则1
    hincrby key filed 3/-3       ##表示自增长3，或减3
    HMSET/HMGET/HSETNX/HGETNX    ##都是通用的
    ```
    
4.  **bin目录下可执行文件**
    

```javascript
redis-server     //启动redis
redis-cli        //命令行客户端
redis-benchmark  //基准测试工具
redis-check-aof  //AOF持久化文件检测和修复工具
redis-check-rdb  //RDB持久化文件检测和修复工具
redis-sentinel   //启动哨兵
```

**二、进阶用法**

1.  **redis的持久化**
    

 RDB使用，简单来说就是把内存所有的数据都记录到磁盘中，当redis出现故障后从磁盘读取文件恢复，类似于备份、快照功能

```javascript
需要登录redis-cli
redis关机时会自动执行一次RDB保存到当前目录下
save        //由主进程执行、会阻塞所有命令
bgsave      //由子进程执行后台备份，异步方式，避免主进程受到影响 
//RDB实现备份的基本流程，会fork一个子进程异步备份，共享内存空间，子进程读取内存数据并写入到新的RDB文件中
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/MAeqxYEbyMydO8j9/img/c02ca057-ad4e-449f-957c-72b07beded8c.png)

AOF使用，简单来说会把在redis里执行的所有命令追加写到记录在AOF文件里，可以看作是命令日志文件，往往会比RDB大很多文件，因为记录的是命令

```javascript
BGREWRITEAOF      //重写
//可命令行交互启动，属于异步后台处理，不影响主进程；
//可配置文件中修改最大体积和倍数，来自动触发重写，前提需要同时满足才会触发；
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/MAeqxYEbyMydO8j9/img/9956e8d7-066f-4c32-bb64-2f848da041f9.png)
# Cgroup资源限制-3

```javascript
/etc/security/limits.conf   在该文件对用户做限制
domain-用户、type-类型、item-内容、values值
ulimit -a                   查看当前用户有哪些限制，-v默认内存的限制
hard                        硬限制
soft                        软限制，默认就是这个值，如果同时设置了硬和软限制可以通过人为的修改到最大的值也就是硬限制设置的值，比如说软是200那默认就是200，硬是500可以通过人为的修改200-500之间的数但不能超过最大值，登出exit就会回到默认值200
手工修改的命令是：ulimit 参数 值
 
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXQk507GaOX19/img/8adb9cb4-14bd-4e25-a17c-315462199860.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXQk507GaOX19/img/03d3c07d-cf62-436e-be16-c71479226d5d.png)

```javascript
/sys/fs/cgroup/                ##目录位置
cpu.shares                     ##权重
通常是高4096，中1024，低512和自定义，默认是1024，通过在systend调整来限制某个服务CPU权重
-------------------------------配置策略步骤---------------------------------
1. 在/etc/systemd/system建立.slice结尾的文件主要是做控制策略的
systemctl daemon-reload        ##加载到内存中
2. 假如有一个nginx服务，就在当前system目录下创建一个以.service.d结尾的文件
3. cd进去编写一个以.conf结尾的文件，写的内容是调用的slice文件
服务启动——>读取目录下.conf配置文件——>配置文件调用.slice文件的规则
man systemd.resource-control   ##查看限制的参数
-------------------------------如何查看是否生效-------------------------------
会在/sys/fs/cgroup/下生成每个目录都有
能在对应的目录下找到该参数是由哪个.slice策略文件管理的，如systend就在systend目录下

```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXQk507GaOX19/img/d0c7ab30-2c7c-4fe4-abad-f72030413e06.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXQk507GaOX19/img/f5de69b9-6496-46e0-905a-3ed8f826e6c4.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/5VLqXQk507GaOX19/img/8285b569-7d9c-4c90-b741-d974641d5660.png)
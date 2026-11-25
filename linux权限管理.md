# linux权限管理

```javascript
[root@master1 ~]# openssl passwd -5 zhulianao
$5$ypPgYwdv6YBICEDq$lb0FR5/mWRVv.5IEYsg7JYmGRE59aHz6HSPP.4qpgG4
useradd zlahs -p '$5$ypPgYwdv6YBICEDq$lb0FR5/mWRVv.5IEYsg7JYmGRE59aHz6HSPP.4qpgG4'
加密的方式创建用户时指定密码，这样别人就无法知道密码是什么保护安全
-salt 保障加密密码不会改变
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZw3rADQylLX8/img/f1ebb33c-299f-4e6e-b005-54cb60776333.png)

```javascript
该目录在/etc/skel
当想创建新用户时想要在家目录home下产生文件可提前在/etc/skel下创建相应的文件或文件夹
useradd NAME
su - NAME
这样切换到普通用户时ls就能看见对应的文件或文件夹
```

```javascript
/etc/sudoers.d/
使用场景当有多台服务器上千个服务时，可通过子配置文件分开设置权限
user1    ALL=(ALL)       ALL
user2    ALL=(ALL)       ALL
user3    ALL=(ALL)       ALL
假设这是三个用户，每个用户管理500个服务即可这样单独设置权限
user4    ALL= NOPASSWD: /usr/sbin/useradd
user4    ALL= NOPASSWD: /usr/sbin/tcpdump
也可像这样只给个创建用户的命令和抓包的命令
```

```javascript
latter NAME       ##查看高级权限
chtter +属性 NAME  ##设置高级权限
chtter -属性 NAME  ##去除高级权限
  
'a:即是Append only'系统只允许这个文件后'追加数据，不允许任何进程覆盖或者删除这个文件'，不允许对该文件进行任何属性上的修改，如果目录具有这个属性，系统将只允许在这个目录下建立和修改文件，而不允许删除文件，哪怕是'root用户也不行'，也要遵循文件属性规则，常用于日志文件
'i:即Immutable'系统'不允许对这个文件进行任何修改'，如果目录具有这个属性，那么任何进程只能修改目录之下的文件，不允许建立和删除文件
's:彻底删除文件'不可恢复，因为是从磁盘上删除，然后用0填充文件所在区域
'u:相反'当一个程序请求删除文件时，系统会保留其block数据块以便以后能够恢复删除这个文件，用来防止意外删除文件或目录
```

```javascript
1.防火墙 firewalld
2.服务权限 各个服务的配置文件里的权限
3.selinux 安全上下文 ls -Z
4.系统权限 读写执行
```
# rsync实现定时备份任务

1.  **简介**
    

一.rsync是一款++远程数据同步工具++，基于IP地址同步的方式让多台服务器进行通信

二.用途就是将一个文件夹和另外一个文件夹进行同步，同步的原理是根据文件修改时间，特征标签，只要源文件下面的文件只要比目标文件夹下面的文件新的话就会覆盖或者是进行一个增量的备份

**2.安装rsync**

一**.yum方式安装**

```javascript
yum -y install rsync
echo "/usr/local/bin/rsync --daemon --config=/etc/rsyncd.conf" >>/etc/profile
```

**二.源码安装**

```javascript
wget https://download.samba.org/pub/rsync/src/rsync-3.2.3.tar.gz
#安装依赖
yum install -y maka gcc openssl-devel libzstd-devel lz4-devel
# 解压并编译安装
tar -xvf rsync-3.2.3.tar.gz -C /usr/local/ && mv /usr/local/rsync-3.2.3 /usr/local/rsync && cd /usr/local/rsync
./configure --prefix=/opt/rsync-3.2.3  --disable-ipv6  --disable-xxhash
make && make install
echo "/usr/local/rsync --daemon --config=/etc/rsyncd.conf" >>/etc/profile
```

**3.基本操作**

```javascript
rsync -av  【源文件】 【目标文件】
rsync -av -e "ssh -p 22"  【源文件】  用户名@IP:【目标文件】
```

#注释   -a存档   -v详细情况 -e ssh登陆信息   -p指定端口    -z压缩

**一.服务端配置**

_**rsync配置文件位置,可以添加模组**_

**/etc/rsyncd.conf**

```javascript
uid = rsync
gid = rsync
max connections = 200          # 最大连接数
timeout = 600                  # 超时时间
use chroot = no
pid file = /var/run/rsync.pid
log file = /var/log/rsync.log #日志信息
[mozu]                     #模组名字
path = /rsync/web           #路径位置
list = yes                 #是否允许查看模块信息
read only = no             #是否只读
ignore errors              #忽略错误信息
auth users = vuser         #定义虚拟用户作为连接用户
secrets file = /etc/rsync/rsync.passwd    #密码保存的地址
hosts allow = 192.172.0.0/16   #允许哪些IP段连接
```

**配置相关信息**

```javascript
mkdir -pv /rsync/web        #创建目录
mkdir  /etc/rsync   &&  touch  /etc/rsync/rsync.passwd        内容格式    虚拟用户：密码
chmod  600     /etc/rsync/rsync.passwd            #授权一定要做
chown  rsync.rsync   /rsync/web        #配置对应权限
groupadd  -r  rsync           #创建用户
useradd  -r  -s  /sbin/nologin   -g   rsync rsync
rsync  --daemon            #启动
netstat  -anput   |   grep   873         #查看默认端口873是否启动
```

注意！！如果修改了配置文件需要执行

```javascript
killall rsync   &&    rsync --daemon
```

**二.客户端配置**

```javascript
示例：rsync -avz 虚拟用户@IP::【模组】 【源文件】
反之：rsync -avz 【源文件】虚拟用户@IP::【模组】
rsync  -avz  /home/  vuser@192.168.136.134::mozu
```

**可以配置密码文件**

```javascript
mkdir /etc/rsync.passwd           #写入密码
chmod 600 /etc/rsync.passwd        #给予权限
rsync -avz /home/   vuser@192.168.136.134::mozu    --password-file=/etc/rsync.passwd
```

**补充：    ---delete:  让客户端和服务端的文件完全一致**

       **---exclude第一种方式命令行添加：在进行文件传输的时候，排除指定的文件**

          **第二种方式：在服务端配置文件中添加排除的文件支持通配符**

**监控系统配置**

**一.介绍**

rsync可以和inotify-tool监控文件系统结合使用来实现实时的文件同步，一般inotify配置在客户端上

**功能：**可以监控指定的文件目录，当文件发生了更改，则会触发时间，这样就可以触发事件的信息

**二.安装**

```javascript
yum -y install inotify-tools
rpm -ql inotify-tools                #查看rpm包
显示的/usr/bin/inotifywait是真正实现文件监控的程序
显示的/usr/bin/inotifywatch是做数据统计
```

**三.inotifywait常用的选项**

```javascript
-r：递归对目录中的子目录中的文件进行监控
-q：仅仅打印少量信息（仅仅打印监控事件）
-m：一直处于监控状态
-d：以守护进程的方式运行（运行在后台）
-o file：将监控到的时间输出到一个文件中
-s：将错误的信息输出到日志中
--excludei：忽略文件大小写
-e   <event>：指定要监控到事件
     access：访问事件
     modify：编辑事件
     attrib：修改文件属性事件（修改文件的元数据
     close_write：当文件从写模式关闭的时候，会触发该事件
     close_nowrite：当文件从只读模式关闭的时候，会触发该事件
     close：无论以什么方式打开文件，在关闭的时候，会触发该事件
     open：当文件被打开的时，会触发该事件
     moved_to：当一个文件夹被移动到被监控目录下，会触发该事件
     moved_from：当一个文件从监控目录下移走的时候，会触发该事件
     moved_self：在监控目录下执行移动操作，会触发该事件
     move：主要发生了文件的移动，会触发该事件
     create：创建文件的事件
     delete：删除文件的事件

--timefmt：指定输出发生的这个事件的时间点的显示格式
--format：指定的当发生事件以后输出的信息，以及输出的个数
      %f：输出发生事件的文件名
      %w：输出发生事件的文件所在的目录的绝对路径
      %e：输出发生事件的名称（如果有多个事件，多个事件用空格分隔）
      %Xe：输出发生事件的名称（如果有多个事件，多个事件用x分隔）
      %T：输出发生事件的时间（时间格式由--timefmt）
```

**三.手动方式监控目录**

```javascript
inotifywait -mrq --timefmt "%F%T" --format '%T %w%f %e' -e create,delete,modify /test
2023-09-2210:01:58 /test/1.txt CREATE,ISDIR          #监控显示的内容
2023-09-2210:01:58 /test/2.txt CREATE,ISDIR
```

**四.脚本实现同步备份**

```javascript
#!/bin/bash
#定义变量
prog="inotifywait"
events="create,modify,attrib"
iopt="-mrqd"
path="/test/"
host="172.19.14.109"
user="vuser"
secfile="/etc/rsync.passwd"
ropt="-az --delete"
modName="web1"

$prog $iopt --format "%T %w%f %e" -e $events $path | while read line
do
        rsync $ropt $line $user@$host::$modName --password-file=$secfile
done
```
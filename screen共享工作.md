# screen共享工作

```javascript
1. 安装使用
yum -y install epel-release
yum -y install screen
[root@master1 ~]# screen -S NAME   ##ctrl+a+d 临时退出,exit退出会直接删除进程
会生成一个窗口，在这里面运行的命令不受ssh影响，不会因为网络或者服务器断开而导致命令不执行，适用于需要长期执行的脚本

2. 查看进程
[root@master1 ~]# screen -ls               
There is a screen on:
          56446.test      (Detached)
Socket in /var/run/screen/S-root.

3. 退出后如何重新进入screen窗口
[root@master1 ~]# screen -r 56446.test
[root@master1 ~]# screen -x 56446.test
如果开了两台终端，另外一台终端想进screen可以通过-x参数
有个好处是里面敲的任何的命令两边终端都能看见共享

4. 如果需要设置密码请按以下步骤
screen                           
[按 Ctrl+A]
:[空格]password
[输入密码]
[按 Enter]
[再次输入密码进行确认]
[按 Enter]
```
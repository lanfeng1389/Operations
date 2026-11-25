# iptables备份策略

_**1.一键导出**_

```javascript
touch    /opt/ky30.bak-`date +%F`
iptables-save  >  /opt/ky30.bak-`date +%F`
```

_**2.默认配置文件在/etc/sysconfig/iptables中**_

把原有的配置文件备份一份，然后把iptables策略备份在配置文件中属于永久备份

永久存储需存放在默认配置文件中

_**3.一键导入**_

```javascript
iptables-restore  <  /opt/ky30.bak-`date +%F`
导入配置也是临时设置，永久生效要写在/etc/sysconfig/iptables中
```
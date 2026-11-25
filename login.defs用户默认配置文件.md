# login.defs用户默认配置文件

```javascript
debian、centos、suse系统之间有些区别文件管理这块
if ["`grep -oi ^create_home /etc/login.defs`" == "CREATE_HOME" ]
then
sed -i 's/^CREATE_HOME.*/CREATE_HOME   yes/' /etc/login.defs
else
echo "CREATE_HOME   yes" >> /etc/login.defs
fi
```
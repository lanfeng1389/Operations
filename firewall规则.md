# firewall规则

_**firewall-cmd常用命令**_

```javascript
--state    ##查看防火墙状态
--reload  ##将更改加载到防火墙中
--get-default-zone  ##查看默认使用区域
--list-all-zone  ##查看所有区域
--set-defaule --zone=trusted  ##更改区域
###其中trusted区域是放行所有服务
--add-service=http  ##添加规则
--remove-service=http  ##删除规则
--add-source=10.1.244.2  ##添加网段
--permanent    ##将规则永久加入到配置文件中，缩写per
--add-port=端口/tcp    ##加入端口
--list-ports    ##查看放行的端口
```
# http➡️https

1.  _**修改nginx配置文件**_
    
    ```javascript
    1.需上传两份证书
    ##创建存放证书目录,解压
    mkdir /etc/nginx/cert
    tar xf tbf.yunmengzhu.com_nginx.tar -C /etc/nginx/cert
    tar xf tb.yunmengzhu.com_nginx.tar -C /etc/nginx/cert
    2.参考nginx安装时的脚本来修改配置文件信息
    ##位置在
    /data/docker-compose/tesmc/data/apps/teambition/ansible/roles/nginx/templates
    其中striker-ssl.conf配置tbf.yunmengzhu.com的证书信息
    其中web-ssl.conf配置tb.yunmengzhu.com的证书信息
    复制粘贴过去修改成443端口，域名，证书位置
    systemctl restart nginx
    ```
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/d0y4WXZZjH7m52QB/ddd279d30ea94668958c85c3db9393903437.png)
    
2.  _**加载数据库，重启所有helm服务**_
    
    [请至钉钉文档查看附件《modify-domain\_js.tar》。](https://alidocs.dingtalk.com/i/nodes/NZQYprEoWoe1ladgT9xMAoBjJ1waOeDk?doc_type=wiki_doc&iframeQuery=anchorId%3DX02loqkazh96ej5682c2o)
    
    ```javascript
    修改域名和端口
    tar -xf modify-domain_js.tar
    :%s/源内容/目标内容/g  ##批量替换
    2.加载到数据库
    mongo -uroot -p 7ui_VqGX4oWRDl7nbfU7aNgNuMInjn --authenticationDatabase admin modify-domain.js
    3.修改大values
    开启ssl把http修改成https，80端口修改成443端口
    4.更新服务
    for i in `ls` ;do helm upgrade $i ./$i -f ./values.prod.yaml ;done
    ```
    
3.  _**##扩展内容**_
    

**没添加证书前如做过静态资源，添加证书后需要修改**

```javascript
1.批量把http换成https
vim /teambition/tblogo/templates.json
2.重启tbcore
helm upgrade tbcore ./tbcore -f ./values.prod.yaml
```
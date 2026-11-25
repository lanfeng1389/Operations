# nginx证书配置

```javascript
证书上传到TB服务器
/etc/nginx目录
vim /etc/nginx/conf.d/striker.conf  //文件域名
新版没有文件域名就不需要管
vim /etc/nginx/conf.d/web.conf  //主域名
分别编辑两个文件内容，修改证书路径，保存
nginx -t //检查
nginx -s reload //更新
网页访问TB看是否更新成功
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Lk3lb87WmxLQqm96/img/1210098e-9469-44b4-ad35-1401cdc7e412.png)
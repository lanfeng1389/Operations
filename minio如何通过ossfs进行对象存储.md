# minio如何通过ossfs进行对象存储

```javascript
1. 第一步
// 首先要给minio做软连接
pkill -9 nginx            
helm uninstall minio
kubectl get pvc                ##检查pvc是否被卸载
cd /teambition/data/
mkdir /teambition/data/oss
mv minio ~/                    ##源目录挪走
ln -s /teambition/data/nfs/minio /teambition/data/          
```
```javascript
2. 第二步
一、阿里云搜索对象存储，创建一个bucket【简称'桶'】
二、服务器上安装ossfs
wget https://gosspublic.alicdn.com/ossfs/ossfs_1.91.3_centos7.0_x86_64.rpm
yum install ossfs_1.91.3_centos7.0_x86_64.rpm
三、拿取bucket_name，AccessKey ID和AccessKey Secret
bucket_name就是你创建的'桶'的名字
AccessKey ID和AccessKey Secret见下面截图
四、服务器上配置认证
echo 桶名字:ID:secret > /etc/passwd-ossfs 
chmod 640 /etc/passwd-ossfs      ##给予权限
ossfs tb-test01 /teambition/data/oss -ourl=http://oss-cn-hangzhou-internal.aliyuncs.com            ##挂载这是一个示例
最后把源目录下的数据cp -rp一份到目标挂载的路径下
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/AJdl64pGvgD9qke1/img/5ff135fc-888e-4707-b20b-17f6f186fa2f.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/AJdl64pGvgD9qke1/img/9af54805-23e0-4056-80d8-151adbc942df.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/AJdl64pGvgD9qke1/img/05238ac7-1ed5-4b3c-b9a7-c955b97f4f12.png)

```javascript
3. 第三步
一、配置minio
cd /teambition/app/helm
vim minio/values
RELEASE.2024-03-06                   ##配置成这个版本
##原来的版本跟ossfs版本不兼容
helm install minio ./minio -f ./values.prod.yaml
前端上传文件验证
二、配置开机自启动
vim /etc/init.d/ossfs
#! /bin/bash
#
# ossfs      Automount Aliyun OSS Bucket in the specified direcotry.
#
# chkconfig: 2345 90 10
# description: Activates/Deactivates ossfs configured to start at boot time.

ossfs tb-test01 /teambition/data/oss -ourl=http://oss-cn-hangzhou-internal.aliyuncs.com
//配置以上内容
chmod a+x /etc/init.d/ossfs
chkconfig ossfs on   ##加入到开机自启
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/AJdl64pGvgD9qke1/img/8ea426f2-c79c-4715-9ae9-c5b9817b8573.png)
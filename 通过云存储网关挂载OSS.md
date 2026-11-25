# 通过云存储网关挂载OSS

**一、配置Bucket**

**1、登录阿里云搜索对象存储OSS**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/0cf27bca-719f-4cee-b34b-f37475efff28.png)

**2、创建bucket-填写基础信息、存储类型等（****注意地域要跟ECS主机一样****）**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/67a29b36-b73c-4696-92f1-71bbdd617c8a.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/b4742c81-f7aa-4bc4-8c3b-f743fd24bc31.png)

**二、配置云存储网关**

**1、ECS挂载OSS-网关基础信息（专有网络和虚拟交换机选择ECS云主机的）**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/457e9b1d-1f72-498d-be53-aac5c466d87b.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/c460203e-3a87-43ed-b1f9-0fbfeebfb333.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/85284068-c77f-4e54-bd60-045bf34c821d.png)

**2、搜索云存储网关—点击ID名称—NFS挂载使用**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/d618ede0-b85d-457e-a052-4815ebf8b21b.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOgk472MRXOWNX/img/a58f7970-3011-4f8a-93d5-dc776ca060c3.png)
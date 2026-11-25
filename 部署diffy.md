# 部署diffy

```javascript
1、安装docker
一、安装依赖
yum install -y yum-utils device-mapper-persistent-data lvm2 git
二、添加阿里云镜像源
yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
三、安装
yum install -y docker-ce
四、启动
systemctl enable docker --now
五、查看版本
docker -v
六、扩展
daemon.json文件内容
{
         "registry-mirrors": [
                     "https://hub.rat.dev/",
                     "https://docker.1panel.live/",
                     "https://docker.m.daocloud.io",
                     "https://docker.nju.edu.cn",
                     "https://dockerproxy.com",
                     "https://docker.xuanyuan.me",
                     "https://docker.m.daocloud.io",
                     "https://docker.1ms.run"
                              ]
}

2、安装diffy
官网：https://docs.dify.ai/zh-hans/getting-started/install-self-hosted/docker-compose
一、安装git
yum -y install git
二、拉包
git clone https://ghfast.top/https://github.com/langgenius/dify.git --branch 0.15.3
三、启动
cd dify/docker
cp .env.example .env
docker compose up -d
四、访问
http://192.168.20.203/install
//注册完正常登录即可
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4EZlweZ0vwda9qxA/img/ec907580-95d6-4d06-824a-095a11cc1b08.png)
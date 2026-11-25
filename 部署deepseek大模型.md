# 部署deepseek大模型

```javascript
1. 基础环境
停止firewalld和selinux
安装wget vim at curl net-tools yum-utils device-mapper-persistent-data lvm2

2. 官网下载ollama
https://ollama.com/download
curl -fsSL https://ollama.com/install.sh | sh ##过程会比较慢

3. systemd启动、运行
vim /etc/systemd/system/ollama.service ##service里添加以下内容
Environment="OLLAMA_HOST=0.0.0.0"
Environment="OLLAMA_ORIGINS=*"
systemctl daemon-reload
systemctl enable ollama --now
ollama run deepseek-r1:7b   ##安装deepseek大模型

4. 安装、启动docker服务
yum-config-manager --add-repo https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo  ##阿里源
yum install -y docker-ce docker-ce-cli containerd.io -y
##配置阿里镜像加速
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://yaxikn5o.mirror.aliyuncs.com"]
}
EOF
systemctl daemon-reload
systemctl enable docker --now

6. 配置LodeChat文件启动大模型UI管理界面
vim docker-compose.yaml 
version: '3.8'

services:
  lobe-chat:
    image: docker.1ms.run/lobehub/lobe-chat
    container_name: lobe-chat
    restart: always
    ports:
      - '3210:3210'
    environment:
      ACCESS_CODE: lobe66
      OLLAMA_PROXY_URL: http://host.docker.internal:11434
docker compose up -d

7. 访问使用LodeChat
浏览器输入http://192.168.20.206:3210
左上角头像——应用设置——语言模型——Ollama——配置IP+端口——检查——获取模型
返回到默认助手——模型设置——选择deepseek大模型
```

**一、部署流程中返回截图**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOg8BAdNL8lWNX/img/aaf546d0-7a56-48ad-ab55-df73245860ff.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOg8BAdNL8lWNX/img/e33ec059-d6cc-41f5-b80b-41fb7b71072f.png)

**二、LodeChat首次登录配置界面**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOg8BAdNL8lWNX/img/9d1a38f0-c1e1-4648-b208-5883d71762ce.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOg8BAdNL8lWNX/img/b07cfe93-474f-45b7-88f2-93d2d2e3745e.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/4maOg8BAdNL8lWNX/img/88ec23b2-f75f-4cd0-9b2a-baf8234bd71a.png)

[请至钉钉文档查看附件《lobe-chat.zip》。](https://alidocs.dingtalk.com/i/nodes/6LeBq413JAzoRpn5fQAR1eg48DOnGvpb?doc_type=wiki_doc&iframeQuery=anchorId%3DX02m6vrgbk51avzfgdpj23)
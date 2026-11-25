# Certbot证书申请工具

# 1、安装Certbot

```shell
1、升级pip工具
python3 -m pip install --upgrade pip
2、安装certbot
pip3 install certbot==1.20.0
3、针对centos7.9版本降低依赖版本
pip3 install 'cryptography==3.4.8' --force-reinstall
pip3 install 'pyOpenSSL==22.1.0' --force-reinstall
```

# 2、安装certbot-dns-aliyun插件（阿里云环境）

```shell
1、安装
pip3 install certbot-dns-aliyun
2、新增配置
mkdir -p ~/.secrets/certbot
chmod 700 ~/.secrets/certbot
3、配置AKSK（阿里云给予dns权限AliyunDNSFullAccess）
vim ~/.secrets/certbot/aliyun.ini
dns_aliyun_access_key = LTAI5t86J3afu1h6yyu8fPmi
dns_aliyun_access_key_secret = HY7KcqVTodF2oS1DaSTXNVzU2qpZ5F
```

# 3、申请证书自动化续签

```shell
1、申请证书
certbot certonly \
  --authenticator dns-aliyun \
  --dns-aliyun-credentials ~/.secrets/certbot/aliyun.ini \
  -d inventchip.com.cn \
  -d '*.inventchip.com.cn' \
  --server https://acme-v02.api.letsencrypt.org/directory
2、配置定时任务
crontab -e
0 3 * * * certbot renew --deploy-hook "systemctl reload nginx" >> /var/log/certbot-renew.log 2>&1
crontab -l      #查看
```

# 4、其他命令

```shell
which certbot                 //查看安装的路径
certbot --version             //查看版本
certbot renew --dry-run       //模拟证书续签但不执行
pip3 index versions certbot   //查看历史版本
```
# gitlab代码推送

```javascript
1、需要先登录gitla创建项目
2、初始化
登录服务器切换到对应需要上传代码的目录
cd /root/word-template-service
git init
//初始化空的 Git 版本库于 /root/word-template-service/.git/
3、设置用户名和邮箱
git config user.name "朱连奥"
git config user.email "zhulianao@hezhihulian.com"
4、添加所有文件并提交
git add .
git commit -m "Initial commit: upload word template service"
5、添加远程仓库地址（SSH）
git remote add origin git@gitlab.hezhihulian.com:zhulianao/word.git
6、推送
git push -u origin master
-f //强制覆盖远程代码仓库
```
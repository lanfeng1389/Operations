# Go语言

1.  **安装**
    

1.  **Mac系统安装go语言**
    

[**https://go.dev/dl/**](https://go.dev/dl/)**官网下载安装包并安装**

**打开终端输入go version，出现版本号即安装成功**

```javascript
go env   ##查看环境变量
1. 工作目录
GOROOT='/usr/local/go'
目录结构：
api：包含每个版本的api变更差异
bin：包含可执行文件
doc：包含文档模版
lib：包含示例程序、代码工具、本地文档等
misc：包含与Go编译器有关的配置文件
pkg：包含标准库的对象文件（.a）
src：包含源代码构建脚本和标准库的完整源代码
test： Go的测试用例等

2. 存储目录
GOPATH='/Users/mac/go'
将来自己写的代码或者网上下载的第三方包或者库，还有源码编译后生成的课执行文件都放在这个工作目录下
```

2.  **windows安装go语言**
    

**https://studygolang.com/dl 下载安装包并安装**

**打开终端输入go version 弹出版本即安装成功**

```javascript
配置环境变量
1. 
打开我的电脑——此电脑（右键）——属性——高级系统设置——环境变量——新建
变量名：GOROOT/GOPATH
变量值：工作目录/存储目录
```

3.  **安装vs code开发工具**
    

[**https://code.visualstudio.com/Download**](https://code.visualstudio.com/Download) **官网安装**

```javascript
https://goproxy.io       ##代理的网站
vim ~/.zshrc
export GOPROXY=https://goproxy.io,direct
export GOPRIVATE=git.mycompany.com,github.com/my/private
source ~/.zshrc
go env 检查
```

**打开vs code然后搜索go并安装**

**接着输入command+shift+p打开搜索框输入go install安装必要的插件**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/1X3lE6E2DadKnJbv/img/ca0445b7-bc04-49d0-8e13-e1f3fd23983d.png)
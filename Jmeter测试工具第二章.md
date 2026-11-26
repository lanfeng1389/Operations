# Jmeter测试工具第二章

一、**取样器下的http请求分三类**

1.  不用参数接口
    

*   参数：把参数放到url之后然后通过?传参，多个参数之间用&分隔
    
*   消息体数据：传JSON格式的数据
    
*   文件上传
    

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/a2QnVJNd0Pjmn4XB/img/2162353e-a61d-453d-91ad-3b51810c36cf.png)

1.  动态参数接口：
    
    tools：函数助手对话框
    
    **常用的随机数：**
    
    Random、RandomString、time
    
2.  加密的接口
    

AES、DES、Base64

RSA

MD5、SHA、HmacSHA    （不能解密）

**随机数：digest**

RSA等其他加密算法，Jmeter没有封装（扩展口：BeanShell组件：Java语言以及它自带的BeanShell语法）

**二、实现Cookie鉴权的接口实战**

cookie是一段文本，json格式，键值对

会话级cookie和持久化cookie

**Http cookie管理器自动实现cookie关联的原理：（默认：作用域在同级别组件）**

第一步：当Jmeter第一次请求服务器的时候，如果说服务器有通过响应头的Set-Cookie有返回cookie，那么

Http cookie管理器就会自动的保存cookie这些值

第二步：然后当Jmeter第2-N次请求服务器的时候，那么Http cookie管理器就会自动的把保存的这些cookie通过请求头的cookie字段传输给服务器，从而实现cookie管理

**三、断言学习**

最常用的三种断言

1.  响应断言
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/a2QnVJNd0Pjmn4XB/img/c9ad7711-255f-4f15-aa86-2f8236482770.png)
    
    主请求和子请求，最常用的就是第二个主请求
    
    匹配规则里：包括和字符串相等，匹配和相等相等
    
2.  json断言
    

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/a2QnVJNd0Pjmn4XB/img/9b25533c-0ac6-4705-8464-e6ad82ce2346.png)

一样的用$.取键，下面填values值，就会判断是否存在

第三个常用的断言是beanshell断言

四、接口测试调试

1.  调试取样器（作用就是能看到全局的变量）
    
2.  看请求和响应四要素
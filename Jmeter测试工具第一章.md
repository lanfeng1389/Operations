# Jmeter测试工具第一章

**一、常用的十大组件**

**组件的执行顺序**

1、测试计划：Jmeter测试的起点，容器

2、线程组：代表一定的用户

3、配置元件：配置请求

4、前置处理器：请求之前的操作

5、定时器：延时请求

6、逻辑控制器：处理请求逻辑

7、取样器：发送请求的最小单元

8、后置处理器：请求之后的操作

9、断言：判断请求结果是否正常

10、监听器：收集测试结果

**组件的作用域**

结论一：组件可以作用于同级组件

结论二：组件可以作用于同级组件下的子组件

结论三：组件可以作用于父组件

**二、目录结构**

/Users/mac/apache-jmeter-5.6.3/

1、backups    （备份文件）

2、bin    （启动文件、配置文件）

*   jmeter.bat    启动测试工具的文件
    
*   jmeter.properties    全局配置文件
    

3、docs    （离线文档）

4、extras    （集成文件）

5、lib    （库文件）

6、license    （许可文件）

7、printable\_docs    （用户手册）

**http协议：**

请求：请求方式、请求路径、请求头、请求数据

常用的请求头：

Accept：客户端接收的数据格式

X-Requested-With：异步请求，ajax

Cookie：cookie信息

Content-type：发送的内容信息

User-Agent：客户端类型

**实战请求四剑客**

1.  **添加默认请求值，http/https，域名，80/443**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwoEbogQlLX8/img/99449199-f6be-4b95-b815-27bb4adc14e7.png)
    
2.  **添加请求方式和请求路径**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwoEbogQlLX8/img/5e1b3289-4ea6-4bcf-a4fb-7760eb3b53d3.png)
    
3.  **添加请求头**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwoEbogQlLX8/img/5a459d70-92eb-4944-bcf7-54bbdafef4f8.png)
    
4.  **添加请求数据和结果树**
    

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwoEbogQlLX8/img/51f278b6-03c7-4a62-b53b-d732fa61f02b.png)

响应：响应码、响应信息、响应头、响应数据

三种传参方式：

表单：参数

JSON：消息体数据

文件：文件上传

接口关联：

**常用的后置处理器**

**一、正则表达式**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwoEbogQlLX8/img/a8ce87f5-8cef-43d0-be18-74c38a334996.png)

1.  变量名：可以随意定义
    
2.  正则表达式：要包含左边界和右边界以及（.\*?）而且必须唯一
    
3.  模版：$1$表示取第一个值，有时候正则表达式会填多个值，$2$就表示取第二个值，可填多个值
    
4.  匹配数字：1表示匹配第一个值
    
5.  默认值：没有匹配到就使用默认值
    

**二、接口提取在RET里使用正则**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwoEbogQlLX8/img/708bafe4-786d-47db-9bd7-3af6031ca9c8.png)

三、JSON提取器

只适用于json数据

（1）$ 代表根目录

（2）. 代表子节点

$.\*根目录下所有子节点

（3）取数组的值通过\[下标\]，下标从0开始

**总结：看到{}用.key，看到\[\]用\[下标\]**![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/WgZOZwoEbogQlLX8/img/76be8c55-b3cb-40f7-9329-fb787e2f112f.png)

1.  变量名
    
2.  jsonpath表达式
    
3.  匹配数字：1表示匹配第一个值
    
4.  默认值：没有匹配到就使用默认值
    

业务闭环：增删改查

RestFul架构：

GET查询

POST增加

PUT修改

DELETE删除
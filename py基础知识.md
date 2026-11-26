# 基础知识-第一章

1.  **print函数的使用**
    
    语法print（）打印
    
    括号里可输入数字、变量、加减乘除、字符串、字母
    
    **ord，chr，open都是py中内置的函数**
    
    ```javascript
    print（9*8）
    print（'hello world',end='-->'）  ##" ",'',''' ''',""" """都是双三单
    print（9,'欢迎你'）          ##不换行方式逗号分隔
    print（'再见'+'2024'）       #显示就不会有空格只支持字符串
    1. 
    'ord'      //用于获取一个字符的 Unicode 编码值
    print(ord('难'))        #会打印出对应的数字38590
    2.
    'chr'      //用于获取一个整数的 Unicode 编码值对应的字符
    print(chr(38590))       #会打印出对应的字符串难
    3.
    'open'     //可以用于打开一个文件，并返回一个文件对象
    fa=open('node.txt','w')        #fa是自定义随便起，创建一个文件并写入w是write
    print('欢迎你',file=fa)         #内容打印到文件中去
    fa.close()                     #最后关闭也就是结束
    可以写入'w'，读'r'，追加'a'
    ```
    
2.  **input函数的使用**
    
    input（）输入
    
    ```javascript
    name=input("请输入你的名字:")
    print("我的名字是："+name)
    //交互式方式+号或者,调用变量name
    ```
    
3.  **保留字和标识符**
    
    不能使用python中的保留字，标识符严格区分大小写
    
    ```javascript
    import keyword
    print(keyword.kwlist)      #打印出所有的保留字，len可以统计有多少个
    ['False', 'None', 'True', '__peg_parser__', 'and', 'as', 'assert', 'async', 'await', 'break', 'class', 'continue', 'def', 'del', 'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda', 'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
    ```
    
4.  **变量与常量**
    
    变量一般用小写字母定义，而常用一般是大写字母定义（常一般是不允许修改的）
    
    ```javascript
    1.单变量
    member=17854010623
    print('我的电话号码是:',member,type(member))
    //通过修改赋予的值可以改变数据类型，type可以查看赋予的值的类型是什么
    我的电话号码是: 17854010623 <class 'int'>
    2.多量
    no=node=1024
    print(no,node)
    print(id(no))  #id()函数返回对象的内存地址
    print(id(node))
    ```
    
5.  **数值类型**
    
    整数类型：表示的数值是没有小数部分的数值，包含正整数、负整数和0
    
    浮点数类型：表示带有小数点的数值，由整数部分和小数部分组成
    
    复数：由实数++.real++和虚数++.imag++表示组成
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/fc8c839b-a93f-4edf-bbce-8b5576f1d8c4.png)
    
6.  **字符串类型**
    
    **字符串中最基本的使用**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/51e664e0-2d14-45e9-8e91-b51e625d5f81.png)
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/7daa11c1-cecb-4b72-ada2-66d2fb495a7f.png)
    
    **常见的字符串的操作**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/33b8cbe8-dfb1-4604-ae34-870d34416cb3.png)
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/4c277153-5ce2-4a59-8417-529988f67cf9.png)
    
7.  **布尔类型**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/83d5d6aa-8a61-48a7-9a9c-942652b99280.png)
    
8.  **类型转换函数**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/9ffb3c78-bb7e-4cc1-a080-3545db016aa8.png)
    
9.  **eval函数**
    
    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/7ef16a0b-f6cd-4ab1-a4b2-99da6482f81d.png)
    
    ```javascript
    x=('3.14+2')       
    print(x,type(x))       //打印类型是str字符串
    p=eval(x)
    print(p,type(p))       //定义了eval后类型就变成了浮点型类型
    ```
    
10.   **运算符**
    

**一、算术运算符**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/f02f659b-e33c-4100-ad0c-343452c9633a.png)

**二、赋值运算符**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/512ec04a-e4f4-48d2-b435-d07d921e79b2.png)

**三、比较运算符**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/91f721bc-21dd-45cd-bfb5-1dd9fbd38235.png)

**四、逻辑运算符**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/5db7c115-d700-412d-b3e0-56edcfe9f4cf.png)

**五、位运算符**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/0589db16-8345-499d-bb3a-f95c89c5053a.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/6fd50d56-1de7-4a4e-b85e-b3bf468e2fa2.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/5e7e4104-3a47-42a7-a44f-8ec273c94a3f.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/70f0197b-b7f2-482c-b1ed-59cdeaa35434.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/8c7580dd-dbec-4a6e-91c1-88610d59c57e.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/mxPOGyNeVrrbnKa9/img/3e763b55-7c5e-4be5-823f-1fcf104795e9.png)
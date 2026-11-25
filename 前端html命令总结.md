# 前端html命令总结

++**一。html的代码格式是以<>开始    </>结尾**++

_**<!DOCTYPE html>    告诉浏览器我们要用什么规范**_

**<!--  -->    注释，里面添加所需对应下面内容的意思，也像标题**

**html    总标签，所有网页的内容代码格式都要在这里面包裹**

  **head    代表网页的头部内容**

**meta    描述性标签，用来描述一些网站的信息和SEO（搜索引擎优化）**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/63c47cc4b1594de8a2b101f0e7b95b473437.png)

**title    表示网页的标题**

++_**二。   boby    代表网页的主体，显示内容的位置，代码编写都在这**_++

**1.    基本标签**

**h1-h6    标题标签，有级数区分**

**p    段落标签，网页默认是文字显示一行，p可变成段落形式**

**br    换行标签，也可用于空行用于分离分隔**

**hr    增加一条水平线**

**strong    粗体标签**

**em    斜体标签**

**3.    常用特殊符号**

**&nbsp;    相当于一个空格**

**&gt;    大于**

**&lt;    小于**

**&copy;    版权符号**![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/aed39d002362458c95b1eaae52d347573437.png)

**4.    图像标签**

**常见的图像格式**

**JPG    静态的图片**

**GIF    动态的图片**

**PNG    无损压缩图像文件格式**

**BMP    位图**

**加载图片的标签**

**img    图片标签        title：鼠标悬停能显示选填的内容**

**src：图像的地址（必填）    alt：图像的代替文字（必填）    width：图像宽度    height：图像高度**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/bd37fb830ef9439f80e990be7c8d4b4f3437.png)

**5.    链接标签**

**页面间链接**

**a**    **超链接    （默认会占用当前的页面）**

**href    链接路径，跳转的页面（必填）所有其他参数都在href里面**

**后面可以嵌套图片标签，这样点击标签即可实现链接到跳转**

**target="\_blank"    会打开新的窗口来显示超链接内容**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/6c1913bef29e4f54ad392440106abab53437.png)

**锚链接**

**<a name="top">顶部</a>    相当于标记**

**<a href="#top">回到顶部</a>    能直接跳转到对应的标记**

**功能性链接**

**href="mailto:877294355@qq.com"    能直接跳转到邮箱**

**6.    列表的分类**

**ul    无序列表**

**li**    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/85023c502fd34a0abd5d891da5c004833437.png)        **小圆点**

**ol    有序列表**

**li**    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/d24556e20d4c443da0f1e532ae1e55313437.png)        **阿拉伯数字**

**dl    自定义列表**

**dt    列表名称**

**dd    内容**        ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/48c150800ae74f26acf3721285d0653c3437.png)     

**7.    表格标签**

**table  表格    能跟border="1px"边框的意思**

**thead**

**tr    行的意思    能跟colspan="4"跨列的意思**

**td    列的意思    能跟rowspan="2"跨行的意思**

**th    表头的意思**

**tbody**

**tr**

**td    表的内容信息**    ![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/983fdf320d4646cfbbe945bb695fe3003437.png)**例如这样上面就是表头下面就是内容**

**8.    媒体元素**

**video    视频**

**src    地址（必填）后面一般跟controls能够控制视频，autoplay意思是自动播放音频**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/41f48158dbf5409885286158252d5a243437.png)

**audio    音频**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/d5cee795acd24ddfb65bd8c02db821ab3437.png)

_**跟视频是一样的唯一不同就是参数**_

++_**三。    网页进阶命令**_++

**1.    页面结构分析**

**header    网页头部（用于页面或页面中一块区域）**

**section    网页主体（web中一块独立的区域）**

**footer    网页脚部（用于整块页面或一块区域）**

**nav    导航类辅助内容**

**article    独立的文章内容**

**aside    相关内容或应用（常用于侧边栏）**

**2.    iframe内部框架**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/7d00ca0849884d3cb85409dafca191d83437.png)

**网站页面嵌套一个框架**

**<!-- forms表单 -->**

**form    所有的表单都要在这下面**

**div    区块**

**lable    标签**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/ae2e6e15126d42b7ae30620f7f7e68463437.png)

**交互模式一般用于输入name，mali，age，number比较多**

**style="margin-top:1000px"意思是增加1000px的空间**

   **"witdh: 100vw"表示占用网站百分百的空间**

**action 指定的是跳转到的页面是什么**

**submit一般用于输入内容后进行提交的格式代码**

**button    单独显示一个按钮**

**abbr    可以引用一些缩写单词，跟title写上缩写单词的全程**

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/RjQQeO7BBSMl8XWa/e998c1632632499bbd939bfb1eff27383437.png)
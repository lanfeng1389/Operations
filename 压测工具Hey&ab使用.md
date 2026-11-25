# 压测工具Hey&ab使用

```javascript
1. 安装
yum -y install httpd-tools
2. 使用
ab -t 600 -c 24 -v 1 -s 36000 -T application/json -H "Cookie:RECENT_OPEN_DOC_KEYS=rxM4lzrMMvLD9qw2;doc_atoken=MzAwMgYxwpipPutXdRaLurlUWJarVUZrMbtpTB; doc_corp_id=ding8547f26c4fa7a843;portal_corp_id=ding8547f26c4fa7a843" https://docs.cncicore.com/core/api/accessToken
2>&1 | grep "HTTP/
//压测10分钟或者-n 压测指定请求量 并发24 打印返回 超时10小时 消息头 地址 接口
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/J9LnW6j41AE25lvD/img/7d951b8f-fe0c-40e7-b4ea-5c5f3df421dd.png)

```javascript
1. 安装
wget http://soft.xiaoz.org/linux/hey_linux_amd64
chmod +x hey_linux_amd64
mv hey_linux_amd64 /usr/sbin/hey
2. 使用
 hey -n 3000/-z 10m -c 24 -t 36000 -H "Content-Type: application/json" -H "Cookie: doc_atoken=MjA0MwrHUgtSpKhMEVBBMePMwgWPARqAlwHFNI; doc_corp_id=ding8547f26c4fa7a843; portal_side_collapsed=false; portal_space_collapse=1; portal_corp_id=ding8547f26c4fa7a843" https://docs.cncicore.com/core/api/accessToken
//参数基本跟ab一样 -z 持续压测10分钟
//使用地址  https://www.cnblogs.com/jspider/p/16004848.html
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/J9LnW6j41AE25lvD/img/e4dbca7d-5415-402c-b55a-d7c7c587d83b.png)

1.  Summary（概要）
    
    这部分提供了整体测试的基本统计数据。
    
    ```plaintext
    Total: 4.1091 secs：整个测试过程耗时4.1091秒。
    Slowest: 0.0882 secs：所有请求中响应时间最长的一个请求耗时0.0882秒。
    Fastest: 0.0106 secs：所有请求中响应时间最短的一个请求耗时0.0106秒。
    Average: 0.0318 secs：所有请求的平均响应时间为0.0318秒。
    Requests/sec: 730.0954：平均每秒钟处理的请求数为730.0954个，即TPS（Transactions Per Second）。
    ```
    
2.  Total data & Size/request（总数据量与单次请求大小）
    
    Total data: 258000 bytes：在整个测试过程中，服务器返回的数据总量为258000字节。  Size/request: 86 bytes：每个请求平均返回的数据大小为86字节。
    
3.  Response time histogram（响应时间直方图）
    
    这部分展示了不同响应时间区间内请求的数量分布情况。
    
    例如：
    
    ```plaintext
    0.011 [1]：有1个请求的响应时间在0.011秒左右。
    0.018 [756]：有756个请求的响应时间在0.018秒左右。
    ```
    
    通过这个直方图，可以直观地看到大多数请求的响应时间集中在哪个区间，以及是否存在明显的长尾效应。 4. Latency distribution（延迟分布）
    
    这部分展示了不同百分位数的请求延迟情况，帮助我们了解系统的稳定性和响应时间的波动范围。
    
    ```plaintext
    10% in 0.0155 secs：10%的请求在0.0155秒内得到了响应。
    50% in 0.0296 secs：50%的请求在0.0296秒内得到了响应，也称为中位数响应时间。
    99% in 0.0749 secs：99%的请求在0.0749秒内得到了响应，只有1%的请求响应时间超过了这个值。
    ```
    
4.  Details（细节）
    

这部分进一步细分了请求处理的各个阶段所花费的时间。

```plaintext
DNS+dialup：域名解析和建立连接的时间。
    平均0.0002秒，最快0.0106秒，最慢0.0882秒。
DNS-lookup：仅域名解析的时间。
    平均0.0000秒，最快0.0000秒，最慢0.0065秒。
req write：发送请求到服务器的时间。
    平均0.0000秒，最快0.0000秒，最慢0.0017秒。
resp wait：等待服务器响应的时间。
    平均0.0315秒，最快0.0105秒，最慢0.0873秒。
resp read：读取服务器响应的时间。
    平均0.0000秒，最快0.0000秒，最慢0.0009秒。
```

```shell
1、oha下载地址
https://doc-private-software.oss-cn-hangzhou.aliyuncs.com/k8s/oha-linux-amd64
```
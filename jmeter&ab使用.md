# jmeter&ab使用

```javascript
./jmeter -n -t /root/jmeter/jmeter.jmx -l /root/jmeter/jmeter.jtl -e -o /root/jmeter/html-jmeter
需要上传一个.jmx结尾的文件去跑要提前写好

ab -t 10 -c 24 -v 1 -s 36000 -T application/json -H "Cookie: RECENT_OPEN_DOC_KEYS=rxM4lzrMMvLD9qw2; doc_atoken=MzAwMgYxwpipPutXdRaLurlUWJarVUZrMbtpTB; doc_corp_id=ding8547f26c4fa7a843; portal_corp_id=ding8547f26c4fa7a843" https://docs.cncicore.com/i/api/v1/config/info
//表示执行10秒，24个并发，最简单的日志打印，设置超时时间10小时，指定参数，地址接口
```
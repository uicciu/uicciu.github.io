# T1 XFF-Referer

> 题目：攻防世界 xff_referer

__0x00 审题__

首先看题，（题目有提示：_xff&&referer_ ）what's this? 再看网页内容

```
ip必须为123.123.123.123
```


__0x01 解题__

> 工欲善其事必先利其器, 让我来看一看这两个分别是什么意思

* X-Forwarded-For(XFF)

> request header is a de-facto standard header for identifying the originating IP address of a client connecting to a web server through a proxy server.

> 维基百科：X-Forwarded-For（XFF）是用来识别通过HTTP代理或负载均衡方式连接到Web服务器的客户端最原始的IP地址的HTTP头字段。Squid缓存代理服务器的开发人员最早引入了这一HTTP头字段，并由IETF在HTTP头字段标准化草案[1]中正式提出。

大意就是：XFF用来给服务器提供原始客户端ip

> 任何与安全相关的用途X-Forwarded-For（例如用于速率限制或基于 IP 的访问控制）必须仅使用由可信代理添加的 IP 地址。使用不可信的值可能会导致规避速率限制器、绕过访问控制、内存耗尽或其他负面的安全或可用性后果。

大意就是：这个东西可修改，存在风险及证明可以利用

* Referer

> 维基百科：用来表示从哪儿链接到目前的网页，采用的格式是URL。换句话说，借着HTTP来源地址，目前的网页可以检查访客从哪里而来，这也常被用来对付伪造的跨网站请求。

总的来说：这两个都是可以更改伪造的，接下来可以开始操作了:

1. 打开Burpsuite抓包
2. request header中加入（X-Forwarded-For=123.123.123.123）
3. 显示来自Google
 [显示如下](https://www.cnblogs.com/XXS-uic9/gallery/image/440464.html)
4. 再次加入（Referer：https://www.google.com）
5. 刷新








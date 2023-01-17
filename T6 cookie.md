# T6 cookie

> 题目：BUUCTF [BSidesCF 2019]Kookie

__0x00 审题__

看题发现给的很充足
```
Log in as admin!
We found the account cookie / monster 
```

以admin为用户名登录，难道有两个账号一个是cookie一个是monster？

* 简单尝试弱口令无果后，考虑与题目cookie的关系

__0x01 解题__

> cookie在这里怎么用

* cookie用来账号登录

两种方法：（操作大体一样）

1. 直接打开浏览器开发者工具，通过HackBar传入---Cookie:username=admin，然后刷新flag就会自动体现
2. BurpSuite抓包，同理添加cookie，然后repeater

__0x02 答案__

```
flag{541cbdeb-6024-49da-831d-4d78262d8f51}
```

__0x03 总结__

关于cookie在登录中的用法再次强调

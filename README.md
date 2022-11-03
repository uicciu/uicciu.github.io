# BUUCTF Web KU
>做题就要记一记，不会做看不懂也要记一记

* 题目
# [GXYCTF2019]Ping Ping Ping 1

> 咱就是说，就是不会

看题正常思路进行
![image](https://github.com/uicciu/image/blob/main/ping1.png)
在这里输入一个正常地址---?ip=127.0.0.1

然后发现其实也没什么变化（尝试查看源代码，没有什么东西
![image](https://github.com/uicciu/image/blob/main/pingpingping2.png)
然后走不下去了，看看目录吧---;ls
![image](https://github.com/uicciu/image/blob/main/ping2.png)
刚看见可开心了，这部直接抓取就好了---cat flag.php
![image](https://github.com/uicciu/image/blob/main/ping3.png)
第一次做题，fxck什么意思来着（
我竟然打开了翻译。。哦，是过滤掉了空格（

啥意思把空格换了试试（---cat,flag.php
![image](https://github.com/uicciu/image/blob/main/ping4.png)
又说flag被过滤了 怎么办 打开另一个吧 ---cat,index.php

在这里发现 打不开 回到开头了 进行不下去了 搜索引擎（

借一张图

![image](https://github.com/uicciu/image/blob/main/ping8.png)

改为输入---cat$IFS$index.php
![image](https://github.com/uicciu/image/blob/main/ping5.png)
又看不懂了 

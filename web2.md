# T3 web2

> 题目：攻防世界 web2

__0x00 审题__

打开题目如下，题目很清晰，目标很明确，分析代码，逆向解密

```php
<?php
$miwen="a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";

function encode($str){
    $_o=strrev($str);
    // echo $_o;
        
    for($_0=0;$_0<strlen($_o);$_0++){
       
        $_c=substr($_o,$_0,1);
        $__=ord($_c)+1;
        $_c=chr($__);
        $_=$_.$_c;  //？ ？
    } 
    return str_rot13(strrev(base64_encode($_)));
}

highlight_file(__FILE__);
/*
   逆向加密算法，解密$miwen就是flag
*/
?> 
```

__0x01 解题__

* 分析加密思路，搜索重要函数：

1. function strrev():将字符串中字母倒置
2. function substr():截取字符串中一定长度，通常的形式：substr(string,start,length)
3. function ord():返回输入的字符串的第一个字母的ASCII 值
4. function chr:根据ASCII值返回字母
5. function str_rot13():str_rot13()：函数对字符串执行 ROT13 编码。ROT13 编码是把每一个字母在字母表中向前移动 13 个字母得到。数字和非字母字符保持不变。
> 提示：编码和解码都是由相同的函数完成的。如果您把一个已编码的字符串作为参数，那么将返回原始字符串

* 研究内容，加密过程：

1. 原flag被放入function中进行一系列加密
2. 先将其顺序倒置
3. 再将其每一位字母提取出来，向后移动一位，即（a->b）
4. 将得到的内容，base64加密，倒置，再移动13位

* 给出脚本

```php
<?php
    $miwen="a1zLbgQsCESEIqRLwuQAyMwLyq2L5VwBxqGA3RQAyumZ0tmMvSGM2ZwB4tws";
    $a = base64_decode(strrev(str_rot13($miwen)));
    $fin = "";//注意
    for($x=0; $x<strlen($a); $x++) {
        $c = substr($a,$x,1);
        $_ = ord($c)-1;
        $_c = chr($_);
        $fin = $fin.$_c;
    }
    echo strrev($fin);
?>
```

> 最简单的想法每一步都反过来写就好，注意不要忘了" ",第一次就忘了一直出不来
> 想法：他倒置了两次，应该已经回到了原位，应该在解码中不需要再解一次

__0x02 答案__

跑脚本，得到flag：

```
flag:{NSCTF_b73d5adfb819c64603d7237fa0d52977}
```


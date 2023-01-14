# T5 Guess Who I am

> 题目：2023 HGAME

_写在前面：当时就是为了这道题做的爬虫，所以关于这道题现在还有不懂的地方，接下来的解题也是由官方wp+个人理解构成_

__0x00 审题__

根据描述找到相应的人，找一百次即为通过

> 我的想法很单纯：python爬虫找到描述性的话，然后放到人员字典里对照，但是第二步操作起来无从下手，又不想找一百次拿到flag

__0x01 解题__

> 话不多说，直接看wp

先放这[题目源码](https://github.com/ek1ng/My-CTF-Challenges/tree/main/HGAME2023-Guess%20Who%20I%20Am)

1. id与成员介绍如何相关联：通过python读入并解析json文件

> 通过json里读进来的内容匹配出对应的ID,用这个ID去请求验证答案的接口，当得分超过100时去关注返回的页面看看有没有什么不一样的信息。

```python
import json//第一次见这个库，处理json文件相关
with open('member.json') as f:
    member = json.load(f)//takes a file object and returns the json object.
print(member)
```

* 注：为了避免报错，将头像删掉

> 然后发现自己之前推测除了爬虫方向选对了，后面的基本走不了

2. 网站是怎么对于不同用户的信息进行判断的

> 来一个猜测，直接从文件里找原文（

* 正解如下：

> 我们的目标是去提取我们想要的成员介绍的内容，但是请求得到的数据类型是str,所以需要转换成Dict。

```python
cookies = {}
cookies['session'] = 'MTY3Mjg4Nzk4MnxEdi1CQkFFQ180SUFBUkFCRUFBQU9fLUNBQUlHYzNSeWFXNW5EQWdBQm5OdmJIWmxaQU5wYm5RRUFnQUFCbk4wY21sdVp3d05BQXRqYUdGc2JHVnVaMlZKWkFOcGJuUUVBZ0JxfAue3fcPP_M2DO-5E6e17rIWaosJfl2FkmAFevW687rc'自己的session
res = requests.get("http://week-1.hgame.lwsec.cn:31746/api/getQuestion",cookies=cookies)//自己的地址
print(res.text)
rs = json.loads(res.text)
print(rs['message'])
# 匹配对应的ID
answer = ''
for i in member:
    if i['intro'] == rs['message']:
        answer = i['id']
        break
print(answer)
```
3. 循环答题：while+每次都要提取session
```python
data = {'id':answer}
res = requests.post("http://week-1.hgame.lwsec.cn:31746/api/verifyAnswer",cookies=cookies,data=data)
print("[*] Result: "+ res.text)
cookies['session'] = res.cookies['session']
```

4. 总的版本
```python
#!/usr/bin/env python
# -*- coding: utf-8 -*-

import json
import requests
# 读取json文件并且解析
with open('member.json') as f:
    member = json.load(f)

cookies = {}
answer = ''
score = 0
url = "你的靶机地址"
cookies['session'] = '从F12中复制session的值'

while score < 100:
    # 请求getQuestion接口
    res = requests.get(url + "/api/getQuestion", cookies=cookies)
    rs = json.loads(res.text)
    print("[+] getQuestion: " + res.text)

    # 匹配对应的ID
    for i in member:
        if i['intro'] == rs['message']:
            answer = i['id']
            break

    # 请求verifyAnswer接口
    data = {'id': answer}
    res = requests.post(url + "/api/verifyAnswer", cookies=cookies, data=data)
    rs = json.loads(res.text)
    print("[*] Result: " + rs['message'])
    cookies['session'] = res.cookies['session']

    # 请求getScore接口
    res = requests.get(url + "/api/getScore", cookies=cookies)
    rs = json.loads(res.text)
    score = rs['message']
    print("[*] Score: " + str(rs['message']))
```
__0x02 总结__

1. 关于其中的几个提到的接口：/api/verifyAnswer；/api/getQuestion
2. 关于wp中提到的一种新的思路：直接将分数改为‘100’，虽然在这个题中不可操作，但是应该是一种常用思路
    

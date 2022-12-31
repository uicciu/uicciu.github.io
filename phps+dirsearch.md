# url编码

>题目 : 攻防世界 PHP2

![image](https://github.com/uicciu/image/blob/main/PHP2.png)

打眼迷惑,不知道这是什么,果断扫目录,发现一个文件index.phps 这个后缀名第一次见到
> phps文件就是php的源代码文件，通常用于提供给用户（访问者）直接通过Web浏览器查看php代码的内容。
因为用户无法直接通过Web浏览器“看到”php文件的内容，所以需要用phps文件代替。

打开得到:

![image](https://github.com/uicciu/image/blob/main/PHP21.png)

开始PHP代码审计,利用GET方法传入变量id,id不能直接赋值为admin,id在传入过程中经历一次urldecode,经历一次url解密

 __注:在url中输入的内容还会进行一次解密__
 
 因此id经过两次解密得到admin,即为将admin进行两次url加密
 
 ## 有多种方法进行处理:
 ### 1.通过在线工具进行两次加密
 工具链接:http://www.jsons.cn/urlcode/
 
 得到admin整体经过两次加密:%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65
 
 最后得到:http://223.112.5.156:64607/?id=%25%36%31%25%36%34%25%36%64%25%36%39%25%36%65
 
 ### 2.只进行部分加密,不需要工具
 已知25是十六进制的%,61是十六进制的a,所以'%25'为%,'%61'为a,经过两次解密所以可得,?id=%25%61dmin
 
 ### 3.自写程序进行解码(程序借鉴)
 
 ```C++
#include<iostream>
#include<cstring>
using namespace std;

int main(){
	char a[1000];
   cin.getline(a,1000);
   for(int i=0;i<strlen(a);i++){
   	cout<<"%";
   	cout<<hex<<int(a[i]);
   }
   cout<<endl;
   return 0;
}
```
最终得到flag

![image](https://github.com/uicciu/image/blob/main/PHP22.png)
 
 

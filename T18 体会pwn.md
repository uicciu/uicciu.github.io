# T18 PWN~

> 题目： BUUCTF test_your_nc

__0x00 审题__

> 之前就只是上过介绍，这次就纯做一做玩一玩

有一个附件，用ida打开

然后就是关于ida的应用（），刚下的不太会用（

> [这里是解答](https://blog.csdn.net/bing_Max/article/details/119947896?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-119947896-blog-112506201.pc_relevant_3mothn_strategy_and_data_recovery&spm=1001.2101.3001.4242.1&utm_relevant_index=3)


__0x01 解题__

1. 关于由题目引发的猜测

打开kali虚拟机:nc -help

连接靶机：

```
nc node4.buuoj.cn xxxx
```
ls查目录，发现flag，直接cat flag得到答案

> 因为这是基础题，所以可以猜测直接能通过靶机连接得到flag文件，但是关于为何能实现，还应通过ida查看确定

> > 救，刚下下来还不太会用

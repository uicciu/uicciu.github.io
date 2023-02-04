# T20 MIsc

> 题目：攻防世界 心仪的公司

__0x00 审题__

> 平时还是只做web,这个就是换一换玩一玩（

下载附件，在kaili中通过wireshark打开，一无所获

__0x01 解题__

1. linux 系统中strings的应用

```
strings webshell.pcapng | grep {
```
> 查找webshell中带有'{'的部分

找到许多，其中就包含flag（因为flag的形式）

__0x02 总结__

1. 关于wireshark的应用
2. 流量分析

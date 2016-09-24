---
layout: post
title: SDIO模式读取连续读取SD卡数据出现错误
date: 2016-03-26T00:00:00.000Z
categories: [ARM]
---

寒假期间完成的SD卡SDIO模式驱动以及FATFS文件系统移植当时测试没有问题，昨天跟今天搞了搞开发板上LCD显示汉字的程序，参考例程为原子战舰V3的汉字显示实验。

## 具体问题

1、当使用{% highlight c %} strcpy((char_)pname,(char_)src); strcat((char_)pname,(char_)UNIGBK_PATH); res=update_fontx(x+20_size/2,y,size,(const TCHAR_)pname,0); {% endhighlight %}<br>
读取字库文件时出现`f_open`无法读取文件的情况，当把`pname`直接写成`0:/SYSTEM/FONT/UNIGBK.BIN` 时读取正常。<br>
2、在从SD卡读取数据写入到W25Q64时出现了不能连续读取SD卡数据的情况，拔出SD卡重新插入有一定几率恢复继续写入，如此反复写入完成后，后续程序可顺利运行。

## 解决方法

问题1暂时可用上面的解决方法解决。<br>
问题2没有查到有效解决方法，留作存档。

--------------------------------------------------------------------------------

<br>
2016-03-28更新<br>
换了张内存卡发现写入正常了，驱动适应性不强，继续待解决。

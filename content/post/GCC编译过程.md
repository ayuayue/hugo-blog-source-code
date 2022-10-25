---
cssclass:
title: GCC编译过程
tags: [IT/PHP, IT/PHP/多进程, IT/GCC]
image-auto-upload: true
date created: 2022-09-07 00:35:20
date modified: 2022-10-03 00:08:43
date: 2022-10-03 20:59:46
lastmod: 2022-10-25 08:08:48
---
# GCC编译过程
参考 [https://www.runoob.com/w3cnote/gcc-parameter-detail.html](https://www.runoob.com/w3cnote/gcc-parameter-detail.html)

gcc 与 g++ 分别是 gnu 的 c & c++ 编译器 gcc/g++ 在执行编译工作的时候，总共需要4步：

-   1、预处理,生成 .i 的文件[预处理器cpp]
-   2、将预处理后的文件转换成汇编语言, 生成文件 .s [编译器egcs]
-   3、有汇编变为目标代码(机器代码)生成 .o 的文件[汇编器as]
-   4、连接目标代码, 生成可执行程序 [链接器ld]

### GCC 直接编译一个程序

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112262005483.png)

拆解

1.  预处理，生成 .i 的文件，使用 -E 参数，只运行c与预编译器
    
    ![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112262006458.png)
    
    ![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112262009090.png)
    
2.  生成汇编文件 使用 -S 参数, 只激活预处理和编译,
    
    ![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112262011863.png)
    
3.  生成目标文件 , 使用 -c 参数 也就是他只把程序做成obj文件
    
    ![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112262012421.png)
    
4.  生成可执行程序,
    

![](https://cdn.jsdelivr.net/gh/ayuayue/cdn/wolai/202112262014417.png)

### 运行可执行文件

```
./filefun
```
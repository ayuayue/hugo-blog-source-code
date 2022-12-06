# onedrive 同步方案

同步一些软件的配置，不在 C 盘中也可以进行同步

## 原理 使用链接，链接到 onedrive 目录中
示例
cmd 管理员中使用
先把文件移动到 onedrive中，再链接到本地，不然只会同步一次
MKLINK 
    ``/D    创建目录符号链接。默认为文件符号链接。
    ``/H    创建硬链接而非符号链接。
    ``/J    创建目录联接。
    ``Link   指定新的符号链接名称。
    ``Target  指定新链接引用的路径(相对或绝对)。

```cmd
mklink /D  "C:\Users\14012\tools" "C:\Users\14012\OneDrive\config\kbz\tools" 
mklink /D  "C:\Users\14012\.wind" "C:\Users\14012\OneDrive\config\kbz\.wind" 
```

nd" 
```


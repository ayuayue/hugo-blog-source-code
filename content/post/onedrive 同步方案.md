# onedrive 同步方案

同步一些软件的配置，不在 C 盘中也可以进行同步

## 原理 使用链接，链接到 onedrive 目录中
示例
mklink /j  link target
- link是你想要链接的虚拟路径
- target是真实文件的路径 

```cmd
mklink /j  "C:\Users\14012\OneDrive\tools" "C:\Users\14012\tools" 
mklink /j  "C:\Users\14012\OneDrive\windterm" "C:\Users\14012\.wind" 
```


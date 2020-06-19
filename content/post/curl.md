---
title: curl常用命令
date: 2020-05-07T10:40:26+08:00
tags: [curl]
categories: [curl]
---
### curl常用命令行

1. 发送请求,默认`get`方式

   ```go
   curl "url" //网页或json,string会打印到标准输出中,-o 参数可以制定输出位置
   ```
<!--more-->
2. 上传文件

   ```go
   curl -u name:password -T filePath ftp://serverName
   ```

3. 使用代理

   ```go
   curl -x server:port url
   ```

4. 保存`cookie`

   ```go
   curl -c cookie.txt url
   curl -D cookie.txrt url //保存header里的信息
   ```

5. 使用`cookie`

   ```go
   curl -b cookie.txt url
   ```

6. 模拟浏览器

   ```go
   curl -A "Mozilla/4.0(compatible; MSIE 8.0; Windows NT 5.0)" url
   ```

7. 伪造`referer`

   ```go
   curl -e "www.baidu.com" url //会误以为从百度跳转过来的请求
   ```

8. 下载文件

   ```go
   curl -o tieba1.jpg http:www.baidu.com/tieba1.JPG
   curl -O http://www.baidu.com/tieba1.JPG
   curl -O http://www.baidu.com/tieba[1-5].JPG //循环下载
   
   ```

9. 通过`ftp`下载

   ```go
   curl -O -u name:password ftp://server
   ```

10. 显示错误

    ```go
    curl -f url
    ```

11. 指定`post`方式请求

    ```go
    curl url -X  POST -d "name=user&age=18"
    curl url -X POST -d {"name":"user","age:18"}
    ```

    


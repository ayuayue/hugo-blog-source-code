---
cssclass:
title: 杂项
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:18:47
lastmod: 2022-10-25 08:08:50
---
# 杂项
### 关闭自动补全

```Bash
[mysql]
no-auto-rehash
```

### 自增值重溯【8.0才解决】

删除自增主键后，重启数据库，自增值会重塑，导致删除过的主键又回来了

场景，插入了n条数据，此时 show create table z

| z | CREATE TABLE `z` (

`a` int(11) NOT NULL AUTO_INCREMENT,

`b` tinyint(4) DEFAULT NULL,

PRIMARY KEY (`a`)

) ENGINE=InnoDB AUTO_INCREMENT=3 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci |

AUTO_INCREMENT=3 的值为 3 。此时我们把上一条数据删除，并重启数据库。【也就是删除id为2的数据】

此时自增值变成了2，再新增一条记录id会自动变为3 ，而不是4

### SQL mode

show variables like 'sql_mode'
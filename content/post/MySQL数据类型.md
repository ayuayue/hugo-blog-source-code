---
cssclass:
title: 数据类型
tags: [IT/MySQL]
image-auto-upload: true
date: 2022-10-03 20:08:47
lastmod: 2022-10-25 08:07:50
---
# 数据类型
## 整型

int(10)，10 只是显示长度，可以存入大于10位显示长度的数据，取出来时只取出10位最大的值。

建议：

1.  不要使用 unsigned 范围没有大的改变
2.  unsigned可能会有溢出现象
3.  自增int类型主键使用 bigint

### 数字类型

如 M*G/G 不一定等于M

FLOAT(M,D)/DOUBLE(M,D)/DECIMAL(M,D) ，表示显示 M 位整数，其中D位位于小数点后。

财务，账务系统必须用decimal类型

单精度：FLOAT 4字节

双精度：DOUBLE 8字节

高精度：DECIMAL 变长

函数：

1.  floor() 向下取整
2.  round() 范围中取
3.  rand() 随机 1-100内随机值 floor( 1+ rand() * 99 );
4.  repeat() 重复字符串，与 rand 联合使用可以重复随机数量的字符串

### 字符串类型

### 字符集

符号和编码的集合。

常见：utf8，utf8mb4，gbk。建议用 utf8mb4。

修改字符集：

1.  alter table z convert to character set utf8mb4;
2.  alter table t charset=utf8mb4 这个只会更改表的，字段的不会改

alter table convert to 会锁表

### 排序规则

Collation：比较字符串的规则

修改为区分大小写：

```SQL
alter table z modify column a char(1) unique ,collate utf8_bin;
```

---

char varchar 存储的是字符，有字符集的概念。

ENUM枚举：起到约束的作用，最多允许65536个值

SET ： 最多64个值

json：可以做数据结构约束检查，可以创建函数索引提高效率

### 日期类型

datetime 8字节

date 3字节

timestamp 4字节

year 1字节

time 3字节

### NULL值

可以是任意值，所有类型的基类

和任意值比较返回的都是NULL值
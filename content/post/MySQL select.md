---
cssclass:
title: MySQL select
tags: [IT/MySQL]
image-auto-upload: true

date: 2022-10-03 20:01:47
lastmod: 2022-10-25 08:49:53
---
# MySQL select
### order by

排序会影响执行效率，导致SQL变慢

更改参数可以优化

`show variables like 'sort_buffer_size';`

增大排序缓冲区

`set sort_buffer_size = 32` _`x 1024`_ `x 1024;`

查看当前会话排序状态

`show status like 'sort%';`

查看所有会话排序状态

`show global status like 'sort%';`

重置状态

`flush status`

### limit n,m

分页优化

1.首先order by字段创建一个索引

alter table employees add index idx_birth_date(birth_date,emp_no)

select * from employees order by birth_date,emp_no limit 30

获取到最后一天，下次获取时把上次最后一条的记录带上，使用where条件过滤。

select * from employees where (birth_date,emp_no) > ('1952-01-01,'1234') order by birth_date,emp_no limit 30;

2.  问题，1的方式只能获取上一页下一页，不能跳转页
    
3.  前端设置，后台一次返回多些数据，前端处理。
    
4.  将分页查询放到redis或其他缓存中，不去请求数据库
    

### group by

分组会产生临时表

函数：

group_concat(a)

将 a 列的值通过 ，分割。可以设置分隔符 group_concat(a order by b desc separator ':')

调优

1。调整临时表大小

`show variables like '%tmp%';`

`set tmp_table_size = 32 x 1024 x1024`

`show status like '%tmp%';`

### count(1) count(a) count(*)

count(1) = count(*) 所有记录数

count(a) a列不为空的记录数

### having与where区别

having分组聚合之后的过滤
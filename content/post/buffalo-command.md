---
title: buffalo-命令
date: 2020-05-07T10:39:51+08:00
tags: [buffalo]
categories: [buffalo ]
---
生成fizz文件命令

```
buffalo db g fizz table_name field_name:field_type(default string)
eg: soda g fizz devops_controller name code desc:text deleted_at:nulls.Time
```
<!--more-->
生成model文件命令

```
buffalo db g m table_name field_name:field_type(default string)
eg: soda g m devops_controller name code desc:text deleted_at:null.Time

会自动生成fizz文件,如需跳过 使用flag --skip-migration 
```

生成action文件命令

```
buffalo g a action_name method_name
eg buffalo g a home index edit create delete
buffalo g a /admin/user index edit create delete
buffalo g a /admin/user -m "POST" editpost createpost 
```

数据库迁移命令

```
先生成fizz文件或者sql文件
使用 buffalo db migrate up 运行迁移
使用 buffalo migrate down 恢复上次迁移
buffalo  migrate status 查看迁移状态
```

新建resource

```go
buffalo g r resource_name resource_field:type(defalut string)
buffalo g r users username email admin:boolean
```

备份整个数据库结构

```go
buffalo db schema dump
```

导入数据库结构

```go
buffalo db schema load
```




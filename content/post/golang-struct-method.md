---
title: "Go使用工厂函数实例结构体"
date: 2020-07-04T21:47:30+08:00
draft: false
tags: [golang]
categories: [golang]
---

### 像面向对象一样

`Go` 语言不支持面向对象编程语言中那样的构造子方法,但很容易实现`工厂模式`来构建子工厂

### 使用`New`来定义实例规范

首先定义一个结构体

```golang
type Exmple struct{
	ID int
	Name string
}
```

开始构造

```
func NewExmple(id int,name string) *Exmple {
	if id <0 {
		return nil
	}
	return &Exmple{
		ID : id,
		Name: name,
	}
}
```

调用

```golang
exmple := NewExmple(1,"lili")
```


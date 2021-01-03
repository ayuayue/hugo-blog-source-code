---
title: buffalo助手函数
date: 2020-05-07T10:39:22+08:00
tags: [buffalo ]
categories: [buffalo ]
---
## buffalo助手函数

`t()`: 翻译函数

locales目录下翻译文件中定义: 
<!--more-->
``` 
-id : create
	translation : "创建"
```

```go
模版中使用 : t("create")
```

`form()`: 前台生成表单函数,[详细](https://github.com/gobuffalo/tags/wiki)

```go
form({action:"",method:"",var:"f"}) //自动生成csrf验证隐藏表单,var默认为f 代表该表单对象名
f.InputTag({name:"",value:"",class:"",type:""}) //生成Input表单,样式属性可以自定义
f.SelectTag({name:"",option{"a":1,"b":2},value:""}) //生成Select表单,option里为select下拉选项和对应的值,value为设置默认选中项的值
f.TextArea({name:"",value:"",row:10}) //生成text文本框
f.CheckboxTag({name: "IsPublic",label:"",checked:""}) //生成复选框
f.DateTimeTag({"name": "ExpiresAt", "value": date}) //生成datetime-local框
f.HiddenTag({"name":""}) //生成隐藏表单框
f.Label("") //生成label标签
f.RadioButtonTag({name: "MyDrink",label:"Water"}) //生成单选框 
eg: <label>
  <input class=" form-control" name="MyDrink" type="radio" checked /> Water
</label>

```

`form_for()`: 生成一个绑定模型的表单,[详细](https://github.com/gobuffalo/tags/wiki)

```
form_for({model_name,actin:"",method:"",var:"f"})
标签内的参数为绑定模型的结构体的属性
eg: model_name{
	ExpiresAt time.Time
}
f.DateTimeTag("ExpiresAt") // 生成datetime-local框,自动填充model_name的name,value
```

`paginator()` : 分页函数

```go
后端pagination变量传递
q := tx.PaginateFromParams(c.Params())
c.Set("paginator", q.Paginator)

paginator(pagination)

生成如下:
<ul class=" pagination">
   <li class="disabled"><span>«</span></li>
   <li class="active"><a href="/movements?page=1">1</a></li>
   <li><a href="/movements?page=2">2</a></li>
   <li><a href="/movements?page=3">3</a></li>
   <li><a href="/movements?page=4">4</a></li>
   <li><a href="/movements?page=5">5</a></li>
   <li class="disabled"><a>...</a></li>
   <li><a href="/movements?page=43">43</a></li>
   <li><a href="/movements?page=2">»</a></li>
</ul>
```




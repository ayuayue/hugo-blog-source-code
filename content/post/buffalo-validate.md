---
title: buffalo-验证规则
date: 2020-05-07T10:40:03+08:00
tags: [buffalo]
categories: [buffalo]
---
### validate包

使用了`validate`进行验证后会调用`validator`验证器,无论`validataAndCreate`还是 `validateAndUpdate` 都会调用这两个方法后继续调用`validate`方法,如果使用了`validateAndSave`方法则会判断`id`是否存在,如果存在则调用`validateAndUpdate`,不存在则调用`validateAndCreate`

[validator doc文档](https://godoc.org/github.com/gobuffalo/validate/validators)
<!--more-->
### 验证使用

```go
func (m *model) validate(db *db.conn) (*validate.Errors,errro){
    return validate.validate{
        &validate.StringIsPresent{Name:"",Field: m.attr,Message:""},
    },nil
}
```



### 规则

```go
1. BytesArePresent // 验证字段字节数是否为空, 属性: Name string, Field []byte, Message string
2. EmailsPresent // 根据正则表达是验证邮箱, 属性: Name string, Field string, Message string
3. EmailLike // 验证邮箱是否包括域名和@符, 属性: Name string, Field string, Message string
4. IntArrayIsPresent // 验证字段是否为空数组, 属性: Name strig, Field []int,Message string
5. IntIsGreaterThan // 验证字段是否大于比较值, 属性: Name string, Field int, Compared int,Message string
6. IntIsLessThan // 验证字段是否小于比较值, 属性: Name string, Field int, Compared int, Message string
7. IntIsPresent //验证字段是否等于0, 属性: Name string, Field int, Message string
8. IntsAreEqual //验证两个整数是否相等, 属性: Name string,ValueOne int,ValueTwo int,Message string
9. IntsAreNotEqual // 验证两个整数是否不相等, 属性: Name string ,ValueOne int ,ValueTwo int,Message string
10. RegexMatch //验证正则规则是否匹配字段, 属性: Name string, Field string,Expr string,Message string
11. StringInclusion //验证字段是否在字符切片内, 属性: Name string, Field string, List []string, Message string
12. StringIsPresent // 验证字符串是否为空, 属性: Name string, Field string,Message string
13. StringLengthInRange //验证字符串长度在某个范围内, 属性: Name string, Field strin ,Max int,Min int,Message string
14. StringsMatch //验证字段是否与字符串匹配, 属性: Name string, Field string,Field2 string Message string
15. TimeAfterTime //验证第一个时间是否在第二个时间后, 属性: FirstName string, FirstTime time.Time, SecondName string, SecondTime time.Time
,Message string
16. TimeIsBeforeTime //验证第一个时间是否在第二个时间之前, 属性: FirstName string,FirstTime time.Time,SecondName string ,SecondTime
time.Time ,Message string
17. TimeIsPresent //验证时间是否有效, 属性: Name string ,Field time.Time,Message string
18. URLIsPresent //验证URL格式是否正确,属性: Name string ,Field string,Message string
19. UUIDIsPresent //验证UUID是都有效, 属性: Name string ,Field uuid.UUID , Message string
20. FuncValiddator //验证是否为方法, 属性: Fn func() bool, Field string,Name string ,Message string
```

### 自定义验证规则

自定义验证规则必须实现 `IsVaild()` 方法

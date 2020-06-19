---
title: golang-buffalo框架
date: 2020-05-07T10:39:08+08:00
tags: [buffalo]
categories: [buffalo]
---
### 关于`c.value("tx").(*pop.connection)`

```go
var s = x.(T) //语法为golang的类型断言, 如果x不为nil,且可以转换为T类型,则断言成功,返回一个T类型的变量 s, 如果T为接口,则要求x实现T,如果断言失败 panic
c.valule() //获取context中的值,关于tx在下面 buffalo.context返回值中说明
```
<!--more-->
### 结构体中定义文件上传

```go
c.Bind(posts)

//model中处理
type posts struct{
    MyFile binding.File `-`
}
//调用AfterCreate方法在创建后上传文件
func (p *posts) AfterCreate(tx *pop.connection){
    if !p.MyFile.Valid() {
        return nil
    }
    dir := filepath.Join(".","uploads")
    if err := os.MkdirAll(dir,0755);err != nil {
        return errors.WithStack(err)
    }
    f,err := os.Create(fileppaht.join(dir,w.MyFile.Filename))
    if err != nil{
        return errors.WithStack(err)
    }
    defer f.Close()
    _,err := io.Copy(f,w.MyFile)
    return err
}
//eg 
func (u *User) AfterSave(tx *pop.Connection) error {
    tokened := strings.Split(u.Image, "/")
    fileName := tokened[len(tokened)-1]

    output, err := os.Create(fileName)
    if err != nil {
        return err
    }
    defer output.Close()

    response, err := http.Get(u.Image)
    if err != nil {
        return err
    }
    defer response.Body.Close()

    n, err := io.Copy(output, response.Body)
    if err != nil {
        return err
    }
}
//action中处理
f,err := c.File("MyFile")
if err != nil {
    return ...
}
...
```



### 定义日志记录器

```go
func JSONLogger(lvl logger.Level) logger.FieldLogger {
	f,err := os.OpenFile("./log.txt",os.O_RDWR|os.O_CREATE|os.O_APPEND, 0766)
	if err != nil {
		log.Fatal(err)
	}
    l := logrus.New()
    l.Level = lvl
    l.SetFormatter(&logrus.JSONFormatter{})
    l.SetOutput(f)
    return logger.Logrus{FieldLogger: l}
}
app = buffalo.New(buffalo.Options{
			Env:         ENV,
			SessionName: "_cxy_session",
			Logger: JSONLogger(logger.DebugLevel),
		})
```

### `buffalo.context` 返回值

```json
type : map[string][][string]
return{
	app{
		name : 
         addr :
         host :
         env :
         log_level :
         log_lvl :
         session_name :
         worker_off :
         prefix :
	},
	authenticity_token : ,
	contentType : ,
	current_path : ,
	current_route{
		method : ,
		path : ,
		handler : ,
		pathName : ,
		aliases : ,
	},
	env : ,
	languages{
	
	},
	method : ,
	request_id : ,
	routes{
		method : ,
		path : ,
		handler : ,
		pathName : ,
		aliases : ,
	},
	tx{
		"ID": "zxNdoFdzFJAZebIzibCdEewkPZDOpG",
        "Store": {
        	"ID": 4641137705029448206,
        	"Mapper": {}
        },
        "Dialect": {
        	"ConnectionDetails": {
                "Dialect": "postgres",
                "Driver": "",
                "Database": "dcmaster",
                "Host": "127.0.0.1",
                "Port": "5432",
                "User": "postgres",
                "Password": "postgres",
                "Encoding": "",
                "URL": "",
                "Pool": 5,
                "IdlePool": 0,
                "ConnMaxLifetime": 0,
                "Options": {
                    "sslmode": "disable"
                 },
       			"RawOptions": ""
       			}
       		},
        "Elapsed": 9000500,
        "TX": {
        	"ID": 4641137705029448206,
        		"Mapper": {}
       		}
      }
}
```

### 自定义验证规则验证create时同名验证及编辑时除本对象外同名的验证

```go
func (d *DevopsModule) Validate(tx *pop.Connection) (*validate.Errors, error) {
	field := make(map[string]string)
	field["name"] = d.Name
	field["code"] = d.Code
	field["id"] = d.ID.String()
	return validate.Validate(
		&validators.StringIsPresent{Field: d.DefaultAction, Name: "DefaultAction", Message: "默认方法不能为空"},
		&NameNotTaken{Field: field, Name: "Name", tx: tx},
	), nil
}
type NameNotTaken struct {
	Name  string
	Field map[string]string
	tx    *pop.Connection
}
func (v *NameNotTaken) IsValid(errors *validate.Errors) {
	tx := v.tx
	module := &DevopsModule{}
	if err := tx.Find(module, v.Field["id"]); err == nil {
        
		if err := tx.Where("id != ?", v.Field["id"]).Where("name = ?", v.Field["name"]).First(module); err == nil {
            
			errors.Add(validators.GenerateKey(v.Name), "该名称已存在!")
            
		} else {
            
			if err := tx.Where("id != ?", v.Field["id"]).Where("code = ?", v.Field["code"]).First(module); err == nil {
                
				errors.Add(validators.GenerateKey(v.Name), "该编码已存在!")
			}
		}

	} else {
        
		if err := tx.Where("name = ?", v.Field["name"]).First(module); err == nil {
            
			errors.Add(validators.GenerateKey(v.Name), "该名称已存在!")

		} else {
            
			if err := tx.Where("code = ?", v.Field["code"]).First(module); err == nil {
                
				errors.Add(validators.GenerateKey(v.Name), "该编码已存在!")

			}
		}
	}

}
```

### 写一个helper函数生成div块

```go
"div":func (opts map[string]interface{},help plush.HelperContext)(template.HTML,error)  		{
				div := tags.New("div",opts)
				s,err := help.Block()
				if err != nil { 
					 return template.HTML(""),err
				}
				div.Append(s)
				return div.HTML(),nil
			},
//页面中使用:
<%= div({class:"row",id:"mydiv"}) {%> 
      <b>生成的div中的b标签</b>
<%}%>
```


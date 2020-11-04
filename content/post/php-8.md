---
title: "PHP8.0 新特性"
date: 2020-11-04T21:32:52+08:00
lastmod: 2020-11-04T21:32:52+08:00
draft: false
keywords: [PHP8.0 新特性]
description: "PHP8.0 新特性"
tags: [php]
categories: [php]
---

## PHP8.0

目前`php8.0`还在测试阶段.正式版大概12月份出来.先来看一下新版本的改进或新特性.

### 1. 更方便的获取类名

**之前的版本**

```php
class User {
    
}
$className = User::class; 

// or 
$user = new User();
get_class($user);
```

**新版本**

```php
class User {
    
}
$user = new User();
$className = $user::class;
```

### 2. 新增字符串函数

对于字符串的一些操作.

**之前的版本**

```php
// 1. 字符串中是否包含某个字符串
$str = 'Hello world!';
if (strpos($str,"world") !== false){
    echo "Found";
}
// 2. 字符串是否以某个字符串开头
$found = 'Hello';
if (substr($str,0,count($found)) == $found){
    echo 'Yes';
}
// 3. 字符串是否以某个字符串结尾
if (substr($str,-count($found),count($found)) == $found){
    echo 'Yes';
}
```

**新版本**

```php
// 1. 字符串中是否包含某个字符串
$str = 'Hello world!';
if (str_contains($str,"world")){
    echo 'Found';
}
// 2. 字符串是否以某个字符串开头
if (str_starts_with($str,"Hello")){
	echo 'Yes';
}
// 3. 字符串是否以某个字符串结尾
if (str_end_with($str,'Hello')){
    echo 'Yes';
}
```

**对比**: 新版本会更加方便.更加语义化.表达的意思更加清晰.

### 3. 构造方便更精简

**旧版本**

```php
class User{
    public $name;
    public function __construct($name){
        $this->name = $name;
    }
}
$user = new User("lisa");
$user->name; 
```

**新版本**

```php
class User{
    public function __construct(public $name){
        
    }
}
$user = new User("lisa");
$user->name; 
```

### 4. 箭头函数支持异常捕捉

**旧版本**

```php
$fn = fn() => 'ABC';
$fn();

$ex = fn() => throw new Exception(); // error
```

**新版本**

```php
$fn = fn() => 'ABC';
$fn();

$ex = fn() => throw new Exception(); 

try{
    $ex();
}catch(Exception){
    echo 'Failed';
}
```

**对比**: 修复了箭头函数对异常的处理.并且可以去点捕捉异常时的参数名 $exception(如果没有用到这个变量)

### 5. null 空值安全检查

**旧版本**

```php
class User{
    public function __construct(public $name){
        
    }
}
class Auth {
    public function user(){
        return new User('lisa');
    }
}
$auth = new Auth();

$auth->user()->name; // error
// if new User return null;
if($auth->user()){
    $auth->user()->name; // ok
}
// or 
$auth->user()?$auth->user()->name:null; //ok
```

**新版本**

```php
class User{
    public function __construct(public $name){
        
    }
}
class Auth {
    public function user(){
        return new User('lisa');
    }
}
$auth = new Auth();

$auth->user()?->name;
```

### 6. 联合数据类型-支持联合类型

**旧版**

```php
class TV{
    
}
class PC{
    
}
function fix(TV $it){
    echo 'I fix it'
}
fix(new Tx); // ok
fix(new PC); // error
```

**新版**

```php
class TV{
    
}
class PC{
    
}
function fix(TV|PC $it){
    echo 'I fix it'
}
fix(new Tx); // ok
fix(new PC); // ok
// 关于返回值
function fix(TV|PC $it) : string| int {
    // return string or int is ok
}
// mixed 类型代表了包括null 空的所有类型.只能单独使用
function fix(TV|PC $it) :mixed {
    // you can return all type
}
// ok method ? 表示可以返回指定的值和null
function fix(TV|PC $it) ?:stirng {
    // you can return string or null 
}
// error method
function fix(TV|PC $it) ?:mixed {
    // you can return all type
}
```

### 7. match 表达式进行逻辑判断 -- 简化 switch

### 8. 函数参数命名传递 -- 传递参数更随意.不在意默认值跟参数顺序
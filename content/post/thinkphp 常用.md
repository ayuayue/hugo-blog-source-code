---
cssclass:
title: thinkphp 常用
tags: [IT/PHP, IT/PHP/ThinkPHP]
image-auto-upload: true
date: 2022-10-03 20:43:46
lastmod: 2022-10-25 08:41:54
---
# thinkphp 常用
### 配置 nginx 路由重定向

nginx 配置中

```
location / {
            index index.php index.html error/index.html;
            if (!-e $request_filename) {
                rewrite ^(.*)$ /index.php?s=$1 last;
            break;
        }
```

### 配置nginx+thinkphp 二级目录

```Bash
    location / {
        try_files $uri $uri/ /index.php?$query_string;
        index index.html index.htm index.php;
        if (!-e $request_filename) {
            rewrite ^/index.php(.*)$ /index.php?s=$1 last;
            rewrite ^/fjnu/public/(.*)$ /fjnu/public/index.php?s=$1 last;
            rewrite ^/jou/public/(.*)$ /fjnu/public/index.php?s=$1 last;
            rewrite ^/zheda/public/(.*)$ /fjnu/public/index.php?s=$1 last;


            rewrite ^(.*)$ /index.php?s=$1 last;
            rewrite ^/fjnu/(.*)$  /fjnu/public/index.php?s=/$1 last;
            rewrite ^/jou/(.*)$  /fjnu/public/index.php?s=/$1 last;
            rewrite ^/zheda/(.*)$  /fjnu/public/index.php?s=/$1 last;
            break;
        }
    }
```

### 多项目 apache .htaccess 配置

```Bash
<IfModule mod_rewrite.c>
RewriteEngine On
RewriteBase /
RewriteRule ^index\.php$ - [L]
RewriteCond %{ENV:REDIRECT_STATUS} ^$
RewriteCond %{REQUEST_FILENAME} !-f
RewriteCond %{REQUEST_FILENAME} !-d
RewriteRule . /index.php [L]
</IfModule>
```

### apache 项目路由重写

```Bash
<IfModule mod_rewrite.c>
  Options +FollowSymlinks -Multiviews
  RewriteEngine On

  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  RewriteRule ^(.*)$ index.php [L,E=PATH_INFO:$1]
</IfModule>
```

获取最后一条SQL

`model->getLastSql();`

`db()->getLastSql();`

获取所有请求参数 `input('get.') input('post.')`

数组合并 array_merge

多维数组中获取子数组 array_column

### 模型条件

1.  多字段 OR 。

```
user_id = '1' or apply_user_id = '1'
where(['user_id|apply_user_id' => '1'])
sql where ( user_id = '1' or apply_user_id = '1')
```

2.  多字段 AND 。

```
user_id = '1' and title = 'aa'。& 和 | 不能同时使用
where(['user_id&title' => ['1','aa']])
```

3.  exp

表达式，使用函数时使用

```
update('create_time',['exp','sysdate']) // update create_time = sysdate
```

### 模型关联

模型关联预加载

```
$inhouse = ChemInHouseLedgersModel::get($condition,'ChemUseLedgers');
```

#### 模型关联模型转数组

```
collection($inhouse->toArray())->toArray()
```

#### 预加载模型加where条件

```
$inhouse = ChemInHouseLedgersModel::get($condition,['ChemUseLedgers' => function($query){
    $query->where('USE_LEDGERS_NO','10983');
}]);
```

#### 关联预加载多个表

```
$inhouse = ChemInHouseLedgersModel::get($condition,['ChemUseLedgers','Chem']);
```

获取多个 hasMany，单个 hasOne

foreach 插入时，可以先生成所有的数据，insertAll 一次插入。

token

```
<input type="hidden" name="__token__" value="{$Request.token}" />
验证
protected $rule = [
        'name'  =>  'require|max:25|token',
        'email' =>  'email',
    ];
获取请求的微秒：`$_SERVER['REQUEST_TIME_FLOAT'];`
设置为 token的值并 hash，存到 session
每次请求都先删除旧的，生成一个新的
request()->token() // 生成一个 token
ajax 验证csrf
function getToken(){
    var token = '';
    $.ajax({
        url: UrlArgent.CreateUrl('api/Csrf/getToken'),
        type: 'get',
        async:false,
        success: function (res){
            token = res.data.token;
        }
    })
    return token;
}
每次请求前先生成 token 再发送过去验证
```

### Oracle clob获取类型错误

由于没有处理，返回的是一个 resource。还需要进行 `stream_get_contents`进行处理。直接修改驱动文件

`vendor/topthink/think-oracle/src/Connection.php`

加入下面两个方法

```
/**
 * 获得数据集数组
 * @access protected
 * @param bool   $pdo 是否返回PDOStatement
 * @param bool   $procedure 是否存储过程
 * @return PDOStatement|array
 */
protected function getResult($pdo = false, $procedure = false)
{
    if ($pdo) {
        // 返回PDOStatement对象处理
        return $this->PDOStatement;
    }
    if ($procedure) {
        // 存储过程返回结果
        return $this->procedure();
    }
    $result=[];
    while ($row = $this->PDOStatement->fetch($this->fetchType)){
        self::res2str($row);
        $result[]= $row;
    }
    $this->numRows = count($result);
    return $result;
}
private static function res2str(&$res){
    if(is_array($res)){
        foreach ($res as $key=>&$value) {
            self::res2str($value);
        }
    }else if(is_resource($res)){
        $res=stream_get_contents($res);
    }
}
```

### 更新和插入 clob 类型数据

```
$chunkArr = $this->mbStrSplit($tmpArr,2000); # 先将字符串分割成数组，再使用 || 进行拼接，因为 Oracle 对一次处理字符串的长度有限制，分成多个字符串进行拼接。
        $detail = implode('\'||\'',$chunkArr);
        $no = $NoticeService->getNextVal('c2_base_web_notice');
        $sql = <<<SQL
declare
cntent clob := '{$detail}';
begin
INSERT INTO c2_base_web_notice (NOTICE_NO, TITLE, NOTICE_DETAIL, NOTICE_TIME, TOP_MARK) values('{$no}', '{$data['TITLE']}', to_clob(cntent), to_date('{$data['NOTICE_TIME']}','YYYY-MM-DD HH24:MI:SS'), '{$data['TOP_MARK']}');
end;
SQL;
        $result = Db::execute($sql);
# update
$sql = <<<SQL
declare
cntent clob := '{$detail}';
begin
UPDATE c2_base_web_notice SET TITLE='{$data['TITLE']}',TOP_MARK='{$data['TOP_MARK']}',NOTICE_DETAIL=cntent,notice_time=to_date('{$data['NOTICE_TIME']}','YYYY-MM-DD HH24:MI:SS') WHERE ( NOTICE_NO='{$NoticeInfo['NOTICE_NO']}');
end;
SQL;
```

### 自定义模板路径常量 **PUBLIC**，原理字符串替换

```
修改配置文件
    // 视图输出字符串内容替换
    'view_replace_str'       => [
        '__PUBLIC__'=> $_SERVER['REQUEST_SCHEME'] .'://' . $_SERVER['HTTP_HOST']. str_replace('/index.php' ,'' ,$_SERVER['SCRIPT_NAME'])
    ],
模板中直接使用 
<a class="btn btn-sm btn-warning " href="__PUBLIC__/download/lab_import.xlsx"><i class="fa fa-upload"></i> 下载导入模板</a>
```

### apache 配置路由重写

```
RewriteCond %{REQUEST_FILENAME} !-d
RewriteCond %{REQUEST_FILENAME} !-f
RewriteRule ^(.*)$ index.php [L,E=PATH_INFO:$1]
```

### tcpdf 打印 png 图片太慢

每张图片要打印十秒

解决：设置 resize = true 并且 dpi = 100

```
$pdf->Image($user_info['SIGN_PIC'], $i, $j, 50, 50,'','','',true,'100');
```

### 模型查询方法

1.  get 底层调用 find
2.  update 底层调用 model−>isUpdate(true)−>save(model->isUpdate(true)->save(model−>isUpdate(true)−>save(data,$where); 返回 int false ,使用empty可以判断
3.  all 底层调用 select

### tp 路由生成,模板及控制器

#### url 助手函数

参数:

1.  路由路径
2.  参数变量
3.  后缀
4.  域名

模板中使用 `{:url("")}` 生成URL 地址

如果在配置文件中配置了 url 的后缀为 html ,那么再生成 url 时,使用第三个参数 false ,可以去除配置的 html 后缀

```JavaScript
var url = "{:url('/download/','',false)}";
```

控制器中可以使用 `url` 助手函数生成url 地址.

```php
$url = url('/download/','',false);
```

生成的 URL 以当前服务器域名为主域名. 适用于一个域名对应一个或多个项目的情况

第四个参数为 二级域名, 如果配置了 第四个参数,那么会在主域名前生成一个二级域名

```php
$url = url('/download/','',false,'blog');
// 当前项目地址为 http://caoayu.xyz/project
// 生成 url 为 http://blog.caoayu.xuz/project/download
```
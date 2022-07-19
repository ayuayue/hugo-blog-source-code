---
title: 百度站点收录 - 什么叫他妈的自动推送
date: 2020-05-25T18:36:07+08:00
tags: [hexo]
categories: [hexo]
---



##### 百度收录问题

当自己搭建了一个个人网站或者是商业性质的网站时,我们通常希望在百度等搜索引擎上可以直接搜索到,提高网站的曝光率和流量.

但是百度对个人网站的收录十分的缓慢,步骤繁多,不像谷歌,提交完后几天就可以搜索到,无论怎么花式搜索都可以搜索到,而百度就有意思了,怎么都搜不到,快则半个月,慢则后年马月.

虽然百度会根据网址的流量及内容来判断审核,对原创内容的支持还是不错的,但是搜索引擎的算法上还是不如谷歌.

那就我们自己动手来加速一下收录的时长吧.

<!--more-->

对于网站的管理我们可以进入 [百度搜索资源平台]( https://ziyuan.baidu.com/linksubmit/index )

##### 验证工作

首先我们需要新增一个网站,进行网站的验证

网站的验证现在有三种方式

1. 文件验证 : 上传百度生成的验证文件到网站的根目录下,进行验证
2. `html` 验证 : 在 `html` 的 `head`标签中加入生成的 `meta` ,进行验证
3. `cname` 验证: 将自己的域名解析一个二级域名出来, 以`cname`的方式解析到百度生成的地址上,进行验证

*验证工作百度说明很详细,不在做赘述*

##### 验证`https`

如果你的网站开启了 `https` ,可以在 `HTTPS` 一栏进行提交验证

##### 链接提交

收录一般由百度的爬虫来完成,但是网络资源丰富,而且内容参差不齐,爬虫还要进行一些过滤等操作,会造成时效的一些损失,我们也可以通过自己主动的提交自己的网站链接给百度,加快我们的收录速度

链接提交的方式:

1. 主动推送 ,: 是有我们将 网站的一些链接主动的通过请求发送给百度,然后百度返回响应给我们,一般这种方式是最快的,百度也提供了很多的方式,这次我们使用 `php`来实现主动推送,推送实现在下面进行结算
2. 自动推送 :  在我们的网站上加入 `js`脚本 实现每次访问都自动推送到百度,比较省心,使用过后发现效率并不高,而且也动不动就在维护工具,不稳定
3. `sitemap` : 以站点地图的方式,将自己的站点所有连接生成到一个请求中,格式为 `txt 或 xml` ,`hexo`可以使用`npm`的一些包(如 `sitemap`)来生成自己站点的地图到一个链接上面,我们只需要将链接填进去,等待验证即可.
4. 手动提交 : 就是我们一个链接一个链接的填写,浪费时间,效率太低,不推荐

##### 主动推送的 `php` 实现

```php
<?php
/*
 * created_at 2020-05-25
 * created_by caoayu
 * myBlog https://blog.caoayu.top
 */
//baidu push api
//https://ziyuan.baidu.com/linksubmit/index 这里提供的 api 接口,普通收录,主动提交,php代码
$api = 'http://data.zz.baidu.com/urls?site=https://blog.caoayu.top&token=xxxxx';


//get site urls
function GetUrls($api):string{
    $site = "https://blog.caoayu.top/sitemap.xml";
    //获取 xml 信息
    $XmlUrls = curl($site,null);
    //将 xml 转为数组
    $resource = xml_parser_create();
    xml_parse_into_struct($resource,$XmlUrls,$value,$index);
    xml_parser_free($resource);
    /*
     * 打印输出后发现,所有连接的 tag 都为 LOC
     *      [tag] => LOC
            [type] => complete
            [level] => 3
            [value] => https://blog.caoayu.top/scp01/
       所以筛选出所有 tag = LOC 的 value 值 即为我们需要上传的 链接地址
     */
    //$urlArr 即为接受所有 url 的数组
    $urlArr = [];
    foreach ($value as $content){
        if ($content["tag"] === "LOC"){
            $urlArr[] = $content["value"];
        }

    }
    //开始上传
    $response = curl($api,$urlArr,1);
    //将响应 json 转为 对象
    $result = json_decode($response);
    if ($result->success != 0){
        return "主动提交成功!";
    }else{
        return  "主动提交失败!";
    }
}

function curl($url,$params,$is_post = 0){
    $ch = curl_init();

    curl_setopt($ch,CURLOPT_CONNECTTIMEOUT,60);
    curl_setopt($ch,CURLOPT_TIMEOUT,60);
    curl_setopt($ch,CURLOPT_RETURNTRANSFER,true);
    curl_setopt($ch,CURLOPT_FOLLOWLOCATION,true);
    curl_setopt($ch,CURLOPT_SSL_VERIFYHOST,false);//禁止验证对等证书,默认为true
    curl_setopt($ch,CURLOPT_SSL_VERIFYPEER,false);//检查服务器SSL证书中是否存在一个公用名,值0|2,
    if($is_post){
        curl_setopt($ch,CURLOPT_POST,true);
        curl_setopt($ch,CURLOPT_POSTFIELDS,$params);
        curl_setopt($ch,CURLOPT_URL,$url);
    }else{
        if($params){
            curl_setopt($ch,CURLOPT_URL,$url.'?'.$params);
        }else{
            curl_setopt($ch,CURLOPT_URL,$url);
        }
    }
    $response = curl_exec($ch);
    return $response;
}
echo GetUrls($api);

```

**注意每天可以上传的`url`限制数量是3000条**

**我们可以将`php` 脚本上传到宝塔,设置一个定时任务,每天晚上自动的主动推送一次**


---
cssclass:
title: PHP配置Oracle并导出所有表
tags: [IT/PHP, IT/Oracle]
image-auto-upload: true

date: 2022-10-03 20:32:46
lastmod: 2022-10-25 08:06:51
---
# PHP配置Oracle并导出所有表
1.  首先下载 Oracle 的即时客户端，下载地址：[https://www.oracle.com/database/technologies/instant-client/winx64-64-downloads.html](https://www.oracle.com/database/technologies/instant-client/winx64-64-downloads.html)

根据自己的机器及项目进行选择版本跟架构。

1.  将下载的即时客户端解压到没有中文的目录中，并加入到环境变量，最好最后重启一次。
2.  如果使用 pdo_oci 的方式进行连接，则直接将 php.ini 文件中的 pdo_oci，pdo_odbc 扩展打开即可。

extension=pdo_oci

1.  如果使用 oci 不使用 pdo_oci 的方式连接，请先安装 oci 扩展,并修改 php.ini 文件。

下载地址：[https://pecl.php.net/package/oci8](https://pecl.php.net/package/oci8) 。找到自己机器合适的扩展，下载放到 php 配置文件的扩展目录中。

关于 扩展目录，可以在 php.ini 文件中搜索 extension_dir 可以查找到。

扩展配置好后在 php.ini 文件中加入一行

extension=php_oci8.dll

关闭项目跟web服务。打开 phpinfo(); 进行验证。如果存在 oci8 的扩展项说明安装成功。

使用 php pdo 连接 Oracle 并将所有的表及字段导出到 html 中

```Bash
<!DOCTYPE html>

<html lang="en">

<head>

  <meta charset="UTF-8">

  <title>表格式导出</title>

  <style type="text/css">

    table.gridtable{

      font-family:微软雅黑;

      font-size:12px;

      width:100%;

      color:#333333;

      border-width:1px;

      border-color:#666666;

      border-collapse:collapse;

    }

    table.gridtable th{

      border-width:1px;

      padding:10px;

      border-style:solid;

      border-color:#666666;

      background-color:#dedede;

    }

    table.gridtable td{

      border-width:1px;

      padding:10px;

      border-style:solid;

      border-color:#666666;

      background-color:#ffffff;

    }

  </style>

</head>

<body>

<?php

set_time_limit(0);

setlocale(LC_CTYPE, 'POSIX');

$tns = "  

(DESCRIPTION =

  (ADDRESS_LIST =

    (ADDRESS = (PROTOCOL = TCP)(HOST = 172.44.0.5)(PORT = 1521))

  )

  (CONNECT_DATA =

    (SERVICE_NAME = xe)

  )

  )

    ";

$db_username = "SEU";

$db_password = "123456";

try{

  $conn = new \PDO("oci:dbname=".$tns.";charset=zhs16gbk",$db_username,$db_password);

  $sth = $conn->prepare("select table_name from user_tables");

  $sth->execute();

  $tbs = $sth->fetchAll(\PDO::FETCH_ASSOC);  

  //var_dump($tbs);die;

  $sql = $conn->prepare("select * from user_tab_comments");

  $sql->execute();

    $tab = $sql->fetchAll(\PDO::FETCH_ASSOC);

  $arr = [];

  // var_dump($tab);die;

  $a = 0;

  foreach($tab as $k=>$v){

    if($v['TABLE_TYPE']=='TABLE'){

      //foreach($tbs as $key=>$val){    

        if($v['COMMENTS'] == ''){

          $arr[$a]['COMMENTS'] = '无';

        }else{

          $arr[$a]['COMMENTS'] =iconv("gbk", "utf-8//ignore", $v['COMMENTS']);

        }  

      $arr[$a]['TABLE_NAME'] =$v['TABLE_NAME'];        

      //}

      $a++;

    }

  }

  // var_dump($arr);die;

  for($i = 0; $i < count ( $arr ); $i ++) {

    //$rs2 [$i] ['tb'] = strtolower ( $arr [$i]['TABLE_NAME'] );

    $rs2 [$i] ['tb'] =  ( $arr [$i]['TABLE_NAME'] );

    $rs2 [$i] ['zs'] = $arr[$i]['COMMENTS'];

      $sth = $conn->prepare("

        select col.COLUMN_NAME, com.Comments, col.DATA_TYPE, col.DATA_LENGTH

        from sys.all_tab_columns col,

          sys.all_col_comments com

        where col.owner = '{$db_username}'

        and col.table_name = '{$arr[$i]['TABLE_NAME']}'

        and com.Owner (+) = '{$db_username}'

        and com.Table_Name (+) = '{$arr[$i]['TABLE_NAME']}'

        and com.Column_Name (+) = col.Column_Name

        order by col.column_id");

        $sth->execute();

      $rs2 [$i] ['fd'] = $sth->fetchAll(\PDO::FETCH_ASSOC);

      foreach ( $rs2 [$i] ['fd'] as $k => $v ) {

        // $rs2 [$i] ['fd'] [$k] ['COLUMN_NAME'] = strtolower ( $v ['COLUMN_NAME'] );

        // $rs2 [$i] ['fd'] [$k] ['DATA_TYPE'] = strtolower ( $v ['DATA_TYPE'] );

        $rs2 [$i] ['fd'] [$k] ['COLUMN_NAME'] =  ( $v ['COLUMN_NAME'] );

        $rs2 [$i] ['fd'] [$k] ['DATA_TYPE'] =  ( $v ['DATA_TYPE'] );

        $rs2 [$i] ['fd'] [$k] ['DATA_LENGTH'] = $v ['DATA_LENGTH'] / 2;

      }

    }

}catch(PDOException $e){

  echo ($e->getMessage());

}

?>
<div class='dict'>
<b>
共<?=$a?>张表
</b>
<div id='top'></div>
<?php foreach($rs2 as $k=>$v1):?>
<b><a href='#<?=$v1['tb']?>'><?=$v1['tb']?></a>
（<a href='#<?=$v1['tb']?>'><?=$v1['zs']?></a>）
</b>
<br>

<?php endforeach;?>

<?php foreach($rs2 as $k=>$v1):?>

<table id='<?=$v1['tb']?>' class="gridtable">

  <caption><b><?=$v1['tb']?><a href='#top' style='font-size:12px;font-weight:normal;margin-left: 20px;'><?=$v1['zs']?></a></b></caption>

  <tr>

    <th width=210>字段</th>

    <th width=150>类型</th>

    <th>注释</th>

  </tr>

  <?php foreach($v1['fd'] as $v2):?>

    <tr>

      <td><?=$v2['COLUMN_NAME']?></td>

      <td><?=$v2['DATA_TYPE']?>(<?=$v2['DATA_LENGTH']?>)</td>

      <td><?=iconv("gbk", "utf-8//ignore", $v2['COMMENTS'])?></td>

    </tr>

  <?php endforeach;?>

</table>

<br />

<?php endforeach;?>

</div>

</body>
</html>
```
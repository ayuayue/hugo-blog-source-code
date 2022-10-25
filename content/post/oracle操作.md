---
cssclass:
title: oracle操作
tags: [IT/Oracle]
image-auto-upload: true
date: 2022-10-03 20:53:46
lastmod: 2022-10-25 08:39:50
---
# oracle操作
## 查看数据库服务状态：`lsnrctl status`

启动监听：`lsnrctl start`

## 连接 远程 or 本地

```
sqlplus user_haut/thinks5567@127.0.0.1:1521/chemdb.thinks.net.cn
```

查询当前用户

```
show user
select user from dual
```

查询所有用户

```
select * from all_users
```

## 创建用户

```
create user username
  identified by "password"
  [default tablespace ts_name]
  [temporary tablespace tempname|tempgroupname]
  [quota n size|unlimited on ts_name]
  [profile DEFAULT/profilename]
  [account lock|unlock]
  [password expire];
eg：create user caoayu identified by "123456";
```

编辑用户

```
alter user username
  identified by "password"
  [default tablespace ts_name]
  [temporary tablespace tempname|tempgroupname]
  [quota n size|unlimited on ts_name]
  [profile DEFAULT/profilename]
  [account lock|unlock]
  [password expire];
eg：alter user caoayu identified by '123456';
```

删除用户

```
drop user username cascade;
```

## 用户授权

授予系统权限

```
grant system_privilege|all privileges to user|role
[with admin option]
system_privilege:授予的权限名称，all privileges：指授予所有系统权限。
user|role：授予权限给用户还是角色
[with admin option]：指的是当前被授权的用户具有授权给其它用户系统权限的权利。
eg：grant create session to TEACHER;
```

查询用户授权

```
select * from dba_sys_privs t where t.GRANTEE='TEACHER'
```

授予对象权限

```
grant obj_privilege|all 
on obj_name  to user|role
[with grant option]
grant obj_privilege|all：给指定用户授予对象权限，all：指的是授予全部对象权限。
on obj_name to user|role：指的是把对象obj_name的权限授予给用户user或者角色role。
with grant option：指定是当前被授权的用户具有授权给其它用户该对象权限的权利。这里with grant option对应对象的授权是级联，而对于上面系统权限的with admin option的授权不是级联的，回收该用户的权限，不会把授予给别人的权限收回，而级联授权的就会把对应的权限都收回。
eg：grant select on student.stuinfo to teacher with grant ooption; 把 stuinfo 信息表授权给 teacher 用户
```

查询对象权限

```
select * from dba_tab_privs t where t.GRANTEE='TEACHER'
```

注意：给用户授权时，要注意自己是不能为自己授权的，要么利用第三方具有该对象授权权限的用户进行授权，或者利用具有 DBA 权限的用户授权。

撤销权限

撤销系统权限 -- 只用拥有 dba 权限的用户才可以

```
revoke system_privilege from user|role;
```

system_privilege：指的是撤销的系统权限的名称，撤销系统权限时，必须是该用户具有了该系统权限，假如不存在系统权限是不能进行撤销的。

user|role：指的是撤销权限的对象是用户还是角色。

```
eg：revoke create session from teacher;
```

撤销对象权限

```
revoke obj_privilege|all 
on object 
from  user|role;
obj_privilege|all：指的是对应的数据库对象的操作权限，all表示把所有的操作权限都撤销。
eg：revoke delete on student.stuinfo trom teacher; 撤销删除权限
```

注意：在进行撤销权限时，系统权限的撤销和对象权限的撤销是不一样的，通过上一章 Oracle 权限授权管理中知道，系统权限的授权不是级联的，对象权限的授权是级联的。

在撤销权限时也是一样的，在撤销对象权限时，那么该用户授予给其它用户的对象权限也要跟着收回。而撤销系统权限就不会收回其它用户的权限

连接 打开 sqlplus 输入用户名密码即可

连接到别的服务器

打开 sqlplus 输入 用户名/密码@地址：端口/服务名（sid）

```
eg：caoayu/123456@localhost:1521/orcl
```

## 导出表

```
exp 用户/密码@地址：端口/服务吗（sid）
eg：
exp caoayu/123456@localhost:1521/test_users file=d:\thinks\test\user.dmp tables=users buffer=30720 log=d:\thinks\test\user.log 
exp scut/123456@192.168.31.96:1521/xe file=d:\thinks\test\scut.dmp buffer=30720 owner=(scut) log=d:\thinks\test\scut.log
导出用户的所有表
exp caoayu/123456@localhost:1521/test_users file=d:\thinks\test\user.dmp  buffer=30720 log=d:\thinks\test\user.log  owner=(caoayu)
导入表
imp caoayu/123456@localhost/test_users file=d:\thinks\test\user.dmp tables=(users)
导入用户所有表（需要创建用户并授予 dba 的权限）
imp wzu/thinks5567@localhost:1521/test_users file=d:\thinks\test\wzu.dmp full=y
```

查看当然用户所有表

```
select table_name from user_tables;
select table_name from dba_tables where owner='用户名'
```

查用户序列，必须管理员

```
select SEQUENCE_OWNER,SEQUENCE_NAME from dba_sequences where sequence_owner='用户名';
查看序列是否存在
select * from user_sequences where sequence_name = 'NLDP_LGIR_BASE_SEQUENCE';
```

## 创建序列，用户隔离，不能操作其他用户的序列，管理员可以

```
CREATE SEQUENCE 序列名
　　[INCREMENT BY n] 
　　[START WITH n]
　　[{MAXVALUE/ MINVALUE n| NOMAXVALUE}]
　　[{CYCLE|NOCYCLE}]
　　[{CACHE n| NOCACHE}];
```

删除序列

```
drop sequence name
```

## 日期转字符串

```
to_char(usergrant.grant_end_validate,'yyyy-mm-dd hh24:mi:ss')
```

## 使用 impdp 导入表到新用户(需要在同一台设备操作)

1. 先创建逻辑目录

```
create directory dpdata1  as '/data/oracle/oradata/mydata'
```

2. 查看目录是否创建成功

```
select * from dba_directories
```

导入表

```
impdp caoayu/123456 directory=dpdata1 dumpfile=0508hiananu.dmp tables=hainanu.c2_chem_dict_chem remap_schema=hainanu:caoayu

导入用户的表 impdp ZJUT/thinks5567 directory=dpdata1 dumpfile=zjut202106.dmp REMAP_SCHEMA=ZJUT:ZJUT EXCLUDE=USER

使用系统帐号导入用户表并生成跟导入用户帐号密码一致的新用户（如果用户不存在） impdp system/Abc123456 directory=dpdata1 dumpfile=zjut202106.dmp REMAP_SCHEMA=zjuttest:zjuttest
```

## 使用 expdp 导出表(需要在同一台设备操作)

1. `select * from dba_directories` 查目录
2. 使用 system 创建一个目录

```
create directory dpdata1  as '/data/oracle/oradata/mydata'
expdp scott/tiger@orcl schemas=scott dumpfile=expdp.dmp DIRECTORY=dpdata1;
expdp scott/tiger@orcl TABLES=emp,dept dumpfile=expdp.dmp DIRECTORY=dpdata1;
```

## 定时任务，存储过程

第一步：

```
/* Formatted on 2019/3/19 11:58:18 (QP5 v5.256.13226.35538) */
CREATE OR REPLACE PROCEDURE WZU.reset_sequence (
   seq_name     IN VARCHAR2,
   startvalue   IN NUMBER)
AS
   cval     INTEGER;
   inc_by   VARCHAR2 (25);
BEGIN
   EXECUTE IMMEDIATE 'ALTER SEQUENCE ' || seq_name || ' MINVALUE 0';
   EXECUTE IMMEDIATE 'SELECT ' || seq_name || '.NEXTVAL FROM dual' INTO cval;
   cval := cval - startvalue;
   IF cval < 0
   THEN
      inc_by := ' INCREMENT BY ';
      cval := ABS (cval);
   ELSE
      inc_by := ' INCREMENT BY -';
   END IF;
   EXECUTE IMMEDIATE 'ALTER SEQUENCE ' || seq_name || inc_by || cval;
   EXECUTE IMMEDIATE 'SELECT ' || seq_name || '.NEXTVAL FROM dual' INTO cval;
   EXECUTE IMMEDIATE 'ALTER SEQUENCE ' || seq_name || ' INCREMENT BY 1';
END reset_sequence;
/
```

第二步：

```
DECLARE
  X NUMBER;
BEGIN
    SYS.DBMS_JOB.SUBMIT
    ( job       => X 
     ,what      => 'RESET_SEQUENCE
  (''SEQ_CHEM_ORDERFORM'' ,to_number(to_char(to_date(sysdate),''yyyymmdd'') || ''0000'' ));'
     ,next_date => to_date('20/03/2019 00:00:00','dd/mm/yyyy hh24:mi:ss')
     ,interval  => 'TRUNC(SYSDATE+1)'
     ,no_parse  => FALSE
    );
    SYS.DBMS_OUTPUT.PUT_LINE('Job Number is: ' || to_char(x));
  COMMIT;
END;
/
```

查询定时任务

```
select * from user_jobs;
```

执行任务

```
begin
  DBMS_JOB.RUN(40); /*40 job的id*/
end;
```

删除任务

```
begin
  /*删除自动执行的job*/
  dbms_job.remove(40);
end;
```

停止任务

```
dbms.broken(job，broken，nextdate);   
dbms_job.broken(v_job,true,next_date);       
/*停止一个job,里面参数true也可是false，next_date（某一时刻停止）也可是sysdate（立刻停止）。   */
```

## 安装配置,重启关机,新增服务名,导入导出

linux 安装配置 oracle

## 查询当前用户的统计信息

```
select 'analyze table '||table_name||' compute statistics;' from user_tables;  
```

## rownum 分页

由于 rownum 从 1 开始赋值给每条结果集，所以不能从大于 1 的值开始查。

需要将所有的 rownum 结果存在子查询中，生成一张临时表，将 rownum 字段变成表的普通字段，就可以正常查询范围了。

首先查询出所有的结果，外部嵌套一层查找 rownum 的字段，再嵌套一层

```
SELECT
    temp1.*
FROM
    (
        SELECT
            temp.*,
            ROWNUM rn
        FROM
            (
                SELECT
                    a.c_sale_no,
                    c_product_name,
                    es_mark,
                    es_id
                FROM
                    qg_v_sale_chem a
                    INNER JOIN (
                        SELECT
                            c_sale_no
                        FROM
                            qg_v_sale_chem
                        WHERE
                            ROWNUM < 10000
                        ORDER BY
                            c_sale_no
                    ) b ON a.c_sale_no = b.c_sale_no
            ) temp
    ) temp1
WHERE
    temp1.rn >= 0
```

## oracle 数据库用户密码到期(去除限制,或者再次修改为原来的密码,限制还在)

查询所有用户的 profile

```
SELECT username,profile FROM dba_users;
```

默认是 default , 查询 default 的密码限制时间

```
SELECT * FROM dba_profiles s WHERE s.profile='DEFAULT' AND resource_name='PASSWORD_LIFE_TIME';
```

修改密码为不限制

```
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED; 
```

如果已经提示快要到期,需要重新修改用户密码(设置为不限制后)

```
alter user caoayu identified by 123456;
```

## 查看某一张表或视图的某一列的属性

表名列名 都要大写

```
SELECT * FROM USER_TAB_COLUMNS where table_name='CHEM_V_INHLS_VIEW' AND COLUMN_NAME='VALUME';
```

## number 小数查询转字符串,小数点前的 0 丢失问题

```
to_char(valume,'FM9999990.99999999')
```

## PDO 查询过长 oracle 字段长导致截断报错

`PDOStatement:: fetchAll(): column 0 data was too large for buffer and was truncated to fit it`

1. 修改表结构,增加长度
2. 转换 cast( Lab.LAB_NAME AS varchar2(512) ) as LAB_NAME

## 关联更新

```SQL
UPDATE EMPLOYEE E SET AGE = (SELECT U.AGE FROM T_USER U WHERE E.ID=U.ID ) 
WHERE EXISTS (SELECT 1 FROM T_USER U WHERE E.ID=U.ID AND E.AGE IS NULL  )
```

```SQL
MERGE INTO C2_CHEM_ORDERFORM E USING C2_BASE_DICT_LABROOM U
 ON (E.ROOM_NO=U.LABROOM_ID ) WHEN MATCHED THEN UPDATE SET E.ROOM_NAME=U.NAME
```

## 删除重复数据,并保留一条

1. 分组查出所有 count 大于 1 的,在查询所有大于 1 的行号,只保留最小的行号那一条记录,较慢

```SQL
Delete
From ZJNU_HXPGL.CHEM_DICT_CHEM t
Where (chem_id) In
      ( -- 重复数据
      Select chem_id
       From ZJNU_HXPGL.CHEM_DICT_CHEM
       Group By chem_id
       Having Count(*) > 1)
  And Rowid Not In (
  -- 重复中最小的行号
  Select Min(Rowid)
                    From ZJNU_HXPGL.CHEM_DICT_CHEM
                    Group By chem_id
                    Having Count(*) > 1);
```

2. 直接查出 rowid,用 not in 判断,较慢

```sql
DELETE FROM ZJNU_HXPGL.CHEM_DICT_CHEM
WHERE ROWID IN (
                 SELECT ROWID
                 FROM (
                        SELECT chem_id,
                               ROWID,
                               ROW_NUMBER() OVER(PARTITION BY chem_id order by chem_id) AS staff_row --按照保留的唯一字段进行分区，取row_number
                               FROM ZJNU_HXPGL.CHEM_DICT_CHEM
                      )
                 WHERE staff_row > 1
               );
```

3. 查询不在分组最小行号的,较慢

```sql
DELETE FROM ZJNU_HXPGL.CHEM_DICT_CHEM t1
WHERE t1.ROWID NOT IN (
                       SELECT MIN(t2.ROWID)
                       FROM ZJNU_HXPGL.CHEM_DICT_CHEM t2
                       GROUP BY t2.chem_id --按照想要唯一保留的字段进行分组
                      );
```

4. 通过连接查询,最后删除大于最小行号的

```sql
DELETE FROM ZJNU_HXPGL.CHEM_DICT_CHEM t1
WHERE t1.rowid > (
                   SELECT MIN(t2.rowid)
                   FROM ZJNU_HXPGL.CHEM_DICT_CHEM t2
                   WHERE t1.chem_id = t2.chem_id --按照想要唯一保留的字段进行匹配
                 );
```

## 查询表中所有重复数据,并保留一个,使用 row_number 函数

```SQL
-- 重复数据的行号会从小到大按顺序排,可以过滤重复的 >1 或只保留一个 < 2
select t.*,
       row_number() over(partition by chem_id order by t.chem_id) num
  from zjnu_hxpgl.chem_dict_chem t where num < 2
```

## 查询数据库中被锁定的表信息

```SQL
select t2.username,
       t2.sid,
       t2.serial#,
       t3.object_name,
       t2.OSUSER,
       t2.MACHINE,
       t2.PROGRAM,
       t2.LOGON_TIME,
       t2.COMMAND,
       t2.LOCKWAIT,
       t2.SADDR,
       t2.PADDR,
       t2.TADDR,
       t2.SQL_ADDRESS,
       t1.LOCKED_MODE
  from v$locked_object t1, v$session t2, dba_objects t3
 where t1.session_id = t2.sid
   and t1.object_id = t3.object_id
 order by t2.logon_time;
```

## 查询会话正在执行的 SQL 语句

```SQL
SELECT /*+ ORDERED */ 
 sql_text
  FROM v$sqltext a
 WHERE (a.hash_value, a.address) IN
       (SELECT DECODE(sql_hash_value, 0, prev_hash_value, sql_hash_value),
               DECODE(sql_hash_value, 0, prev_sql_addr, sql_address)
          FROM v$session b
         WHERE b.sid = '67')  /* 此处67 为SID*/
 ORDER BY piece ASC;
```

## 查询当前会话 ID

```SQL
select sid from v$mystat where rownum=1;
```

## 使用管理员身份解除表锁

```SQL
alter system kill session 'sid,seial#';
```

## 查询 PSID 然后 kill

```text
select s.sid, s.serial#, s.LOGON_TIME, s.machine, p.spid, p.terminal
from v$session s, v$process p
 where s.paddr = p.addr
 and s.sid = 42
```

## 表锁模式

^9f47fd

0：none
1：null 空
2：Row-S 行共享(RS)：共享表锁，sub share
3：Row-X 行独占(RX)：用于行的修改，sub exclusive
4：Share 共享锁(S)：阻止其他 DML 操作，share
5：S/Row-X 共享行独占(SRX)：阻止其他事务操作，share/sub exclusive
6：exclusive 独占(X)：独立访问使用，exclusive

数字越大锁级别越高, 影响的操作越多。

1 级锁有：Select，有时会在 v$locked_object 出现。
2 级锁有：Select for update,Lock For Update,Lock Row Share
select for update 当对话使用 for update 子串打开一个游标时，所有返回集中的数据行都将处于行级(Row-X)独占式锁定，其他对象只能查询这些数据行，不能进行 update、delete 或 select for update 操作。
3 级锁有：Insert, Update, Delete, Lock Row Exclusive
没有 commit 之前插入同样的一条记录会没有反应, 因为后一个 3 的锁会一直等待上一个 3 的锁, 我们必须释放掉上一个才能继续工作。
4 级锁有：Create Index, Lock Share
locked_mode 为 2,3,4 不影响 DML(insert,delete,update,select)操作, 但 DDL(alter,drop 等)操作会提示 ora-00054 错误。
00054,00000, “resource busy and acquire with NOWAIT specified”
// *Cause: Resource interested is busy.
// *Action: Retry if necessary.
5 级锁有：Lock Share Row Exclusive
具体来讲有主外键约束时 update / delete … ; 可能会产生 4,5 的锁。
6 级锁有：Alter table, Drop table, Drop Index, Truncate table, Lock Exclusive

## 查询包含中文值得记录,不包含 not in 过滤

```SQL
select chem_id from CHEM_DICT_CHEM where REGEXP_LIKE(CHEM_NAME,'[' || unistr('\0391') || '-' || unistr('\9fa5') || ']')
```

## 修改表字段类型
通过建临时字段完成
```sql
alter table CHEM_DICT_LABROOM_TEST rename column LABROOM_ID to LABROOM_ID_TMP;  
alter table CHEM_DICT_LABROOM_TEST add LABROOM_ID varchar2(512);  
update CHEM_DICT_LABROOM_TEST set LABROOM_ID = trim(LABROOM_ID_TMP);  
alter table CHEM_DICT_LABROOM_TEST add primary key (LABROOM_ID);  
alter table CHEM_DICT_LABROOM_TEST drop column LABROOM_ID_TMP;
```



# 从 SQL Server 迁移到 DM

本章节主要介绍从 SQL Server 迁移到 DM 常见问题，为用户提供从 SQL Server 迁移到 DM 常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [数据迁移过程中,自增列已有的数据不变方法](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#%E6%95%B0%E6%8D%AE%E8%BF%81%E7%A7%BB%E8%BF%87%E7%A8%8B%E4%B8%AD,%E8%87%AA%E5%A2%9E%E5%88%97%E5%B7%B2%E6%9C%89%E7%9A%84%E6%95%B0%E6%8D%AE%E4%B8%8D%E5%8F%98%E6%96%B9%E6%B3%95)
  * [字段类型 varbinary(max) 迁移报错：数据大小已超过可支持范围](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#%E5%AD%97%E6%AE%B5%E7%B1%BB%E5%9E%8B%20varbinary\(max\)%20%E8%BF%81%E7%A7%BB%E6%8A%A5%E9%94%99%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%A4%A7%E5%B0%8F%E5%B7%B2%E8%B6%85%E8%BF%87%E5%8F%AF%E6%94%AF%E6%8C%81%E8%8C%83%E5%9B%B4)
  * [使用 DTS 将表数据从 SQL Server 迁移到 DM 报错：无效的日期或时间类型](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#%E4%BD%BF%E7%94%A8%20DTS%20%E5%B0%86%E8%A1%A8%E6%95%B0%E6%8D%AE%E4%BB%8E%20SQL%20Server%20%E8%BF%81%E7%A7%BB%E5%88%B0%20DM%20%E6%8A%A5%E9%94%99%EF%BC%9A%E6%97%A0%E6%95%88%E7%9A%84%E6%97%A5%E6%9C%9F%E6%88%96%E6%97%B6%E9%97%B4%E7%B1%BB%E5%9E%8B)
  * [在 sqlserver 建外部链接的时候报错：指定驱动程序无法加载](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#%E5%9C%A8%20sqlserver%20%E5%BB%BA%E5%A4%96%E9%83%A8%E9%93%BE%E6%8E%A5%E7%9A%84%E6%97%B6%E5%80%99%E6%8A%A5%E9%94%99%EF%BC%9A%E6%8C%87%E5%AE%9A%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F%E6%97%A0%E6%B3%95%E5%8A%A0%E8%BD%BD)
  * [DTS 迁移 SqlServer2019 数据库时连接失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#DTS%20%E8%BF%81%E7%A7%BB%20SqlServer2019%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%97%B6%E8%BF%9E%E6%8E%A5%E5%A4%B1%E8%B4%A5)
  * [DTS 迁移工具连接 SQL SERVER 失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#DTS%20%E8%BF%81%E7%A7%BB%E5%B7%A5%E5%85%B7%E8%BF%9E%E6%8E%A5%20SQL%20SERVER%20%E5%A4%B1%E8%B4%A5)
  * [迁移 SQL server 到 DM 时报错：“此列列表已索引”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#%E8%BF%81%E7%A7%BB%20SQL%20server%20%E5%88%B0%20DM%20%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E6%AD%A4%E5%88%97%E5%88%97%E8%A1%A8%E5%B7%B2%E7%B4%A2%E5%BC%95%E2%80%9D)
  * [SqlServer 迁移到 DM 涉及到 SqlServer 中的 convert 函数需要改写](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#SqlServer%20%E8%BF%81%E7%A7%BB%E5%88%B0%20DM%20%E6%B6%89%E5%8F%8A%E5%88%B0%20SqlServer%20%E4%B8%AD%E7%9A%84%20convert%20%E5%87%BD%E6%95%B0%E9%9C%80%E8%A6%81%E6%94%B9%E5%86%99)
  * [sqlserver 迁移到达梦数据库时达梦数据库如何支持 with (nolock) 语法](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#sqlserver%20%E8%BF%81%E7%A7%BB%E5%88%B0%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E6%97%B6%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A6%82%E4%BD%95%E6%94%AF%E6%8C%81%20with%20\(nolock\)%20%E8%AF%AD%E6%B3%95)
  * [sqlserver 的 for xml path 语法迁移到达梦无法执行，如何进行改写？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#sqlserver%20%E7%9A%84%20for%20xml%20path%20%E8%AF%AD%E6%B3%95%E8%BF%81%E7%A7%BB%E5%88%B0%E8%BE%BE%E6%A2%A6%E6%97%A0%E6%B3%95%E6%89%A7%E8%A1%8C%EF%BC%8C%E5%A6%82%E4%BD%95%E8%BF%9B%E8%A1%8C%E6%94%B9%E5%86%99%EF%BC%9F)
  * [SqlServer 到 DM 进行装载数据时报错：“EXE[ERROR]：建表失败，执行挂起”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#SqlServer%20%E5%88%B0%20DM%20%E8%BF%9B%E8%A1%8C%E8%A3%85%E8%BD%BD%E6%95%B0%E6%8D%AE%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9CEXE\[ERROR\]%EF%BC%9A%E5%BB%BA%E8%A1%A8%E5%A4%B1%E8%B4%A5%EF%BC%8C%E6%89%A7%E8%A1%8C%E6%8C%82%E8%B5%B7%E2%80%9D)
  * [sqlserver 的 @@ROWCOUNT 获取影响行数功能在达梦是否有替代方案](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html#sqlserver%20%E7%9A%84%20@@ROWCOUNT%20%E8%8E%B7%E5%8F%96%E5%BD%B1%E5%93%8D%E8%A1%8C%E6%95%B0%E5%8A%9F%E8%83%BD%E5%9C%A8%E8%BE%BE%E6%A2%A6%E6%98%AF%E5%90%A6%E6%9C%89%E6%9B%BF%E4%BB%A3%E6%96%B9%E6%A1%88%E3%80%82)



## 正文

原有的系统或开发习惯为 SQL Server 的用户，可以参考文档[《DM DBA 手记之 SQLServer 移植到 DM》](https://eco.dameng.com/eco-file-server/file/eco/download/20220816141120BHE25KK7KHKUQFD55K)。

## 数据迁移过程中,自增列已有的数据不变方法

  * 测试过程中，先将待迁移表 TABLE3 重命名成 TABLE2



```
ALTER TABLE TABLE_3 rename TO TABLE_2;
```

  * 导出建表语句，将创表 SQL 粘贴到新 SQL 编辑器后，将表名修改成重命名前的表名，将 id 列的类型修改成 int 或者 bigint 类型，再加上自增列的当前值和增长值



```
CREATE TABLE "SYSDBA"."TABLE_3"
(
"id" int NOT NULL IDENTITY(1, 1),
"name" VARCHAR2(50),
NOT CLUSTER PRIMARY KEY("id")) STORAGE(ON "MAIN", CLUSTERBTR) ;
```

  * 执行以下 SQL，允许将显式值插入表的自增列中



```
SET IDENTITY_INSERT "SYSDBA"."TABLE_3"  ON;
```

  * 数据插入到新表中



```
insert into "SYSDBA"."TABLE_3"("id","name") select * from TABLE_2;
```

  * 关闭显示插入



```
SET IDENTITY_INSERT "SYSDBA"."TABLE_3"  OFF;
```

## 字段类型 varbinary(max) 迁移报错：数据大小已超过可支持范围

【问题解决】：

按照提示修改字段类型 varbinary 为 blob，增大数据可支持大小，重新迁移即可。

## 使用 DTS 将表数据从 SQL Server 迁移到 DM 报错：无效的日期或时间类型

【问题解决】：

DTS 迁移工具从 SQL Server 迁移数据时，对于表的定义，会将 DATETIME 时间类型的字段定义，转换成 TIMESTAMP 类型；但是对于表的数据，会将 DATETIME 类型的字段数据，转换成 TIMESTAMP WITH TIME ZONE，格式变成’2021-10-24 12:00:00.000 +08:00’。使得实际数据和字段定义不相匹配，导入失败报错。
这种情况下，可以将迁移的目标库迁移用户下的所有 TIMESTAMP 类型字段转换为 TIMESTAMP WITH TIME ZONE 类型，待表数据迁移完后再改回。

## 在 sqlserver 建外部链接的时候报错：指定驱动程序无法加载

【问题描述】：

在 sqlserver 建外部链接的时候报错：通过访问接口"Microsoft OLE DB Provider for ODBC Drivers" 建立，sqlserver 报错 7303，无法初始化连接服务器 的 OLE DB 访问接口"MSDASQL"的数据源对象；由于系统错误 5：拒绝访问(DM8 ODBC DRIVER ,C:dmdbms\bin\dodbc.dll),指定驱动程序无法加载。

【问题解决】:

方法 1：检查系统变量，是否有达梦客户端的安装目录，没有的话，添加达梦的系统变量；
方法 2：把 bin 下面的文件拷贝到对应操作系统的系统变量目录(注意：windows 有 32、64 之分) ，拷贝的时候看下运行的 sqlserver 客户端是多少位的。
注： winserver 2012 要看下 bin 下面的权限是否正常，不能是只读，有些客户为了系统安全性设置的比较高。
配置完成后，重启客户端即可。

## DTS 迁移 SqlServer2019 数据库时连接失败

【问题描述】

DTS 迁移 SqlServer2019 数据库，在与 SqlServer 数据库建立连接时报错：通过端口 1433 连接到主机 192.168.1.2 的 TCP/IP 连接失败。错误:“connect timed out。请验证连接属性。确保 SQL Server 的实例正在主机上运行，且在此端口接受 TCP/IP 连接，还要确保防火墙没有阻止到此端口的 TCP 连接。”。报错截图如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202210281132019REQ83W8QNXJBBB7Y1)

【问题解决】

  1. 检查防火墙策略，确保防火墙对应的 SqlServer 端口 1433 开放。
  2. 同时确保开启 SqlServer 的 TCP/IP 协议，默认为禁用状态。



SqlServer 的 TCP/IP 协议开启方法：

打开 Sql Server Configuration manage 配置工具，依次找到“Sql server 网络配置”→“MSSQLSERVER 的协议”→“TCP/IP”，默认 TCP/IP 协议为禁用状态，右击启用 TCP/IP 协议。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221028113233TNQL9IQ0OEJ4G71Y12)

重启 SqlServer 数据库服务使修改生效。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221028113502L8NX7EF8NA6Q7VZWK4)

## DTS 迁移工具连接 SQL SERVER 失败

【问题描述】

DTS 迁移工具连接 SQL SERVER 时报错：”The server selected protocol version TLS10 is not accepted by client preferences [TLS13, TLS12]“。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221226143538PH2QNB33TULG0VI7IF)

【问题分析】

历史版本的 SQL SERVER 服务端默认使用 TLS1.0 版本协议对外提供服务，因安全问题 DTS 迁移工具部分版本默认已关闭该协议。

【问题解决】

  1. 修改 java.security 文件。



在达梦数据库安装目录下的 jdk\jre\lib\security\ 路径下找到 java.security 文件，备份后直接打开编辑，找到 jdk.tls.disabledAlgorithms 配置项，如果存在 TLSv1，TLSv1.1，3DES_EDE_CBC 等配置，直接删掉，然后保存 java.security 文件。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2022122614365845M1700N9JBOCN4OQN)

  2. 重启 DTS 迁移工具后，再次进入连接界面即可以正常连接 SQL SERVER 。



## 迁移 SQL server 到 DM 时报错：“此列列表已索引”

【问题描述】

从 SQL server 迁移到 DM 时报错：“此列列表已索引”。

【问题分析】

在 SQL server 中，由于对创建索引的列存在索引未校验，所以在 SQL server 中可存在两个索引属于同一列的索引，但在达梦中不允许同时存在属于同一列的两个索引。

【问题解决】

例如：以下语法，在 SQL server 中可执行通过，但在达梦执行到第三条语句时执行失败，报错“此列列表已索引”。

```
CREATE TABLE "TABLE_1"
(
"COLUMN_1" CHAR(10),
"COLUMN_2" CHAR(10))  ;
CREATE  INDEX "a" ON "TABLE_1"("COLUMN_1" )  ;
CREATE  INDEX "b" ON "TABLE_1"("COLUMN_1" )  ;
```

该问题报错可以忽略。

## SqlServer 迁移到 DM 涉及到 SqlServer 中的 convert 函数需要改写

【问题描述】

SqlServer 迁移到 DM 时在存储过程、函数及视图中大量引用了 convert 函数该函数，例如：

```
select CONVERT(varchar(10),getdate(),110)
```

当使用该 SQL 在达梦数据库中执行时会报错。

```
-2007: 第 1 行, 第 63 列[,]附近出现错误:
语法分析出错
```

【问题分析】

从 convert 函数实现语法来看

sqlserver convert 函数实现语法：

```
CONVERT ( data_type [ ( length ) ] , expression [ , style ] )
```

达梦中的 convert 实现语法：

```
CONVERT(type,value)
```

sqlserver 的 convert 函数多了 expression [ , style ] 参数，此参数含义是指日期时间格式，对于日期或时间数据类型的 expression，style 可以具有下表所示的某个值。 其他值作为 0 进行处理。 从 SQL Server 2012 (11.x) 开始，在从日期和时间类型转换为 datetimeoffset 时支持的唯一样式是 0 或 1。 所有其他转换样式均返回错误 9809。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230421093315BGR6T2D5BFJSLFPUC7)

【问题解决】

在 DM 中可以通过 to_char 对 SqlServer 中的 convert 函数进行改写.

例如：
sqlserver 中 SQL：

```
select convert(char(19),GETDATE(),120);
```

120 的意思是时间格式化为  24 小时时间格式

在达梦数据库中改写：

```
select to_char(CONVERT(char(19),getdate()),'yyyy-mm-dd hh24:MM:ss');
```

## sqlserver 迁移到达梦数据库时达梦数据库如何支持 with (nolock) 语法

【问题描述】

sqlserver 迁移到达梦数据库时达梦数据库如何支持 with (nolock) 语法。

例如：

```
select id from employee e with (nolock);
```

【问题分析】

sqlserver 中 with (nolock) 除了本身不锁表，也不会受其他的已存在的锁影响， 锁住的行数据也照样读，就是脏读的意思。达梦存在相应的功能 with ur，DM 允许用户在 SELECT 语句的末尾加上 WITH UR 以指定当前查询语句的隔离级为读未提交，即允许脏读，并在该语句结束时自动恢复为原来的隔离级。

【问题解决】

SqlServer 中的 SQL

```
select id from employee e with (nolock);
```

在达梦数据库中可改写为：

```
select id from employee e with ur;
```

sqlserver 中关联查询在达梦数据库中也可以改写，只需要在 select 语句的最后面添加 with ur 即可，不需要单独每张表进行标记。

例如：

SqlServer 中的 SQL：

```
select e.employee_id ,
          e.employee_name,
          department_name  
     from employee e with (nolock)
left join DEPARTMENT d with (nolock)
       on e.DEPARTMENT_ID=d.DEPARTMENT_ID;
```

在达梦数据库中可改写为：

```
select e.employee_id ,
          e.employee_name,
          department_name  
     from employee e
left join DEPARTMENT d
       on e.DEPARTMENT_ID=d.DEPARTMENT_ID with ur;
```

## sqlserver 的 for xml path 语法迁移到达梦无法执行，如何进行改写？

【问题描述】

sqlserver 的 for xml path 语法用于字符串拼接，迁移到达梦无法执行，如何进行改写呢？

【问题分析】

sqlserver 的 for xml path 语法和达梦的 listagg 以及 xmlagg 输出形式一致，可直接进行改写。

【问题解决】

例如：

创建测试表 student 。

```
create table student(stuID int,sName varchar(10),hobby varchar(10));
insert into student values(1,'张三','爬山');
insert into student values(1,'张三','游泳');
insert into student values(2,'李四','美食');
insert into student values(2,'李四','美食');
insert into student values(3,'王五','爬山');
insert into student values(4,'王五','游泳');
commit;
```

在 SqlServer 中使用 FOR XML PATH 语法。

```
--
SELECT B.sName,
      LEFT(StuList,LEN(StuList)-1) as hobby
 FROM (   SELECT sName, (SELECT hobby+',' FROM student
 WHERE sName=A.sName
 FOR XML PATH('')) AS StuList
   FROM student A
GROUP BY sName) B
```

在达梦数据库中可采用如下两种方式进行改写。

  1. 因为 sqlserver 的拼接结果最后会带有逗号，而 listagg 默认最后不会拼接逗号，所以最终解决办法为 listagg 后面直接手动拼接逗号解决，返回的数据类型为 varchar，如下：



```
select sname,listagg(hobby,',') within group (order by hobby)||',' from student group by sname;
```

  2. xmlagg 可解决上面 1 的问题，xmlagg 最后返回的数据类型为 text。



```
select sname,xmlagg(xmlparse(content hobby ||','wellformed)order by hobby).getclobval() from student group by sname;
```

## SqlServer 到 DM 进行装载数据时报错：“EXE[ERROR]：建表失败，执行挂起”

【问题描述】

源端 SqlServer 目的端 DM 在进行装载数据时报错：“EXE[ERROR]：建表失败，执行挂起”

【问题解决】

可以通过在目的端 exec 的配置文件 dmhs.hs 中添加参数 `<ddl_continue>1</ddl_continue>`  来解决。

ddl_continue 参数说明：

含义：DDL 出错时是否允许继续同步，可选 0（挂起）或 1（继续），默认值 0。

说明：EXEC 模块在执行 DDL 时，比如修改表等操作，可能会执行失败，当 DDL 失败时同步流程是否继续，需要通过该参数来指定。

## sqlserver 的 @@ROWCOUNT 获取影响行数功能在达梦是否有替代方案。

【问题解决】

可考虑将 sql 语句放到程序块里，用隐式游标 SQL 的 ROWCOUNT 属性获取影响行数，如下：

```
drop table if exists trow;
create table trow (c1 int,c2 varchar(100));
begin
print ('执行前打印影响行数：' || SQL%ROWCOUNT);
insert into trow values(1,'Hello world!'); --SQL1
print ('commit前打印SQL1 影响行数：' || SQL%ROWCOUNT);

insert into trow values(3,'Hello world!'),(4,'Hello world!'); --SQL2
print ('commit前打印SQL2影响行数：' || SQL%ROWCOUNT);
commit;
print ('commit后打印SQL2影响行数：' || SQL%ROWCOUNT);
end;
/
```

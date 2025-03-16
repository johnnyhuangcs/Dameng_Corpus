

# DBMS_METADATA 包

GET_DDL 函数用于获取数据库对表、视图、索引、全文索引、存储过程、函数、包、序列、同义词、约束、触发器等的 DDL 语句。

## 10.1 数据类型

DBMS_METADATA 包中涉及到类型。如下统一说明。

  1. KU$_PARSED_ITEM 记录类型



```
TYPE KU$_PARSED_ITEM IS RECORD (
	ITEM			VARCHAR(30),
	VALUE			VARCHAR(4000),
	OBJECT_ROW		SMALLINT
);
```

参数详解

  * ITEM

被解析对象属性的名称。

  * VALUE

对象属性值，如果没有就是 NULL。

  * OBJECT_ROW

对象所在的行数。



KU$_PARSED_ITEMS 为 KU$_PARSED_ITEM 类型的索引表：

```
TYPE KU$_PARSED_ITEMS IS TABLE OF KU$_PARSED_ITEM INDEX BY INT;
```

  2. KU$_DDL 记录类型



```
TYPE KU$_DDL IS RECORD(
	DDLTEXT			CLOB,
	PARSEDITEM		KU$_PARSED_ITEMS 
);
```

参数详解

  * DDLTEXT

DDL 语句。

  * PARSEDITEM

对象属性。



KU$_DDLS 为 KU$_DDL 类型的索引表：

```
TYPE KU$_DDLS IS TABLE OF KU$_DDL INDEX BY INT;
```

  3. KU$_MULTI_DDL 记录类型



```
TYPE KU$_MULTI_DDL IS RECORD(
	OBJECT_ROW		NUMBER,
	DDLS			KU$_DDLS
);
```

参数详解

  * OBJECT_ROW

对象所在的行数。

  * DDLS

KU$_DDLS 类型。



KU​\_MULTI_DDLS为KU_MULTI_DDL 类型的索引表：

```
TYPE KU$_MULTI_DDLS IS TABLE OF KU$_MULTI_DDL INDEX BY INT;
```

## 10.2 相关方法

  1. GET_DDL



获取指定对象元数据中的 DDL 语句。

权限使用说明：

用户只能看到自己被授权的对象定义。拥有 dba/object_admin 权限可看到其他用户的对象定义。

用户可查询自己模式下的对象定义。拥有 dba/object_admin 权限才能查看 sys 模式下的对象定义。

SYSDBA 和普通用户均无权限查询安全、审计下用户的对象定义；安全、审计用户可以查询 SYSDBA 或普通用户的任何对象定义。

语法如下：

```
FUNCTION GET_DDL(
	OBJECT_TYPE		IN		VARCHAR(30),
	NAME			IN		VARCHAR(128), 
	SCHEMA			IN		VARCHAR(128)	DEFAULT NULL
) RETURN CLOB
```

参数详解

  * object_type

对象类型。包括表、视图、物化视图、索引、全文索引、存储过程、函数、包、目录等，详情请见 open 参数详解。其中，object_type 只能为大写。

  * name

对象名称，区分大小写。

  * schema

模式，默认是当前用户模式。



返回值

以 ddl 返回对象元数据中的 DDL 语句。

错误处理

INVALID_ARGVAL：如果输入参数中存在空值或非法值。

OBJECT_NOT_FOUND：如果指定的对象在数据库中不存在。

  2. ”OPEN”



打开对象类型的句柄

语法如下：

```
FUNCTION OPEN (
	OBJECT_TYPE		IN		VARCHAR2
) RETURN NUMBER;
```

参数详解

  * object_type



可返回的对象类型名称，合法的类型名称见下表 11.1。

表11.1 OPEN函数可返回的对象类型名称

|**类型名称**|**含义**|**属性**|**说明**|
|----|----|----|----|
|CLASS|类类型|SN|默认返回类头类体|
|CLASS_HEAD|类型名|SN|无|
|CLASS_BODY|类型体|SN|无|
|COL_STATISTICS|列统计|D|无|
|COMMENT|注释|D|无|
|CONSTRAINT|约束|SND|不包括聚集主键和非空约束|
|CONTEXT|上下文|N|无|
|CONTEXT_INDEX|全文索引|N|无|
|DATABASE_EXPORT|数据库下的所有对象|H|库级导出|
|DB_LINK|数据库链接|SN|因此类对象具有所有者,因此将其视为模式级对象。对于公有连接，它们的所有者是 PUBLIC；对于私有链接，它们的创建者就是它们的所有者|
|DIRECTORY|目录|N|无|
|DOMAIN|域|N|无|
|FUNCTION|存储函数|SN|无|
|INDEX|索引|SND|不包括系统内部定义的索引|
|INDEX_STATISTICS|索引统计|D|无|
|JOB|任务|N|无|
|OBJECT_GRANT|对象权限|DG|无|
|PACKAGE|包|SN|默认返回包头包体|
|PKG_SPEC|包头|SN|无|
|PKG_BODY|包体|SN|无|
|POLICY|策略|D|无|
|PROCEDURE|存储过程|SN|无|
|ROLE|角色|N|无|
|ROLE_GRANT|角色权限|G|无|
|SCHEMA_EXPORT|模式下的所有对象|H|模式级导出|
|SEQUENCE|序列|SN|无|
|SYNONYM|同义词|见说明|私有同义词为模式对象，公有同义词为命名对象|
|SYSTEM_GRANT|系统权限|G|无|
|TABLE|表|SN|无|
|TABLE_STATISTICS|表统计信息|D|无|
|TABLE_EXPORT|表及与其相关的元数据|H|表级导出|
|TABLESPACE|表空间|N|无|
|TRIGGER|触发器|SND|无|
|USER|用户|N|无|
|VIEW|视图|SN|无|
|TYPE|用户自定义类型|SN|无|
|MATERIALIZED_VIEW|物化视图|SN|无|
|MATERIALIZED_VIEW_LOG|物化视图日志|SN|无|
|PROFILE|PROFILE 对象|N|无|




返回值

不透明的句柄。该句柄将用于 SET_FILTER, SET_PARSE_ITEM, FETCH_XXX, and CLOSE。

错误处理

INVALID_ARGVAL：参数为 NULL，或者参数值非法。

  3. ”CLOSE”



关闭 OPEN 打开的句柄，并清理相关环境。

语法如下：

```
PROCEDURE CLOSE (
	HANDLE		IN		NUMBER
);
```

参数详解

  * handle

OPEN 函数返回的句柄。



错误处理

INVALID_ARGVAL：参数为 NULL，或者参数值非法。

  4. FETCH_DDL()



该函数返回符合 OPEN，SET_FILTER，SET_PARSE_ITEM 设定条件的对象元数据。

语法如下：

```
FUNCTION FETCH_DDL (
	HANDLE		IN		NUMBER
)RETURN KU$_DDLS;
```

参数详解

  * handle

OPEN 函数返回的句柄。



返回值

对象的元数据或者当所有对象都已返回后返回 NULL。

使用说明：

从表 ku$_ddls 中返回 ddl 语句，表 ku$_ddls 的每一行在 ddlText 列包含一条 ddl 语句；如果使用解析的话，解析出的对象属性将从 parsedItem 列返回。

错误处理

INVALID_ARGVAL：参数为 NULL,或者参数值非法。

  5. FETCH_CLOB()



该函数返回符合 OPEN，SET_FILTER 设定条件的对象元数据。

语法如下：

```
FUNCTION  FETCH_CLOB(
	HANDLE		IN		INT
)RETURN CLOB;
```

参数详解

  * handle

OPEN 函数返回的句柄。



返回值

对象的元数据或者当所有对象都已返回后返回 NULL。

使用说明：

将对象以 clob 返回。

异常：

INVALID_ARGVAL：参数为 NULL，或者参数值非法。

  6. GET_GRANTED_DDL()



该函数用于返回对象的授权语句。权限使用说明和 GET_DDL 一样，请参考 GET_DDL。

语法如下：

```
FUNCTION GET_GRANTED_DDL (
	OBJECT_TYPE		IN		VARCHAR2,
	GRANTEE			IN		VARCHAR2,
	OBJECT_COUNT	IN		NUMBER		DEFAULT 10000
)RETURN CLOB;
```

参数详解

  * object_type

对象类型。参数要与 OPEN 的对象类型参数相同, 不能为 heterogeneous 对象类型。

  * grantee

被赋予权限的对象。

  * object_count

返回对象个数的最大值。



返回值

以 ddl 返回对象的元数据

异常:

INVALID_ARGVAL：参数值为 null，或者参数值非法。

OBJECT_NOT_FOUND：指定的对象数据库中不存在。

  7. GET_DEPENDENT_DDL()



该函数用于返回依赖对象的 DDL 语句。权限使用说明和 GET_DDL 一样，请参考 GET_DDL。

语法如下：

```
FUNCTION GET_DEPENDENT_DDL (
	OBJECT_TYPE				IN		VARCHAR2,
	BASE_OBJECT_NAME		IN		VARCHAR2,
	BASE_OBJECT_SCHEMA		IN		VARCHAR2	DEFAULT NULL,
	OBJECT_COUNT			IN		NUMBER		DEFAULT 10000
)RETURN CLOB;
```

参数详解

  * object_type

对象类型。参数要与 OPEN 的对象类型参数相同, 不能为 heterogeneous 对象类型。

  * base_object_name

基对象名称。内部使用 BASE_OBJECT_NAME 过滤器过滤。

  * base_object_schema

基对象模式。内部使用 BASE_OBJECT_SCHEMA 过滤器过滤。

  * object_count

返回对象个数的最大值。



返回值

以 ddl 返回对象的元数据。

异常:

INVALID_ARGVAL：参数值为 null，或者参数值非法。

OBJECT_NOT_FOUND 指定的对象数据库中不存在。

  8. SET_FILTER()



该过程用于过滤待返回的对象。

语法如下：

```
PROCEDURE SET_FILTER (
	HANDLE		IN		NUMBER,
	NAME		IN		VARCHAR2,
	VALUE		IN		VARCHAR2
);
```

参数详解

  * handle

句柄，由 DBMS_METADATA.OPEN 输出。

  * name

过滤器的名称，详见下表。

  * value

过滤器的值，与 name 相对应，详见下表 11.2。



表11.2 SET_FILTER过程中的过滤器名称

|**对象类型**|**名称**|**数据类型**|**含义**|
|----|----|----|----|
|可命名对象|NAME|文本|通过对象名过滤|
|可命名对象|NAME_EXPR|文本表达式|通过对象名的 where 表达式过滤，满足条件的返回|
|可命名对象|EXCLUDE_NAME_EXPR|文本表达式|通过对象名的 where 表达式过滤，不满足条件的返回|
|模式对象|SCHEMA|文本|通过模式名过滤，如果是公有同义词则指定其模式为 PUBLIC|
|模式对象|SCHEMA_EXPR|文本表达式|通过对象名的 where 表达式过滤，默认为当前模式|
|表、索引、表级导出|TS|文本|通过表空间名过滤|
|表、索引、表级导出|TS_EXPR|文本表达式|通过表空间名的 where 表达式过滤|
|依赖对象|BASE_OBJECT_NAME|文本|通过基对象名过滤。|
|依赖对象|BASE_OBJECT_SCHEMA|文本|通过基对象模式过滤。如果使用 BASE_OBJECT_NAME 过滤，默认是当前模式。|
|依赖对象|BASE_OBJECT_NAME_EXPR|文本表达式|通过基对象名的 where 表达式过滤，满足条件的返回。不适用于模式和库级触发器。|
|依赖对象|EXCLUDE_BASE_OBJECT_NAME_EXPR|文本表达式|通过基对象名的 where 表达式过滤，不满足条件的返回。不适用于模式和库级触发器。|
|依赖对象|BASE_OBJECT_SCHEMA_EXPR|文本表达式|通过基对象模式的 where 表达式过滤。|
|依赖对象|BASE_OBJECT_TYPE|文本|通过基对象的对象类型过滤。|
|依赖对象|BASE_OBJECT_TYPE_EXPR|文本表达式|通过基对象的对象类型表达式过滤。|
|依赖对象|BASE_OBJECT_TS|文本|通过基对象的表空间过滤。|
|依赖对象|BASE_OBJECT_TS_EXPR|文本表达式|通过基对象的表空间 where 表达式过滤。|
|被授权对象|GRANTEE|文本|被授权的用户或角色。 AUDIT_obj 只能过滤跟 grantee 有关的其他 grant 过滤器不适用。|
|被授权对象|ROLE_NAME|文本|通过权限或角色的名称过滤|
|被授权对象|ROLE_NAME_EXPR|文本表达式|通过权限或角色的 where 表达式名称过滤|
|被授权对象|GRANTEE_EXPR|文本表达式|通过被授权者名称的 where 表达式过滤，符合条件的返回|
|被授权对象|EXCLUDE_GRANTEE_EXPR|文本表达式|通过被授权者名称的 where 表达式过滤，不符合条件的返回|
|对象权限(OBJECT_GRANT )|GRANTOR|文本|通过授权者过滤，对于系统用户（sysdba sysauditor syssso）赋予的权限在权限表里面 ID 记录的都是-1，所以设定了一个 grantor 叫"$SUPER",当 filter 中设定 grantor 为$SUPER 时，系统用户赋予的权限将全部返回而不区分具体是哪个系统用户|
|所有对象|CUSTOM_FILTER|文本|用户自定义过滤，要求填写完整的 where 子句，如“NAME = ‘T1’”|
|模式级导出|SCHEMA|文本|通过模式名过滤|
|模式级导出|SCHEMA_EXPR|文本表达式|通过模式名 where 表达式过滤，有以下两种情况：1.获取模式对象； 2.获取依赖于此模式的对象。默认情况下，将选中当前用户的对象。|
|表级导出|SCHEMA|文本|通过模式名过滤|
|表级导出|SCHEMA_EXPR|文本表达式|通过模式名 where 表达式过滤，有以下两种情况：1.获取模式下的表；2.获取依赖于表的对象。默认情况下，将选中当前用户的对象。|
|表级导出|NAME|文本|通过表名过滤出表及依赖表的对象|
|表级导出|NAME_EXPR|文本表达式|通过表名的 where 表达式过滤出表及依赖表的对象|


> **注意**
>
> **约束的BASE_OBJECT_TYPE
> 是UTAB，注释的BASE_OBJECT_TYPE为TABLE或者VIEW。索引、OBJECT_GRANT的BASE_OBJECT_TYPE为UTAB，TABLE_STATISTICS，INDEX_STATISTICS，COL_STATISTICS的BASE_OBJECT_TYPE分别对应为T、I、C。**



错误处理

INVALID_ARGVAL：参数为 NULL，或者参数值非法。

INVALID_OPERATION：非法操作。在调用 FETCH_xxx 之后，不允许再调用 SET_FILTER 。

INCONSISTENT_ARGS：参数不一致，包括如下状况：

  * 过滤器名字与 DBMS_METADATA.OPEN 中指定的类型不匹配。
  * 过滤器的名字与 object_type_path 不匹配。
  * object_type_path 与 DBMS_METADATA.OPEN 中指定的类型不匹配。
  * 输入的过滤器的 value 与期待的数据类型不匹配。



  9. SET_PARSE_ITEM()



该过程用于解析对象的属性。

语法如下：

```
PROCEDURE SET_PARSE_ITEM (
	HANDLE			IN		NUMBER,
	NAME			IN		VARCHAR2,
	OBJECT_TYPE		IN		VARCHAR2	DEFAULT NULL
);
```

参数详解

  * handle

OPEN 函数返回的句柄。

  * name

被解析对象属性的名称，具体的可被解析的对象属性名称见下表 11.3。

  * object_type

应用于不同对象的集合，不设置时解析所有的对象。



表11.3 SET_PARSE_ITEM可解析的对象属性

|**对象类型**|**属性名称**|**含义**|
|----|----|----|
|所有对象|VERB|如果调用 FETCH_DDL，表 ku$_ddls 每一行 ddltext 列中的动词将被返回. 如果 ddlText 是 SQL DDL 语句, SQL 中的动词 (如 CREATE, GRANT, AUDIT) 被返回。如果 ddlText 是过程(如 DBMS_METADATA.FETCH_DDL()) 那么 package.procedure-name 被返回|
|所有对象|OBJECT_TYPE|如果调用 FETCH_DDL， ddlText 是 SQL DDL 语句且包含动词 CREATE 或者 ALTER，DDL 语句中的对象类型将被返回 (如 TABLE，PKG_BODY 等等)。否则“表 OPEN 函数可返回的对象类型名称”中的对象类型被返回|
|模式对象|SCHEMA|返回对象的模式，如果不是模式对象则没有返回值|
|命名的对象|NAME|返回对象的名称，如果不是命名的对象则没有返回值|
|表、表数据、索引|TABLESPACE|返回对象表空间名称，如果是分区表则返回默认的表空间。对于 TABLE_DATA 对象, 总是返回行所在的表空间|
|触发器|ENABLE|返回触发器是禁用还是启用状态|
|依赖对象|BASE_OBJECT_NAME|返回基对象名称，如果不是依赖对象则没有返回值|
|依赖对象|BASE_OBJECT_SCHEMA|返回基对象所属的模式，如果不是依赖对象则没有返回值|
|依赖对象|BASE_OBJECT_TYPE|返回基对象所属类型，如果不是依赖对象则没有返回值|


> **注意**
>
>
> **对象类型是可解析的对象属性所适用的范围；BASE_OBJECT_NAME，BASE_OBJECT_SCHEMA，BASE_OBJECT_TYPE这三个属性只解析约束、索引、全文索引、表级/视图级触发器。**



使用说明：

FETCH_XXX 函数可以返回对象的 ddl 语句，使用 SET_PARSE_ITEM 则可以返回对象的各个属性，可以多次调用 SET_PARSE_ITEM 来解析和返回多个解析项，返回的解析项存于表 ku$_parsed_items 中。

错误处理

INVALID_ARGVAL：参数为 NULL,或者参数值非法。

INVALID_OPERATION：非法操作。SET_PARSE_ITEM 只能用于 FETCH_xxx 函数之前。第一次调用 FETCH_xxx 后，不允许再调用 SET_PARSE_ITEM。

  10. GET_QUERY()



该函数用于获取查询文本。

语法如下：

```
FUNCTION GET_QUERY (
	HANDLE		IN		NUMBER
)RETURN VARCHAR2;
```

参数详解

  * handle

OPEN 函数返回的句柄。



返回值

将被用于 FETCH_XXX 函数的查询文本。

错误处理

INVALID_ARGVAL：参数值为 null，或非法。

  11. SET_COUNT()



该过程用于指定一次 fetch_xxx 函数调用所返回对象个数的最大值。

语法如下：

```
PROCEDURE SET_COUNT (
	HANDLE		IN		NUMBER,
	VALUE		IN		NUMBER
);
```

参数详解

  * handle

OPEN 函数返回的句柄。

  * value

设定的最大值。



错误处理

INVALID_ARGVAL：参数值为 null 或非法；

INVALID_OPERATION：非法操作。SET_COUNT 只能用于第一次调用 FETCH_xxx 函数之前，第一次调用 FETCH_xxx 后，不允许再调用 SET_COUNT。

  12. SET_TRANSFORM_PARAM()



该过程用于格式化过滤会话级约束或存储选项的定义。

语法如下：

```
PROCEDURE SET_TRANSFORM_PARAM (
	TRANSFORM_HANDLE	IN		INT,
	NAME				IN		VARCHAR,
	VALUE				IN		BOOLEAN		DEFAULT TRUE,
	OBJECT_TYPE			IN		VARCHAR		DEFAULT NULL
);
```

参数详解

  * transform_handle

会话句柄，目前仅支持 DBMS_METADATA.SESSION_TRANSFORM，表示定义的格式化应用于当前会话。

  * name

表示待过滤的约束或存储选项的名称，可取值 CONSTRAINTS 或 STORAGE：CONSTRAINTS 表示约束；STORAGE 表示存储选项。与参数
VALUE 配合使用。

  * value

表示是否过滤约束或存储选项的定义，可取值 TRUE 或 FALSE：TRUE 表示过滤；FALSE 表示不过滤。缺省为 TRUE。

  * object_type

应用本格式化定义的对象类型，由于 transform_handle 只支持会话级，因此该参数暂无实际作用。



错误处理

INVALID_ARGVAL：参数值为 null 或非法。

13.ADD_TRANSFORM ()

该过程用在 fetch_clob 前，表示 feTCH_CLOB 的内容以 DDL 形式展示，即返回对象的 ddl 格式的语句。不调用则默认情况下返回 XML 格式化定义。 （因为要支持这个函数， metadata 包中元数据改造成了 xml 格式。ku$_ddl.DDLTEXT 为元数据）

语法如下：

```
FUNCTION ADD_TRANSFORM (
	HANDLE     IN NUMBER,
	NAME      IN VARCHAR2,
	ENCODING    IN VARCHAR2 DEFAULT NULL,
	OBJECT_TYPE   IN VARCHAR2 DEFAULT NULL)
RETURN NUMBER;
```

参数详解

  * handle



OPEN 函数返回的句柄。

  * NAME



参数值有 DDL 和 MODIFY。表示转换类型，目前只支持 DDL，其他字符则不起作用。

  * ENCODING

编码。忽略，暂不支持设置。

  * OBJECT_TYPE

应用本格式化定义的对象类型。由于 transform_handle 只支持会话级，因此该参数暂无实际作用。

  * 返回值 TRANSFORM_HANDLE



会话句柄，表示定义的格式化应用于当前会话。目前暂定从 10000 开始计数，无实际作用。因为此函数可直接设置全局的转换类型。但该句柄可传递给 SET_TRANSFORM_PARAM。NAME 为 MODIFY 时，句柄返回值可以用于函数 SET_REMAP_PARAM，暂不支持。

错误处理

无

## 10.3 错误处理

错误、异常情况释义：

INVALID_ARGVAL：非法的参数数据。

OBJECT_NOT_FOUND：未找到对象。

INVALID_OPERATION：无效的过程/函数调用顺序。

INCONSISTENT_ARGS：对象类型与过滤器不匹配。

## 10.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_METADATA');
```

例 1 获取表和存储过程的 DDL 语句。

```
//建表
CREATE TABLE "SYSDBA"."T1"
(
"C1" CHAR(10) NOT NULL,
"C2" CHAR(10),
CONSTRAINT "PK" PRIMARY KEY("C1")) STORAGE(ON "MAIN", CLUSTERBTR);
//获取对象的ddl语句
SELECT DBMS_METADATA.GET_DDL('TABLE','T1','SYSDBA');
```

执行结果如下：

```
CREATE TABLE "SYSDBA"."T1"
(
"C1" CHAR(10) NOT NULL,
"C2" CHAR(10),
CONSTRAINT "PK" NOT CLUSTER PRIMARY KEY("C1")) STORAGE(ON "MAIN", CLUSTERBTR) ;
```

例 2 使用 DBMS_METADATA 程序化接口获取元数据。

```
//建表
CREATE TABLE "RESOURCES"."TIMECARDS"
(  "EMPLOYEE_ID" INT,
   "WEEK" NUMBER(2,0),
   "JOB_ID" VARCHAR2(10),
   "HOURS_WORKED" NUMBER(4,2),  
   FOREIGN KEY ("EMPLOYEE_ID")
   REFERENCES "RESOURCES"."EMPLOYEE" ("EMPLOYEEID")
) STORAGE(ON "MAIN", CLUSTERBTR) ;
//建函数
CREATE OR REPLACE FUNCTION get_table_md RETURN CLOB IS
//定义局部变量
h NUMBER; //handle returned by OPEN
th NUMBER; //handle returned by ADD_TRANSFORM
doc CLOB;
BEGIN
//指定对象类型
h := DBMS_METADATA."OPEN" ('TABLE');

//使用过滤器返回特定的对象
DBMS_METADATA.SET_FILTER(h,'SCHEMA','RESOURCES');
DBMS_METADATA.SET_FILTER(h,'NAME','TIMECARDS');

//获取DDL格式的语句
DBMS_METADATA.ADD_TRANSFORM(H,'DDL');

//获取对象
doc := DBMS_METADATA.FETCH_CLOB(h);

//释放资源
DBMS_METADATA."CLOSE" (h);
RETURN doc;
END;
/

SELECT get_table_md;
```

查询结果如下：

```
CREATE TABLE "RESOURCES"."TIMECARDS"
(
"EMPLOYEE_ID" INT,
"WEEK" NUMBER(2,0),
"JOB_ID" VARCHAR2(10),
"HOURS_WORKED" NUMBER(4,2),
FOREIGN KEY("EMPLOYEE_ID") REFERENCES "RESOURCES"."EMPLOYEE"("EMPLOYEEID")
) STORAGE(ON "MAIN", CLUSTERBTR) ;
```

例 3 使用 DBMS_METADATA 浏览接口获取元数据。

```
SELECT DBMS_METADATA.GET_DDL('TABLE','TIMECARDS','RESOURCES');
```

查询结果同例 2。

例 4 获取多个对象。

```
DROP TABLE my_metadata;
CREATE TABLE my_metadata (md clob);
CREATE OR REPLACE PROCEDURE get_tables_md IS
//定义局部变量
h NUMBER;  //handle returned by 'OPEN'
th NUMBER; //handle returned by 'ADD_TRANSFORM'
doc CLOB;  //metadata is returned in a CLOB
BEGIN
//指定对象类型
h := DBMS_METADATA."OPEN" ('TABLE');

// 使用过滤器指定模式
DBMS_METADATA.SET_FILTER(h,'SCHEMA','sysdba') ;

//获取DDL格式的语句
DBMS_METADATA.ADD_TRANSFORM(H,'DDL');

//获取对象
LOOP
	doc := DBMS_METADATA.FETCH_CLOB(h);
   //当没有对象可获取时, FETCH_CLOB 返回 NULL
	EXIT WHEN doc IS NULL;
   //将元数据存入表中
	INSERT INTO my_metadata(md) VALUES (doc);
	COMMIT;
END LOOP;
//释放资源
DBMS_METADATA."CLOSE" (h);
END;
/

CALL get_tables_md;
SELECT * FROM my_metadata;
```

例 5 使用 parse items 解析特定的元数据属性。

```
DROP TABLE my_metadata;
CREATE TABLE my_metadata (
   object_type VARCHAR2(30),
   name VARCHAR2(30),
   md CLOB);
CREATE OR REPLACE PROCEDURE get_tables_and_indexes IS
//定义局部变量
h1 NUMBER; // 打开表返回的句柄
doc DBMS_METADATA.ku$_ddls;  //ku$_ddls，返回的元数据
ddl CLOB;  //对象的ddl语句
pi DBMS_METADATA.ku$_parsed_items;  //包含在ku$_ddL中的对象返回的解析项
objname VARCHAR2(30);   //解析对象的名称
BEGIN
//指定对象类型: TABLE.
h1 := DBMS_METADATA."OPEN" ('TABLE');

//获取DDL格式语句
DBMS_METADATA.ADD_TRANSFORM(H1,'DDL');

//将表名作为解析项返回
DBMS_METADATA.SET_PARSE_ITEM(h1,'NAME');

//设置循环: 获取 TABLE 对象
LOOP
	doc := dbms_metadata.fetch_ddl(h1);

//当没有对象可获取时，FETCH_CLOB 返回 NULL
	EXIT WHEN doc IS NULL;

//循环ku$_ddls表中的行
	FOR i IN doc.FIRST..doc.LAST LOOP
  		ddl := doc(i).ddlText;
		pi := doc(i).parsedItem;
		//循环返回的解析项
		IF pi IS NOT NULL AND pi.COUNT > 0 THEN
        	FOR j IN pi.FIRST..pi.LAST LOOP
            	IF pi(j).item='NAME' THEN
                	objname := pi(j).value;
                END IF;
        	END LOOP;
         END IF;
		//将该对象的信息插入表 my_metadata中
    	 INSERT INTO my_metadata(object_type, name, md)
         	VALUES ('TABLE',objname,ddl);
        	COMMIT;
    	 END LOOP;
	END LOOP;
	DBMS_METADATA."CLOSE" (h1);
END;
/
CALL get_tables_and_indexes;
SELECT * FROM my_metadata;
```

例 6 使用 SET_TRANSFORM_PARAM 格式化过滤约束。

```
//建表
DROP TABLE "SYSDBA"."T1";
CREATE TABLE "SYSDBA"."T1"
(
"C1" CHAR(10) NOT NULL,
"C2" CHAR(10),
CONSTRAINT "PK" PRIMARY KEY("C1")) STORAGE(ON "MAIN", CLUSTERBTR);
//格式化过滤约束
DBMS_METADATA.SET_TRANSFORM_PARAM(DBMS_METADATA.SESSION_TRANSFORM, 'CONSTRAINTS', FALSE, '');
//查看表定义
SELECT DBMS_METADATA.GET_DDL('TABLE','T1','SYSDBA');
```

执行结果如下：

```
CREATE TABLE "SYSDBA"."T1"
(
"C1" CHAR(10),
"C2" CHAR(10)) STORAGE(ON "MAIN", CLUSTERBTR);
```

例 7 使用 ADD_TRANSFORM 返回对象的 ddl 格式的语句。

```
CREATE TABLE TRANS1(C1 INT);
CREATE OR REPLACE FUNCTION get_table_md RETURN CLOB IS
h NUMBER; //handle returned by OPEN
th NUMBER; //handle returned by ADD_TRANSFORM
doc CLOB;
BEGIN
h := DBMS_METADATA."OPEN" ('TABLE');
PRINT 'set_filter|'||h;
//使用过滤器返回特定的对象
DBMS_METADATA.SET_FILTER(h,'SCHEMA','SYSDBA');
DBMS_METADATA.SET_FILTER(h,'NAME','TRANS1');
//获取对象
th:=DBMS_METADATA.ADD_TRANSFORM(h,'DDL1');
DBMS_METADATA.SET_TRANSFORM_PARAM(th,'STORAGE',FALSE);
doc := DBMS_METADATA.FETCH_CLOB(h);
DBMS_METADATA."CLOSE" (h);
RETURN doc;
END;
/
select get_table_md;
```

执行结果：

```
//无STORAGE标签
<?XML VERSION="1.0"?><ROWSET><ROW><TABLE_T><SCHEMANAME>"SYSDBA"</SCHEMANAME><NAME>"TRANS1"</NAME><COL_LIST><COL_LIST_ITEM><COL_NAME>"C1"</COL_NAME><COLTYPE>INT</COLTYPE></COL_LIST_ITEM></COL_LIST></TABLE_T></ROW></ROWSET>
```

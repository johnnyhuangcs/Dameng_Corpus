

# DBMS_SESSION 包

DBMS_SESSION 包用来访问或者设置会话信息。

## 16.1 数据类型

DBMS_SESSION 包中涉及到类型。如下统一说明。

APPCTXRECTYP 类型是 DBMS_SESSION 专有类型。用户不能引用和改变该记录的内容。APPCTXRECTYP 记录类型定义如下：

```
TYPE APPCTXRECTYP IS RECORD( 
	NAMESPACE 		VARCHAR2(30), 
	ATTRIBUTE   	VARCHAR2(30),
	VALUE     		VARCHAR2(256)
);
```

参数详解

  * NAMESPACE



上下文的名称。

  * ATTRIBUTE



上下文的属性名称。

  * VALUE



上下文的属性值。

例 APPCTXTABTYP 为 APPCTXRECTYP 类型的索引表：

```
TYPE APPCTXTABTYP IS TABLE OF APPCTXRECTYP INDEX BY BINARY_INTEGER; 
```

## 16.2 相关方法

  1. CLEAR_ALL_CONTEXT 过程



清理当前会话的所有上下文。该过程需通过 namespace 关联的 package 调用。

语法如下：

```
DBMS_SESSION.CLEAR_ALL_CONTEXT(
	NAMESPACE	VARCHAR(30)
);
```

参数详解

  * namespace

不区分大小写。



  2. CLEAR_CONTEXT 过程



清理 namespace 中的 attribute 值。该过程需通过上下文 namespace 关联的 package 调用。

语法如下：

```
DBMS_SESSION.CLEAR_CONTEXT
	NAMESPACE           VARCHAR(30),
	CLIENT_IDENTIFIER	VARCHAR(30)
	ATTRIBUTE			VARCHAR(30)
);
```

参数详解

  * namespace 和 attribute

不区分大小写。

  * client_identifier

用于全局访问的 client 标识，不起作用，如果指定，则忽略。



  3. IS_SESSION_ALIVE 函数



会话是否为活动会话，如果是，则返回 TRUE，否则返回 FALSE。

语法如下：

```
DBMS_SESSION.IS_SESSION_ALIVE (
   UNIQUEID		BIGINT
)RETURN VARCHAR;
```

参数详解

  * uniqueid

为会话的唯一标识。



  4. LIST_CONTEXT 过程



返回当前会话所有上下文属性和值。

语法如下：

```
DBMS_SESSION.LIST_CONTEXT( 
	LIST 	OUT 		APPCTXTABTYP, 
	SIZE 	OUT 		NUMBER
);
```

参数详解

  * list

输出参数，类型为索引表。

  * size

输出参数，该索引表中元素的总数。



  5. RESET_PACKAGE 过程



清除当前会话上的所有 package 对象，即可以释放所有 package 对象占用的内存空间。

语法如下：

```
DBMS_SESSION.RESET_PACKAGE;
```

参数详解

无。

  6. SET_CONTEXT 过程



设置上下文 namespace 的属性和值。

语法如下：

```
DBMS_SESSION.SET_CONTEXT (
	NAMESPACE  		VARCHAR(30),
	ATTRIBUTE   	VARCHAR(30),
	VALUE      		VARCHAR(4000),
	USERNAME   		VARCHAR(128),
	CLIENT_ID   	VARCHAR(64)
);
```

参数详解

  * namespace 和 attribute 不区分大小写。
  * username 和 client_id 参数不起作用，如果指定，则忽略。



  7. UNIQUE_SESSION_ID 函数



返回唯一会话 id。

语法如下：

```
DBMS_SESSION.UNIQUE_SESSION_ID
RETURN VARCHAR;
```

  8. CLOSE_DATABASE_LINK 过程



关闭一个指定的打开的外部链接，若该外部链接正在使用中（可通过 V$DBLINK 进行查询），则报错。

语法如下：

```
DBMS_SESSION.CLOSE_DATABASE_LINK (
	DBLINK		VARCHAR(128)
);
```

参数详解

  * dblink

为指定的要关闭的外部链接名。



  9. SET_IDENTIFIER 过程



用于设置当前会话 ID。设置之后的会话 id 可通过 V$SESSIONS 中 CLIENT_IDENTIFIER 字段查看。

语法如下：

DBMS_SESSION.SET_IDENTIFIER(

SESSIONID    VARCHAR(64)

);

参数详解

  * SESSIONID



会话 ID。

## 16.3 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_SESSION');
SET SERVEROUTPUT ON;  //PRINT需要设置这条语句，才能打印出消息
```

初始化环境：

```
//创建上下文关联的包
create or replace package test_pk as
	procedure set_context(ts_name varchar, key varchar, value varchar);
	procedure clear_context(ts_name varchar, clientid varchar, key varchar);
	procedure clear_all_context(ts_name varchar);
end test_pk;
/

create or replace package body test_pk as
	procedure set_context(ts_name varchar, key varchar, value varchar) as
	begin
        dbms_session.set_context(ts_name, key, value);
	end;

	procedure clear_context(ts_name varchar, clientid varchar, key varchar) 	as
	begin
        dbms_session.clear_context(ts_name, clientid, key);
	end;

	procedure clear_all_context(ts_name varchar) as
	begin
        dbms_session.clear_all_context(ts_name);
	end;
end test_pk;
/

//创建上下文C_TEST
create or replace context C_TEST using test_pk;
```

例 1 设置上下文 c_test 的属性和值。

```
call test_pk.set_context('c_test', 'k1', 'v1');
call test_pk.set_context('c_test', 'k2', 'v2');
```

例 2 查询上下文 C_TEST 的属性 K1 的值。

```
select sys_context('c_test', 'k1') from dual;
```

结果如下：

```
v1
```

例 3 清除上下文 C_TEST 属性 K1 的值。

```
call test_pk.clear_context('c_test', null, 'k1');
```

再次查询名字空间 C_TEST 的属性 K1 的值。

```
select sys_context('c_test', 'k1') from dual;
```

查询结果为空。

例 4 当前会话是否为活动会话

```
select dbms_session.IS_SESSION_ALIVE(dbms_session.unique_session_id) from dual;
```

结果如下：

```
TRUE
```

例 5 打印当前会话所有的上下文属性和值

```
declare
res DBMS_SESSION.AppCtxTabTyp;
num int;
begin
	DBMS_SESSION.LIST_CONTEXT (res, num);
	for i in 0..num-1 loop
	  print res(i).namespace;
	  print res(i).ATTRIBUTE;
	  print res(i).VALUE;
	end loop;
end;
/
```

结果如下：

```
C_TEST
k1
NULL
C_TEST
k2
v2
```

例 6 删除上下文 C_TEST

```
drop context c_test;
```

例 7 清理当前会话的 package 对象

```
create or replace package test_p is
cnt number := 0;
procedure print_status;
end test_p;
/

create or replace package body test_p is
procedure print_status is
begin
	print 'test_p.cnt = ' || cnt;
end;
end;
/
```

初始化并打印值：

```
begin
test_p.cnt := 5236;
test_p.print_status;
end;
/
```

输出结果如下：

```
test_p.cnt = 5236
```

打印 CNT 值：

```
begin
	test_p.print_status;
end;
/
```

输出结果如下：

```
test_p.cnt = 5236
```

清理包对象：

```
CALL DBMS_SESSION.RESET_PACKAGE;
```

再打印 CNT 值：

```
begin
	test_p.print_status;
end;
/
```

输出结果如下：

```
test_p.cnt = 0
```

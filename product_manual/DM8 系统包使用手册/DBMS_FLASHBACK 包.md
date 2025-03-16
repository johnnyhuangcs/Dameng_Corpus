

# DBMS_FLASHBACK 包

使用 DBMS_FLASHBACK 包，可以查询在指定时间或 LSN 时刻的数据库版本。

## 50.1 相关方法

  1. DISABLE



关闭会话闪回模式。

语法如下：

```
PROCEDURE DISABLE;
```

  2. ENABLE_AT_SYSTEM_CHANGE_NUMBER



以指定 LSN 打开会话闪回模式。

语法如下：

```
PROCEDURE ENABLE_AT_SYSTEM_CHANGE_NUMBER(QUERY_SCN IN BIGINT);
```

参数详解

  * QUERY_SCN 输入参数，指定闪回时刻 LSN。



  3. ENABLE_AT_TIME



以指定时间打开会话闪回模式。

语法如下：

```
PROCEDURE ENABLE_AT_TIME(QUERY_TIME IN TIMESTAMP);
```

参数详解

  * QUERY_TIME 输入参数, 指定闪回时间。



  4. GET_SYSTEM_CHANGE_NUMBER



获取系统当前 LSN 值。

语法如下：

```
FUNCTION GET_SYSTEM_CHANGE_NUMBER RETURN BIGINT;
```

返回值

返回系统当前 LSN

## 50.2 使用说明

  1. 使用 DBMS_FLASHBACK 包，需要打开闪回 INI 参数 ENABLE_FLASHBACK。
  2. 打开会话闪回模式下不能执行 DML 和 DDL 操作，需要先调用包中方法 DISABLE 关闭闪回模式才能正常执行。
  3. 会话闪回模式下可以执行带闪回查询子句的语句，也可以执行不带闪回查询子句的语句；如果指定闪回查询子句中的 LSN/TIME 和会话闪回模式中的 LSN/TIME 相同，则查询结果一致，若不同则以指定的闪回查询子句为主。
  4. DMDPC 环境下仅支持指定时间打开会话闪回模式，不支持指定 LSN。
  5. 在数据守护环境下，备库不支持使用 DBMS_FLASHBACK 包。
  6. DM MPP 环境下不支持 DBMS_FLASHBACK 包。



## 50.3 举例说明

使用包内的过程和函数之前，如果还未创建过系统包。请先调用系统过程创建系统包，默认已经创建。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_FLASHBACK');
```

以指定 LSN 为例使用闪回包进行查询。

```
drop table if exists t1;
create table t1(c1 int, c2 int);
insert into t1 values(1,1);
commit;

select DBMS_FLASHBACK.GET_SYSTEM_CHANGE_NUMBER;  //假设查询结果为117846

insert into t1 values(2,2);
commit;

DBMS_FLASHBACK.ENABLE_AT_SYSTEM_CHANGE_NUMBER(117846);
select * from t1;

DBMS_FLASHBACK.DISABLE;
```

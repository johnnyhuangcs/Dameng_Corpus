

# DBMS_XPLAN 包

为了更好展示历史执行计划，达梦提供了 DBMS_XPLAN 系统包。DBMS_XPLAN 包用于展示历史执行计划，以及对持久化的历史执行计划进行清除。

仅当历史执行计划持久化功能打开（USE_PLN_POOL!=0，ENABLE_MONITOR_PLNHIST=1）时，使用 DBMS_XPLAN 包才有意义。不支持 DMDPC 环境下记录历史执行计划。

## 53.1 相关方法

DBMS_XPLAN 包目前共提供三种方法。

  1. DISPLAY_PLANHIST



展示指定原始 SQL 语句的历史执行计划信息。历史执行计划信息来源于系统表 SYSPLANHIST。

语法如下：

```
DBMS_XPLAN.DISPLAY_PLANHIST (

    SQL_TEXT           IN VARCHAR(8818));
```

参数详解

  * SQL_TEXT：具体的 sql 语句。



  2. DISPLAY_PLANHIST



展示指定哈希值的 SQL 语句的历史执行计划信息。历史执行计划信息来源于系统表 SYSPLANHIST。

语法如下：

```
DBMS_XPLAN.DISPLAY_PLANHIST (

    HASH_VALUE        IN INTEGER);
```

参数详解

  * HASH_VALUE：SQL 语句的哈希值。



  3. CLEAR_PLANHIST



清理系统表 SYSPLANHIST 中指定时间范围内的历史执行计划记录。

语法如下：

```
DBMS_XPLAN.CLEAR_PLANHIST(

	START_TIME         IN DATETIME,

    END_TIME           IN DATETIME);
```

参数详解

  * START_TIME：指定时间范围起始值。指定为 NULL 时, 表示不限制起始时间。
  * END_TIME：指定时间范围终止值。指定为 NULL 时, 表示不限制终止时间。终止时间不应该早于起始时间。



## 53.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包。请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_XPLAN');
```

执行前确保计划复用和历史计划持久化功能都开启，即 USE_PLN_POOL!=0，ENABLE_MONITOR_PLNHIST=1。

  1. 建表



```
CREATE TABLE T1(C1 INT,C2 INT);

INSERT INTO T1 SELECT LEVEL,LEVEL FROM DUAL CONNECT BY LEVEL <= 100;

SELECT C1 FROM T1;

CREATE INDEX IDX1 ON T1(C1);

SELECT C1 FROM T1;

SP_FLUSH_HIST_PLAN; //将历史执行计划信息刷盘到系统表SYSPLANHIST中
```

  2. 展示 SQL 语句的历史执行计划信息



```
DBMS_XPLAN.DISPLAY_PLANHIST('SELECT C1 FROM T1;');
```

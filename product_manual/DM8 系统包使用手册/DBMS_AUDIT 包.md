

# DBMS_AUDIT 包

用户可以使用 DBMS_AUDIT 包获取指定审计日志下的审计记录，并通过获取的信息进行更深入的分析。

## 52.1 相关方法

  1. ADD_AUDFILE



在审计日志列表中增加日志文件，该函数只影响当前会话的 AUD。

语法如下：

```
PROCEDURE  ADD_AUDFILE(
  LogFileName  IN VARCHAR,
);
```

参数详解

  * LogFileName

增加的文件的全路径。



  2. REMOVE_AUDFILE



从日志列表中移除某个日志文件，此处的日志列表为 AUD 预加载的审计日志文件列表，可通过 V$DBMSAUDIT_FILE 查看，该函数只影响当前会话的 AUD。

语法如下：

```
PROCEDURE  REMOVE_AUDFILE(
  LogFileName   IN VARCHAR
);
```

参数详解

  * LogFileName

待移除的日志的文件全路径。



  3. START_AUD



用于打开某个会话上的 AUD，且开启 AUD 后会加载审计日志列表 V$DBMSAUDIT_FILE 中的文件，若审计列表为空，会加载所有审计文件。使用该函数后，不能再对审计日志列表中的文件进行进行增加或删除。

语法 1：

根据指定的模式和条件来开始某个会话上的 AUD，一个会话上仅能开始一个 AUD。

具体语法如下：

```
PROCEDURE  START_AUD( 
   startTime    IN DATETIME default to_date('1988/1/1', 'YYYY-MM-DD'),
   endTime     IN DATETIME default to_date('2110/12/31', 'YYYY-MM-DD'),
   userName     IN VARCHAR default '',
   ip        IN VARCHAR default '',
   succFlag    IN INT default 0
);
```

参数详解

  * startTime

分析时或者加载时的过滤条件，审计记录起始时间，默认为 1988/1/1。

  * endTime

分析时或者加载时的过滤条件，审计记录结束时间，默认为 2110/12/31。

  * userName

分析时或者加载时的过滤条件，当前审计记录的用户名，默认为空。

  * ip

分析时或者加载时的过滤条件，当前审计记录的 IP 地址，默认为空。

  * succFlag

分析时或者加载时的过滤条件，当前审计记录执行后是否成功，默认为 0。0 表示无限制，1 表示只加载成功审计记录，2 表示只加载失败审计记录。只能为这三个值。



语法 2：

开始某个会话上的 AUD，一个会话上仅能 START 一个 AUD。使用该方法将不对审计记录进行筛选。

具体语法如下：

```
PROCEDURE  START_AUD( );
```

  4. END_AUD



结束 AUD。使用该函数后，会清空审计列表中的信息。

语法如下：

```
PROCEDURE  END_AUD();
```

## 52.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包。请先调用系统过程创建系统包。

SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_AUDIT');

以下示例展示日志分析工具使用流程。由于只有审计用户才有审计视图的查询权限，所以只有审计用户执行以下操作才能查询到正确视图。

第 1 步 打开审计开关

(1)通过 disql 设置打开审计开关，审计开关打开设置可查阅 DM 安全管理，如下：

```
SP_SET_ENABLE_AUDIT (1);
```

第 2 步 添加审计日志文件(最多添加 64 个文件)

(1) 查询有哪些审计日志（可查看《DM8 系统管理员手册》的附录二第 19 小节中对于 V$AUDIT_FILE 的说明）。

```
SELECT filepath,begin_time,dbname FROM V$AUDIT_file; 
```

查询结果如下：

```
d:\DMdata\arch\AUDIT_TEST_01AC9E1D46814CD482E58154AF1D663F_2023-4-14-9-28-38.log 2023-04-30 15:51:06.000000 DAMENG
```

(2) 添加一个或多个需要分析的审计日志文件

```
dbms_AUDIT.add_AUDfile('d:\DMdata\arch\AUDIT_TEST_01AC9E1D46814CD482E58154AF1D663F_2023-4-14-9-28-38.log')
```

如要查看通过 ADD_AUDFILE 添加的审计日志文件，可以通过动态视图 V$DBMSAUDIT_FILE 进行查询。

```
SELECT filepath,begin_time,dbname FROM V$DBMSAUDIT_FILE;
```

查询结果如下：

```
d:\DMdata\arch\AUDIT_TEST_01AC9E1D46814CD482E58154AF1D663F_2023-4-14-9-28-38.log 2023-04-30 15:51:06.000000 DAMENG
```

第 3 步 启动审计日志文件分析

```
dbms_AUDIT.start_AUD();
```

在启动审计日志文件的同时，也可以添加相应的过滤条件提前过滤审计日志的内容。

```
DBMS_AUDIT.START_AUD(SUCCFLAG=>1 , STARTTIME=>TO_DATE('2023-03-04 17:36:00','YYYY-MM-DD HH24:MI:SS') , ENDTIME=>TO_DATE('2023-06-04 17:36:02','YYYY-MM-DD HH24:MI:SS'));
```

如要查看审计日志文件的分析结果，可以通过动态视图 V$AUDITRECORDS 进行查询。

```
select * from V$AUDITRECORDS;
```

也可以在查询的同时增加起始、结束时间、用户、IP、对象、成功、失败等条件过滤。

```
select * from V$AUDITRECORDS where SUCC_FLAG = 'Y';
```

第 4 步 终止审计日志文件分析

```
dbms_AUDIT.end_AUD();
```

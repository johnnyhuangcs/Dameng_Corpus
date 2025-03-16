

# DBMS_LOGMNR 包

用户可以使用 DBMS_LOGMNR 包对归档日志进行挖掘，重构出 DDL 和 DML 等操作，并通过获取的信息进行更深入的分析。

目前 DBMS_LOGMNR 只支持对归档日志进行分析，配置归档后，还需要将 dm.ini 中的 RLOG_APPEND_LOGIC 选项置为 1、2、3 或 4。

DM MPP 环境下不支持 DBMS_LOGMNR 包。DMDPC 使用 DBMS_LOGMNR 时， DBMS_LOGMNR.ADD_LOGFILE 只能添加同一个节点的多个日志同时进行分析，不支持同时分析不同节点的日志。

## 9.1 相关方法

  1. ADD_LOGFILE



增加日志文件。

语法如下：

```
PROCEDURE	ADD_LOGFILE (
	LOGFILENAME		IN		VARCHAR,
	OPTIONS			IN		INT		DEFAULT	DBMS_LOGMNR.ADDFILE,
    RAFTID			IN      INT
);
```

参数详解

  * LogFileName

增加的文件的全路径。

  * Options

可选配置参数，具体的可选配置包括：

◆ DBMS_lOGMNR."NEW"结束当前 LOGMNR（调用 LOGMNR_END），并增加指定文件（如果已经 START，则不可增加）

◆ DBMS_lOGMNR.ADDFILE 在当前 LOGMNR 中增加日志文件（如果已经 START，则不可增加）

◆ DBMS_lOGMNR.REMOVEFILE 从当前 LOGMNR 中去除一个日志文件

  * RAFTID

用于 DMDPC 环境下指定 RAFT 组的 RAFT_ID，后续 DBMS_LOGMNR 包相关操作都在该 RAFT 组的主节点执行。非 DMDPC
环境忽略此参数。当调用 END_LOGMNR()或 REMOVE_LOGFILE()清除所有日志时，指定的 RAFTID 不再生效。若想 RAFTID
继续生效，需要调用 ADD_LOGFILE()重新指定。



  2. COLUMN_PRESENT



判断某列是否被包含在指定的一行逻辑记录中。

语法如下：

```
FUNCTION COLUMN_PRESENT (
	SQL_REDO_UNDO	IN		BIGINT,
	COLUMN_NAME		IN		VARCHAR		DEFAULT	''
) RETURN INT;
```

参数详解

  * sql_redo_undo

REDO 或 UNDO 记录的唯一标识，对应 V$LOGMNR_CONTENTS 中的 REDO_VALUE 或 UNDO_VALUE 字段。

  * column_name

由模式名.表名.列名组成的字符串。



返回值

0 或 1，0 表示该值不可以挖掘，1 表示可以挖掘

  3. END_LOGMNR



语法如下：

```
PROCEDURE END_LOGMNR();
```

功能说明：

结束 LOGMNR。

  4. MINE_VALUE



以字符串的格式来获取某一条日志中包含的指定列的值。

语法如下：

```
FUNCTION	MINE_VALUE(
	SQL_REDO_UNDO	IN		BIGINT,
	COLUMN_NAME		IN		VARCHAR		DEFAULT ''
) RETURN VARCHAR;
```

参数详解

  * sql_redo_undo

REDO 或 UNDO 记录的唯一标识，对应 V$LOGMNR_CONTENTS 中的 REDO_VALUE 或 UNDO_VALUE 字段。

  * column_name

由模式名.表名.列名组成的字符串。



返回值

以字符串的格式返回某一条日志中包含的指定列的值。

  5. REMOVE_LOGFILE



从日志列表中移除某个日志文件。

语法如下：

```
PROCEDURE	REMOVE_LOGFILE ( 
	LOGFILENAME		IN	VARCHAR
);
```

参数详解

  * LogFileName



待移除的日志文件名。

  6. START_LOGMNR



根据指定的模式和条件来开始某个会话上的 LOGMNR，一个会话上仅能 START 一个 LOGMNR。

语法如下：

```
PROCEDURE 	START_LOGMNR ( 
	STARTSCN		IN		BIGINT		DEFAULT 0,
	ENDSCN			IN		BIGINT		DEFAULT 0,
	STARTTIME		IN		DATETIME	DEFAULT '1988/1/1',
	ENDTIME			IN		DATETIME	DEFAULT '2110/12/31',
	DICTFILENAME	IN		VARCHAR		DEFAULT '',
	OPTIONS			IN		INT			DEFAULT	0 
);
```

参数详解

  * startScn

分析时或者加载时的过滤条件，日志起始序列号，缺省为 0，表示无限制。

  * endScn

分析时或者加载时的过滤条件，日志结束序列号，缺省为 0，表示无限制。

  * startTime

分析时或者加载时的过滤条件，日志起始时间，缺省为 1988/1/1。

  * endTime

分析时或者加载时的过滤条件，日志结束时间，缺省为 2110/12/31。

  * DictFileName

离线字典的全路径名，缺省为空。目前 DM 暂不支持离线字典功能。

  * Options

提供如下表所列的可选模式，各模式可以通过 + 或者按位或来进行组合。其它位的值如 1、4、8
等目前不支持，配置后不会报错，但是没有效果。例如，组合全部模式，则取值计算方法为 2+16+64+2048=2130，那么 Options 值取就是
2130。



|**Options**|**对应值**|**说明**|
|-------|-------|-------|
|COMMITTED_DATA_ONLY|2|仅从已提交的事务的日志中挖掘信息|
|DICT_FROM_ONLINE_CATALOG|16|使用在线字典|
|NO_SQL_DELIMITER|64|拼写的 SQL 语句最后不添加分隔符|
|NO_ROWID_IN_STMT|2048|拼写的 SQL 语句中不包含 ROWID|


拼写的 SQL 语句使用的是当前系统中的表信息。表定义不同的两个同名表（例如同名不同列）被认定为是两个不同的表。如果 SQL 中包含的表不存在则该 SQL 语句不能被挖掘。

## 9.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_LOGMNR');
```

一下示例展示日志分析工具使用流程。

第 1 步 配置环境

(1)修改 dm.ini 中的参数，如下：

```
ARCH_INI = 1
RLOG_APPEND_LOGIC = 1
```

(2)dmarch.ini 需要配置本地归档，举例如下：

```
[ARCHIVE_LOCAL1]
   ARCH_TYPE = LOCAL
   ARCH_DEST = d:\dmdata\arch
   ARCH_FILE_SIZE = 128 #单位Mb
   ARCH_SPACE_LIMIT = 0 #单位Mb，0表示无限制，范围1024~4294967294M
```

第 2 步 添加归档日志文件

(1) 查询有哪些归档日志

```
select name , first_time , next_time , first_change# , next_change# from
v$archived_log;
```

设查询结果如下：

```
d:\DMdata\arch\ARCHIVE_LOCAL1_20140505155106577.log
2014-05-05 15:51:06.000000 2014-05-05 15:51:07.000000
1                     23303
```

(2) 添加一个或多个需要分析的归档日志文件

```
dbms_logmnr.add_logfile('d:\DMdata\arch\ARCHIVE_LOCAL1_20140505155106577.log');
```

如要查看通过 ADD_LOGFILE 添加的归档日志文件，可以通过动态视图 V$LOGMNR_LOGS 进行查询，如下：

```
select low_scn, next_scn, low_time, high_time, log_id, filename from v$logmnr_logs;
```

查询结果如下：

```
1                          23303
2014-05-05 15:51:06.000000 2014-05-05 15:51:07.000000  0
d:\DMdata\arch\ARCHIVE_LOCAL1_20140505155106577.log
```

第 3 步 启动归档日志文件分析

```
dbms_logmnr.start_logmnr(Options=>2128 , Starttime=>to_date('2014-03-04
17:36:00','YYYY-MM-DD HH24:MI:SS') , Endtime=>to_date('2014-06-04
17:36:02','YYYY-MM-DD HH24:MI:SS'));
```

如要查看归档日志文件的分析结果，可以通过动态视图 V$LOGMNR_CONTENTS 进行查询，如下：

```
select operation_code , scn, sql_redo , timestamp ,seg_owner, table_name from
V$LOGMNR_CONTENTS where seg_owner = 'SYSDBA' and operation_code in (3,1,2);
```

第 4 步 终止归档日志文件分析

```
dbms_logmnr.end_logmnr();
```

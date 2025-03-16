

# DBMS_SQLTUNE 包

DBMS_SQLTUNE 包提供一系列对实时 SQL 监控的方法。当 SQL 监控功能开启后，DBMS_SQLTUNE 包可以实时监控 SQL 执行过程中的信息，包括：执行时间、执行代价、执行用户、统计信息等情况。

SQL 监控功能开启的方法是将 DM.INI 参数 ENABLE_MONITOR 和 MONITOR_SQL_EXEC 均设置为 1。MPP 环境下，当前节点执行的 SQL，只能在当前节点查询监控报告，其他节点无法查询。

## 39.1 相关方法

  1. REPORT_SQL_MONITOR



返回单个 SQL 的实时监控报告。

语法如下：

```
FUNCTION REPORT_SQL_MONITOR(
	SQL_ID					IN 		VARCHAR2	DEFAULT  NULL,
	DBOP_NAME                 	IN 		VARCHAR2 	DEFAULT  NULL,
  	DBOP_EXEC_ID             	IN 		INT			DEFAULT  NULL,
   	SESSION_ID                	IN 		BIGINT    	DEFAULT  NULL,
   	SESSION_SERIAL        	IN 		INT       	DEFAULT  NULL,
   	SQL_EXEC_START         	IN 		DATETIME	DEFAULT  NULL,
   	SQL_EXEC_ID             	IN 		BIGINT       	DEFAULT  NULL,
   	INST_ID                   	IN 		INT       	DEFAULT  NULL,
   	START_TIME_FILTER     	IN 		DATETIME 	DEFAULT  NULL,
   	END_TIME_FILTER         	IN 		DATETIME 	DEFAULT  NULL,
   	INSTANCE_ID_FILTER     	IN 		INT       	DEFAULT  NULL,
   	PARALLEL_FILTER        	IN 		VARCHAR2 	DEFAULT  NULL,
   	PLAN_LINE_FILTER        	IN 		INT      	DEFAULT  NULL,
   	EVENT_DETAIL             	IN 		VARCHAR2 	DEFAULT  'YES',
   	BUCKET_MAX_COUNT       	IN 		INT       	DEFAULT  128,
   	BUCKET_INTERVAL       	IN 		INT       	DEFAULT  NULL,
   	BASE_PATH                	IN 		VARCHAR2 	DEFAULT  NULL,
   	LAST_REFRESH_TIME       	IN 		DATETIME 	DEFAULT  NULL,
   	REPORT_LEVEL             	IN 		VARCHAR2 	DEFAULT  'TYPICAL',
   	TYPE                       	IN 		VARCHAR2 	DEFAULT  'TEXT',
   	SQL_PLAN_HASH_VALUE    	IN 		INT     		DEFAULT  NULL,
   	CON_NAME                 	IN 		VARCHAR2 	DEFAULT  NULL,
   	REPORT_ID                 	IN 		INT      	DEFAULT  NULL
)RETURN CLOB;
```

参数详解

  * SQL_ID

表示展示 SQL_ID 对应语句的监控报告。SQL_ID 表示 SQL 语句编号，全局递增。相同 SQL 语句对应 SQL_ID 一般相同。

当 SQL_ID 为 NULL 时，表示返回最后一条被监控 SQL 的报告。

  * SESSION_ID

展示 SESSION_ID 指定会话的监控报告，默认为 NULL。SESSION_ID 表示 SESSION 地址信息，SESSION 地址会重用，导致
SESSION_ID 相同。

SESSION_ID 为-1 表示当前会话。

  * SESSION_SERIAL

会话的唯一标识。全局递增的，不会重复。

  * SQL_EXEC_ID

表示展示 SQL_EXEC_ID 执行号对应语句的监控报告。SQL_EXEC_ID 为 SQL 执行编号，全局递增。相同 SQL 语句多次执行，对应
SQL_EXEC_ID 也各不相同。

  * TYPE

监控报告格式。目前只支持文本“TEXT”格式，其它报错。

  * 其它参数

保留参数，暂时忽略，不进行合法性检查。



  2. REPORT_SQL_MONITOR_LIST



返回被监控 SQL 的概要信息列表。

语法如下：

```
FUNCTION REPORT_SQL_MONITOR_LIST(
   	SQL_ID					IN 		VARCHAR2	DEFAULT  NULL,
   	SESSION_ID              	IN 		BIGINT   	DEFAULT  NULL,
   	SESSION_SERIAL         	IN 		INT       	DEFAULT  NULL,
   	INST_ID                  	IN 		INT       	DEFAULT  NULL,
   	ACTIVE_SINCE_DATE    	IN 		DATETIME 	DEFAULT  NULL,
   	ACTIVE_SINCE_SEC      	IN 		INT       	DEFAULT  NULL,
   	ACTIVE_BEFORE_DATE   	IN 		DATETIME	DEFAULT  NULL,
   	LAST_REFRESH_TIME       	IN 		DATETIME 	DEFAULT  NULL,
   	DBOP_NAME                	IN 		VARCHAR2 	DEFAULT  NULL,
   	MONITOR_TYPE             	IN 		INT       	DEFAULT  MONITOR_TYPE_ALL,
   	MAX_SQLTEXT_LENGTH     	IN 		INT       	DEFAULT  NULL,
   	TOP_N_COUNT              	IN 		INT       	DEFAULT  NULL,
   	TOP_N_RANKBY            	IN 		VARCHAR2	DEFAULT  'LAST_ACTIVE_TIME',
   	REPORT_LEVEL           	IN 		VARCHAR2	DEFAULT  'TYPICAL',
   	AUTO_REFRESH             	IN 		INT       	DEFAULT  NULL,
   	BASE_PATH               	IN 		VARCHAR2	DEFAULT  NULL,
   	TYPE                     	IN 		VARCHAR2	DEFAULT  'TEXT',
   	CON_NAME                	IN 		VARCHAR2 	DEFAULT  NULL,
   	TOP_N_DETAIL_COUNT    	IN 		INT       	DEFAULT  NULL
)RETURN CLOB;
```

参数详解

  * SQL_ID

表示展示 SQL_ID 对应语句的监控报告。当 SQL_ID 为 NULL 时，表示返回所有被监控 SQL 的报告。

  * SESSION_ID

展示 SESSION_ID 指定会话的监控报告，默认为 NULL。SESSION_ID 为-1 表示当前会话。

  * SESSION_SERIAL

会话的唯一标识。

  * MAX_SQLTEXT_LENGTH SQL

文本的最大长度，默认为 NULL 表示没限制。SQL 文本长度是字符长度，而不是字节长度。

  * TYPE

监控报告格式。目前只支持文本“TEXT”格式，其它报错。

  * 其它参数

暂时忽略，不进行合法性检查。



  3. CREATE_TUNING_TASK



创建语句调优任务。

语法如下：

```
FUNCTION CREATE_TUNING_TASK(
   	SQL_TEXT			IN 		CLOB		DEFAULT  NULL,
   	BIND_LIST            	IN 		VARCHAR   	DEFAULT  NULL,
   	USER_NAME            	IN 		VARCHAR   	DEFAULT  NULL,
   	SCOPE               	IN 		VARCHAR   	DEFAULT  'COMPREHENSIVE',
   	TIME_LIMIT         	IN 		INT 			DEFAULT  1800,
   	TASK_NAME          	IN 		VARCHAR   	DEFAULT  NULL,
   	DESCRIPTION        	IN 		VARCHAR   	DEFAULT  NULL,
   	CON_NAME         		IN 		VARCHAR   	DEFAULT  NULL,
   	DATABASE_LINK_TO   	IN 		VARCHAR   	DEFAULT  NULL
)RETURN VARCHAR;
```

参数详解

  * SQL_TEXT

调优语句。

  * BIND_LIST

调优语句的绑定参数部分。目前仅为保留参数，无实际作用，只能传入 NULL。

  * USER_NAME

调优任务所属用户的用户名称。

  * SCOPE

调优等级，可取值为 COMPREHENSIVE 或 LIMITED，仅语法支持。

  * TIME_LIMIT

调优任务的最大执行时间，超时则会中断调优任务，单位为秒。

  * TASK_NAME

调优任务名称，值为 NULL 时由系统分配名称。

  * DESCRIPTION

调优任务相关描述。

  * CON_NAME

连接名。目前仅为保留参数，无实际作用，只能传入 NULL。

  * DATABASE_LINK_TO

数据库链接。目前仅为保留参数，无实际作用，只能传入 NULL。



  4. EXECUTE_TUNING_TASK



执行语句调优任务。

语法如下：

```
FUNCTION EXECUTE_TUNING_TASK(
   	TASK_NAME          		IN 		VARCHAR   	DEFAULT  NULL,
   	EXECUTION_NAME			IN 		VARCHAR   	DEFAULT  NULL, 
   	EXECUTION_PARAMS        	IN 		VARCHAR   	DEFAULT  NULL,
   	EXECUTION_DESC         	IN 		VARCHAR   	DEFAULT  NULL,
   	DATABASE_LINK_TO       	IN 		VARCHAR   	DEFAULT  NULL
)RETURN VARCHAR;

PROCEDURE EXECUTE_TUNING_TASK(
   	TASK_NAME          		IN 		VARCHAR   	DEFAULT  NULL,
   	EXECUTION_NAME			IN 		VARCHAR   	DEFAULT  NULL, 
   	EXECUTION_PARAMS        	IN 		VARCHAR   	DEFAULT  NULL,
   	EXECUTION_DESC         	IN 		VARCHAR   	DEFAULT  NULL,
   	DATABASE_LINK_TO        	IN 		VARCHAR   	DEFAULT  NULL
);
```

参数详解

  * TASK_NAME

调优任务的名称。

  * EXECUTION_NAME

调优任务的执行名称，值为 NULL 则由系统分配名称。

  * EXECUTION_PARAMS

调优任务的执行参数。目前仅为保留参数，无实际作用。

  * EXECUTION_DESC

执行描述信息。

  * DATABASE_LINK_TO

数据库链接。目前仅为保留参数，无实际作用，只能传入 NULL。



  5. REPORT_TUNING_TASK



输出语句调优报告。

语法如下：

```
FUNCTION REPORT_TUNING_TASK(
   	TASK_NAME          		IN 		VARCHAR   	DEFAULT  NULL, 
   	TYPE          			IN 		VARCHAR   	DEFAULT  'TEXT',
   	LEVEL          			IN 		VARCHAR   	DEFAULT  'TYPICAL',
   	"SECTION"          		IN 		VARCHAR   	DEFAULT  'ALL',
   	OBJECT_ID				IN 		VARCHAR   	DEFAULT  NULL, 
   	RESULT_LIMIT        		IN 		VARCHAR   	DEFAULT  NULL,
   	OWNER_NAME         		IN 		VARCHAR   	DEFAULT  NULL, 
   	EXECUTION_NAME			IN 		VARCHAR   	DEFAULT  NULL, 
   	DATABASE_LINK_TO     	IN 		VARCHAR   	DEFAULT  NULL
)RETURN CLOB;
```

参数详解

  * TASK_NAME

调优任务的名称。

  * TYPE

报告输出的格式，仅支持“TEXT”格式。

  * LEVEL

报告的等级，可取值为 BASIC、TYPICAL、ALL。BASIC：基本等级；TYPICAL：常规等级；ALL：详细等级。

  * "SECTION"

报告的内容，可取值为
SUMMARY、FINDINGS、PLAN、INFORMATION、ERROR、ALL。SUMMARY：概要；FINDINGS：优化点；PLAN：优化计划；INFORMATION：综合信息；ERROR：语法错误；ALL：全部信息。

  * OBJECT_ID

对象 ID，涉及多个对象时可使用该参数输出指定对象的调优报告。

  * RESULT_LIMIT

结果集行数限制。目前仅为保留参数，无实际作用。

  * OWNER_NAME

调优任务的所属用户名称。

  * EXECUTION_NAME

调优任务的执行名称。

  * DATABASE_LINK_TO

数据库链接。目前仅为保留参数，无实际作用，只能传入 NULL。



  6. DROP_TUNING_TASK



删除语句调优任务。

语法如下：

```
PROCEDURE DROP_TUNING_TASK(
	TASK_NAME          	IN 		VARCHAR   	DEFAULT  NULL
);
```

参数详解

  * TASK_NAME

待删除的调优任务名称。



## 39.2 举例说明

DBMS_SQLTUNE 包会根据系统参数的不同给出相应的调优建议，因此不同系统中下面例子的运行结果可能会不同。

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_SQLTUNE');
```

例 1 监控 SQL 语句。

一 开启 SQL 监控开关。

设置 DM.INI 参数 ENABLE_MONITOR 和 MONITOR_SQL_EXEC 为 1。

```
SQL>SP_SET_PARA_VALUE (1,'ENABLE_MONITOR',1);
SQL>SP_SET_PARA_VALUE (1,'MONITOR_SQL_EXEC',1);
```

二 执行 SQL 语句。

```
SQL>SELECT  * FROM DBA_OBJECTS;
……
已用时间: 146.907(毫秒). 执行号:11
SQL>SELECT * FROM DBA_TABLES;
……
已用时间: 544.633(毫秒). 执行号:24.
```

得到两条 SQL 语句的执行号 SQL_EXEC_ID 分别为 11 和 24。

三 查询 SQL 监控报告。

为了能够完整展示 SQL 监控报告，特使用 SET 命令将将 DIsql 环境变量 LONG 设置成一个较大值 999999。

```
SQL>SET LONG 999999
```

根据 SQL 执行号来查询监控报告。

```
SQL>select DBMS_SQLTUNE.REPORT_SQL_MONITOR(SQL_EXEC_ID=>11) from dual;
或
SQL>select DBMS_SQLTUNE.REPORT_SQL_MONITOR(SQL_EXEC_ID=>24) from dual;
```

四 查询 SQL 监控报告链表。

```
select DBMS_SQLTUNE.REPORT_SQL_MONITOR_list() from dual;
```

例 2 SQL 语句调优。

准备数据表。

```
SQL>CREATE TABLE TEST(C1 INT);
SQL>INSERT INTO TEST VALUES(1);
SQL>INSERT INTO TEST VALUES(2);
SQL>INSERT INTO TEST VALUES(3);
```

一 创建语句调优任务。

```
SQL>DBMS_SQLTUNE.CREATE_TUNING_TASK('SELECT C1 FROM TEST', TASK_NAME=>'TASK1');
```

二 执行语句调优任务。

```
SQL>DBMS_SQLTUNE.EXECUTE_TUNING_TASK('TASK1');
```

三 输出语句调优报告。

```
//首先将环境变量LONG设置成一个较大值999999，以保证完整显示调优报告
SQL>SET LONG 999999
SQL>SELECT DBMS_SQLTUNE.REPORT_TUNING_TASK('TASK1');
```

查询结果如下：

```
行号     DBMS_SQLTUNE.REPORT_TUNING_TASK('TASK1')                                                                    
---------- ---------------------------------------------------------
1          GENERAL INFORMATION SECTION
--------------------------------------------------------------------
TUNING TASK NAME    : TASK1
TUNING TASK OWNER   : SYSDBA
WORKLOAD TYPE       : SINGLE SQL STATEMENT
EXECUTION COUNT     : 1
CURRENT EXECUTION   : SYSEXECNAME_1
EXECUTION TYPE      : TUNE SQL
SCOPE               : COMPREHENSIVE
TIME LIMIT(SECONDS) : 1800
COMPLETION STATUS   : COMPLETED
STARTED AT          : 2021-06-30 14:45:38
COMPLETED AT        : 2021-06-30 14:45:38
SQL TEXT            : SELECT C1 FROM TEST

--------------------------------------------------------------------

FINDING SECTION (2 FINDINGS)
--------------------------------------------------------------------
  1 - STATISTICS FINDING
  ----------------------
    尚未分析表 SYSDBA.TEST。
    RECOMMENDATION
    --------------
    考虑收集此表的优化程序统计信息。
    CALL DBMS_STATS.GATHER_TABLE_STATS('SYSDBA', 'TEST');
  2 - INDEX FINDING
  -----------------
    通过创建一个或多个索引可以改进此语句的执行计划。
    RECOMMENDATION
    --------------
    考虑运行创建推荐的索引。
    CREATE INDEX IDX$$_1 ON "SYSDBA"."TEST"("C1");
```

四 删除语句调优任务。

```
SQL>DBMS_SQLTUNE.DROP_TUNING_TASK('TASK1');
```

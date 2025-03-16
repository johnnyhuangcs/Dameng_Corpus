

# DBMS_JOB 包

DBMS_JOB 系统包用于创建定时任务，支持按指定的时间或间隔执行用户定义的作业。

DM MPP 环境下不支持 DBMS_JOB 包。

## 6.1 相关方法

  1. BROKEN 过程



更新一个已提交的工作的状态，典型地是用来把一个已过期工作标记为未过期工作；或者把一个未过期的工作设置为何时过期。

语法如下：

```
PROCEDURE Broken(
  job		in integer，
  broken	in boolean,
  next_date in datetime :=SYSDATE
);
```

参数详解

  * job

工作号，唯一标识一个特定工作。

  * broken

指示此工作是否将标记为过期，TRUE 或 FALSE。TRUE 表明过期，而 FALSE 表明未过期。

  * next_date

指示在什么时候此工作将再次运行。此参数缺省值为当前日期和时间。



  2. CHANGE 过程



用来改变指定工作的设置。

语法如下：

```
PROCEDURE Change (
	JOB			IN	INTEGER,
	WHAT		IN	VARCHAR2,
	NEXT_DATE	IN	DATETIME, 
	INTERVAL	IN	VARCHAR2,
	INSTANCE	IN	INT DEFAULT 0,
	FORCE		IN	BOOLEAN DEFAULT FALSE
);
```

参数详解

  * job

工作号，唯一标识一个特定工作。

  * what

是由此工作运行的一块 PL/SQL 代码块。

  * next_date

指示何时此工作将被执行。

  * interval

指示一个工作重执行的频度。

  * instance

指定 DSC 集群环境中执行该作业的节点号，取值范围 [0,15]。节点号必须是实际存在的节点。

  * force

参数仅作兼容用，无实际作用。





  3. INTERVAL 过程



用来显式地设置重执行一个工作之间的时间间隔数。

语法如下：

```
PROCEDURE Interval (
  job		in integer,
  interval	in varchar2
);
```

参数详解

  * job

工作号，唯一标识一个特定工作。

  * interval

指示一个工作重执行的频度，该频度是一个日期表达式字符串，以当天 0 点 0
分为起点和该日期表达式字符串计算出来的日期时间之差，以秒为最小间隔单位。例如'sysdate + 1/86400'则表示间隔 1
秒，'sysdate+1/1440'则表示间隔 1 分钟，'sysdate + 1/24'则表示间隔 1 小时，'sysdate +1'则表示间隔 1
天，最多允许间隔 100 天。达梦的 INTERVAL 参数不支持以周，月，季等单位方式指定间隔。





  4. ISUBMIT 过程



用来用特定的工作号提交一个工作。这个过程与 Submit()过程的唯一区别在于此 job 参数作为 IN 型参数传递且包括一个由开发者提供的工作号。如果提供的工作号已被使用，将产生一个错误。

语法如下：

```
PROCEDURE ISubmit (
  job		in integer,
  what		in varchar2,
  next_date	in datetime,
  interval	in varchar2 default null,
  no_parse	in boolean:=FALSE
);
```

参数详解

  * job

是由用户指定的工作编号，不能为负数，且必须是不存在的工作编号。

  * what

是将被执行的 PL/SQL 代码块。

  * next_date

指示何时将运行这个工作。

  * interval

何时这个工作将被重执行，不指定时缺省为空，表示仅执行一次。

  * no_parse

指示此工作在提交时或执行时是否应进行语法分析，TRUE 指示此 PL/SQL 代码在它第一次执行时应进行语法分析，而 FALSE 指示本 PL/SQL
代码应立即进行语法分析。



  5. NEXT_DATE 过程



用来显式地设定一个工作的执行时间。

语法如下：

```
PROCEDURE Next_Date(
  job		in integer,
  next_date	in datetime
);
```

参数详解

  * job

标识一个已存在的工作。

  * next_date

指示了此工作应被执行的日期与时间。



  6. REMOVE 过程



用来删除一个已计划运行的工作。已正在运行的工作不能由调用过程序删除。

语法如下：

```
PROCEDURE Remove(
  job in integer
);
```

参数详解

  * job

唯一地标识一个工作。这个参数的值是由为此工作调用 Submit()过程返回的 job 参数的值。



  7. RUN 过程



用来立即执行一个指定的工作。需要说明的是，RUN 过程的调用不影响工作的计划运行时间。

语法如下：

```
PROCEDURE Run(
  JOB	IN	INTEGER,
  FORCE	BOOLEAN DEFAULT FALSE
);
```

参数详解

  * job

标识将被立即执行的工作。

  * force

参数仅作兼容用，无实际作用。



  8. SUBMIT 过程



使用 Submit()过程，工作被正常地计划好。

语法如下：

```
PROCEDURE	SUBMIT (
	JOB			OUT	INTEGER, 
	WHAT		IN	VARCHAR2, 
	NEXT_DATE	IN	DATE DEFAULT SYSDATE, 
	INTERVAL	IN	VARCHAR2 DEFAULT NULL, 
	NO_PARSE	IN	BOOLEAN DEFAULT FALSE,
	INSTANCE	IN	INT DEFAULT 0,
	FORCE		IN	BOOLEAN DEFAULT FALSE
);
```

参数详解

  * job

是由 Submit()过程返回的工作编号。这个值用来唯一标识一个工作。

  * what

是将被执行的 PL/SQL 代码块。

  * next_date

指示将何时运行这个工作，缺省值为当前的日期和时间。

  * interval

何时这个工作将被重执行，不指定时缺省为空，表示仅执行一次。

  * no_parse

指示此工作在提交时或执行时是否应进行语法分析，TRUE 指示此 PL/SQL 代码在它第一次执行时应进行语法分析，而 FALSE 指示本 PL/SQL
代码应立即进行语法分析。

  * instance

指定 DSC 集群环境中执行该作业的节点号，取值范围 [0,15]。节点号必须是实际存在的节点。单节点环境中单机场景中 instance 参数无实际意义。

  * force

参数仅作兼容用，无实际作用。



  9. WHAT 过程



允许在工作执行时重新设置此正在运行的命令。

语法如下：

```
PROCEDURE What (
  job	in integer,
  what	in out varchar2
);
```

参数详解

  * job

标识一个存在的工作。

  * what

指示将被执行的新的 PL/SQL 代码，如果传入的 what 参数值为空串，那将返回该任务原来的 PL/SQL 代码。



## 6.2 相关视图

  1. DBA_JOBS 视图



描述数据库中所有的 JOB。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|JOB|INTEGER|工作的唯一标识号|
|2|LOG_USER|VARCHAR(8188)|提交工作的用户|
|3|PRIV_USER|VARCHAR(8188)|赋予工作权限的用户|
|4|SCHEMA_USER|VARCHAR(8188)|对工作做语法分析的用户模式|
|5|LAST_DATE|DATE|最后一次成功运行工作的时间|
|6|LAST_SEC|TIME(0)|HH:MM:SS 格式的 LAST_DATE 的时间|
|7|THIS_DATE|DATE|正在运行工作的开始时间|
|8|THIS_SEC|TIME(0)|HH:MM:SS 格式的 THIS_DATE 的时间|
|9|NEXT_DATE|DATE|下一次定时任务运行的时间|
|10|NEXT_SEC|TIME(0)|HH:MM:SS 格式的 NEXT_DATE 的时间|
|11|TOTAL_TIME|DOUBLE|该 JOB 每次运行时间的总和，该值会进行累加|
|12|BROKEN|VARCHAR(1)|是否工作中断标识参数，Y 是，N 否|
|13|INTERVAL|INTEGER|JOB 每次执行的时间间隔，单位：秒|
|14|FAILURES|BIGINT|工作运行连续没有成功的次数|
|15|WHAT|TEXT|执行工作的 PL/SQL 块|
|16|NLS_ENV|VARCHAR(1)|工作运行的 NLS 会话设置|
|17|MISC_ENV|VARCHAR(1)|工作运行的其他一些会话参数|
|18|INSTANCE|INTEGER|能够运行或正在运行工作的实例的 id 号。 单节点上默认值为 1，如果是 DM MPP 或 DMDSC 环境。 默认值为实例序号加 1。|


2.USER_JOBS 视图

描述当前用户所拥有的 JOB。视图结构与 DBA_JOBS 相同。

3.DBA_JOBS_RUNNING

显示实例中所有正在执行的作业。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|SID|BIGINT|作业执行的 SESSION 的 ID|
|2|JOB|INTEGER|作业号|
|3|FAILURES|INTEGER|作业自上一次成功以来的失败次数，暂不支持|
|4|LAST_DATE|DATE|最后一次成功运行工作的时间|
|5|LAST_SEC|TIME(0)|HH:MM:SS 格式的 LAST_DATE 的时间|
|6|THIS_DATE|DATE|本次运行的开始时间|
|7|THIS_SEC|TIME(0)|HH:MM:SS 格式的 THIS_DATE 的时间|
|8|INSTANCE|INTEGER|能够运行或正在运行作业的实例的 ID 号。 单节点上默认值为 1，如果是 DM MPP 或 DMDSC 环境，默认值为实例序号加 1|


## 6.3 创建、删除、重建语句

创建或删除 DBMS_JOB 系统包。

语法如下：

```
void
SP_INIT_JOB_SYS(
	CREATE_FLAG int
)
```

参数详解

  * CREATE_FLAG



取值 0、1、2。0 表示删除 DBMS_JOB 包；1 表示创建 DBMS_JOB 包；2 表示重建 DBMS_JOB 包中的相关方法，一般用于版本升级后，包中方法发生了改变的情况下使用。

返回值

无

举例说明

例 创建 DBMS_JOB 系统包。

```
SP_INIT_JOB_SYS(1);
```

## 6.4 举例说明

用户在使用 DBMS_JOB 包之前，需要提前调用系统过程 SP_INIT_JOB_SYS(1)，创建好调试所需要的包，就可以使用 DBMS_JOB 来调用 PL/SQL 中的过程或函数了。

```
SP_INIT_JOB_SYS(1);
```

创建测试表

```
create table a(a datetime);
```

创建一个自定义过程

```
create or replace procedure test as
begin
insert into a values(sysdate);
end;
/
```

创建 JOB

```
begin
dbms_job.isubmit(1,'test;',sysdate,'sysdate+1/1440');	//每天1440分钟，即一分钟运行test过程一次
  commit;
end;
/
```

查看 JOB 运行结果

```
select to_char(a,'yyyy/mm/dd hh24:mi:ss') 时间 from a;
```

结果如下：

```
时间
-------------------
2013/09/04 13:22:44
2013/09/04 13:23:44
2013/09/04 13:24:44
2013/09/04 13:25:44
```

删除 JOB

```
begin
	dbms_job.remove(1);
end;
/
```

PL/SQL 过程已成功完成。

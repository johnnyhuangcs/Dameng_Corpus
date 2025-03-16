

# DBMS_ALERT 包

dbms_alert 系统包用于生成并传递数据库预警信息，当发生特定数据库事件时能够将预警信息传递给应用程序。

达梦还提供了 dbms_alert_info 视图用于查看注册过的预警事件。

DM MPP 环境下不支持 DBMS_ALERT 包。

## 4.1 相关方法

1．DBMS_ALERT.REGISTER

为当前会话注册一个预警事件，本操作会提交当前事务。

语法如下：

```
DBMS_ALERT.REGISTER (
   NAME IN VARCHAR(30)
)
```

参数详解

  * NAME

输入参数，预警事件名。



2．DBMS_ALERT.REMOVE

删除当前会话的一个预警事件，如果该预警事件正在 DBMS_ALERT.WAITONE 或者 DBMS_ALERT.WAITANY 等待，则被阻塞，直到 DBMS_ALERT.WAITONE 或者 DBMS_ALERT.WAITANY 结束。本操作会提交当前事务。

语法如下：

```
DBMS_ALERT. REMOVE (
   NAME IN VARCHAR(30)
)
```

参数详解

  * NAME

输入参数，预警事件名。



3．DBMS_ALERT.REMOVEALL

删除当前会话下所有的预警事件，如果该预警事件正在 DBMS_ALERT.WAITONE 或者 DBMS_ALERT.WAITANY 等待，则被阻塞，直到 DBMS_ALERT.WAITONE 或者 DBMS_ALERT.WAITANY 结束。本操作会提交当前事务。

语法如下：

```
DBMS_ALERT. REMOVEALL ()
```

4．DBMS_ALERT.SIGNAL

给所有名为 name 的预警事件发送消息，当前事务提交时生效。

语法如下：

```
DBMS_ALERT. SIGNAL (
	NAME	IN VARCHAR(30),
	MESSAGE IN VARCHAR(1800)
)
```

参数详解

  * NAME

输入参数，预警事件名。

  * MESSAGE

输入参数，待发送的消息。



5．DBMS_ALERT.SET_DEFAULTS

设置轮询时间，仅语法支持。

语法如下：

```
DBMS_ALERT. SET_DEFAULTS (
  SENSITIVITY IN INT
)
```

参数详解

  * SENSITIVITY

输入参数，轮询时间间隔，单位秒。参数为 NULL，则使用缺省值 5s。



6．DBMS_ALERT.WAITONE

预警事件上等待预警信号，获得 DBMS_ALERT.SIGNAL 发送的消息内容。本操作会提交当前事务。

语法如下：

```
DBMS_ALERT. WAITONE (
  NAME IN VARCHAR(30),
  MESSAGE OUT VARCHAR(1800),
  STATUS OUT INT,
  TIMEOUT IN INT DEFAULT 86400000
)
```

参数详解

  * NAME

输入参数，预警事件名。

  * MESSAGE

输出参数，获得 DBMS_ALERT. SIGNAL 发送的消息。

  * STATUS

输出参数，表示 WAITONE 是否成功。如果 BMS_ALERT.WAITONE 传入的 NAME 是已经注册过的预警，则 0 表示预警发生；1
表示预警等待超时。

  * TIMEOUT

输入参数，WAITONE 的等待超时时间，单位为秒，默认值为 86400000 秒。



7．DBMS_ALERT. WAITANY

语法如下：

```
DBMS_ALERT. WAITANY (
  NAME OUT VARCHAR(30),
  MESSAGE OUT VARCHAR(1800),
  STATUS OUT INT,
  TIMEOUT IN INT DEFAULT 86400000
)
```

参数详解

同 DBMS_ALERT. WAITONE。

功能说明：

等待当前会话任意一个预警信号，获得 DBMS_ALERT.SIGNAL 发送的消息内容。本操作会提交当前事务。会话上没有预警事件时，报错没有注册预警事件。

## 4.2 DBMS_ALERT_INFO 视图

DBMS_ALERT_INFO 视图可用于查看注册过的预警事件的信息，其列信息如下：

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|NAME|VARCHAR(30)|预警事件的名称|
|2|SID|BIGINT|创建预警事件的会话 ID|
|3|CHANGED|VARCHAR(1)|预警事件状态：Y/N，预警事件被 SIGNAL 则为 Y|
|4|MESSAGE|VARCHAR(1800)|预警信号所带的消息内容|


## 4.3 举例说明

使用包内的过程和函数之前，如果还未创建过例子中的系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_ALERT');
```

例 1 为当前会话注册一个名为 A1 的预警事件，给预警事件上等待预警信号，获得 DBMS_ALERT.SIGNAL 发送的消息内容，最后删除当前会话的一个预警事件。同时，会话 1 存活期间，可以通过 dbms_alert_info 视图查看到 A1 预警事件。

```
会话1：
	CREATE OR REPLACE PROCEDURE WAIT1 IS
	msg VARCHAR(1800);
	statu INTEGER;
	BEGIN
	dbms_alert.register('A1');
	dbms_alert.waitone('A1', msg, statu, 39);
	print 'Msg: ' || msg || ' Statu: ' || statu;
	dbms_alert.remove('A1');
	END WAIT1;
	/
	EXEC WAIT1;

同时会话2执行：
	CREATE OR REPLACE PROCEDURE SET_ALERT1
	is
	BEGIN
	dbms_alert.signal('A1', 'this is a sig');
	COMMIT;
	END SET_ALERT1;
	/
	EXEC SET_ALERT1;
```

会话 1 存活期间，在会话 2 上，通过 dbms_alert_info 视图查看 A1。

```
select * from dbms_alert_info;
```

查询结果如下：

```
NAME SID CHANGED MESSAGE
A1 83034240 Y this is a sig
```

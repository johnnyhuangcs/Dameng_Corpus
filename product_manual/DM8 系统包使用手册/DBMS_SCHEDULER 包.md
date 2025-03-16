

# DBMS_SCHEDULER 包

DBMS_SCHEDULER 包提供一系列调度相关的存储过程及方法供 PL/SQL 调用。

## 32.1 相关方法

### 32.1.1 SCHEDULE 对象

  1. CREATE_SCHEDULE 过程



创建一个调度。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.CREATE_SCHEDULE(
	SCHEDULE_NAME		IN 		VARCHAR,
	START_DATE			IN 		TIMESTAMP WITH TIME ZONE DEFAULT NULL,
	REPEAT_INTERVAL		IN 		VARCHAR,
	END_DATE			IN 		TIMESTAMP WITH TIME ZONE DEFAULT NULL, 
	COMMENTS    		IN 		VARCHAR 	DEFAULT NULL
);
```

参数详解

  * SCHEDULE_NAME

调度名称。要求在包模式下名称唯一。不能为空，若未设置，则报错。

  * START_DATE

调度第一次有效日期或者时间。对于重复的调度，START_DATE 是个参照值，这种情况 START_DATE 不是调度的开始时间，调用的开始决定于
REPEATE_INTERVAL 的设置。START_DATE 用于决定调度的第一个实例。

  * REPEATE_INTERVAL

调度重复间隔，用于指定调用隔多久重复一次。它是基于日历语法的表达式。对于命名的调度，REPEATE_INTERVAL 不支持 PL/SQL 表达式。

  * END_DATE

作业停止运行时间。如果为未设置，作业则一直有效。

  * COMMENTS

调度相关的评论或注释。默认为空。



  2. DROP_SCHEDULE 过程



删除一个调度。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.DROP_SCHEDULE(
	SCHEDULE_NAME		IN 		VARCHAR,
	FORCE				IN 		BOOLEAN 		DEFAULT FALSE
);
```

参数详解

  * SCHEDULE_NAME

调度名称。仅支持单独一个调度名称。

  * FORCE

设为 FALSE，说明调度删除前不能被任何作业引用，否则报错。

设为 TRUE，所有引用这个调度的作业在调度删除之前都将调为无效。

使用该调度，但正在运行的作业不受影响。

如果为 NULL，则取值为 FALSE。



### 32.1.2 PROGRAM 对象

  1. CREATE_PROGRAM 过程



创建一个程序。通过 CREATE_PROGRAM 方法创建的 PROGRAM 对象，在视图 DBA_SCHEDULER_PROGRAMS 或 USER_SCHEDULER_PROGRAMS 中可以查看到。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.CREATE_PROGRAM(
	PROGRAM_NAME			IN 		VARCHAR,
	PROGRAM_TYPE			IN 		VARCHAR,
	PROGRAM_ACTION			IN 		VARCHAR,
	NUMBER_OF_ARGUMENTS		IN 		INT 			DEFAULT 0,
	ENABLED					IN 		BOOLEAN 		DEFAULT FALSE,
	COMMENTS    			IN 		VARCHAR 		DEFAULT NULL
);
```

参数详解

  * PROGRAM_NAME

程序名称。要求在包模式下名称中名称唯一。若未设置，则报错。

  * PROGRAM_TYPE

程序类型。未设置，则报错。支持类型如下：

PLSQL_BLOCK：说明程序是一个 PL/SQL 语句块，即 DM 的 SQL
程序设计里的语句块。此类型的程序或者作业不允许设置参数个数，也就是说设置的参数个数必须为 0；

STORED_PROCEDURE：说明程序是一个 PL/SQL 存储过程。支持存储过程，不支持带有返回值的函数。包含输入输出或者输出参数的 PL/SQL
存储过程不支持。

  * PROGRAM_ACTION

定义程序的动作。可能的动作如下：

对于 PL/SQL 语句块，程序动作就是去执行 PL/SQL 代码。

对于存储过程，程序动作是存储过程的名称。如果存储过程与作业不属于同一个模式，则需要指定存储过程名称的时候，指定模式名。

若未指定程序动作，则报错。

  * number_of_arguments

定义程序包含的参数的个数。若未设置，则默认为 0。一个程序最多可以指定 255 个参数。

  * enabled

指定程序是否以激活的方式创建。若设置为 TRUE，则执行合法性检测，检测成功，则创建程序并置 ENABLED 状态。默认设置为
FALSE，那么程序不以激活的方式创建，可以在程序使用之前通过调用 ENABLE 过程来激活。

如果为 NULL，则取值为 FALSE。

  * COMMENTS

程序的相关评论或者注释。默认为空。



  2. DEFINE_PROGRAM_ARGUMENT 过程



该过程为重载过程。通过 DEFINE_PROGRAM_ARGUMENT 方法定义的 PROGRAM 参数信息，可以在 DBA_SCHEDULER_PROGRAM_ARGS 或 USER_SCHEDULER_PROGRAM_ARGS 视图中查看得到。

1）定义一个程序参数，不带默认值。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.DEFINE_PROGRAM_ARGUMENT(
	PROGRAM_NAME			IN 		VARCHAR,
	ARGUMENT_POSITION		IN 		INT,
	ARGUMENT_NAME			IN 		VARCHAR 		DEFAULT NULL,
	ARGUMENT_TYPE			IN 		VARCHAR,
	OUT_ARGUMENT			IN 		BOOLEAN 		DEFAULT FALSE
);
```

2）定义一个程序参数，带默认值。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.DEFINE_PROGRAM_ARGUMENT(
	PROGRAM_NAME			IN 		VARCHAR,
	ARGUMENT_POSITION		IN 		INT,
	ARGUMENT_NAME			IN 		VARCHAR 		DEFAULT NULL,
	ARGUMENT_TYPE			IN 		VARCHAR,
	DEFAULT_VALUE			IN 		VARCHAR,
	OUT_ARGUMENT		    IN 		BOOLEAN 		DEFAULT FALSE
);
```

参数详解

  * PROGRAM_NAME

待定义参数的程序名。该程序必须已经存在。

  * ARGUMENT_POSITION

待定义参数的位置，可选值，从 1 开始到 NUMBER_OF_ARGUMENTS。若指定位置已经定义过，则将被覆盖。

  * ARGUMENT_NAME

定义参数名称，可选项。若设置，则要确保在程序所有参数名中唯一。若设置，则可能被其他包过程使用，包括 SET_JOB_ARGUEMENT_VALUE 过程。

  * ARGUMENT_TYPE

定义参数使用数据的类型。调度不证实也不使用这个类型。只有在程序用户决定赋值给参数时才使用。所有可用的 SQL 数据类型均允许，不支持用户自定义类型。

  * DEFAULT_VALUE

参数默认值。若作业未给参数设值，则将默认值赋值给对应参数。

  * OUT_ARGUMENT

预留参数，以后使用。必须设置为 FALSE。如果为 NULL，则取值为 FALSE。



  3. DROP_PROGRAM 过程



删除一个程序。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.DROP_PROGRAM(
	PROGRAM_NAME		IN 		VARCHAR,
	FORCE				IN 		BOOLEAN 		DEFAULT FALSE
);
```

参数详解

  * PROGRAM_NAME

待删除的程序名。

  * FORCE

设为 FALSE，说明程序删除前不能被任何作业引用，否则报错。

设为 TRUE，所有引用这个程序的作业在调度删除之前都将调为无效。

使用该程序，且正在运行的作业不受影响，并允许继续运行。

如果为 NULL，则取值为 FALSE。



### 32.1.3 JOB 对象

  1. CREATE_JOB 过程



创建作业。通过 CREATE_JOB 创建的 JOB 对象，可以在 DBA_SCHEDULER_JOBS 或 USER_SCHEDULER_JOBS 视图中查看得到。

1）不使用已经存在的程序（PROGRAM）或者调度（SCHEDULE）创建作业。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.CREATE_JOB(
	JOB_NAME				IN 		VARCHAR,
	JOB_TYPE				IN 		VARCHAR,
	JOB_ACTION				IN 		VARCHAR,
	NUMBER_OF_ARGUMENTS		IN 		INT 			DEFAULT 0,
	START_DATE				IN 		TIMESTAMP WITH TIME ZONE DEFAULT NULL,
	REPEAT_INTERVAL			IN 		VARCHAR			DEFAULT NULL,
	END_DATE				IN 		TIMESTAMP WITH TIME ZONE DEFAULT NULL,
	JOB_CLASS				IN 		VARCHAR 		DEFAULT 'DEFAULT_JOB_CLASS',
	ENABLED					IN 		BOOLEAN 		DEFAULT FALSE,
	AUTO_DROP				IN 		BOOLEAN 		DEFAULT TRUE,
	COMMENTS				IN 		VARCHAR 		DEFAULT NULL,
	CREDENTIAL_NAME			IN 		VARCHAR 		DEFAULT NULL,
	DESTINATION_NAME        IN 		VARCHAR 		DEFAULT NULL  
);
```

2）使用命名程序（PROGRAM）和命名调度（SCHEDULE）创建作业。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.CREATE_JOB(
	JOB_NAME			IN 		VARCHAR,
	PROGRAM_NAME		IN 		VARCHAR,
	SCHEDULE_NAME		IN 		VARCHAR,
	JOB_CLASS			IN 		VARCHAR 		DEFAULT 'DEFAULT_JOB_CLASS',
    ENABLED				IN 		BOOLEAN 		DEFAULT FALSE,
    AUTO_DROP			IN 		BOOLEAN 		DEFAULT TRUE,
    COMMENTS			IN 		VARCHAR 		DEFAULT NULL,
	JOB_STYLE			IN 		VARCHAR 		DEFAULT 'REGULAR',
	CREDENTIAL_NAME		IN 		VARCHAR 		DEFAULT NULL,
	DESTINATION_NAME    IN 		VARCHAR 		DEFAULT NULL
);
```

3）使用命名程序（PROGRAM）和内置调度（SCHEDULE）创建作业。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.CREATE_JOB(
	JOB_NAME			IN 		VARCHAR,
	PROGRAM_NAME		IN 		VARCHAR,
	START_DATE			IN 		TIMESTAMP WITH TIME ZONE DEFAULT NULL,
	REPEAT_INTERVAL		IN 		VARCHAR 		DEFAULT NULL,
	END_DATE			IN 		TIMESTAMP WITH TIME ZONE DEFAULT NULL,
   	JOB_CLASS			IN 		VARCHAR 		DEFAULT 'DEFAULT_JOB_CLASS',
	ENABLED				IN 		BOOLEAN 		DEFAULT FALSE,
    AUTO_DROP			IN 		BOOLEAN 		DEFAULT TRUE,
    COMMENTS			IN 		VARCHAR 		DEFAULT NULL,
	JOB_STYLE			IN 		VARCHAR 		DEFAULT 'REGULAR', 
    CREDENTIAL_NAME		IN 		VARCHAR 		DEFAULT NULL,
	DESTINATION_NAME    IN 		VARCHAR 		DEFAULT NULL
);
```

  4. 使用命名调度（SCHEDULE）和内置程序（PROGRAM）创建作业。



语法如下：

```
PROCEDURE CREATE_JOB (
     JOB_NAME				IN 		VARCHAR,
     SCHEDULE_NAME			IN 		VARCHAR, 
     JOB_TYPE				IN 		VARCHAR,
     JOB_ACTION				IN 		VARCHAR,
     NUMBER_OF_ARGUMENTS	IN 		INT 			DEFAULT 0,
     JOB_CLASS				IN 		VARCHAR 		DEFAULT 'DEFAULT_JOB_CLASS',
     ENABLED				IN 		BOOLEAN 		DEFAULT FALSE,
     AUTO_DROP				IN 		BOOLEAN 		DEFAULT TRUE,
     COMMENTS				IN 		VARCHAR 		DEFAULT NULL,
     CREDENTIAL_NAME		IN 		VARCHAR 		DEFAULT NULL,
     DESTINATION_NAME		IN 		VARCHAR 		DEFAULT NULL
);
```

参数详解

  * JOB_NAME

作业名称。要求在包模式下名称中名称唯一，在其它模式下使用时，必须加模式名前缀。若未设置，则报错。使用 GENERATE_JOB_NAME
过程可以自动生成一个作业名。详见 _GENERATE_JOB_NAME_ _ **过程**_ 。

  * JOB_TYPE

创建作业类型。未设置，则报错。支持类型如下：

PLSQL_BLOCK：指定作业是一个匿名的 PL/SQL 语句块。此类型的程序或者作业不允许设置参数个数，也就是说设置的参数个数必须为 0；

STORED_PROCEDURE：指定作业是一个 PL/SQL 存储过程。支持存储过程，不支持带有返回值的函数。

  * JOB_ACTION

定义作业的动作。可能的动作如下：

对于 PL/SQL 语句块，程序动作就是去执行 PL/SQL 代码。

对于存储过程，程序动作是存储过程的名称。如果存储过程与作业不属于同一个模式，则需要指定存储过程名称的时候，指定模式名。

若未指定作业动作，则报错。

  * number_of_arguments

定义作业预期的参数个数。若未设置，则默认为 0。一个程序最多可以指定 255 个参数。

  * PROGRAM_NAME

和作业关联的 program 名称。

  * SCHEDULE_NAME

作业使用的调度名称。

  * START_DATE

作业起始日期。如果 START_DATE 和 REPEAT_INTERVAL 都为 NULL，表示作业在 ENABLE 后立即执行。

  * REPEAT_INTERVAL

作业重复执行及间隔时长。仅支持日历语法格式。

  * END_DATE

作业结束日期。

  * JOB_CLASS

不支持，仅作兼容参数用。

  * enabled

指定作业创建时是否启用。默认为 FALSE。作业如果未启用，则系统不会调度执行。作业创建时设置为 TRUE，会去检查作业是否有效。作业 ENABLE
时，如果作业所使用的 PROGRAM 无效，作业创建失败。

如果为 NULL，则取值为 FALSE。

  * AUTO_DROP

如果为 TRUE，作业自动删除。默认为 TRUE。自动删除的条件如下：

    * 作业的结束日期已经过期
    * 作业不是重复执行作业，并且只执行一次

如果为 NULL，则取值为 FALSE，作业不会自动删除。

  * COMMENTS

程序的相关评论或者注释。默认为空。

  * JOB_STYLE

不支持，只作兼容参数用。

  * CREDENTIAL_NAME

不支持，只作兼容参数用。

  * DESTINATION_NAME

不支持，只作兼容参数用。



  2. SET_JOB_ARGUMENT_VALUE 过程



重载过程。通过 SET_JOB_ARGUMENT_VALUE 设置的 JOB 参数值，可以在 DBA_SCHEDULER_JOB_ARGS 或 USER_SCHEDULER_JOB_ARGS 视图中查询得到。

  1. 通过指定位置设置参数值。



语法如下：

```
PROCEDURE DBMS_SCHEDULER.SET_JOB_ARGUMENT_VALUE(
	JOB_NAME				IN		VARCHAR,
    ARGUMENT_POSITION		IN		INT,
	ARGUMENT_VALUE			IN 		VARCHAR
);
```

  2. 通过指定参数名称，设置相应参数值。仅适用于作业使用了已经存在的程序对象，并且程序参数通过调用 DEFINE_PROGRAM_ARGUMENT 过程定义了参数名称。



语法如下：

```
PROCEDURE DBMS_SCHEDULER.SET_JOB_ARGUMENT_VALUE(
	JOB_NAME				IN 		VARCHAR,
	ARGUMENT_NAME			IN 		VARCHAR,
	ARGUMENT_VALUE			IN 		VARCHAR
);
```

参数详解

  * JOB_NAME

待设置参数值的作业名称。

  * ARGUMENT_NAME

待设置参数值的程序参数名称。

  * ARGUMENT_POSITION

待设置参数值的程序参数位置，从 1 开始。

  * ARGUMENT_VALUE

设置到程序参数上的新值，暂仅支持字符串类型的值。



  3. DROP_JOB 过程



删除一个作业过程。作业删除时，作业上所有设置参数值也都删除。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.DROP_JOB(
	JOB_NAME			IN 		VARCHAR,
	FORCE				IN 		BOOLEAN		DEFAULT FALSE,
	DEFER             	IN 		BOOLEAN		DEFAULT FALSE,
	COMMIT_SEMANTICS   	IN 		VARCHAR2	DEFAULT 'STOP_ON_FIRST_ERROR'
);
```

参数详解

  * JOB_NAME

待删除的作业名。

  * FORCE

设为 TRUE，调度首先尝试中止正在运行的作业实例（通过调用 STOP_JOB 过程，参数 force 设置 FALSE）。如果为 NULL，则取值为
FALSE。参数 DEFER 和参数 FORCE 都设为 TRUE 时无意义，因此不允许同时设为 TRUE。

  * DEFER

设为 TRUE，调度运行正在运行的作业完成，然后删除。如果为 NULL，则取值为 FALSE。参数 DEFER 和参数 FORCE 都设为 TRUE
时无意义，因此不允许同时设为 TRUE。

  * COMMIT_SEMANTICS

提交语义，暂不处理。



  4. RUN_JOB 过程



立即执行一次作业。执行以任务的方式异步进行，当系统任务较多时，需要排队等待执行。

语法如下：

```
PROCEDURE RUN_JOB (
	JOB_NAME               	IN		VARCHAR,
   	USE_CURRENT_SESSION     IN		BOOLEAN		DEFAULT TRUE
);
```

参数详解

  * JOB_NAME

待运行的作业名。

  * USE_CURRENT_SESSION

作业是否运行在和存储过程被调用的同一个会话中。不支持此功能，仅作兼容。如果为 NULL，则取值为 FALSE。



  5. STOP_JOB 过程



停止正在运行的作业。当作业的 AUTO_DROP 配置为 TRUE，停止作业时会检查作业是否需要删除，满足删除条件时将作业删除。

语法如下：

```
PROCEDURE STOP_JOB (
	JOB_NAME			IN		VARCHAR,
	FORCE				IN		BOOLEAN		DEFAULT FALSE,
	COMMIT_SEMANTICS	IN		VARCHAR		DEFAULT 'STOP_ON_FIRST_ERROR'
);
```

参数详解

  * JOB_NAME

待停止的作业名。

  * FORCE

FALSE 表示中断作业，DM 会用中断会话的方式停止作业，VM 里面会定期检测会话是否中断；

TRUE 表示立即停止作业，目前不支持这种方式。

如果为 NULL，则取值为 FALSE。

  * COMMIT_SEMANTICS

仅语法支持。



### 32.1.4 全局调度属性

全局调度属性是作用于整个 SCHEDULER 中所有作业上。

表32.1 全局属性列表

|**属性名称**|**取值**|**备注**|
|----|----|----|
|Default_timezone|Start_date 未指定时，用于重复作业和窗口应用日历语法格式串时获取时区信息|不支持，仅作兼容使用|
|Event_expiry_time|以秒为单位，作业从调度事件队列中产生的过期事件；若为 NULL，则过期时间为 24 小时。|不支持，仅作兼容使用|
|Log_history|作业日志和窗口日志的保存天数，默认 30 天。|不支持，仅作兼容使用|
|Max_job_slave_processes|特殊系统配置和加载的从进程的最大个数。默认是 NULL，有效值范围为 1-999.|不支持，仅作兼容使用|
|Email_server|调度用于发送作业状态事件邮件通知使用的 SMTP 服务器的地址。如果为 NULL，则不能发送。|支持|
|Email_port|调度用于发送作业状态事件邮件通知使用的 SMTP 服务器的端口号，默认 25。|扩展支持，DM 特有的选项|
|Email_password|邮件发送连接 SMTP 服务器的密码。|扩展支持，加密保存。|
|Email_sender|发送作业状态邮件通知的默认邮件地址，默认为 NULL|支持|
|Email_mimetype|邮件的 MIMETYPE，设置邮件使用的内容和编码方式，默认 TEXT/PLAIN; CHARSET=US-ASCII； 中文可调整为 TEXT/PLAIN; CHARSET=GB18030|扩展支持，DM 特有的选项|


  1. SET_SCHEDULER_ATTRIBUTE 过程



设置全局调度的一个属性。设置属性值会立即有效，但结果不一定立即出现。全局调度属性可以在 DBA_SCHEDULER_GLOBAL_ATTRIBUTE 视图中查询得到。

语法如下：

```
PROCEDURE SET_SCHEDULER_ATTRIBUTE(
	ATTRIBUTE		IN		VARCHAR,
 	VALUE			IN		VARCHAR
);
```

参数详解

  * ATTRIBUTE

属性名称，详见上表。

  * VALUE

属性值，详见上表说明，根据实际情况设置。



  2. GET_SCHEDULER_ATTRIBUTE 过程



获取全局调度的一个属性。

语法如下：

```
PROCEDURE GET_SCHEDULER_ATTRIBUTE(
	ATTRIBUTE		IN		VARCHAR,
	VALUE			OUT		VARCHAR
);
```

参数详解

  * ATTRIBUTE

属性名称，详见上表。

  * VALUE

属性值，详见上表说明，根据实际情况设置。



### 32.1.5 邮件通知

编辑作业事件的邮件通知。

表32.2 在邮件中SUBJECT和BODY参数中可使用的变量

|**变量名称**|**释义**|
|----|----|
|%job_owner%|创建的作业的模式|
|%job_name%|-|
|%job_subname%|用于基于事件的作业，设置了 parallel_instances 属性或者用于 CHAIN 步骤（STEPS）|
|%event_type%|见表 2|
|%event_timestamp%|事件时间戳|
|%log_id%|指向视图*_SCHEULER_JOB_LOG 和*_SCHEDULER_JOB_RUN_DETAILS 中的 LOG_ID 列。|
|%error_code%|错误码|
|%error_message%|错误信息|
|%run_count%|运行次数|
|%failure_count%|失败次数|
|%retry_count%|重试次数|


表32.3 调度抛出的事件类型

|**事件类型**|**释义**|
|----|----|
|Job_all_events|不是一个事件，是一个常量，用于提供一种简单的方法激活所有事件|
|Job_broken|作业必须已经 disabled 并且调整到 BROKEN 状态，因为执行失败次数超过了作业属性 max_failures 设置值|
|Job_chain_stalled|运行 CHAIN 的作业进入 CHAIN_STALLED 状态。A running chain becomes stalled if there are no steps running or scheduled to run and the chain evaluation_interval is set to NULL. No progress will be made in the chain unless there is manual intervention.|
|Job_completed|作业成功完成，达到 max_runs 或者 end_date|
|Job_disabled|调度本身调整作业 disabled，或者调用 SET_ATTRIBUTE|
|Job_failed|作业失败，可以是抛出错误，也可以是异常中断|
|Job_over_max_dur|作业执行超过了 max_run_duration 属性设置的最大运行周期。（注意：没必要使用作业 raise_events 属性来激活这个事件；总是激活状态）|
|Job_run_completed|作业运行或者失败、或者成功或者停止|
|Job_sch_lim_reached|达到作业调用 limit。作业未开始，因为启动作业延迟超过了作业属性 schedule_limit 定义值|
|Job_started|作业启动了|
|Job_stopped|调用 STOP_JOB 停止了作业|
|Job_succeeded|作业成功完成|


  1. ADD_JOB_EMAIL_NOTIFICATION 过程



为作业添加一个邮件通知。无论哪个指定的作业状态事件触发，邮件会发送到指定链表中的接收端。

语法如下：

```
PROCEDURE ADD_JOB_EMAIL_NOTIFICATION(
	JOB_NAME	IN		VARCHAR,
	RECIPIENTS	IN		VARCHAR,
	SENDER		IN		VARCHAR DEFAULT NULL,
	SUBJECT		IN		VARCHAR DEFAULT DBMS_SCHEDULER.DEFAULT_NOTIFICATION_SUBJECT,
	BODY		IN		VARCHAR DEFAULT DBMS_SCHEDULER.DEFAULT_NOTIFICATION_BODY,
	EVENTS		IN 		VARCHAR DEFAULT 'JOB_FAILED,JOB_BROKEN,JOB_SCH_LIM_REACHED,JOB_CHAIN_STALED,JOB_OVER_MAX_DUR',
	FILTER_CONDITION	IN		VARCHAR DEFAULT NULL
);
```

参数详解

  * JOB_NAME

添加邮件通知的目标作业名称，不能为 NULL。

  * Recipients

逗号隔开的接收端的 EMAIL 地址链表。列出的所有邮件通知都会发送到所有的接收端地址。不能为 NULL。

  * Sender

发送端的 EMAIL 地址。若为 NULL 或者忽略，则使用全局调度属性中的 email_sender。

  * Subject

邮件主题。目前仅支持默认主题：



```
DamengScheduler Job Notification - %job_owner%.%job_name% %event_type%
```

其中，% 中间代表是要插入的变量值。

  * Body

邮件内容。目前仅支持默认邮件内容格式：



```
 'Job: %job_owner%.%job_name%
 Event: %event_type%
 Date: %event_timestamp%
 Log id: %log_id%
 Run count: %run_count%
 Failure count: %failure_count%
 Retry count: %retry_count%
 Error code: %error_code%
 Error message:%error_message%'
```

```
其中，%中间代表是要插入的变量值。
```

  * Events

以逗号隔开的发送邮件通知的作业状态事件。不能为 NULL。列表中任何一个事件触发都会向接收端发送通知。如果忽略，则以下默认事件发送通知：



```
JOB_FAILED、JOB_BROKEN、JOB_SCH_LIM_REACHED、JOB_CHAIN_STALLED、JOB_OVER_MAX_DUR。
```

  * Filter_condition

用于过程要发送邮件通知的事件。如果为 NULL，所有指定事件发生时机都会产生一个邮件通知并发送。Filter_condition 必须是一个布尔类型 SQL  
WHERE 表达式，引用:event 绑定变量。绑定变量自动绑定到代表抛出事件的对象上（实现中并未支持，仅作兼容处理）。

例如，过滤条件为：



```
:event.error_code=600 或者 :error.error_code=700
```

  2. REMOVE_JOB_EMAIL_NOTIFICATION 过程



移除作业的邮件通知。可以移除所有的邮件通知，或者仅移除指定接收者或指定事件的邮件通知。执行作业删除时，会将作业上所有的邮件通知移除。

语法如下：

```
PROCEDURE REMOVE_JOB_EMAIL_NOTIFICATION(
	JOB_NAMEIN				VARCHAR,
	RECIPIENTS		IN		VARCHAR		DEFAULT NULL,
	EVENTS			IN		VARCHAR		DEFAULT NULL
);
```

参数详解

  * JOB_NAME

移除邮件通知的目标作业。不能为 NULL。

  * recipients

逗号隔开的要移除满足配置指定接收者的邮件通知。若为 NULL，则移除邮件通知时，忽略接口端的过滤。

  * Events

逗号隔开的要移除满足配置直指定事件的邮件通知。若为 NULL，则移除邮件通知时，忽略指定事件的过滤。



### 32.1.6 其他

  1. GENERATE_JOB_NAME 函数



返回一个唯一的作业名称。名称格式为{前缀}N，其中 N 是从一个序列获取的数字。若未指定前缀，则默认产生名字为 JOB$_1、JOB$_2、JOB$_3 等。若设置前缀为’DM’，则生成 JOB 名称为 DM1、DM2、DM3 等。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.GENERATE_JOB_NAME(
	PREFIX		IN		VARCHAR		DEFAULT 'JOB$_'
)RETURN VARCHAR;
```

参数详解

  * PREFIX

生成作业名的前缀



  2. EVALUATE_CALENDAR_STRING 过程



计算日历字符串。

语法如下：

```
PROCEDURE DBMS_SCHEDULER.EVALUATE_CALENDAR_STRING (
	CALENDAR_STRING			IN		VARCHAR,
	START_DATE				IN		TIMESTAMP WITH TIME ZONE,
	RETURN_DATE_AFTER		IN		TIMESTAMP WITH TIME ZONE,
	NEXT_RUN_DATE			OUT		TIMESTAMP WITH TIME ZONE
);
```

参数详解

  * CALENDAR_STRING

待计算的日历格式字符串。必须是按照日历语法。

  * START_DATE

日历字符串有效的起始日期和时间。若指定为 NULL，则起始时间为当前时间。

  * RETURN_DATE_AFTER

使用 START_DATE 和
CALENDAR_STRING，调度就有足够的信息来决定所有有效执行日期。设置这个参数，调度知道匹配哪个可能的时间退出。若未设置，则系统直接使用系统时间戳。

  * NEXT_RUN_DATE

匹配 CALENDAR_STRING 和 START_DATE，并在 RETURN_DATE_AFTER 参数值之后发生的第一个时间戳。



  3. SET_ATTRIBUTE 过程



设置 SCHEDULER 对象的一个属性，目前仅支持 JOB 对象的属性设置。

语法如下：

```
PROCEDURE SET_ATTRIBUTE(
	NAME                 IN		VARCHAR,
	ATTRIBUTE            IN		VARCHAR,
	VALUE                IN		{BOOLEAN | 
								VARCHAR | 
								TIMESTAMP |
								TIMESTAMP WITH TIME ZONE |
								INTERVAL DAY TO SECOND}
);
```

参数详解

  * NAME

设置的对象名称。目前仅支持对 JOB 对象属性设置。

  * ATTRIBUTE

属性名称。

    * JOB 对象支持的属性有以下属性：
    * auto_drop：作业是否自动删除

如果为 TRUE，作业自动删除。默认为 TRUE。自动删除的条件如下：

1）作业的结束日期已经过期

2）作业不是重复执行作业，并且只执行一次

    * program_name：作业执行的程序名称，若 JOB 对象设置了 JOB_TYPE、JOB_ACTION、NUMBER_OF_ARGUMENTS，则不允许再设置 PROGRAM_NAME 属性。
    * schedule_name：作业所使用的调度名称，若 JOB 对象设置了 START_DATE、END_DATE、REPEAT_INTERVL 属性，则不允许再设置 SCHEDULE_NAME 属性。
    * repeat_interval：作业执行的重复/间隔规则，仅支持日历语法，详见[日历语法](#33.4 日历语法)章节。若 JOB 对象设置了 SCHEDULE_NAME 属性，则不允许设置该属性。
    * end_date：作业截止日期。若 JOB 对象设置了 SCHEDULE_NAME 属性，则不允许设置该属性。
    * start_date：作业起始日期。若 JOB 对象设置了 SCHEDULE_NAME 属性，则不允许设置该属性。







  * 以下属性仅作兼容，未实现功能：



```
restartable
allow_runs_in_restricted_mode
follow_default_timezone
instance_stickiness
parallel_instances
stop_on_windows_close
instance_id
max_failure
max_runs
job_priority
logging_level
credential_name
database_role
destination
destination_name
event_spec
job_class
job_weight
raise_events
schedule_limit
```

  * VALUE

设置的属性值。



  4. PURGE_LOG 过程



清理日志。

语法如下：

```
PROCEDURE PURGE_LOG (
	LOG_HISTORY		IN		INT			DEFAULT 0,
	WHICH_LOG		IN		VARCHAR		DEFAULT 'JOB_AND_WINDOW_LOG',
	JOB_NAME		IN		VARCHAR		DEFAULT NULL
)
```

参数详解

  * LOG_HISTORY

历史记录保留天数。取值范围 0-999，如果为 0，则清空历史记录。

  * WHICH_LOG

指定清理的日志类型，不支持，仅作兼容参数用。注：DBMS_SCHEDULER 日志只支持 JOB_LOG 类型日志，WHICH_LOG
指定与否，PURGE_LOG 均删除作业日志。

  * JOB_NAME

指定清理日志历史记录的作业名称。



  5. DISABLE 过程



使指定的对象失效。目前仅支持 PROGRAM 和 JOB。

语法如下：

```
PROCEDURE DISABLE (
	NAME              	IN		VARCHAR,
	FORCE             	IN		BOOLEAN		DEFAULT FALSE,
	COMMIT_SEMANTICS  	IN		VARCHAR		DEFAULT 'STOP_ON_FIRST_ERROR'
)
```

参数详解

  * NAME

DISABLE 对象名称。目前仅支持 JOB 和 PROGRAM

  * FORCE

是否强制 DISABLE 对象。参数不支持，仅作兼容用，目前处理都为强制 DISABLE

  * COMMIT_SEMANTICS

不支持，仅作兼容用。



  6. ENABLE 过程



使指定的对象生效。目前仅支持 PROGRAM 和 JOB。

语法如下：

```
PROCEDURE ENABLE(
	NAME             		IN 		VARCHAR,
	COMMIT_SEMANTICS  		IN 		VARCHAR 		DEFAULT 'STOP_ON_FIRST_ERROR'
)
```

参数详解

  * NAME

ENABLE 对象名称。目前仅支持 JOB 和 PROGRAM。

作业 ENABLE 时，如果作业所使用的 PROGRAM 无效，作业创建失败。

  * COMMIT_SEMANTICS

不支持，仅作兼容用。



## 32.2 创建、删除语句

创建或删除 DBMS_SCHEDULER 系统包。

语法如下：

```
void
SP_INIT_DBMS_SCHEDULER_SYS (
	CREATE_FLAG		INT
)
```

参数详解

  * CREATE_FLAG

为 1 时表示创建 DBMS_SCHEDULER 包；为 0 表示删除该系统包。



返回值

无

举例说明

创建 DBMS_SCHEDULER 系统包。

```
SP_INIT_DBMS_SCHEDULER_SYS (1);
```

## 32.3 相关视图

MPP 环境下，作业只在节点号最小的节点上执行。所有视图，都只显示本站点的相关信息。

  1. DBA_SCHEDULER_PROGRAMS



记录 PROGRAM 对象信息。

|**序号**|**列**|**说明**|
|--|--|--|
|1|OWNER|PROGRAM 所有者|
|2|PROGRAM_NAME|PROGRAM 名称|
|3|PROGRAM_TYPE|PROGRAM 类型，仅支持 PLSQL_BLOCK 和 STORED_PROCEDURE 类型|
|4|PROGRAM_ACTION|PROGRAM 执行的动作|
|5|NUMBER_OF_ARGUMENTS|参数个数|
|6|ENABLED|是否启用|
|7|DETACHED|不支持，仅作兼容用|
|8|SCHEDULE_LIMIT|不支持，仅作兼容用|
|9|PRIORITY|不支持，仅作兼容用|
|10|WEIGHT|不支持，仅作兼容用|
|11|MAX_RUNS|不支持，仅作兼容用|
|12|MAX_FAILURES|不支持，仅作兼容用|
|13|MAX_RUN_DURATION|不支持，仅作兼容用|
|14|NLS_ENV|不支持，仅作兼容用|
|15|COMMENTS|PROGRAM 评论或注释|


  2. DBA_SCHEDULER_PROGRAM_ARGS



记录 PROGRAM 对象的参数定义信息。

|**序号**|**列**|**说明**|
|--|--|--|
|1|OWNER|PROGRAM 所有者|
|2|PROGRAM_NAME|PROGRAM 名称|
|3|ARGUMENT_NAME|参数名称|
|4|ARGUMENT_POSITION|当前参数在参数列表中所在位置|
|5|ARGUMENT_TYPE|参数的数据类型|
|6|METADATA_ATTRIBUTE|不支持，仅作兼容用|
|7|DEFAULT_VALUE|参数默认值，字符串格式|
|8|DEFAULT_ANYDATA_VALUE|不支持，仅作兼容用|
|9|OUT_ARGUMENT|是否为输出参数|


  3. DBA_SCHEDULER_SCHEDULES



记录 SCHEDULE 对象信息。

|**序号**|**列**|**说明**|
|--|--|--|
|1|OWNER|调度的所有者|
|2|SCHEDULE_NAME|调度名称|
|3|SCHEDULE_TYPE|调度类型，取值 ONCE,CALENDAR|
|4|START_DATE|起始时间|
|5|REPEAT_INTERVAL|调度的重复/间隔规则|
|6|EVENT_QUEUE_OWNER|不支持，仅作兼容用|
|7|EVENT_QUEUE_NAME|不支持，仅作兼容用|
|8|EVENT_QUEUE_AGENT|不支持，仅作兼容用|
|9|EVENT_CONDITION|不支持，仅作兼容用|
|10|FILE_WATCHER_OWNER|不支持，仅作兼容用|
|11|FILE_WATCHER_NAME|不支持，仅作兼容用|
|12|END_DATE|结束时间|
|13|COMMENTS|调度的评论或注释|


  4. DBA_SCHEDULER_JOBS



记录 JOB 对象信息。

|**序号**|**列**|**说明**|
|--|--|--|
|1|OWNER|作业的所有者|
|2|JOB_NAME|作业名称|
|3|JOB_SUBNAME|作业子名称，不支持，仅作兼容用|
|4|JOB_STYLE|作业风格，仅支持 REGULAR|
|5|JOB_CREATOR|作业原始的创建者|
|6|CLIENT_ID|不支持，仅作兼容用|
|7|GLOBAL_UID|不支持，仅作兼容用|
|8|PROGRAM_OWNER|和作业关联的 PROGRAM 的所有者|
|9|PROGRAM_NAME|和作业关联的 PROGRAM 的名称|
|10|JOB_TYPE|作业类型，取值 PLSQL_BLOCK，STORED_PROCEDURE， 不支持 EXECUTABLE 和 CHAIN|
|11|JOB_ACTION|作业执行的动作|
|12|NUMBER_OF_ARGUMENTS|作业参数个数|
|13|SCHEDULE_OWNER|调度的所有者|
|14|SCHEDULE_NAME|和作业相关联的调度名称|
|15|SCHEDULE_TYPE|调度的类型|
|16|START_DATE|作业起始时间|
|17|REPEAT_INTERVAL|作业的重复/间隔规则|
|18|EVENT_QUEUE_OWNER|不支持，仅作兼容用|
|19|EVENT_QUEUE_NAME|不支持，仅作兼容用|
|20|EVENT_QUEUE_AGENT|不支持，仅作兼容用|
|21|EVENT_CONDITION|不支持，仅作兼容用|
|22|EVENT_RULE|不支持，仅作兼容用|
|23|FILE_WATCHER_OWNER|不支持，仅作兼容用|
|24|FILE_WATCHER_NAME|不支持，仅作兼容用|
|25|END_DATE|作业对束时间|
|26|JOB_CLASS|不支持，仅作兼容用|
|27|ENABLED|作业是否启用|
|28|AUTO_DROP|作业结束时是否自动删除|
|29|RESTARTABLE|作业执行失败是否重新启动。不支持，仅作兼容用|
|30|STATE|当前作业的状态|
|31|JOB_PRIORITY|不支持，仅作兼容用|
|32|RUN_COUNT|不支持，仅作兼容用|
|33|MAX_RUNS|不支持，仅作兼容用|
|34|FAILURE_COUNT|不支持，仅作兼容用|
|35|MAX_FAILURES|不支持，仅作兼容用|
|36|RETRY_COUNT|不支持，仅作兼容用|
|37|LAST_START_DATE|不支持，仅作兼容用|
|38|LAST_RUN_DURATION|不支持，仅作兼容用|
|39|NEXT_RUN_DATE|作业下一次执行时间|
|40|SCHEDULE_LIMIT|不支持，仅作兼容用|
|41|MAX_RUN_DURATION|作业执行的最大持续时间|
|42|LOGGING_LEVEL|不支持，仅作兼容用|
|43|STOP_ON_WINDOW_CLOSE|不支持，仅作兼容用|
|44|INSTANCE_STICKINESS|不支持，仅作兼容用|
|45|RAISE_EVENTS|不支持，仅作兼容用|
|46|SYSTEM|不支持，仅作兼容用|
|47|JOB_WEIGHT|不支持，仅作兼容用|
|48|NLS_ENV|不支持，仅作兼容用|
|49|SOURCE|不支持，仅作兼容用|
|50|NUMBER_OF_DESTINATIONS|不支持，仅作兼容用|
|51|DESTINATION_OWNER|不支持，仅作兼容用|
|52|DESTINATION|不支持，仅作兼容用|
|53|CREDENTIAL_OWNER|不支持，仅作兼容用|
|54|CREDENTIAL_NAME|不支持，仅作兼容用|
|55|INSTANCE_ID|不支持，仅作兼容用|
|56|DEFERRED_DROP|不支持，仅作兼容用|
|57|ALLOW_RUNS_IN_RESTRICTED_MODE|不支持，仅作兼容用|
|58|COMMENTS|作业的评论或注释|
|59|FLAGS|内部使用字段|


  5. DBA_SCHEDULER_JOB_ARGS



记录 JOB 对象的参数值信息。

|**序号**|**列**|**说明**|
|--|--|--|
|1|OWNER|参数所属作业的所有者|
|2|JOB_NAME|参数所属作业名称|
|3|ARGUMENT_NAME|参数名称|
|4|ARGUMENT_POSITION|当前参数在参数列表中所在位置|
|5|ARGUMENT_TYPE|参数的数据类型|
|6|VALUE|参数值，字符串格式|
|7|ANYDATA_VALUE|其他格式的参数值|
|8|OUT_ARGUMENT|是否为输出参数|


  6. DBA_SCHEDULER_JOB_RUN_DETAILS



记录 JOB 运行过程中的详细信息。

|**序号**|**列**|**说明**|
|--|--|--|
|1|LOG_ID|日志唯一 ID|
|2|LOG_DATE|作业执行结束时间|
|3|OWNER|作业的所有者|
|4|JOB_NAME|作业名称|
|5|JOB_SUBNAME|作业子名称，不支持，仅作兼容用|
|6|STATUS|作业运行状态|
|7|ERROR#|作业出错时的错误码|
|8|REQ_START_DATE|作业请求运行时间，不支持，仅作兼容用|
|9|ACTUAL_START_DATE|作业实际执行起始时间|
|10|RUN_DURATION|作业执行时长|
|11|INSTANCE_ID|不支持，仅作兼容用|
|12|SESSION_ID|作业执行所在会话 ID|
|13|SLAVE_PID|不支持，仅作兼容用|
|14|CPU_USED|不支持，仅作兼容用|
|15|CREDENTIAL_OWNER|不支持，仅作兼容用|
|16|CREDENTIAL_NAME|不支持，仅作兼容用|
|17|DESTINATION_OWNER|不支持，仅作兼容用|
|18|DESTINATION|不支持，仅作兼容用|
|19|ADDITIONAL_INFO|作业运行过程中相关信息|


  7. DBA_SCHEDULER_JOB_LOG



记录作业日志信息。

|**序号**|**列**|**说明**|
|--|--|--|
|1|LOG_ID|日志唯一 ID|
|2|LOG_DATE|日志记录的日期|
|3|OWNER|作业的所有者|
|4|JOB_NAME|作业名称|
|5|JOB_SUBNAME|作业子名称，不支持，仅作兼容用|
|6|JOB_CLASS|不支持，仅作兼容用|
|7|OPERATION|不支持，仅作兼容用|
|8|STATUS|作业运行状态|
|9|USER_NAME|用户名|
|10|CLIENT_ID|不支持，仅作兼容用|
|11|GLOBAL_UID|不支持，仅作兼容用|
|12|CREDENTIAL_OWNER|不支持，仅作兼容用|
|13|CREDENTIAL_NAME|不支持，仅作兼容用|
|14|DESTINATION_OWNER|不支持，仅作兼容用|
|14|DESTINATION|不支持，仅作兼容用|
|16|ADDITIONAL_INFO|作业运行过程中相关信息|


  8. DBA_SCHEDULER_GLOBAL_ATTRIBUTE



记录 SCHEDULER 包的全局属性。

|**序号**|**列**|**说明**|
|--|--|--|
|1|ATTRIBUTE_NAME|属性名称，不包括 EMAIL_PASSWORD|
|2|VALUE|属性值|


  9. DBA_SCHEDULER_RUNNING_JOBS



描述数据库中由 DBMS_SCHEDULER 包创建的且正在执行的作业。

|**序号**|**列**|**说明**|
|--|--|--|
|1|OWNER|作业所有者|
|2|JOB_NAME|作业名称|
|3|JOB_SUBNAME|作业子名称，不支持，仅作兼容用|
|4|JOB_STYLE|作业风格，仅支持 REGULAR|
|5|DETACHED|不支持，仅作兼容用|
|6|SESSION_ID|作业执行的会话 ID|
|7|SLAVE_PROCESS_ID|不支持，仅作兼容用|
|8|SLAVE_OS_PROCESS_ID|不支持，仅作兼容用|
|9|RUNNING_INSTANCE|执行作业的实例号|
|10|RESOURCE_CONSUMER_GROUP|不支持，仅作兼容用|
|11|ELAPSED_TIME|作业已经执行的时长|
|12|CPU_USED|不支持，仅作兼容用|
|13|DESTINATION_OWNER|不支持，仅作兼容用|
|14|DESTINATION|不支持，仅作兼容用|
|15|CREDENTIAL_OWNER|不支持，仅作兼容用|
|16|CREDENTIAL_NAME|不支持，仅作兼容用|
|17|LOG_ID|不支持，仅作兼容用|


  10. DBA_SCHEDULER_RUNNING_JOBS



描述当前用户拥有的由 DBMS_SCHEDULER 包创建的且正在执行的作业，结构与 DBA_SCHEULER_RUNNING_JOBS 相同。

## 32.4 日历语法

### 32.4.1 日历语法格式

DM 支持的日历语法格式：

```
repeat_interval：：= 
<frequency子句>[;<interval子句>] [;<bymonth子句>] [;<byweekno子句>][;<byyearday子句>] [;<bymonthday子句>][;<byday子句>] [;<byhour子句>] [;<byminute子句>][;<bysecond子句>] 

<frequency子句>::= FREQ = <predefined_frequency>

<predefined_frequency>:: = YEARLY | MONTHLY | WEEKLY | DAILY |  HOURLY | MINUTELY | SECONDLY
<interval子句> ::= INTERVAL = <intervalnum>
<intervalnum> ::= <取值1到99>
<bymonth子句> ::= BYMONTH = <monthlist>
<monthlist> ::= <month>{,<month>}
<month> ::= <numeric_month> | <char_month>
<numeric_month> ::= 1 | 2 | 3 ...  |12
<char_month> ::= JAN | FEB | MAR | APR | MAY | JUN | JUL | AUG | SEP | OCT | NOV | DEC

<byweekno子句>::= BYWEEKNO = <weeknumber_list>
<weeknumber_list> ::= <weeknumber>{,<weeknumber>}
<weeknumber> ::= [<minus>] <weekno>
<weekno> ::= <取值1到53>

<byyearday子句> ::= BYYEARDAY = <yearday_list>
<yearday_list> ::= <yearday>{, <yearday>}
<yearday> ::= [<minus>] <yeardaynum>
<yeardaynum> ::= <取值1到366>

<bymonthday子句> ::= BYMONTHDAY = <monthday_list>
<monthday_list> ::= <monthday>{, <monthday>}
<monthday> ::= [<minus>] <monthdaynum>
<monthdaynum> ::= <取值1到31>

<byday子句> ::= BYDAY = <byday_list>
<byday_list> ::= <byday>{,<byday>}
<byday> ::= [<weekdaynum>] <day>
<weekdaynum> ::= [<minus>] <daynum>
<daynum> ::= <取值1到53> /* 当frequency 为 yearly */
<daynum> ::= <取值1到5> /* 当frequency 为 monthly */
<day> ::= MON | TUE | WED | THU | FRI | SAT | SUN

<byhour子句> ::= BYHOUR = <hour_list>
<hour_list> ::= <hour>{, <hour>}
<hour> ::= <取值0到23>

<byminute子句> ::= BYMINUTE = <minute_list>
<minute_list> ::= <minute>{, <minute>}
<minute> := <取值0到59>

<bysecond子句> ::= BYSECOND = <second_list>
<second_list> ::= <second>{, <second>}
<second> ::= <取值0到59>

<minus> ::= -
```

参数详解

  * \<minus> 为负数（-），表示倒数。
  * \<byweekno> 只有在 freq=yearly 时有效。
  * \<daynum> freq=yearly 时，\<byday>里面\<daynum>范围为 1-53；freq=monthly 时，\<byday>里面\<daynum>范围为 1-5。
  * SECONDLY，\<bysecond 子句> ，在 DBMS_SCHEDULER 包中进行秒级设置需要把 INI 参数 TIMER_TRIG_CHECK_INTERVAL 置为 1。



举几个简单的日历写法。例如：

1）每年的第 1 天执行：freq=yearly;byyearday=1；

2）每月的最后一天执行一次：freq=monthly;bymonthday=-1；

3）每月 1， 3， 5 号执行：freq=monthly;bymonthday=1,3,5；

4）每月第一周，周 1 执行： freq=monthly;byday=1MON；

5）每天执行一次，12 点执行：freq=daily;byhour=12

### 32.4.2 版本升级

达梦数据库产品注重向下兼容性，虽然版本不断地更新升级，但在绝大多数情况下，使用高版本的执行码启动低版本的数据库时，会自动执行一系列更新升级动作。对于待升级的数据库，用户只需要保证它之前是正常退出的即可。

在数据库升级操作前，建议先进行数据备份。

需要注意的是，从 V7.6.1.67 版本和 V8.1.1.107 开始，对日历表达式触发器进行了改进。使得老版本升级后的新版本需要进行额外的升级操作，下面进行详细的说明。

#### 32.4.2.1 日历表达式触发器升级

日历表达式触发器就是使用了“日历语法”的时间触发器。主要应用于 DBMS_SCHEDULER 包中。当使用 PROCEDURE DBMS_SCHEDULER.CREATE_JOB 创建 JOB 时，只要使用了 REPEAT_INTERVAL 参数，按照日历语法格式指定了调度重复间隔，那么系统内部就会自动生成一个日历表达式触发器。
老版本中，触发器日历表达式 encode 到 sysobjects 视图 info6 字段时，指针偏移多移动了 31 字节，decode 时也同样多移动了 31 字节，导致日历表达式涉及秒级间隔触发，部分秒数实际未触发。在 V7.6.1.67 以上的 DM7 版本和 V8.1.1.107 以上的 DM8 版本中，此问题已被修正，均不存在此问题。

##### 32.4.2.1.1 版本说明

DM7 支持从 DM7 老版本升级到 V7.6.1.67 以上版本，DM8 支持从 DM8 老版本升级到 V8.1.1.107 以上版本。但是，从老版本升级的新版本，需要手动重新创建日历触发器，使 sysobjects 视图 info6 字段重新填写，来修正上述问题。
非日历表达式触发器无此问题，无需修正。

##### 32.4.2.1.2 升级步骤

待升级的日志表达式触发器分为两种情况：一种是 DBMS_SCHEDULER 包相关，另一种是非 DBMS_SCHEDULER 包相关。下面详细介绍如下：

  1. DBMS_SCHEDULER 包相关



由 DBMS_SCHEDULER 包创建的作业，内部会生成日历表达式触发器，对于仍然有效的日历表达式触发器，只需要重新 enable 该作业，系统内部会重建该触发器。步骤如下：

1）查询系统视图，获取预期仍然有效的作业名称。

```
SQL> SELECT JOB_NAME,ENABLED FROM DBA_SCHEDULER_JOBS WHERE ENABLED='TRUE';
//查询结果如下：
行号       JOB_NAME   ENABLED
---------- -------- -------
1	         TEST_JOB   TRUE
```

2）重新生成作业的触发器。

```
begin
dbms_scheduler.enable(name=>'TEST_JOB');
end;
/
```

3）至此，升级完成。

  2. 非 DBMS_SCHEDULER 包相关



其它非 DBMS_SCHEDULER 包相关的日历表达式触发器，可以通过查询系统表或 MANAGER 工具获取触发器 DDL 创建脚本，然后重新执行一遍 DDL 脚本即可，以此来完成升级工作。

## 32.5 举例说明

使用包内的过程和函数之前，如果还未创建过系统包。请先调用系统过程创建系统包。

```
SP_INIT_DBMS_SCHEDULER_SYS(1);
```

准备工作：

```
drop table test cascade;
create table test(c1 varchar, c2 varchar);
create or replace procedure proc1(c1 varchar, c2 varchar)
as
begin
insert into test values(c1, c2);
commit;
end;
/
```

创建 PROGRAM：

```
begin
dbms_scheduler.create_program(
program_name=>'PROG1',
program_type=>'STORED_PROCEDURE',
program_action=>'PROC1',
number_of_arguments=>2);
end;
/
```

定义 program 参数：

```
begin
dbms_scheduler.define_program_argument(
program_name=>'PROG1',
argument_position=>1,
argument_name=>'C1',
argument_type=>'VARCHAR'
);
end;
/

begin
dbms_scheduler.define_program_argument(
program_name=>'PROG1',
argument_position=>2,
argument_name=>'C2',
argument_type=>'VARCHAR'
);
end;
/
```

设置 PROGRAM 为 ENABLE 状态：

```
begin
dbms_scheduler.enable('PROG1');
end;
/
```

创建作业：

```
begin
dbms_scheduler.create_job(
job_name=>'test_job',
program_name=>'PROG1',
start_date=>'2024-09-01 8:55:00',
repeat_interval=>'FREQ=MINUTELY;INTERVAL=1',
end_date=>'2030-09-01 9:15:00',
enabled=>FALSE,
auto_drop=>FALSE,
comments=>'test_job',
job_style=>'REGULAR'
);
end;
/
```

设置作业参数值：

```
begin
dbms_scheduler.set_job_argument_value(
job_name=>'test_job',
argument_position=>1,
argument_value=>'abc'
);
end;
/
begin
dbms_scheduler.set_job_argument_value(
job_name=>'test_job',
argument_name=>'C2',
argument_value=>'abc'
);
end;
/
```

配置全局调度属性：

```
begin
DBMS_SCHEDULER.SET_SCHEDULER_ATTRIBUTE(
	'email_server',
	'192.168.0.212');
end;
/

begin
DBMS_SCHEDULER.SET_SCHEDULER_ATTRIBUTE(
	'email_sender',
	'shenning@dameng.shanghai');
end;
/

begin
DBMS_SCHEDULER.SET_SCHEDULER_ATTRIBUTE(
	'email_port',
	'25');
end;
/

begin
DBMS_SCHEDULER.SET_SCHEDULER_ATTRIBUTE(
	'email_password',
	'888888');
end;
/

begin
DBMS_SCHEDULER.SET_SCHEDULER_ATTRIBUTE(
	'email_mimetype',
	'TEXT/PLAIN; CHARSET=GB18030');
end;
/
```

添加作业邮件通知：

```
BEGIN
DBMS_SCHEDULER.ADD_JOB_EMAIL_NOTIFICATION(
	Job_name => 'test_job',
	Recipients => 'shenning@dameng.shanghai',
    Sender   => 'shenning@dameng.shanghai',  
events	 => 'JOB_ALL_EVENTS');
END;
/

//设置作业为ENABLE
begin
dbms_scheduler.enable(name=>'test_job');
end;
/
```

删除作业邮件通知：

```
BEGIN
DBMS_SCHEDULER.REMOVE_JOB_EMAIL_NOTIFICATION(
	Job_name => 'test_job');
END;
/
```

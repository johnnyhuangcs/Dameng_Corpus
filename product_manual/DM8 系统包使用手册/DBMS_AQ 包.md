

# DBMS_AQ 包

DBMS_AQ 包提供高级队列的以下常用功能：消息入队出队，注册、注销通知等。

## 38.1 使用前提

DBMS_AQ 包的使用依赖 DBMS_AQADM 和 DBMS_RANDOM。所以，在创建 DBMS_AQ 包之前，请先成功创建 DBMS_AQADM 和 DBMS_RANDOM。

## 38.2 数据类型

  1. SYS.AQ$_RECIPIENT_LIST_T



系统对象类型，用于描述订阅者信息组。

语法如下：

```
TYPE SYS.AQ$_RECIPIENT_LIST_T IS TABLE OF SYS.AQ\$_AGENT INDEX BY INTEGER;
```

  2. SYS.AQ$_REG_INFO



系统对象类型，用于描述注册信息。

语法如下：

```
TYPE SYS.AQ$_REG_INFO AS OBJECT(
	NAME				VARCHAR(128),   
	NAMESPACE           	NUMBER,   
	CALLBACK            	VARCHAR(256),   
	CTX                 	VARBINARY(2000),
	ANYCTX              	"<ADT_1>",  
	CTXTYPE             	NUMBER,   
	QOSFLAGS            	NUMBER,   
	PAYLOADCBK          	VARCHAR(4000),  
	TIMEOUT             	NUMBER   
);
```

参数详解

  * NAME 指定注册信息，格式为：[模式名.]队列名:订阅者。非正规表示符要用""括起来，如 SYSDBA."@que_name":sub_name。
  * NAMESPACE 指定订阅者命名空间，取值包括 NAMESPACE_ANONYMOUS 或 NAMESPACE_AQ。仅做语法支持。
  * CALLBACK 指定回调过程名，格式为：PLSQL://过程名。
  * 剩余参数仅做语法支持。



  3. SYS.AQ$_REG_INFO_LIST



系统对象类型，用于描述注册信息组。

语法如下：

```
TYPE SYS.AQ$_REG_INFO_LIST AS VARRAY(1024) OF SYS.AQ$_REG_INFO;
```

  4. SYS.AQ$_DESCRIPTOR



系统对象类型，用于描述回调过程参数。

语法如下：

```
TYPE SYS.AQ$_DESCRIPTOR AS OBJEC (
	QUEUE_NAME        		VARCHAR(65), 
	CONSUMER_NAME     		VARCHAR(30), 
	MSG_ID            		VARBINARY(16)
);
```

参数详解

  * queue_name 指定触发回调的队列名，格式为：模式名.队列名。
  * CONSUMER_NAME 指定触发回调的订阅者名。
  * MSG_ID 为触发回调的消息 id。



  5. DBMS_AQ. ENQUEUE_OPTIONS_T



DBMS_AQ 包专有类型，指定入队选项。

语法如下：

```
TYPE ENQUEUE_OPTIONS_T IS RECORD (
	VISIBILITY              	INTEGER 			DEFAULT ON_COMMIT,  
	RELATIVE_MSGID          	VARBINARY(16) 		DEFAULT NULL,
	SEQUENCE_DEVIATION			INTEGER 			DEFAULT NULL
);
```

参数详解

  * 参数仅做语法支持。



  6. DBMS_AQ.DEQUEUE_OPTIONS_T



DBMS_AQ 包专有类型，指定出队选项。

语法如下：

```
TYPE DEQUEUE_OPTIONS_T IS RECORD (
	CONSUMER_NAME       		VARCHAR(30) 		DEFAULT NULL, 
	DEQUEUE_MODE       			INTEGER 			DEFAULT REMOVE,  
	NAVIGATION         			INTEGER 			DEFAULT NEXT_MESSAGE,  
	VISIBILITY         			INTEGER 			DEFAULT ON_COMMIT,   
	WAIT             			INTEGER 			DEFAULT FOREVER,  
	MSGID                		VARBINARY(16) 		DEFAULT NULL  
);
```

参数详解

  * DEQUEUE_MODE 指定出队模式，取值包括：BROWSE（直接读取消息）、REMOVE（读取后删除消息）。
  * WAIT 指定没有消息时的等待时间（秒），值 FOREVER 和 NO_WAIT 分别表示一直等待和不等待。
  * 剩余参数仅做语法支持。



  7. DBMS_AQ. MESSAGE_PROPERTIES_T



DBMS_AQ 包专有类型，指定消息属性。

语法如下：

```
TYPE MESSAGE_PROPERTIES_T IS RECORD (
	PRIORITY          		INTEGER 		NOT NULL 	DEFAULT 1, 
	DELAY              		INTEGER 		NOT NULL 	DEFAULT 0, 
	EXPIRATION        		INTEGER 		NOT NULL 	DEFAULT -1,
	ATTEMPTS        			INTEGER,              
	RECIPIENT_LIST  			SYS.AQ$_RECIPIENT_LIST_T,   
	EXCEPTION_QUEUE   		VARCHAR(61) 			DEFAULT NULL,   
	STATE                		INTEGER                     
);
```

参数详解

  * 参数仅做语法支持。



  8. SYS.AQ$_SIG_PROP



用于标识或配置与队列消息相关的信号或属性。

语法如下：

```
TYPE SYS.AQ$_SIG_PROP AS OBJECT (
    SIGNATURE   RAW(2000),
    CANALGO    	 VARCHAR2(2000),
    DIGALGO    	 VARCHAR2(2000),
    SIGALGO    	 VARCHAR2(2000),
    CERTIFICATE	 VARCHAR2(2000),
    DIGVAL      	RAW(2000)
);
```

参数详解

  * 参数仅做语法支持。



## 38.3 相关方法

  1. REGISTER



注册订阅者和回调信息。

语法如下：

```
PROCEDURE REGISTER(
	REG_LIST		IN  		SYS.AQ$_REG_INFO_LIST
	REG_COUNT		IN  		NUMBER  
);
```

参数详解

  * REG_LIST 指定注册信息数组。参考 38.2 数据类型。
  * REG_COUNT 指定 REG_LIST 中的注册信息个数。



  2. UNREGISTER



注销订阅者和回调信息。

语法如下：

```
PROCEDURE UNREGISTER(
	REG_LIST   		IN  		SYS.AQ$_REG_INFO_LIST
	REG_COUNT      	IN  		NUMBER  
);
```

参数详解

  * REG_LIST 指定注销信息数组。参考 38.2 数据类型。
  * REG_COUNT 指定 REG_LIST 中的注销信息个数。



  3. ENQUEUE



消息入队，若队列注册过订阅者和回调信息，则入队消息后会尝试为每个订阅者执行其注册的回调过程。

语法如下：

```
PROCEDURE ENQUEUE(
	QUEUE_NAME          	IN  		VARCHAR,   
	ENQUEUE_OPTIONS     	IN  		ENQUEUE_OPTIONS_T,   
	MESSAGE_PROPERTIES  	IN  		MESSAGE_PROPERTIES_T,
	PAYLOAD             	IN  		"<ADT_1>",   
	MSGID               	OUT 		VARBINARY  
);
```

参数详解

  * QUEUE_NAME 指定要入队的队列名，格式为：[模式名.]队列名。
  * ENQUEUE_OPTIONS 指定入队选项，仅做语法支持。参考 38.2 数据类型。
  * MESSAGE_PROPERTIES 指定消息属性，仅做语法支持。参考 38.2 数据类型。
  * PAYLOAD 指定消息载荷，必须与创建队列表指定的消息载荷类型对应。
  * MSGID 输出生成的入队消息 id。



  4. DEQUEUE



消息出队，队列中存在多条消息时总是优先出队第一条。

语法如下：

```
PROCEDURE DEQUEUE(
	QUEUE_NAME          	IN  		VARCHAR,   
	DEQUEUE_OPTIONS     	IN  		DEQUEUE_OPTIONS_T,  
	MESSAGE_PROPERTIES  	OUT 		MESSAGE_PROPERTIES_T,
	PAYLOAD             	OUT 		"<ADT_1>", 
	MSGID               	OUT 		VARBINARY 
);
```

参数详解

  * QUEUE_NAME 指定要出队的队列名，格式为：[模式名.]队列名。
  * DEQUEUE_OPTIONS 指定出队选项。参考 38.2 数据类型。
  * MESSAGE_PROPERTIES 指定消息属性，仅做语法支持。参考 38.2 数据类型。
  * PAYLOAD 输出消息载荷，必须与创建队列表指定的消息载荷类型对应。
  * MSGID 输出消息 id。



## 38.4 回调说明

每个订阅者可注册多个回调过程，但消息入队时最多仅触发一个回调。触发的回调过程必须有效，与队列处于同一模式，且参数数目、类型和顺序必须满足如下定义，不符合预期的回调会跳过执行：

```
PROCEDURE CALLBACK(
	CONTEXT1          	RAW,
	REGINFO       		SYS.AQ$_REG_INFO,
	DESCR         		SYS.AQ$_DESCRIPTOR,
	PAYLOAD      		RAW,
	PAYLOADL    		NUMBER
);
```

参数详解

  * REGINFO 指定触发回调的注册信息，同过程 REGISTER 参数。参考 38.2 数据类型。
  * DESCR 指定触发回调的消息和队列信息。参考 38.2 数据类型。
  * 剩余参数仅做语法支持。



## 38.5 举例说明

使用包过程和函数之前，如果还未创建过系统包，请先调用系统过程创建。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_AQADM');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_RANDOM');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_AQ');
SET SERVEROUTPUT ON;  //RINT需要设置这条语句，才能打印出消息
```

例 1 在 37.3 节例 3 之后执行以下操作：创建回调过程 MY_CALLBACK 并注册到订阅者 MY_SUB。

```
CREATE OR REPLACE PROCEDURE SSS.MY_CALLBACK(
    CONTEXT1    RAW,
    REGINFO     SYS.AQ$_REG_INFO,
    DESCR       SYS.AQ$_DESCRIPTOR,
    PAYLOAD     RAW,
    PAYLOADL    NUMBER
) AS
    DOP DBMS_AQ.DEQUEUE_OPTIONS_T;
MSG SYSDBA.MY_MSG_TYPE;
MSGID RAW(16);
BEGIN
    DOP.DEQUEUE_MODE := DBMS_AQ.BROWSE;
    DBMS_AQ.DEQUEUE(
        QUEUE_NAME          => DESCR.QUEUE_NAME,
        DEQUEUE_OPTIONS     => DOP,
        MESSAGE_PROPERTIES  => NULL,
        PAYLOAD             => MSG,
        MSGID               => MSGID
    );
    PRINT MSG.MESSAGE;
END;
/
DBMS_AQ.REGISTER(
SYS.AQ$_REG_INFO_LIST(
SYS.AQ$_REG_INFO(
           'SSS.MY_QUEUE:MY_SUB',
           DBMS_AQ.NAMESPACE_AQ,
           'PLSQL://MY_CALLBACK',
           HEXTORAW('FF')
       )
    ),
    1);
```

例 2 消息入队，入队后会触发回调 MY_CALLBACK，从而打印入队的消息载荷。

```
DECLARE
	EOP DBMS_AQ.ENQUEUE_OPTIONS_T;
	MSG MY_MSG_TYPE;
	MSGID RAW(16);
BEGIN
	MSG := MY_MSG_TYPE(TO_CHAR(SYSTIMESTAMP, 'DD-MON-YYYY HH24:MI:SS.FF3'));
	DBMS_AQ.ENQUEUE(
		QUEUE_NAME          => 'SSS.MY_QUEUE',
		ENQUEUE_OPTIONS 	=> EOP,
		MESSAGE_PROPERTIES  => NULL,
		PAYLOAD             => MSG,
		MSGID               => MSGID
	);
	COMMIT;
END;
/
```

例 3 消息以 REMOVE 模式出队，出队后消息会被删除。

```
DECLARE
    DOP DBMS_AQ.DEQUEUE_OPTIONS_T;
MSG MY_MSG_TYPE;
MSGID RAW(16);
BEGIN
    DBMS_AQ.DEQUEUE(
QUEUE_NAME          => 'SSS.MY_QUEUE',
DEQUEUE_OPTIONS 	=> DOP,
MESSAGE_PROPERTIES  => NULL,
PAYLOAD             => MSG,
MSGID               => MSGID
    );
    PRINT MSG.MESSAGE;
END;
/
```

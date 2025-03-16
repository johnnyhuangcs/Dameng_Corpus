

# DBMS_AQADM 包

DBMS_AQADM 包提供高级队列的以下常用功能：创建、修改、删除队列表和队列，添加、删除订阅者等。

## 37.1 数据类型

  1. SYS.AQ$_AGENT



系统对象类型，用于描述订阅者信息。

语法如下：

```
TYPE SYS.AQ$_AGENT IS OBJECT (
	NAME				VARCHAR(30),  
	ADDRESS				VARCHAR(1024),
	PROTOCOL			NUMBER  
);
```

参数详解

  * NAME 指定订阅者名，非正规表示符要用""括起来，如"@@#"。
  * 剩余参数仅做语法支持。



  2. DBMS_AQADM. AQ$_PURGE_OPTIONS_T



DBMS_AQADM 包专有类型，指定清理队列表选项。

语法如下：

```
TYPE AQ$_PURGE_OPTIONS_T IS RECORD(
	BLOCK               	BOOLEAN DEFAULT FALSE,
	DELIVERY_MODE       	INTEGER DEFAULT 1
);  
```

参数详解

  * 参数仅做语法支持。



## 37.2 相关方法

  1. CREATE_QUEUE_TABLE



创建队列表。

语法如下：

```
PROCEDURE CREATE_QUEUE_TABLE(
	QUEUE_TABLE				IN 		VARCHAR,        
	QUEUE_PAYLOAD_TYPE  	IN 		VARCHAR,        
	STORAGE_CLAUSE      		IN 		VARCHAR 		DEFAULT NULL, 
	SORT_LIST           		IN 		VARCHAR 		DEFAULT NULL, 
	MULTIPLE_CONSUMERS  	IN 		BOOLEAN 		DEFAULT FALSE,
	MESSAGE_GROUPING    		IN 		INTEGER 		DEFAULT NONE, 
	TAB_COMMENT         		IN 		VARCHAR 		DEFAULT NULL, 
	AUTO_COMMIT         		IN 		BOOLEAN 		DEFAULT TRUE, 
	PRIMARY_INSTANCE    		IN 		INTEGER 		DEFAULT 0,  
	SECONDARY_INSTANCE  	IN 		INTEGER 		DEFAULT 0,  
	COMPATIBLE          		IN 		VARCHAR 		DEFAULT NULL, 
	SECURE              		IN 		BOOLEAN 		DEFAULT FALSE
);
```

参数详解

  * QUEUE_TABLE 指定要创建的队列表名，格式为：[模式名.]表名。非正规表示符要用""括起来，如"@@#".MYtab。
  * QUEUE_PAYLOAD_TYPE 指定消息载荷类型，格式为：[模式名.]数据类型名。可取值包括 RAW\VARBINARY\VARBINARY(8188)或自定义 CLASS\object 类型。
  * STORAGE_CLAUSE 指定创建队列表的存储选项，即建表语法 STORAGE()中的内容。
  * MULTIPLE_CONSUMERS 指定队列是否可以添加多个订阅者，为 TRUE 时才可使用添加订阅、注册通知和触发回调等功能。
  * TAB_COMMENT 指定队列表注释。
  * 剩余参数仅做语法支持。



  2. ALTER_QUEUE_TABLE



修改队列表。

语法如下：

```
PROCEDURE ALTER_QUEUE_TABLE (
	QUEUE_TABLE         		IN 		VARCHAR,       
	TAB_COMMENT             	IN 		VARCHAR 	DEFAULT NULL,
	PRIMARY_INSTANCE		IN 		INTEGER 		DEFAULT NULL,
	SECONDARY_INSTANCE		IN 		INTEGER 		DEFAULT NULL
);
```

参数详解

  * QUEUE_TABLE 指定要修改的队列表名，格式为：[模式名.]队列名。
  * TAB_COMMENT 指定要修改的队列表注释。
  * 剩余参数仅做语法支持。



  3. DROP_QUEUE_TABLE



删除队列表。

语法如下：

```
PROCEDURE DROP_QUEUE_TABLE(
	QUEUE_TABLE         		IN 		VARCHAR,        
	FORCE               		IN 		BOOLEAN 		DEFAULT FALSE,
	AUTO_COMMIT         		IN 		BOOLEAN 		DEFAULT TRUE  
);
```

参数详解

  * QUEUE_TABLE 指定要删除的队列表名，格式为：[模式名.]队列名。
  * FORCE 指定是否强制删除关联的队列。
  * 剩余参数仅做语法支持。



  4. PURGE_QUEUE_TABLE



清理队列表中的消息。

语法如下：

```
PROCEDURE PURGE_QUEUE_TABLE(
	QUEUE_TABLE         		IN 		VARCHAR,        
	PURGE_CONDITION     		IN 		VARCHAR,
	PURGE_OPTIONS       		IN 		AQ$_PURGE_OPTIONS_T  
);
```

参数详解

  * QUEUE_TABLE 指定要清理的队列表名，格式为：[模式名.]队列名。
  * 剩余参数仅做语法支持。



  5. CREATE_QUEUE



创建队列。

语法如下：

```
PROCEDURE CREATE_QUEUE(
	QUEUE_NAME          		IN 		VARCHAR,               
	QUEUE_TABLE         		IN 		VARCHAR,               
	QUEUE_TYPE          		IN 		INTEGER 		DEFAULT NORMAL_QUEUE,
	MAX_RETRIES         		IN 		NUMBER 		DEFAULT NULL,   
	RETRY_DELAY         		IN 		NUMBER 		DEFAULT 0,      
	RETENTION_TIME      		IN 		NUMBER 		DEFAULT 0,      
	DEPENDENCY_TRACKING 	IN 		BOOLEAN 		DEFAULT FALSE,   
	QUEUE_COMMENT       		IN 		VARCHAR 		DEFAULT NULL,  
	AUTO_COMMIT         		IN 		BOOLEAN 		DEFAULT TRUE   
);
```

参数详解

  * QUEUE_NAME 指定要创建的队列名，格式为：[模式名.]队列名。非正规表示符要用""括起来，如"@@#".MYqueue。
  * QUEUE_TABLE 指定队列所属的队列表，格式为：[模式名.]队列表名。队列和队列表必须属于同一模式。
  * QUEUE_COMMENT 指定队列注释。
  * 剩余参数仅做语法支持。



  6. ALTER_QUEUE



修改队列。

语法如下：

```
PROCEDURE ALTER_QUEUE (
	QUEUE_NAME          		IN 		VARCHAR,      
	MAX_RETRIES         		IN 		NUMBER 		DEFAULT NULL,
	RETRY_DELAY         		IN 		NUMBER 		DEFAULT NULL,
	RETENTION_TIME      		IN 		NUMBER 		DEFAULT NULL,
	QUEUE_COMMENT       		IN 		VARCHAR 	DEFAULT NULL
);
```

参数详解

  * QUEUE_NAME 指定要修改的队列名，格式为：[模式名.]队列名。
  * QUEUE_COMMENT 指定队列注释。
  * 剩余参数仅做语法支持。



  7. DROP_QUEUE



删除队列，删除前必须先 STOP 队列。

语法如下：

```
PROCEDURE DROP_QUEUE(
	QUEUE_NAME          		IN 		VARCHAR,      
	AUTO_COMMIT         		IN 		BOOLEAN		DEFAULT TRUE   
);
```

参数详解

  * QUEUE_NAME 指定要删除的队列名，格式为：[模式名.]队列名。
  * 剩余参数仅做语法支持。



  8. START_QUEUE



启用队列功能。

语法如下：

```
PROCEDURE START_QUEUE(
	QUEUE_NAME          		IN 		VARCHAR,       
	ENQUEUE             		IN 		BOOLEAN 		DEFAULT TRUE,
	DEQUEUE             		IN 		BOOLEAN 		DEFAULT TRUE    
);
```

参数详解

  * QUEUE_NAME 指定要启用功能的队列名，格式为：[模式名.]队列名。
  * ENQUEUE 指定是否启用入队操作。
  * DEQUEUE 指定是否启用出队操作。



  9. STOP_QUEUE



禁用队列功能。

语法如下：

```
PROCEDURE STOP_QUEUE(
	QUEUE_NAME          		IN 		VARCHAR,   
    ENQUEUE             		IN 		BOOLEAN 		DEFAULT TRUE,
	DEQUEUE             		IN 		BOOLEAN 		DEFAULT TRUE,  
	WAIT                		IN 		BOOLEAN 		DEFAULT TRUE
);
```

参数详解

  * QUEUE_NAME 指定要禁用功能的队列名，格式为：[模式名.]队列名。
  * ENQUEUE 指定是否禁用入队操作。
  * DEQUEUE 指定是否禁用出队操作。
  * 剩余参数仅做语法支持。



  10. ADD_SUBSCRIBER



添加订阅者。

语法如下：

```
PROCEDURE ADD_SUBSCRIBER (
	QUEUE_NAME          		IN 		VARCHAR,                       
	SUBSCRIBER          		IN 		SYS.AQ$_AGENT,                 
	RULE                		IN 		VARCHAR 		DEFAULT NULL,         
	TRANSFORMATION      		IN 		VARCHAR 		DEFAULT NULL,         
	QUEUE_TO_QUEUE      		IN 		BOOLEAN 		DEFAULT FALSE,        
	DELIVERY_MODE       		IN 		INTEGER 		DEFAULT DBMS_AQADM.PERSISTENT
);
```

参数详解

  * QUEUE_NAME 指定要添加订阅者的队列名，格式为：[模式名.]队列名。
  * SUBSCRIBER 指定订阅者，队列的订阅者最大数量为 1024。参考 37.1 节中的数据类型说明。
  * 剩余参数仅做语法支持。



  11. REMOVE_SUBSCRIBER



删除订阅者。

语法如下：

```
PROCEDURE REMOVE_SUBSCRIBER (
	QUEUE_NAME          		IN 		VARCHAR,  
    SUBSCRIBER          		IN 		SYS.AQ$_AGENT  
);
```

参数详解

  * QUEUE_NAME 指定要删除订阅者的队列名，格式为：[模式名.]队列名。
  * SUBSCRIBER 指定要删除的订阅者，参考 37.1 节中数据类型的说明。



  12. ALTER_SUBSCRIBER



修改订阅者属性。该方法目前仅做语法兼容，暂无实际效果。

语法如下：

```
PROCEDURE ALTER_SUBSCRIBER (
	QUEUE_NAME          		IN 		VARCHAR,      
    SUBSCRIBER          		IN 		SYS.AQ$_AGENT,
  
    RULE                 		IN 		VARCHAR,  
  
    TRANSFORMATION    		    IN 		VARCHAR  
  
);
```

参数详解

  * QUEUE_NAME 指定要修改的队列名，格式为：[模式名.]队列名。
  * SUBSCRIBER 指定要修改的订阅者，参考 36.1 节中数据类型的说明。
  * RULE 定义消息、PL/SQL 等内容的布尔表达式。
  * TRANSFORMATION 定义订阅者出队消息的转换形式。



## 37.3 举例说明

使用包过程和函数之前，如果还未创建过系统包，请先调用系统过程创建。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_AQADM');
```

例 1 创建消息载荷类型 MY_MSG_TYPE 和模式 SSS，并在其上创建队列表"@TAB"。

```
CREATE OR REPLACE TYPE MY_MSG_TYPE AS OBJECT(MESSAGE VARCHAR(4000));
/
CREATE SCHEMA SSS;
/
DBMS_AQADM.CREATE_QUEUE_TABLE(
        QUEUE_TABLE         => 'SSS."@TAB"',
        QUEUE_PAYLOAD_TYPE  => 'MY_MSG_TYPE',
 		MULTIPLE_CONSUMERS  => TRUE
    );
```

例 2 创建队列 MY_QUEUE 并启动入队出队功能。

```
DBMS_AQADM.CREATE_QUEUE(
        QUEUE_NAME  => 'SSS.MY_QUEUE',
        QUEUE_TABLE => 'SSS."@TAB"'
);
DBMS_AQADM.START_QUEUE(QUEUE_NAME  => 'SSS.MY_QUEUE');
```

例 3 为队列添加订阅者 MY_SUB。

```
DBMS_AQADM.ADD_SUBSCRIBER(
        QUEUE_NAME => 'SSS.MY_QUEUE',
        SUBSCRIBER => SYS.AQ$_AGENT('MY_SUB', NULL, NULL)
    );
```

例 4 清理环境，包括禁用、删除队列，删除队列表。

```
BMS_AQADM.STOP_QUEUE(QUEUE_NAME => 'SSS.MY_QUEUE');
DBMS_AQADM.DROP_QUEUE(QUEUE_NAME => 'SSS.MY_QUEUE');
DBMS_AQADM.DROP_QUEUE_TABLE(QUEUE_TABLE => 'SSS."@TAB"');
```



# DBMS_XA包

DBMS_XA 包为应用提供了和 XA 接口一样功能的方法，以供用户在客户端工具（如 DIsql）或 DMSQL 程序中调用。

仅当 INI 参数 XA_COMPATIBLE_MODE 不为 2 时，支持使用 DBMS_XA 包。

## 45.1 常量

DBMS_XA 包中包含的常量如下：

```
LAST_ERRCODE        	INTEGER := 0;

//包中XA_START和XA_END函数的FLAG参数的可取值
TMNOFLAGS           	CONSTANT INTEGER :=0X00000000;
TMSUCCESS           	CONSTANT INTEGER :=0x04000000;
TMJOIN              	CONSTANT INTEGER :=0x00200000;
TMSUSPEND           	CONSTANT INTEGER :=0x02000000;
TMRESUME            	CONSTANT INTEGER :=0x08000000;

  
//包中函数PLS_INTEGER类型返回值的可取值
XA_RBBASE           	CONSTANT INTEGER := 100;
XA_RBROLLBACK       	CONSTANT INTEGER := XA_RBBASE;
XA_RBCOMMFAIL       	CONSTANT INTEGER := XA_RBBASE+1;
XA_RBDEADLOCK       	CONSTANT INTEGER := XA_RBBASE+2;
XA_RBINTEGRITY      	CONSTANT INTEGER := XA_RBBASE+3;
XA_RBOTHER          	CONSTANT INTEGER := XA_RBBASE+4;
XA_RBPROTO          	CONSTANT INTEGER := XA_RBBASE+5;
XA_RBTIMEOUT        	CONSTANT INTEGER := XA_RBBASE+6;
XA_RBTRANSIENT      	CONSTANT INTEGER := XA_RBBASE+7;
XA_RBEND            	CONSTANT INTEGER := XA_RBTRANSIENT;

XA_NOMIGRATE        	CONSTANT INTEGER := 9;
XA_HEURHAZ          	CONSTANT INTEGER := 8;
XA_HEURCOM          	CONSTANT INTEGER := 7;
XA_HEURRB           	CONSTANT INTEGER := 6;
XA_HEURMIX          	CONSTANT INTEGER := 5;
XA_RETRY            	CONSTANT INTEGER := 4;
XA_RDONLY           	CONSTANT INTEGER := 3;

XA_OK               	CONSTANT INTEGER := 0;

XAER_ASYNC          	CONSTANT INTEGER := -2;
XAER_RMERR          	CONSTANT INTEGER := -3;
XAER_NOTA           	CONSTANT INTEGER := -4;
XAER_INVAL          	CONSTANT INTEGER := -5;
XAER_PROTO          	CONSTANT INTEGER := -6;
XAER_RMFAIL         	CONSTANT INTEGER := -7;
XAER_DUPID          	CONSTANT INTEGER := -8;
XAER_OUTSIDE        	CONSTANT INTEGER := -9;
XAER_AFFINITY       	CONSTANT INTEGER := -10;
```

## 45.2 数据类型

DBMS_XA 包中包含的数据类型如下：

  1. DBMS_XA_XID



DBMS_XA_XID 是 XA 事务标识符。一个 DBMS_XA_XID 代表一个 XA 事务的一个事务分支。

语法如下：

```
CREATE OR REPLACE TYPE SYS.DBMS_XA_XID AS OBJECT(
  	FORMATID  	INTEGER,
  	GTRID     	RAW(64),
  	BQUAL     	RAW(64),
  
  	CONSTRUCTOR FUNCTION DBMS_XA_XID(
    		GTRID     IN      INTEGER
	)RETURN SELF AS RESULT,
  
  	CONSTRUCTOR FUNCTION DBMS_XA_XID(
    		GTRID     IN      RAW,
    		BQUAL     IN      RAW
	)RETURN SELF AS RESULT,
  
    	CONSTRUCTOR FUNCTION DBMS_XA_XID(
    		FORMATID  	IN      INTEGER,
    		GTRID     	IN      RAW,
    		BQUAL   IN  RAW  DEFAULT HEXTORAW('00000000000000000000000000000001')
	)RETURN SELF AS RESULT
);
```

类成员说明：

  * FORMATID 保留参数，无实际意义。
  * GTRID 全局事务 ID。由用户指定，需保证全局唯一。GTRID 相同的事务属于同一个 XA 事务。
  * BQUAL 事务分支。由用户指定，需保证全局唯一。缺省为系统内部自动创建。GTRID 相同，BQUAL 不同，表示为同一个 XA 事务的不同事务分支。



## 45.3 相关方法

DBMS_XA 包中包含的过程和函数如下详细介绍：

  1. XA_START



将当前会话与指定 XID 的事务分支关联起来。

语法如下：

```
FUNCTION XA_START(
	XID    	IN 		DBMS_XA_XID,
    FLAG   	IN 		PLS_INTEGER
)RETURN PLS_INTEGER;
```

参数详解

  * XID 输入参数，XA 事务标识符。
  * FLAG 输入参数，具体取值可参考 [45.1 常量](https://eco.dameng.com/document/dm/zh-cn/pm/dbms_xa-package.html#45.1%20%E5%B8%B8%E9%87%8F)。



  2. XA_END



将当前会话与指定 XID 的事务分支取消关联。

语法如下：

```
FUNCTION XA_END (
	XID    	IN 		DBMS_XA_XID,
    FLAG   	IN 		PLS_INTEGER
)RETURN PLS_INTEGER;
```

参数详解

  * XID 输入参数，XA 事务标识符。
  * FLAG 输入参数，具体取值可参考 [45.1 常量](https://eco.dameng.com/document/dm/zh-cn/pm/dbms_xa-package.html#45.1%20%E5%B8%B8%E9%87%8F)。



  3. XA_SETTIMEOUT



为当前会话设置事务的超时时间。

语法如下：

```
FUNCTION XA_SETTIMEOUT (
    SECONDS   	IN 		PLS_INTEGER
)RETURN PLS_INTEGER;
```

参数详解

  * SECONDS 输入参数，当前会话的事务超时时间，单位：秒。



  4. XA_PREPARE



事务分支准备提交。

语法如下：

```
FUNCTION XA_PREPARE (
	XID    	IN 		DBMS_XA_XID
)RETURN PLS_INTEGER;
```

参数详解

  * XID 输入参数，XA 事务标识符。



  5. XA_COMMIT



根据指定的 XID 提交全局事务。

语法如下：

```
FUNCTION XA_COMMIT (
	XID    		IN 		DBMS_XA_XID,
    ONEPHASE 	IN 		BOLLEAN
)RETURN PLS_INTEGER;
```

参数详解

  * XID 输入参数，XA 事务标识符。
  * ONEPHASE 输入参数，是否一阶段提交。TRUE：一阶段提交；FALSE：两阶段提交。



  6. XA_RECOVER



从 RM 上获取所有准备提交或者启发式完成的事务分支。

语法如下：

```
FUNCTION XA_RECOVER() RETURN DBMS_XA_XID_ARRAY;
```

返回值

RM 上所有准备提交或者启发式完成的事务分支 ID 数组。

  7. XA_FORGET



通知 RM 擦除启发式提交或回滚掉的事务分支。

语法如下：

```
FUNCTION XA_FORGET(
	XID    	IN 		DBMS_XA_XID
)RETURN PLS_INTEGER;
```

参数详解

  * XID 输入参数，XA 事务标识符。



  8. XA_ROLLBACK



通知 RM 回滚事务分支所做的修改。

语法如下：

```
FUNCTION XA_ROLLBACK(
	XID    	IN 		DBMS_XA_XID
)RETURN PLS_INTEGER;
```

参数详解

  * XID 输入参数，XA 事务标识符。



  9. XA_GETLASTOER



获取最后一个错误码。

语法如下：

```
FUNCTION XA_GETLASTOER()RETURN PLS_INTEGER;
```

返回值

最后一个错误码。

## 45.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程 SP_CREATE_SYSTEM_PACKAGES 创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES(1,'DBMS_XA');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

准备数据表。

```
CREATE TABLE TESTXA(C1 INT,C2 INT);
```

利用 XA_START 将当前会话与事务分支关联起来。

```
DECLARE
	TESTI INT;
	TESTXID DBMS_XA_XID := DBMS_XA_XID(16);
BEGIN
	TESTI := DBMS_XA.XA_START(TESTXID,DBMS_XA.TMNOFLAGS);
	DBMS_OUTPUT.PUT_LINE(TESTI);
	INSERT INTO TESTXA VALUES(1,1);
END;
```

执行结果如下：

```
0
```

利用 XA_END 将当前会话与事务分支取消关联。

```
DECLARE
	TESTI INT;
	TESTXID DBMS_XA_XID := DBMS_XA_XID(16);
BEGIN
	TESTI := DBMS_XA.XA_END(TESTXID,DBMS_XA.TMSUCCESS);
	DBMS_OUTPUT.PUT_LINE(TESTI);
END;
```

执行结果如下：

```
0
```

利用 XA_PREPARE 和 XA_COMMIT 两阶段提交事务。

```
DECLARE
	TESTI INT;
	TESTXID DBMS_XA_XID := DBMS_XA_XID(16);
BEGIN
	TESTI := DBMS_XA.XA_PREPARE(TESTXID);
	DBMS_OUTPUT.PUT_LINE(TESTI);
	TESTI := DBMS_XA.XA_COMMIT(TESTXID,FALSE);
	DBMS_OUTPUT.PUT_LINE(TESTI);
END;
```

执行结果如下：

```
0
0
```

上述示例返回值均为 0，即 XA_OK，表示语句执行成功，其余返回值含义可参考 [45.1 常量](https://eco.dameng.com/document/dm/zh-cn/pm/dbms_xa-package.html#45.1%20%E5%B8%B8%E9%87%8F)。

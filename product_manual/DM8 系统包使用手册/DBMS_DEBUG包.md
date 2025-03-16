

# DBMS_DEBUG包

DBMS_DEBUG 包用于调试 PL/SQL 语句块。利用 DBMS_DEBUG 系统包调试 PL/SQL 时，至少需要两个会话，一个会话用于执行目标程序，另一个会话用于调试目标程序。

DBMS_DEBUG 系统包调试 PL/SQL 的主要步骤如下：

  1. 初始化会话 1；
  2. 会话 1 打开调试模式；
  3. 会话 1 执行目标程序，目标程序阻塞；
  4. 会话 2 启动调试；
  5. 会话 2 设置断点；
  6. 会话 2 执行单步调试，直至目标程序执行结束；
  7. 会话 1 和会话 2 结束调试。



## 47.1  相关方法

  1. PROBE_VERSION



DBMS_DEBUG 系统包的版本号。

语法如下：

```
PROCEDURE PROBE_VERSION(
	MAJOR OUT INTEGER,
	MINOR OUT INTEGER
);
```

参数详解

  * MAJOR

主要版本号。

  * MINOR

次要版本号。随着功能的增加而迭代。



返回值

无

  2. INITIALIZE



初始化当前会话以进行调试。

语法如下：

```
FUNCTION INITIALIZE(
	DEBUG_SESSION_ID  	IN VARCHAR2		:= NULL,
	DIAGNOSTICS       	IN INTEGER 		:= 0,
	DEBUG_ROLE        	IN VARCHAR2		:= NULL,
	DEBUG_ROLE_PWD    	IN VARCHAR2		:= NULL
) RETURN VARCHAR2;
```

参数详解

  * DEBUG_SESSION_ID

指定调试会话 ID。

  * DIAGNOSTICS

暂不支持。

  * DEBUG_ROLE

暂不支持。

  * DEBUG_ROLE_PWD

暂不支持。



返回值

新注册的调试会话 ID，返回值与 DEBUG_SESSION_ID 相同。若 DEBUG_SESSION_ID 为 NULL，则自动生成唯一的调试会话 ID，不同会话返回值不同，相同会话返回值相同。

  3. DEBUG_ON



打开调试模式。

语法如下：

```
PROCEDURE DEBUG_ON(
	NO_CLIENT_SIDE_PLSQL_ENGINE 	BOOLEAN 		:= TRUE,
	IMMEDIATE                 		BOOLEAN 		:= FALSE
);
```

参数详解

  * NO_CLIENT_SIDE_PLSQL_ENGINE

暂不支持。

  * IMMEDIATE

暂不支持。



返回值

无

  4. DEBUG_OFF



关闭调试模式。

语法如下：

```
PROCEDURE DEBUG_OFF;
```

参数详解

无

返回值

无

  5. ATTACH_SESSION



将待调试目标程序与当前会话相关联，启动调试。

语法如下：

```
PROCEDURE ATTACH_SESSION(
	DEBUG_SESSION_ID  	IN 	VARCHAR2, 
	DIAGNOSTICS       	IN 	INTEGER 	:= 0
);
```

参数详解

  * DEBUG_SESSION_ID

待调试目标程序所在的调试会话 ID。即会话使用 DBMS_DEBUG.INITIALIZE 进行初始化时的返回值。

  * DIAGNOSTICS

暂不支持。



返回值

无

  6. DETACH_SESSION



结束调试。

语法如下：

```
PROCEDURE DETACH_SESSION;
```

参数详解

无

返回值

无

  7. SET_BREAKPOINT



设置断点。若指定行无法设置断点，则在距离指定行最近的行设置断点。若同一行重复设置断点，则只会添加一个断点。

语法如下：

```
FUNCTION SET_BREAKPOINT(
	PROGRAM     		IN  		PROGRAM_INFO,
	LINE#       		IN  		INTEGER,
	BREAKPOINT# 		OUT 		INTEGER,
	FUZZY       		IN  		INTEGER := 0,
	ITERATIONS  		IN  		INTEGER := 0
) RETURN INTEGER;
```

参数详解

  * PROGRAM

仅支持输入 NULL。

  * LINE#

指定需要设置断点的行号。

  * BREAKPOINT#

输出断点编号，断点从 1 开始依次编号。

  * FUZZY

暂不支持。

  * ITERATIONS

暂不支持。



返回值

返回值为 0 表示成功执行；为 12 表示不能在找到指定行设置断点，如：行号过大、加密文本解密失败；为 14 表示无法设置断点，如：对象不存在；为 16 表示其它错误导致无法设置断点。

  8. "CONTINUE"



单步运行目标程序执行调试。需要注意的是，该函数名必须为大写。

语法如下：

```
FUNCTION "CONTINUE"(
	RUN_INFO       		IN OUT 		RUNTIME_INFO,
	BREAKFLAGS     		IN     		INTEGER,
	INFO_REQUESTED 		IN     		INTEGER := NULL
) RETURN INTEGER;
```

参数详解

  * RUN_INFO

程序运行相关信息。数据类型为 DBMS_DEBUG.RUNTIME_INFO。目前 DBMS_DEBUG.RUNTIME_INFO
中包含四个属性，分别为：Breakpoint：断点编号；Line#：语句行号；Program.Owner：调用语句块所属模式名；Program.Name：调用语句块名称。

  * BREAKFLAGS

指定目标程序单步执行方式。可取值：DBMS_DEBUG.break_next_line：在目标程序语句的下一行中断（跳过调用）；DBMS_DEBUG.break_any_call：在目标程序语句的下一行中断（分步调用）。

  * INFO_REQUESTED

指定目标程序停止时需要返回的信息。可取值
DBMS_DEBUG.info_getBreakpoint：获取断点编号；DBMS_DEBUG.info_getLineinfo：获取行号；DBMS_DEBUG.info_getStackDepth：获取堆栈的当前深度；DBMS_DEBUG.info_getOerInfo：获取当前调用语句块相关信息。支持同时指定多个值，不同值之间使用“+”相连。



返回值

单步执行完成，返回 DBMS_DEBUG.SUCCESS。

## 47.2  举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_DEBUG');
```

用户可调用 PROBE_VERSION 过程查看 DBMS_DEBUG 系统包的当前版本。

```
//DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
SET SERVEROUTPUT ON

//查看DBMS_DEBUG系统包的当前版本
DECLARE
    MAJOR INTEGER;
    MINOR INTEGER;
BEGIN
    DBMS_DEBUG.PROBE_VERSION(MAJOR,MINOR);
    DBMS_OUTPUT.PUT_LINE('PROBE VERSION IS: ' || MAJOR || '.' || MINOR);
END;
/

//打印信息如下，表示DBMS_DEBUG系统包的当前版本为2.0
PROBE VERSION IS: 2.0
```

例 1 调试目标程序，调试过程中跳过调用。

步骤一 创建函数 SYSDBA.F1，以便在目标程序中进行调用。

```
CREATE OR REPLACE FUNCTION SYSDBA.F1(C1 INT) RETURN INT
AS
	A INT;
	B INT;
BEGIN
	A = C1 * C1;
	B = C1 + C1;
	RETURN A+B;
END;
/
```

步骤二 创建会话 1 并初始化，然后打开调试模式。

```
//DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
SET SERVEROUTPUT ON

//初始化会话
SELECT DBMS_DEBUG.INITIALIZE FROM DUAL;
//返回值
行号     INITIALIZE  
---------- --------------
1          0x7f18fc011278

EXEC DBMS_DEBUG.DEBUG_ON;
```

步骤三 会话 1 执行目标程序，此时目标程序阻塞。

```
DECLARE
V1 INT;
V2 INT;
BEGIN
	V1 := 10;
	V2 := SYSDBA.F1(V1);
	DBMS_OUTPUT.PUT_LINE(V2);
END;
/
```

步骤四 创建会话 2，执行 DBMS_DEBUG.ATTACH_SESSION 启动调试。

```
//DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
SET SERVEROUTPUT ON

//需要输入会话1执行DBMS_DEBUG.INITIALIZE时的返回值
CALL DBMS_DEBUG.ATTACH_SESSION(?);
请输入参数1的值:0x7f18fc011278
```

步骤五 会话 2 设置断点。

```
//在目标程序的第5行设置断点
DECLARE
	BP INTEGER;
     RET INTEGER;
BEGIN
     RET := DBMS_DEBUG.SET_BREAKPOINT(NULL, ?,  BP);
     DBMS_OUTPUT.PUT_LINE('Ret := '||RET);
     IF RET = DBMS_DEBUG.SUCCESS THEN 
     	DBMS_OUTPUT.PUT_LINE('Breakpoint := '||BP);
     END IF;
END;
/
请输入参数1的值:5

//打印信息如下，表示第一个断点设置成功
Ret := 0
Breakpoint := 1

//在目标程序的第6行设置断点
DECLARE
	BP INTEGER;
     RET INTEGER;
BEGIN
     RET := DBMS_DEBUG.SET_BREAKPOINT(NULL, ?,  BP);
     DBMS_OUTPUT.PUT_LINE('Ret := '||RET);
     IF RET = DBMS_DEBUG.SUCCESS THEN 
     	DBMS_OUTPUT.PUT_LINE('Breakpoint := '||BP);
     END IF;
END;
/
请输入参数1的值:6

//打印信息如下，表示第二个断点设置成功
Ret := 0
Breakpoint := 2
```

步骤六 会话 2 单步运行目标程序执行调试，调试过程中跳过调用。

```
//第1次单步执行
DECLARE
     RUN_INFO DBMS_DEBUG.RUNTIME_INFO;
     RET INTEGER;
BEGIN
     RET := DBMS_DEBUG."CONTINUE"(RUN_INFO, 0,  
     0
     + DBMS_DEBUG.info_getBreakpoint
     + DBMS_DEBUG.info_getLineinfo);
     DBMS_OUTPUT.PUT_LINE('Ret := '||RET);
     IF RET = DBMS_DEBUG.SUCCESS THEN 
   	 	DBMS_OUTPUT.PUT_LINE('Breakpoint := '||RUN_INFO.Breakpoint);
   	 	DBMS_OUTPUT.PUT_LINE('Line# := '||RUN_INFO.Line#);
     END IF;
END;
/
//打印信息如下，表示命中第1个断点，行号为5
Ret := 0
Breakpoint := 1
Line# := 5

//第2次单步执行，语句同上，打印信息如下，表示命中第2个断点，行号为6
Ret := 0
Breakpoint := 2
Line# := 6

//第3次单步执行，语句同上，打印信息如下，表示没有命中断点或某一行
Ret := 16
//此时切换到会话1会发现目标程序已经执行完成
```

步骤七 会话 1 和会话 2 结束调试。

```
// 会话1关闭调试模式，此时语句会阻塞
EXEC DBMS_DEBUG.DEBUG_OFF;

//会话2执行"CONTINUE"，唤醒会话1继续执行
DECLARE
	RUN_INFO DBMS_DEBUG.RUNTIME_INFO;
     RET INTEGER;
BEGIN
	RET := DBMS_DEBUG."CONTINUE"(RUN_INFO, 0,  0);
	DBMS_OUTPUT.PUT_LINE('Ret := '||RET);
END;
/
//打印信息如下，表示没有命中断点或某一行，此时切换到会话1会发现目标程序已经执行完成
Ret := 28

//会话2结束调试
EXEC DBMS_DEBUG.DETACH_SESSION;
```

例 2 调试目标程序，调试过程中进行分步调用。

除步骤六外，其余步骤与例 1 相同，用户可参考例 1 中的步骤介绍。

若在调试过程中进行分步调用，则会话 2 单步运行目标程序执行调试的过程如下：

```
//第1次单步执行，碰到调用时执行跳入，并输出当前调用语句块名称以及所属模式名
DECLARE
    RUN_INFO DBMS_DEBUG.RUNTIME_INFO;
    RET INTEGER;
BEGIN
    RET := DBMS_DEBUG."CONTINUE"(RUN_INFO, 
    DBMS_DEBUG.break_any_call,  
    0
    + DBMS_DEBUG.info_getBreakpoint
    + DBMS_DEBUG.info_getLineinfo
    + DBMS_DEBUG.info_getStackDepth
    + DBMS_DEBUG.info_getOerInfo);
    DBMS_OUTPUT.PUT_LINE('Ret := '||RET);
    IF RET = DBMS_DEBUG.SUCCESS THEN 
        DBMS_OUTPUT.PUT_LINE('Breakpoint := '||RUN_INFO.Breakpoint);
        DBMS_OUTPUT.PUT_LINE('Line# := '||RUN_INFO.Line#);
        IF RUN_INFO.Program.Owner IS NOT NULL THEN
            DBMS_OUTPUT.PUT_LINE('Owner := '||RUN_INFO.Program.Owner);
        END IF;
        IF  RUN_INFO.Program.Name IS NOT NULL THEN
            DBMS_OUTPUT.PUT_LINE('Name := '||RUN_INFO.Program.Name);
        END IF;
    END IF;
END;
/
//打印信息如下，表示命中第1个断点，行号为5
Ret := 0
Breakpoint := 1
Line# := 5

//第2次单步执行，语句同上，打印信息如下，表示命中第2个断点，行号为6
Ret := 0
Breakpoint := 2
Line# := 6

//第3次单步执行，语句同上，此时执行跳入，打印信息如下，表示执行到SYSDBA.F1的第6行
Ret := 0
Breakpoint := 
Line# := 6
Owner := SYSDBA
Name := F1

//第4次单步执行，语句同上，打印信息如下，表示执行到SYSDBA.F1的第7行
Ret := 0
Breakpoint := 
Line# := 7
Owner := SYSDBA
Name := F1

//第5次单步执行，语句同上，打印信息如下，表示执行到SYSDBA.F1的第8行
Ret := 0
Breakpoint := 
Line# := 8
Owner := SYSDBA
Name := F1

//第6次单步执行，语句同上，此时跳出调用，执行到目标程序的第7行
Ret := 0
Breakpoint := 
Line# := 7

//第7次单步执行，语句同上，打印信息如下，表示没有命中断点或某一行
Ret := 16
//此时切换到会话1会发现目标程序已经执行完成
```

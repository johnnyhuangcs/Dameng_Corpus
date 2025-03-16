

# DBMS_APPLICATION_INFO包

DBMS_APPLICATION_INFO 包用于记录数据库正在运行的模块名称以及模块正在执行的事务名称。利用这个包，管理员和开发者可以进行性能追踪以及调试。

## 43.1 相关方法

DBMS_APPLICATION_INFO 包中包含的过程和函数如下详细介绍：

  1. SET_CLIENT_INFO



设置当前会话的客户端信息。

语法如下：

```
PROCEDURE SET_CLIENT_INFO(
	CLIENT_INFO		IN		VARCHAR2
);
```

参数详解

  * CLIENT_INFO 输入参数，当前会话的客户端信息，最大长度为 64 字节。



  2. SET_MODULE



设置当前运行的模块名称。

语法如下：

```
PROCEDURE SET_MODULE(
	MODULE_NAME		IN		VARCHAR2,
	ACTION_NAME		IN		VARCHAR2
);
```

参数详解

  * MODULE_NAME 输入参数，当前运行的模块名称，模块名通常为数据库中的表名或预编译应用的代码段名称，最大长度为 48 字节。当前模块运行结束后需要再次调用 SET_MODULE 过程，若存在下一个待运行模块，则需要将 MODULE_NAME 参数设置为下一个运行模块的名称；若不存在待运行模块，则需要将 MODULE_NAME 参数设置为 NULL。
  * ACTION_NAME 输入参数，当前模块正在执行的事务名称，最大长度为 32 字节。用户可将该参数设置为 NULL，之后通过调用 SET_ACTION 过程设置当前模块正在执行的事务名称。



  3. SET_ACTION



设置当前模块正在执行的事务名称。

语法如下：

```
PROCEDURE SET_ACTION(
	ACTION_NAME		IN		VARCHAR2
);
```

参数详解

  * ACTION_NAME 输入参数，当前模块正在执行的事务名称，最大长度为 32 字节。当前事务运行结束后需要再次调用 SET_ACTION 过程，若存在下一个待执行事务，则需要将 ACTION_NAME 参数设置为下一个执行事务的名称；若不存在待执行事务，则需要将 ACTION_NAME 参数设置为 NULL。



  4. READ_CLIENT_INFO



获取当前会话的客户端信息。

语法如下：

```
PROCEDURE READ_CLIENT_INFO(
	CLIENT_INFO     	OUT		VARCHAR2
);
```

参数详解

  * CLIENT_INFO 输出参数，当前会话的客户端信息。



  5. READ_MODULE



获取数据库当前运行的模块名称以及模块正在执行的事务名称。

语法如下：

```
PROCEDURE READ_MODULE(
	MODULE_NAME		OUT		VARCHAR2,
	ACTION_NAME		OUT		VARCHAR2
);
```

参数详解

  * MODULE_NAME 输出参数，当前运行的模块名称。
  * ACTION_NAME 输出参数，当前模块正在执行的事务名称。



## 43.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程 SP_CREATE_SYSTEM_PACKAGES 创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_APPLICATION_INFO');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

记录数据库当前正在运行的模块名称以及模块正在执行的事务名称。

```
DECLARE
	C_N VARCHAR2(100);
	M_N VARCHAR2(100);
	A_N VARCHAR2(100);
BEGIN
	DBMS_APPLICATION_INFO.SET_CLIENT_INFO('MY CLIENT');
	DBMS_APPLICATION_INFO.SET_MODULE('MODULE1', 'ACTION1');
	DBMS_APPLICATION_INFO.SET_ACTION('ACTION2');
	DBMS_APPLICATION_INFO.READ_CLIENT_INFO(C_N);
	DBMS_APPLICATION_INFO.READ_MODULE(M_N, A_N);
	DBMS_OUTPUT.PUT_LINE('CLIENT INFO IS:' || C_N);
	DBMS_OUTPUT.PUT_LINE('MODULE NAME IS:' || M_N);
	DBMS_OUTPUT.PUT_LINE('ACTION NAME IS:' || A_N);
END;
```

执行结果如下：

```
CLIENT INFO IS:MY CLIENT
MODULE NAME IS:MODULE1
ACTION NAME IS:ACTION2
```

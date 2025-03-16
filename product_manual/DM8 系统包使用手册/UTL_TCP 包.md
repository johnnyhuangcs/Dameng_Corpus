

# UTL_TCP 包

许多的应用程序都是基于 TCP/IP 协议的，UTL_TCP 包提供了与外部的 TCP/IP 服务器通讯和基本的收发数据的功能。

## 30.1 相关方法

  1. AVAILABLE



当前能够读取的连接字符数(最大不超过 96 字节)。

语法如下：

```
FUNCTION AVAILABLE(
	C 			IN OUT 		CONNECTION,
	TIMEOUT 	IN 			INT 			DEFAULT 0
)RETURN INT;
```

参数详解

  * C 一个 OPEN_CONNECTION 打开的、有效的 CONNECTION。
  * TIMEOUT 如果没有数据，则在指定的 timeout 时间返回。



返回值

当前获取的一个 TCP/IP 连接字节数。

  2. CLOSE_ALL_CONNECTION



关闭所有的连接。

语法如下：

```
PROCEDURE CLOSE_ALL_CONNECTION();
```

  3. CLOSE_ALL_CONNECTIONS



关闭所有的连接，用法同 CLOSE_ALL_CONNECTION()。

语法如下：

```
PROCEDURE CLOSE_ALL_CONNECTIONS();
```

  4. CLOSE_CONNECTION



关闭指定的连接。

语法如下：

```
PROCEDURE CLOSE_CONNECTION (
	C 		IN OUT  		CONNECTION
);
```

参数详解

  * C 一个 OPEN_CONNECTION 打开的、有效的 CONNECTION。



  5. GET_RAW



读取指定长度（二进制的数据）的数据。

语法如下：

```
FUNCTION GET_RAW  (
	C 		IN OUT 		CONNECTION, 
	LEN 	IN 			INT 			DEFAULT 1, 
	PEEK 	IN 			BOOLEAN 		DEFAULT FALSE
)RETURN VARBINARY;
```

参数详解

  * C 一个 open_connection 打开的、有效的 connection。
  * LEN 要收到的数据的长度，默认值 1。
  * PEEK 该选项为 true，则指只是查看队列的数据，不会从队列中删除，下次还能够读到；为 false，则会删除队列中的数据。默认为 FALSE。



返回值

读取到的二进制数据。

  6. OPEN_CONNECTION



打开一个连接到外部服务器的连接。

语法如下：

```
FUNCTION OPEN_CONNECTION(
	REMOTE_HOST		IN 		VARCHAR2, 
	REMOTE_PORT 	IN 		INT, 
	LOCAL_HOST 		IN 		VARCHAR2 	DEFAULT NULL, 
	LOCAL_PORT 		IN 		INT 		DEFAULT NULL, 
	CHARSET    		IN 		VARCHAR2 	DEFAULT NULL,
    NEWLINE    		IN 		VARCHAR2 	DEFAULT CRLF,
	TX_TIMEOUT 		IN 		INT 		DEFAULT NULL
) RETURN CONNECTION;
```

参数详解

  * remote_host 远程主库的名字，或者 ip 地址。
  * remote_port 远程主库的端口。
  * local_hos t 本地主库的名字（一般不用设置）。
  * local_port 本地主库的端口（一般不用设置）。
  * charse t 用于编码转换的在线字符集。
  * newline 换行符序列，用于将文本行转换为在线字符集。
  * tx_timeout 尝试链接到远程主库的等待时间。



返回值

一个连接到指定外部服务器的链接。

  7. READ_RAW



以二进制形式读取一行数据。

语法如下：

```
FUNCTION READ_RAW (
	C 		IN OUT 		CONNECTION, 
	DATA 	OUT 		VARBINARY, 
	LEN 	IN 			INT 			DEFAULT 1, 
	PEEK 	IN 			BOOLEAN 		DEFAULT FALSE
) RETURN INT;
```

参数详解

  * c 一个 open_connection 打开的、有效的 connection。
  * data 用于存放读到的数据。
  * len 要收到的数据的长度，默认值 1。
  * peek 该选项为 true，则指只是查看队列的数据，不会从队列中删除，下次还能够读到；为 false，则会删除队列中的数据。默认为 FALSE。



返回值

收到的数据的字节长度。

  8. WRITE_RAW



发送一行二进制数据。

语法如下：

```
FUNCTION WRITE_RAW(
	C 		IN OUT 		CONNECTION, 
	DATA 	IN 			VARBINARY, 
	LEN 	IN 			INT 			DEFAULT NULL
) RETURN INT;
```

参数详解

  * c 一个 open_connection 打开的、有效的 connection。
  * data 存放要发送的数据。
  * len 要发送数据的长度。



返回值

发送的二进制数据的字节长度。只有成功发送才会有返回值。

  9. WRITE_LINE



将文本行转换为在线字符集，并发送至连接的服务器。函数 OPEN_CONNECTION 中设置的换行符序列 NEWLINE 会在消息传输之前附加到消息中。

语法如下：

```
FUNCTION WRITE_LINE(
	C 		IN OUT 		CONNECTION, 
	DATA 	IN 			VARCHAR2 		DEFAULT NULL 
) RETURN INT;
```

参数详解

  * c 一个 open_connection 打开的、有效的 connection。
  * data 存放要发送的数据。



返回值

实际写入的字符数。

  10. WRITE_TEXT



将文本行转换为在线字符集，并发送至连接的服务器。

语法如下：

```
FUNCTION WRITE_TEXT(
	C 		IN OUT 		CONNECTION, 
	DATA 	IN 			VARCHAR2, 
	LEN					INT			DEFAULT NULL
) RETURN INT;
```

参数详解

  * c 一个 open_connection 打开的、有效的 connection。
  * data 存放要发送的数据。
  * len 要发送数据的长度。



返回值

实际写入的字符数。

  11. GET_LINE



读取一行数据。

语法如下：

```
FUNCTION GET_LINE(
	C      		IN OUT  CONNECTION, 
	REMOVE_CRLF IN    	BOOLEAN     DEFAULT FALSE, 
	PEEK     	IN  	BOOLEAN     DEFAULT FALSE
) RETURN VARCHAR2;
```

参数详解

  * c 一个 open_connection 打开的、有效的 connection。
  * REMOVE_CRLF 用于表示是否需要移除接收数据尾部的换行符，默认为 FALSE。
  * peek 该选项为 TRUE，则指只是查看队列的数据，不会从队列中删除，下次还能够读到；为 FALSE，则会删除队列中的数据。默认为 FALSE。



返回值

读取到的一行数据。

  12. READ_TEXT



读取一行数据。

语法如下：

```
FUNCTION READ_TEXT(
	C    	IN OUT  CONNECTION, 
	DATA   	IN OUT  VARCHAR2, 
	LEN   	IN    	INT      	DEFAULT 1, 
	PEEK   	IN   	BOOLEAN     DEFAULT FALSE
) RETURN INT;
```

参数详解

  * c 一个 open_connection 打开的、有效的 connection。
  * DATA 用于存放接收到的字符数据。
  * LEN 用于指定希望接收数据的长度，默认为 1。
  * peek 该选项为 true，则指只是查看队列的数据，不会从队列中删除，下次还能够读到；为 false，则会删除队列中的数据。默认为 false。



返回值

实际接收到的数据长度。

  13. GET_TEXT



读取数据。

语法如下：

```
FUNCTION GET_TEXT(
	C      IN OUT   CONNECTION, 
	LEN    IN       INT         DEFAULT 1, 
	PEEK   IN       BOOLEAN     DEFAULT FALSE
) RETURN VARCHAR2;
```

参数详解

  * C 一个 open_connection 打开的、有效的 connection。
  * LEN 用于指定希望读取的数据的长度，取值范围 0~32767，单位：字符，默认为 1。
  * PEEK 该选项为 TRUE，则指只是查看队列的数据，不会从队列中删除，下次还能够读到；为 FALSE，则会删除队列中的数据。默认为 FALSE。



返回值

读取到的数据。

  14. FLUSH



立刻将缓冲区中数据发送。

语法如下：

```
PROCEDURE FLUSH(
	C  IN OUT  CONNECTION
);
```

参数详解

  * c 一个 open_connection 打开的、有效的 connection。



## 30.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'UTL_TCP');
SET SERVEROUTPUT ON;  //PRINT需要设置这条语句，才能打印出消息
```

例 UTL_TCP 包示例。

```
DECLARE
    HOST VARCHAR(128);
    PORT INT;
    RET INT;
    LEN INT;
    DATA VARBINARY(512);
    SHOW VARBINARY(128);
    C UTL_TCP.CONNECTION;
BEGIN
    HOST := '223.254.17.100';
    PORT := 25;
    C := UTL_TCP.OPEN_CONNECTION(HOST,PORT);
    RET := UTL_TCP.WRITE_RAW(C, RAWTOHEX('DAMENG DATABASE'),15);
    PRINT RET;   
    LEN := 48;
    RET := UTL_TCP.AVAILABLE(C,0);   
    PRINT RET;
    SHOW := UTL_TCP.GET_RAW(C,LEN); //UTL_TCP包仅用于发送请求，响应信息由远程HOST决定，此处获取的二进制流信息根据不同的远程HOST可能为不同的响应结果，故下述打印结果以实际为准
    PRINT SHOW;
    RET := UTL_TCP.READ_RAW(C,DATA,LEN);
    UTL_TCP.CLOSE_CONNECTION(C);
END; 
/
```

结果如下：

```
15
0
323230206D61696C2E64616D656E672E636F6D2045534D545020506F73746669780D0A34323120342E342E32206D6169
```

![](media/邮件内容.png)

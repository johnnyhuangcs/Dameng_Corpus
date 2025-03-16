

# UTL_SMTP 包

UTL_SMTP 包的功能是提供对 smtp 服务器的基本访问请求以及通过 SMTP 服务器发送邮件的功能。

使用 UTL_SMTP 包发送邮件的大致流程为：首先，声明连接，并开启连接；其次，遵照 SMTP 协议与服务器进行握手，并根据 RFC821 简单邮件传输协议进行命令和邮件内容等的发送；最后，关闭会话，关闭连接。

## 34.1 相关方法

下面对各个函数和过程进行详细说明。

  1. CONNECTION



定义 CONNECTION 为 RECORD 类型，为下面的过程或函数使用。

语法如下：

```
TYPE CONNECTION IS RECORD (
	REMOTE_HOST			VARCHAR2(255),
	REMOTE_PORT			INT,
	TX_TIMEOUT			INT,
	PRIVATE_HNDL		BIGINT
);
```

参数详解

  * REMOTE_HOST，REMOTE_PORT

为 SMTP 服务器的地址及端口号，由 OPEN_CONNECTION 过程传入。

  * TX_TIMEOUT

为等待响应时间，同样由 OPEN_CONNECTION 过程传入。

  * PRIVATE_HNDL

为内部标识 CONNECTION 的 ID。



  2. REPLY



支持新类型 REPLY，表示一行 SMTP 服务器的回复

语法如下：

```
TYPE REPLY IS RECORD (
	CODE   INTEGER,
    TEXT    VARCHAR2(508)
);
```

参数详解

  * CODE

为服务器返回的返回码。

  * TEXT

为服务器返回的消息内容。



  3. REPLIES



支持新类型 REPLIES，表示多行 SMTP 服务器的回复

语法如下：

```
TYPE REPLIES IS TABLE OF REPLY INDEX BY BINARY_INTEGER; 
```

  4. OPEN_CONNECTION



用于开启并返回与 SMTP 服务器的连接。

语法如下：

```
FUNCTION OPEN_CONNECTION (
	HOST        		IN  		VARCHAR2,
	PORT        		IN  		PLS_INTEGER DEFAULT 25,
	TX_TIMEOUT  		IN  		PLS_INTEGER DEFAULT NULL
)RETURN CONNECTION;

FUNCTION  OPEN_CONNECTION  (
    HOST        IN  VARCHAR2,
    PORT        IN  PLS_INTEGER  DEFAULT  25,
    C           OUT  CONNECTION,
    TX_TIMEOUT  IN  PLS_INTEGER  DEFAULT  NULL
)RETURN  REPLY;
```

参数详解

  * HOST

SMTP 服务器地址。

  * PORT

SMTP 服务器端口号。

  * C

使用的 SMTP 连接。

  * TX_TIMEOUT

等待时间， NULL 代表一直等待，0 代表不等待。



返回值

CONNECTION 表示使用的 SMTP 连接，REPLY 表示一行 SMTP 服务器的回复。

  5. HELO/EHLO



用于向 SMTP 服务器打招呼。EHLO 指令表示需要 SMTP 认证，HELO 指令表示不需要 SMTP 认证。

语法如下：

```
PROCEDURE HELO (
	C       		IN OUT 		CONNECTION,
	R_DOMAIN  		IN          VARCHAR2
); 
```

```
PROCEDURE EHLO (
	C       		IN OUT 		CONNECTION,
	R_DOMAIN  		IN          VARCHAR2
); 
```

```
FUNCTION HELO (
  C    IN OUT NOCOPY  CONNECTION,
  R_DOMAIN IN       VARCHAR2
)RETURN REPLY;
```

```
FUNCTION EHLO (
  C    IN OUT NOCOPY  CONNECTION,
  R_DOMAIN IN       VARCHAR2
) RETURN REPLIES;
```

参数详解

  * C

使用的 SMTP 连接。

  * R_DOMAIN

SMTP 连接的域名。



返回值

REPLY：一行 SMTP 服务器的回复；REPLIES：多行 SMTP 服务器的回复。

  6. COMMAND



用于向 SMTP 服务器发送命令。命令不由此程序判断正确性，发送错误命令，程序会报错。

语法如下：

```
PROCEDURE COMMAND (
	C     		IN OUT 		CONNECTION,
	CMD   		IN          VARCHAR2,
	ARG   		IN       	VARCHAR2 DEFAULT NULL
);

FUNCTION COMMAND (
	C	IN OUT	NOCOPY		CONNECTION,
	CMD		IN		VARCHAR2,
	ARG		IN 		VARCHAR2	DEFAULT	NULL
)RETURN	REPLY;
```

参数详解

  * C

使用的连接。

  * CMD 命令名

准备登陆为 AUTH LOGIN(AUTH LOGIN 命令使用后，要分别再使用 COMMAND 发送用户名和密码)；握手为 HELO；更多的命令详见
RFC821 简单邮件传输协议。

  * ARG 命令参数

可选参数，不同的 CMD 使用不同的 ARG 。例如: AUTH LOGIN 无参数，但必须接下来继续使用 COMMAND 发送用户名和密码；而 HELO
后面的参数为服务器地址。



返回值

一行 SMTP 服务器的回复。

  7. MAIL



用于告知服务器发件人。

语法如下：

```
PROCEDURE MAIL (
	C           		IN OUT 		CONNECTION,
	SENDER      		IN          VARCHAR2,
	M_PARAMETERS  		IN          VARCHAR2 	DEFAULT NULL
);

FUNCTION  MAIL  (
    C             IN  OUT  NOCOPY    CONNECTION,
    SENDER        IN       VARCHAR2,
    M_PARAMETERS  IN       VARCHAR2  DEFAULT  NULL
)  RETURN  REPLY;
```

参数详解

  * C

使用的 SMTP 连接。

  * SENDER

发件人邮箱。

  * M_PARAMETERS

可选参数。公认的几种 SMTP 的扩展将会用到 MAIL FROM 和 RCPT TO 的额外参数，详见 RFC1869 第 6 部分，格式要求必须符合
"XXX=XXX (XXX=XXX  
....)"。



返回值

一行 SMTP 服务器的回复。

  8. RCPT



用于告知 SMTP 服务器收件人信息。

语法如下：

```
PROCEDURE RCPT (
	C           		IN OUT 			CONNECTION,
	RECIPIENTS   		IN           	VARCHAR2,
	R_PARAMETERS  		IN          	VARCHAR2 	DEFAULT NULL
);

FUNCTION  RCPT  (
    C             IN  OUT  NOCOPY   CONNECTION,
    RECIPIENTS    IN                VARCHAR2,
    R_PARAMETERS  IN                VARCHAR2  DEFAULT  NULL
)RETURN  REPLY;
```

参数详解

  * C

使用的 SMTP 连接。

  * RECIPIENTS

收件人邮箱。

  * R_PARAMETERS

可选参数。公认的几种 SMTP 的扩展将会用到 MAIL FROM 和 RCPT TO 的额外参数，详见 RFC1869 第 6 部分，格式要求必须符合
"XXX=XXX (XXX=XXX  
....)"。



返回值

一行 SMTP 服务器的回复。

  9. OPEN_DATA



用于告知 SMTP 服务器开始发送数据。只有使用过该过程才可以发送数据。

语法如下：

```
PROCEDURE OPEN_DATA (
	C			IN OUT		CONNECTION
);

FUNCTION  OPEN_DATA  (
    C      IN  OUT  NOCOPY  CONNECTION
)  RETURN  REPLY;
```

参数详解

  * C

使用的 SMTP 连接。



返回值

一行 SMTP 服务器的回复。

  10. WRITE_DATA



用于发送邮件内容（VARCHAR 类型）。

语法如下：

```
PROCEDURE WRITE_DATA (
	C			IN OUT		CONNECTION, 
	DATA		IN			VARCHAR2
);
```

参数详解

  * C

使用的 SMTP 连接。

  * DATA

发送的内容，VARCHAR 类型。



  11. WRITE_RAW_DATA



用来发送邮件内容（VARBINARY 类型）。

语法如下：

```
PROCEDURE WRITE_RAW_DATA (
	C     		IN OUT 		CONNECTION,
	DATA  		IN 			VARBINARY
);
```

参数详解

  * C

使用的 SMTP 连接。

  * DATA

邮件内容，VARBINARY 类型。



  12. CLOSE_DATA



关闭数据流，告知服务器数据发送完毕。

语法如下：

```
PROCEDURE CLOSE_DATA (
	C     		IN OUT 		CONNECTION
);

FUNCTION CLOSE_DATA(
	C	IN OUT	NOCOPY		CONNECTION
)RETURN	REPLY;
```

参数详解

  * C

所使用 SMTP 连接。



返回值

一行 SMTP 服务器的回复。

  13. QUIT



用来结束会话。

语法如下：

```
PROCEDURE QUIT (
	C  			IN OUT 		CONNECTION
);

FUNCTION  QUIT(
    C  IN  OUT  NOCOPY  CONNECTION
)RETURN  REPLY;
```

参数详解

  * C

使用的 SMTP 连接。



返回值

一行 SMTP 服务器的回复。

  14. CLOSE_CONNECTION



关闭 SMTP 连接。

语法如下：

```
PROCEDURE CLOSE_CONNECTION (
	C     		IN OUT 		CONNECTION
);
```

参数详解

  * C



所需要关闭的 SMTP 连接。

## 34.2 应用实例

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES(1, 'UTL_SMTP');
SP_CREATE_SYSTEM_PACKAGES(1, 'UTL_TCP');
SP_CREATE_SYSTEM_PACKAGES(1, 'UTL_RAW');
```

举一个使用 UTL_SMTP 包发邮件的例子。发件人 yhx@dameng.shanghai，收件人 gyf@dameng.shanghai，发送内容为邮件头部信息(收件人、发件人、发送时间、主题、编码等)，重复 10 次的 “达梦数据库第 i 行”。

```
DECLARE
  C UTL_SMTP.CONNECTION;	//声明连接
  MSG VARCHAR2(4000);
BEGIN
  C := UTL_SMTP.OPEN_CONNECTION('192.168.0.212');	//开启连接
  UTL_SMTP.HELO(C, '192.168.0.212');	//握手
  UTL_SMTP.COMMAND(C, 'AUTH LOGIN');	//准备登陆
  UTL_SMTP.COMMAND(C,UTL_RAW.CAST_TO_VARCHAR2(UTL_ENCODE.BASE64_ENCODE(UTL_RAW.CAST_TO_RAW('yhx@DAMENG.SHANGHAI'))));	//用户名
  UTL_SMTP.COMMAND(C,UTL_RAW.CAST_TO_VARCHAR2(UTL_ENCODE.BASE64_ENCODE(UTL_RAW.CAST_TO_RAW('888888'))));	//密码
  UTL_SMTP.MAIL(C, 'yhx@DAMENG.SHANGHAI');	//发件人
  UTL_SMTP.RCPT(C, 'GYF@DAMENG.SHANGHAI');	//收件人
  UTL_SMTP.OPEN_DATA(C);	//开始发送邮件内容
  MSG := 'CONTENT-TYPE: TEXT/PLAIN; CHARSET=GB2312' || UTL_TCP.CRLF || 'DATE:' ||
  TO_CHAR(SYSDATE, 'MM DD YYYY HH24:MI:SS') || UTL_TCP.CRLF || 'FROM:' ||
  'YHX@DAMENG.SHANGHAI' || UTL_TCP.CRLF || 'SUBJECT: HOW TO USE SMTP' || UTL_TCP.CRLF ||
  'TO: ' || 'GYF@DAMENG.SHANGHAI' ||  UTL_TCP.CRLF ||
  'CONTENT-TYPE: TEXT/PLAIN; CHARSET=GB2312' || UTL_TCP.CRLF ;
 	//MSG为邮件头信息, 邮件头信息需要与正文信息, 通过UTL_TCP.CRLF来分隔开
  UTL_SMTP.WRITE_RAW_DATA(C, UTL_RAW.CAST_TO_RAW(MSG));	//发送邮件头信息
  UTL_SMTP.WRITE_DATA(C, UTL_TCP.CRLF ||'达梦数据库SMTP包邮件示例:' || UTL_TCP.CRLF);	 //发送邮件正文信息
  FOR I IN 1 .. 10 LOOP
        UTL_SMTP.WRITE_DATA(C, '达梦数据库DM 第' || i || '行' || UTL_TCP.CRLF);
  END LOOP;
  UTL_SMTP.CLOSE_DATA(C);	//邮件内容发送完毕
  UTL_SMTP.QUIT(C);	//结束会话
  END;
```

所收到的邮件内容(包含 msg 中所写的收件人，发件人，时间信息)：

![邮件内容.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240816114123OGP26FD1HDTJPCW7LN)

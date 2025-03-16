

# DBMS_PIPE 包

DBMS_PIPE 包用于同一实例的不同会话之间通过管道进行通信。这里的管道(PIPE)类似于 UNIX 系统的管道，但它不是采用操作系统机制实现的。管道信息被存储在本地消息缓冲区中，当关闭实例时会丢失管道信息。

管道的发送端和接收端都有相应的本地消息缓冲区进行接收和发送消息。DBMS_PIPE 包中函数的使用流程大致为：首先，发送端创建（CREATE_PIPE）管道、打包（PACK_MESSAGE）管道消息、发送（SEND_MESSAGE）消息；其次，接收端接收（RECEIVE_MESSAGE）消息、取出（UNPACK_MESSAGE）消息。

## 13.1 使用前提

DBMS_PIPE 包的使用依赖 DBMS_XMLGEN 包，而 DBMS_XMLGEN 包的使用依赖 DBMS_LOB 包。所以，在创建 DBMS_PIPE 包之前，请先成功创建 DBMS_XMLGEN 包和 DBMS_LOB 包。

## 13.2 相关方法

  1. CREATE_PIPE



显式创建一个公用或者私有管道。

语法如下：

```
DBMS_PIPE.CREATE_PIPE(
	PIPENAME		IN		VARCHAR2,
	MAXPIPESIZE		IN		INTEGER		DEFAULT 8192,
	IS_PRIVATE		IN		BOOLEAN		DEFAULT TRUE
)RETURN INTEGER;
```

参数详解

  * pipename

指定管道的名称。

  * maxpipesize

指定管道消息的最大尺寸。取值范围：8KB~2GB。

  * BIS_private

指定管道类型。TRUE 表示建立私有管道，FALSE 建立公有管道。公用管道是指所有数据库用户都可以访问的管道，而私有管道只有建立管道的数据库用户才能访问。



返回值

如果该函数返回 0，则表示建立管道成功，否则表示建立管道失败。

使用说明：

通过 CREATE_PIPE 创建的管道的方法为显式创建；在向指定的管道发送消息的过程中，如果指定的管道不存在，则系统会隐式创建管道，这种方法称为隐式创建。

  2. NEXT_ITEM_TYPE



获取本地消息缓冲区中下一条消息的数据类型，在调用了 RECEIVE_MESSAGE 之后调用该函数。

语法如下：

```
DBMS_PIPE. NEXT_ITEM_TYPE RETURN INTEGER;
```

返回值

返回 0，则表示管道没有任何消息。

返回 6，则表示下一项的数据类型为 NUMBER。

返回 9，则表示下一项的数据类型为 VARCHAR2。

返回 12，则表示下一项的数据类型为 DATE。

返回 23，则表示下一项的数据类型为 BINARY。

  3. REMOVE_PIPE



删除指定的管道。

语法如下：

```
DBMS_PIPE.REMOVE_PIPE (
	PIPENAME	IN		VARCHAR2
)RETURN INTEGER;
```

参数详解

  * pipename

管道名称。



使用说明:

  1. 对于隐式创建的管道来说，管道为空时自动被删除，不需要调用该函数。
  2. 对于显式创建的管道来说，只能通过调用 REMOVE_PIPE 或者关闭数据库实例来删除管道。



返回值

0：删除成功

其他错误码：删除失败

  4. RESET_BUFFER



清空本地发送消息缓冲区。因为一个会话中所有的管道都共享一个本地管道缓冲区，所以为防止将本地缓存中原有的数据发送到管道中，在发送消息之前应该复位管道缓冲区。

语法如下：

```
DBMS_PIPE.RESET_BUFFER;
```

  5. PURGE



清空指定管道中的消息。

语法如下：

```
DBMS_PIPE.PURGE (
	PIPENAME	IN		VARCHAR2
);
```

参数详解

  * pipename

管道名称。



  6. PACK_MESSAGE



在本地消息缓冲区中指定消息打包。

为了给管道发送信息，首先需要使用过程 PACK_MESSAGE 将本地消息缓冲区中的指定消息打包好，然后使用过程 SEND_MESSAGE 将打包好的消息发送到管道。

语法如下：

```
DBMS_PIPE.PACK_MESSAGE (ITEM  IN  VARCHAR);
DBMS_PIPE.PACK_MESSAGE (ITEM  IN  INT);
DBMS_PIPE.PACK_MESSAGE (ITEM  IN  BIGINT);
DBMS_PIPE.PACK_MESSAGE (ITEM  IN  DOUBLE);
DBMS_PIPE.PACK_MESSAGE (ITEM  IN  NUMBER);
DBMS_PIPE.PACK_MESSAGE (ITEM  IN  DATE);
DBMS_PIPE.PACK_MESSAGE_ROWID (ITEM  IN  ROWID);
```

参数详解

  * item

指定管道消息。其输入值可以是字符，数字，日期等多种数据类型。



  7. RECEIVE_MESSAGE



接收指定管道中的消息到本地消息缓冲区。

语法如下：

```
DBMS_PIPE.RECEIVE_MESSAGE (
	PIPENAME	IN		VARCHAR2,
	TIMEOUT		IN		INTEGER		DEFAULT MAXWAIT
)RETURN INTEGER;
```

参数详解

  * pipename

管道名称。

  * timeout

最大等待时间。取值范围 0~86400000，单位：秒。



返回值

返回 0，表示接收消息成功。

返回 1，则表示出现超时。

返回 2，则表示本地缓冲区不能容纳管道消息。

返回 3，则表示发生中断。

使用说明：

  1. 接收之后，消息从管道删除。
  2. 一条消息只能被接收一次。
  3. 对于隐式创建的管道来说，管道中最后一条消息被删除后，该管道被自动删除。



  8. SEND_MESSAGE



向指定管道上发送一条消息。

语法如下：

```
DBMS_PIPE.SEND_MESSAGE (
	PIPENAME		IN		VARCHAR2,
	TIMEOUT			IN		INTEGER		DEFAULT MAXWAIT,
	MAXPIPESIZE		IN		INTEGER		DEFAULT 8192
) RETURN INTEGER;
```

参数详解

  * pipename

管道名称。

  * timeout

最大等待时间。取值范围 0~86400000，单位：秒。

  * maxpipesize

指定管道消息的最大尺寸。取值范围 8KB~2GB。



使用说明：

  1. 消息是通过调用 PACK_MESSAGE 生成并存储在本地缓冲区内。
  2. 如果指定的管道不存在，则自动隐式创建。



返回值

返回 0，表示接收消息成功。

返回 1，表示出现超时。

返回 2，则表示本地缓冲区不能容纳管道消息。

返回 3，则表示发生中断。

  9. UNIQUE_SESSION_NAME



返回一个会话的唯一标示名称。对于同一会话来说，其值不会改变。

语法如下：

```
DBMS_PIPE.UNIQUE_SESSION_NAME 
RETURN VARCHAR2;
```

  10. UNPACK_MESSAGE



用于取出消息缓冲区中的内容。在使用函数 RECEIVE_MESSAGE 将管道消息接收到本地消息缓冲区中之后，应该使用过程 UNPACK_MESSAGE 取得本地消息缓冲区的消息。

当使用过程 UNPACK_MESSAGE 取出消息缓冲区的消息时，每次只能取出一条消息。如果要取出多条消息，则需要多次调用过程 UNPACK_MESSAGE。

语法如下：

```
DBMS_PIPE.UNPACK_MESSAGE (ITEM  OUT VARCHAR2);
DBMS_PIPE.UNPACK_MESSAGE (ITEM  OUT INT);
DBMS_PIPE.UNPACK_MESSAGE (ITEM  OUT BIGINT);
DBMS_PIPE.UNPACK_MESSAGE (ITEM  OUT DOUBLE);
DBMS_PIPE.UNPACK_MESSAGE (ITEM  OUT NUMBER);
DBMS_PIPE.UNPACK_MESSAGE (ITEM  OUT DATE);
DBMS_PIPE.UNPACK_MESSAGE_ROWID (ITEM  OUT ROWID);
```

参数详解

  * item

指定管道消息，其输入值可以是字符，数字，日期等多种数据类型。



## 13.3 动态视图

V$DB_PIPES

记录管道的相关信息。

|**列名**|**类型**|**说明**|
|--|--|--|
|NAME|VARCHAR(30) NOT NULL|管道的名字|
|OWNERID|BIGINT NOT NULL|管道拥有者 ID|
|TYPE|VARCHAR(7)|管道类型:PUBLIC/PRIVATE|
|PIPE_SIZE|BIGINT|管道的长度|


## 13.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_LOB');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_XMLGEN');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_PIPE');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE和PRINT需要设置这条语句，才能打印出消息
```

举一个两个会话之间通信的实例。

第一步，第一个会话发送消息。该会话连接的用户名和密码为 SYSDBA/*****。

```
declare
v_pipename varchar2(30):='pipe2';
v_status integer;
begin
DBMS_PIPE.PURGE(v_pipename);
DBMS_PIPE.RESET_BUFFER;
v_status:=DBMS_PIPE.create_pipe(v_pipename,8192,FALSE);
if v_status !=0 then
raise_application_error(-20099, 'create_pipe error status = '|| v_status);
end if;
dbms_pipe.pack_message(' hello,this is sending process!');
v_status:=dbms_pipe.send_message(v_pipename);
if v_status !=0 then
dbms_output.put_line('error!');
end if;
end;
/
```

第二步，第二个会话接收消息。该会话连接的用户名和密码为 USER01/USER012345。

```
declare
v_pipename varchar2(30):='pipe2';
v_status integer;
v_msg varchar2(20);
begin
v_status:=dbms_pipe.receive_message(v_pipename);
if v_status !=0 then
dbms_output.put_line('error');
end if;
dbms_pipe.unpack_message(v_msg);
print v_msg;
end;
/
```

结果打印出：hello,this is sending process!

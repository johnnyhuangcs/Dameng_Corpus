

# DBMS_OUTPUT包

dbms_output 系统包提供了将文本行写入内存、供以后提取和显示的功能。

## 12.1 相关方法

1．DBMS_OUTPUT.DISABLE

禁用 DBMS_OUTPUT 包。

语法如下：

```
DBMS_OUTPUT.DISABLE
```

举例说明

```
DBMS_OUTPUT. DISABLE(); //禁用DBMS_OUTPUT包
```

2．DBMS_OUTPUT.ENABLE

启用 DBMS_OUTPUT 包，并设置缓冲区大小。

语法如下：

```
DBMS_OUTPUT.ENABLE (
	BUFFER_SIZE		IN		INT		DEFAULT	20000
)
```

参数详解

  * buffer_size

指定缓冲区大小，以字节为单位，默认值为 20000。当指定的 buffer_size 小于 2000 时，实际 buffer_size 为
2000；当指定的 buffer_size 大于 1000000 时，实际 buffer_size 为 1000000。



举例说明

```
DBMS_OUTPUT.ENABLE(); //启用DBMS_OUTPUT包，并指定缓冲区大小为10000
```

3．DBMS_OUTPUT.PUT_LINE

输出字符串到缓冲区。DIsql 中需要设置 SET SERVEROUTPUT ON;语句才可以打印消息到客户端。

语法如下：

```
DBMS_OUTPUT.PUT_LINE(
	STR		IN		VARCHAR
)
```

参数详解

  * STR

输出的字符串，VARCHAR 类型。



4．DBMS_OUTPUT.GET_LINE

从缓冲区中读取一行信息。

语法如下：

```
DBMS_OUTPUT.GET_LINE(
	LINE		OUT		VARCHAR, 
	STATUS		OUT		INT
)
```

参数详解

  * LINE

缓冲区中的内容。

  * STATUS

0 或 1。读取成功返回 0，反之为 1。



5．DBMS_OUTPUT.GET_LINES

从打印缓冲区中检索行数组。

语法如下：

```
DBMS_OUTPUT.GET_LINES(
	LINES		OUT		CHARARR, 
	NUMLINES		IN OUT		INT
)
```

语法如下：

```
DBMS_OUTPUT.GET_LINES(
	LINES		OUT		DBMSOUTPUT_LINESARRAY, 
	NUMLINES		IN OUT		INT
)
```

参数详解

  * LINES

返回已经缓存的行数组。每行的最大大小为 32767 字节。

  * NUMLINES

输入需要获取的行数，输出实际获取的行数。如果输出的行数小于需要获取的行数则表明缓冲区中已经没有剩余的数据了。



6．DBMS_OUTPUT.PUT

向行缓冲区中写入部分数据。

语法如下：

```
DBMS_OUTPUT.PUT(
	ITEM		IN		VARCHAR2
)
```

参数详解

  * ITEM

向行缓冲区中写入的内容。



7．DBMS_OUTPUT.NEW_LINE

向行缓冲区中放置一个行结束标记。

语法如下：

```
DBMS_OUTPUT.NEw_LINE()
```

## 12.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

例 放入内容到缓冲区，并从缓冲区中读出

```
//启用DBMS_OUTPUT包
DBMS_OUTPUT.ENABLE();
//使用put_line和get_line
declare
buffer varchar;
status int;
begin
	dbms_output.put_line('达梦数据库有限公司');
	dbms_output.get_line(buffer,status); 
	dbms_output.put_line('BUFFER:'||buffer||' status:'||status);
	commit;
end;
/
//打印结果：BUFFER:达梦数据库有限公司 status:0
```

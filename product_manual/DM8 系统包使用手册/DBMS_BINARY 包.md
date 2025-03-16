

# DBMS_BINARY 包

DBMS_BINARY 系统包用于读写二进制流，实现从一个二进制流指定位置开始对基本数据类型的读写，包括 char、varchar、tinyint、smallint、int、bigint、float、double 数据类型。

## 5.1 相关方法

DBMS_BINARY 包所支持的 8 个过程和 8 个函数，分别用来在二进制流中存取数据。这些过程和函数通过调用相应系统内部函数来实现存取数据的过程。如下详细介绍各过程和函数：

  1. binary_get_char



返回从二进制流 vb 中偏移 offset 开始的一个 char 类型数据。

语法如下：

```
function binary_get_char(
  vb		varbinary,
  offset	int
);
```

返回值

char 类型数据。

  2. binary_get_varchar



返回从二进制流 vb 中偏移 offset 开始的一个长度为 length 的 varchar 类型数据。

语法如下：

```
function binary_get_varchar(
  vb		varbinary,
  offset	int,
  length	int
);
```

返回值

varchar 类型数据

  3. binary_get_tinyint



返回从二进制流 vb 中偏移 offset 开始的一个 tinyint 型数据。

语法如下：

```
function binary_get_tinyint(
  vb		varbinary,
  offset	int
);
```

返回值

tinyint 类型数据。

  4. binary_get_smallint



返回从二进制流 vb 中偏移 offset 开始的一个 smallint 型数据。

语法如下：

```
function binary_get_smallint(
  vb		varbinary,
  offset	int
);
```

返回值

smallint 类型数据。

  5. binary_get_int



返回从二进制流 vb 中偏移 offset 开始的一个 int 型数据。

语法如下：

```
function binary_get_int(
  vb		varbinary,
  offset	int
);
```

返回值

int 类型数据。

  6. binary_get_bigint



返回从二进制流 vb 中偏移 offset 开始的一个 bigint 型数据。

语法如下：

```
function binary_get_bigint(
  vb		varbinary,
  offset	int
);
```

返回值

bigint 类型数据。

  7. binary_get_float



返回从二进制流 vb 中偏移 offset 开始的一个 float 型数据。

语法如下：

```
function binary_get_float(
  vb		varbinary,
  offset	int
);
```

返回值

float 类型数据。

  8. binary_get_double



返回从二进制流 vb 中偏移 offset 开始的一个 double 型数据。

语法如下：

```
function binary_get_double(
  vb		varbinary,
  offset	int
);
```

返回值

double 类型数据。

  9. binary_set_char



从二进制流 vb 中偏移 offset 开始的位置写入一个 char 型数据。

语法如下：

```
procedure binary_set_char(
  vb		in out varbinary,
  offset	int, 
  value		char
);
```

**说明：**binary_set_char 仅支持 value 为单字符，若需要写入多字符请使用 binary_set_varchar

  10. binary_set_varchar



从二进制流 vb 中偏移 offset 开始的位置写入 varchar 型数据。

语法如下：

```
procedure binary_set_varchar(
  vb		in out varbinary,
  offset	int,
  value		varchar
);
```

  11. binary_set_tinyint



从二进制流 vb 中偏移 offset 开始的位置写入一个 tinyint 型数据。

语法如下：

```
procedure binary_set_tinyint(
  vb		in out varbinary,
  offset	int,
  value		tinyint
);
```

  12. binary_set_smallint



从二进制流 vb 中偏移 offset 开始的位置写入一个 smallint 型数据。

语法如下：

```
procedure binary_set_smallint(
  vb		in out varbinary,
  offset	int,
  value		smallint
);
```

  13. binary_set_int



从二进制流 vb 中偏移 offset 开始的位置写入一个 int 型数据。

语法如下：

```
procedure binary_set_int(
  vb		in out varbinary,
  offset	int,
  value		int
);
```

  14. binary_set_bigint



从二进制流 vb 中偏移 offset 开始的位置写入一个 bigint 型数据。

语法如下：

```
procedure binary_set_bigint(
  vb		in out varbinary,
  offset	int,
  value		bigint
);
```

  15. binary_set_float



从二进制流 vb 中偏移 offset 开始的位置写入一个 float 型数据。

语法如下：

```
procedure binary_set_float(
  vb		in out varbinary,
  offset 	int,
  value		float
);
```

  16. binary_set_double



从二进制流 vb 中偏移 offset 开始的位置写入一个 double 型数据。

语法如下：

```
procedure binary_set_double(
  vb		in out varbinary,
  offset	int,
  value		double
);
```

## 5.2 参数说明

因为 DBMS_BINARY 包所支持的 8 个过程和 8 个函数参数意义相同，统一说明如下：

  * vb

用户输入的二进制流。

  * offset

向二进制流中写入数据或者从二进制流取出数据时的偏移量。

  * length

表示从二进制流指定偏移位置开始取多少个字符。

  * value

待写入的数据。



## 5.3 错误处理

在 DBMS_BINARY 包方法的执行过程中，按下列顺序进行参数检查：

  1. **空值处理**



如果输入参数中存在空值，则结果返回空值。特别的，参数 LENGTH 为 0 时视为 LENGTH 为 NULL。

例 输入的参数中存在空值。

```
binary_get_char (vb varbinary, offset int)
select dbms_binary.binary_get_char ('ABCDEF ', null);
```

结果：NULL

  2. **非法参数**



如果参数 OFFSET 或 LENGTH 为负数，或者偏移量加上所取数据类型的长度大于二进制流的长度，报非法参数错误 EC_RN_INVALID_ARG_DATA，错误码：-6803。

例 输入偏移量为负数。

```
binary_get_char (vb varbinary, offset int)

select dbms_binary.binary_get_char ('ABCDEF', -11);
```

执行结果报错：输入参数非法。

## 5.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_BINARY');
SET SERVEROUTPUT ON;  //PRINT需要设置这条语句，才能打印出消息
```

例 1 使用 binary_set_tinyint 过程写入一个 tinyint 类型数据，并使用 binary_get_tinyint 函数输出该 tinyint 类型数据。

```
DECLARE 
	BIN VARBINARY(50);
	VAL TINYINT;
BEGIN
	BIN = 'ABCDEF87';
	VAL = 127;
	DBMS_BINARY.BINARY_SET_TINYINT(BIN,0,VAL);
	VAL = DBMS_BINARY.BINARY_GET_TINYINT(BIN,0);
	PRINT VAL;
END;
/
```

结果如下：

```
127
```

例 2 使用 binary_set_varchar 过程写入一个 varchar 类型数据，并使用 binary_get_varchar 函数输出该 varchar 类型数据。

```
DECLARE
	BIN VARBINARY(50);
	VAL VARCHAR;
BEGIN
	BIN = 'ABCDEF87';
	DBMS_BINARY.BINARY_SET_VARCHAR(BIN,0,'AAAA');
	VAL = DBMS_BINARY.BINARY_GET_VARCHAR(BIN,0,4);
	PRINT VAL;
END;
/
```

结果如下：

```
AAAA
```

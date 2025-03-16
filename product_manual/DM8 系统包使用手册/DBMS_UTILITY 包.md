

# DBMS_UTILITY 包

DBMS_UTILITY 包用于查看异常信息。

## 21.1 数据类型

DBMS_UTILITY 定义了一种索引表类型 LNAME_ARRAY，此类型存储长名称，包括完全限定的属性名称。LNAME_ARRAY 类型定义如下：

语法如下：

```
TYPE LNAME_ARRAY IS TABLE OF VARCHAR2(4000) INDEX BY INTEGER;
```

## 21.2 相关方法

  1. FORMAT_ERROR_STACK



获取 DMSQL 程序异常的堆栈信息。

语法如下：

```
FUNCTION FORMAT_ERROR_STACK (
)RETURN VARCHAR2;
```

  2. GET_HASH_VALUE



对于指定的字符串，返回范围在[base, base+hase_size-1]的散列值。

语法如下：

```
FUNCTION  GET_HASH_VALUE(
	NAME		VARCHAR,
	BASE		INT,
	HASH_SIZE	INT
)RETURN INT;
```

参数详解

  * NAME

指定的要被 HASH 的字符串

  * BASE

返回的 HASH 值的基准值，即返回值的最小值

  * HASH_SIZE

指定的 HASH 表的大小



  3. GET_TIME



返回一个代表当前时间的以 1/100 秒为精度的值，该值并不真正表示当前的时间值，此方法常用来计算两个时间点的间隔。

语法如下：

```
FUNCTION GET_TIME() RETURN NUMBER;
```

参数详解

无

  4. FORMAT_CALL_STACK



返回 DMSQL 程序当前调用的堆栈，包括每层的栈帧方法名。

语法如下：

```
FUNCTION FORMAT_CALL_STACK() RETURN VARCHAR2;
```

  5. FORMAT_ERROR_BACKTRACE



返回 DMSQL 程序发生异常时的堆栈信息，包括每层的栈帧地址和方法名。

语法如下：

```
FUNCTION FORMAT_ERROR_BACKTRACE() RETURN VARCHAR2;
```

  6. COMMA_TO_TABLE



将用户输入的字符串依据逗号进行分割，返回分割后字符串的索引表。

语法如下：

```
PROCEDURE COMMA_TO_TABLE(
   LIST		IN		VARCHAR2,
   TABLEN	OUT 	BINARY_INTEGER, 
   TAB		OUT 	LNAME_ARRAY
);
```

参数详解

  * LIST

输入参数，待分割的字符串列表，若其中的单个字符串未用双引号括起来，则仅支持该字符串格式为 a[.b]*，其中的 A 和 B
均为不包含特殊字符的字符串；若列表中的单个字符串需要包含部分特殊字符，则需要将该字符串用双引号括起来，COMMA_TO_TABLE
不会对双引号中的字符串做字符转换处理。

  * TABLEN

输出参数，索引表中的成员个数。

  * TAB

输出参数，对用户输入的字符串列表依据逗号进行分割后得到的索引表。



使用说明：

输入的字符串参数 LIST 不能为空，且多个字符串之间需用逗号进行分隔。双引号内的逗号视作正常字符处理。

  7. TABLE_TO_COMMA



TABLE_TO_COMMA 是 COMMA_TO_TABLE 的逆过程，TABLE_TO_COMMA 将字符串的索引表合并成以逗号为分隔符的字符串列表。

语法如下：

```
PROCEDURE TABLE_TO_COMMA(
   TAB		IN		LNAME_ARRAY,
   TABLEN	OUT		BINARY_INTEGER, 
   LIST		OUT		VARCHAR2
);
```

参数详解

  * TAB

输入参数，待合并字符串的索引表。

  * TABLEN

输出参数，索引表中的成员个数。

  * LIST

输出参数，将索引表中的字符串以逗号为分隔符进行合并后的字符串列表。



使用说明：

输入的索引表的最后一个成员必须为 NULL，否则报错。

  8. EXEC_DDL_STATEMENT



执行 DDL 语句。

语法如下：

```
PROCEDURE EXEC_DDL_STATEMENT(

  PARSE_STRING IN VARCHAR2

);
```

参数详解

  * PARSE_STRING

输入参数，待执行的 DDL 语句。



## 21.3 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程 SP_CREATE_SYSTEM_PACKAGES 创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES(1,'DBMS_OUTPUT');
SP_CREATE_SYSTEM_PACKAGES(1,'DBMS_UTILITY');
SET SERVEROUTPUT ON;   //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

例 1 COMMA_TO_TABLE 过程的使用。

```
DECLARE
	LEN INTEGER := 0;
	ARRAY1 DBMS_UTILITY.LNAME_ARRAY;
	P_LIST VARCHAR2 := 'HOPE,HOPE.WORLD.PEACE,"#*!:,\A123("';
BEGIN
	DBMS_UTILITY.COMMA_TO_TABLE(P_LIST, LEN, ARRAY1);
	FOR I IN 1..LEN LOOP
		DBMS_OUTPUT.PUT_LINE(ARRAY1[I]);
	END LOOP;
END;
```

执行结果如下：

```
HOPE
HOPE.WORLD.PEACE
"#*!:,\A123("
```

例 2 TABLE_TO_COMMA 过程的使用。

```
DECLARE
	LEN INTEGER := 0;
	ARRAY1 DBMS_UTILITY.LNAME_ARRAY;
	STR VARCHAR(200) := '';
BEGIN
	ARRAY1[1] := 'AA*($#^';
	ARRAY1[2] := 'BB.,123';
	ARRAY1[3] := NULL;
	DBMS_UTILITY.TABLE_TO_COMMA(ARRAY1, LEN, STR);
	DBMS_OUTPUT.PUT_LINE(STR);
END;
```

执行结果如下：

```
AA*($#^,BB.,123
```

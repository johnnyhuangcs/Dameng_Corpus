

# DBMS_SQL 包

支持在 pl/sql 中使用动态 sql 语句。

## 18.1 相关方法

DBMS_SQL 包中所用到的结构定义如下：

```
TYPE binary_float_table IS TABLE OF FLOAT INDEX BY INTEGER;
TYPE blob_table IS TABLE OF BLOB INDEX BY INTEGER;
TYPE clob_table IS TABLE OF CLOB INDEX BY INTEGER;
type date_table IS TABLE OF DATETIME INDEX BY INTEGER;
TYPE desc_tab IS TABLE OF desc_rec INDEX BY INTEGER;
TYPE desc_tab2 IS TABLE OF desc_rec2 INDEX BY INTEGER;
TYPE desc_tab3 IS TABLE OF desc_rec3 INDEX BY INTEGER;
TYPE interval_day_to_second_Table IS TABLE OF INTERVAL DAY TO SECOND INDEX BY INTEGER;
TYPE interval_year_to_month_table IS TABLE OF INTERVAL YEAR TO MONTH INDEX BY INTEGER;
TYPE Int_Table IS TABLE OF INT INDEX BY INTEGER;
TYPE number_table IS TABLE OF NUMBER INDEX BY INTEGER;
TYPE time_table IS TABLE OF TIME INDEX BY INTEGER;
TYPE time_with_time_zone_table IS TABLE OF TIME WITH TIME ZONE INDEX BY INTEGER;
TYPE timestamp_table IS TABLE OF DATETIME INDEX BY INTEGER;
TYPE timestamp_with_ltz_table IS TABLE OF TIMESTAMP WITH LOCAL TIME ZONE INDEX BY INTEGER;
TYPE timestamp_with_time_zone_Table IS TABLE OF TIMESTAMP WITH TIME ZONE INDEX BY INTEGER;
TYPE varchar2_table IS TABLE OF VARCHAR2(2000) INDEX BY INTEGER;
TYPE varchar2a IS TABLE OF VARCHAR2(32767) INDEX BY INTEGER;
```

DESC_REC 的结构如下：

```
TYPE DESC_REC IS RECORD(
COL_TYPE INTEGER := 0,
COL_MAX_LEN INTEGER := 0,
COL_NAME VARCHAR2(128) := '',
COL_NAME_LEN INTEGER := 0,
COL_SCHEMA_NAME VARCHAR2(128) := '',
COL_SCHEMA_NAME_LEN INTEGER := 0,
COL_PRECISION INTEGER := 0,
COL_SCALE INTEGER := 0,
COL_CHARSETID INTEGER := 0,
COL_CHARSETFORM INTEGER := 0,
COL_NULL_OK BOOLEAN := TRUE);
```

使用 DESCRIBE_COLUMNS 函数可以将查询项的数据类型填充到 DESC_REC 中，其中服务器部分数据类型和 DBMS_SQL 中的数据类型的映射关系如下：

```
TYPECODE_VARCHAR			INTEGER :=   1;
TYPECODE_NUMBER             	INTEGER :=   2;
TYPECODE_INT                	INTEGER :=   3;
TYPECODE_SMALLINT           	INTEGER :=   4;
TYPECODE_TINYINT            	INTEGER :=   5;
TYPECODE_BIGINT             	INTEGER :=   6;
TYPECODE_FLOAT              	INTEGER :=   7;
TYPECODE_VARCHAR2           	INTEGER :=   9;
TYPECODE_DATE               	INTEGER :=  12;
TYPECODE_VARBINARY          	INTEGER :=  23;
TYPECODE_RAW                	INTEGER :=  95;
TYPECODE_CHAR               	INTEGER :=  96;
TYPECODE_MLSLABEL				INTEGER :=  105;
TYPECODE_AOT               	INTEGER :=  109; // TYPECODE_AOT为用户自定义类型
TYPECODE_BLOB               	INTEGER := 113;
TYPECODE_BFILE              	INTEGER := 114;
TYPECODE_CLOB               	INTEGER := 112;
TYPECODE_CFILE              	INTEGER := 115;
TYPECODE_TIME               	INTEGER := 170;
TYPECODE_TIME_TZ            	INTEGER := 171;
TYPECODE_TIMESTAMP          	INTEGER := 180;
TYPECODE_TIMESTAMP_TZ       	INTEGER := 181;
TYPECODE_TIMESTAMP_LTZ      	INTEGER := 232;
TYPECODE_INTERVAL_YM        INTEGER := 182;
TYPECODE_INTERVAL_DS        INTEGER := 183;
TYPECODE_REF                  INTEGER := 110;
TYPECODE_OBJECT             INTEGER := 108;
TYPECODE_VARRAY             INTEGER := 247;
```

DBMS_SQL 包中包含的过程和函数如下详细介绍：

  1. BIND_ARRAY



按照 SQL 语句中变量名将变量值绑定到游标的索引表上。

语法如下：

```
DBMS_SQL.BIND_ARRAY ( 
	CURID	           	IN 		INTEGER, 
	NAME				IN 		VARCHAR2, 
	<TABLE_VARIABLE>	IN 		<DATATYPE>
	[,INDEX1           	IN 		INTEGER, 
	INDEX2             	IN 		INTEGER] 
);
```

参数详解

  * CURID

游标。

  * NAME

变量名称。

  * \<TABLE_VARIABLE>

索引表，\<DATATYPE> 是 DBMS_SQL 包中的索引表类型，如下所示：

◆ binary_float_table

◆ blob_table

◆ clob_table

◆ date_table

◆ interval_day_to_second_Table

◆ interval_year_to_month_table

◆ Int_Table

◆ number_table

◆ time_table

◆ time_with_time_zone_table

◆ timestamp_table

◆ timestamp_with_ltz_table

◆ timestamp_with_time_zone_Table

◆ varchar2_table

◆ varchar2a

  * INDEX1

绑定数组表的起始下标。

  * INDEX2

绑定数组表的结束下标。



  2. BIND_VARIABLE



按照 SQL 语句中变量名将变量值绑定到游标的基本数据类型变量上。

语法如下：

```
DBMS_SQL.BIND_VARIABLE (
	C			IN 		INTEGER,
	NAME		IN 		VARCHAR2,
	VALUE		IN 		<DATATYPE>
);
```

参数详解

  * C

游标。

  * NAME

变量名称。

  * VALUE

变量值。可以为任何类型，类型\<datatype> 是 DBMS_SQL 包中基本数据类型，如下所示：

◆ FLOAT

◆ CLOB

◆ DATETIME

◆ INTERVAL DAY TO SECOND

◆ NUMBER

◆ TIME

◆ TIME WITH TIME ZONE

◆ TIMESTAMP WITH TIME ZONE

◆ VARCHAR

◆ INTERVAL YEAR TO MONTH

◆ INT

◆ SMALLINT

◆ TINYINT

◆ BIGINT

◆ VARBINARY

◆ BLOB



  3. BIND_VARIABLE_RAW



绑定二进制数据类型变量，可通过 OUT_VALUE_SIZE 指定 VARBINARY 长度。

语法如下：

```
DBMS_SQL.BIND_VARIABLE_RAW (
	C			IN 		INTEGER,
	NAME		IN 		VARCHAR2,
	VALUE		IN 		VARBINARY	
	[,OUT_VALUE_SIZE 	IN 	INTEGER]
);
```

参数详解

  * C

游标。

  * NAME

变量名称。

  * VALUE

变量值，数据类型为 VARBINARY。

  * OUT_VALUE_SIZE

指定 VARBINARY 的长度。



  4. BIND_VARIABLE_CHAR



绑定 VARCHAR 数据类型变量，可通过 OUT_VALUE_SIZE 指定 VARCHAR 长度。

语法如下：

```
DBMS_SQL.BIND_VARIABLE_CHAR (
	C			IN 		INTEGER,
	NAME		IN 		VARCHAR2,
	VALUE		IN 		VARCHAR	
	[,OUT_VALUE_SIZE 	IN 	INTEGER]
);
```

参数详解

  * C

游标。

  * NAME

变量名称。

  * VALUE

变量值，数据类型为 VARCHAR。

  * OUT_VALUE_SIZE

指定 VARCHAR 的长度。



  5. CLOSE_CURSOR



关闭所给定的游标。

语法如下：

```
DBMS_SQL.CLOSE_CURSOR (
	C		IN OUT 		INTEGER
);
```

参数详解

  * C

游标。



  6. COLUMN_VALUE



根据列的位置，返回游标中的列值，通常在 FETCH_ROWS 后被调用。

语法如下：

```
DBMS_SQL.COLUMN_VALUE (
	C					IN  		INTEGER,
	POSITION			IN  		INTEGER,
	VALUE				OUT 		<DATATYPE>
	[,COLUMN_ERROR		OUT 		NUMBER] 
	[,ACTUAL_LENGTH		OUT 		INTEGER]
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中列的位置。

  * VALUE

列值。\<datatype>可以为任何类型。

  * COLUMN_ERROR

仅语法兼容，无实际意义。

  * ACTUAL_LENGTH

仅语法兼容，无实际意义。



  7. COLUMN_VALUE_CHAR



根据列的位置，返回数据类型为 CHAR 的列值。

语法如下：

```
DBMS_SQL.COLUMN_VALUE_CHAR (
	C               		IN  		INTEGER,
	POSITION        		IN  		INTEGER,
	VALUE           		OUT 		CHAR
	[,COLUMN_ERROR    		OUT 		NUMBER]
	[,ACTUAL_LENGTH   		OUT 		INTEGER]
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中列的位置。

  * VALUE

列值，数据类型为 CHAR。

  * COLUMN_ERROR

仅语法兼容，无实际意义。

  * ACTUAL_LENGTH

仅语法兼容，无实际意义。



  8. COLUMN_VALUE_RAW



根据列的位置，返回数据类型为 BINARY 的列值。

语法如下：

```
DBMS_SQL.COLUMN_VALUE_RAW (
	C               		IN  		INTEGER,
	POSITION        		IN  		INTEGER,
	VALUE           		OUT 		BINARY
	[,COLUMN_ERROR    		OUT 		NUMBER]
	[,ACTUAL_LENGTH   		OUT 		INTEGER]
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中列的位置。

  * VALUE

列值，数据类型为 BINARY。

  * COLUMN_ERROR

仅语法兼容，无实际意义。

  * ACTUAL_LENGTH

仅语法兼容，无实际意义。



  9. DEFINE_ARRAY



定义索引表类型的接收列，用于接收 fetch 结果集，一次可获取多行数据。

语法如下：

```
DBMS_SQL.DEFINE_ARRAY (
	C           			IN 		INTEGER, 
	POSITION    			IN 		INTEGER,
	<TABLE_VARIABLE>   		IN 		<DATATYPE>,
	LEAST_FETCH         	IN 		INTEGER, 
	START_OP   				IN 		INTEGER
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中列的位置。

  * \<TABLE_VARIABLE>

索引表，\<DATATYPE> 是 DBMS_SQL 包中的索引表类型，具体可参考 BIND_ARRAY 的参数介绍。

  * LEAST_FETCH

至少 FETCH 的记录行数。

  * START_OP

指定数组表下标，从指定位置开始填充 FETCH 结果集。



  10. DEFINE_COLUMN



定义基本数据类型接收列，用于接收 fetch 结果集，一次获取一行数据。

语法如下：

```
DBMS_SQL.DEFINE_COLUMN (
	C             		IN 		INTEGER,
	POSITION       		IN 		INTEGER,
	COLUMN         		IN 		<DATATYPE>
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中的位置(从 1 开始)。

  * COLUMN

该值所对应的变量，可以为任何类型。



  11. EXECUTE



执行给定游标内的 SQL 语句。

语法如下：

```
DBMS_SQL.EXECUTE (
	C   		IN 		INTEGER
) RETURN INTEGER;
```

参数详解

  * C

游标。



返回值

返回处理的数据记录行数，仅对 INSERT、DELETE、UPDATE 操作有效，对于其他操作返回 0。

  12. EXECUTE_AND_FETCH



执行给定游标内的 SQL 语句，同时将 fetch 数据。

语法如下：

```
DBMS_SQL.EXECUTE_AND_FETCH (
	C       		IN 		INTEGER,
	EXACT 			IN 		BOOLEAN 		DEFAULT FALSE
)RETURN INTEGER;
```

参数详解

  * C

游标。

  * EXACT

仅语法兼容，无实际意义。



  13. FETCH_ROWS



fetch 指定游标中的数据，同时返回 fetch 的行数。为 0 时表示已经取到游标末端。

语法如下：

```
DBMS_SQL.FETCH_ROWS (
	C 			IN 		INTEGER
)RETURN INTEGER;
```

参数详解

  * C

游标。



  14. IS_OPEN



判断游标是否打开，若打开则返回 true，否则返回 false。

语法如下：

```
DBMS_SQL.IS_OPEN (
	C			IN 		INTEGER
)RETURN BOOLEAN;
```

参数详解

  * C

游标。



  15. OPEN_CURSOR



打开一个游标，返回游标号。

语法如下：

```
DBMS_SQL.OPEN_CURSOR 
RETURN INTEGER;
```

  16. PARSE



解析 sql 语句。

语法如下：

```
DBMS_SQL.PARSE (
   C					IN   	INTEGER,
   STATEMENT			IN   	VARCHAR2,
   LANGUAGE_FLAG		IN   	NUMBER
);
```

参数详解

  * C

游标。

  * statement

动态游标所提供的 sql 语句。

  * language_flag

仅语法兼容，无实际意义。



  17. TO_CURSOR_NUMBER



将一个外部游标转化为 dbms_sql 包内部的游标，返回游标号。

语法如下：

```
DBMS_SQL.TO_CURSOR_NUMBER(
	RC 				IN OUT 		SYS_REFCURSOR
)RETURN INTEGER;
```

参数详解

  * RC

外部游标。



  18. TO_REFCURSOR



将 dbms_sql 包内的游标转换为 pl/sql 游标。

语法如下：

```
DBMS_SQL.TO_REFCURSOR(
	CURSOR_NUMBER 	IN OUT  		INTEGER
)RETURN SYS_REFCURSOR;
```

参数详解

  * CURSOR_NUMBER

游标。



  19. DESCRIBE_COLUMNS



获取查询项的描述信息，需要一个执行过查询操作的游标作为输入参数。

语法如下：

```
DBMS_SQL.DESCRIBE_COLUMNS (
	C               	IN  			INTEGER,
	COL_CNT  	   		OUT 			INTEGER,
	DESC_T  		   	OUT 			DESC_TAB
);
```

参数详解

  * C

游标。

  * COL_CNT

查询项数量。

  * DESC_T

查询项描述信息，数据类型为数组表。



  20. VARIABLE_VALUE



获取变量的值，通常需要先执行 bind_variable。

语法如下：

```
DBMS_SQL.VARIABLE_VALUE (
	C               	IN  			INTEGER,
	NAME            	IN  			VARCHAR2,
	VALUE           	OUT 			<DATATYPE>
);
```

参数详解

  * C

游标。

  * NAME

变量名。

  * VALUE

变量值，可以为任何类型。



  21. VARIABLE_VALUE_CHAR



获取数据类型为 VARCHAR 的变量值。

语法如下：

```
DBMS_SQL.VARIABLE_VALUE_CHAR (
	C               	IN  			INTEGER,
	NAME            	IN  			VARCHAR2,
	VALUE          		OUT 			VARCHAR
);
```

参数详解

  * C

游标。

  * NAME

变量名。

  * VALUE

变量值，数据类型为 VARCHAR。



  22. VARIABLE_VALUE_RAW



获取数据类型为 VARBINARY 的变量值。

语法如下：

```
DBMS_SQL.VARIABLE_VALUE_RAW (
	C               	IN  			INTEGER,
	NAME            	IN  			VARCHAR2,
	VALUE           	OUT 			VARBINARY
);
```

参数详解

  * C

游标。

  * NAME

变量名。

  * VALUE

变量值，数据类型为 VARBINARY。



  23. DEFINE_COLUMN_CHAR



定义 VARCHAR 数据类型接收列，用于接收 FETCH 结果集，一次获取一行数据。

语法如下：

```
DBMS_SQL.DEFINE_COLUMN_CHAR(
	C              	IN 			INTEGER,
   	POSITION       	IN 			INTEGER,
   	COLUMN         	IN 			VARCHAR, 
	COLUMN_SIZE    	IN 			INTEGER
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中列的位置。

  * COLUMN

列值。

  * COLUMN_SIZE

可接收的最大列值。



  24. DEFINE_COLUMN_RAW



定义 RAW 数据类型接收列，用于接收 FETCH 结果集，一次获取一行数据。

语法如下：

```
DBMS_SQL.DEFINE_COLUMN_RAW(   
	C              	IN 			INTEGER, 
	POSITION       	IN 			INTEGER, 
	COLUMN         	IN 			RAW,  
	COLUMN_SIZE    	IN 			INTEGER
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中列的位置。

  * COLUMN

列值。

  * COLUMN_SIZE

可接收的最大列值。



  25. DEFINE_COLUMN_LONG



定义 CLOB 数据类型接收列，用于接收 FETCH 结果集，一次获取一行数据。

语法如下：

```
DBMS_SQL.DEFINE_COLUMN_LONG(   
	C              	IN 			INTEGER,   
	POSITION       	IN 			INTEGER
);
```

参数详解

  * C

游标。

  * POSITION

为对应动态 sql 中列的位置。



  26. DESCRIBE_COLUMNS3



获取查询项的描述信息，需要一个执行过查询操作的游标作为输入参数。

DESCRIBE_COLUMNS3 和 DESCRIBE_COLUMNS 的唯一区别是 DESCRIBE_COLUMNS3 在描述时可以增加获取类型名称信息。

语法如下：

```
DBMS_SQL.DESCRIBE_COLUMNS3 (
    c          IN         INTEGER,
    COL_CNT    OUT        INTEGER,
    DESC_T     OUT        DESC_TAB3
);
```

参数详解

  * C

游标。

  * COL_CNT

查询项数量。

  * DESC_T

查询项描述信息，数据类型为数组表。



## 18.2 举例说明

使用包内的过程和函数之前，如果还未创建过例子中的系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1, 'DBMS_SQL');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

下面是利用 DBMS_SQL 动态执行语句的例子。

```
例 1 一次获取一行数据
create table t1(n1 number);
insert into t1 values(1.1),(2.1),(3.2),(4.3);

//非collection类型
declare
	c NUMBER;
	d1 NUMBER;
BEGIN
	c := dbms_sql.OPEN_CURSOR; //打开游标
	dbms_sql.PARSE(c, 'select N1 from sysdba.t1', '1'); //解析sql语句
	dbms_sql.DEFINE_COLUMN(c, 1, d1); //定义列，将来可用column_value获取列值
	dbms_sql.EXECUTE(c); //执行语句
	dbms_sql.FETCH_ROWS(c); //获取结果集
	dbms_sql.COLUMN_VALUE(c, 1, d1);  //获取列值
	dbms_sql.CLOSE_CURSOR(c);  //关闭游标
	dbms_output.put_line(d1);
END;
/

//输出结果：
1.1
```

例 2  一次获取多行数据

```
DECLARE
	c NUMBER;
	d1 DBMS_SQL.NUMBER_TABLE;
BEGIN
	c := dbms_sql.OPEN_CURSOR; //打开游标
	dbms_sql.PARSE(c, 'select N1 from sysdba.t1', '1'); //解析sql语句
	dbms_sql.DEFINE_ARRAY(c, 1, d1,3,1); //定义列，将来可用column_value获取列值
	dbms_sql.EXECUTE(c); //执行语句
	dbms_sql.FETCH_ROWS(c); //获取结果集
	dbms_sql.COLUMN_VALUE(c, 1, d1); //获取列值
	dbms_sql.CLOSE_CURSOR(c); //关闭游标
	dbms_output.put_line(d1(2)); //输出d1中第二条列值
END;
/

//输出结果：
2.1
```

例 3  绑定参数

```
declare
	c NUMBER;
	d1 NUMBER;
BEGIN
		d1 :=9.12;
	c := dbms_sql.OPEN_CURSOR; //打开游标
	dbms_sql.PARSE(c, 'insert into SYSDBA.t1 values(:c1)','1'); //解析sql语句
	dbms_sql.BIND_VARIABLE(C,'c1',d1);
	dbms_sql.EXECUTE(c); //执行语句
	bms_sql.CLOSE_CURSOR(c);  //关闭游标
END;
/

SELECT * FROM T1;
//查询结果：
行号 N1
---------- -----------------------
1          1.1
2          2.1
3          3.2
4          4.3
5          9.12
```

例 4  批量绑定

```
declare
	c NUMBER;
	d1 DBMS_SQL.NUMBER_TABLE;
BEGIN
	d1(1) :=12.3;
	d1(2) :=6.12;
	d1(3) :=8.12;
	d1(4) :=342.12;
  c := dbms_sql.OPEN_CURSOR; //打开游标
  dbms_sql.PARSE(c, 'insert into SYSDBA.t1 values(:c1)', '1'); //解析sql语句
  dbms_sql.BIND_ARRAY(C,'c1',d1);
  dbms_sql.EXECUTE(c); //执行语句
  dbms_sql.CLOSE_CURSOR(c); //关闭游标
END;
/

SELECT * FROM T1;
//查询结果：
行号 N1
---------- -------------------------
1          1.1
2          2.1
3          3.2
4          4.3
5          9.12
6          12.3
7          6.12
8          8.12
9          342.12
```

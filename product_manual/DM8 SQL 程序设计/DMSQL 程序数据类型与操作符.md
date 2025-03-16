

# DMSQL 程序数据类型与操作符

DMSQL 程序支持所有的 DM SQL 数据类型，包括：精确数值数据类型、近似数值数据类型、字符数据类型、多媒体数据类型、一般日期时间数据类型、时间间隔数据类型。

此外，为了进一步提高 DMSQL 程序的程序设计属性，DMSQL 程序还扩展支持了 %TYPE、%ROWTYPE、记录类型、数组类型、集合类型和类类型，用户还可以定义自己的子类型。

## 2.1 常规数据类型

### 2.1.1 数值数据类型

  1. **NUMERIC 类型**



语法如下：

```
NUMERIC[( 精度 [, 标度])]
```

功能：

NUMERIC 数据类型用于存储零、正负定点数。其中：精度是一个无符号整数，定义了总的数字数，精度范围是 1~38，标度定义了小数点右边的数字位数，定义时如省略精度，则缺省是 16。如省略标度，则缺省为 0。一个数的标度不应大于其精度。

例如：NUMERIC(4,1)定义了小数点前面 3 位和小数点后面 1 位，共 4 位的数字，范围在-999.9 到 999.9。所有 NUMERIC 数据类型，如果其值超过精度，达梦数据库返回一个出错信息，如果超过标度，则多余的位截断。

如果不指定精度和标度，缺省精度为 38。

  2. **NUMBER 类型**



语法如下：

```
NUMBER[(精度 [, 标度])]
```

功能：

与 NUMERIC 类型相同。

  3. **DECIMAL/DEC 类型**



语法如下：

```
DECIMAL[(精度 [, 标度])]
DEC[(精度 [, 标度])]
```

功能：

与 NUMERIC 相似。

  4. **BIT 类型**



语法如下：

```
BIT
```

功能：

BIT 类型用于存储整数数据 1、0 或 NULL，可以用来支持 ODBC 和 JDBC 的布尔数据类型。DM 的 BIT 类型与 SQL SERVER2000 的 BIT 数据类型相似。

  5. **INTEGER/INT 类型**



语法如下：

```
INTEGER
INT
```

功能：

用于存储有符号整数，精度为 10，标度为 0。取值范围为：-2147483648(-2^31)～+2147483647(2^31-1)。

  6. **PLS_INTEGER 类型**



语法如下：

```
PLS_INTEGER
```

功能：

与 INTEGER 相同。

  7. **BIGINT 类型**



语法如下：

```
BIGINT
```

功能：

用于存储有符号整数，精度为 19，标度为 0。取值范围为：-9223372036854775808(-2^63)～+9223372036854775807(2^63-1)。

  8. **TINYINT 类型**



语法如下：

```
TINYINT
```

功能：

用于存储有符号整数，精度为 3，标度为 0。取值范围为：-128～+127。

  9. **BYTE 类型**



语法如下：

```
BYTE
```

功能：

与 TINYINT 相似，精度为 3，标度为 0。取值范围为：-128~+127。

  10. **SMALLINT 类型**



语法如下：

```
SMALLINT
```

功能：

用于存储有符号整数，精度为 5，标度为 0。取值范围为：-32768(-2^15)~ +32767(2^15-1)。

  11. **BINARY 类型**



语法如下：

```
BINARY[(长度 )]
```

功能：

BINARY 数据类型指定定长二进制数据。缺省长度为 1 个字节，最大长度由数据库页面大小决定，具体可参考《DM8_SQL 语言使用手册》1.4.1 节。BINARY 常量以 0x 开始，后跟数据的十六进制表示，例如 0x2A3B4058。

  12. **VARBINARY 类型**



语法如下：

```
VARBINARY[(长度 )]
```

功能：

VARBINARY 数据类型指定变长二进制数据，用法类似 BINARY 数据类型，可以指定一个正整数作为数据长度。缺省长度为 8188 个字节，最大长度由数据库页面大小决定，具体可参考《DM8_SQL 语言使用手册》1.4.1 节。

  13. **RAW** **类型**



语法如下：

RAW[(长度)]

功能：

与 VARBINARY 相同。

  14. **REAL 类型**



语法如下：

```
REAL
```

功能：

REAL 是带二进制的浮点数，但它不能由用户指定使用的精度，系统指定其二进制精度为 24，十进制精度为 7。取值范围-3.4E+38～3.4E + 38。

  15. **FLOAT 类型**



语法如下：

```
FLOAT[(精度)]
```

功能：

FLOAT 是带二进制精度的浮点数，精度最大不超过 53，如省略精度，则二进制精度为 53，十进制精度为 15。取值范围为-1.7E+308～1.7E+308。

  16. **DOUBLE 类型**



语法如下：

```
DOUBLE[(精度)]
```

功能：

同 FLOAT 相似，精度最大不超过 53。

  17. **DOUBLE PRECISION 类型**



语法如下：

```
DOUBLE PRECISION
```

功能：

该类型指明双精度浮点数，其二进制精度为 53，十进制精度为 15。取值范围-1.7E+308 ～1.7E+308。

### 2.1.2 字符数据类型

  1. **CHAR/CHARACTER 类型**



语法如下：

```
CHAR[(长度)]
CHARACTER[(长度)]
```

功能：

定长字符串，最大长度由数据库页面大小决定，具体可参考《DM8_SQL 语言使用手册》1.4.1 节。长度不足时，自动填充空格。

  2. **VARCHAR/VARCHAR2 类型**



语法如下：

```
VARCHAR[(长度)]
VARCHAR2[(长度)]
```

功能：

可变长字符串，最大长度由数据库页面大小决定。VARCHAR2 类型和 VARCHAR 类型用法相同。具体可参考《DM8_SQL 语言使用手册》1.4.1 节。

  3. **ROWID 类型**



语法如下：

```
ROWID
```

功能：

ROWID 类型数据由 18 位字符组成，用来表示 ROWID 数据。18 位字符由“4 位站点号 +6 位分区号 +8 位物理行号”组成。ROWID 类型数据可通过 SF_BUILD_ROWID()构造而来。

表中的 ROWID 类型列，可以用于排序或创建索引。但是 ROWID 类型不支持作为分区列和自定义类型的属性数据类型。ROWID 列与字符类型一样，支持 MAX，MIN 等集函数，不支持 SUM，AVG 等集函数。

例 创建一个含有 ROWID 类型的数据库表。

先构造 ROWID 数据。假定站点号为 1，分区号为 2，物理行号为 50。使用 SF_BUILD_ROWID 函数构造出一个 ROWID 类型数据。

```
SELECT SF_BUILD_ROWID(1,2,50);
```

查询结果如下：

```
AAABAAAAACAAAAAAAy
```

其中，AAAB 为站点号、AAAAAC 为分区号、AAAAAAAy 为 ROWID 值。

创建含有 ROWID 类型的表，并插入数据。

```
CREATE TABLE T(C1 INT,C2 ROWID);

INSERT INTO T VALUES(8,'AAABAAAAACAAAAAAAy');

SELECT C1,C2,ROWID FROM T;
```

c2 列为插入的值，ROWID 为当前数据行的伪列 ROWID。查询结果如下：

```
行号    C1     C2         ROWID

---------- ----------- ------------------ ------------------

1     8      AAABAAAAACAAAAAAAy AAAAAAAAAAAAAAAAAB
```

### 2.1.3 布尔数据类型

语法如下：

```
BOOL
BOOLEAN
```

功能：

布尔数据类型：TRUE 和 FALSE。DMSQL 程序的布尔类型和 INT 类型可以相互转化。如果变量或方法返回的类型是布尔类型，则返回值为 0 或 1。TRUE 和非 0 值的返回值为 1，FALSE 和 0 值返回为 0。

## 2.2 日期时间数据类型

DMSQL 程序支持的日期时间数据类型分为一般日期时间数据类型、时区数据类型和时间间隔数据类型三类。

### 2.2.1 一般日期时间数据类型

  1. **DATE 类型**



语法如下：

```
DATE
```

功能：

DATE 类型包括年、月、日信息，定义了'-4712-01-01'和'9999-12-31'之间任何一个有效的格里高利日期。

  2. **TIME 类型**



语法如下：

```
TIME[(小数秒精度)]
```

功能：

TIME 类型包括时、分、秒信息，定义了一个在'00:00:00.000000'和'23:59:59.999999'之间的有效时间。TIME 类型的小数秒精度规定了秒字段中小数点后面的位数，取值范围为 0～6，如果未定义，缺省精度为 0。

  3. **TIMESTAMP/DATETIME 类型**



语法如下：

```
TIMESTAMP[(小数秒精度)]
DATETIME[(小数秒精度)]
```

功能：

TIMESTAMP/DATETIME 类型包括年、月、日、时、分、秒信息，定义了一个在'-4712-01-0100:00:00.000000000'和'9999-12-31 23:59:59.999999999'之间的有效格里高利日期时间。小数秒精度规定了秒字段中小数点后面的位数，取值范围为 0～9，如果未定义，缺省精度为 6。

### 2.2.2 时区数据类型

  1. **TIME WITH TIME ZONE 类型**



语法如下：

```
TIME［(小数秒精度)］WITH TIME ZONE
```

功能：

描述一个带时区的 TIME 值，其定义是在 TIME 类型的后面加上时区信息。时区部分的实质是 INTERVAL HOUR TO MINUTE 类型，取值范围：-12:59 与 +14:00 之间。例如：TIME '09:10:21 +8:00'。

  2. **TIMESTAMP WITH TIME ZONE 类型**



语法如下：

```
TIMESTAMP［(小数秒精度)］WITH TIME ZONE
```

功能：

描述一个带时区的 TIMESTAMP 值，其定义是在 TIMESTAMP 类型的后面加上时区信息。时区部分的实质是 INTERVAL HOUR TO MINUTE 类型，取值范围：-12:59 与 +14:00 之间。例如：’2009-10-11 19:03:05.0000 -02:10’。

  3. **TIMESTAMP WITH LOCAL TIME ZONE 类型**



语法如下：

```
TIMESTAMP［(小数秒精度)］WITH LOCAL TIME ZONE
```

功能：

描述一个本地时区的 TIMESTAMP 值，能够将标准时区类型 TIMESTAMP WITH TIME ZONE 类型转化为本地时区类型，如果插入的值没有指定时区，则默认为本地时区。

### 2.2.3 时间间隔数据类型

DM 支持两类十三种时间间隔类型：两类是年-月间隔类和日-时间隔类，它们通过时间间隔限定符区分，前者结合了日期字段年和月，后者结合了时间字段日、时、分、秒。由时间间隔数据类型所描述的值总是有符号的。

对时间间隔类型的介绍见表 2.1，需要查看更为详细的信息可参看《DM8_SQL 语言使用手册》1.4.3 节。

表2.1 DMSQL程序支持的时间间隔数据类型

|**类型名**|**类型描述**|
|----|----|
|INTERVAL YEAR(P)|年间隔，即两个日期之间的年数字，P 为时间间隔的引导精度|
|INTERVAL MONTH(P)|月间隔，即两个日期之间的月数字，P 为时间间隔的引导精度|
|INTERVAL DAY(P)|日间隔，即为两个日期/时间之间的日数字，P 为时间间隔的引导精度|
|INTERVAL HOUR(P)|时间隔，即为两个日期/时间之间的时数字，P 为时间间隔的引导精度|
|INTERVAL MINUTE(P)|分间隔，即为两个日期/时间之间的分数字，P 为时间间隔的引导精度|
|INTERVAL SECOND(P,Q)|秒间隔，即为两个日期/时间之间的秒数字，P 为时间间隔的引导精度，Q 为时间间隔秒精度|
|INTERVAL YEAR(P) TO MONTH|年月间隔，即两个日期之间的年月数字，P 为时间间隔的引导精度|
|INTERVAL DAY(P) TO HOUR|日时间隔，即为两个日期/时间之间的日时数字，P 为时间间隔的引导精度|
|INTERVAL DAY(P) TO MINUTE|日时分间隔，即为两个日期/时间之间的日时分数字，P 为时间间隔的引导精度|
|INTERVAL DAY(P)TO SECOND(Q)|日时分秒间隔，即为两个日期/时间之间的日时分秒数字，P 为时间间隔的引导精度，Q 为时间间隔秒精度|
|INTERVALL HOUR(P) TO MINUTE|时分间隔，即为两个日期/时间之间的时分数字，P 为时间间隔的引导精度|
|INTERVAL HOUR(P) TO SECOND(Q)|时分秒间隔，即为两个日期/时间之间的时分秒数字，P 为时间间隔的引导精度，Q 为时间间隔秒精度|
|INTERVAL MINUTE(P) TO SECOND(Q)|分秒间隔，即为两个日期/时间之间的分秒间隔，P 为时间间隔的引导精度，Q 为时间间隔秒精度|


## 2.3 多媒体数据类型

  1. **TEXT/LONG/LONGVARCHAR 类型**



语法如下：

```
TEXT
LONG
LONGVARCHAR
```

功能：

变长字符串类型，其字符串的长度最大为 100G-1，可用于存储长的文本串。

  2. **IMAGE/LONGVARBINARY 类型**



语法如下：

```
IMAGE
LONGVARBINARY
```

功能：

可用于存储多媒体信息中的图像类型。图像由不定长的象素点阵组成，长度最大为 100G-1 字节。该类型除了存储图像数据之外，还可用于存储任何其它二进制数据。

  3. **BLOB 类型**



语法如下：

```
BLOB
```

功能：

BLOB 类型用于指明变长的二进制大对象，长度最大为 100G-1 字节。

  4. **CLOB 类型**



语法如下：

```
CLOB
```

功能：

CLOB 类型用于指明变长的字符串，长度最大为 100G-1 字节。

  5. **BFILE 类型**



语法如下：

```
BFILE
```

功能：

BFILE 用于指明存储在操作系统中的二进制文件，文件存储在操作系统而非数据库中，仅能进行只读访问。

## 2.4 %TYPE 和 %ROWTYPE

在许多时候，DMSQL 程序变量被用来处理存储在数据库表中的数据。这种情况下，变量应该拥有与表列相同的类型。例如表 T(ID
INT, NAME VARCHAR(30))中字段 NAME 类型为 VARCHAR(30)。对应地在 DMSQL 程序中，我们可以声明一个变量：

```
DECLARE

	V_NAME VARCHAR(30);
```

但是如果 T 中的 NAME 字段定义发生了变化，比如变为 VARCHAR(100)。那么存储过程中的变量 V_NAME 也要相应修改为：

```
DECLARE

	V_NAME VARCHAR(100);
```

如果用户应用中有很多的变量以及 DMSQL 程序代码，这种处理可能是十分耗时和容易出错的。

为了解决上述问题，DMSQL 程序提供了 %TYPE 类型。%TYPE 可以将变量同表列的类型进行绑定。例如：

```
DECLARE

	V_NAME T.NAME%TYPE;
```

通过使用 %TYPE，V_NAME 将拥有 T 表的 NAME 列所拥有的类型。如果表 T 的 NAME 列类型定义发生变化，V_NAME 的类型也随之自动发生变化，而不需要用户手动修改。

例如，使用 %TYPE 把变量 v1 的类型和表 PERSON.ADDRESS 的 ADDRESS1 列类型进行绑定。

```
DECLARE

	v_type PERSON.ADDRESS.ADDRESS1%TYPE;

BEGIN

	SELECT ADDRESS1 INTO v_type FROM PERSON.ADDRESS WHERE ADDRESSID=1;

	PRINT v_type;

END;

/
```

与 %TYPE 类似，%ROWTYPE 将返回一个基于表定义的运算类型，它将一个记录声明为具有相同类型的数据库行。例如：

```
DECLARE

	V_TREC T % ROWTYPE;
```

将定义一个记录，该记录中的字段与表 T 中的行相对应。V_TREC 变量会拥有这样的结构：(ID INT, NAME VARCHAR(30))。如果表定义改变了，那么 %ROWTYPE 定义的变量也会随之改变。

例如，使用 %ROWTYPE 将变量 v_row 与表 PERSON.ADDRESS 的行相对应。

```
DECLARE

	v_row PERSON.ADDRESS%ROWTYPE;

	cur CURSOR;

BEGIN

	OPEN cur FOR SELECT * FROM PERSON.ADDRESS WHERE ADDRESSID=3;

	FETCH cur INTO v_row;

	PRINT v_row.ADDRESSID;

	PRINT v_row.ADDRESS1;

	CLOSE cur;

END;
```

> **建议**
>
> 在DMSQL程序设计中使用%TYPE和%ROWTYPE是一种非常好的编程风格，它使得DMSQL程序更加灵活，更适应于对数据库的处理。



## 2.5 自定义类型

DM 支持用户创建自定义类型，分别为记录类型、对象类型、数组类型和集合类型。

自定义类型的创建方式有两种：一是通过 SQL 语句 CREATE TYPE 语句创建。二是通过 DMSQL 程序 TYPE 语句创建。

### 2.5.1 通过 SQL 语句创建

通过 SQL 语句 CREATE TYPE 语句创建自定义类型，具体内容可参考《DM8 SQL 语言使用手册》中自定义类型章节。

此种方式创建的自定义类型可以作为 DMSQL 程序语句块中变量、过程或函数参数的数据类型。其中对象类型、VARRAY 类型、嵌套表类型还可以直接作为表中列的数据类型。

例 展示对象类型如何作为表中列的类型，过程中的参数类型和变量类型。

创建对象类型 COMPLEX。

```
CREATE TYPE COMPLEX AS OBJECT(

RPART INT,

IPART INT,

FUNCTION PLUS(X COMPLEX) RETURN COMPLEX,

FUNCTION LES(X COMPLEX) RETURN COMPLEX

);

/

CREATE TYPE BODY COMPLEX AS

FUNCTION PLUS(X COMPLEX) RETURN COMPLEX IS

BEGIN

RETURN COMPLEX(RPART+X.RPART, IPART+X.IPART);

END;

 

FUNCTION LES(X COMPLEX) RETURN COMPLEX IS

BEGIN

  RETURN COMPLEX(RPART-X.RPART, IPART-X.IPART);

END;

END;

/
```

建立表 c_tab。将 complex 对象类型作为 C2 的数据类型。

```
CREATE TABLE C_TAB(C1 INT, C2 COMPLEX);
```

向表 c_tab 中插入数据。

```
INSERT INTO C_TAB VALUES(1, COMPLEX(2,3));

INSERT INTO C_TAB VALUES(2, COMPLEX(4,2).PLUS(COMPLEX(2,3)));
```

打印表中 C2.RPART 的内容。

```
DECLARE 

A INT;

BEGIN

FOR I IN 1..2 LOOP 

SELECT C2.RPART INTO A FROM C_TAB WHERE C1=I;

PRINT A;

END LOOP;

END

/
```

将 complex 对象类型作为过程的参数 a 的类型和变量 b 的类型。

```
CREATE or replace PROCEDURE proc_complex(a COMPLEX default COMPLEX(2,3)) AS

b COMPLEX;

BEGIN

a.RPART=a.RPART+100;

b=a;

PRINT b.RPART;

END;

/
```

调用过程 proc_complex。

```
call proc_complex;
```

### 2.5.2 通过 DMSQL 程序创建

通过 DMSQL 程序 TYPE 语句创建自定义类型。此种方式创建的自定义类型可以作为 DMSQL 程序语句块中变量、过程或函数参数的数据类型。

#### 2.5.2.1 记录类型

记录类型是由单行多列的标量类型构成复合类型，类似于 C 语言中的结构。记录类型提供了处理彼此独立但又作为一个整体单元的多个相关变量的一种机制。例如：DECLARE V_ID INT; V_NAME VARCHAR(30); 这两个变量在逻辑上是相互关联的，因为它们分别对应表 T(ID INT, NAME VARCHAR(30))中的两个字段。如果为这样的变量声明一个记录类型，那么它们之间的关系就十分明显了。

语法如下：

```
TYPE <记录类型名> IS RECORD
(<字段名><数据类型> [<default子句>]{,<字段名><数据类型> [<default子句>]});
<default子句> ::= <default子句1> 
			  | <default子句2>
<default子句1> ::= DEFAULT <缺省值>
<default子句2> ::= := <缺省值>
```

通过将需要操作的表结构定义成一个记录，可以方便地对表中的行数据进行操作。在 DMSQL 程序中使用记录，需要先定义一个 RECORD 类型，再用该类型声明变量，也可以使用上一小节介绍的 %ROWTYPE 来创建与表结构匹配的记录。

可以单独对记录中的字段赋值，使用点标记引用一个记录中的字段（记录名.字段名）。

例  下面的例子定义了一个记录类型 sale_person，声明一个该记录类型的变量 v_rec，使用点标记为 v_rec 的两个字段赋值，之后使用 v_rec 更新表的一行数据。

```
DECLARE
	TYPE sale_person IS RECORD(
		ID SALES.SALESPERSON.SALESPERSONID%TYPE,
		SALESTHISYEAR SALES.SALESPERSON.SALESTHISYEAR%TYPE);
	v_rec sale_person;
BEGIN
	v_rec.ID := 1;
	v_rec.SALESTHISYEAR:= 5500;
	UPDATE SALES.SALESPERSON SET SALESTHISYEAR=v_rec.SALESTHISYEAR WHERE
SALESPERSONID =v_rec.ID;
END;
/
```

也可以将一个记录直接赋值给另外一个记录，此时两个记录中的字段类型定义必须完全一致。如下面的例子将表中的一行数据读取到一个记录中。然后，将记录 v_rec1 赋值给 v_rec2。

```
DECLARE
	TYPE t_rec IS RECORD( ID INT, NAME VARCHAR(50));
	TYPE t_rec_NEW IS RECORD( ID INT, NAME VARCHAR(50));
	v_rec1 T_REC;
	v_rec2 T_REC_NEW;
BEGIN
	SELECT PRODUCTID,NAME INTO v_rec1 FROM PRODUCTION.PRODUCT WHERE AUTHOR LIKE'鲁迅';
	v_rec2 := v_rec1;
	PRINT v_rec2.ID;
	PRINT v_rec2.NAME;
END;
/
```

定义记录类型时，字段的数据类型除了可以是常规数据类型，还可以是常规数据类型后跟着“[n]”或“[n1,n2...]”表示一维或多维数组，如：

```
DECLARE
	TYPE T_REC IS RECORD( ID INT[3], NAME VARCHAR(30)[3]);
```

DMSQL 程序还支持定义包含数组、集合和其他 RECORD 的 RECORD。例如下面是一个在 RECORD 定义中包含其他 RECORD 的例子，关于数组和集合的介绍请看后续小节。

```
DECLARE
	TYPE TimeType IS RECORD (hours INT, minutes INT ); --定义记录TimeType
	TYPE MeetingType IS RECORD (
	day DATE,
	time_of TimeType -- 嵌套记录TimeType
);
BEGIN
	NULL;
END;
/
```

#### 2.5.2.2 对象类型

不支持通过 DMSQL 程序 TYPE 语句创建对象类型。对象类型的创建请使用 SQL 语句 CREATE TYPE 语句创建。

#### 2.5.2.3 数组类型

DMSQL 程序支持数组数据类型，包括静态数组类型和动态数组类型。

> **注意**
>
> DM数组下标的起始值为1。



##### 2.5.2.3.1 静态数组类型

静态数组是在声明时已经确定了数组大小的数组，其长度是预先定义好的，在整个程序中，一旦给定大小后就无法改变。

语法如下：

```
TYPE <数组名> IS ARRAY <数据类型> [<数组长度>{,<数组长度>}]); 
```

< 数据类型 > 除了支持常规数据类型（INT，VARCHAR 等），还支持复杂数据类型（数组、记录类型和集合类型）。本节中的示例以常规类型为主，复杂类型的示例请参考 [2.3.2.3.3 复杂类型数组](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_sql-data-types-operators.html#2.3.2.3.3%20%E5%A4%8D%E6%9D%82%E7%B1%BB%E5%9E%8B%E6%95%B0%E7%BB%84)。

\< 数组长度 > 指定数组中元素的个数，正整数。 例如：[5]。此处的[]是定义数组长度的必选符号。定义多维数组需指定多个 \< 数组长度 >。

定义了静态数组类型后需要用这个类型申明一个数组变量然后进行操作。

理论上 DM 支持静态数组的每一个维度的最大长度为 65534，但是静态数组最大长度同时受系统内部堆栈空间大小的限制，如果超出堆栈的空间限制，系统会报错。

例 使用静态数组。

```
DECLARE
	TYPE Arr IS ARRAY VARCHAR[3]; //TYPE定义一维数组类型
	a Arr; //声明一维数组
	TYPE Arr1 IS ARRAY VARCHAR[2,4]; //TYPE定义二维数组类型
	b Arr1; //声明二维数组
BEGIN
	FOR I IN 1..3 LOOP
		a[I] := I * 10;
		PRINT a[I];
	END LOOP;
	PRINT '--------';
	FOR I IN 1..2 LOOP
		FOR J IN 1..4 LOOP
			b[I][J] = 4*(I-1)+J;
			PRINT b[I][J];
		END LOOP;
	END LOOP;
END;
/
```

##### 2.5.2.3.2 动态数组类型

与静态数组不同，动态数组可以随程序需要而重新指定大小，其内存空间是从堆（HEAP）上分配（即动态分配）的，通过执行代码而为其分配存储空间，并由 DM 自动释放内存。

动态数组与静态数组的定义方法类似，区别只在于动态数组没有指定下标，需要动态分配空间。

语法如下：

```
TYPE <数组名> IS ARRAY <数据类型> [{,}]); 
```

< 数据类型 > 除了支持常规数据类型（INT，VARCHAR 等），还支持复杂数据类型（数组、记录类型和集合类型）。本节中的示例以常规类型为主，复杂类型的示例请参考 [2.3.2.3.3 复杂类型数组](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_sql-data-types-operators.html#2.3.2.3.3%20%E5%A4%8D%E6%9D%82%E7%B1%BB%E5%9E%8B%E6%95%B0%E7%BB%84)。

定义了动态数组类型后需要用这个类型申明一个数组变量，之后在 DMSQL 程序的执行部分需要为这个数组变量动态分配空间。动态分配空间语句如下所示：

```
数组变量名 := NEW <数据类型>[<常量表达式>{,<常量表达式>}];
```

\< 常量表达式 > 指定数组中元素的个数。例如：[5]或者[5+1]。此处的[]是定义数组长度的必选符号。定义多维数组需指定多个 \< 常量表达式 >。

或者可以使用如下语句对多维数组的某一维度进行空间分配：

```
数组变量名 := NEW <数据类型>[<常量表达式>][];
```

例 1 使用动态数组。

```
DECLARE
	TYPE Arr IS ARRAY VARCHAR[];
	a Arr;
BEGIN
	a := NEW VARCHAR[4];	//动态分配空间
	FOR I IN 1..4 LOOP
		a[I] := I * 4;
		PRINT a[I];
	END LOOP;
END;
/
```

对于多维动态数组，可以单独为每个维度动态分配空间，如下面的例子所示：

例 2 为多维动态数组的每个维度动态分配空间。

```
DECLARE
	TYPE Arr1 IS ARRAY VARCHAR[,];
	b Arr1;
BEGIN
	b := NEW VARCHAR[2][];	//动态分配第一维空间
	FOR I IN 1..2 LOOP
		b[I] := NEW VARCHAR[4]; 	//动态分配第二维空间
		FOR J IN 1..4 LOOP
			b[I][J] = I*10+J;
			PRINT b[I][J];
		END LOOP;
	END LOOP;
END;
/
```

也可以一次性为多维动态数组分配空间，则上面的例子可以写为：

```
DECLARE
	TYPE Arr1 IS ARRAY VARCHAR[,];
	b ARR1;
BEGIN
	b := NEW VARCHAR[2,4];
	FOR I IN 1..2 LOOP
		FOR J IN 1..4 LOOP
			b[I][J]= I*10+J;
			PRINT b[I][J];
		END LOOP;
	END LOOP;
END;
/
```

理论上 DM 支持动态数组的每一个维度的最大长度为 10485760，但是数组最大长度同时受系统内部堆空间大小的限制，如果超出堆的空间限制，系统会报错。

##### 2.5.2.3.3 复杂类型数组

静态数据和动态数组中的 \< 数据类型 > 除了支持普通数据类型，还支持复杂数据类型（数组、记录类型和集合类型）。本节以具体的示例展示复杂类型数组的用法。

例 1  定义一个自定义类型（OBJECT 类型）的静态数组，存放图书的序号和名称。

```
CREATE OR REPLACE TYPE COMPLEX AS OBJECT(
	RPART INT,
	IPART VARCHAR(100)
);
/

DECLARE
	TYPE ARR_COMPLEX IS ARRAY SYSDBA.COMPLEX[3];
	arr ARR_COMPLEX;
BEGIN
	FOR I IN 1..3 LOOP
		SELECT SYSDBA.COMPLEX(PRODUCTID, NAME) INTO arr[I] FROM PRODUCTION.PRODUCT WHERE
PRODUCTID=I;
		PRINT arr[I].RPART || arr[I].IPART;
	END LOOP;
END;
/
```

也可以将上例中的对象类型改为记录类型，则 DMSQL 程序可写为：

```
DECLARE
	TYPE REC IS RECORD(ID INT, NAME VARCHAR(128));
	TYPE REC_ARR IS ARRAY REC[3];
	arr REC_ARR;
BEGIN
	FOR I IN 1..3 LOOP
		SELECT PRODUCTID, NAME INTO arr[I] FROM PRODUCTION.PRODUCT WHERE PRODUCTID=I;
		PRINT arr[I].ID || arr[I].NAME;
	END LOOP;
END;
/
```

例 2  定义一个集合类型（以 VARRAY 为例）的数组，将员工的姓名、性别和职位信息存放到数组变量中。

```
DECLARE
	TYPE VARY IS VARRAY(3) OF varchar(100);
	TYPE ARR_VARY IS ARRAY VARY[8];
	arr ARR_VARY;
	v1,v2,v3 varchar(50);
BEGIN
	FOR I IN 1..8 LOOP
		SELECT NAME,PERSON.SEX,TITLE INTO v1,v2,v3 FROM PERSON.PERSON,RESOURCES.EMPLOYEE
WHERE PERSON.PERSONID=EMPLOYEE.PERSONID AND PERSON.PERSONID=I;
		arr[I] := VARY(v1,v2,v3);
		PRINT '*****工号'||I||'*****';
		FOR J IN 1..3 LOOP
			PRINT arr[I][J];
		END LOOP;
	END LOOP;
END;
/
```

#### 2.5.2.4 集合类型

DMSQL 程序支持三种集合类型：VARRAY 类型、索引表类型和嵌套表类型。

##### 2.5.2.4.1 VARRAY

VARRAY 是一种具有可伸缩性的数组，数组中的每个元素具有相同的数据类型。VARRAY 在定义时通过 \< 数组长度 > 由用户指定一个最大容量，其元素索引是从 1 开始的有序数字。

语法如下：

```
TYPE<数组名> IS VARRAY(<数组长度>) OF <数据类型> [NOT NULL]；
```

参数说明：

\< 数组长度 > 即数组中元素的最大个数，正整数。

\< 数据类型>是 VARRAY 中元素的数据类型。类型可以是：1.常规数据类型；2.自定义类型。

在定义了一个 VARRAY 数组类型后，再声明一个该数组类型的变量，就可以对这个数组变量进行操作了。如下面的代码片段所示：

```
TYPE my_array_type IS VARRAY(10) OF INTEGER;
	v MY_ARRAY_TYPE;
```

使用 v.COUNT()方法可以得到数组 v 当前的实际大小，v.LIMIT()则可获得数组 v 的最大容量。需要注意的是，VARRAY 的元素索引总是连续的。

例 1 下面使用常规类型 VARCHAR 进行举例。查询人员姓名并将其存入一个 VARRAY 变量中。VARRAY 最初的实际大小为 0，使用 EXCTEND()方法可扩展 VARRAY 元素个数，具体在 [2.3.2.4.4 集合类型支持的方法](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_sql-data-types-operators.html#2.3.2.4.4%20%E9%9B%86%E5%90%88%E7%B1%BB%E5%9E%8B%E6%94%AF%E6%8C%81%E7%9A%84%E6%96%B9%E6%B3%95)中介绍。

```
DECLARE
	TYPE MY_ARRAY_TYPE IS VARRAY(10) OF VARCHAR(100);
	v MY_ARRAY_TYPE;
BEGIN
	v:=MY_ARRAY_TYPE();
	PRINT 'v.COUNT()=' || v.COUNT();
	FOR I IN 1..8 LOOP
		v.EXTEND();
		SELECT NAME INTO v(I) FROM PERSON.PERSON WHERE PERSON.PERSONID=I;
END LOOP;
	PRINT 'v.COUNT()=' || v.COUNT();
	FOR I IN 1..v.COUNT() LOOP
		PRINT 'v(' || i || ')=' ||v(i);
	END LOOP;
END;
/
```

例 2 下面使用自定义类型 RECORD 进行举例。

```
CREATE TYPE REC IS RECORD(ID INT, NAME VARCHAR(1280));
/
DECLARE 
TYPE REC_ARR IS varray(30) of rec;   
arr REC_ARR; 
BEGIN
arr:=REC_ARR();
FOR I IN 1..10 LOOP 
arr.EXTEND();
SELECT PRODUCTID, NAME INTO arr[I] FROM PRODUCTION.PRODUCT WHERE PRODUCTID=I;
  PRINT arr[I].ID || arr[I].NAME;
END LOOP;  
END;
/
```

##### 2.5.2.4.2 索引表

索引表提供了一种快速、方便地管理一组相关数据的方法。索引表是一组数据的集合，它将数据按照一定规则组织起来，形成一个可操作的整体，是对大量数据进行有效组织和管理的手段之一，通过函数可以对大量性质相同的数据进行存储、排序、插入及删除等操作，从而可以有效地提高程序开发效率及改善程序的编写方式。

索引表不需要用户指定大小，其大小根据用户的操作自动增长。

语法如下：

```
TYPE <索引表名> IS TABLE OF<数据类型> INDEX BY <索引数据类型>;
```

参数说明：

\< 数据类型 > 指索引表存放的数据的类型。类型可以是：1.常规数据类型；2.自定义类型（但不能是动态数组）。

\< 索引数据类型 > 则是索引表中元素索引的数据类型，DM 目前仅支持 INTEGER/INT 和 VARCHAR 两种类型，分别代表整数索引和字符串索引。对于 VARCHAR 类型，长度不能超过 1024。

用户可使用“索引-数据”对向索引表插入数据，之后可通过“索引”修改和查询这个数据，而不需要知道数据在索引表中实际的位置。

下面给出了一个非常简单的索引表的使用示例。

```
DECLARE
	TYPE Arr IS TABLE OF VARCHAR(100) INDEX BY INT;
	x Arr;
BEGIN
	x(1) := 'TEST1';
	x(2) := 'TEST2';
	x(3) := x(1) || x(2);
	PRINT x(3);
END;
/
```

索引表还可以用来管理记录，下面的例子索引表中存放的数据类型为 RECORD，展示了如何存入和遍历输出索引表的记录类型数据。

```
DECLARE
	TYPE Rd IS RECORD(ID INT, NAME VARCHAR(128));
	TYPE Arr IS TABLE OF Rd INDEX BY INT;
	x Arr;
	i INT;
	CURSOR C1;
BEGIN
	i := 1;
	OPEN C1 FOR SELECT PERSONID, NAME FROM PERSON.PERSON;
//遍历结果集，把每行的值都存放入索引表中
	LOOP
		IF C1%NOTFOUND THEN
			EXIT;
		END IF;
		FETCH C1 INTO x(i).ID, x(i).NAME;
		i := i + 1;
	END LOOP;
//遍历输出索引表中的记录
	i = x."FIRST"();
	LOOP
		IF i IS NULL THEN
			EXIT;
		END IF;
		PRINT 'ID:' || CAST(x(i).ID AS VARCHAR(10)) || ', NAME:' || x(i).NAME;
		i = x."NEXT"(i);
	END LOOP;
END;
/
```

下面的例子定义了一个二维索引表 x，展示了如何存入和遍历输出二维索引表的数据。

```
DECLARE
	TYPE Arr IS TABLE OF VARCHAR(100) INDEX BY BINARY_INTEGER;
	TYPE Arr2 IS TABLE OF Arr INDEX BY VARCHAR(100);
	x Arr2;
	ind_i INT;
	ind_j VARCHAR(10);
BEGIN
//存入数据
	FOR I IN 1 .. 6 LOOP
		FOR J IN 1 .. 3 LOOP
			x(I)(J) := CAST(I AS VARCHAR(100)) || '+'||CAST(J AS VARCHAR(10));
		END LOOP;
	END LOOP;
//遍历多维数组
	ind_i := x."FIRST"();
	LOOP
		IF ind_i IS NULL THEN
			EXIT;
		END IF;
		ind_j := x(ind_i)."FIRST"();
		LOOP
			IF ind_j IS NULL THEN
				EXIT;
			END IF;
			PRINT x(ind_i)(ind_j);
			ind_j := x(ind_i)."NEXT"(ind_j);
		END LOOP;
		ind_i := x."NEXT"(ind_i);
	END LOOP;
END;
/
```

##### 2.5.2.4.3 嵌套表

嵌套表类似于一维数组，但与数组不同的是，嵌套表不需要指定元素的个数，其大小可自动扩展。嵌套表元素的下标从 1 开始。

语法如下：

```
TYPE <嵌套表名> IS TABLE OF <元素数据类型> [NOT NULL];
```

参数说明：

\< 元素数据类型 > 用来指明嵌套表元素的数据类型，当元素数据类型为一个定义了某个表记录的对象类型时，嵌套表就是某些行的集合，实现了表的嵌套功能。

下面的例子定义了一个嵌套表，其结构与 SALES.SALESPERSON 表相同，用来存放今年销售额大于 1000 万的销售代表的信息。

```
DECLARE
	TYPE Info_t IS TABLE OF SALES.SALESPERSON%ROWTYPE;
	info Info_t;
BEGIN
	SELECT SALESPERSONID,EMPLOYEEID,SALESTHISYEAR,SALESLASTYEAR BULK COLLECT INTO info FROM SALES.SALESPERSON WHERE SALESTHISYEAR>1000;
END;
/
```

##### 2.5.2.4.4 集合类型支持的方法

DM 为 VARRAY、索引表和嵌套表提供了一些方法，用户可以使用这些方法访问和修改集合与集合元素。

  1. COUNT



语法：

```
<集合变量名>.COUNT
```

功能：

返回集合中元素的个数。

  2. LIMIT



语法：

```
<VARRAY变量名>.LIMIT
```

功能：

返回 VARRAY 集合的最大的元素个数，对索引表和嵌套表不适用。

  3. FIRST



语法：

```
<集合变量名>.FIRST
```

功能：

返回集合中的第一个元素的下标号，对于 VARRAY 集合始终返回 1。

  4. LAST



语法：

```
<集合变量名>.LAST
```

功能：

返回集合中最后一个元素的下标号，对于 VARRAY 返回值始终等于 COUNT。

  5. NEXT



语法：

```
<集合变量名>.NEXT(<下标>)
```

参数：

指定的元素下标。

功能：

返回在指定元素 i 之后紧挨着它的元素的下标号，如果指定元素是最后一个元素，则返回 NULL。

  6. PRIOR



语法：

```
<集合变量名>.PRIOR(<下标>)
```

参数：

指定的元素下标。

功能：

返回在指定元素 i 之前紧挨着它的元素的下标号，如果指定元素是第一个元素，则返回 NULL。

  7. EXISTS



语法：

```
<集合变量名>.EXISTS(<下标>)
```

参数：

指定的元素下标。

功能：

如果指定下标对应的元素已经初始化，则返回 TRUE，否则返回 FALSE。

  8. DELETE



语法：

```
<集合变量名>.DELETE([<下标>])
```

参数：

待删除元素的下标。

功能：

下标参数省略时删除集合中所有元素，否则删除指定下标对应的元素，如果指定下标为 NULL，则集合保持不变。VARRAY 类型不支持指定索引下标的 DELETE 方法。

  9. DELETE



语法：

```
<集合变量名>.DELETE(<下标1>, <下标2>)
```

参数：

下标 1：要删除的第一个元素的下标；

下标 2：要删除的最后一个元素的下标。

功能：

删除集合中下标从下标 1 到下标 2 的所有元素。VARRAY 类型不支持指定索引下标的 DELETE 方法。

  10. TRIM



语法：

```
<集合变量名>.TRIM([<n>])
```

参数：

删除元素的个数。

功能：

n 参数省略时从集合末尾删除一个元素，否则从集合末尾开始删除 n 个元素。本方法不适用于索引表。

  11. EXTEND



语法：

```
<集合变量名>.EXTEND([<n>])
```

参数：

扩展元素的个数。

功能：

n 参数省略时在集合末尾扩展一个空元素，否则在集合末尾扩展 n 个空元素。本方法不适用于索引表。

  12. EXTEND



语法：

```
<集合变量名>.EXTEND(<n>,<下标>])
```

参数：

n：扩展元素的个数；

下标：待复制元素的下标。

功能：

在集合末尾扩展 n 个与指定下标元素相同的元素。本方法不适用于索引表。

  13. MEMBER OF



语法：

```
<待判断元素> MEMBER OF <集合变量名>
```

参数：

待判断的元素：可以是变量，也可以是常量。

功能：

判断给定元素是否存在于集合中，若存在则返回 TRUE，否则返回 FALSE，一般用作 IF 语句中的布尔判断。本方法不适用于索引表。

  14. NOT MEMBER OF



语法：

```
<待判断元素> NOT MEMBER OF <集合变量名>
```

参数：

待判断的元素：可以是变量，也可以是常量。

功能：

判断给定元素是否不存在于集合中，若不存在则返回 TRUE，否则返回 FALSE，一般用作 IF 语句中的布尔判断。本方法不适用于索引表。

  15. MULTISET UNION



语法：

```
<集合变量名1> MULTISET UNION [ALL | DISTINCT | UNIQUE] <集合变量名2>
```

功能：

计算两个集合的并集。若指定 ALL 关键字，则不去重；若指定 DISTINCT 或 UNIQUE 关键字，则去重，即去除结果集中的重复项。缺省为不去重。

本方法仅适用于嵌套表。

  16. MULTISET INTERSECT



语法：

```
<集合变量名1> MULTISET INTERSECT [ALL | DISTINCT | UNIQUE] <集合变量名2>
```

功能：

计算两个集合的交集。ALL 为缺省设置，当集合 1 有 M 个重复项，集合 2 有 N 个重复项时，返回 min（M,N）个重复项；DISTINCT 和 UNIQUE 表示去除结果集中的重复项。

本方法仅适用于嵌套表。

  17. MULTISET EXCEPT



语法：

```
<集合变量名1> MULTISET EXCEPT [ALL | DISTINCT | UNIQUE] <集合变量名2>
```

功能：

计算集合 1 和集合 2 的差集，即集合 1 中存在但集合 2 中不存在的元素集合。ALL 为缺省设置，表示返回所有集合 1 中存在但集合 2 中不存在的元素；DISTINCT 和 UNIQUE 表示去除结果集中的重复项。

本方法仅适用于嵌套表。

举例说明：

例 1 在集合中添加、删除元素。

```
SET SERVEROUTPUT ON; //PRINT需要设置这条语句，才能打印出消息

DECLARE

	TYPE IntList IS TABLE OF INT;

	v IntList := IntList(1,3,5,7,9);   // 开始赋值5个元素.

BEGIN

	DBMS_OUTPUT.PUT_LINE

  		('最初v中共有 ' || v.COUNT || '个元素。');

	v.EXTEND(3);  // 在末尾增加三个元素

	DBMS_OUTPUT.PUT_LINE

  		('现在v中共有 ' || v.COUNT || '个元素。');

	v.DELETE(2);  // 删掉第2个元素

	DBMS_OUTPUT.PUT_LINE

  		('现在v中共有 ' || v.COUNT || '个元素。');

	v.TRIM(2);  //删除掉末尾的两个元素

	DBMS_OUTPUT.PUT_LINE

  		('现在v中共有 ' || v.COUNT || '个元素。');

END;

/
```

例 2 计算两个集合的并集、交集、差集。

```
SET SERVEROUTPUT ON; //PRINT需要设置这条语句，才能打印出消息

DECLARE

	TYPE IntList IS TABLE OF INT;

	v1 IntList := IntList(1,2,3,4);   // 创建集合v1

	v2 IntList := IntList(3,4,5,6);   // 创建集合v2

	s1 IntList; 

	s2 IntList; 

	s3 IntList; 

	s4 IntList; 

BEGIN

	s1:=v1 MULTISET UNION v2; //计算v1和v2的并集

	DBMS_OUTPUT.PUT_LINE('集合v1和v2的并集如下：');

	FOR I IN 1..s1.COUNT LOOP

  		DBMS_OUTPUT.PUT_LINE(s1(I));

	END LOOP;

	s2:=v1 MULTISET UNION DISTINCT v2; //计算v1和v2的并集，并去除重复项

	DBMS_OUTPUT.PUT_LINE('集合v1和v2的并集去处重复项后结果如下：');

	FOR I IN 1..s2.COUNT LOOP

  		DBMS_OUTPUT.PUT_LINE(s2(I));

	END LOOP;

	s3:=v1 MULTISET INTERSECT v2; //计算v1和v2的交集

	DBMS_OUTPUT.PUT_LINE('集合v1和v2的交集如下：');

	FOR I IN 1..s3.COUNT LOOP

  		DBMS_OUTPUT.PUT_LINE(s3(I));

	END LOOP;

	s4:=v1 MULTISET EXCEPT v2; //计算v1和v2的差集，即v1中存在但v2中不存在的元素

	DBMS_OUTPUT.PUT_LINE('集合v1和v2的差集如下：');

	FOR I IN 1..s4.COUNT LOOP

  		DBMS_OUTPUT.PUT_LINE(s4(I));

	END LOOP;

END;

/
```

## 2.6 类类型

DM 支持类类型，类将结构化的数据及对其进行操作的过程或函数封装在一起。允许用户根据现实世界的对象建模，而不必再将其抽象成关系数据。关于类类型的详细介绍可以参考《DM8_SQL 语言使用手册》的相关章节。

DMSQL 程序中可以声明一个类类型的变量，初始化该变量后就可以访问类的成员变量，调用类的成员方法了。

## 2.7 子类型

子类型是其基数据类型的子集。子类型具有与基数据类型相同的操作性质，但是其有效值域是基数据类型的子集。

语法如下：

```
SUBTYPE <subtype_name> IS <基数据类型>[(<精度>,[<刻度>])] [NOT NULL];
```

< 基数据类型 > 仅支持常规数据类型。具体请参考 [2.1 常规数据类型](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_sql-data-types-operators.html#2.1%20%E5%B8%B8%E8%A7%84%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)。

举例说明：

```
DECLARE
	SUBTYPE COUNTER IS NUMBER(5);
	C COUNTER;
BEGIN
	NULL;
END;
```

在这个例子中定义了一个名称为 COUNTER 的子类型，其实际数据类型为 NUMBER(5)。子类型可用来防止变量超出规定的值域，也可以增强 DMSQL 程序的可读性。

可以在任何 DMSQL 程序块、子程序或包中定义自己的子类型。一旦定义了子类型，就可以声明该类型的变量、常量等，如上例中的变量 C。

## 2.8 游标类型

语法如下：

```
CURSOR|
SYS_REFCURSOR
```

功能：

游标类型用来定义游标变量。对游标变量进行赋值，赋值对象需要是一个游标对象。

例  使用 CURSOR 定义游标变量 c2。

```
DECLARE
    CURSOR c1 IS SELECT TITLE FROM RESOURCES.EMPLOYEE WHERE MANAGERID = 3;
    c2 CURSOR;  
BEGIN
    c2 =c1;
    open c2;
    close c2;
END;
```

## 2.9 操作符

与其他程序设计语言相同，DMSQL 程序有一系列操作符。操作符分为下面几类：

  * 算术操作符
  * 关系操作符
  * 比较操作符
  * 逻辑操作符



算术操作符如表 2.2 所示。

表2.2 算术操作符

|**操作符**|**对应操作**|
|----|----|
|+|加|
|-|减|
|*|乘|
|/|除|


关系操作符主要用于条件判断语句或用于 WHERE 子句中，关系操作符检查条件和结果为 TRUE 或 FALSE。表 2.3、表 2.4、表 2.5 分别列出了 DMSQL 程序中的关系操作符、比较操作符和逻辑操作符。

表2.3 关系操作符

|**操作符**|**对应操作**|
|----|----|
|\<|小于操作符|
|\<=|小于或等于操作符|
|>|大于操作符|
|>=|大于或等于操作符|
|=|等于操作符|
|!=|不等于操作符|
|\<>|不等于操作符|
|:=|赋值操作符|


表2.4 比较操作符

|**操作符**|**对应操作**|
|----|----|
|IS NULL|如果操作数为 NULL 返回 TRUE|
|LIKE|比较字符串值|
|BETWEEN|验证值是否在范围之内|
|IN|验证操作数在设定的一系列值中|


表2.5 逻辑操作符

|**操作符**|**对应操作**|
|----|----|
|AND|两个条件都必须满足|
|OR|只要满足两个条件中的一个|
|NOT|取反|


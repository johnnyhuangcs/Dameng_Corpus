

# UTL_RAW 包

UTL_RAW 包提供了一系列十六进制类型相关函数，可以将十六进制类型数据转化为其它类型数据。

## 29.1 相关方法

UTL_RAW 包所支持的 20 个函数，分别实现十六进制的相关转化函数。这些函数通过调用相应系统内部函数来实现存取数据的过程。如下详细介绍各函数：

  1. XRANGE



输出包含 START_BYTE~END_BYTE 在内的，十六进制单字节区间内的全部字节。

语法如下：

```
FUNCTION XRANGE(
	START_BYTE	IN	VARBINARY	DEFAULT NULL,
	END_BYTE	IN	VARBINARY	DEFAULT NULL
) RETURN VARBINARY;
```

参数详解

  * START_BYTE 输入参数，单字节 VARBINARY 数据。
  * END_BYTE 输入参数，单字节 VARBINARY 数据。



注意：1）要求 START_BYTE 和 END_BYTE 都是单字节。若超过了一个字节，则只从左取第一个高位字节；2）如果 START_BYTE 值 >END_BYTE 值，则返回以 START_BYTE 十六进制字节开始的，通过 FF 至 00，再到 END_BYTE 的全部十六进制字节；3）如果 START_BYTE 为 NULL 默认为 00，END_BYTE 为 NULL 默认为 FF，二者均为 NULL 则 START_BYTE 默认为 00，END_BYTE 默认为 FF。

  2. BIT_AND



输出 R1 与 R2 的按位运算的逻辑与。

语法如下：

```
FUNCTION BIT_AND(
	R1 	IN 	VARBINARY,
	R2 	IN 	VARBINARY
) RETURN VARBINARY;
```

参数详解

  * R1 输入参数，varbinary 数据。
  * R2 输入参数，varbinary 数据。



注意：如果 R1、R2 长度不等，则对长度较小的参数低位补 0XFF 字节至与另一个参数对齐后再做按位运算。

```
3. BIT_COMPLEMENT
```

输出 r 的按位运算的反码。

语法如下：

```
FUNCTION BIT_COMPLEMENT(
	R 	IN 	VARBINARY
) RETURN VARBINARY;
```

参数详解

  * R 输入参数，VARBINARY 数据。



  4. BIT_OR



输出 r1 与 r2 的按位运算的逻辑或。

语法如下：

```
FUNCTION BIT_OR(
	R1 	IN 	VARBINARY,
	R2 	IN 	VARBINARY
) RETURN VARBINARY;
```

参数详解

  * R1 输入参数，VARBINARY 数据。
  * R2 输入参数，VARBINARY 数据。



注意：如果 r1、r2 长度不等，则对长度较小的参数低位补 0X00 字节至与另一个参数对齐后再做按位运算。

  5. BIT_XOR



输出 R1 与 R2 的按位运算的逻辑异或。

语法如下：

```
FUNCTION BIT_XOR(
	R1 	IN 	VARBINARY,
	R2 	IN 	VARBINARY
) RETURN VARBINARY;
```

参数详解

  * R1 输入参数，VARBINARY 数据。
  * R2 输入参数，VARBINARY 数据。



注意：如果 R1、R2 长度不等，则对长度较小的参数低位补 0X00 字节至与另一个参数对齐后再做按位运算。

  6. COMPARE



比较十六进制串数据 R1 和 R2。如果 R1 串长度\<R2 串长度，R1 串用 PAD 填充至二者长度相等后再作比较。返回结果：数字，表示从几个数字开始不相等。0 表示相等。

语法如下：

```
FUNCTION COMPARE(
	R1  				IN 		VARBINARY,
	R2  				IN 		VARBINARY,
	PAD  			IN 		VARBINARY	 DEFAULT 0X00
) RETURN NUMBER;
```

参数详解

  * R1 输入参数，VARBINARY 数据。
  * R2 输入参数，VARBINARY 数据。
  * PAD 输入参数，VARBINARY 数据，缺省为 0X00。



  7. CONCAT



按参数顺序分别连接各个十六进制串数。最多可连接 12 个。返回连接后的十六进制串数值。

语法如下：

```
FUNCTION CONCAT(
	R1  		IN 		VARBINARY 	DEFAULT NULL,
	R2  		IN 		VARBINARY 	DEFAULT NULL,
	R3  		IN 		VARBINARY 	DEFAULT NULL,
	R4  		IN 		VARBINARY 	DEFAULT NULL,
	R5  		IN 		VARBINARY 	DEFAULT NULL,
	R6  		IN 		VARBINARY 	DEFAULT NULL,
	R7  		IN 		VARBINARY 	DEFAULT NULL,
	R8  		IN 		VARBINARY 	DEFAULT NULL,
	R9  		IN 		VARBINARY 	DEFAULT NULL,
	R10  		IN 		VARBINARY 	DEFAULT NULL,
	R11  		IN 		VARBINARY 	DEFAULT NULL,
	R12  		IN 		VARBINARY 	DEFAULT NULL
) RETURN VARBINARY;
```

参数详解

  * R1 输入参数，VARBINARY 数据，缺省为 NULL。
  * R2 输入参数，VARBINARY 数据，缺省为 NULL。
  * R3 输入参数，VARBINARY 数据，缺省为 NULL。
  * R4 输入参数，VARBINARY 数据，缺省为 NULL。
  * R5 输入参数，VARBINARY 数据，缺省为 NULL。
  * R6 输入参数，VARBINARY 数据，缺省为 NULL。
  * R7 输入参数，VARBINARY 数据，缺省为 NULL。
  * R8 输入参数，VARBINARY 数据，缺省为 NULL。
  * R9 输入参数，VARBINARY 数据，缺省为 NULL。
  * R10 输入参数，VARBINARY 数据，缺省为 NULL。
  * R11 输入参数，VARBINARY 数据，缺省为 NULL。
  * R12 输入参数，VARBINARY 数据，缺省为 NULL。



  8. CONVERT_RAW



将十六进制串数值从后指定的字符集到前指定的字符集转换。支持 UTF8 与其它字符集(GBK、BIG5、ISO_8859_9、EUC_JP、EUC_KR、KOI8R、GB18030、SQL_ASCII、ISO_8859_1)的相互转换。

语法如下：

```
FUNCTION CONVERT_RAW (
	R        			IN 		VARBINARY,
	TO_CHARSET			IN 		VARCHAR2,
	FROM_CHARSET 		IN 		VARCHAR2
) RETURN VARBINARY;
```

参数详解

​

  * R 输入参数，VARBINARY 数据。
  * TO_CHARSET 输入参数，字符集。指定转换后的字符集。
  * FROM_CHARSET 输入参数，字符集。转换前的字符集。



  9. COPIES



将十六进制串数值拷贝 N 次，依次排在前一个数的后面。返回新的十六进制数值。

语法如下：

```
FUNCTION COPIES(
	R 	IN 	VARBINARY,
	N 	IN 	NUMBER
) RETURN VARBINARY;
```

  * R 输入参数，VARBINARY 数据。
  * N 输入参数，NUMBER 类型，指拷贝 N 次。



  10. LENGTH



返回十六进制串的字节长度。

语法如下：

```
FUNCTION LENGTH(
	R 	IN 	VARBINARY
) RETURN NUMBER;
```

  * R 输入参数，VARBINARY 数据。



  11. overlay_raw



用十六进制串 overlay_str 覆盖源十六进制串 target 中指定的十六进制子串。

语法如下：

```
FUNCTION OVERLAY_RAW(
	OVERLAY_STR 	IN 		VARBINARY,
	TARGET  		IN 		VARBINARY,
	POS   			IN 		INTEGER 		DEFAULT 1,
	LEN   			IN 		INTEGER 		DEFAULT NULL,
	PAD   			IN 		VARBINARY  		DEFAULT NULL
) RETURN VARBINARY;
```

参数详解

  * OVERLAY_STR 十六进制串。
  * TARGET 源十六进制串。
  * POS 子串在源串 target 中的起始位置。
  * LEN 子串的长度。
  * PAD OVERLAY_STR 串长度若小于 LEN 或 POS 超过了 TARGET 的长度，用 PAD 值代替。



  12. REVERSE_RAW



将输入十六进制串的字符顺序反转后返回。

语法如下：

```
FUNCTION REVERSE_RAW (
	R 	IN 	VARBINARY
) RETURN VARBINARY;
```

  * R 输入参数，VARBINARY 数据。



  13. SUBSTR



返回十六进制串中从字节位置 POS 开始的 LEN 个字节。

语法如下：

```
FUNCTION SUBSTR(
	R   		IN 		VARBINARY,
	POS 		IN 		INTEGER,
	LEN 		IN 		INTEGER 		DEFAULT NULL
) RETURN VARBINARY;
```

  * R 输入参数，VARBINARY 数据。
  * POS 子串在源串 TARGET 中的起始位置。
  * LEN 子串的长度。



  14. TRANSLATE



十六进制替换函数。将 R 字符串中的 from_set 串，全部替换成 to_set 串。

语法如下：

```
FUNCTION TRANSLATE(
	R        	IN 		VARBINARY,
	FROM_SET  	IN 		VARBINARY,
	TO_SET   	IN 		VARBINARY
) RETURN VARBINARY;
```

参数详解

  * R 源字符串。
  * FROM_SET 被替换的字符串。
  * TO_SET 替换后的字符串。



  15. TRANSLITERATE



功能和 TRANSLATE 一样。

语法如下：

```
FUNCTION TRANSLITERATE(
	R        		IN 		VARBINARY,
	TO_SET   		IN 		VARBINARY  	DEFAULT NULL,
	FROM_SET 		IN 		VARBINARY  	DEFAULT NULL,
	PAD     		IN 		VARBINARY  	DEFAULT NULL
) RETURN VARBINARY;
```

参数详解

  * R 源字符串。
  * FROM_SET 被替换的字符串。
  * TO_SET 替换后的字符串。
  * PAD 如果 TO_SET 小于 FROM_SET，少的部分用 PAD 值代替。如果 PAD 为 NULL，则以 0 从低位开始填充。



  16. CAST_TO_RAW



ASCII 字符转化成十六进制数字。

语法如下：

```
FUNCTION CAST_TO_RAW(
	C 	IN 	VARCHAR2
) RETURN VARBINARY;
```

  * C 输入参数，ASCII 字符。



  17. CAST_TO_VARCHAR2



十六进制数字转化成对应的 ASCII 字符。

语法如下：

```
FUNCTION CAST_TO_VARCHAR2(
	R 	IN 	VARBINARY
) RETURN VARCHAR2;
```

  * R 源字符串。



  18. CAST_TO_INT



十六进制数字转化成整型十进制。

语法如下：

```
FUNCTION CAST_TO_INT(
	R 			IN 		VARBINARY,
	ENDIANESS 	IN 		INTEGER  	DEFAULT 1
)RETURN  INTEGER; 
```

参数详解

  * R 源字符串。
  * ENDIANESS 读取 R 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示依次从高位向低位读取。2 和 3 表示依次从低位向高位读取。



  19. CAST_FROM_INT



整型十进制转化成十六进制数字。

语法如下：

```
FUNCTION CAST_FROM_INT(
	N  			IN  	INTEGER,
	ENDIANESS  	IN 		INTEGER  	DEFAULT 1
) RETURN VARBINARY;
```

  * N 输入参数，整型十进制数据。
  * ENDIANESS 读取 N 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示使用大端方式，2 表示使用小端方式，3 表示使用机器的大小端方式。



  20. CAST_TO_BINARY_INTEGER



十六进制数字转化成整型十进制。

语法如下：

```
FUNCTION CAST_TO_BINARY_INTEGER (
	R 			IN 		VARBINARY,
	ENDIANESS 	IN 		INTEGER 		DEFAULT 1
)RETURN  INTEGER;
```

参数详解

  * R 源字符串。
  * ENDIANESS 读取 R 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示使用大端方式，2 表示使用小端方式，3 表示使用机器的大小端方式。



  21. CAST_FROM_BINARY_INTEGER



整型十进制转化成十六进制数字。

语法如下：

```
FUNCTION CAST_FROM_BINARY_INTEGER (
	N  			IN  	INTEGER,
	ENDIANESS  	IN 		INTEGER  	DEFAULT 1
) RETURN VARBINARY;
```

参数详解

  * N 输入参数，整型十进制数据。
  * ENDIANESS 读取 N 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示使用大端方式，2 表示使用小端方式，3 表示使用机器的大小端方式。



  22. CAST_TO_BINARY_DOUBLE



十六进制数字转化成 DOUBLE。

语法如下：

```
FUNCTION CAST_TO_BINARY_DOUBLE (
	R 			IN 		VARBINARY,
	ENDIANESS 	IN 		INTEGER  	DEFAULT 1
)RETURN  DOUBLE; 
```

参数详解

  * R 源字符串。
  * ENDIANESS 读取 R 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示使用大端方式，2 表示使用小端方式，3 表示使用机器的大小端方式。



  23. CAST_FROM_BINARY_DOUBLE



DOUBLE 转化成十六进制数字。

语法如下：

```
FUNCTION CAST_BINARY_DOUBLE (
	N  			IN  	DOUBLE,
	ENDIANESS  	IN 		INTEGER  	DEFAULT 1
) RETURN VARBINARY;
```

参数详解

  * N 输入参数，DOUBLE 类型数据。
  * ENDIANESS 读取 N 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示使用大端方式，2 表示使用小端方式，3 表示使用机器的大小端方式。



  24. CAST_TO_BINARY_FLOAT



十六进制数字转化成 FLOAT。

语法如下：

```
FUNCTION CAST_TO_BINARY_FLOAT(
	R 			IN 		VARBINARY,
	ENDIANESS 	IN 		INTEGER  	DEFAULT 1
)RETURN  FLOAT;
```

参数详解

  * R 源字符串。
  * ENDIANESS 读取 R 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示使用大端方式。2 表示使用小端方式，3 表示使用机器的大小端方式。



  25. CAST_FROM_BINARY_FLOAT



FLOAT 转化成十六进制数字。

语法如下：

```
FUNCTION CAST_FROM_BINARY_FLOAT(
	N  			IN  	DOUBLE,
	ENDIANESS  	IN 		INTEGER  	DEFAULT 1
) RETURN VARBINARY;
```

参数详解

  * N 输入参数，FLOAT 类型数据。
  * ENDIANESS 读取 N 数据的顺序，取值范围：1、2、3，缺省为 1。1 表示使用大端方式。2 表示使用小端方式，3 表示使用机器的大小端方式。



  26. CAST_TO_NUMBER



十六进制数字转化成 NUMBER。

语法如下：

```
FUNCTION CAST_TO_NUMBER(
	R 			IN 		VARBINARY
)RETURN  NUMBER; 
```

参数详解

  * R 源字符串。



  27. CAST_FROM_NUMBER



将 NUMBER 类型（DEC、DECIMAL）转化成十六进制数据。

语法如下：

```
FUNCTION CAST_FROM_NUMBER(
	N 	IN 	NUMBER
) RETURN VARBINARY;
```

参数详解

  * N 输入参数，NUMBER 类型数据。



## 29.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'UTL_RAW');
```

例 UTL_RAW 包的应用。

例 1 xrange

```
select utl_raw.xrange(utl_raw.cast_to_raw('A'),utl_raw.cast_to_raw('Z')) from dual;
```

查询结果如下：

```
0x4142434445464748494A4B4C4D4E4F505152535455565758595A
```

例 2 bit_and

```
select utl_raw.bit_and('0102F3','F30201') from dual;
```

查询结果如下：

```
0x010201
```

例 3 bit_complement

```
select utl_raw.bit_complement('0102F3') from dual;
```

查询结果如下：

```
0xFEFD0C
```

例 4  bit_or

```
select utl_raw.bit_or('0102F3','F30201') from dual;
```

查询结果如下：

```
0xF302F3
```

例 5 bit_xor

```
select utl_raw.bit_xor('0102F3','F30201') from dual;
```

查询结果如下：

```
0xF200F2
```

例 6 compare

```
select utl_raw.compare(utl_raw.cast_to_raw('ABC'),utl_raw.cast_to_raw('ABCD'))from dual;
```

查询结果如下：

```
4
```

例 7 concat

```
select utl_raw.concat('A','41','B','42') from dual;
```

查询结果如下：

```
0x0A410B42
```

例 8 convert_raw

```
select UTL_RAW.CONVERT_raw('E4B8ADE69687', 'GBK', 'UTF8') from dual;
```

查询结果如下：

```
0xD6D0CEC4
select UTL_RAW.CONVERT_raw('D6D0CEC4','UTF8', 'GBK') from dual;
```

查询结果如下：

```
0xE4B8ADE69687
```

例 9 copies

```
select utl_raw.copies('A',6) from dual;
```

查询结果如下：

```
0x0A0A0A0A0A0A
```

例 10 length

```
select utl_raw.LEngth(UTL_RAW.CAST_TO_RAW('ABCD')) from dual;
```

查询结果如下：

```
4
```

例 11 overlay_raw

```
select utl_raw.overlay_raw
(utl_raw.cast_to_raw('D'),utl_raw.cast_to_raw('AAAC'),1,1) from dual;
```

查询结果如下：

```
0x44414143
```

```
select
utl_raw.overlay_raw(utl_raw.cast_to_raw('D'),utl_raw.cast_to_raw('AAAC'),1,2,utl_raw.cast_to_raw('
')) from dual;
```

查询结果如下：

```
0x44204143
```

例 12 reverse_raw

```
select utl_raw.reverse_raw(utl_raw.cast_to_raw('123')) from dual;
```

查询结果如下：

```
0x333231
```

例 13 substr

```
select utl_raw.substr(0x414243444541424344454142434445,6,2) from dual;
```

查询结果如下：

```
0x4142
```

例 14 translate

```
select
UTL_RAW.translate(utl_raw.cast_to_raw('aDe'),utl_raw.cast_to_raw('AD'),utl_raw.cast_to_raw('ad'))
from dual;
```

查询结果如下：

```
0x616465  //即ade
```

例 15 translITERATE

```
select
UTL_RAW.translITERATE(utl_raw.cast_to_raw('FaDe'),utl_raw.cast_to_raw('adef'),utl_raw.cast_to_raw('ADEF'),utl_raw.cast_to_raw('')) from dual;
```

查询结果如下：

```
0x66616465 //即fade
```

例 16 CAST_TO_RAW

```
select UTL_RAW.CAST_TO_RAW(10) from dual;
```

查询结果如下：

```
0x3130
```

例 17 cast_to_varchar2

```
select utl_raw.cast_to_varchar2('40') from dual;
```

查询结果如下：

```
@
```

例 18 cast_to_INT

```
select utl_raw.cast_to_int('00000064',1) from dual;
```

查询结果如下：

```
100
```

```
select utl_raw.cast_to_int('00000064',2) from dual;
```

查询结果如下：

```
1677721600
```

例 19 cast_from_INT

```
select utl_raw.cast_from_int(10) from dual;
```

查询结果如下：

```
0x0000000A
```

```
select utl_raw.cast_FROM_int(94311,2) from dual;
```

查询结果如下：

```
0x67700100
```

例 20 cast_from_number

```
select UTL_RAW.cast_FROM_number(10) from dual;
```

查询结果如下：

```
0xC10B
```

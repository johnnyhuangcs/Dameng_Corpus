

# DBMS_LOB 包

DBMS_LOB 包用于对大对象（BLOB、CLOB）进行多种操作。

## 7.1 相关方法

  1. APPEND



追加一个源 LOB 数据到一个目标的 LOB 数据后面。

语法如下：

```
PROCEDURE APPEND(
	DEST_LOB	IN OUT	BLOB,
	SRC_LOB		IN		BLOB
);
PROCEDURE APPEND(
	DEST_LOB	IN OUT	CLOB,
	SRC_LOB		IN		CLOB
);
```

参数详解

  * DEST_LOB

目标临时大对象

  * SRC_LOB

源临时大对象



  2. CLOSE



关闭一个之前打开的 LOB 或 BFILE 对象。

语法如下：

```
PROCEDURE CLOSE(
	LOB_LOC		IN OUT	BLOB
);
PROCEDURE CLOSE(
	LOB_LOC		IN OUT	CLOB,
);
PROCEDURE CLOSE(
	FILE_LOC	IN OUT	BFILE
);
```

参数详解

  * LOB_LOC

BLOB 或 CLOB 类型的大对象

  * FILE_LOC

BFILE 类型对象



  3. COMPARE



比较 2 个 LOB 的数据，返回一个结果值。

语法如下：

```
FUNCTION COMPARE(
	LOB_1		IN	BLOB,
	LOB_2		IN	BLOB,
	AMOUNT		IN	BIGINT:= DBMS_LOB.LOBMAXSIZE,
	OFFSET_1	IN	BIGINT := 1,
	OFFSET_2	IN	BIGINT := 1
) RETURN INTEGER;
FUNCTION COMPARE(
	LOB_1		IN	CLOB,
	LOB_2		IN	CLOB,
	AMOUNT		IN	BIGINT:= DBMS_LOB.LOBMAXSIZE,
	OFFSET_1	IN	BIGINT := 1,
	OFFSET_2	IN	BIGINT := 1
) RETURN INTEGER;
FUNCTION COMPARE(
	LOB_1		IN	BFILE,
	LOB_2		IN	BFILE,
	AMOUNT		IN	BIGINT,
	OFFSET_1	IN	BIGINT:= 1,
	OFFSET_2	IN	BIGINT:= 1
) RETURN INTEGER;
```

参数详解

  * LOB_1

第一个临时大对象

  * LOB_2

第二个临时大对象

  * AMOUNT

需要比较的总长度，BLOB 为字节长度，CLOB 为字符长度，最大取值为 LOBMAXSIZE。LOBMAXSIZE 是包内变量，为 bigint
类型，取值为 BIGINT 的最大值 9223372036854775807。若调用时设置该参数大于
LOBMAXSIZE，则进行参数转换时就会报错：数据溢出。

  * OFFSET_1

第一个临时大对象的起始偏移，最大取值为 LOBMAXSIZE。LOBMAXSIZE 是包内变量，为 bigint 类型，取值为 BIGINT 的最大值
9223372036854775807。若调用时设置该参数大于 LOBMAXSIZE，则进行参数转换时就会报错：数据溢出。

  * OFFSET_2

第二个临时大对象的起始偏移，最大取值为 LOBMAXSIZE。LOBMAXSIZE 是包内变量，为 bigint 类型，取值为 BIGINT 的最大值
9223372036854775807。若调用时设置该参数大于 LOBMAXSIZE，则进行参数转换时就会报错：数据溢出。



返回值

0：表示相等；

-1：表示 LOB_1\<LOB_2；

1：表示 LOB_1>LOB_2

NULL：表示 AMOUNT\<1，OFFSET_1\<1，OFFSET_2\<1。

  4. COPY



拷贝指定长度的源 LOB 数据插入到目标 LOB。

语法如下：

```
PROCEDURE COPY(
	DEST_LOB		IN OUT	BLOB,
	SRC_LOB			IN		BLOB,
	AMOUNT			IN		BIGINT,
	DEST_OFFSET		IN		BIGINT := 1,
	SRC_OFFSET		IN		BIGINT := 1
);
PROCEDURE COPY(
	DEST_LOB		IN OUT	CLOB,
	SRC_LOB			IN		CLOB,
	AMOUNT			IN		BIGINT,
	DEST_OFFSET		IN		BIGINT := 1,
	SRC_OFFSET		IN		BIGINT := 1
);
```

参数详解

  * DEST_LOB

目标临时大对象。

  * SRC_LOB

源临时大对象。

  * AMOUNT

需要拷贝的字节或字符总数，BLOB 为字节，CLOB 为字符。

  * DEST_OFFSET

写入的起始偏移，BLOB 为字节，CLOB 为字符，如果写入偏移大于原字段的长度，将自动填充原长度到指定偏移间的数据，BLOB 填充 0，CLOB
填充空格。

  * SRC_OFFSET

读取的起始偏移，BLOB 为字节，CLOB 为字符。



  5. CREATETEMPORARY



创建一个临时的 LOB 对象，并存储在临时的表空间里。

语法如下：

```
PROCEDURE CREATETEMPORARY(
	LOB_LOC		IN OUT	BLOB,
	CACHE		IN		BOOLEAN,
	DUR			IN		PLS_INTEGER := DBMS_LOB.SESSION
);
PROCEDURE CREATETEMPORARY(
	LOB_LOC		IN OUT	CLOB,
	CACHE		IN		BOOLEAN,
	DUR			IN		PLS_INTEGER := 10
);
```

参数详解

  * LOB_LOC

目标临时大对象。

  * CACHE

仅支持 TRUE 操作，输入值为其它值时不报错，也不产生其它效果。

  * DUR

存活周期，支持 DBMS_LOB.SESSION 和 DBMS_LOB.”CALL”。



  6. ERASE



擦除指定偏移开始的指定长度 LOB 数据。BLOB 用 0，CLOB 用空格代替原有数据。

语法如下：

```
PROCEDURE ERASE(
	LOB_LOC		IN OUT	BLOB,
	AMOUNT		IN OUT	BIGINT,
	OFFSET		IN		BIGINT := 1
);
PROCEDURE ERASE(
	LOB_LOC		IN OUT	CLOB,
	AMOUNT		IN OUT	BIGINT,
	OFFSET		IN		BIGINT := 1
);
```

参数详解

  * LOB_LOC

临时大对象。

  * AMOUNT

输入需要擦除数据的总数，BLOB 为字节，CLOB 为字符，输出实际擦除的总数，AMOUNT\<=0 将报错，AMOUNT 大于原字段长度，将擦写所有数据。

  * OFFSET

起始偏移。



  7. FILECLOSE



关闭一个之前打开的 BFILE 对象。

语法如下：

```
PROCEDURE FILECLOSE(
	FILE_LOC IN OUT BFILE
);
```

参数详解

  * FILE_LOC

BFILE 类型对象。



  8. FILECLOSEALL



关闭所有之前打开的 BFILE 对象。

语法如下：

```
PROCEDURE FILECLOSEALL();
```

  9. FILEEXISTS



判断指定的 BFILE 是否存在。

语法如下：

```
FUNCTION FILEEXISTS(
	FILE_LOC IN	BFILE
)RETURN INTEGER;
```

参数详解

  * FILE_LOC

BFILE 类型对象。



返回值

0：不存在

1：存在

  10. FILEGETNAME



获取 BFILE 对象的目录名和文件名。

语法如下：

```
PROCEDURE FILEGETNAME(
	FILE_LOC	IN	BFILE,
	DIR_ALIAS	OUT	VARHCAR,
	FILEANME	OUT	VARCHAR
);
```

参数详解

  * FILE_LOC

BFILE 类型对象。

  * DIR_ALIAS

目录对象名。

  * FILEANME

文件名。



  11. FILEISOPEN



判断指定的 BFILE 是否已经打开。

语法如下：

```
FUNCITON FILEISOPEN(
	FILE_LOC IN	BFILE
) RETURN INTEGER;
```

参数详解

  * FILE_LOC

BFILE 类型对象。



返回值

0：未打开

1：打开

  12. FILEOPEN



打开一个 BFILE 文件。

语法如下：

```
PROCEDURE FILEOPEN(
	FILE_LOC	IN OUT	BFILE,
	OPEN_MODE	IN		INTEGER:=FILE_READONLY
);
```

参数详解

  * FILE_LOC

BFILE 类型对象。

  * OPEN_MODE

打开方式。仅支持对 BFILE 对象使用 FILE_READONLY 参数。对 BLOB 或 CLOB 对象使用参数
LOB_READONLY、LOB_READWRITE 时，仅提供语法支持，不报错，也不产生其它效果。



  13. FRAGMENT_DELETE



删除指定偏移开始的指定长度的数据，不重写。

语法如下：

```
PROCEDURE FRAGMENT_DELETE(
	LOB_LOC		IN OUT	BLOB,
	AMOUNT		IN		BIGINT,
	OFFSET		IN		BIGINT
);
PROCEDURE FRAGMENT_DELETE(
	LOB_LOC		IN OUT	CLOB,
	AMOUNT		IN		BIGINT,
	OFFSET		IN		BIGINT
);
```

参数详解

  * LOB_LOC

临时大对象。

  * AMOUNT

需要删除数据的总数，BLOB 为字节，CLOB 为字符。

  * OFFSET

起始偏移。BLOB 为字节，CLOB 为字符。



错误说明：

OFFSET\<1，OFFSET 超过原字段长，AMOUNT 大于原字段长，AMOUNT + OFFSET 大于原字段长，返回错误。

  14. FRAGMENT_INSERT



插入指定长度的数据到 LOB 的指定偏移处，最大为 32K。

语法如下：

```
PROCEDURE FRAGMENT_INSERT(
	LOB_LOC		IN OUT	BLOB,
	AMOUNT		IN		INTEGER,
	OFFSET		IN		BIGINT,
	BUFFER		IN		VARBINARY
);
PROCEDURE FRAGMENT_INSERT(
	LOB_LOC		IN OUT	CLOB,
	AMOUNT		IN		INTEGER,
	OFFSET		IN		BIGINT,
	BUFFER		IN		VARCHAR
);
```

参数详解

  * LOB_LOC

临时大对象。

  * AMOUNT

需要插入数据的总数，BLOB 为字节，CLOB 为字符，最大 32K。

  * OFFSET

写入起始的偏移，BLOB 为字节，CLOB 为字符。

  * BUFFER

待写入数据。



错误说明：

AMOUNT 大于 BUFFER 内数据长度，AMOUNT \<= 0，OFFSET \<= 0，返回错误。

  15. FRAGMENT_MOVE



移动指定长度的数据从原始偏移到新偏移处。

语法如下：

```
PROCEDURE FRAGMENT_MOVE(
	LOB_LOC			IN OUT	BLOB,
	AMOUNT			IN		BIGINT,
	SRC_OFFSET		IN		BIGINT,
	DEST_OFFSET		IN		BIGINT
);
PROCEDURE FRAGMENT_MOVE(
	LOB_LOC			IN OUT	CLOB,
	AMOUNT			IN		BIGINT,
	SRC_OFFSET		IN		BIGINT,
	DEST_OFFSET		IN		BIGINT
);
```

参数详解

  * LOB_LOC

临时大对象。

  * AMOUNT

需要移动的数据总数，BLOB 为字节，CLOB 为字符。

  * SRC_OFFSET

待移动数据的起始偏移。BLOB 为字节，CLOB 为字符。

  * DEST_OFFSET

目标偏移。BLOB 为字节，CLOB 为字符。dest_offset 不能在(SRC_offset, SRC_offset + amount)上取值。



错误说明：

SRC_OFFSET 或 DEST_OFFSET 小于 1，SRC_OFFSET 或 DEST_OFFSET  大于原字段长，AMOUNT 小于 0， AMOUNT + SRCOFFSET 大于原字段长度，返回错误。

  16. FRAGMENT_REPLACE



替换从指定偏移位置的指定长度的数据为新数据，长度不超过 32K。

语法如下：

```
PROCEDURE FRAGMENT_REPLACE(
	LOB_LOC		IN OUT	BLOB,
	OLD_AMOUNT	IN		BIGINT,
	NEW_AMOUNT	IN		BIGINT,
	OFFSET		IN		BIGINT,
	BUFFER		IN		VARBINARY
);
PROCEDURE FRAGMENT_REPLACE(
	LOB_LOC		IN OUT	CLOB,
	OLD_AMOUNT	IN		BIGINT,
	NEW_AMOUNT	IN		BIGINT,
	OFFSET		IN		BIGINT,
	BUFFER		IN		VARCHAR2
);
```

参数详解

  * LOB_LOC

临时大对象。

  * OLD_AMOUNT

需要被替换数据的总数，BLOB 为字节，CLOB 为字符。

  * NEW_AMOUNT

替换数据的总数，BLOB 为字节，CLOB 为字符，最大 32K。

  * OFFSET

被替换数据的起始偏移。

  * BUFFER

需要替换的数据。



使用说明

OFFSET 小于 1， OLD_AMOUNT 或 NEW_AMOUNT 小于 0，OLD_AMOUNT 或者 NEW_AMOUNT 大于 32K，NEW_AMOUNT 大于 BUFFER 内数据长度，将返回错误。

  17. FREETEMPORARY



释放临时的 LOB 操作符。

语法如下：

```
PROCEDURE FREETEMPORARY(LOB_LOC IN OUT BLOB);
PROCEDURE FREETEMPORARY(LOB_LOC IN OUT CLOB);
```

参数详解

  * LOB_LOC

需要被释放的临时大对象



  18. GETLENGTH



返回 LOB 数据的字符长度。

语法如下：

```
FUNCTION GETLENGTH(LOB_LOC IN BLOB) RETURN BIGINT;
FUNCTION GETLENGTH(LOB_LOC IN CLOB) RETURN BIGINT;
FUNCTION GETLENGTH(FILE_LOC IN BFILE) RETURN BIGINT;
```

参数详解

  * LOB_LOC

临时大对象。

  * FILE_LOC

BFILE 类型对象。



返回值

临时大对象的数据长度。BLOB 为字节，CLOB 为字符。

  19. GET_STORAGE_LIMIT



返回指定 LOB 的存储长度上限。

语法如下：

```
FUNCTION GET_STORAGE_LIMIT(LOB_LOC IN BLOB) RETURN BIGINT;
FUNCTION GET_STORAGE_LIMIT(LOB_LOC IN CLOB) RETURN BIGINT;
```

参数详解

  * LOB_LOC

大字段对象。



返回值

大字段存储限制大小。DM 等同于最大长度。

  20. INSTR



返回从指定偏移开始的第 NTH 个匹配对象的位置。

语法如下：

```
FUNCTION INSTR(
	LOB_LOC		IN	BLOB,
	PATTERN		IN	VARBINARY,
	OFFSET		IN	BIGINT := 1,
	NTH			IN	BIGINT := 1
) RETURN BIGINT;
FUNCTION INSTR(
	LOB_LOC		IN	CLOB,
	PATTERN		IN	VARCHAR2,
	OFFSET		IN	BIGINT := 1,
	NTH			IN	BIGINT := 1
) RETURN BIGINT;
```

参数详解

  * LOB_LOC

临时大对象。

  * PATTERN

匹配符。

  * OFFSET

查找起始偏移，BLOB 为字节，CLOB 为字符。

  * NTH

查找的第几个匹配符。



返回值

找到的匹配符出现的偏移。BLOB 为字节，CLOB 为字符，找不到匹配串返回 0。

错误说明：

lob_loc 为 NULL、offset\<1 或 nth\<1，返回 NULL。

offset>LOBMAXSIZE 或 nth>LOBMAXSIZE，返回错误。

  21. ISOPEN



判断指定的 LOB 对象是否已经打开。

语法如下：

```
FUNCTION ISOPEN(LOB_LOC IN BLOB) RETURN INTEGER;
FUNCTION ISOPEN(LOB_LOC IN CLOB) RETURN INTEGER;
FUNCTION ISOPEN(FILE_LOC IN BFILE) RETURN INTEGER;
```

参数详解

  * LOB_LOC

LOB 类型对象。

  * FILE_LOC

BFILE 类型对象。



返回值

0：未打开

1：打开

  22. ISTEMPORARY



返回是否指定的 LOB 是临时操作符。

语法如下：

```
FUNCTION ISTEMPORARY(LOB_LOC IN BLOB) RETURN INTEGER;
FUNCTION ISTEMPORARY(LOB_LOC IN CLOB) RETURN INTEGER;
```

参数详解

  * LOB_LOC

大对象。



返回值

1：是临时大对象；

0：不是临时大对象；

NULL：输入参数为 NULL 或没有与表的大字段列进行关联的大字段对象。

  23. LOADBLOBFROMFILE



从 BFILE 对象加载数据到 BLOB 对象。

语法如下：

```
PROCEDURE LOADBLOBFROMFILE(
	DEST_LOB	IN OUT	BLOB,
	SRC_BFILE	IN		BFILE,
	AMOUNT		IN		BIGINT,
	DEST_OFFSET	IN OUT	BIGINT,
	SRC_OFFSET	IN OUT	BIGINT
);
```

参数详解

  * DEST_LOB

目标 BLOB 对象。

  * SRC_BFILE

源 BFILE 对象。

  * AMOUNT

要从 BFILE 对象加载到 BLOB 对象的字节数。

  * DEST_OFFSET

开始写入 BLOB 对象的偏移。

  * SRC_OFFSET

从 BFILE 对象开始读取的偏移。



  24. LOADCLOBFROMFILE



从 BFILE 对象加载数据到 CLOB 对象。

语法如下：

```
PROCEDURE LOADCLOBFROMFILE(
	DEST_LOB		IN OUT		CLOB,
	SRC_BFILE		IN			BFILE,
	AMOUNT			IN			BIGINT,
	DEST_OFFSET		IN OUT		BIGINT,
	SRC_OFFSET		IN OUT		BIGINT,
	BFILE_CSID     	IN     		NUMBER,
	LANG_CONTEXT	IN OUT 		INTEGER,
	WARNING      	OUT  		INTEGER
);
```

参数详解

  * DEST_LOB 输入：

目标 CLOB 对象。输出：结果 CLOB 对象。

  * SRC_BFILE

源 BFILE 对象。

  * AMOUNT

要从 BFILE 对象加载到 CLOB 对象的字节数。

  * DEST_OFFSET

输入：开始写入 CLOB 对象的字符偏移。输出：结束写入的字符偏移。

  * SRC_OFFSET

输入：从 BFILE 对象开始读取的字节偏移，用户需确保此偏移位置为某字符的第一个字节。输出：结束读取的字节偏移。

  * BFILE_CSID

源 BFILE 的编码集。



支持的编码集：



  * LANG_CONTEXT，WARNING

这两个参数暂时不支持，调用时指定任意值即可。



错误说明：

输入参数为 NULL，返回错误。

AMOUNT、SRC_OFFSET 或 DEST_OFFSET 小于 1 或大于 LOBMAXSIZE，返回错误。

SRC_OFFSET 大于文件长度、DEST_OFFSET+AMOUNT-1 大于 LOB 最大长度、AMOUNT+SRC_OFFSET-1 超过文件长度，返回错误。

对于 EUC_KR 编码的数据库，BFILE_CSID 仅支持缺省值，若为其他值，返回错误。

  25. LOADFROMFILE



从 BFILE 对象加载数据到 BLOB 对象。

语法如下：

```
PROCEDURE LOADFROMFILE(
	DEST_LOB	IN OUT	BLOB,
	SRC_FILE	IN		BFILE,
	AMOUNT		IN		BIGINT,
	DEST_OFFSET	IN OUT	BIGINT:=1,
	SRC_OFFSET	IN OUT	BIGINT:=1
);
```

参数详解

  * DEST_LOB

目标 BLOB 对象。

  * SRC_FILE

源 BFILE 对象。

  * AMOUNT

要从 BFILE 对象加载到 BLOB 对象的字节数。

  * DEST_OFFSET

开始写入 BLOB 对象的偏移。

  * SRC_OFFSET

从 BFILE 对象开始读取的偏移。



  26. OPEN



打开一个 LOB 对象。

语法如下：

```
PROCEDURE OPEN(
	LOB_LOC		IN OUT	BLOB,
	OPEN_MODE	IN		INTEGER
);
PROCEDURE OPEN(
	LOB_LOC		IN OUT	CLOB,
	OPEN_MODE	IN		INTEGER
);
PROCEDURE OPEN(
	FILE_LOC	IN OUT	BFILE,
	OPEN_MODE	IN		INTEGER:=FILE_READONLY
);
```

参数详解

  * LOB_LOC

LOB 对象。

  * FILE_LOC

BFILE 类型对象。

  * OPEN_MODE

打开方式。仅支持对 BFILE 对象使用 FILE_READONLY 参数。参数
LOB_READONLY、LOB_READWRITE，仅提供语法支持，使用时不报错，也不产生其它效果。



  27. READ



读取指定偏移开始的指定长度数据到 buffer 中。

语法如下：

```
PROCEDURE READ(
	LOB_LOC		IN		BLOB,
	AMOUNT		IN OUT	INTEGER,
	OFFSET		IN		BIGINT,
	BUFFER		OUT		VARBINARY
);
PROCEDURE READ(
	LOB_LOC		IN		CLOB,
	AMOUNT		IN OUT	INTEGER,
	OFFSET		IN		BIGINT,
	BUFFER		OUT		VARCHAR2
);
PROCEDURE READ(
	FILE_LOC	IN		BFILE,
	AMOUNT		IN OUT	INTEGER,
	OFFSET		IN		BIGINT,
	BUFFER		OUT		VARBINARY
);
```

参数详解

  * LOB_LOC

临时大对象。

  * FILE_LOC

BFILE 对象。

  * AMOUNT

输入需要读取的数据总数，BLOB 为字节，CLOB 为字符，输出为实际读取的总数。

  * OFFSET

读取数据的偏移，BLOB 为字节，CLOB 为字符。

  * BUFFER

存储数据的缓冲区。



错误说明：

AMOUNT 为 NULL、小于等于 0 或大于 32767，返回错误。

OFFSET 为 NULL、小于等于 0 或超过原字段长度，返回错误。

  28. SUBSTR



获取指定偏移开始的指定长度的子串。

语法如下：

```
FUNCTION SUBSTR(
	LOB_LOC		IN	BLOB,
	AMOUNT		IN	INTEGER := 32767,
	OFFSET		IN	BIGINT := 1
) RETURN VARBINARY;
FUNCTION SUBSTR(
	LOB_LOC		IN	CLOB,
	AMOUNT		IN	INTEGER := 32767,
	OFFSET		IN	BIGINT := 1
) RETURN VARCHAR2;
FUNCTION SUBSTR(
	FILE_LOC	IN	BFILE,
	AMOUNT		IN	INTEGER := 32767,
	OFFSET		IN	BIGINT := 1
) RETURN VARBINARY;
```

参数详解

  * LOB_LOC ：

临时大对象。

  * FILE_LOC

BFILE 对象。

  * AMOUNT

读取的数据长度，BLOB 为字节，CLOB 为字符，上限为 32767，实际获取为大对象长度与 AMOUNT 的小值。

  * OFFSET

读取数据的起始偏移。



返回值

返回指定偏移开始的指定长度的子串。若输入的 AMOUNT 或 OFFSET 参数非法，返回 NULL。

  29. TRIM/TRIM_LOB



截断 LOB 数据到指定的长度。

语法如下：

```
PROCEDURE TRIM(
	LOB_LOC			IN OUT	BLOB,
	NEW_LEN 		IN 		BIGINT
);
PROCEDURE TRIM(
	LOB_LOC 		IN OUT	CLOB,
	NEW_LEN 		IN		BIGINT
);
PROCEDURE TRIM_LOB(
	LOB_LOC 		IN OUT	BLOB,
	NEW_LEN 		IN		BIGINT
);
PROCEDURE TRIM_LOB(
	LOB_LOC 		IN OUT	CLOB,
	NEW_LEN 		IN		BIGINT
);
```

参数详解

  * LOB_LOC

临时大对象。

  * NEW_LEN

截断到的新长度，BLOB 为字节，CLOB 为字符。



  30. WRITEAPPEND



追加指定长度字符的数据到 LOB 对象的末尾。

语法如下：

```
PROCEDURE WRITEAPPEND(
	LOB_LOC		IN OUT 	BLOB,
	AMOUNT 		IN		INTEGER,
	BUFFER 		IN 		VARBINARY
);
PROCEDURE WRITEAPPEND(
	LOB_LOC 	IN OUT 	CLOB,
	AMOUNT 		IN 		INTEGER,
	BUFFER 		IN 		VARCHAR2
);
```

参数详解

  * LOB_LOC

临时大对象。

  * AMOUNT

追加数据的总数，BLOB 为字节，CLOB 为字符。

  * BUFFER

待追加的数据。



错误说明：

AMOUNT 小于等于 0 或 NULL 或者超过 BUFFER 内数据长度，返回错误。

  31. WRITE



写入指定长度字符的数据到 LOB 对象，从 LOB 对象的指定绝对偏移开始。如果指定的位置有数据，则新写入的会覆盖原来的数据。

语法如下：

```
PROCEDURE WRITE (
	LOB_LOC 	IN OUT 	BLOB,
	AMOUNT 		IN 		INTEGER,
	OFFSET		IN 		BIGINT,
	BUFFER		IN 		VARBINARY
);
PROCEDURE WRITE (
	LOB_LOC 	IN OUT 	CLOB,
	AMOUNT 		IN 		INTEGER,
	OFFSET		IN 		BIGINT,
	BUFFER 		IN 		VARCHAR2
);
```

参数详解

  * LOB_LOC

临时大对象。

  * AMOUNT

写入数据的总数，BLOB 为字节，CLOB 为字符。

  * OFFSET

偏移量，起始为 1。BLOB 为字节，CLOB 为字符。

  * BUFFER

待写入的数据。



错误说明：

AMOUNT 小于等于 0 或 NULL 或者超过 BUFFER 内数据长度，返回错误。

  32. CONVERTTOBLOB



将指定的 LOB 的指定部分按一定的编码格式完成 BLOB 和 CLOB 的互相转换。

语法如下：

```
PROCEDURE CONVERTTOBLOB(
	DEST_LOB		IN OUT 		BLOB,
	SRC_CLOB       	IN      	CLOB,
	AMOUNT			IN      	BIGINT,
	DEST_OFFSET    	IN OUT 		BIGINT,
	SRC_OFFSET     	IN OUT 		BIGINT, 
	BLOB_CSID      	IN      	NUMBER,
	LANG_CONTEXT 	IN OUT		INTEGER,
	WARNING       	OUT  		INTEGER
);
PROCEDURE CONVERTTOCLOB(
	DEST_LOB       	IN OUT		CLOB,
	SRC_BLOB      	IN     		BLOB,
	AMOUNT      	IN			BIGINT,
	DEST_OFFSET    	IN OUT		BIGINT,
	SRC_OFFSET    	IN OUT		BIGINT,
	BLOB_CSID     	IN     		NUMBER,
	LANG_CONTEXT	IN OUT 		INTEGER,
	WARNING      	OUT  		INTEGER
);
```

参数详解

  * DEST_LOB

输入输出参数，转换的结果将存入到这个大字段中，如果这个大字段原来已经有数据，新转换出的数据会根据指定的目标偏移覆盖原数据。

  * SRC_LOB

待转换的数据。

  * AMOUNT

转换的数据总数，BLOB 为字节，CLOB 为字符。

  * DEST_OFFSET

完成转换的数据向目的大字段存放时的偏移。

  * SRC_OFFSET

源数据开始转换的偏移。

  * BLOB_CSID,LANG_CONTEXT,WARNING

这三个参数暂时不支持，调用时指定任意值即可，CLOB 转 BLOB 时，最后 BLOB 编码格式和 CLOB 的存入格式相同。



错误说明：

如果 AMOUNT 大于 SRC_LOB 的长度、AMOUNT 小于 0、 DEST_OFFSET+AMOUNT 大于大字段限制最大长度（DBMS_LOB.LOBMAXSIZE），返回错误。

  33. GETCHUNKSIZE



计算出块大小的字节数。

语法如下：

```
FUNCTION GETCHUNKSIZE(
	LOB_LOC   IN  BLOB
) RETURN INT;

FUNCTION GETCHUNKSIZE(
	LOB_LOC   IN  CLOB
) RETURN INT;
```

参数详解

  * LOB_LOC



输入参数，LOB 类型对象。

返回值

返回控制项当前最大项的相对页大小的整数倍乘以页大小的字节数。控制项为 0 时返回当前大字段相对页大小的整数倍乘以页大小的字节数。

## 7.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_LOB');
```

例 将输入的 BLOB 类型数据转化为 CLOB 类型。

```
CREATE OR REPLACE FUNCTION BLOBTOCLOB  ( BLOB_IN IN BLOB)
        RETURN CLOB
AS
        V_CLOB CLOB;
        V_VARCHAR VARCHAR2(32767);
        V_START PLS_INTEGER  := 1;
        V_BUFFER PLS_INTEGER := 32767;
BEGIN
        DBMS_LOB.CREATETEMPORARY(V_CLOB, TRUE);
        FOR I IN 1 .. CEIL(DBMS_LOB.GETLENGTH(BLOB_IN) / V_BUFFER) + 1
        LOOP
            V_VARCHAR := UTL_RAW.CAST_TO_VARCHAR2(DBMS_LOB.SUBSTR(BLOB_IN, V_BUFFER, V_START));
            DBMS_LOB.WRITEAPPEND(V_CLOB, LENGTH(V_VARCHAR), V_VARCHAR);
            PRINT V_CLOB;
            V_START := V_START + V_BUFFER;
        END LOOP;
        RETURN V_CLOB;
END BLOBTOCLOB;
/
```

调用函数：

```
SELECT BLOBTOCLOB('B4EFC3CECAFDBEDDBFE2D3D0CFDEB9ABCBBE');
```

结果如下：

```
达梦数据库有限公司
```

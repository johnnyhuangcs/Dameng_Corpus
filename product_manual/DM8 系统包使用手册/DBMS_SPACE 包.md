

# DBMS_SPACE 包

为了展示所有物理对象（文件、页）和逻辑对象（表空间、簇、段）的存储空间信息。通过 DBMS_SPACE 包来获取表空间、文件、页、簇、段的内容。

## 17.1 使用前提

DBMS_SPACE 包的使用依赖 DBMS_PAGE 包，而 DBMS_PAGE 包的使用依赖 DBMS_BINARY 包。所以，在创建 DBMS_SPACE 包之前，请先成功创建 DBMS_PAGE 包和 DBMS_BINARY 包。

## 17.2 数据类型

DBMS_SPACE 包中涉及到的变量和记录类型。如下统一说明：

|**包内变量和记录类型**|**解释**|
|---------|---------|
|TS_T|表空间记录类型，用于记录表空间的信息|
|TS_ARR_T|表空间记录类型数组|
|TS_ALL_ARR_T|表空间 ID 数组|
|FILE_T|文件记录类型，用于记录文件的信息|
|FILE_ARR_T|文件记录类型数组|
|FILE_ALL_ARR_T|文件的 ID 数组|
|SEG_T|段记录类型，用于记录段的信息|
|SEG_ARR_T|段记录类型数组|
|SEG_ID_ARR_T|段 ID 数组|
|EXTENT_T|簇记录类型，用于记录簇的信息|
|EXTENT_ARR_T|簇记录类型数组|
|EXTENT_SIZE|簇的大小（页为单位）|
|PAGE_ADDR_T|页地址记录类型，用于记录页地址的信息|
|PAGE_ADDR_ARR_T|页地址记录类型数组|
|PAGE_N|描述页能描述的页数|


下面为 DBMS_SPACE 包中记录类型的具体定义：

  1. TS_T



TS_T 为表空间记录类型，用于记录表空间的信息，具体数据信息来源于 V$TABLESPACE，类型定义如下：

```
TYPE TS_T IS RECORD(
	ID 			INT,
	NAME        VARCHAR(128),
	CACHE       VARCHAR(20),
	TYPE        TINYINT, 
	STATUS      TINYINT,
	MAX_SIZE    BIGINT, 
	TOTAL_SIZE  BIGINT,
	FILE_NUM    SMALLINT 
);
```

参数详解

  * ID

表空间 ID。

  * NAME

表空间名称。

  * CACHE

缓冲区名。

  * TYPE

表空间类型，1：DB 类型，2：临时表空间。

  * STATUS

表空间状态。0：ONLINE；1：OFFLINE；2：RES_OFFLINE；3：CORRUPT。

  * MAX_SIZE

表空间的最大大小，以页为单位。0 代表只受操作系统限制（暂无实际意义）。

  * TOTAL_SIZE

表空间的总大小，以页为单位。

  * FILE_NUM

包含的文件数。



  2. TS_ARR_T



TS_ARR_T 为表空间记录类型数组，类型定义如下：

```
TYPE TS_ARR_T IS ARRAY TS_T[];
```

  3. TS_ALL_ARR_T



TS_ALL_ARR_T 为表空间 ID 数组，类型定义如下：

```
TYPE TS_ALL_ARR_T IS ARRAY INT[];
```

  4. FILE_T



FILE_T 为文件记录类型，用于记录文件的信息，具体数据信息来源于 V$DATAFILE，类型定义如下：

```
TYPE FILE_T IS RECORD(
    PATH	               VARCHAR(256), 
    CREATE_TIME 	       TIMESTAMP,
    MODIFY_TIME   	       TIMESTAMP, 
    TOTAL_SIZE 	           BIGINT, 
    FREE_SIZE	           BIGINT, 
    FREE_PAGE_NO	       BIGINT, 
    PAGE_SIZE 	           INT, 
    AUTOEXTEND  	       INT,
    MAX_SIZE	           INT,
    NEXT_SIZE              INT 
);
```

参数详解

  * PATH

数据库文件路径。

  * CREATE_TIME

数据库文件创建时间。

  * MODIFY_TIME

文件修改的时间。

  * TOTAL_SIZE

文件的总大小，以页为单位。

  * FREE_SIZE

未分配的空闲大小，以页为单位。

  * FREE_PAGE_NO

数据文件中连续空白页的起始页号。

  * PAGE_SIZE

页大小，单位 BYTE。

  * AUTOEXTEND

是否支持自动扩展，1：支持；0：不支持。

  * MAX_SIZE

文件最大大小，以 MB 为单位。

  * NEXT_SIZE

文件每次扩展大小，以 MB 为单位。创建文件时没有指定则返回 0。



  5. FILE_ARR_T



FILE_ARR_T 为文件记录类型数组，类型定义如下：

```
TYPE FILE_ARR_T IS ARRAY FILE_T[];
```

  6. FILE_ALL_ARR_T



FILE_ALL_ARR_T 为文件的 ID 数组，类型定义如下：

```
TYPE FILE_ALL_ARR_T IS ARRAY INT[];
```

  7. SEG_T



SEG_T 为段记录类型，用于记录段的信息，类型定义如下：

```
TYPE SEG_T IS RECORD(
    SEGID                 INT,
    INODE_FILE_ID	   SMALLINT,	 
    INODE_PAGE_NO	   INT, 
    INODE_OFFSET	   SMALLINT, 
    N_FULL_EXTENT        INT, 
    N_FRAG_EXTENT	   INT, 
    N_FREE_EXTENT	   INT,
    N_USED_IN_FRAG     INT
);  
```

参数详解

  * SEGID

段 ID。

  * INODE_FILE_ID

段 INODE 项所在的文件 ID。

  * INODE_PAGE_NO

段 INODE 项所在的页号。

  * INODE_OFFSET

段 INODE 项的页偏移。

  * N_FULL_EXTENT

全满簇的个数。

  * N_FRAG_EXTENT

半满簇的个数。

  * N_FREE_EXTENT

空闲簇的个数。

  * N_USED_IN_FRAG

半满簇中使用页的总数量。



  8. SEG_ARR_T



SEG_ARR_T 为段记录类型数组，类型定义如下：

```
TYPE SEG_ARR_T IS ARRAY SEG_T[];
```

  9. SEG_ID_ARR_T



SEG_ID_ARR_T 为段 ID 数组，类型定义如下：

```
TYPE SEG_ID_ARR_T IS ARRAY INT[];
```

  10. EXTENT_T



EXTENT_T 为簇记录类型，用于记录簇的信息，类型定义如下：

```
TYPE EXTENT_T IS RECORD(
    TSID                  SMALLINT,
    SEGID                 INT,
    STATE 		          VARCHAR(5),
    BITMAP                VARCHAR(64), 
    EXTENT_FILE_ID        SMALLINT,
    EXTENT_PAGE_NO        INT,
    EXTENT_OFFSET         SMALLINT,
    FIRST_PAGE_NO         INT,
    LAST_PAGE_NO          INT, 
    NEXT_LINK_FILE_ID     SMALLINT, 
    NEXT_LINK_PAGE_NO     INT, 
    NEXT_LINK_OFFSET      SMALLINT
);
```

参数详解

  * TSID

表空间 ID。

  * SEGID

段 ID。

  * STATE

簇状态，NOUSE：未使用；FULL：全满；FRAG：半满；FREE：空闲；Unknown：未知。

  * BITMAP

簇的页标记位图。

  * EXTENT_FILE_ID

簇描述项的文件 ID。

  * EXTENT_PAGE_NO

簇描述项所在页号。

  * EXTENT_OFFSET

簇描述项的页偏移。

  * FIRST_PAGE_NO

簇的起始页号。

  * LAST_PAGE_NO

簇的终止页号。

  * NEXT_LINK_FILE_ID

下一个簇描述项的文件号 ID。

  * NEXT_LINK_PAGE_NO

下一个簇描述项的页号。

  * NEXT_LINK_OFFSET

下一个簇描述项的页偏移。



  11. EXTENT_ARR_T



EXTENT_ARR_T 为簇记录类型数组，类型定义如下：

```
TYPE EXTENT_ARR_T IS ARRAY EXTENT_T[];
```

  12. PAGE_ADDR_T



PAGE_ADDR_T 为页记录类型，用于记录页的信息，类型定义如下：

```
TYPE PAGE_ADDR_T IS RECORD(
    TS_ID                  SMALLINT,
	FILE_ID		           SMALLINT, 
    PAGE_NO                INT 
);
```

参数详解

  * TS_ID

页所在的表空间 ID。

  * FILE_ID

页所在的文件 ID。

  * PAGE_NO

页号。



  13. PAGE_ADDR_ARR_T



PAGE_ADDR_ARR_T 为页记录类型数组，类型定义如下：

```
TYPE PAGE_ADDR_ARR_T IS ARRAY PAGE_ADDR_T[];
```

## 17.3 相关方法

DBMS_SPACE 包中包含的过程和函数如下详细介绍：

  1. TS_LOAD/ TS_GET



根据输入的表空间 id，获得表空间信息。过程和函数功能相同。

语法如下：

```
PROCEDURE TS_LOAD(
	TSID		IN		SMALLINT, 
	TS_ARR		OUT		TS_ARR_T
);
```

语法如下：

```
FUNCTION  TS_GET (
	TSID		IN		SMALLINT
)RETURN TS_ARR_T; 
```

参数详解

  * tsid

表空间 id。

  * ts_arr_T

表空间记录类型数组。



  2. TS_N_LOAD / TS_N_GET



获得数据库中表空间的个数。过程和函数功能相同。

语法如下：

```
PROCEDURE TS_N_LOAD(
	NUMBER		OUT		INT
);
```

语法如下：

```
FUNCTION TS_N_GET 
RETURN INT;
```

参数详解

  * NUMBER

表空间的个数。



  3. TS_ALL_LOAD/ ts_all_get



获得所有表空间的 ID。过程和函数功能相同。

语法如下：

```
PROCEDURE TS_ALL_LOAD(
	TS_ALL_ARR	OUT		TS_ALL_ARR_T,
	OFFSET		IN		INT		DEFAULT 1,
	NUM			IN		INT		DEFAULT 100
);
```

语法如下：

```
FUNCTION TS_ALL_GET(
	OFFSET		IN		INT		DEFAULT 1,
	NUM			IN		INT		DEFAULT 100
) RETURN TS_ALL_ARR_T;
```

参数详解

  * offset

数组起始位置。

  * num

数组长度

  * TS_ALL_ARR

表空间 ID



  4. FILE_LOAD/ FILE_GET



根据输入的表空间 id、文件 id，获得文件信息。过程和函数功能相同。

语法如下：

```
PROCEDURE FILE_LOAD(
	TS_ID		IN		SMALLINT,
	FILE_ID		IN		SMALLINT, 
	FILE_ARR	OUT		FILE_ARR_T
);
```

语法如下：

```
FUNCTION FILE_GET(
	TS_ID		IN		SMALLINT, 
	FILE_ID		IN		SMALLINT
) RETURN FILE_ARR_T;
```

参数详解

  * tsid

表空间 id。

  * file_id

文件号。

  * FILL_ARR_T

文件记录类型数组。



  5. FILE_ALL_LOAD/ FILE_ALL_GET



获得所有文件的 ID。过程和函数功能相同。

语法如下：

```
PROCEDURE FILE_ALL_LOAD(
	TS_ID			IN		SMALLINT, 
	FILE_ALL_ARR	OUT		FILE_ALL_ARR_T, 
	OFFSET			IN		INT		DEFAULT 1, 
	NUM				IN		INT		DEFAULT 100
);
```

语法如下：

```
FUNCTION FILE_ALL_GET(
	TS_ID		IN		SMALLINT, 
	OFFSET		IN		INT		DEFAULT 1,
	NUM			IN		INT		DEFAULT 100
)RETURN FILE_ALL_ARR_T;
```

参数详解

  * tsid

表空间 id。

  * FILE_ALL_ARR

文件的 ID。

  * OFFSET

数组起始位置。

  * NUM

数组的长度。



  6. SEG_LOAD_BY_ID/SEG_GET_BY_ID



根据输入的表空间 id、段号获得段信息。过程和函数功能相同。

语法如下：

```
PROCEDURE SEG_LOAD_BY_ID(
	TS_ID		IN		SMALLINT, 
	SEGID		IN		INT, 
	SEG_INFO	OUT		SEG_T
);
```

语法如下：

```
FUNCTION SEG_GET_BY_ID(
	TS_ID		IN		SMALLINT,
	SEGID		IN		INT
)RETURN SEG_T; 
```

参数详解

  * tsid

表空间 id。

  * segid

段号。

  * SEG_INFO

段的信息。

  * SEG_T

段记录类型。



  7. SEG_LOAD_BY_HEADER/ SEG_GET_BY_HEADER



根据输入的表空间 id、段头的文件号、页号、页内偏移，获得段信息，其中页内偏移取值只能为 62 或 72。过程和函数功能相同。

语法如下：

```
PROCEDURE SEG_LOAD_BY_HEADER(
	TS_ID				IN		SMALLINT, 
	HEADER_FILE_ID		IN		SMALLINT, 
	HEADER_PAGE_NO		IN		INT,
	HEADER_OFFSET 		IN 		SMALLINT, 
	SEG_INFO  			OUT 	SEG_T
);
```

语法如下：

```
FUNCTION SEG_GET_BY_HEADER(
	TS_ID 				IN 		SMALLINT, 
	HEADER_FILE_ID 		IN 		SMALLINT, 
	HEADER_PAGE_NO 		IN 		INT, 
	HEADER_OFFSET 		IN 		SMALLINT
)RETURN SEG_T; 
```

参数详解

  * tsid

表空间 id。

  * header_file_id

段头文件号。

  * header_page_no

段头页号。

  * header_offset

段头偏移量。

  * SEG_INFO

段的信息。



  8. SEG_ALL_LOAD/ seg_all_get



根据表空间 id、数组相对起始位置、数组长度，获得数据库中表空间中所有段的 id 信息。过程和函数功能相同。

语法如下：

```
PROCEDURE SEG_ALL_LOAD(
	TS_ID 			IN  		SMALLINT, 
	SEG_ID_ARR 		OUT 		SEG_ID_ARR_T,
	OFFSET 			IN 		INT 		DEFAULT 1,
	NUM 				IN 		INT 		DEFAULT 100
);  
```

语法如下：

```
FUNCTION SEG_ALL_GET(
	TS_ID      		IN  		SMALLINT,
	OFFSET     		IN  		INT 		DEFAULT 1, 
	NUM	       		IN  		INT 		DEFAULT 100   
)RETURN SEG_ID_ARR_T;
```

参数详解

  * tsid

表空间 id。

  * offset

数组起始位置。

  * num

数组长度。

  * seg_id_arr_t

段 id 信息。



  9. SEG_EXTENT_LST_LOAD/ seg_extent_lst_get



根据输入的表空间 id、段号、链表类型、链表方向、链表相对起始位置、指定链表长度，获得 FULL、FREE 或 FRAG 链表的信息。过程和函数功能相同。

语法如下：

```
PROCEDURE SEG_EXTENT_LST_LOAD(
	TS_ID  			IN  		SMALLINT, 
	SEGID 			IN 			INT,
	EXTENT_ARR 		OUT 		EXTENT_ARR_T, 
	LINKTYPE 		IN  		VARCHAR(5) 		DEFAULT 'FULL',
	DIRECT 			IN  		VARCHAR(10) 	DEFAULT 'FORWARD', 
	OFFSET 			IN  		INT 			DEFAULT 1,
	NUM 			IN  		INT 			DEFAULT 100
);
```

语法如下：

```
FUNCTION SEG_EXTENT_LST_GET(
	TSID   			IN  		SMALLINT, 
	SEGID 			IN  		SMALLINT, 
	LINKTYPE  		IN  	 	VARCHAR(5) 		DEFAULT 'FULL',
	DIRECT     		IN  		VARCHAR(10) 	DEFAULT 'FORWARD',
	OFFSET      	IN   		INT 			DEFAULT 1,
	NUM     		IN   		INT 			DEFAULT 100,
) RETURN EXTENT_ARR_T;
```

参数详解

  * tsid

表空间 id。

  * segid

段 id。

  * linktype

链表类型。

  * direct

链表方向。

  * offset

链表相对起始位置。

  * num

指定链表长度。

  * EXTENT_ARR_t

簇信息结构体链表



  10. EXTENT_SIZE_LOAD/ extent_size_get



获得簇的大小（页为单位）。过程和函数功能相同。

语法如下：

```
PROCEDURE EXTENT_SIZE_LOAD(
	EXTENT_SIZE 	OUT 		INT
);
```

语法如下：

```
FUNCTION  EXTENT_SIZE_GET  RETURN INT;
```

  11. EXTENT_XDESC_PAGE_NO_LOAD/extent_xdesc_page_no_get



根据输入的表空间 id、文件 id，获得所有簇描述页的信息。过程和函数功能相同。

语法如下：

```
PROCEDURE EXTENT_XDESC_PAGE_NO_LOAD(
	TS_ID 					SMALLINT, 
	FILE_ID 				SMALLINT, 
	XPAGE_ADDR_ARR 	OUT 	PAGE_ADDR_ARR_T,
	OFFSET 					INT 		DEFAULT 1, 
	NUM  					INT 		DEFAULT 100  
);
```

语法如下：

```
FUNCTION EXTENT_XDESC_PAGE_NO_GET(
	TSID   			IN   	SMALLINT,
	FILE_ID   		IN   	SMALLINT,
	OFFSET      	IN   	INT 		DEFAULT 1,
	NUM				IN   	INT 		DEFAULT 100
)RETURN PAGE_ADDR_ARR_T;
```

参数详解

  * tsid

表空间 id。

  * file_id

文件 id。

  * offset

数组的起始位置。

  * num

指定输出的长度。

  * page_addr_arr_t

页地址记录类型数组。



  12. PAGE_N_LOAD/ PAGE_N_GET



获得一个描述页能描述的页数。过程和函数功能相同。

语法如下：

```
PROCEDURE PAGE_N_LOAD(
	PAGE_N 		OUT 		INT
);
```

语法如下：

```
FUNCTION PAGE_N_GET
RETURN INT;
```

参数详解

  * PAGE_N

描述页能描述的页数。



  13. IPAGE_NO_ALL_LOAD/ IPAGE_NO_ALL_GET



输入表空间 id、inode 页链表类型 full 或 free 链表方向、链表相对起始位置、指定链表长度，输出所有 inode 页的页号。过程和函数功能相同。

语法如下：

```
PROCEDURE IPAGE_NO_ALL_LOAD(
	TS_ID  			IN  	SMALLINT,
	PAGE_ADDR_ARR 	OUT 	PAGE_ADDR_ARR_T ,
	LINKTYPE  		IN 		VARCHAR(5) 		DEFAULT 'FULL', 
	DIRECT 			IN 		VARCHAR(10) 	DEFAULT 'FORWARD', 
	OFFSET 			IN 		INT 			DEFAULT 1, 
	NUM 			IN 		INT 			DEFAULT 100 
);
```

语法如下：

```
FUNCTION IPAGE_NO_ALL_GET(
	TS_ID  			IN  	SMALLINT,
	LINKTYPE  		IN		VARCHAR(5) 		DEFAULT 'FULL', 
	DIRECT 			IN 		VARCHAR(10) 	DEFAULT 'FORWARD', 
	OFFSET 			IN 		INT 			DEFAULT 1, 
	NUM 			IN 		INT 			DEFAULT 100
)RETURN PAGE_ADDR_ARR_T;
```

参数详解

  * tsid

表空间 id。

  * PAGE_ADDR_ARR_T

页地址记录类型数组。

  * linktypeinode

页链表类型：full 或 free。

  * direct

链表的方向。

  * offset

链表的相对起始位置。

  * num

指定链表长度。



## 17.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_BINARY');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_PAGE');
SP_CREATE_SYSTEM_PACKAGES(1,'DBMS_SPACE');
SET SERVEROUTPUT ON;  //PRINT需要设置这条语句，才能打印出消息
```

例 1 获取表空间信息（表空间 ID、表空间名、表空间类型：1 DB 类型，2 临时文件组、表空间状态、表空间的最大空间、表空间的总大小（页）、包含文件的个数）。

```
DECLARE
	TS_INFO DBMS_SPACE.TS_ARR_T;
 BEGIN
	TS_INFO = DBMS_SPACE.TS_GET(4);
	IF TS_INFO IS NOT NULL
 THEN
		SELECT * FROM ARRAY TS_INFO;
 ELSE PRINT 'TABLESPACE IS NULL';
 END IF;
END;
/
```

结果如下：

```
行号 ID NAME CACHE TYPE STATUS MAX_SIZE TOTAL_SIZE FILE_NUM
---------- ---------- ---- ----- ---------- ---------- -------------------
1   4   MAIN  1   0      0               16384       1
```

例 2 获得数据库中表空间的个数

```
DECLARE
TS_NUM INT;
BEGIN
TS_NUM = DBMS_SPACE.TS_N_GET;
PRINT 'TS_NUM:';
PRINT TS_NUM;
END;
/
```

结果如下：

```
TS_NUM:
5  //个数根据实际表空间个数不同而不同
```

例 3  获得文件信息（文件路径、文件创建时间、文件读写状态 1 读，2 写文件修改的时间、修改的事务 id、文件的总大小（MB）、文件的空闲大小（MB）、数据文件中连续空白页的起始页号、读页个数、写页个数、页大小（KB）、读请求个数、写请求个数、文件可扩展标记、文件最大大小（MB）、文件每次扩展大小（MB）、文件包含的总描述页的数目）

```
DECLARE
	FILE_INFO DBMS_SPACE.FILE_ARR_T;
BEGIN
	FILE_INFO = DBMS_SPACE.FILE_GET(4,0);
	IF FILE_INFO IS NOT NULL
	THEN
		SELECT * FROM ARRAY FILE_INFO;
	ELSE PRINT 'FILE IS NULL';
	END IF;
END;
/
```

结果如下：

```
行号 PATH CREATE_TIME MODIFY_TIME TOTAL_SIZE FREE_SIZE FREE_PAGE_NO PAGE_SIZE
AUTOEXTEND MAX_SIZE NEXT_SIZE
--------- ------------------------------- --------------------------
1 d:\database0718\DAMENG\MAIN.DBF 2012-07-19 09:47:55.000000 2012-08-07
09:43:33.000000 16384 16348 368 8192 1 0 0
```

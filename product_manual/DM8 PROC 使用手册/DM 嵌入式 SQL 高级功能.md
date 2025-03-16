

# DM 嵌入式 SQL 高级功能

## 6.1SSL 连接

DM 支持 SSL 加密通信方式，可以通过配置 dm_svc.conf 文件实现 PRO*C 程序的 SSL 通信。dm_svc.conf 是 DM 数据库安装时生成的一个配置文件，在 Windows 操作平台下此文件位于 %SystemRoot%\system32 目录，在 Linux 平台下此文件位于/etc 目录。

配置方法如下：

  1. **打开 SSL**



语法如下：

```
ENABLE_SSL=(参数)
```

参数说明：

  * 1：启动 SSL 配置；0：关闭 SSL 配置



  2. **配置具体用户的 SSL**



PRO*C 程序连接数据库时，自动将用户名与配置文件中配置了 SSL 连接的用户名进行匹配，如果能匹配成功，则对其使用 SSL 连接。

语法如下：

```
sSSL_CONFIG=((USER=(用户名1)SSL_PATH=(SSL路径1)SSL_PWD=(SSL key1))(USER=(用户名2)SSL_PATH=(SSL路径2)SSL_PWD=(SSL key2)))
```

参数说明：

  * USER：需要使用 SSL 登录的用户名，最大长度 128 字节
  * SSL_PATH：指定用户的 SSL 路径，最大长度 256 字节
  * SSL_PWD：指定用户 SSL 的 key，最大长度 128 字节
  * 完整的一个用户的 SSL 配置外面需要用括号扩起来，以分隔多个配置。



例 配置两个 SSL 用户，SYSDBA 和 SYSSSO，配置文件中相关配置项如下设置。

```
ENABLE_SSL=(1)

SSL_CONFIG=((USER=(SYSDBA)SSL_PATH=(C:\dmdbms\bin\client_ssl\SYSDBA)SSL_PWD=(*****))(USER=(SYSSSO)SSL_PATH=(C:\dmdbms\bin\client_ssl\SYSSSO)SSL_PWD=(*****)))
```

> **注意**
>
> dm_svc.conf配置文件还可以进行其他配置项的配置，这里只介绍了SSL相关配置，其他配置项可参考《DM8系统管理员手册》



## 6.2DMSQL 程序块

PRO*C 把 DMSQL 程序块视作单一的嵌入 SQL 语句，任何 SQL 语句能嵌入的地方也能嵌入 DMSQL 程序块。在宿主程序中嵌入 DMSQL 程序块，必须使用关键字 EXEC SQL EXECUTE 和 END-EXEC 将 DMSQL 程序块括起来。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] EXECUTE

BEGIN

plsql_block;

END;

END-EXEC;
```

例

```
EXEC SQL EXECUTE

	BEGIN

SELECT NAME, EMAIL, PHONE

            INTO :person_name:ind_name, :person_email, :person_phone

            FROM PERSON.PERSON

            WHERE PERSONID = :person_number;

	END;

END-EXEC;
```

## 6.3 使用大字段句柄处理 LOB 类型

DM PRO*C 中的大字段句柄，对应 DM 数据库服务器的大字段类型（BLOB、CLOB、LONGVARBINARY、LONGVARCHAR、IMAGE、TEXT）。大字段句柄分为 OCIBlobLocator 和 OCIClobLocator。

在使用大字段句柄前，需要先定义、申请、绑定大字段句柄；使用结束后，需要关闭和释放大字段句柄。

  1. **定义大字段句柄**



像定义变量一样定义大字段句柄。

例 定义一个大字段句柄 blob。

```
OCIBlobLocator *blob;
```

  2. **申请大字段句柄**



使用 ALLOCATE 申请大字段句柄。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] ALLOCATE :blob;
```

参数说明：

  * blob：大字段句柄。



  3. **绑定大字段句柄**



绑定大字段句柄就是通过 SQL 语句将数据库的大字段列绑定到大字段句柄变量，使本地的大字段变量与数据库的大字段列进行关联。可以通过 SELECT INTO 语句和 RETURNING 语句进行绑定。

例 将大字段列 PHOTO 绑定到大字段句柄 blob。

```
EXEC SQL INSERT INTO PRODUCTION.PRODUCT(NAME,AUTHOR,PUBLISHER,PUBLISHTIME,\

PRODUCTNO,PRODUCT_SUBCATEGORYID,SATETYSTOCKLEVEL,ORIGINALPRICE,NOWPRICE,\

DISCOUNT,DESCRIPTION,PHOTO,SELLSTARTTIME) VALUES('噼里啪啦丛书续集',\

'(日)佐佐木洋子','21世纪出版社','1901-01-01','9787539125992',35,'10','58',\

'42','6.1','11','书',empty_blob(),'2006-03-20')RETURNING PHOTO INTO :blob;

EXEC SQL SELECT PHOTO INTO :blob FROM PRODUCTION.PRODUCT where
NAME='噼里啪啦丛书续集';
```

  4. **打开大字段**



打开大字段，可选步骤。实际使用过程中可以省略不写。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] LOB OPEN :blob [READ ONLY | READ WRITE];
```

参数说明：

  * dbname：指明 lob 读操作使用的连接名；
  * blob：大字段句柄；
  * READ ONLY 表示只读；
  * READ WRITE 表示可读可写。



  5. **使用大字段句柄进行读写**



使用大字段句柄读数据到本地缓存区。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] LOB READ :amt FROM :src [AT :src_offset] INTO :buffer
[WITH LENGTH :buflen] ;
```

参数说明：

  * dbname：指明 lob 读操作使用的连接名；
  * amt：输入/输出参数，输入表示将要读取的字节数，输出表示已经读取的字节数
  * src：已绑定的大字段句柄
  * src_offset：大字段读取的偏移量
  * buffer：读取到的本地缓存区
  * buflen：缓存区的长度



  6. **使用大字段句柄写数据到大字段中**



语法如下：

```
EXEC SQL [AT [dbname | :dbname]] LOB WRITE [APPEND] [ONE ]:amt FROM :buffer
	[WITH LENGTH :buflen] INTO :dst [AT :dst_offset] ;
```

参数说明：

  * dbname：指明 lob 读操作使用的连接名；
  * amt：输入/输出参数，输入表示将要写入的字节数，输出表示已经写入的字节数
  * dst：已绑定的大字段句柄
  * dst_offset：大字段写入的偏移量
  * buffer：本地缓存区地址
  * buflen：缓存区的长度
  * APPEND：表示从大字段的末尾开始写入



  7. **关闭大字段句柄**



使用 CLOSE 关闭大字段句柄。

例 下面的语句关闭大字段句柄 blob。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] LOB CLOSE :blob;
```

参数说明：

  * dbname：指明 lob 读操作使用的连接名；
  * blob：大字段句柄。



  8. **释放大字段句柄**



使用 FREE 释放大字段句柄。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] FREE :blob;
```

参数说明：

  * dbname：指明 lob 读操作使用的连接名；
  * blob：大字段句柄。



  9. **获取大字段的长度**



将大字段句柄对应的大字段长度写入到变量中。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] LOB DESCRIBE :blob GET LENGTH INTO :itotal;
```

参数说明：

  * dbname：指明 lob 读操作使用的连接名；
  * blob：大字段句柄;
  * itotal：大字段变量。



下面给出两个使用大字段句柄处理 LOB 类型的实例。

例 1 读取大字段数据。

```
#include <stdio.h>
#include "DCI.h"
EXEC SQL INCLUDE SQLCA;
EXEC SQL BEGIN DECLARE SECTION;
char username[50];
char password[50];
char servername[50];
int c1=1;
unsigned int iBufLen = 4;
unsigned int offset = 1;  //1_based
unsigned int itotal;
unsigned char buf_blob[5];
unsigned char tmp[1024];
OCIBlobLocator *blob;
EXEC SQL END DECLARE SECTION;
/* Declare function to handle unrecoverable errors. */
void sql_error();
main()
{
/* Connect to DM. */
    strcpy(username, "SYSDBA");
    strcpy(password, "*****");
    strcpy(servername, "192.168.0.89:5289");
    EXEC SQL WHENEVER SQLERROR DO sql_error("DM error--");
    EXEC SQL CONNECT :username IDENTIFIED BY :password USING :servername;
    printf("\nConnected to dm as user: %s\n", username);
    printf("\n\n BEGIN BLOB select into \n");
    EXEC SQL update PRODUCTION.PRODUCT set PHOTO='abcdabcdabcd';
    EXEC SQL commit;
  
    EXEC SQL ALLOCATE :blob;
    EXEC SQL SELECT PHOTO into :blob FROM PRODUCTION.PRODUCT where NAME='红楼梦';
    EXEC SQL LOB DESCRIBE :blob GET LENGTH INTO :itotal;
    printf("itotal %d\n",itotal);
    while (1)
    {
        printf("offset %d\n",offset);
        EXEC SQL LOB READ :iBufLen FROM :blob AT :offset INTO :buf_blob;
        printf("%s\n",buf_blob);
        memcpy(tmp + offset - 1, buf_blob, iBufLen);
        offset = offset + iBufLen;
        if (offset > itotal)
        	break;
    }
    EXEC SQL FREE :blob;
    EXEC SQL COMMIT WORK RELEASE;
    exit(0);
}

void
sql_error(msg)
char *msg;
{
    char err_msg[512];
    size_t buf_len, msg_len;
    EXEC SQL WHENEVER SQLERROR CONTINUE;
    printf("\n%s\n", msg);

/* Call sqlglm() to get the complete text of the
* error message.
*/
    buf_len = sizeof (err_msg);
    sqlglm(err_msg, &buf_len, &msg_len);
    printf("%.*s\n", msg_len, err_msg);
  
    EXEC SQL ROLLBACK RELEASE;
    exit(1);
}
```

例 2 写入大字段。

```
EXEC SQL BEGIN DECLARE SECTION;
    OCIBlobLocator *blob;
    unsigned int offset = 1;
    unsigned int itotal = 2;
    unsigned char buf_blob[10]="AF";
EXEC SQL END DECLARE SECTION;
	EXEC SQL ALLOCATE :blob;
	EXEC SQL INSERT INTO PRODUCTION.PRODUCT(NAME,AUTHOR,PUBLISHER,PUBLISHTIME,\
PRODUCTNO,PRODUCT_SUBCATEGORYID,SATETYSTOCKLEVEL,ORIGINALPRICE,NOWPRICE,\
DISCOUNT,DESCRIPTION,PHOTO,SELLSTARTTIME) VALUES('噼里啪啦丛书续集',\
'(日)佐佐木洋子','21世纪出版社','1901-01-01','9787539125992',35,'10','58',\
'42','6.1','11','书',empty_blob(),'2006-03-20')RETURNING PHOTO INTO :blob;
    EXEC SQL LOB WRITE :itotal FROM :buf_blob INTO :blob AT :offset;
    EXEC SQL FREE :blob;
    EXEC SQL COMMIT RELEASE;
```

## 6.4 游标变量

除了 3.3.4 节中介绍的游标语句，DM 嵌入式 SQL 还支持游标变量，将游标作为一种对象变量使用。使用时基本的步骤为：定义、申请、打开、获取数据、关闭、释放游标。

  1. 定义游标变量，变量类型为 sql_cursor。



例

```
sql_cursor person_cv;

sql_cursor person_cursor;

sql_cursor *person;	//游标变量指针
```

  2. 申请游标变量。



例

```
EXEC SQL ALLOCATE :person_cv;
```

  3. 打开游标变量



  * 可以使用 DMSQL 程序块打开，如：



例

```
EXEC SQL EXECUTE

    BEGIN

    	OPEN :person_cv FOR SELECT * FROM person.person;

    END;

END-EXEC;
```

  * 也可以使用包打开游标，如：



例 1 在服务器端定义包体：

```
CREATE PACKAGE demo_cur_pkg AS

    TYPE personName IS RECORD (name VARCHAR2(51));

    TYPE cur_type IS REF CURSOR RETURN personName;

	PROCEDURE open_person_cur (

				curs IN OUT cur_type,

personid IN NUMBER);

END;

CREATE PACKAGE BODY demo_cur_pkg AS

	CREATE PROCEDURE open_person_cur (

				curs IN OUT cur_type,

personid IN NUMBER) IS

	BEGIN

    OPEN curs FOR

        SELECT name FROM person.person

            WHERE personid = personid

            ORDER BY name ASC;

    END;

END;
```

例 2 在客户端使用此包：

```
EXEC SQL EXECUTE

    begin

    	demo_cur_pkg.open_person_cur(:person_cursor, :personid);

    end;

END-EXEC;
```

  4. 获取数据。



例

```
EXEC SQL FETCH :person_cv INTO :output_person_rec;
```

  5. 释放游标变量。



例

```
EXEC SQL FREE :person_cv;
```

使用游标进行查询时，需要注意：

  * 当返回值的数据类型与宿主变量的数据类型不一致时，DM 将返回值转换成宿主变量的类型。这种转换只局限于数值转换。不论数据类型如何，如果返回给宿主变量的值是 NULL，那么相应指示符变量被置为-1。如果没有与之相应的指示符变量，那么 SQLCODE 被设置为-5000。数值型数据转换，包括 short、int、double、float 四种数值型数据的转换，若发生溢出错误，将给出警告信息；
  * 如果当前游标已经指向查询的最后一条记录，使用 FETCH 语句将会导致返回错误代码(SQLCODE=100)。



例 下面是一个使用游标变量的例子

```
##include <stdio.h>

EXEC SQL INCLUDE SQLCA;

EXEC SQL BEGIN DECLARE SECTION;

char username[50];

char password[50];

char servername[50];

EXEC SQL END DECLARE SECTION;

typedef struct _EMP_CV

{

    int personid;

    char sex[2] ;

    char name[51];

    char email[51];

    char phone[26];} V_EMP_CV;

EXEC SQL BEGIN DECLARE SECTION;

    SQL_CURSOR emp_cv;

    V_EMP_CV output_emp_rec;

EXEC SQL END DECLARE SECTION;

/* Declare function to handle unrecoverable errors. */

void sql_error();

main()

{

/* Connect to DM. */

    strcpy(username, "SYSDBA");

    strcpy(password, "*****");

    strcpy(servername, "192.168.0.89:5289");

    EXEC SQL WHENEVER SQLERROR DO sql_error("DM error--");

    EXEC SQL CONNECT :username IDENTIFIED BY :password USING :servername;

    printf("\nConnected to dm as user: %s\n", username);

EXEC SQL ALLOCATE :emp_cv;

EXEC SQL EXECUTE

    BEGIN

    	OPEN :emp_cv FOR SELECT * FROM person.person;

    END;

END-EXEC;

EXEC SQL WHENEVER NOT FOUND DO BREAK;

for (;;)

{

    EXEC SQL FETCH :emp_cv INTO :output_emp_rec;

    printf("personid=%d; sex=%s; name=%s; email=%s;
phone=%s;\n",output_emp_rec.personid,output_emp_rec.sex,\

    output_emp_rec.name,output_emp_rec.email,output_emp_rec.phone);

}

EXEC SQL CLOSE :emp_cv;

EXEC SQL FREE :emp_cv;

    EXEC SQL COMMIT WORK RELEASE;

    exit(0);

}

void

sql_error(msg)

char *msg;

{

    char err_msg[512];

    size_t buf_len, msg_len;

    EXEC SQL WHENEVER SQLERROR CONTINUE;

    printf("\n%s\n", msg);

/* Call sqlglm() to get the complete text of the

* error message.

*/

    buf_len = sizeof (err_msg);

    sqlglm(err_msg, &buf_len, &msg_len);

    printf("%.*s\n", msg_len, err_msg);

    EXEC SQL ROLLBACK RELEASE;

    exit(1);

}
```

## 6.5 批量执行

DM 的 PRO*C 支持批量操作，可以批量查询、打开、获取和执行，批量操作描述信息的相关信息需要增加语法 FOR:size，其中 size 用于指定批量的行数，可以是常量，也可以是变量。

### 6.5.1SELECT 批量操作

  1. **使用数组宿主变量**



当知道查询会返回的行数时，可以使用简单的 SELECT INTO 语句，你可以使用大于等于行数的数组宿主变量。

例 下例中从 PERSON.PERSON 表中查询 3 条记录放入数组宿主变量，而数组宿主变量可以存放 4 个元素。

```
int cc1[4];

VARCHAR cc2[4][200];

VARCHAR cc3[4][200];

EXEC SQL SELECT top 3 personid,name,phone into :cc1,:cc2,:cc3 FROM
person.person;
```

  2. **使用数组宿主变量 + 游标 FETCH**



如果不知道查询结果的数据行数时，可以使用数组宿主变量 + 游标 FETCH，每次 FETCH 的行数等于宿主数组的大小。

例 下例中每次 fetch 10 行。最后一次 fetch 是余下的数据可能小于 10 行。

```
int personid[10];

char name[10][51];

EXEC SQL DECLARE person_cursor CURSOR FOR

	SELECT personid, name FROM person.person;

EXEC SQL OPEN person_cursor;

EXEC SQL WHENEVER NOT FOUND do break;

for (;;)

{

    EXEC SQL FETCH person_cursor

    INTO :personid, :name;

	/* process batch of rows */

	...

}

...
```

  3. **使用 sqlca.sqlerrd[2]**



对于 INSERT、UPDATE、DELETE 和 SELECT INTO 语句，sqlca.sqlerrd[2]记录了操作处理的数据行数。对于游标 FETCH 操作 sqlca.sqlerrd[2]记录的是当前总共 FETCH 的行数。你可以利用 sqlca.sqlerrd[2]的值进行批量操作的循环控制。

例 下面代码片段中 sqlca.sqlerrd[2]用来记录已经 FETCH 的行数。

```
int personid[50];

char name[50][51];

int rows_to_fetch, rows_before, rows_this_time;

EXEC SQL DECLARE person_cursor CURSOR FOR

	SELECT personid, name

    FROM person.person

    WHERE personid = 2;

EXEC SQL OPEN person_cursor;

EXEC SQL WHENEVER NOT FOUND CONTINUE;

/* initialize loop variables */

rows_to_fetch = 10; /* number of rows in each "batch" */

rows_before = 0; /* previous value of sqlerrd[2] */

rows_this_time = 10;

while (rows_this_time == rows_to_fetch)

{

    EXEC SQL FOR :rows_to_fetch

    FETCH person_cursor

    	INTO :personid, :name;

    rows_this_time = sqlca.sqlerrd[2] - rows_before;

    rows_before = sqlca.sqlerrd[2];

}

...
```

  4. **批量获取描述信息**



通过描述符可以获取动态 SQL 语句的输出列类型与长度，关于描述符的具体说明见 6.6 节。DM 支持批量获取描述信息。

例

```
char sqlstr[500];
int array_size=5;
int col_cnt;
int i;
char c_name[50];
int c1_data[5];
char c2_data[5][21];
char c3_data[5][50];
char c4_data[5][50];
char c5_data[5][50];
int c1_type,c2_type,c3_type,c4_type,c5_type;
int c1_length,c2_length,c3_length,c4_length,c5_length;

EXEC SQL drop table testdes;
EXEC SQL create table testdes(c1 int,c2 varchar(20),c3 varbinary(20),c4 clob,c5 blob);
EXEC SQL insert into testdes values(1, 'dm', 0x12ab, 'dameng', 0x34ef);
EXEC SQL insert into testdes values(2, 'dm', 0x12ab, 'dameng', 0x34ef);
EXEC SQL insert into testdes values(3, 'dm', 0x12ab, 'dameng', 0x34ef);
EXEC SQL insert into testdes values(4, 'dm', 0x12ab, 'dameng', 0x34ef);
EXEC SQL insert into testdes values(5, 'dm', 0x12ab, 'dameng', 0x34ef);

strcpy(sqlstr, "select c1,c2,c3,c4,c5 from testdes");

EXEC SQL PREPARE stmt FROM :sqlstr;
EXEC SQL DECLARE cur_stmt CURSOR FOR stmt;
EXEC SQL OPEN cur_stmt;

// 绑定输出变量
EXEC SQL FOR :array_size ALLOCATE DESCRIPTOR 'out';
EXEC SQL DESCRIBE OUTPUT stmt USING DESCRIPTOR 'out';

c1_type=4;
c1_length=4;
EXEC SQL SET DESCRIPTOR 'out' VALUE 1 TYPE=:c1_type, LENGTH=:c1_length;
c2_type=1;
c2_length=10;
EXEC SQL SET DESCRIPTOR 'out' VALUE 2 TYPE=:c2_type, LENGTH=:c2_length;
c3_type=1;
c3_length=4;
EXEC SQL SET DESCRIPTOR 'out' VALUE 3 TYPE=:c3_type, LENGTH=:c3_length;
c4_type=1;
c4_length=10;
EXEC SQL SET DESCRIPTOR 'out' VALUE 4 TYPE=:c4_type, LENGTH=:c4_length;
c5_type=1;
c5_length=4;
EXEC SQL SET DESCRIPTOR 'out' VALUE 5 TYPE=:c5_type, LENGTH=:c5_length;

EXEC SQL GET DESCRIPTOR 'out' :col_cnt=COUNT;
printf("col_cnt:%d\n",col_cnt);

for(i=1;i<=col_cnt;i++)
{
    memset(c_name, 0, 10);
    EXEC SQL GET DESCRIPTOR 'out' VALUE :i :c_name=NAME;
    printf("%s ",c_name);
}
printf("\n------------------------\n");

EXEC SQL FOR :array_size FETCH cur_stmt INTO DESCRIPTOR 'out';
EXEC SQL FOR :array_size GET DESCRIPTOR 'out' VALUE 1 :c1_data=DATA;
EXEC SQL FOR :array_size GET DESCRIPTOR 'out' VALUE 2 :c2_data=DATA;
EXEC SQL FOR :array_size GET DESCRIPTOR 'out' VALUE 3 :c3_data=DATA;
EXEC SQL FOR :array_size GET DESCRIPTOR 'out' VALUE 4 :c4_data=DATA;
EXEC SQL FOR :array_size GET DESCRIPTOR 'out' VALUE 5 :c5_data=DATA;

for(i=0;i<array_size;i++)
	printf("%d %s %s %s %s\n",c1_data[i],c2_data[i],c3_data[i],c4_data[i],c5_data[i]);

EXEC SQL CLOSE cur_stmt;
EXEC SQL DEALLOCATE DESCRIPTOR 'out';
EXEC SQL DEALLOCATE DESCRIPTOR 'in';

EXEC SQL COMMIT WORK;
return 0;
```

### 6.5.2INSERT 批量操作

同样地，可以使用宿主数组作为 INSERT 语句的输入变量，只需要在执行插入操作前给数组赋值即可。如果数组数据不足也可以使用 FOR 语句来指定批量插入的行数，未指定 FOR 则默认插入行数等于数组大小。

例 下面代码片段使用数组进行批量插入。

```
char name[50][51];
char phone[50][21];
char email[50][51];
//为数组宿主变量赋值
...
EXEC SQL INSERT INTO PERSON.PERSON (NAME, PHONE, EMAIL)
VALUES (:name, :phone, :email);
```

### 6.5.3UPDATE 批量操作

也可以使用数组作为 UPDATE 语句的宿主变量，与 INSERT 批量操作类似。

例 下面代码片段使用数组进行批量更新。

```
int person_number[50];
char phone[50][51];
//为数组宿主变量赋值
...
EXEC SQL UPDATE PERSON.PERSON SET PHONE = :phone
WHERE PERSONID = :person_number;
```

在上面例子中 PERSONID 是表 PERSON.PERSON 的主键，每个 PERSONID 对应一行数据。批量更新也可以是条件数组中每条数据对应多行。

例

```
char sex [2][51];
char email[2][51];
//为数组宿主变量赋值
...
EXEC SQL UPDATE person.person SET email = :email
WHERE sex = :sex;
```

### 6.5.4DELETE 批量操作

同样可以使用数组作为 DELETE 语句的宿主变量，与 INSERT 批量操作类似。

例下面代码片段使用数组进行批量删除。

```
int person_number[50];
char phone[50];
//为数组宿主变量赋值
...
EXEC SQL DELETE FROM PERSON.PERSON WHERE PERSONID = :person_number;
```

在上面例子中 PERSONID 是表 PERSON.PERSON 的主键，每个 PERSONID 对应一行数据。批量删除也可以是条件数组中每条数据对应多行。

例

```
char sex [2][51];

...

EXEC SQL DELETE FROM person.person WHERE sex = :sex;
```

### 6.5.5FOR 语法

前面介绍的 SELECT、UPDATE、INSERT 和 DELETE 批量操作都可以使用 FOR 语法指定具体批量操作的行数。

例 下面的代码片段使用 FOR 语法指定 INSERT 批量操作处理的行数。

```
char name[100][51];
char email[100][51];
int rows_to_insert;
//为数组宿主变量赋值
...
rows_to_insert = 25; //设置FOR子句变量
EXEC SQL FOR :rows_to_insert //将插入25条记录
INSERT INTO PERSON.PERSON (NAME, EMAIL)
VALUES (:name, :email);
```

### 6.5.6 使用结构数组

当批量操作同时涉及到多个列时，可以将这些列集成到一个结构体中，然后定义结构数组，在嵌入式 SQL 中使用结构数组进行查询或插入更新，这样也能实现批量的效果。

在嵌入式 SQL 中使用结构数组有以下限制:

  * 不能在 PL\SQL 语句块中使用结构数组
  * 不能在 where 条件中使用结构数组
  * 不能在 update 语句中使用结构数组



例 下例中批量查询同时查询 PERSON.PERSON 的三个列，因此定义了一个对应这三个列的结构数组 person_rec，同时还使用了对应这个结构数组的指示变量数组。

```
##include <stdio.h>

EXEC SQL INCLUDE SQLCA;

EXEC SQL BEGIN DECLARE SECTION;

struct

{

    int personid;

    char name[51];

    char email[51];

} person_rec[5];

struct

{

    short ind_personid;

    short ind_name;

    short ind_email;

} ind_person_rec[5];

char username[50];

char password[50];

char servername[50];

int num_ret;

EXEC SQL END DECLARE SECTION;

/* Declare function to handle unrecoverable errors. */

void sql_error();

void print_rows();

main()

{

/* Connect to DM. */

    strcpy(username, "SYSDBA");

    strcpy(password, "*****");

    strcpy(servername, "192.168.0.89:5289");

    EXEC SQL WHENEVER SQLERROR DO sql_error("DM error--");

    EXEC SQL CONNECT :username IDENTIFIED BY :password USING :servername;

    printf("\nConnected to dm as user: %s\n", username);

/* Declare a cursor for the FETCH. */

    EXEC SQL DECLARE c1 CURSOR FOR

    	SELECT personid, name, email FROM person.person where personid\<15;

    EXEC SQL OPEN c1;

    num_ret = 0;

/* Array fetch loop - ends when NOT FOUND becomes true. */

    EXEC SQL WHENEVER NOT FOUND DO break;

    for (;;)

    {

        EXEC SQL FETCH c1 INTO :person_rec :ind_person_rec;

        print_rows(sqlca.sqlerrd[2] - num_ret);

        num_ret = sqlca.sqlerrd[2]; /* Reset the number. */

    }

/* Print remaining rows from last fetch, if any. */

    if ((sqlca.sqlerrd[2] - num_ret) > 0)

        print_rows(sqlca.sqlerrd[2] - num_ret);

        EXEC SQL CLOSE c1;

        printf("\nAu revoir.\n\n\n");

        EXEC SQL COMMIT WORK RELEASE;

        exit(0);

    }

void

print_rows(n)

int n;

{

    int i;

    printf("\npersonid name email");

    printf("\n------ -------- ------\n");

    for (i = 0; i < n; i++)

        printf("%d;%s; %s\n", person_rec[i].personid,

        	person_rec[i].name, person_rec[i].email);

}

void

sql_error(msg)

char *msg;

{

    char err_msg[512];

    size_t buf_len, msg_len;

	printf("\n%s\n", msg);

/* Call sqlglm() to get the complete text of the

* error message.

*/

    buf_len = sizeof (err_msg);

    sqlglm(err_msg, &buf_len, &msg_len);

    printf("%.*s\n", msg_len, err_msg);

    EXEC SQL ROLLBACK RELEASE;

    exit(1);

}
```

## 6.6 动态 SQL 语句

动态 SQL 技术是一种先进的程序设计技术。

前面介绍的嵌入式 SQL 语句为编程提供了一定的灵活性，使用户可以在程序运行过程中根据实际需要输入 WHERE 子句或 HAVING 子句中的某些变量的值。这些 SQL 语句的共同特点是，语句中主变量的个数与数据类型在预编译时都是确定的，只是主变量的值是程序运行过程中动态输入的，我们称这类嵌入式 SQL 语句为静态 SQL 语句。

静态 SQL 语句提供的编程灵活性在许多情况下仍显得不足，有时候需要编写更为通用的程序。例如查询人员信息表，经理想查询所有人员姓名、性别，员工想查询联系电话，也就是说查询条件是不确定的，要查询的属性列也是不确定的，这就无法用一条静态 SQL 语句实现了。因为在这些情况下，SQL 语句不能完整地写出来，而且这类语句在每次执行时都还有可能变化，只有在程序执行时才能构造完整。像这种在程序执行过程中临时生成的 SQL 语句叫动态 SQL 语句。

实际上，如果在预编译时下列信息不能确定，就必须使用动态 SQL 技术：

  * SQL 语句正文；
  * 主变量个数；
  * 主变量的数据类型；
  * SQL 语句中引用的数据库对象（例如，列、索引、基表、视图等）。



动态 SQL 方法允许在程序运行过程中临时“组装”SQL 语句，主要有三种形式：

  1. 语句可变；
  2. 条件可变；
  3. 数据对象、查询条件均可变。



这几种动态形式几乎覆盖所有的可变要求。为了实现上述三种可变形式，SQL 提供了相应的语句，例如，EXECUTE IMMEDIATE、PREPARE、EXECUTE、DESCRIBE 等。

### 6.6.1DM 动态 SQL 语句

### 6.6.1.1 动态 SQL 语句的表示方法

一般来说，应该使用一个字符串变量来表示一个动态 SQL 语句的文本，但该文本部分包括 EXEC SQL 子句。

> **注意**
>
> 下列语句不能作动态SQL语句：CLOSE、 DECLARE、DESCRIBE、
> EXECUTE、FETCH、INCLUDE、OPEN、WHENEVER、PREPARE。



在大多数情况下，动态 SQL 语句的文本中可能包含虚拟宿主变量，这些变量只为实宿主变量保留位置。

例如，下列例子中的问号就是只为实宿主变量保留位置：

```
DELETE FROM EMP WHERE NO=? AND NAME=?;
```

### 6.6.1.2 生成动态 SQL 语句的方法

在 DM 嵌入式 SQL 中，生成动态 SQL 语句的方法有四种，下面分别介绍。

  1. **方法一**



用立即执行语句执行动态 SQL 语句。

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] EXECUTE IMMEDIATE <SQL动态语句文本>;
```

功能：

动态地准备和执行一条语句。该语句首先分析动态语句文本，检查是否有错误，如果有错误则不执行它，并在 SQLCODE 中返回错误码；如果没发现错误则执行它。

使用说明：

用该方法处理的 SQL 语句一定不是 SELECT 语句，而且不包含任何虚拟的输入宿主变量。

例 1 下列 SQL 动态语句文本串是合法的：

```
CREATE TABLE person.person
 (
"personid" integer identity(1,1) not null,  //人员编号
"sex" char(1) not null,  //人员性别
"name" varchar(50) not null,  //人员姓名
"email" varchar(50),  //邮箱地址
"phone" varchar(25),  //联系电话
cluster primary key("personid")
)
INSERT INTO person.person VALUES('F' , '李丽', 'lily@sina.com', '02788548562');
```

如果有语句 EXEC SQL EXECUTE IMMEDIATE :string，则该语句将分析和执行宿主变量 string 中的 SQL 语句。

例 2 有语句 EXEC SQL EXECUTE IMMEDIATE INSERT INTO person.person VALUES('F' , '李丽', 'lily@sina. com', '02788548562')。该语句直接分析和执行 INSERT INTO person.person VALUES('F' , '李丽', 'lily@sina. com', '02788548562')。

对于仅执行一次的动态语句，用立即执行语句最合适。而对于重复多次执行的动态 SQL 语句，用立即执行语句则会降低效率，应选用方法二。

  2. **方法二**



方法二首先用准备语句，准备将要执行的动态 SQL 语句，然后用执行语句 EXECUTE 执行已准备好的动态 SQL 语句。在该方法中，动态 SQL 语句的处理分三步：

  1. 构造一个动态 SQL 语句；
  2. 用 PREPARE 语句来分析和命名该动态 SQL 语句；
  3. 用 EXECUTE 语句来执行它。



下面分别介绍 PREPARE、EXECUTE 语句。

  * 准备语句



语法如下：

```
EXEC SQL [AT [dbname | :dbname]] PREPARE <SQL 语句名> FROM

<SQL动态语句文本>;
```

  * dbname：指明语句声明使用的连接名，动态 SQL 操作完成前不支持进行连接切换；
  * \<SQL 语句名>标识被分析的动态 SQL 语句，它是供预编译程序使用的标识符，而不是宿主变量；
  * \<SQL 动态语句文本 > 包含动态 SQL 语句，可为变量形式或直接写 SQL 字符串形式；其中 SQL 字符串形式支持两种格式：（1）使用单引号括起动态 SQL 语句，例如：EXEC SQL PREPARE stmt FROM '\< 动态 SQL 语句 >';，（2）不使用单括号，直接放于 FROM 关键字后，例如：EXEC SQL PREPARE stmt FROM \< 动态 SQL 语句 >;。



功能：

准备一个语句执行。

使用说明：

用该方法处理的 SQL 语句，不能是 SELECT 语句。该语句可能包含虚拟输入宿主变量（用问号表示），而且变量的类型是已知的。

例 1 语句包含虚拟输入宿主变量

```
INSERT INTO person.person VALUES(?, ?, ?, ?);

DELETE FROM person.person WHERE personid =?;
```

例 2 用 stmt 标识被分析的 SQL 语句，”?”表示一个虚拟输入宿主变量。

```
strcpy(sql_stmt, "DELETE FROM person.person WHERE personid =?");

EXEC SQL PREPARE stmt FROM :sql_stmt;
```

  * 执行语句



语法如下：

```
EXEC SQL EXECUTE <SQL 语句名> [<结果使用子句>];
```

功能：

执行一个由 PREPARE 准备好的动态 SQL 语句。

使用说明：

\<SQL 语句名>标识被分析的动态 SQL 语句。\<结果使用子句>指出一个实宿主变量表，用于替换虚宿主变量，且\<结果使用子句>中的实宿主变量要与被分析的动态 SQL 语句中的虚宿主变量在类型、次序上相对应，个数相匹配。

例 执行上例准备的 SQL 语句。

```
EXEC SQL EXECUTE stmt USING :personid;
```

其中，personid 是一个实输入宿主变量，它替换前面所述的动态 SQL 语句中的“?”。

  3. **方法三**



用方法三处理的 SQL 语句一定是 SELECT 语句，而且它包含的选择表项或虚拟输入宿主变量的个数或类型在预编译时都已知。

例

```
SELECT NAME, PHONE FROM PERSON.PERSON WHERE PERSONID=?
```

在方法三中，动态 SQL 语句的处理过程如下：

  1. 构造一个动态 SQL 语句；
  2. 用 PREPARE 语句来分析和命名该动态 SQL 语句，其描述同法二；
  3. 用游标语句来执行它。



执行动态 SQL 查询的游标语句包括动态声明游标、动态打开游标、动态拨动游标、动态关闭游标，其中除了动态打开游标，其余的使用方法与 3.3.4 节中介绍的相同。

下面介绍一下动态打开游标语句，语法如下：

```
EXEC SQL OPEN <游标名> [USING : host_variable [,.]];
```

功能：

打开动态游标。

使用说明：

\<游标名>是要打开的游标的名字，host_variable 是实际宿主变量，用于替换动态 SQL 语句中的虚拟宿主变量。

### 6.6.1.3 动态 SQL 实例

下面的例子示范了三种生成动态 SQL 的方法。在运行本程序之前，应该先确定登录的用户名下是否已经存在表 PERSON，如果这个表已经存在，那么程序将在执行一开始的建表语句时发生异常而退出登录。

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
EXEC SQL BEGIN DECLARE SECTION;
long SQLCODE;
char SqlStr[200];
char username[19];
char password[19];
char servername[20];
varchar person_no[10],person_name[50],person_phone[25];
EXEC SQL END DECLARE SECTION;
void main()
{
int i;
char person_id[3][6]={"1","2","3"};
char person_name[3][11]={"李丽","王刚","李勇",};
char person_phone[3][12]={"02788548562","13055173012","15955173024"};
sprintf(servername,"localhost");
sprintf(username,"SYSDBA");
sprintf(password,"*****");
EXEC SQL LOGIN :username PASSWORD :password SERVER :servername;
EXEC SQL WHENEVER SQLERROR GOTO sql_err;
//用方法一建表 
strcpy(SqlStr,"CREATE TABLE person (\"personid\" integer identity(1,1) not
null,\"name\" varchar(50) not null,\"phone\" varchar(25))");
EXEC SQL EXECUTE IMMEDIATE :SqlStr;
//用方法二插入一组值 
strcpy(SqlStr,"INSERT INTO person (\"name\", \"phone\") VALUES(?,?)");
EXEC SQL PREPARE INS1 FROM :SqlStr;
for(i=0;i<3;i++)
{
strcpy(person_name,person_name[i]);
strcpy(person_phone,person_phone[i]);
EXEC SQL EXECUTE INS1 USING :person_name,:person_phone;
EXEC SQL COMMIT WORK;
}
//用方法三查询并显示结果 
strcpy(SqlStr,"SELECT \"name\", \"phone\" FROM person WHERE \"personid\"
=?");
strcpy(person_no,"1");
EXEC SQL PREPARE S1 FROM :SqlStr;
EXEC SQL DECLARE C1 CURSOR FOR S1;
EXEC SQL OPEN C1 USING :person_no;
printf("The resultis:%s\n",person_no);
EXEC SQL FETCH C1 INTO :person_name,:person_phone;
printf("NO=%s,NAME=%s\n\n",person_phone, person_name);
EXEC SQL CLOSE C1;
EXEC SQL DROP TABLE PERSON CASCADE;
sql_err:
printf("Error Code:%d\n",SQLCODE);
EXEC SQL ROLLBACK;
EXEC SQL LOGOUT;
exit(1);
}
```

### 6.6.2ANSI 动态 SQL 语句

DM 支持 ANSI 标准的动态 SQL 语法，以下面的 SQL 为例：

```
SELECT PERSONID ,NAME FROM PERSON.PERSON WHERE EMAIL=:email_data;
```

使用 ANSI 动态 SQL 的步骤是：

  1. 声明变量，包含一个字符串变量存放待执行的 SQL 语句；
  2. 为输入输出变量分配描述符；
  3. 准备（PREPARE）SQL 语句；
  4. 为输入描述符描述输入；
  5. 设置输入描述符（上述 SQL 中有一个输入宿主变量 email_data）；
  6. 声明打开一个动态游标；
  7. 设置输出描述符（上述 SQL 中有两个输出变量：personid 和 name）
  8. 反复 FETCH 数据，使用 GET DESCRIPTOR 检索 personid 和 name 对应输出描述符的 data 域；
  9. 关闭游标，释放描述符。



ANSI 动态 SQL 语句中描述符起着重要的作用，下面主要介绍描述符相关操作，其他的声明 SQL 语句变量、准备语句等在前面都已介绍过了。

  1. **分配描述符**



在动态 SQL 语句中使用描述符前，必须先分配描述符。

语法如下：

```
EXEC SQL ALLOCATE DESCRIPTOR [GLOBAL | LOCAL] <:desc_nam | string_literal>

[WITH MAX <:occurrences | numeric_literal>];
```

参数说明：

  * GLOBAL 描述符能够在其他模块中使用，LOCAL 描述符只能在当前模块中使用；
  * 描述符的名字可以是一个宿主变量，也可以是字符串；
  * occurrences 是描述符最多可以绑定的变量数，可以使用宿主变量或者直接在 SQL 中使用数字表示。



  2. **获取语句描述信息**



描述符分为输入变量的描述符和输出变量的描述符。DESCRIBE 输入描述符可描述语句的绑定变量，DESCRIBE 输出描述符能描述输出列的类型跟长度。

语法如下：

```
EXEC SQL DESCRIBE [INPUT | OUTPUT] <sql_statement> USING [SQL] DESCRIPTOR [GLOBAL | LOCAL] <:desc_nam | string_literal>;
```

  3. **设置描述符**



使用 SET DESCRIPTOR 语句设置输入描述符属性。

语法如下：

```
EXEC SQL [FOR <array_size>] SET DESCRIPTOR <:desc_nam | string_literal> < <COUNT = :hv0> | <VALUE item_number> >

< [REF] item_name1 = :hv1 > {, < [REF] item_nameN = :hvN > } ;
```

参数说明：

  * FOR \<array_size>:数组大小，oracle 兼容模式下支持设置 DATA，INDICATOR 为数组宿主变量。
  * :desc_nam：前面 ALLOCATE DESCRIPTOR 分配描述符名称，以宿主变量形式书写。例如，char c1="out_lobInsert";……SET DESCRIPTOR :c1……。
  * string_literal：前面 ALLOCATE DESCRIPTOR 分配描述符名称，以字符串形式书写。例如，……SET DESCRIPTOR 'out_lobInsert'……。
  * COUNT：输入或输出绑定变量个数，可以是一个宿主变量，也可以直接使用数字。
  * VALUE item_number：动态 SQL 绑定变量位置。
  * hv1 .. hvN： 设置的用来绑定宿主变量。
  * item_nameI：描述符属性名，详细参见表 6.1。
  * REF :仅语法上支持，不起任何作用。且只能在设置 DATA, INDICATOR 属性时使用。



SET DESCRIPTOR 可以设置的描述符属性如下表所示。

表6.1 SET DESCRIPTOR可设置的描述符属性名

|**属性名**|**介绍**|
|---|---|
|TYPE|ANSI 列类型码。详细参见表 6.2|
|LENGTH|列数据的最大长度|
|INDICATOR|对应列的指示符|
|DATA|对应要设置存放列数据的宿主变量|


其中，ANSI 的列类型码见下表。

表6.2 ANSI数据类型编码

|**数据类型**|**列类型码**|
|----|----|
|CHARACTER|1|
|CHARACTER VARYING|12|
|DATE|9|
|DECIMAL|3|
|DOUBLE PRECISION|8|
|FLOAT|6|
|INTEGER|4|
|NUMERIC|2|
|REAL|7|
|SMALLINT|5|
|CLOB (oracle 模式下兼容)|112|
|BLOB (oracle 模式下兼容)|113|


  4. **提取描述符**



在执行 FETCH 操作后，可以通过 GET DESCRIPTOR 获取返回的查询结果。

语法如下：

```
EXEC SQL GET DESCRIPTOR [GLOBAL | LOCAL] <:desc_nam | string_literal>

VALUE<item_number> :hv1 = DATA, :hv2 = INDICATOR, :hv3 = RETURNED_LENGTH ;
```

参数说明：

  * desc_nam 可以是字符串也可以是宿主变量；
  * item_number 可以是数字也可以是宿主变量；
  * hv1、hv2、hv3 必须是宿主变量；
  * 可以只获取 DATA、INDICATOR、RETURNED_LENGTH 其中一个或多个。



GET DESCRIPTOR 可以查询的描述符相关属性如下表所示。

表6.3 GET DESCRIPTOR可以查询的描述符属性

|**属性**|**介绍**|
|--|--|
|TYPE|类型码|
|LENGTH|列长度|
|OCTET_LENGTH|列按字符串输出最大字节数|
|RETURNED_LENGTH|实际数据长度|
|RETURNED_OCTET_LENGTH|列按字符串输出实际字节数|
|PRECISION|精度|
|SCALE|标度|
|NULLABLE|列是否可以为空|
|INDICATOR|指示数据是否为 NULL|
|DATA|指定数据存放宿主变量|
|NAME|列名|
|CHARACTER_SET_NAME|列的字符集|


例 下面的代码片段示例如何使用 ANSI 动态 SQL，分配了输入描述符‘in’与输出描述符‘out’，准备并执行一个查询语句，用 SET DESCRIPTOR 设置描述符，用 GET DESCRIPTOR 获取描述符的相关属性值。

```
#include <stdio.h>
EXEC SQL INCLUDE SQLCA;
EXEC SQL BEGIN DECLARE SECTION;
char* dyn_statement = "SELECT name, personid FROM person.person WHERE personid= :personid" ;
int personid_type_in = 3, personid_len_in = 4, personid_data_in = 10 ;
int name_type = 97, name_len = 50 ;
char name_data[51];
int personid_type = 3, personid_len = 4 ;
int personid_data ;
long SQLCODE = 0 ;
char username[50];
char password[50];
char servername[50];
EXEC SQL END DECLARE SECTION;

void sql_error();

main()
{
/* Connect to DM. */
    strcpy(username, "SYSDBA");
    strcpy(password, "*****");
    strcpy(servername, "192.168.0.89:5289");

    EXEC SQL WHENEVER SQLERROR DO sql_error("DM error--");

    EXEC SQL CONNECT :username IDENTIFIED BY :password USING :servername;
    printf("\nConnected to dm as user: %s\n", username);

EXEC SQL ALLOCATE DESCRIPTOR 'in' ;
EXEC SQL ALLOCATE DESCRIPTOR 'out' ;
EXEC SQL PREPARE s FROM :dyn_statement ;
EXEC SQL DESCRIBE INPUT s USING DESCRIPTOR 'in' ;
EXEC SQL SET DESCRIPTOR 'in' VALUE 1 TYPE = :personid_type_in,
	LENGTH = :personid_len_in, DATA = :personid_data_in ;
EXEC SQL DECLARE c CURSOR FOR s ;
EXEC SQL OPEN c USING DESCRIPTOR 'in';
EXEC SQL DESCRIBE OUTPUT s USING DESCRIPTOR 'out' ;
EXEC SQL SET DESCRIPTOR 'out' VALUE 1 TYPE = :name_type,
	LENGTH = :name_len, DATA = :name_data ;
EXEC SQL SET DESCRIPTOR 'out' VALUE 2 TYPE = :personid_type,
	LENGTH = :personid_len, DATA = :personid_data ;

EXEC SQL WHENEVER NOT FOUND DO BREAK ;
while (SQLCODE == 0)
{
    EXEC SQL FETCH c INTO DESCRIPTOR 'out' ;
    EXEC SQL GET DESCRIPTOR 'out' VALUE 1 :name_data = DATA ;
    EXEC SQL GET DESCRIPTOR 'out' VALUE 2 :personid_data = DATA ;
    printf("\nname = %s personid = %d", name_data, personid_data) ;
}
EXEC SQL CLOSE c ;
EXEC SQL DEALLOCATE DESCRIPTOR 'in' ;
EXEC SQL DEALLOCATE DESCRIPTOR 'out' ;
  
    EXEC SQL COMMIT WORK RELEASE;
    exit(0);
}

void
sql_error(msg)
char *msg;
{
    char err_msg[512];
    size_t buf_len, msg_len;
  
    EXEC SQL WHENEVER SQLERROR CONTINUE;
  
	printf("\n%s\n", msg);
/* Call sqlglm() to get the complete text of the
* error message.
*/
    buf_len = sizeof (err_msg);
    sqlglm(err_msg, &buf_len, &msg_len);
    printf("%.*s\n", msg_len, err_msg);
  
    EXEC SQL ROLLBACK RELEASE;
    exit(1);
}
```

## 6.7 多线程支持

PRO*C 支持使用嵌入式 SQL 语句与指令开发多线程应用。

### 6.7.1 多线程应用的运行上下文环境

上下文环境资源包括一个或多个连接到一个或多个服务，一个或多个游标对应一个连接，以及它们的状态信息。不仅仅可以每个线程与连接使用多个上下文，也可以将上下文从一个线程传给另一个线程使用。

例如，一个多线程应用中定义一个线程 T1，执行了一个查询，返回前 10 行数据给应用，T1 结束。又定义一个线程 T2，将 T1 中使用的上下文 CONTEXT 传给 T2,在 T2 中使用同样的游标处理方式就能获取接下来的 10 行数据了。

### 6.7.2 上下文的两种使用方式

CONTEXT 在多线程应用中有两种使用方式：

  1. 多个线程共享一个 CONTEXT；
  2. 多个线程各自单独使用一个 CONTEXT。



> **注意**
>
>
> 不论使用哪种方式，在同一时刻多个线程不能使用同一个CONTEXT。多个线程使用同一个CONTEXT，必须互斥访问，多个线程各自使用一个CONTEXT时，可以各自自由使用。



### 6.7.3 多线程嵌入式 SQL 与指令

多线程应用中需要使用下面这些 SQL 与指令：

  * EXEC SQL ENABLE THREADS;
  * EXEC SQL CONTEXT ALLOCATE :context_var;
  * EXEC SQL CONTEXT USE \<:context_var | DEFAULT>;
  * EXEC SQL CONTEXT FREE :context_var;



这些 EXEC SQL 语句中的 context_var 是上下文句柄，必须声明为 sql_context。

语法如下：

```
sql_context <context_variable>;
```

USE DEFAULT 表示使用默认的全局的上下文，直到下一个 CONTEXT USE 使用后才会切换上下文。

  1. **EXEC SQL ENABLE THREADS**



该语句用来初始化多线程环境，必须是应用的第一条可执行的嵌入 SQL 语句。

  2. **EXEC SQL CONTEXT ALLOCATE**



该语句用来初始化 CONTEXT 变量内存

  3. **EXEC SQL CONTEXT USE**



该语句使用指定的运行上下文，使用之前必须先 ALLOCATE。EXEC SQL CONTEXT USE 指令与 C 规则一样有自己的范围，全局的上下文能在多个模块使用，局部的只能在当前模块使用。

例 在下面的代码片段中，function2()中的 UPDATE 语句使用的是全局上下文 ctx1。

```
sql_context ctx1; /* declare global context ctx1 */

function1()

{

    sql_context :ctx1; /* declare local context ctx1 */

    EXEC SQL CONTEXT ALLOCATE :ctx1;

    EXEC SQL CONTEXT USE :ctx1;

    EXEC SQL INSERT INTO ... /* local ctx1 used for this stmt */

    ...

}

function2()

{

	EXEC SQL UPDATE ... /* global ctx1 used for this stmt */

}
```

  4. **EXEC SQL CONTEXT FREE**



该语句用来释放指定的 CONTEXT。

下面的两个代码片段分别示例使用 CONTEXT 的两种方式。

例 1 示例使用多个 CONTEXT，每个线程使用单独的 CONTEXT。

```
main()
{
	sql_context ctx1,ctx2;
EXEC SQL ENABLE THREADS;
    EXEC SQL CONTEXT ALLOCATE :ctx1;
    EXEC SQL CONTEXT ALLOCATE :ctx2;
    ...
    thread_create(..., function1, ctx1);
    thread_create(..., function2, ctx2);
    ...
    EXEC SQL CONTEXT FREE :ctx1;
    EXEC SQL CONTEXT FREE :ctx2;
    ...
}

void function1(sql_context ctx)
{
	EXEC SQL CONTEXT USE :ctx;
	//使用ctx执行可执行SQL语句
	...
}

void function2(sql_context ctx)
{
    EXEC SQL CONTEXT USE :ctx;
    //使用ctx执行可执行SQL语句
    ...
}
```

例 2 示例多个线程使用同一个 context。因为 function1()和 function2()有可能同时执行，在使用 EXEC SQL 时应该使用互斥量。

```
mutex_t mutex;

main()

{

    sql_context ctx;

    EXEC SQL CONTEXT ALLOCATE :ctx;

    ...

    /* spawn thread, execute function1 (in the thread) passing ctx */

    thread_create(..., function1, ctx);

    /* spawn thread, execute function2 (in the thread) passing ctx */

    thread_create(..., function2, ctx);

    ...

}

void function1(sql_context ctx)

{

    EXEC SQL CONTEXT USE :ctx;

    mutex_lock(&mutex);

    /* Execute SQL statements on runtime context ctx. */

    ...

    mutex_unlock(&mutex);

}

void function2(sql_context ctx)

{

    EXEC SQL CONTEXT USE :ctx;

    mutex_lock(&mutex);

    /* Execute SQL statements on runtime context ctx. */

    ...

    mutex_unlock(&mutex);

}
```

### 6.7.4 多线程 PRO*C 程序注意事项

DM 提供的 PRO*C 是线程安全的，同时需要保证 PRO*C 应用编写也线程安全，如全局变量的使用等。除此之外需要注意以下两点：

  * 对于静态或全局宿主变量要注意线程安全
  * 避免在多个线程里同时使用同一个 CONTEXT



## 6.8PRO*C 与 OCI 环境关联

DM 兼容 Oracle 的 SQLEnvGet 和 SQLSvcCtxGet 接口，可以用来把 OCI 环境句柄与 PRO*C 的 CONTEXT 上下文环境关联起来，进而可以在 PRO*C 应用中使用 OCI 句柄进行操作。

### 6.8.1SQLEnvGet

SQLEnvGet 返回与 PRO*C 的 CONTEXT 关联的 OCI 环境句柄指针。

函数定义：

```
sword SQLEnvGet(

dvoid *rctx,

OCIEnv **oeh

);
```

**功能说明：**返回与 rctx 对应的 oeh OCI 环境句柄指针

参数说明：

  * rctx：输入参数，上下文变量 CONTEXT,SQL_CONTEXT 类型
  * oeh：输出参数，要返回的 OCI 环境句柄



返回值：

成功返回 0，失败返回-1

### 6.8.2SQLSvcCtxGet

SQLSvcCtxGet 用来获取与 PRO*C 上下文变量 sql_context 相关的 OCI 的上下文句柄，在 OCI 接口中能直接使用返回的 OCI 上下文句柄。

函数定义：

```
sword SQLSvcCtxGet(

dvoid *rctx,

text *dbname,

sb4 dbnamelen,

OCISvcCtx **svc

);
```

功能说明：

返回与 rctx 关联的 svc 给 OCISvcCtx 句柄

参数说明：

  * rctx：输入参数，sql_context 类型指针变量
  * dbname：输入参数，逻辑上的连接名
  * dbnamelen：输入参数，连接名长度
  * svc：输出参数，OCISvcCtx 指针的地址



返回值：

成功返回 0，失败返回-1

### 6.8.3 编写与 OCI 关联的 PRO*C 程序

编写与 OCI 关联的 PRO*C 程序的步骤主要如下：

  1. include OCI 接口的头文件 DCI.h
  2. 声明 OCI 环境句柄 OCIEnv *oeh
  3. 声明 OCI 上下文句柄 OCISvcCtx *svc
  4. 声明 OCI 的错误句柄 OCIError *err
  5. 使用 PRO*C 连接数据库
  6. 使用 SQLEnvGet 获取 OCI 的环境句柄，与 PRO*C 的上下文关联



在这一步中，单线程应用与多线程应用获取 OCI 环境句柄的方式有一些不同：

  * 单线程应用：



```
retcode = SQLEnvGet(SQL_SINGLE_RCTX, &oeh);
```

  * 多线程应用：



```
sql_context ctx1;

...

EXEC SQL CONTEXT ALLOCATE :ctx1;

EXEC SQL CONTEXT USE :ctx1;

...

EXEC SQL CONNECT :uid IDENTIFIED BY :pwd;

...

retcode = SQLEnvGet(ctx1, &oeh);
```

  7. 使用获取到的环境句柄分配 OCI 错误句柄



```
retcode = OCIHandleAlloc((dvoid *)oeh, (dvoid **)&err,

(ub4)OCI_HTYPE_ERROR, (ub4)0, (dvoid **)0);
```

  8. 使用 SQLSvcCtxGet 获取 OCI 要使用的上下文句柄



在这一步中，单线程应用与多线程应用获取上下文句柄的方式有一些不同：

  * 单线程应用：



```
retcode = SQLSvcCtxGet(SQL_SINGLE_RCTX, (text *)dbname, (ub4)dbnlen, &svc);
```

  * 多线程应用：



```
sql_context ctx1;

...

EXEC SQL ALLOCATE :ctx1;

EXEC SQL CONTEXT USE :ctx1;

...

EXEC SQL CONNECT :uid IDENTIFIED BY :pwd AT :dbname USING :hst;

...

retcode = SQLSvcCtxGet(ctx1, (text *)dbname, (ub4)strlen(dbname), &svc);
```

### 6.8.4PRO*C 中使用 OCI 实例

下面给出了一个在 PRO*C 中使用 OCI 的程序实例。

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <DCI.h>

EXEC SQL INCLUDE SQLCA;
typedef struct _tagORAConnection
{
    OCIEnv *envhp;
    OCIServer *srvhp;
    OCIError *errhp;
    OCISession *usrhp;
    OCISvcCtx *svchp;
    OCITrans* txnhp; //事务句柄 
    void * ctx; //pro*c连接上下文
    FILE* fpTracefile; //trace文件的句柄
} ORAConnection;

EXEC SQL BEGIN DECLARE SECTION;
char username1[20],password1[20],servername1[50];
int * nrc;
#define BUFFERMAX 20971520 //缓存 1024*1024*20 
unsigned char buffer[BUFFERMAX];
int nrsize;
OCIClobLocator *clob1;
OCIClobLocator *clob2;
int offset =1;
int nlength;
int errcnt=0;
EXEC SQL END DECLARE SECTION;

void
CHECKOK()
{
    if(sqlca.sqlcode!=0)
    {
        printf("fail! [%d] %s\n",sqlca.sqlcode,sqlca.sqlerrm.sqlerrmc);
        errcnt = errcnt +1;
    }
}

int CLIAppInit_EC(char dbAlias[],
    char user[],
    char pswd[],
    ORAConnection *conn)
{
    sword rc;
  
    rc = OCIHandleAlloc((dvoid *) conn->envhp, (dvoid **) & conn->srvhp,
    					OCI_HTYPE_SERVER, 0, (dvoid **) 0);
    if (rc != 0)
    	return -1;
  
    rc = OCIHandleAlloc((dvoid *) conn->envhp, (dvoid **) & conn->errhp,
    					OCI_HTYPE_ERROR, 0, (dvoid **) 0);
    if (rc != 0)
    	return -1;
   
    rc = OCIServerAttach(conn->srvhp, conn->errhp, (text *) dbAlias,
    					strlen(dbAlias), OCI_DEFAULT);
    if (rc != 0)
    {
        OCIHandleFree((dvoid *) conn->envhp, OCI_HTYPE_ENV);
        return -1;
    }
  
    OCIHandleAlloc((dvoid *) conn->envhp, (dvoid **) &conn->svchp,
    	OCI_HTYPE_SVCCTX, 0, (dvoid **) 0);
    OCIAttrSet((dvoid *) conn->svchp, OCI_HTYPE_SVCCTX, (dvoid *) conn->srvhp,0, OCI_ATTR_SERVER, conn->errhp);
    OCIHandleAlloc((dvoid *)conn->envhp, (dvoid **) &conn->usrhp,
    	OCI_HTYPE_SESSION, (size_t)0, (dvoid **) 0);
    OCIAttrSet((dvoid *) conn->usrhp, OCI_HTYPE_SESSION, (dvoid *) user,
    	(ub4)strlen(user), OCI_ATTR_USERNAME, conn->errhp);
    OCIAttrSet((dvoid *) conn->usrhp, OCI_HTYPE_SESSION, (dvoid *) pswd,
    	(ub4)strlen(pswd), OCI_ATTR_PASSWORD, conn->errhp);
  
	rc = OCISessionBegin(conn->svchp, conn->errhp, conn->usrhp,
						OCI_CRED_RDBMS, OCI_DEFAULT);
    if (rc != 0)
    {
        OCIHandleFree((dvoid *) conn->envhp, OCI_HTYPE_ENV);
        return -2;
    }

	OCIAttrSet((dvoid *)conn->svchp, OCI_HTYPE_SVCCTX,
		(dvoid *)conn->usrhp, 0, OCI_ATTR_SESSION, conn->errhp);
	OCIHandleAlloc((dvoid *) conn->envhp, (dvoid **) &conn->txnhp,
		OCI_HTYPE_TRANS, 0, 0);
	OCIAttrSet((dvoid *) conn->svchp, OCI_HTYPE_SVCCTX,
		(dvoid *) conn->txnhp, 0, OCI_ATTR_TRANS, conn->errhp);

	printf(" Connected to %s.\n", dbAlias);
	return 0;
} // CLIAppInit

int main()
{
    FILE *fp;
    OCIEnv *oeh;
    OCISvcCtx *svc;
    OCIError *err;
    sword rc;
    ORAConnection *_conn;
  
    EXEC SQL BEGIN DECLARE SECTION;
    sql_context ctx;
    EXEC SQL END DECLARE SECTION;
  
    EXEC SQL ENABLE THREADS;
    EXEC SQL CONTEXT ALLOCATE :ctx;
    EXEC SQL CONTEXT USE :ctx;
    printf("\n\n BEGIN sqlenvget_main sqlenvget \n");
    _conn = calloc(1, sizeof(ORAConnection));
    if (_conn == NULL)
    {
        printf("calloc failed");
        return -1;
    }
  
    printf("%s\n","STEP11");
    memset(servername1, 0, sizeof(servername1));
    memset(username1, 0, sizeof(username1));
    memset(password1, 0, sizeof(password1));
    strcpy(username1,"SYSDBA");
    strcpy(password1,"*****");
    strcpy(servername1,"192.168.0.157:5290");
  
    EXEC SQL CONNECT :username1 IDENTIFIED BY :password1 USING :servername1;
  
    printf("%s\n","STEP12");
    rc=SQLEnvGet(ctx, &_conn->envhp);
    rc = CLIAppInit_EC(servername1,
    username1,
    password1,
    _conn);
    if (rc != 0)
    {
    	goto END_FLAG;
    }
	rc=SQLSvcCtxGet(ctx, (text *)0, (ub4)0, &_conn->svchp);
  
    fp = NULL;
    fp = fopen("data.txt","rb");
    if( fp == NULL )
    {
        printf("%s\n","open file failed!");
        *nrc = 2;
        goto END_FLAG;
    }
  
    EXEC SQL DROP TABLE DTMODULE;
    EXEC SQL WHENEVER SQLERROR DO CHECKOK();
    EXEC SQL ALLOCATE :clob1;
    EXEC SQL ALLOCATE :clob2;
  
    EXEC SQL CREATE TABLE DTMODULE(c1 int,c2 clob);
    EXEC SQL INSERT INTO DTMODULE (c1,c2)VALUES(0,empty_clob());
    EXEC SQL COMMIT;
    EXEC SQL SELECT c2 INTO :clob1 FROM DTMODULE WHERE c1=0 FOR UPDATE;
  
    offset = 1;
    while((nlength =fread(buffer,1,BUFFERMAX,fp)) !=0 )
    {
        EXEC SQL LOB WRITE :nlength FROM :buffer INTO :clob1 at :offset;
        offset = offset + nlength;
    }
    nlength=0;
    EXEC SQL LOB DESCRIBE :clob1 GET LENGTH INTO :nlength;
  
    fclose(fp);
    EXEC SQL COMMIT;
  
    EXEC SQL SELECT C2 INTO :clob2 from DTMODULE WHERE c1=0;
  
    nlength=0;
    EXEC SQL LOB DESCRIBE :clob2 GET LENGTH INTO :nlength;
    printf("nlength=%d",nlength);
  
    EXEC SQL COMMIT RELEASE;
  
END_FLAG:
    if(_conn->txnhp)
    OCIHandleFree((dvoid *) _conn->srvhp, (ub4) OCI_HTYPE_SERVER);
    if(_conn->srvhp)
    OCIHandleFree((dvoid *) _conn->srvhp, (ub4) OCI_HTYPE_SERVER);
    if(_conn->svchp)
    OCIHandleFree((dvoid *) _conn->svchp, (ub4) OCI_HTYPE_SVCCTX);
    if(_conn->errhp)
    OCIHandleFree((dvoid *) _conn->errhp, (ub4) OCI_HTYPE_ERROR);
    if(_conn->usrhp)
    OCIHandleFree((dvoid *) _conn->usrhp, (ub4) OCI_HTYPE_SESSION);
    if(_conn->envhp)
    OCIHandleFree((dvoid *) _conn->envhp, (ub4) OCI_HTYPE_ENV);
    free(_conn);
    _conn = NULL;
  
    printf("\n\n END sqlenvget_main sqlenvget \n");
  
    return(0);
}
```

## 6.9 修改当前连接的自动提交属性

DM PRO*C 提供 dpc_autocommit()和 dpc_autocommit_off()接口，用于修改当前连接的自动提交属性。

函数定义：

```
void dpc_autocommit()
```

**功能说明：**修改当前连接为自动提交

函数定义：

```
void dpc_autocommit_off()
```

**功能说明：**修改当前连接为非自动提交

## 6.10 集合对象

PRO*C 中的集合对象句柄，对应 DM 数据库服务器的动态数组类型（VARRAY）。集合对象句柄为 OCIArray。

在使用对象句柄前，需要先定义、申请、绑定对象句柄；使用结束后，需要关闭和释放对象句柄。

1. 定义集合对象句柄

语法如下：

```
typedef OCIArray DB_ROLLING_STOCK_IDS;

DB_ROLLING_STOCK_IDS *coll = NULL;
```

功能说明：

像定义变量一样定义集合对象句柄。

参数说明：

coll：对象句柄。

2. 申请集合对象句柄

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] [FOR :num] ALLOCATE : coll;
```

功能说明：

使用 ALLOCATE 申请集合对象句柄。

参数说明：

dbname：连接名。

mum：对象个数，coll 为数组时可批量申请对象。

coll：对象句柄。

3. 对象句柄赋值

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] [FOR :num] COLLECTION APPEND :src [[INDICATOR] :src_ind] TO :coll [[INDICATOR] :coll_ind] ;
```

功能说明：

对象句柄追加方式写入数据。

参数说明：

dbname：连接名。

num：src 数据个数,src 为数组时一次赋值多个元素

src：赋值数据绑定变量。

src_ind：src 对应指示符。

coll：对象句柄。

coll_ind：对象指示符，仅语法支持。

4. 读对象数据

语法如下：

```
EXEC SQL [AT [dbname | :dbname]] [FOR :num]

COLLECTION GET :coll [[INDICATOR] :coll_ind]

INTO :hv [[INDICATOR] :hv_ind] ;
```

功能说明：

使用对象句柄读数据到本地缓存区。

参数说明：

dbname：指明对象读操作使用的连接名。

num：对象每次读取元素个数 。

coll：对象句柄。

coll_ind：对象指示符。

hv：读取到的本地缓存区。

hv_ind：指示符。

5. 获取对象的属性描述信息

语法如下：

```
EXEC SQL [AT [dbname | :dbname]]

  COLLECTION DESCRIBE :coll [[INDICATOR] :coll_ind]

   GET attribute1 [{, attributeN}]

     INTO :hv1 [[INDICATOR] :hv_ind1] [{, hvN [[INDICATOR] :hv_indN]}] ;
```

功能说明：

获取对象的描述信息。

参数说明：

dbname：指明对象读操作使用的连接名。

coll：对象句柄。

coll_ind：对象指示符。

attribute1：属性 DATA_SIZE | TYPECODE | DATA_TYPE | NUM_ELEMENTS | PRECISION | SCALE | TYPE_NAME | TYPE_SCHEMA | SIZE | TABLE_SIZE。目前只支持 SIZE: 集合对象中元素个数。

hv：读取到的本地缓存区。

hv_ind：指示符。

6. 重置对象偏移

语法如下：

```
EXEC SQL EXEC SQL [AT [dbname | :dbname]]

 COLLECTION RESET :collect [ [INDICATOR] :collect_ind] ;
```

功能说明：

如果需要从对象开始位置插入数据或读取数据，可以重置对象的偏移位置。

参数说明：

dbname：指明对象读操作使用的连接名。

coll：对象句柄。

coll_ind：对象指示符。

7. 释放集合对象句柄

```
EXEC SQL [AT [dbname | :dbname]] [FOR :num] FREE : coll;
```

功能说明：

使用 FREE 释放集合对象句柄。

参数说明：

dbname：连接名。

num：对象个数，coll 为数组时可批量申请对象。

coll：对象句柄。

## 6.11 VAR 和 TYPE 语句

DM 的 PRO*C 支持通过 VAR 语句指定变量与数据库中的类型等效，或使用 TYPE 语句将用户定义的数据类型等同于数据库中的类型。

  * **VAR 语句**



语法如下：

```
EXEC SQL VAR host_variable IS dtype [(length)] ;
```

功能说明：

宿主变量数据类型同等化或给宿主变量指定一个外部数据类型，覆盖变量原有的数据类型和长度。

参数说明：

host_variable：在应用中 VAR 之前定义的宿主变量。

dtype：同等化外部类型，目前只支持 STRING 类型，STRING 类型存放数据时含有结束符。

length：变量长度。

例如：

```
EXEC SQL BEGIN DECLARE SECTION; 
  ... 
  char dept_name[15];        -- default datatype is CHAR 
  EXEC SQL VAR dept_name IS STRING; -- reset to STRING 
  ...
EXEC SQL END DECLARE SECTION;
```

  * **TYPE 语句**



语法如下：

```
EXEC SQL TYPE type IS dtype [(length)] [REFERENCE] ;
```

功能说明：

自定义类型同等化为外部类型。

参数说明：

type : 应用自定义类型，用来跟外部类型同等化

dtype : 外部类型，目前支持 STRING,RAW, CHAR, VARCHAR,CHARZ

length : 外部类型长度

reference : 同等化指针类型，type 是指针

例如：

```
struct screen {
  short len; 
  char buff[4002];
}; 

typedef struct screen graphics; 

  EXEC SQL TYPE graphics IS RAW(4002); 
  graphics crt; -- host variable of type graphics 
  ...
```

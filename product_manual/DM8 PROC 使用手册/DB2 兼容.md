

# DB2 兼容

DM PRO*C 对 DB2PRO*C 的常用语法进行了兼容，在 dpc_new 预编译命令中将 MODE 参数置为 DB2 表示使用兼容 DB2 语法的编译模式。使用兼容 DB2 模式时，在编译时还需要使用 sqlca_db2.h、sqlda_db2.h。

## 5.1 简单的 DB2 嵌入式程序结构分析

首先通过一个简单的 DB2 嵌入式程序入手，对 DB2 嵌入式程序的结构及基本语法进行分析。与 Oracle、DM 嵌入式程序不同的是，DB2 嵌入式程序的扩展名不是.pc，而是.sqc。

例 以下名为 update.sqc 的嵌入式程序，完成对表 staff 相关操作的功能。

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
EXEC SQL INCLUDE SQLCA;
int main(int argc, char *argv[]) {
    EXEC SQL BEGIN DECLARE SECTION;
        char pname[10];
        short dept;
        char username[19],password[19],servername[19];
    EXEC SQL END DECLARE SECTION;
    printf(" Please input Servername :");
    scanf("%s",servername);
    printf("Input Username: ");
    scanf("%s", username);
    printf("Input Password: ");
    scanf("%s",password);
    EXEC SQL CONNECT TO :servername USER :username USING :password;
    EXEC SQL CREATE TABLE STAFF (name varchar(10), dept int, job varchar(40));
    EXEC SQL INSERT INTO STAFF VALUES('Jack',80,'Mgr');
    EXEC SQL INSERT INTO STAFF VALUES('Joan',30,'Mgr');
    EXEC SQL DECLARE c1 CURSOR FOR
    SELECT name, dept FROM staff WHERE job='Mgr'FOR UPDATE OF job;
    EXEC SQL OPEN c1;
    do {
        EXEC SQL FETCH c1 INTO :pname, :dept;
        if (SQLCODE != 0) break;
        if (dept > 40) {
            printf( "%-10.10s in dept. %2d will be demoted to Clerk\n",
            pname, dept );
            EXEC SQL UPDATE staff SET job = 'Clerk'
            WHERE CURRENT OF c1;
    	}
        else {
            printf ("%-10.10s in dept. %2d will be DELETED!\n",
            pname, dept);
            EXEC SQL DELETE FROM staff WHERE CURRENT OF c1;
        } //endif
	} while ( 1 );
    EXEC SQL CLOSE c1;
    EXEC SQL DROP TABLE STAFF;
    EXEC SQL ROLLBACK;
    printf( "\nOn second thought -- changes rolled back.\n" );
    EXEC SQL CONNECT RESET;
    return 0;
}
```

与其他 PRO*C 文件类似，DB2 的 sqc 文件由以下几个部分组成：

  1. **SQLDA/SQLCA 引用**



```
EXEC SQL INCLUDE SQLCA;
```

详见 5.2 SQLDA/SQLCA。

  2. **宿主变量定义**



```
EXEC SQL BEGIN DECLARE SECTION;

char pname[10];

short dept;

char userid[9];

char passwd[19];

EXEC SQL END DECLARE SECTION;
```

详见 3.2 宿主变量定义。

  3. **登录数据库**



```
EXEC SQL CONNECT TO localhost USER :userid USING :passwd [AT [dbname | :dbname]];;
```

  4. **数据操作**



登录成功之后，便可对数据库进行各种操作。

  5. **退出登录**



```
EXEC SQL [AT [dbname | :dbname]] CONNECT RESET;
```

操作完毕后，从数据库退出登录。

## 5.2SQLDA/SQLCA

DB2 中的 SQLDA、SQLCA 是用于记录 PRO*C 程序在执行过程中的描述信息及诊断信息。为了实现与 DB2 的兼容，DM 在 dpc_dll 中对其进行实现，保留了 SQLDA、SQLCA 各成员变量在 DB2 中的原始含义，并分别用于 DB2 PRO*C 程序在 DM 上执行的描述及诊断信息的记录。

SQLDA、SQLCA 的相关声明与定义在预编译时不会自动引入，用户需在 PRO*C 文件的头部手动引入，代码如下所示：

```
EXEC SQL INCLUDE SQLCA;

EXEC SQL INCLUDE SQLDA;
```

## 5.3 可执行的 SQL 语句

DM 与 DB2 登录数据库的语法不同，为此，DM 对 DB2 登录数据库语句语法实现了支持。

语法如下：

```
EXEC SQL CONNECT TO :servername USER :username USING :psw [AT [dbname |:dbname]];;

或

EXEC SQL CONNECT TO servername USER :username USING :psw [AT [dbname |:dbname]];;
```

功能：

用于数据库的登录。

使用说明：

  * 语句中 username 是长度最多为 128 个字符的指针或者为具体名称(如 localhost)，其值为注册用户的名字；
  * pwd 表示相应的用户口令，口令的长度最多为 128 个字符；
  * dbname 用于指定连接名，为长度最大为 128 个字符的标识符或宿主变量。在使用多连接时给每个连接命名（不命名时系统会自动给一个默认连接名），在执行其他嵌入 SQL 时通过指定连接名可以切换到不同连接进行操作；
  * servername 为服务器的名字, 名字的长度最多为 128 个字符。



> **注意**
>
> 在用户程序中，登录语句的逻辑位置必须先于所有可执行的 SQL语句，只有执行登录语句之后，才能执行其它可执行的
> SQL语句。如果登录失败，则终止程序的运行。



其他可执行语句详见 3.3 节描述。

## 5.4 数据类型映射

指定 DB2 兼容的预编译模式时，数据类型的映射如下表所示。

表5.1 DB2兼容数据类型映射

|**数据类型**|**DM_TYPE**|**DB2_TYPE 兼容**|**ANSI 标准类型**|
|-----------|-----------|-----------|-----------|
|DSQL_CHAR|1|-109|1|
|DSQL_VARCHAR|2|-108|12|
|DSQL_BIT|3|不支持|14|
|DSQL_TINYINT|5|不支持|-1|
|DSQL_SMALLINT|6|-121|5|
|DSQL_INT|7|-120|4|
|DSQL_BIGINT|8|-119|-2|
|DSQL_DEC|9|-117|3|
|DSQL_FLOAT|10|-116|6|
|DSQL_DOUBLE|11|-116|8|
|DSQL_BLOB|12|-106|不支持|
|DSQL_DATE|14|-100|9|
|DSQL_TIME|15|-101|9|
|DSQL_TIMESTAMP|16|-103|9|
|DSQL_BINARY|17|不支持|-3|
|DSQL_VARBINARY|18|不支持|-3|
|DSQL_CLOB|19|-107|-4|
|DSQL_TIME_TZ|22|不支持|9|
|DSQL_TIMESTAMP_TZ|23|不支持|9|
|DSQL_INTERVAL_YEAR|100|-100|10|
|DSQL_INTERVAL_MONTH|101|-100|10|
|DSQL_INTERVAL_DAY|102|-100|10|
|DSQL_INTERVAL_HOUR|103|-100|10|
|DSQL_INTERVAL_MINUTE|104|-100|10|
|DSQL_INTERVAL_SECOND|105|-100|10|
|DSQL_INTERVAL_YEAR_TO_MONTH|106|-100|10|
|DSQL_INTERVAL_DAY_TO_HOUR|107|-100|10|
|DSQL_INTERVAL_DAY_TO_MINUTE|108|-100|10|
|DSQL_INTERVAL_DAY_TO_SECOND|109|-100|10|
|DSQL_INTERVAL_HOUR_TO_MINUTE|110|-100|10|
|DSQL_INTERVAL_HOUR_TO_SECOND|111|-100|10|
|DSQL_INTERVAL_MINUTE_TO_SECOND|112|-100|10|


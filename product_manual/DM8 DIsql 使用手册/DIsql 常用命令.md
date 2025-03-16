

# DIsql 常用命令

## 4.1 帮助 HELP

DIsql 帮助命令，可以帮助用户查看其他命令的具体用法。用户可以看到其他命令系统显示的内容，概括为：

  * 命令的标题
  * 命令的文本描述
  * 命令的简写（例如，AUTO 可以代替 AUTOCOMMIT）
  * 可以向命令传递的强制参数和可选参数



help 显示指定命令的帮助信息。

语法如下：

```
HELP|? [topic]
```

topic：命令名称或者命令名称的首字母，查询某一命令用法或者某一字母开头的所有命令用法。

示例如下：

```
SQL> HELP DEFINE

DEFINE

------

设置变量值，或者显示已定义的变量信息。

DEF[INE] [variable] | [variable = text]
```

## 4.2 输出文件 SPOOL

将查询结果输出到指定文件。

语法如下：

```
SPOOL {<file> | OFF }

<file>：：= <file_path> [CRE[ATE]|REP[LACE]|APP[END]][NO_PRINT]
```

\<file_path>：指定文件的绝对路径

CRE[ATE]：创建指定的文件，若指定的文件已存在，则报错

REP[LACE]：创建指定的文件，若指定的文件已存在，则替换它，默认方式

APP[END]：将输出内容追加到指定文件的末尾

NO_PRINT：控制台不打印查询结果

OFF：关闭 SPOOL 输出

> **注意**
>
> 只有SPOOL OFF之后，才能在输出文件中看到输出的内容。



示例如下：

环境变量 CONSOLE_PRINT 缺省为 ON，具体请参见 [3.3.31 CONSOLE_PRINT](https://eco.dameng.com/document/dm/zh-cn/pm/environment-variable-settings.html#3.3.31%20CONSOLE_PRINT)。

```
SQL> spool d:\b.sql

SQL> select 1;


行号       1

---------- -----------

1          1


已用时间: 0.400(毫秒). 执行号:325.

SQL> spool off
```

执行上述语句，控制台打印查询结果，并将查询结果输出到指定文件 d:\b.sql 中。

指定 NO_PRINT。

```
SQL> spool d:\b.sql no_print

SQL> select 2;

SQL> spool off
```

执行上述语句，控制台不打印查询结果，只将查询结果输出到指定文件 d:\b.sql 中。

## 4.3 切换到操作系统命令 HOST

使用 HOST 命令可以不用退出 DIsql 就能执行操作系统命令。如果单独执行 host，则能够直接从 DIsql 界面切换到操作系统，之后可使用 EXIT 回到 DIsql 界面。

语法如下：

```
HOST[<command>]
```

\<command>：操作系统命令。

示例如下：

```
SQL>HOST DIR
```

## 4.4 获取对象结构信息 DESCRIBE

获取表或视图、存储过程、函数、包、记录、类的结构描述。

语法如下：

```
DESC[RIBE] [<模式名>.]<对象名>
```

各对象获取的内容略有不同：

  * 表或视图获取的内容包括列名，列数据类型，列是否可以取空值。
  * 函数、过程、类获取的内容包括两类：1）存储函数/过程名，类型（函数或过程），函数返回值类型；2）参数名，参数数据类型、参数输入输出属性、参数缺省值。
  * 包获取的内容分为两类：1）包内存储函数/过程名，类型（函数或过程），函数返回值类型；2）包内参数名，参数数据类型、参数输入输出属性、参数缺省值。
  * 记录获取的内容为：参数名，参数数据类型，参数是否可以取空值。



注意：当仅声明了包或自定义类型中的过程或函数，但包还未创建包主体或自定义类型还未创建类型体时，对包或自定义类型使用 DESC 会报错，此时不能使用 DESC。

示例如下：

例 1 获取表 sysgrants 的结构描述。

```
SQL> desc sysgrants;
```

执行结果如下：

```
行号     NAME      TYPE$   NULLABLE
---------- --------- ------- --------
1          URID      INTEGER N
2          OBJID     INTEGER N
3          COLID     INTEGER N
4          PRIVID    INTEGER N
5          GRANTOR   INTEGER N
6          GRANTABLE CHAR(1) N

6 rows got

已用时间: 74.394(毫秒). 执行号:65201.
```

例 2 获取存储过程的结构描述。

创建一个存储过程：

```
CREATE OR REPLACE PROCEDURE PROC_1(A IN OUT INT)  AS
B  INT;
BEGIN
A:=A+B;
EXCEPTION
  WHEN OTHERS THEN NULL;
END;
/
```

获取存储过程 PROC_1 的结构描述：

```
SQL>describe  PROC_1;
```

执行结果如下：

```
行号     NAME   TYPE$   IO    DEF RT_TYPE
---------- ------ ------- ----- --- -------
1          PROC_1 PROC
2            A    INTEGER INOUT
已用时间: 10.350(毫秒). 执行号:65203.
```

例 3 获取包的结构描述

创建数据库表：

```
CREATE TABLE Person(Id INT IDENTITY, Name VARCHAR(100), City VARCHAR(100));

INSERT INTO Person(Name, City) VALUES('Tom','武汉');

INSERT INTO Person(Name, City) VALUES('Jack','北京');

INSERT INTO Person(Name, City) VALUES('Mary','上海');
```

创建包：

```
CREATE OR REPLACE PACKAGE PersonPackage AS

        E_NoPerson EXCEPTION; 
  
        PersonCount INT; 
  
Pcur CURSOR; 

        PROCEDURE AddPerson(Pname VARCHAR(100), Pcity varchar(100)); 
  
        PROCEDURE RemovePerson(Pname VARCHAR(100), Pcity varchar(100)); 
  
PROCEDURE RemovePerson(Pid INT); 

        FUNCTION GetPersonCount RETURN INT; 
  
        PROCEDURE PersonList; 
  
END PersonPackage;

/
```

创建包主体：

```
CREATE OR REPLACE PACKAGE BODY PersonPackage AS

  PROCEDURE AddPerson(Pname VARCHAR(100), Pcity varchar(100) )AS
  
    BEGIN
  
      INSERT INTO Person(Name, City) VALUES(Pname, Pcity); 
  
      PersonCount = PersonCount + SQL%ROWCOUNT; 
  
    END AddPerson; 
  
  PROCEDURE RemovePerson(Pname VARCHAR(100), Pcity varchar(100)) AS
  
    BEGIN
  
      DELETE FROM Person WHERE NAME LIKE Pname AND City like Pcity; 
  
      PersonCount = PersonCount - SQL%ROWCOUNT; 
  
    END RemovePerson; 
  
  PROCEDURE RemovePerson(Pid INT) AS
  
    BEGIN
  
      DELETE FROM Person WHERE Id = Pid; 
  
      PersonCount = PersonCount - SQL%ROWCOUNT; 
  
    END RemovePerson; 
  
  FUNCTION GetPersonCount RETURN INT AS
  
BEGIN

    RETURN PersonCount; 
  
END GetPersonCount; 

  PROCEDURE PersonList AS
  
 DECLARE
 
 V_id INT; 
 
V_name VARCHAR(100); 

V_city VARCHAR(100); 

 BEGIN
 
 IF PersonCount = 0 THEN
 
       RAISE E_NoPerson; 
   
 END IF; 
 
    OPEN Pcur FOR SELECT Id, Name, City FROM Person;  
  
LOOP

      FETCH Pcur INTO V_id,V_name,V_city; 
  
      EXIT WHEN Pcur%NOTFOUND; 
  
      PRINT ('No.' + (cast (V_id as varchar(100))) + '  ' + V_name + '来自' + V_city ); 
  
    END LOOP; 
  
    CLOSE Pcur; 
  
  END PersonList; 
  
BEGIN

    SELECT COUNT(*) INTO PersonCount FROM Person; 
  
END PersonPackage;

/
```

获取包 PersonPackage 的结构描述：

```
SQL>describe  PersonPackage;
```

执行结果如下：

```
行号     NAME           TYPE$        IO DEF RT_TYPE
---------- -------------- ------------ -- --- -------
1          ADDPERSON      PROC
2            PNAME        VARCHAR(100) IN
3            PCITY        VARCHAR(100) IN
4          REMOVEPERSON   PROC
5            PNAME        VARCHAR(100) IN
6            PCITY        VARCHAR(100) IN
7          REMOVEPERSON   PROC
8            PID          INTEGER      IN
9          GETPERSONCOUNT FUNC                INTEGER
10         PERSONLIST     PROC
10 rows got
已用时间: 24.349(毫秒). 执行号:65210.
```

例 4 使用 DEPTH 显示列的结构信息

```
CREATE TYPE ADDRESS AS OBJECT

( STREET  VARCHAR2(20),

    CITY    VARCHAR2(20)
  
  );
  
/

CREATE TYPE ADDRESS1 AS OBJECT

( NO  INT,

  SADDR  ADDRESS
  
  );
  
/
```

创建表 EMPINFO：

```
CREATE TABLE EMPINFO

  (LAST_NAME VARCHAR2(30),
  
   EMPADDR ADDRESS,
   
   SMADDR ADDRESS1,
   
   JOB_ID VARCHAR2(20),
   
   SALARY NUMBER(7,2)
   
  );
```

设置 DESCRIBE 的显示方式 :

```
SQL>SET DESCRIBE DEPTH 1 LINENUM ON INDENT ON;
```

获取表 EMPINFO 的结构描述如下：

```
SQL>DESC EMPINFO;
行号     ID PID NAME      TYPE$       NULLABLE
---------- -- --- --------- ----------- --------
1          1      LAST_NAME VARCHAR(30) Y
2          2      EMPADDR   ADDRESS     Y
3          3  2     STREET  VARCHAR(20)
4          4  2     CITY    VARCHAR(20)
5          5      SMADDR    ADDRESS1    Y
6          6  5     NO      INTEGER
7          7  5     SADDR   ADDRESS
8          8      JOB_ID    VARCHAR(20) Y
9          9      SALARY    DEC(7, 2)   Y
9 rows got
已用时间: 23.473(毫秒). 执行号:65214.
```

设置 DEPTH 为 1 时，表 EMPINFO 的结构信息只显示至第一层。LINENUM 为 ON 时，显示行号 ID、PID 信息；反之，行号 ID、PID 信息不显示；INDENT 为 ON 时，NAME 的显示方式发生了缩进；反之，不发生缩进。

重新设置 DESCRIBE 的显示方式，并获取表 EMPINFO 的结构描述如下：

```
SQL>SET DESCRIBE DEPTH 2 LINENUM ON INDENT ON;

SQL>DESC EMPINFO;
```

执行结果如下：

```
行号     ID PID NAME       TYPE$       NULLABLE
---------- -- --- ---------- ----------- --------
1          1      LAST_NAME  VARCHAR(30) Y
2          2      EMPADDR    ADDRESS     Y
3          3  2     STREET   VARCHAR(20)
4          4  2     CITY     VARCHAR(20)
5          5      SMADDR     ADDRESS1    Y
6          6  5     NO       INTEGER
7          7  5     SADDR    ADDRESS
8          8  7       STREET VARCHAR(20)
9          9  7       CITY   VARCHAR(20)
10         10     JOB_ID     VARCHAR(20) Y
11         11     SALARY     DEC(7, 2)   Y
11 rows got
已用时间: 10.566(毫秒). 执行号:65215.
```

与 DEPTH 为 1 时对比，表 EMPINFO 的结构描述增加了属于第二层的两列信息：STREET 和 CITY。

## 4.5 管理本地变量 DEFINE, COLUMN 和 UNDEFINE

定义本地变量的命令有 DEFINE 和 COLUMN。

删除本地变量的命令是 UNDEFINE。

### 4.5.1 DEFINE

用来定义一个本地变量的替代变量，然后对该变量赋一个 CHAR 类型的值；或者输出变量的值和类型。

语法如下：

```
DEF[INE] [<VARIABLE=text>|< VARIABLE >]
```

DEF[INE] VARIABLE = text：申明一个变量，如果该变量存在，则重新赋值，否则新生成一个变量，并进行赋值。

DEF[INE] VARIABLE：如果该变量存在，则输出特定 VARIABLE 的值和类型，否则报错。

DEF[INE]：输出 DIsql 中所有的变量的值和类型。

该命令定义的替代变量在当前的 DIsql 环境和/NOLOG 环境中均可以起作用。

当使用该命令定义变量时，如果变量值包含空格或区分大小写，则用引号引注。另外，使用“DEFINE 变量名”可以检查变量是否已经定义。

DEFINE 定义的变量会保存在环境 DIsql 环境中，可以在 SQL 语句中使用。默认的变量前缀是&。

示例如下：

```
SQL>DEF VAR=666;

SQL>select * from sysobjects where id=&VAR;
```

如果 var 没有定义，会提示输入变量的值；没有定义的 var 不会保存在 DIsql 环境中。

关闭变量替换：

```
SET define off
```

例 1 Define 变量与其他字符之间的连接字符是点号’.’。示例如下：

```
SQL>SET DEFINE ON
SQL>DEF VAR1 = C;
SQL>DEF VAR2 = TEST1;
SQL>DROP TABLE TEST1;
SQL>CREATE TABLE TEST1(C1 INT);
SQL>INSERT INTO TEST1 VALUES(1);
SQL>COMMIT;
SQL>SELECT &VAR1.1 FROM TEST1;
```

执行结果如下：

```
行号       C1
---------- -----------
1          1
已用时间: 0.428(毫秒). 执行号:702.

SQL> select &var2..c1 from test1;
```

执行结果如下：

```
原值 1:select &var2..c1 from test1;
新值 1:select TEST1.c1 from test1;

行号       C1
---------- -----------
1          1
已用时间: 0.355(毫秒). 执行号:703.
```

例 2 DEFINE 变量定义为整型。

```
SQL>SET DEFINE ON //打开DEFINE变量定义
SQL>DEFINE C1=1   //定义变量C1为1 
SQL>SELECT &C1 FROM DUAL;
```

执行结果如下：

```
原值 1:SELECT &C1 FROM DUAL;
新值 1:SELECT 1 FROM DUAL;
行号       1
---------- -----------
1          1

已用时间: 0.477(毫秒). 执行号:704.

//在存储函数中的使用
SQL>CREATE OR REPLACE FUNCTION F1(C1 INT)RETURN INT IS 
BEGIN
C1=&C1;
RETURN(C1);
END;
/

SQL> SELECT F1(0);
```

执行结果如下：

```
行号       F1(0)
---------- -----------
1          1
已用时间: 0.355(毫秒). 执行号:707.

SQL>DEFINE C2=(2+3*4) //定义变量C2为表达式,定义为表达式时必须加括号
SQL> SELECT &C2*4 FROM DUAL;
```

执行结果如下：

```
原值 1:SELECT &C2*4 FROM DUAL;
新值 1:SELECT (2+3*4)*4 FROM DUAL;
行号       (2+(3*4))*4
---------- -----------
1          56
已用时间: 0.490(毫秒). 执行号:708.
```

例 3 DEFINE 变量定义为字符型。

```
SQL>SET DEFINE ON //打开DEFINE变量定义
SQL>DEFINE C3="'OG'" //定义变量C3为'OG'
```

一种使用 DEFINE 字符串变量的方式

```
SQL> SELECT &C3 FROM DUAL;
```

执行结果如下：

```
原值 1:SELECT &C3 FROM DUAL;
新值 1:SELECT 'OG' FROM DUAL;
行号       'OG'
---------- ----
1          OG
已用时间: 0.470(毫秒). 执行号:709.

SQL>SELECT LCASE(&C3) FROM DUAL;//引用变量为函数参数
```

执行结果如下：

```
原值 1:SELECT LCASE(&C3) FROM DUAL;
新值 1:SELECT LCASE('OG') FROM DUAL;
行号       LCASE('OG')
---------- -----------
1          og
已用时间: 14.052(毫秒). 执行号:59.
SQL>SET DEFINE ON //打开DEFINE变量定义
SQL>DEFINE C4=OG //定义变量C4为OG  
```

另外一种使用 DEFINE 字符串变量的方式

```
SQL>SELECT '&C4' FROM DUAL;
```

执行结果如下：

```
原值 1:SELECT '&C4' FROM DUAL;
新值 1:SELECT 'OG' FROM DUAL;
行号       'OG'
---------- ----
1          OG
已用时间: 0.173(毫秒). 执行号:711.
SQL>CREATE OR REPLACE FUNCTION F1(&C4 INT)RETURN INT IS 
BEGIN
&C4=777;
RETURN(&C4);
END;
/

SQL> SELECT F1(0);
```

执行结果如下：

```
行号       F1(0)
---------- -----------
1          777                          // 返回值OG=777
已用时间: 0.460(毫秒). 执行号:713.
```

例 4  DEFINE 变量定义为日期类型。

```
SQL>SET DEFINE ON //打开DEFINE变量定义
SQL>DEFINE C5="DATE'2015-10-01'"
SQL>SELECT &C5+1 FROM DUAL; //引用变量值加1天
```

执行结果如下：

```
原值 1:SELECT &C5+1 FROM DUAL;
新值 1:SELECT DATE'2015-10-01'+1 FROM DUAL;
行号       DATE'2015-10-01'+1
---------- ------------------
1          2015-10-02
已用时间: 28.478(毫秒). 执行号:714.

SQL>SELECT &C5+INTERVAL '01-02' YEAR TO MONTH FROM DUAL; //引用变量值与日期类型作运算
```

执行结果如下：

```
原值 1:SELECT &C5+INTERVAL '01-02' YEAR TO MONTH FROM DUAL;
新值 1:SELECT DATE'2015-10-01'+INTERVAL '01-02' YEAR TO MONTH FROM DUAL;
行号       DATE'2015-10-01'+INTERVAL+'01-02'YEARTOMONTH
---------- --------------------------------------------
1          2016-12-01
已用时间: 0.482(毫秒). 执行号:715.
```

### 4.5.2 COLUMN

定义一个本地列或表达式。

语法如下：

```
COL[UMN] [<column | expr> [<option>]] 
<option> ::=	NEW_VALUE variable |
				FOR[MAT] <format>
<format> ::=	An | an | <fmt>
```

COL[UMN]：列举出所有的 COLUMN 变量信息。

COL[UMN] column | expr：列举出某个 column 或 expr，如果存在，则输出信息，否则报错。

COL[UMN] column | expr option：option 目前仅支持 NEW_VALUE 和 FORMAT。

NEW_VALUE：表示该 column|expr 的值同时作为变量 variable 存在。但如果该变量未赋值，通过 DEFINE 查询时，不会显示该变量。查询结果的最后一个值赋给变量 variable。

FOR[MAT]：表示该 column|expr 的列打印格式。

  1. An 或 an 是字符类型列的格式串，其中，‘A’或‘a’是字符类型列格式串的前缀，n 是列打印长度的值，n 的取值范围为 1~60000。仅当 ISQL_MODE = 1 且 COLUMN \<column | expr> 为 ON 时，该参数有效。定长类型的列的打印长度不受该参数影响。



2）\<fmt> 是数值类型列的格式串，格式串由格式控制符组合而成。格式控制符支持逗号(,)、点号(.)、0、9、D、G、X、V、RN、rn、FM、$、B、S、TM9、TME、C、L、EEEE、MI、PR 等，具体可参考《DM8_SQL 语言使用手册》8.1 节中函数 to_char 中关于 fmt 的相关描述。

示例如下：

准备数据如下：

```
SQL> DROP TABLE TEST;
SQL> CREATE TABLE TEST(C1 VARCHAR(50));
SQL> INSERT INTO TEST VALUES('DAMENGSHUJUKU');
SQL> INSERT INTO TEST VALUES('A');
SQL> INSERT INTO TEST VALUES('B');
SQL> COMMIT;
```

例 1  将列 C1 的值作为变量 DVAR，缺省 COLUMN C1 ON。

```
SQL> COLUMN C1 NEW_VALUE DVAR
SQL> SELECT * FROM TEST;
```

执行结果如下：

```
行号   C1  
---------- -------------
1     DAMENGSHUJUKU
2     A
3     B
 
SQL> DEFINE DVAR
DEFINE DVAR   = "B"  (CHAR)
```

例 2  设置 ISQL_MODE 为 1，C1 列的列打印长度为 8，缺省 WRAP 为 ON，表示折行打印结果集，缺省 COLUMN C1 ON。

```
SQL> SET ISQL_MODE 1
SQL> COLUMN C1 FORMAT A8
SQL> SELECT * FROM TEST;
```

执行结果如下：

```
行号     C1   
---------- --------
1          DAMENGSH
           UJUKU  
2          A   
3          B   
```

通过如下方式设置是否将变量 variable 或列打印长度 format 与 column|expr 相关联，缺省值为 ON，表示已关联。

语法如下：

```
COL[UMN] <column | expr> <OFF|ON(默认值)>
```

示例如下：

```
SQL>COLUMN C1 OFF
```

例 3 设置 ISQL_MODE 为 1，设置数值类型列 C1 格式串为 09,99.999。

```
SET ISQL_MODE 1
CREATE TABLE T1(C1 DEC(5,2));
INSERT INTO T1 VALUES(123.45);
COMMIT;
SET ISQL_MODE 1
COL C1 FOR 09,99.999
SELECT * FROM T1;
```

执行结果如下：

```
行号       C1
---------- ----------
1           01,23.450 
```

### 4.5.3 UNDEFINE

删除一个或多个本地变量，此变量可以是 DEFINE 定义生成的本地变量，也可以是 COLUMN 关联的本地变量。

语法如下：

```
UNDEF[INE] <变量子句>
<变量子句>::=<变量名>{ <变量名>}
```

示例如下：

```
SQL> UNDEF VAR1 C1 

```

### 4.5.4 ACCEPT

从命令行读取内容，将其赋值给变量。

语法如下：

```
ACC[EPT] <变量名> [<变量类型>] [FOR[MAT] <format>] [DEF[AULT] <缺省值>] [<前导字符串>] [HIDE]

<变量类型>::= NUM[BER] | CHAR | DATE | BINARY_FLOAT | BINARY_DOUBLE

<前导字符串>::= PROMPT <字符串值>|

              NOPR[OMPT]
```

\< 变量名 >：用于指定存储读取内容的变量名。最大长度为 30 个字符，如果变量不存在则新加一个变量。

\< 变量类型 >：变量的类型，支持 NUMBER、CHAR、DATE、FLOAT 和 DOUBLE。缺省为 CHAR。

FOR[MAT] *\<*format>:指定输入内容的格式，如果输入内容不满足指定格式报错重新输入。具体用法参考 COLUMN 命令的 FORMAT。

DEF[AULT] \< 缺省值 >：指定变量的缺省值。\< 缺省值 > 必须满足 FORMAT 格式，如果命令行没有输入内容，则给变量赋值 \< 缺省值 >。如果未指定 DEF[AULT] \< 缺省值 >，且命令行没有输入内容，则给变量赋值空串。

\< 前导字符串 >：PROMPT 指定前导字符串。NOPR[OMPT]指定不用前导字符串。缺省为不指定。

HIDE：用于指定将输入内容隐藏。缺省则不隐藏。

示例如下：

例 定义一个变量 VAR，将命令行输入值 8888 赋值给变量 VAR。

```
SQL> DEF VAR=666; 

SQL> ACCEPT VAR NUMBER FORMAT L9999 DEFAULT ￥1020 PROMPT XXX   //输入值须满足FORMAT格式L9999，即货币符号和最多4位数字。

XXX￥8888    //命令行显示XXX，用户输入￥8888

SQL> DEFINE VAR     //查看变量值

DEFINE VAR    = "8888"    (NUMBER)
```

## 4.6 查看执行计划 EXPLAIN

用 EXPLAIN 命令来查看查询语句的执行计划。

语法如下：

```
EXPLAIN <sql_clause>
```

\<sql_clause>请参考《DM8_SQL 语言使用手册》。

示例如下：

```
SQL>EXPLAIN select count(*) from sysobjects;
```

## 4.7 设置异常处理方式 WHENEVER

用 whenever 命令可以设置异常处理方式，继续执行或退出 DIsql。

语法如下：

```
WHENEVER SQLERROR

CONTINUE [ COMMIT | ROLLBACK | NONE ] |

EXIT [ SUCCESS | FAILURE | WARNING | n | <variable> | : <bindvariable> ]

[ COMMIT | ROLLBACK ]
```

n 和\<variable>的返回值受限于操作系统，在不同平台下，会有所不同，例如：UNIX 系统只用一个字节来存 code，所以返回值的范围只在 0~255 之间。

示例如下：

```
SQL>whenever sqlerror exit 1
SQL>select c1 from dual;
select c1 from dual;
```

执行结果如下：

```
第1 行附近出现错误[-2111]:无效的列名[C1].
//windows系统下，输入echo %ERRORLEVEL%，查看返回值为：1
//linux系统下，输入echo $?，查看返回值为：1
```

## 4.8 查看下一个结果集 MORE

当结果集过多，屏幕只能显示一个时，用户可以使用 MORE 命令切换到下一个结果集。

```
MORE | MR
```

例如，当执行如下语句时，用户想查看更多的结果集，可以使用 MORE 命令。

```
begin

select top 10 * from v$dm_ini;

select top 10 * from sysobjects;

select * from dual;

end

/
```

## 4.9 显示 SQL 语句或块信息 LIST

显示最近执行的 SQL 语句或者 PL/SQL 块信息。不显示 DIsql 命令。

语法如下：

```
L[IST] [n | n m | n  * | n LAST | * | * n | * LAST | LAST] 或者;
```

n ,m ：数值 SQL 行号。

*： 当前行号。

LAST: 最后一行。

## 4.10 插入大字段数据文件

当插入语句中包含大字段数据文件时，使用 @。

```
@INSERT [INTO] <table_name>[(<col_name>{,< col_name >})] VALUES (<ins_value>{,<ins_value>}) ; 
<ins_value>=@'file_path' |
            <data>
```

\<ins_value> ：如果插入值为大字段数据文件，则使用 @'file_path'格式。如果插入值是常规数据，则正常插入即可。

示例如下：

例如，在 test 表中插入大字段数据文件 DSC_1663.jpg、DSC_1664.jpg、DSC_1665.jpg、DSC_1666.jpg。

```
create table test(a int,b image,c text);
@insert into test values(1,@'e:\DSC_1663.jpg',@'e:\DSC_1664.jpg'), (2,@'e:\DSC_1665.jpg',@'e:\DSC_1666.jpg');
```

## 4.11 缓存清理 CLEAR

清理指定操作本地缓存。

语法如下：

```
CL[EAR] <option>

<option> ::= [COL[UMNS] | SQL | SCR[EEN] | BUFF[ER]]
```

COL[UMNS]：清理所有的 COLUMN 变量信息。

SQL：清理本地 SQL 缓存信息。

SCR[EEN]：清理 DIsql 终端屏幕信息。

BUFF[ER]：同 SQL 功能一样，清理本地 SQL 缓存信息。

示例如下：

```
SQL> COLUMN CVAR NEW_VALUE DVAR

SQL> col

COLUMN CVAR ON

NEW_VALUE DVAR

SQL> cl col

columns 已清除

SQL> col

SQL>
```

## 4.12 SQL 追加命令 APPEND

追加内容到 SQL 缓存中当前行的末尾。

语法如下：

```
APPEND text
```

text：sql 语句。

示例如下：

```
SQL> APPEND SELECT * FROM DUAL;
```

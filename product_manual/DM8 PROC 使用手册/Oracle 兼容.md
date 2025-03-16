

# Oracle 兼容

DM PRO*C 对 Oracle PRO*C 的常用语法进行了兼容，在 dpc_new 预编译命令中将 MODE 参数置为 ORACLE 表示使用兼容 ORACLE 语法的编译模式。使用兼容 Oracle 模式时，在编译时还需要使用 sqlca_ora.h、sqlda_ora.h、dpc_ora_dll.h、oraca.h。

## 4.1 简单的 Oracle 嵌入式程序结构分析

首先，通过一个简单的 Oracle 嵌入式程序入手，对 Oracle 嵌入式程序的结构及基本语法进行分析。

例 以下名为 test_ora.pc 的嵌入式程序，完成对表 test_ora 进行全表及条件查询并打印相关信息的功能。

```
#include <string.h>
#include <stdlib.h>
//定义列和绑定变量的最大个数
#define MAX_ITEMS 40  
//定义列名的最大值
#define MAX_VNAME_LEN 30  
#define MAX_INAME_LEN 30   
int alloc_descriptor(int size,int max_vname_len,int max_iname_len);   
void set_bind_v();  
void set_select_v();
void free_da();   
void sql_error(char *msg);   
EXEC SQL INCLUDE SQLCA;   
EXEC SQL INCLUDE SQLDA;   
EXEC SQL INCLUDE ORACA;
//宿主变量定义：
EXEC SQL BEGIN DECLARE SECTION;   
char sql_statement[256]= "select * from test_ora";   
    	char type_statement[256]="select f1,f2 from test_ora where f1=1";   
    	int f1 = 1;
    	int i;
VARCHAR username[10];
VARCHAR password[10];
VARCHAR servername[50];
EXEC SQL END DECLARE SECTION;   
SQLDA *bind_p;   
	SQLDA *select_p;   
	int main()  
	{   
	strcpy(username.arr,"SYSDBA");
	username.len = strlen(username.arr);
	username.arr[username.len] = '\0';
	strcpy(password.arr,"*****");
	password.len = strlen(password.arr);
	password.arr[password.len] = '\0';
       strcpy(servername.arr,"127.0.0.1:16501");
       servername.len = strlen(servername.arr);
       servername.arr[servername.len] = '\0';
EXEC SQL CONNECT :username identified by :password using :servername;
    EXEC ORACLE OPTION (ORACA=YES);
    oraca.orastxtf=3;
	if(sqlca.sqlcode!=0)
	{
	printf("Fail! [%d] %s\n",sqlca.sqlcode,sqlca.sqlerrm.sqlerrmc);
	printf("ERROR SQL: %s\n",oraca.oratxt.orastxtc);
	    }else
        {
            printf("\n [OK Connected!] \n\n");
        }
	    EXEC SQL WHENEVER SQLERROR DO sql_error("<ERROR>");   
	    alloc_descriptor(MAX_ITEMS,MAX_VNAME_LEN,MAX_INAME_LEN);   
           EXEC SQL CREATE TABLE TEST_ORA(f1 INT, f2 INT); 
EXEC SQL 	INSERT INTO TEST_ORA VALUES(1,2);
           EXEC SQL  INSERT INTO TEST_ORA VALUES(1,4);
	    EXEC SQL PREPARE S from :type_statement;   
	    EXEC SQL DECLARE C1 CURSOR FOR S;  
	    set_bind_v();   
	    EXEC SQL OPEN C1 USING DESCRIPTOR bind_p;   
	    EXEC SQL DESCRIBE SELECT LIST for S INTO select_p;   
           set_select_v(); 
	    printf("f1\t\tf2\n");   
	    printf("----------------------------------------------------------\n");   
	    for(;;)   
	    {    EXEC SQL WHENEVER NOT FOUND DO break;   
	         EXEC SQL FETCH C1 USING DESCRIPTOR select_p;   
	         for(i = 0;i<select_p->F;i++){   
	         printf("%s ",select_p->V[i]);   
	        }   
	         printf("\n");   
	    }   
	    free_da();   
	    EXEC SQL CLOSE C1;   
	    printf("\n-----------------------------------------------------\n");   
	    alloc_descriptor(MAX_ITEMS,MAX_VNAME_LEN,MAX_INAME_LEN);   
	    EXEC SQL PREPARE S from :sql_statement;   
	    EXEC SQL DECLARE C CURSOR FOR S;  
	    set_bind_v();   
	    EXEC SQL OPEN C USING DESCRIPTOR bind_p;   
	    EXEC SQL DESCRIBE SELECT LIST for S INTO select_p;  
		set_select_v(); 
	    for(;;)   
	    {    EXEC SQL WHENEVER NOT FOUND DO break;  
	         EXEC SQL FETCH C USING DESCRIPTOR select_p;   
	         for(i = 0;i<select_p->F;i++)   
	            printf("%s ",select_p->V[i]);   
	         printf("\n");   
	    }   
	    free_da();   
	    EXEC SQL CLOSE C;  
		EXEC SQL DROP TABLE TEST_ORA;
	    EXEC SQL COMMIT WORK RELEASE;   
	    exit(0);   
	}   
//分配描述符空间：
int alloc_descriptor(int size,int max_vname_len,int max_iname_len)   
	{   
	    int i;
    if((bind_p=SQLSQLDAAlloc(0,size,max_vname_len,max_iname_len))==(SQLDA*)0)   
	    {   
	        printf("can't allocate memory for bind_p.");   
	        return -1;   
	    }      if((select_p=SQLSQLDAAlloc(0,size,max_vname_len,max_iname_len))==(SQLDA*)0)   
	    {   
	        printf("can't allocate memory for select_p.");   
	        return -1;   
	    }   
	    return 0;   
   }   
	//绑定变量的设置：
	void set_bind_v()   
	{   
	    int i;   
	    EXEC SQL WHENEVER SQLERROR DO sql_error("<ERROR>");   
	    bind_p ->N = MAX_ITEMS;   
	    EXEC SQL DESCRIBE BIND VARIABLES FOR S INTO bind_p;   
	    if(bind_p->F<0)   
	    {   
	        printf("Too Many bind varibles");   
	        return;   
	    }   
	    bind_p->N = bind_p->F;   
	    for(i=0;i<bind_p->N;i++)   
	    {   
	        bind_p->T[i] = 1;   
	    }   
	}   
//选择列处理
	void set_select_v()   
	{   
		    int i,null_ok,precision,scale;   
	     EXEC SQL DESCRIBE SELECT LIST for S INTO select_p;   
	     if(select_p->F<0)   
	    {   
	        printf("Too Many column varibles");   
	        return;   
	    }   
	    select_p->N = select_p->F;   
//对格式作处理
	    for(i = 0;i<select_p->N;i++)   
	    {   
//检查类型是否为空
	        sqlnul(&(select_p->T[i]), &(select_p->T[i]), &null_ok);
             switch (select_p->T[i])   
	        {   
	           case 1://VARCHAR2   
	                break;   
	           case 2://NUMBER   
	                sqlprc(&(select_p->L[i]), &precision, &scale);   
	                if (precision == 0)   
	                  precision = 40;   
	                select_p->L[i] = precision + 2;   
	                break;   
	           case 8://LONG   
	                 select_p->L[i] = 240;   
	                 break;   
	           case 11://ROWID   
	                 select_p->L[i] = 18;   
	                 break;   
	           case 12://DATE   
	                 select_p->L[i] = 9;   
	                 break;   
	           case 23://RAW   
	                 break;   
	           case 24://LONGRAW   
	                 select_p->L[i] = 240;   
                 break;   
        }   
	        select_p->V[i] = (char *)realloc(select_p->V[i], select_p->L[i]+1);   
	        select_p->V[i][select_p->L[i]] ='\0';//加上终止符
	        select_p->T[i] = 1;//把所有类型转换为字符型
	     }   
	}  
//释放内存SQLDA的函数：
	void free_da()   
	{  
	    SQLSQLDAFree(0,bind_p);   
	    SQLSQLDAFree(0,select_p);   
  }   
//错误处理
	void sql_error(char *msg)   
	{   
	    printf("\n%s %s\n", msg,(char *)sqlca.sqlerrm.sqlerrmc);   
	    exit(0);   
	}  
```

由此可见，Oracle 嵌入式程序通常由以下部分组成：

  1. **SQLDA 申请与释放**



```
int alloc_descriptor(int size,int max_vname_len,int max_iname_len);

void free_da();
```

详见 4.2 SQLDA/SQLCA。

  2. **SQLDA、SQLCA、ORACA 引用**



```
EXEC SQL INCLUDE SQLCA;

EXEC SQL INCLUDE SQLDA;

EXEC SQL INCLUDE ORACA; 
```

详见 4.2 SQLDA/SQLCA/ORACA。

  3. **宿主变量定义**



```
//宿主变量定义：

EXEC SQL BEGIN DECLARE SECTION;

char sql_statement[256]= "select * from test_ora";

char type_statement[256]="select f1,f2 from test_ora where f1=1";

int f1 = 1;

int i;

VARCHAR username[10];

VARCHAR password[10];

EXEC SQL END DECLARE SECTION;
```

详见 3.2 宿主变量定义。

  4. **数据库登录**



```
EXEC SQL CONNECT :username identified by :password;
```

  5. **ORACA 启用**



```
EXEC ORACLE OPTION (ORACA=YES);
oraca.orastxtf=3;
```

  6. **数据库操作**



登录成功之后，便可对数据库进行各种操作。

  7. **异常处理**



详见 3.3.5 嵌入式程序中的异常处理。

## 4.2SQLDA/SQLCA/ORACA

SQLDA、SQLCA、ORACA 是 Oracle 用于记录 PRO*C 程序在执行过程中的描述信息及诊断信息。为了实现与 Oracle 的兼容，DM 在 dpc_dll 中对其进行实现，保留了 SQLDA、SQLCA、ORACA 各成员变量在 Oracle 中的原始含义，并分别用于 Oracle PRO*C 程序在 DM 上执行的描述及诊断信息的记录。

SQLDA 本身并不存在，用户需先申请 SQLDA 描述空间才可使用，且使用完毕后，需进行释放。如有必要，用户可申请多个 SQLDA，方便使用。

SQLDA、SQLCA 的相关声明与定义不会在预编译时自动引入，用户需在 PRO*C 文件的头部手动引入，代码如下所示：

```
EXEC SQL INCLUDE SQLCA;
EXEC SQL INCLUDE SQLDA;
```

ORACA 的相关头文件不会在预编译时自动引入，用户需要在 PROC*C 文件的头部手动引入，有两种方法可以引入。

方法一：头文件引入

```
#include <oraca.h>;  
```

方法二：INCLUDE 引入

```
EXEC SQL INCLUDE SQLDA;
```

ORACA 的启用需要用户在 PROC*C 文件的手动打开，有两种方法可以启用。

方法一：命令行启用

```
ORACA=YES 
```

方法二：预编译命令 option

```
EXEC ORACLE OPTION(ORACA=YES);
```

启用 ORACA 后，需要手动指定 oraca.orastxtf 成员的值。取值为 0 表示不要保存 SQL 语句文本，默认取 0；取值为 1 表示仅保存出现 ERR 的 SQL 语句文本；取值为 2 表示保存出现 ERR 或 WARN 的 SQL 语句文本；取值为 3 表示始终保存 SQL 语句文本。

以下详细介绍 DM 实现的 SQLDA、SQLCA 的相关操作函数。

  1. **SQLDA 空间的申请**



函数定义：

```
1) SQLDA*

	sqlald(

            int max_n,

            size_t max_name,

            size_t max_i

        );

2) SQLDA *

	SQLSQLDAAlloc(

        void *context,

        int max_n,

        size_t max_name,

        size_t max_i

    );
```

参数说明：

|**参数名称**|**含义说明**|
|----|----|
|context|运行时上下文指针，DM 中作 0 处理|
|max_n|变量的最大个数|
|max_name|变量名称的最大长度|
|max_i|指示器(indicator)名称的最大长度|


  2. **SQLDA 空间的释放**



函数定义：

```
1) void

	sqlclu(

			SQLDA* da

		);

2) void

	SQLSQLDAFree(

			void* context,

			SQLDA* da

		);
```

参数说明：

|**参数名称**|**含义说明**|
|----|----|
|context|运行时上下文指针，DM 中作 0 处理|
|da|待释放的 SQLDA 变量名|


  3. **Precision 和 Scale 抽取**



函数定义：

```
1) void

	sqlprc(

			udint4* length,

			sdint4* precision,

			sdint4* scale

		);

2) void

	SQLNumberPrecV6(

			void* context,

			udint4* length,

			sdint4* precision,

			sdint4* scale

		);
```

参数说明：

|**参数名称**|**含义说明**|
|----|----|
|context|运行时上下文指针，DM 中作 0 处理|
|length|长整型变量指针，指向存放 Oracle NUMBER 类型值的长度;长度存储在 L[i]， scale 和 precision 分别存放在低字节与高字节|
|precision|整型变量指针，指向返回 Oracle NUMBER 类型值的 precision 整型变量|
|scale|整型变量指针，指向返回 Oracle NUMBER 类型值的 scale 整型变量|


  4. **NULL/NOT NULL 数据类型处理**



函数定义：

```
1) void

	sqlnul(

		udint2* value_type,

		udint2* type_code,

		sdint4* null_status

	);

2) void

	SQLColumnNullCheck(

		void* context,

		udint2* value_type,

		udint2* type_code,

		sdint4* null_status

	);
```

参数说明：

|**参数名称**|**含义说明**|
|----|----|
|context|运行时上下文指针，DM 中作 0 处理|
|value_type|无符号 short 整型变量指针，指向 select-list 列数据类型码； select-list 列数据类型存储在 T[i]中|
|type_code|无符号 short 整型变量指针，指向返回 select-list 列数据类型码的变量， 返回值中高位被清空|
|null_status|整型变量指针，指向返回的 select-list 列的 null 状态；1 代表允许为 null， 0 代表不允许为 null|


  5. **错误信息获取**



函数定义：

```
1) void

		sqlglm(

		char* message_buffer,

		size_t* buffer_size,

		size_t* message_length

	);

2) void

	SQLErrorGetText(

		void* context,

		char* message_buffer,

		size_t* buffer_size,

		size_t* message_length

	);
```

参数说明：

|**参数名称**|**含义说明**|
|----|----|
|context|运行时上下文指针，DM 中作 0 处理|
|message_buffer|指向存放错误信息缓存区|
|buffer_size|指向存放缓存区大小变量的指针|
|message_length|指向返回错误信息最大长度变量的指针|


## 4.3 可执行的 SQL 语句

DM 与 Oracle 在登录数据库、基本 SQL 语句语法的支持方面存在差异，因此，为方便用户使用，DM 对以下 Oracle 语法实现支持，其他可执行语句详见 3.3 节描述。

  1. **数据库登录语句**



语法如下：

```
EXEC SQL CONNECT :username IDENTIFIED BY :pwd [AT [dbname | :dbname]] [USING :servername] [ALTER AUTHORIZATION :new_pwd];
```

功能：

用于数据库的登录。当指定了 ALTER AUTHORIZATION :new_pwd 子句时，表示使用该语句登录数据库后，将用户的口令改为 new_pwd，再使用新口令登录数据库。

使用说明：

  * 语句中 username 是长度最多为 128 个字符的指针，其值为注册用户的名字；
  * pwd 表示相应的用户口令，口令的长度最多为 128 个字符；
  * dbname 用于指定连接名，为长度最大为 128 个字符的标识符或宿主变量。在使用多连接时给每个连接命名（不命名时系统会自动给一个默认连接名），在执行其他嵌入 SQL 时通过指定连接名可以切换到不同连接进行操作；
  * servername 为服务器的名字或 IP 地址，可选，名字的长度最多为 128 个字符；
  * new_pwd 为新的用户口令，口令长度最多为 128 个字符。



> **注意**
>
> 在用户程序中，登录语句的逻辑位置必须先于所有可执行的 SQL语句，只有执行登录语句之后，才能执行其它可执行的
> SQL语句。如果登录失败，则终止程序的运行。



  2. **基本 SQL 语句**



语法如下：

```
EXEC SQL [AT [dbname | :dbname]] FOR :array_size INSERT/UPDATE /DELETE-CLAUSE…;
```

或者

```
EXEC SQL [AT [dbname | :dbname]] FOR integer INSERT/UPDATE /DELETE-CLAUSE…;
```

功能：

用于执行指定次数的 INSERT/UPDATE/DELETE 操作。

使用说明：

  * dbname：指明 SQL 执行使用的连接名；
  * array_size 为整型变量；
  * integer 为整型数字；



当插入操作的 VALUES-语句包含宿主变量数组；或当更新操作的 SET-语句和 WHERE-语句包含宿主变量数组；或删除操作的 WHERE-语句包含宿主变量数组，且:array_size、integer 指示的值不大于宿主变量数组的最小长度时，:array_size、integer 代表后面语句的执行次数。其他情况为非法的执行语句，该类错误在 DM 预编译过程中不做处理。

具体使用示例可参见 6.5.5 节。

## 4.4 预编译命令 OPTION

语法如下：

```
EXEC ORACLE OPTION (:option = :option_value);
```

：

功能：

兼容 Oracle 的 OPTION 预编译命令。

使用说明：

  * option 表示预编译选项，目前只支持 CHAR_MAP 和 ORACA；
  * option_value 表示预编译选项的值，目前支持的 CHAR_MAP 选项的值为 STRING、CHARZ 和 CHARF，支持的 ORACA 选项的值为 YES 和 NO。



例

```
CREATE TABLEchar_test (c1 int,c2 varchar(100));

INSERT INTO char_test VALUES(1,'abcde');

COMMIT;
```

下面的程序片段说明了 CHAR_MAP 取值 STRING、CHARZ 和 CHARF 时执行结果的不同。

```
……
char ch_array[5];
short ind;
strncpy(ch_array, "12345", 5);
EXEC ORACLE OPTION (char_map=charz);
EXEC SQL SELECT c2 INTO :ch_array:ind FROM char_test WHERE c1=1 ;
//结果为ch_array = { 'A', 'B', 'c', 'd', '\0' }，ind=5

strncpy (ch_array, "12345", 5);
EXEC ORACLE OPTION (char_map=string) ;
EXEC SQL SELECT c2 INTO :ch_array:ind FROM char_test WHERE c1=1 ;
//结果为ch_array = { 'A', 'B', 'c', 'd', '\0' }，ind=5

strncpy( ch_array, "12345", 5);
EXEC ORACLE OPTION (char_map=charf);
EXEC SQL SELECT c2 INTO :ch_array:ind FROM char_test WHERE c1=1 ;
//结果为ch_array = { 'A', 'B', 'c', 'd', 'e' }，ind=0 
……
```

## 4.5 数据类型映射

指定 Oracle 兼容的预编译模式时，数据类型的映射如下表所示。

表4.1 Oracle兼容数据类型映射

|**数据类型**|**DM_TYPE**|**ORACLE_TYPE 兼容**|**ANSI 标准类型**|
|--------------|--------------|--------------|--------------|
|DSQL_CHAR|1|96|1|
|DSQL_VARCHAR|2|1|12|
|DSQL_BIT|3|2|14|
|DSQL_TINYINT|5|2|-1|
|DSQL_SMALLINT|6|2|5|
|DSQL_INT|7|2|4|
|DSQL_BIGINT|8|2|-2|
|DSQL_DEC|9|2|3|
|DSQL_FLOAT|10|2|6|
|DSQL_DOUBLE|11|2|8|
|DSQL_BLOB|12|113|不支持|
|DSQL_DATE|14|12|9|
|DSQL_TIME|15|187|9|
|DSQL_TIMESTAMP|16|187|9|
|DSQL_BINARY|17|23|-3|
|DSQL_VARBINARY|18|23|-3|
|DSQL_CLOB|19|112|-4|
|DSQL_TIME_TZ|22|186|9|
|DSQL_TIMESTAMP_TZ|23|188|9|
|DSQL_INTERVAL_YEAR|100|189|10|
|DSQL_INTERVAL_MONTH|101|189|10|
|DSQL_INTERVAL_DAY|102|190|10|
|DSQL_INTERVAL_HOUR|103|190|10|
|DSQL_INTERVAL_MINUTE|104|190|10|
|DSQL_INTERVAL_SECOND|105|190|10|
|DSQL_INTERVAL_YEAR_TO_MONTH|106|189|10|
|DSQL_INTERVAL_DAY_TO_HOUR|107|190|10|
|DSQL_INTERVAL_DAY_TO_MINUTE|108|190|10|
|DSQL_INTERVAL_DAY_TO_SECOND|109|190|10|
|DSQL_INTERVAL_HOUR_TO_MINUTE|110|190|10|
|DSQL_INTERVAL_HOUR_TO_SECOND|111|190|10|
|DSQL_INTERVAL_MINUTE_TO_SECOND|112|190|10|


## 4.6 表声明语句

语法如下：

```
EXEC SQL DECLARE table_name TABLE (col_name col_datetype [DEFAULT expr] [NULL | NOT NULL] [, ...]);
```

功能：

兼容 Oracle 的 DECLARE TABLE 预编译命令，声明表结构，暂时仅语法支持，不提供实际功能。

使用说明：

  * table_name 表示表名；
  * col_name 表示列名；
  * col_datetype 表示列类型；
  * expr 表示默认值的表达式。



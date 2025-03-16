

# DIsql 环境变量设置

使用 SET 命令可以对当前 DIsql 的环境变量进行设置。并通过 SHOW 命令来查看当前系统中环境变量的设置情况。也可以通过配置文件使 Disql 启动时自动设置一批环境变量。

## 3.1 DIsql 环境变量

DIsql 中的系统环境变量，汇总如下：

表3.1 DIsql命令的快速参考

|**序号**|**变量名称**|**属性**|**用途**|
|----|----|----|----|
|1|AUTO[COMMIT]|\<ON|OFF (缺省值)>|设置自动提交|
|2|DEFINE|\<c(默认的变量前缀是&)|ON (缺省值)|OFF>|定义本地变量|
|3|ECHO|\<ON (缺省值)|OFF>|显示脚本中正在执行的 SQL 语句|
|4|FEED[BACK]|\<6 (缺省值)|n|ON|OFF>|显示当前 SQL 语句查询或修改的行数|
|5|HEA[DING]|\<ON (缺省值)|OFF>|显示列标题|
|6|LINESHOW|\<ON (缺省值)|OFF>|显示行号|
|7|NEWP[AGE]|\<1 (缺省值)|n|NONE>|设置页与页之间的分隔|
|8|PAGES[IZE]|\<14 (缺省值)|n>|设置一页有多少行数|
|9|TIMING|\<ON (缺省值)|OFF>|显示每个 SQL 语句花费的执行时间|
|10|TIME|\<ON|OFF (缺省值)>|显示系统的当前时间|
|11|VER[IFY]|\<ON (缺省值)|OFF>|列出环境变量被替换前、 后的控制命令文本|
|12|LONG|\<800 (缺省值)|n>|设置大字段类型显示的最大字节数|
|13|LINESIZE|\<screem_length (缺省值，屏幕宽度)|n>|设置屏幕上一行显示宽度|
|14|SERVEROUT[PUT]|\<ON | OFF (缺省值)> [SIZE \<20000 (缺省值)|n>] [FOR[MAT] \<WRA[PPED] | WOR[D_WRAPPED] (缺省值) | TRU[NCATED]>]|在块中有打印信息时， 是否打印，以及打印的格式|
|15|SCREENBUFSIZE|\<DEFAULT(20M) | n>|设置屏幕缓冲区的长度|
|16|CHAR_CODE|\<GBK | GB18030 | UTF8 | DEFAULT (缺省值，操作系统的编码方式)>|设置 SQL 语句的编码方式|
|17|LOCAL_CODE|\<GBK| GB18030 | UTF8 | DEFAULT (缺省值，操作系统的编码方式)>|设置本地编码方式|
|18|CURSOR|\<STATIC | FORWARDONLY (缺省值)| DEFAULT>|设置 DPI 语句句柄中游标的类型|
|19|AUTOTRACE|\<OFF (缺省值) | NL | INDEX | ON | TRACE | TRACEONLY>|设置执行计划和统计信息的跟踪|
|20|DESCRIBE|[DEPTH\<1 (缺省值) | n | ALL>] [LINE[NUM] \<ON | OFF(默认值)>] [INDENT \<ON (缺省值) | OFF>]|设置 DESCRIBE 的显示方式|
|21|TRIMS[POOL]|\<OFF (缺省值)| ON>|设置 spool 文件中每行的结尾空格|
|22|LOBCOMPLETE|\<OFF (缺省值)| ON>|设置大字段数据是否从服务器全部取出|
|23|COLSEP|[text]|设置列之间的分割符。缺省为一个空格|
|24|KEEPDATA|\<ON|OFF (缺省值)>|是否为数据对齐进行优化，或者保持数据的原始格式。ON 不优化，OFF 对齐优化。缺省为 OFF|
|25|AUTORECONN|\<ON (缺省值)|OFF>|是否自动重新连接。ON 是，OFF 否。缺省为 ON|
|26|NEST_COMMENT|\<ON|OFF (缺省值)>|是否支持多行注释嵌套。ON 是，OFF 否。缺省为 OFF|
|27|NULL_ASNULL|\<ON|OFF (缺省值)>|在绑定参数输入时，是否将输入的 NULL 当作数据库的 null 处理。ON 是，OFF 否。缺省为 OFF|
|28|CMD_EXEC|\<ON (缺省值)|OFF>|是否执行文件中“/”命令。ON 是，OFF 否。缺省为 ON|
|29|CHARDEL|[text]|设置字符串的限定符。缺省为一个空格|
|30|FLOAT_SHOW|\<0 (缺省值)|float_length>|设置 FLOAT、DOUBLE 类型按科学计数法显示的分界长度。缺省为 0，代表全部按科学计数法显示|
|31|CONSOLE_PRINT|\<ON (缺省值)|OFF|OFF_WITH_TIME |OFF_WITH_FEEDBACK>|控制台是否打印查询结果。ON 是，OFF 否。缺省为 ON|
|32|SQLCODE|\<ON|OFF (缺省值)>|控制台是否打印 SQLCODE 返回值。ON 是，OFF 否。缺省为 OFF|
|33|SQL_LINESHOW|\<ON (缺省值)|OFF>|输入多行 SQL 语句时，是否打印 SQL 语句的行号。ON 是，OFF 否。缺省为 ON|
|34|NULL_SHOW|\<ON (缺省值)|OFF>|空数据是否显示为 NULL。ON 是，OFF 否。缺省为 ON|
|35|SQLPROMPT|*\<*text>|设置 DIsql 的命令行前缀标识|
|36|ISQL_MODE|\<0 (缺省值)|1|2|3>|结果集打印方式。0：DM 数据库的打印方式；1：兼容 ORACLE 的打印格式；2：兼容 Greenplum 的打印格式；3：兼容 MYSQL 的打印格式。缺省为 0|
|37|WRAP|\<ON (缺省值)|OFF>|是否折行打印结果集。ON 是，OFF 否。缺省为 ON仅在 ISQL_MODE 为 1 时，该参数有效|
|38|CTRL_INFO|\<0 (缺省值)|1|2|4|8|16>|设置 DIsql 控制信息，本变量支持多个值组合使用，例如 7 同时控制 1、2、4 生效|
|39|RETRY_CONN|\<0 (缺省值)|n>|设置 DIsql 尝试自动重连的次数|
|40|RETRY_CONN_TIME|\<0 (缺省值)|n>|用于当 RETRY_CONN 为 n 时，设置 DIsql 自动重连时间间隔。单位：秒|
|41|ROWS|\<0 (缺省值)|n>|设置结果集最大显示行数|
|42|EXPLAIN_MODE|\< 0|1|2 >|设置 SQL 执行方式|
|43|PREFIX_STR|\< 0|text >|设置 SQL 执行时拼接指定前缀串|
|44|READ_ONLY|\< ON|OFF(缺省值)>|设置会话只读|


## 3.2 SET 命令用法

SET 命令用于设置 DIsql 系统环境变量。

语法如下：

```
SET <system_variable><value>{ <system_variable><value>}
```

`\<system_variable\>`：变量名称，参考 3.1 节。

`\<value\>`：属性。

可以同时 SET 多个环境变量，如：Set heading on timing on。一旦 SET 之后某个环境变量出错，那么该变量之后的将不再起作用。

## 3.3 用 SET 命令设置环境变量详解

本节详细介绍如何使用 SET 命令对环境变量进行设置。

### 3.3.1 AUTO[COMMIT]

设置当前 session 是否对修改的数据进行自动提交。

语法如下：

```
SET AUTO[COMMIT] <ON|OFF>
```

ON：表示打开自动提交，所有执行的 SQL 语句的事务将自动进行提交。

OFF：表示关闭自动提交，所有执行的 SQL 语句的事务将由用户显式提交，为默认设置。

### 3.3.2 DEFINE

是否使用 DEFINE 定义本地变量。

语法如下：

```
SET DEFINE<c(默认的变量前缀是&)|ON（缺省值）|OFF>
```

c：表示打开 DEFINE 功能，同时定义前缀变量符号，c 为定义的前缀符号。

ON：表示打开 DEFINE 功能，使用默认前缀符号&。

OFF：表示不使用 DEFINE 功能。

示例如下

打开 DEFINE 功能，并设置##为变量前缀。

```
SQL> SET DEFINE #
SQL> SELECT #A FROM DUAL;
输入 A的值:1
```

执行结果如下：

```
原值 1:SELECT #A FROM DUAL;
新值 1:SELECT 1 FROM DUAL;
行号       1
---------- -----------
1          1
已用时间: 0.178(毫秒). 执行号:722.
```

### 3.3.3 ECHO

在用 START 命令执行一个 SQL 脚本时，是否显示脚本中正在执行的 SQL 语句。

语法如下：

```
SET ECHO <ON（缺省值）|OFF>
```

### 3.3.4 FEED[BACK]

是否显示当前 SQL 语句查询或修改的总行数。

语法如下：

```
SET FEED[BACK] <6（缺省值）|n|ON|OFF>
```

n：表示结果大于 n 行时，才显示结果的总行数。

ON：打开显示开关，使用缺省值 6。

OFF：关闭显示开关。

示例如下：

![FEED[BACK]用法](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图3.1-FEED[BACK]用法.png)

图3.1 FEED[BACK]用法

### 3.3.5 HEA[DING]

是否显示列标题。

语法如下：

```
SET HEA[DING] <ON（缺省值）|OFF>
```

当 set heading off 时，在每页的上面不显示列标题，而是以空白行代替。

示例如下：

![HEA[DING]用法](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图3.2-HEA[DING]用法.png)

图3.2 HEA[DING]用法

### 3.3.6 LINESHOW

lineshow 设置是否显示行号。

语法如下：

```
SET LINESHOW<ON（缺省值）|OFF >;
```

默认为每行输出打印行号。

示例如下：

![LINESHOW 用法](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图3.3-LINESHOW用法.png)

图3.3 LINESHOW 用法

### 3.3.7 NEWP[AGE]

设置页与页之间的分隔。

语法如下：

```
SET NEWP[AGE] <1（缺省值）|n|NONE>
```

当 set newpage 0 时，在每页的开头有一个换号符。

当 set newpage n 时，在页和页之间隔着 n 个空行。

当 set newpage none 时，在页和页之间没有任何间隔。

### 3.3.8 PAGES[IZE]

设置一页有多少行数。

语法如下：

```
SET PAGES[IZE] <14（缺省值）|n>
```

如果设为 0，则所有的输出内容为一页，并且当 ISQL_MODE 不等于 2 时，不显示列标题。缺省值为 14。

当 ISQL_MODE = 1 时，如果 PAGESIZE \< NEWPAGE + 2，则不显示列标题。

当 ISQL_MODE = 2 时，PAGESIZE 取任意值，都显示列标题。

### 3.3.9 TIMING

显示每个 SQL 语句花费的执行时间。

语法如下：

```
SET TIMING<ON（缺省值）|OFF>
```

### 3.3.10 TIME

显示系统的当前时间。

语法如下：

```
SET TIME<ON|OFF（缺省值）>
```

### 3.3.11 VER[IFY]

VER[IFY]是否列出环境变量被替换前、后的控制命令文本。缺省值为 ON，表示列出命令文本。

语法如下：

```
SET VER[IFY] < ON（缺省值）|OFF>
```

示例如下：

例 1 设置 VER[IFY]为 OFF，不列出命令文本。

```
SQL> SET VERIFY OFF
SQL> SELECT &A FROM DUAL;
输入 A的值:3
```

执行结果如下：

```
行号        3
---------- -----------
1          3
已用时间: 0.558(毫秒). 执行号:733.
```

例 2 设置 VER[IFY]为 ON，列出命令文本。

```
SQL> SET VERIFY ON
SQL> SELECT &A FROM DUAL;
输入 A的值:3
```

执行结果如下：

```
原值 1:SELECT &A FROM DUAL;
新值 1:SELECT 3 FROM DUAL;
行号        3
---------- -----------
1          3
已用时间: 0.127(毫秒). 执行号:734.
```

### 3.3.12 LONG

设置 BLOB、CLOB、CHAR、VARCHAR、BINARY、VARBINARY、CLASS 等类型一列能显示的最大字节数。

语法如下：

```
SET LONG <800(缺省值)|n>
```

示例如下：

```
SQL> CREATE TABLE TEST(C1 TEXT);
SQL> INSERT INTO TEST VALUES('DIsql是DM数据库的一个命令行客户端工具，用来与DM数据库服务器进行交互。');
```

例 1 设置能显示的最大字节数为 10。

```
SQL> SET LONG 10
SQL> SELECT * FROM TEST;
```

执行结果如下：

```
警告: 字符串截断
行号     C1  
---------- ---------
1          DIsql是DM
已用时间: 0.237(毫秒). 执行号:55109.
```

例 2 设置能显示的最大字节数为 100。

```
SQL> SET LONG 100
SQL> SELECT * FROM TEST;
```

执行结果如下：

```
行号     C1   
---------- ------------------------------------------------------------------
1          DIsql是DM数据库的一个命令行客户端工具，用来与DM数据库服务器进行交互。
已用时间: 0.230(毫秒). 执行号:55110.
```

### 3.3.13 LINESIZE

设置屏幕上一行显示宽度。

语法如下：

```
SET LINESIZE <screen_length(缺省值，屏幕宽度)|n>
```

当 ISQL_MODE 取值为 0 或 1 时，LINESIZE 取值范围为自 1 至 32767 的整数。

当 ISQL_MODE 取值为 2 时，LINESIZE 取值范围为自 0 至 32767 的整数，取值为 0 时，表示一行数据在同一行显示。

### 3.3.14 SERVEROUT[PUT]

在块中有打印信息时，是否打印，以及打印的格式。设置之后，可以使用 DBMS_OUTPUT 包打印(认为 DBMS_OUTPUT 包已经创建)。

语法如下：

```
SET SERVEROUT[PUT] <ON | OFF(缺省值)> [SIZE <20000(缺省值)|n>]
[FOR[MAT] <WRA[PPED] | WOR[D_WRAPPED](缺省值) | TRU[NCATED]>]
```

ON/OFF：是否打印。

SIZE：打印的最大长度。

WORD_WRAPPED：按照单词分隔。

TRUNCATED：单词被截断。

FORMAT：按照服务器返回的显示，不做格式化。

示例如下：

对比两个结果，不难发现，第二个结果中的单词被截断。

例 1 设置为按照单词分隔，显示宽度为 20。

```
SQL> SET SERVEROUTPUT ON FORMAT WORD_WRAPPED
SQL> SET LINESIZE 20
SQL>BEGIN
	DBMS_OUTPUT.PUT_LINE('If there is nothing left to do');
	DBMS_OUTPUT.PUT_LINE('shall we continue with plan B?');
	END;  
/
```

执行结果如下：

```
If there is nothing
left to do
shall we continue with
plan B?
PL/SQL 过程已成功完成
已用时间: 1.140(毫秒). 执行号:729.
```

例 2 设置为单词被截断，显示宽度为 20。

```
SQL> SET SERVEROUTPUT ON FORMAT TRUNCATED
SQL> SET LINESIZE 20
SQL>BEGIN
2        DBMS_OUTPUT.PUT_LINE('If there is nothing left to do');
3        DBMS_OUTPUT.PUT_LINE('shall we continue with plan B?');
4      END;
5      /
```

执行结果如下：

```
If there is nothing
shall we continue wi
PL/SQL 过程已成功完成
已用时间: 0.330(毫秒). 执行号:731.
```

### 3.3.15 SCREENBUFSIZE

设置屏幕缓冲区的长度。用来存储屏幕上显示的内容。单位为字节。

语法如下：

```
SET SCREENBUFSIZE<DEFAULT(20M) | n>
```

n：有效值范围 1~50M。

### 3.3.16 CHAR_CODE

设置 SQL 语句的编码方式。

语法如下：

```
SET CHAR_CODE <GBK | GB18030 | UTF8 | DEFAULT(缺省值，操作系统的编码方式)>
```

### 3.3.17 LOCAL_CODE

设置本地编码方式，指定客户端使用的编码格式，会影响结果集和错误信息的编码。

设置该参数时会先检查当前是否连接了数据库，若未连接数据库则设置无效，重连数据库后恢复设置为指定的本地编码方式。

语法如下：

```
SET LOCAL_CODE <GBK | GB18030 | UTF8 | DEFAULT(缺省值，操作系统的编码方式)>
```

### 3.3.18 CURSOR

设置 DPI 语句句柄中游标的类型。

语法如下：

```
SET CURSOR <STATIC | FORWARDONLY (缺省值)| DEFAULT>
```

### 3.3.19 AUTOTRACE

设置执行计划和统计信息的跟踪。

语法如下：

```
SET AUTOTRACE <OFF(缺省值) | NL | INDEX | ON | TRACE | TRACEONLY>
```

当 SET AUTOTRACE OFF 时，停止 AUTOTRACE 功能，常规执行语句。

当 SET AUTOTRACE NL 时，开启 AUTOTRACE 功能，不执行语句，如果执行计划中有嵌套循环操作，那么打印 NEST LOOP 相关操作符的内容。

当 SET AUTOTRACE INDEX(或者 ON)时，开启 AUTOTRACE 功能，不执行语句，如果有表扫描，那么打印执行计划中表扫描的方式、表名和索引。

当 SET AUTOTRACE TRACE 时，开启 AUTOTRACE 功能，执行语句，打印执行计划，并展示执行过程中的部分监控信息；需要设置 INI 中监控参数 ENABLE_MONITOR、MONITOR_SQL_EXEC、ENABLE_MONITOR_DMSQL 均为开启（即等于 1 时）才有实际意义。此功能与服务器 EXPLAIN 语句的区别在于，EXPLAIN 只生成执行计划，并不会真正执行 SQL 语句，因此产生的执行计划有可能不准；而通过 TRACE 获得的执行计划，是服务器实际执行的计划（可能是重用了计划缓存中计划，也可能是新生成的计划）。

当 SET AUTOTRACE TRACEONLY 时，开启 AUTOTRACE 功能，执行语句，打印执行计划，并展示执行过程中的部分监控信息；需要设置 INI 参数 ENABLE_MONITOR、MONITOR_SQL_EXEC、ENABLE_MONITOR_DMSQL 均为开启（即等于 1 时）才有实际意义。此功能与 TRACE 区别在于对于查询语句集不打印结果集。

示例如下：

设置 INI 参数 ENABLE_MONITOR=1、MONITOR_SQL_EXEC=1、ENABLE_MONITOR_DMSQL=1，设置为开启 AUTOTRACE TRACE。执行语句后会展示执行结果，打印执行计划，展示执行过中的部分监控信息，其中包括更改的数据页数、重做页数、向客户端传送的数据字节、从客户端接收的数据字节、执行时间等信息。

示例中监控信息项的具体含义如下：

  * data pages changed : 更改的数据页数
  * undo pages changed : 更改的 UNDO 日志页数
  * logical reads : 逻辑读次数
  * physical reads : 物理读次数
  * redo size : REDO 日志大小
  * bytes sent to client : 发给客户端的数据流量
  * bytes received from client : 客户端接收的数据流量
  * roundtrips to/from client : 客户端与服务器交互次数
  * sorts (memory) : 最优排序次数
  * sorts (disk) : 单路/多路归并排序次数
  * rows processed : DML 操作影响的行数
  * io wait time(ms) : I/O 等待时间
  * exec time(ms) : 执行时间



```
SQL> SET AUTOTRACE TRACE;
SQL> SELECT COUNT(*) FROM SYSOBJECTS;

//执行结果如下：
COUNT(*)
--------------------
1299

1   #NSET2: [1, 1->1, 0]
2     #PRJT2: [1, 1->1, 0]; exp_num(1), is_atom(FALSE)
3       #FAGR2: [1, 1, 0]; sfun_num(1),

Statistics
-----------------------------------------------------------------
        0           data pages changed
        0           undo pages changed
        32          logical reads
        0           physical reads
        0           redo size
        138         bytes sent to client
        98          bytes received from client
        1           roundtrips to/from client
        0           sorts (memory)
        0           sorts (disk)
        1           rows processed
        0           io wait time(ms)
        3           exec time(ms)
```

### 3.3.20 DESCRIBE

设置 DESCRIBE 对象结构信息的显示方式。

语法如下：

```
SET DESCRIBE [DEPTH <1(缺省值) | n | ALL>] [LINE[NUM] <ON | OFF(缺省值)>] [INDENT <ON(缺省值) | OFF>]
```

DEPTH：结构信息显示至第 N 层，默认只显示第 1 层。取值范围是 1~40。当设置为 ALL 时，DEPTH 为 40。

LINENUM：是否显示对象行号，成员显示父亲的行号。

INDENT：当对象的类型是复合类型时，是否通过缩进的方式显示成员信息。

### 3.3.21 TRIMS[POOL]

设置 TRIMS[POOL]，和 SPOOL 功能结合使用。

语法如下：

```
SET TRIMS[POOL] <OFF(缺省值) | ON>
```

对于 SPOOL 文件，是否去除输出每行的结尾空格，缺省为 OFF。

### 3.3.22 LOBCOMPLETE

设置 LOBCOMPLETE，是否从服务器中全部取出大字段数据。

语法如下：

```
SET LOBCOMPLETE <OFF(缺省值) | ON>
```

对于大字段数据，是否从服务器全部取出，防止死锁的发生；与显示长度不同，即便是全部取出，也可以只显示一部分。

### 3.3.23 COLSEP

设置列之间的分割符。

语法如下：

```
SET COLSEP[text]
```

如果 text 包含空格或标点符号，请用单引号扩起来。默认为一个空格。

### 3.3.24 KEEPDATA

是否为数据对齐进行优化，或者保持数据的原始格式。

语法如下：

```
SET KEEPDATA <ON|OFF(缺省值)>
```

OFF：表示为保证数据的对齐格式，DIsql 对服务器传回的字符串数据，将其中的换行符、TAB 都转换为空格。缺省为 OFF。

ON：表示关闭对齐优化。

### 3.3.25 AUTORECONN

是否进行自动重新连接。

语法如下：

```
SET AUTORECONN <ON(缺省值) | OFF>
```

是否进行自动重新连接，设置为 ON 时，使用上次连接的属性设置进行自动重连，缺省为 ON。

### 3.3.26 NEST_COMMENT

是否支持多层注释嵌套。Disql 中支持注释，注释必须由起始符号“/*”开始，由结束符号“*/”结束。注释内容还可以嵌套其他的注释，嵌套的注释也同样需由“/*”开始和“*/”结束。

语法如下：

```
SET NEST_COMMENT <ON|OFF(缺省值)>
```

ON：是，支持多层注释。

OFF：否，只支持一层注释。缺省为 OFF。

示例如下：

当 SETNEST_COMMENT ON 时，打开支持多层注释的嵌套。下面这个例子就无法停止，因为有两个注释起始符号，只有一个注释结束符号。所以程序一直处于输入状态。当 SET NEST_COMMENT OFF 时，只支持一行注释，程序只要找到一组/*和*/就会结束。

```
SQL>SET NEST_COMMENT OFF
/************** abcdfdddef
/*********
*/ 
CREATE TABLE TEST(C1 INT);
```

### 3.3.27 NULL_ASNULL

在绑定参数输入时，是否将输入的 NULL 当做数据库的 null 处理。ON 是，OFF 否。缺省为 OFF。

语法如下：

```
SET NULL_ASNULL<ON|OFF(缺省值)>
```

ON：是。

OFF：否。缺省为 OFF。

示例如下：

```
drop table tm;
create table tm(c1 int,c2 varchar);
insert into tm values(null,'a');
commit;
```

例 1 设置为不将输入的 NULL 当做数据库的 null 处理。

```
SET NULL_ASNULL OFF
insert into tm values(?,?);
请输入参数1的值:null
请输入参数2的值:null
insert into tm values(?,?);
[-70011]:无效的转换字符串.
```

例 2 设置为将输入的 NULL 当做数据库的 null 处理。

```
SET NULL_ASNULL ON
insert into tm values(?,?);
请输入参数1的值:null
请输入参数2的值:null
影响行数 1
```

### 3.3.28 CMD_EXEC

是否执行 sql 脚本文件中“/”命令，ON 是，OFF 否。缺省为 ON。

语法如下：

```
SET CMD_EXEC <ON (缺省值)|OFF>
```

ON：是。缺省为 ON。

OFF：否。

示例如下：

test.sql 脚本位于 d:\目录下。test.sql 内容如下：

```
select 1;
/
Commit;
```

例 1 缺省 CMD_EXEC 为 ON 的情况。

```
SQL> `d:\test.sql
SQL> select 1;
```

执行结果如下：

```
行号        1
---------- -----------
1          1
已用时间: 0.842(毫秒). 执行号:1664.

SQL> /
```

执行结果如下：

```
行号        1
---------- -----------
1          1
已用时间: 0.832(毫秒). 执行号:1665.

SQL> commit;
操作已执行
已用时间: 1.061(毫秒). 执行号:1666.
```

例 2 修改 CMD_EXEC 为 OFF。

```
SQL>SET CMD_EXEC OFF
SQL> `d:\test.sql
SQL> select 1;
```

执行结果如下：

```
行号        1
---------- -----------
1          1
已用时间: 0.866(毫秒). 执行号:1667.

SQL> commit;
操作已执行
已用时间: 0.800(毫秒). 执行号:1668.
```

### 3.3.29 CHARDEL

设置字符串的限定符。

语法如下：

```
SET CHARDEL [text]
```

如果 text 包含空格或标点符号，则需要用单引号扩起来。默认为一个空格。

示例如下：

例 1 设置字符串限定符为 $。

```
SQL> SET CHARDEL $
SQL> SELECT 'aa';
```

执行结果如下：

```
行号        'aa'
---------- ----
1		   $aa$
```

例 2 设置字符串限定符为"。

```
SQL> SET CHARDEL '"';
SQL> SELECT 'aa';
```

执行结果如下：

```
行号        'aa'
---------- ----
1		   "aa"
```

### 3.3.30 FLOAT_SHOW

设置 FLOAT、DOUBLE 类型数据按科学计数法显示的分界长度。

语法如下：

```
SET FLOAT_SHOW <0(缺省值)| float_length>
```

当数据直接打印长度超过 float_length 时以科学计数法显示，否则直接显示。float_length 取值范围为 0~350，为 0 代表总是以科学计数法显示。

示例如下：

```
SQL> DROP TABLE T1;
SQL> CREATE TABLE T1(X FLOAT);
SQL> INSERT INTO T1 VALUES(11.00011);
SQL> COMMIT;
```

例 1 设置数据按科学计数法显示的分界长度为 50。

```
SQL> SET FLOAT_SHOW 50
SQL> SELECT * FROM T1;
```

执行结果如下：

```
行号        X  
---------- ----------
1          11.000110
```

例 2 设置为总是以科学计数法显示数据。

```
SQL> SET FLOAT_SHOW 0
SQL> SELECT * FROM T1;
```

执行结果如下：

```
行号        X  
---------- ----------------------
1          1.100011000000000E+01
```

### 3.3.31 CONSOLE_PRINT

控制台是否打印查询结果和执行时间，ON 是，OFF 否。缺省为 ON。

语法如下：

```
SET CONSOLE_PRINT <ON(缺省值)|OFF |OFF_WITH_TIME>
```

ON：是，打印执行结果和执行时间。缺省为 ON。

OFF：否，既不打印执行结果，也不打印执行时间。

OFF_WITH_TIME：否，不打印执行结果但是打印执行时间。

OFF_WITH_FEEDBACK：否，只打印执行时间和影响行数。

示例如下：

例 1 缺省 CONSOLE_PRINT 为 ON，打印查询结果。

```
SQL> select 1;
```

执行结果如下：

```
行号       1
---------- -----------
1          1
已用时间: 0.440(毫秒). 执行号:1664.
```

例 2 修改 CONSOLE_PRINT 为 OFF，不打印查询结果。

```
SQL> set console_print off
SQL> select 1;
```

### 3.3.32 SQLCODE

控制台是否打印 SQLCODE 返回值，ON 是，OFF 否。缺省为 OFF。SQLCODE 返回 0 表示当前 SQL 语句执行成功，SQLCODE 返回 1 表示当前 SQL 语句执行失败。

语法如下：

```
SET SQLCODE <ON|OFF(缺省值)>
```

ON：是。

OFF：否。缺省为 OFF。

示例如下：

例 1 缺省 SQLCODE 为 OFF，控制台不打印 SQLCODE 返回值。

```
SQL> select 1;
```

执行结果如下：

```
行号       1
---------- -----------
1          1
已用时间: 2.136(毫秒). 执行号:700.
```

例 2 修改 SQLCODE 为 ON，控制台打印 SQLCODE 返回值。

```
SQL> set sqlcode on
SQL> select 1;
```

执行结果如下：

```
行号       1
--------- -----------
1          1
已用时间: 0.402(毫秒). 执行号:701.
SQLCODE=0: SQL statement executed successfully
```

### 3.3.33 SQL_LINESHOW

输入多行 SQL 语句时，是否打印 SQL 语句的行号，ON 是，OFF 否。缺省为 ON。

语法如下：

```
SET SQL_LINESHOW <ON(缺省值)|OFF>
```

ON：是。缺省为 ON。

OFF：否。

示例如下：

例 1 缺省 SQL_LINESHOW 为 ON，打印 SQL 语句的行号。

```
SQL> select
2   'a'
3   ;
```

执行结果如下：

```
行号       'a'
---------- ---
1          a
```

例 2 修改 SQL_LINESHOW 为 OFF，不打印 SQL 语句的行号。

```
SQL> set sql_lineshow off
SQL> select
'a'
;
```

执行结果如下：

```
行号       'a'
---------- ---
1          a
```

### 3.3.34 NULL_SHOW

空数据是否显示为 NULL，ON 是，OFF 否。缺省为 ON。

语法如下：

```
SET NULL_SHOW <ON(缺省值)|OFF>
```

ON：是。缺省为 ON。

OFF：否。

示例如下：

例 1 缺省 NULL_SHOW 为 ON，设置空数据显示为 NULL。

```
SQL> select null;
```

执行结果如下：

```
行号       NULL
---------- ----
1          NULL
```

例 2 修改 NULL_SHOW 为 OFF，设置空数据不显示为 NULL。

```
SQL> set null_show off
SQL> select null;
```

执行结果如下：

```
行号       NULL
---------- ----
1
```

### 3.3.35 SQLPROMPT

设置 DIsql 命令行的前缀标识

语法如下：

```
SET SQLPROMPT <text>
```

*\<text> 为任意的字符串。\<*text> 中可以包含 DEFINE 变量名。缺省则 SQL> 为前缀。其中 *_*USER 和_connect_identifier 两个系统变量代表“系统用户名”和“IP 地址：端口号”。

示例如下：

```
SQL> set SQLPROMPT "_USER'@'_connect_identifier>"
[SYSDBA@LOCALHOST:5236](mailto:SYSDBA@192.168.1.11)>
[SYSDBA@LOCALHOST:5236](mailto:SYSDBA@192.168.1.11)> set SQLPROMPT "GYF-COMPUTER>"
GYF-COMPUTER>
```

### 3.3.36 ISQL_MODE

结果集打印方式。

语法如下：

```
SET ISQL_MODE <0(缺省值)|1|2>
```

0：DM 数据库的打印方式。缺省为 0。

1：兼容 ORACLE 的打印格式。

2：兼容 Greenplum 的打印格式。

3：兼容 MYSQL 的打印格式，支持在一行 SQL 语句末尾将“\G”或“\g”作为分号使用。其中“\g”和分号等价；“\G”表示结果集按列打印，可以将每个字段打印到单独的行。若一行 SQL 语句由多个单独的查询语句组成，只能识别此行 SQL 语句末尾的“\g”或“\G”，中间的 SQL 要以分号分割；且多个结果集的显示格式相同，由最后一条 SQL 语句的末尾是否使用“\G”决定。

### 3.3.37 WRAP

是否折行打印结果集，ON 是，OFF 否。缺省为 ON。仅在 ISQL_MODE 为 1 时，该参数有效。

语法如下：

```
SET WRAP <ON(缺省值)|OFF>
```

ON：是。缺省为 ON。

OFF：否。

示例如下：

设置 ISQL_MODE 为 1，LINESIZE 为 10，准备数据如下。

```
SQL> set isql_mode 1
SQL> set linesize 10
SQL> drop table test;
SQL> create table test(c1 varchar(50), c2 varchar(50));
SQL> insert into test values('damengshujuku', 'damengshujuku');
SQL> commit;
```

例 1 缺省 WRAP 为 ON。查询结果根据设置的 LINESIZE 进行折行显示。

```
SQL> select * from test;
```

执行结果如下：

```
行号     C1   
---------- ----------
           C2  
		  ----------
1          damengshuj
           uku   
           damengshuj   
           uku   
```

例 2 设置 WRAP 为 OFF。查询结果不折行显示，若实际数据长度超过设置的 LINESIZE，则将打印结果截断。

```
SQL> set wrap off
SQL> select * from test;
```

执行结果如下：

```
行号     C1  
---------- ----------
1          damengshuj

行被截断
```

### 3.3.38 CTRL_INFO

设置 DIsql 的回显打印信息。

语法如下：

```
SET CTRL_INFO <0(缺省值)|1|2|4|8|16>
```

0：没有特殊设置。缺省为 0。

1：控制-E 不强制关闭行号、执行时间以及执行号等信息的展示。

2：控制 DIsql 的回显信息按 5 列打印，具体如下：

  * 第 1 列：语句的 SQL 类型，如：DELETE、UPDATE、SELECT 等；
  * 第 2 列：语句的执行结果，执行成功则为 0，执行失败则为错误码；
  * 第 3 列：语句的影响行数，DDL 和 PLsql 类型语句的影响行数均默认为 0，仅 DELETE、 UPDATE、SELECT 类型语句存在具体的影响行数；
  * 第 4 列：语句的执行时间；
  * 第 5 列：语句执行失败时的错误信息，若语句执行成功，则不打印该列。



设置为 2 时，不再打印执行过程中的报错信息。

4：控制语句块多个结果集全部自动显示，不需要通过 more 命令显示结果集。

8：控制执行脚本文件时，不忽略 SQL 之间的空行。

16：控制遇到 SQL 结束符反斜杠不检查单引号是否匹配。

支持使用上述有效值的组合值，如 7 表示同时控制 1、2 和 4。

示例如下：

例 1 使用-E 命令直接执行 SQL 语句，不显示行号、执行时间以及执行号等信息

```
./disql SYSDBA/*****@192.168.100.163:5254 -E "SELECT * FROM TEST;"
```

执行结果如下：

```
服务器[192.168.100.163:5254]:处于普通打开状态
登录使用时间 : 2.635(ms)
disql V8
 
C1 
---
ABC
```

例 2  利用-C 命令设置 CTRL_INFO 为 1，控制-E 不强制关闭行号、执行时间以及执行号等信息的展示。

```
./disql SYSDBA/*****@192.168.100.163:5254 -C "SET CTRL_INFO 1" -E "SELECT * FROM TEST;"
```

执行结果如下：

```
服务器[192.168.100.163:5254]:处于普通打开状态
登录使用时间 : 2.447(ms)
disql V8

行号   C1 
---------- ---
1     ABC

已用时间: 0.702(毫秒). 执行号:56100.
```

例 3  设置 CTRL_INFO 为 2，控制 DIsql 的回显信息按 5 列打印。

```
SQL> SET CTRL_INFO 2
SQL> SELECT * FROM TEST;
```

执行结果如下：

```
行号   C1 
---------- ---
1     ABC
SELECT 0 1 0.925 
 
SQL> SELECT * FROM TEST22;
SELECT -2106 0 0.604 第1行附近出现错误: 无效的表或视图名[TEST22] 
```

### 3.3.39 RETRY_CONN

设置 DIsql 尝试自动重连的次数。该环境变量主要用于写自动化脚本，当连接失败导致 SQL 语句执行失败时，DIsql 尝试自动重新连接数据库，如果在设置次数内连接成功，则重新执行相应 SQL 语句。需要注意的是，该环境变量与 AUTORECONN 并无关系。

语法如下：

```
SET RETRY_CONN <0(缺省值)|n>
```

0：不自动重连。缺省为 0。

n：自动重连的次数。

示例如下：

例 设置 DIsql 尝试自动重连的次数为 1000，如果 DIsql 在 1000 次内连接服务器成功，则重新执行相应 SQL 语句。

```
./disql SYSDBA/*****@192.168.100.163:5254 -C "SET RETRY_CONN 1000" -E "select 1;"
[-70019]:网络通讯失败.
disql V8
连接丢失
连接丢失
连接丢失


服务器[192.168.100.163:5254]:处于普通打开状态
已连接

1
-----------
1
```

### 3.3.40 RETRY_CONN_TIME

用于当 RETRY_CONN 为 n 时，设置 DIsql 自动重连的时间间隔。

语法如下：

```
SET RETRY_CONN_TIME <0(缺省值)|n>
```

0：缺省值，无时间间隔进行重连。

n：间隔 n 秒重连一次。

示例如下：

例 设置 DIsql 尝试自动重连的次数为 20，如果 DIsql 在 20 次内连接服务器成功，则重新执行相应 SQL 语句。自动重连时间间隔为 10s。

```
./disql SYSDBA/***** -C "SET RETRY_CONN 20 RETRY_CONN_TIME 10" -E " select 1;"
[-70028]: 创建SOCKET连接失败.
disql V8
连接丢失
连接丢失
连接丢失 

服务器[LOCALHOST:5236]:处于普通打开状态
已连接
1
-----------
1
```

### 3.3.41 ROWS

设置结果集最大显示行数。

语法如下：

```
SET ROWS <0(缺省值)|n>
```

结果集最多显示 n 行，设置为 0 表示不限制结果集最大显示行数。

### 3.3.42 EXPLAIN_MODE

设置 SQL 执行方式。

按照 EXPLAIN_MODE 为 1 和 2 执行时，和 EXPLAIN_MODE 为 0 有两点不同：一 spool 不会记录 sql 执行错误信息；二不支持 @ 开头插入大对象数据文件的语句。

语法如下：

```
SET EXPLAIN_MODE < 0|1|2 >
```

0：缺省值。正常执行 SQL 语句。

1：按照 EXPLAIN FOR 方式执行，即在 SQL 语句前自动拼接上 EXPLAIN FOR 之后再执行。

2：按 EXPLAIN 方式执行，即在 SQL 语句前自动拼接上 EXPLAIN 之后再执行。

示例如下：

例 按照 EXPLAIN_MODE 为 2 的方式执行。本例子的效果相当于直接执行 EXPLAIN SELECT 1;

```
SQL> set EXPLAIN_MODE 2
SQL> SELECT 1;

1  #NSET2: [1, 1, 1]
2   #PRJT2: [1, 1, 1]; exp_num(1), is_atom(FALSE)
3    #CSCN2: [1, 1, 1]; SYSINDEXSYSDUAL(SYSDUAL); btr_scan(1)
```

### 3.3.43 PREFIX_STR

设置 SQL 语句拼接前缀，在待执行的 SQL 语句前拼上指定字符再发给服务器执行。执行完成后，在 SQL 日志文件中记录的为带前缀的 SQL 语句。此参数的目的是为了让用户能够在日志中准确识别出具有同一前缀的 SQL 语句，进行日志分析。

语法如下：

```
SET PREFIX_STR < 0| text >
```

0：缺省值，不加前缀。

text：SQL 语句的拼接前缀。前缀必须是注释的形式：/xxx/。其他形式将报错。

示例如下：

例 在 SQL 语言前拼接上前缀**/My code/**。

```
SQL>SET PREFIX_STR '/*My code*/' 

SQL>select * from sysobjects;
```

执行完成后，在 SQL 日志文件中记录的为带前缀的 SQL 语句。

```
/*My code*/ select * from sysobjects;
```

### 3.3.44 READ_ONLY

设置会话只读，缺省为 OFF。

语法如下：

```
SET READ_ONLY <ON|OFF(缺省值) >
```

ON：是，当前会话只能只读。

OFF：否，会话能正常读写。

## 3.4 SHOW 命令查看环境变量

通过使用 SHOW 命令，用户就可以快速而方便的了解到 DIsql 环境的当前环境变量设置和初始化参数。

SHOW 可以显示一个或多个变量。显示多个变量时中间加空格，当其中某一变量出错之后，后面的仍会继续显示。

### 3.4.1 显示当前环境变量

显示指定的环境变量。

语法如下：

```
SHOW <system_variable>{<system_variable>}
```

\<system_variable>:环境变量。

示例如下：

例 显示 HEADING 和 TIMING 两个变量：

```
SQL> SHOW HEADING TIMING
HEADING ON.
TIMING ON
```

### 3.4.2 显示初始化参数

显示所有包含指定字符串的初始化参数。

语法如下：

```
SHOW PARAMETER[S] [<parameter_name>]
```

\<parameter_name>：包含在初始化参数名中的字符串。\<parameter_name> 为空、单引号或双引号时，则显示所有初始化参数。若 \<parameter_name> 为多个字符串，字符串之间可用空格隔开，只有第一个字符串生效，后面的字符串会被忽略。

示例如下：

例 显示包含 SORT 的初始化参数。

```
SQL> SHOW PARAMETERS SORT

行号    PARA_NAME      PARA_VALUE

---------- -------------------- ----------

1     SORT_BUF_SIZE    20

2     SORT_BLK_SIZE    1

3     SORT_BUF_GLOBAL_SIZE 1000

4     SORT_FLAG      1

5     SORT_OPT_SIZE    0

6     TSORT_OPT      1

7     BASE_SORT_CPU    36000

8     LARGE_SORT_CPU    40000

9     SMALL_SORT_CPU    200

10     NLS_SORT_TYPE    0

11     SORT_ADAPTIVE_FLAG  1
```

## 3.5 使用配置文件设置环境变量

DIsql 在连接成功数据库时会自动运行两个配置文件 glogin.sql 和 login.sql。glogin.sql 文件中的设置永久生效，该配置文件需要用户自行创建在​DM_HOME/bin/disql_conf路径下，其中DM_HOME 为 DM 的安装目录，需要用户在操作系统配置 DM_HOME 环境变量，disql_conf 目录需要用户自行创建。login.sql 文件为用户自定义配置文件，对其存放路径不做限制。

glogin.sql 文件和 login.sql 文件的执行顺序如下：

  1. 默认在 $DM_HOME/bin/disql_conf 路径下查找 glogin.sql 文件并执行；
  2. 默认在当前工作目录下（注意不是 DIsql 工具所在的目录，而是启动 DIsql 时所在的目录）查找 login.sql 文件并执行，若未找到则执行步骤 3；
  3. 判断操作系统是否配置了 DM_SQLPATH 环境变量，如果配置了该变量则在对应路径下查找 login.sql 文件并执行。



DIsql 执行上述配置文件时并不会在 DIsql 窗口打印信息，如果没有找到上述配置文件则忽略。

例如，用户配置 DM_HOME 环境变量后，在 $DM_HOME/bin/disql_conf 目录下创建 glogin.sql 文件，内容如下：

```
SET FEEDBACK ON
SET TIMING ON
```

另外，用户在当前工作目录下创建了 login.sql 文件，内容如下：

```
SET CTRL_INFO 2
```

DIsql 启动并连接数据库服务器后，上述两个配置文件中的设置立即生效。

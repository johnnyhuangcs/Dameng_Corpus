

# dmfldr 实战

在上一章简单了解了 dmfldr 的各参数含义后，本章介绍如何通过设置不同参数值使用 dmfldr 进行不同情况的数据装载。并介绍了如何通过设置参数提高 dmfldr 的性能以及使用限制。

> **注意**
>
> 本章中皆以在Linux操作系统下进行操作为例，在Windows操作 系统下目录分隔符与单引号转义的处理有些许区别，不再赘述。



## 3.1 dmfldr 控制文件

控制文件 CONTROL 是启动 dmfldr 必须要指定的参数，用于指定数据文件中数据的格式。在数据载入时，dmfldr 根据控制文件指定的格式来解析数据文件；导出数据时，dmfldr 也会根据控制文件指定的列分隔符、行分隔符等生成数据文件。

控制文件中还可以指定其他 dmfldr 参数值。

dmfldr 控制文件的语法如下所示：

```
[OPTIONS(
<id>=<value>
……
		)]
LOAD [DATA]
INFILE < <file_option>|<directory_option> >
[BADFILE <path_name>]
[APPEND|REPLACE|INSERT]
<into_table_clause>
<id> ::=参数
<value> ::=值
<file_option> ::= [LIST] <path_name> [<row_term_option>] [,<path_name>
[<row_term_option>]]
<directory_option> ::= DIRECTORY <path_name> [<row_term_option>]
<path_name> ::=文件地址
<row_term_option> ::=STR [X] <delimiter>
<into_table_clause> ::= <into_table_single>{<into_table_single>}
<into_table_single> ::=INTO TABLE [<schema>.]<tablename>
						[EP <ep_option>]
						[WHEN <field_conditions>]
						[FIELDS [TERMINATED BY] [X] <delimiter>]
						[<enclosed_option>]
						[TRAILING NULLCOLS]
						[<coldef_option>]
<schema> ::=模式名
<tablename> ::=表名
<ep_option> ::=(<ep_list>)
<ep_list> ::=整型数字列表，以逗号分隔
<field_conditions> ::= <field_condition>{ AND <field_condition>}
<field_condition> ::= [(] <cmp_exp><cmp_ops><cmp_data>[)]
<cmp_exp> ::= <colid> | (p1:p2)
<cmp_ops> ::= = | <> | !=
<cmp_data> ::= [X] '<字符串常量>' | BLANKS | WHITESPACE
<delimiter> ::= '<字符串常量>'
<coldef_option> ::= (<col_def>{ ,<col_def>})
<col_def> ::= <col_id> [FILLER][<property_option>][<dtype_option>][<fmt_option>][<term_option>][<enclosed_option>][<constant_option>][<fun_option>]
<col_id> ::= 列名
<property_option> ::= <position_option> | NULL
<position_option> ::= position(p1:p2) | position(p1) | position(*)
<dtype_option> ::= DATE | CHAR
<fmt_option> ::= FORMAT '<日期格式>'
<term_option> ::= TERMINATED [BY] <wx_option>
<wx_option> ::= WHITESPACE|[X] <delimiter>
<enclosed_option> ::= [OPTIONALLY] ENCLOSE [BY] [X] <delimiter>
<constant_option> ::= CONSTANT "<常量>"
<fun_option> ::= "<函数>"
```

对于上述控制文件语法，需要说明的是：

  * dmfldr 在处理数据文件中换行符时 windows 默认为 0x0D0A(\r\n)，非 windows 默认为 0x0A(\n)，用户应该根据现有的数据文件中的换行符做相应的调整。对应选项为\<row_term_option>，若指定的\<value>值为十六进制的字符串值需要指明[X]选项，\<value>值不再需要以 0x 开头。若没有指明[X]选项，则\<value>值为指定的字符串；

  * 关于列分隔符，用户应当指定 FIELDS 或者 coldef_option 中的至少一种。若两者均存在，则以 coldef_option 中的设置为准，若分隔符指明[X]选项，则表明此分隔符为十六进制格式的字符串；

  * 关于 file_option，用来指定单个文件；

  * 关于 directory_option，用来指定整个文件夹。指定此选项后，dmfldr 会自动扫描指定文件夹下的所有文件，这些文件数据将被导入到服务器；

  * 关于 LIST 选项，INFILE 使用 LIST 选项时，表明实际的数据文件路径存储在 INFILE 指定的文件中，该文件可以存储多个实际的数据文件路径，使用逗号或者换行分割；

  * 关于 APPEND|REPLACE|INSERT 选项，当 dmfldr 处于数据装载模式时，INSERT 表示插入方式，向空表插入新记录（如果不是空表则会报错无效的装载模式）；APPEND 表示追加方式，为缺省方式，在表中追加新记录；REPLACE 表示替代方式，先清空表再插入新记录。

当 dmfldr 处于导出数据模式时，dmfldr 会检查导出数据文件是否存在，若不存在则直接创建新文件；若存在，当设置为 APPEND
时，以追加的方式写入数据；设置为 REPLACE 时，先删除文件再重新创建新文件；设置为其他值时则报错。

选项默认值为 APPEND；

  * 关于 OPTIONS 选项，该选项支持命令行参数中除 userid，control，help 以外的所有参数的指定，每个参数值对使用空格或者换行分割。对于 option 中出现的参数，在 dmfldr 的指定执行参数中也出现的，dmfldr 会选择 option 中对应参数的值执行；

  * 关于 TRAILING NULLCOLS，该选项表示若数据文件中未提供列数据，则该列的值设为 NULL；

  * 关于 col_def，FILLER 表示跳过处理数据文件中指定列的值；

  * 关于 property_option 选项

✔position(p1:p2)：从数据文件中每行数据的第 p1 个字节到第 p2 个字节为该列值，包含边界 p1，p2；

✔position(p1)：从数据文件中每行数据的第 p1 个字节开始，到下一个列分隔符之间的数据为该列值，包含边界 p1；

✔position(*)：根据数据文件中前一列数据的结束位置确定当前列的起始位置，根据 dtype_option
选项确定当前列的结束位置。若当前列为第一列，则起始位置为第 1 个字节，若未指定 dtype_option 选项，则默认为 CHAR(1)；

✔position 选项对大字段数据无效，若对大字段类型指定此选项会报错；

✔NULL：指定的值为 NULL，忽略数据文件中的值；

✔ 某一列使用了 position，后续的列都要使用 position；

✔property_option 参数仅对导入有效。

  * 关于 dtype_option 选项，用来指定列的数据类型，仅当指定 position_option 选项时有效，目前仅支持 DATE 和 CHAR 两种类型，缺省为 CHAR；

  * 关于 fmt_option 选项，用来指定日期格式，仅当 dtype_option 选项指定为 DATE 时有效。更多关于 \< 日期格式 > 的介绍请参考《DM8_SQL 语言使用手册》中函数 TO_DATE/TO_TIMESTAMP/TO_TIMESTAMP_TZ；

  * 关于 term_option 选项，该选项用来指定数据文件中指定列的结束标志。列的结束标志可以是 WHITESPACE（空格）或者用户自定义的字符串或十六进制串。指定了 term_option 后，该列不需要用 FIELDS 分隔；

  * 关于 enclosed_option 选项，此参数指定封闭符，为可选参数，默认不存在封闭符。若在 into_table_clause 和 coldef_option 中均设置了封闭符，则以 coldef_option 中的设置为准，若封闭符前指定[X]选项，则表明此封闭符为十六进制格式的字符串。若数据列设置了封闭符，则将跳过封闭符取出数据；

  * 分隔符或封闭符字符串的长度均不能超过 255 个字节；

  * 关于 constant_option 选项，指定 constant 关键字后，数据文件中不需要为该列准备数据，如果指定了，该列数据将作为下一字段数据装载而导致数据混乱。constant 选项对大字段类型无效；

  * 关于 fun_option 选项，目前在使用 direct=true 的场景下，只支持 trim()、replace(colname, srcStr, destStr)、sysdate、sequence.NEXTVAL、substr(colname, pos, len)、substrb(colname, pos, len)和 default，该选项在控制文件中的总长不能超过 128，超过将无法识别。trim()函数用于去除列数据的前后空格；replace()函数用于将 colname 列名指定的列数据中的 srcStr 替换为 destStr，其中 srcStr 和 destStr 参数可使用 chr(int)函数将数字转换成字符串，要求 chr()的参数和返回值均不超过 4 个字节；sysdate 函数用于将系统当前时间插入指定列，忽略数据文件中指定列的值；sequence.NEXTVAL 用于将序列的 NEXTVAL 作为列数据，其中 sequence 为实际序列名，用户可在 sequence 前增加模式名，例如“sch1.seq1.NEXTVAL”表示使用 sch1 模式下的 seq1 序列的 NEXTVAL 作为列数据；substr()、substrb()函数用于将 colname 列名指定的列数据从 pos 开始取 len 长度，作为当前列的数据，substr()具体实现与系统函数 substr 相同，substrb()与系统函数 substrb 相同；default 函数用于将列默认值插入指定列，使用 default 时，无论数据文件的内容是什么，均会填充默认值到表的列；

在使用 direct=false 的场景下，支持使用参数类型正确的自定义列函数，且允许自定义列函数进行嵌套；

  * 关于 ep_option 选项，用于指定数据将要发送的目的站点，仅适用于 MPP 环境。



例 1 展示一个完整的 dmfldr 控制文件。

```
OPTIONS
(
	SKIP = 0
	ROWS = 50000
	DIRECT = TRUE
	INDEX_OPTION = 2
)
LOAD DATA
INFILE '/opt/data/test1.txt' STR X '0A'
BADFILE '/opt/data/test1.bad'
INTO TABLE test1
FIELDS '|'
(
F1,
F2 DATE FORMAT 'YYYY-MM-DD',
F3 NULL,
F4 TERMINATED BY WHITESPACE ENCLOSE BY '(',
F5 CONSTANT "test",
F6 "trim()"
)

LOAD DATA
INFILE '/opt/data/test2.txt' STR X '0A'
BADFILE '/opt/data/test2.bad'
INTO TABLE test2
FIELDS '|'
(
C1 TERMINATED BY ' ',
C2,
C3 "sysdate",
C4 "substr(:C2,1,2)"
)

```

在这个例子中：

  * OPTIONS 选项中定义了 SKIP、ROWS、DIRECT 和 INDEX_OPTION 参数
  * 有两个 LOAD 选项，表明有一次装载同时处理两个表，在每个 LOAD 选项中： 
    * 指定了数据文件和数据文件的换行符，还指定了 BADFILE 文件
    * 指定了操作的数据库基表、列分隔符以及列定义



例 2  当 direct=false 时，在 dmfldr 控制文件中使用自定义列函数。下例中的 F1、F2、F3 均为自定义列函数，由用户自行定义。

```
LOAD DATA
INFILE 't1.dta'
INTO TABLE T1
FIELDS '|'
(
C1 "F2(:C1, F1(F1(:C2)), :C3)",
C2 ,
C3 "F3(F1(:C2), F1(:C3), F2(:C1, F1(:c2), :C3))"
) 
```

### 3.2 指定数据文件

指定数据文件的方式有三种：一是在控制文件中指定；二是通过命令行参数 DATA 直接指定；三是使用 dmfldr.ini 指定。使用其中一种方式指定即可。

当 dmfldr 工作在 IN 模式时，从数据文件中读取数据并载入数据库；当工作在 OUT 模式时，从数据库中将指定数据导出到数据文件。

数据文件通常为文本文件，列与列之间由列分隔符隔开，行与行之间由行分隔符隔开。数据文件中的列分隔符和行分隔符由用户指定，并在控制文件中设置为与数据文件中的一致。

#### 3.2.1 在控制文件中指定数据文件

可以在控制文件的 LOAD 节点中指定数据文件。

例 展示如何使用控制文件进行载入。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 INT,C3 DATE);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1 1|2015-11-06

2 2|2015-11-05

3 3|2015-11_04
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1 TERMINATED BY ' ',

C2,

C3 DATE FORMAT 'yyyy-mm-dd'

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
```

#### 3.2.2 使用 DATA 参数指定数据文件

也可以使用 DATA 参数指定 dmfldr 的数据文件。用户可以在命令行中直接指定 DATA 参数，也可以在 dmfldr.ini 配置文件中指定 DATA 参数。如果控制文件中数据文件路径指定为‘**’*，那么会使用命令行或 dmfldr.ini 配置文件中指定的 DATA 参数来替换‘*’，优先使用命令行中指定的 DATA 参数进行替换。

例 展示如何使用 DATA 参数指定数据文件进行载入。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 INT,C3 DATE);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1 1|2015-11-06

2 2|2015-11-05

3 3|2015-11_04
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE *

INTO TABLE test

FIELDS '|'

(

C1 TERMINATED BY ' ',

C2,

C3 DATE FORMAT 'yyyy-mm-dd'

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\' data=\'/opt/data/test.txt \'
```

### 3.3 数据转换与错误数据文件

dmfldr 使用的数据文件都是文本格式的，其中的列值都是以字符串的方式保存在数据文件中。要想将这些数据载入数据库表中，需要将字符串转换成数据库表各列对应的数据类型。dmfldr 支持所有 DM 数据库支持的列定义类型，包括字符串、数值、时间日期、时间日期间隔、大字段类型等。

若数据文件的编码方式与 DM 数据库服务器的编码方式不一样，dmfldr 还需要进行字符编码的转换。dmfldr 支持 UTF-8、GBK 和 GB18030 编码之间的相互转换。

数据类型和编码转换工作由 dmfldr 客户端进行，在这个过程中如果出现错误，dmfldr 会跳过该行继续后面的工作，并记录错误行到 BADFILE 指定的文件。常见的出错的情况有以下几种：

  * 编码转换失败
  * 目标列为字符串类型时，数据长度大于列定义长度
  * 目标列为数值类型时，数据包含非法字符或者转换后超出该数值的范围
  * 目标列为日期类型时，dmfldr 默认按 yyyy-mm-dd hh:mi:ss 的格式解析，如果数据不是这样的格式，需要指定对应列的时间日期 fmt 格式



dmfldr 错误数据的文件路径由 BADFILE 参数设置，默认的错误文件名为 fldr.bad。用户也可以通过设置控制文件中的 OPTIONS 选项来指定错误数据文件的路径，同时也可以在控制文件的 LOAD 节点中指定错误数据文件的路径。错误数据文件路径最终值的优先选择顺序为命令行中指定的参数值，控制文件的 LOAD 节点选项，控制文件的 OPTIONS 选项，dmfldr.ini 配置文件中指定的参数值，用户可以同时对四种设置方式中的一个或多个设置，但最终的值只取一个。BADFILE 仅作用于 dmfldr 的工作 MODE 为 IN 的情况下，MODE 为 OUT 时无效。

当数据类型和编码转换中存在错误数据，而错误数在允许的最大错误数范围内时，dmfldr 会将出错的数据记录到错误数据文件中。文件记录的信息为执行程序、时间、目标表、数据文件中存在格式错误的行数据以及转换出错的行数据。

允许的最大容错个数由 ERRORS 选项设置，取值范围为 `[0，4294967295]` 之间的整数，默认为 100。当 dmfldr 客户端在数据类型和编码转换过程中出现的错误个数超过了 ERRORS 所设置的数目，dmfldr 会停止载入，当前时间点的所有正确数据将会被提交到服务器端。如果载入过程中不允许出现错误则可以将 ERRORS 设置为 0；如果允许所有的错误出现，则可以将 ERRORS 设置为一个非常大的数。

例 展示错误数据文件的用法。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 INT,C3 DATE);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1 1|2015-11-06

2 2|2015-11-05

3 3|2015-11_04

44|aaaa-bbb-ccc
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1 TERMINATED BY ' ',

C2,

C3 DATE FORMAT 'yyyy-mm-dd'

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
badfile=\'/opt/data/test.bad\'
```

  5. 查看错误数据文件/opt/data/test.bad，其内容如下：



```
dmfldr: 2015-11-09 16:56:52 SYSDBA->TEST 4|4 aaaa-bbb-ccc
```

### 3.4 服务器端错误数据处理

dmfldr 客户端将载入的数据进行数据转换和编码转换后，将转换正确的数据发往 DM 服务器的 dmfldr 模块，也就是 dmfldr 的服务器端，由其进行真正的数据载入工作。

dmfldr 客户端每次向服务器端发送一批数据，在服务器端插入数据的过程中，由于目的表上可能存在约束等原因，导致某些数据无法插入成功，此时服务器端会将这一批数据全部回滚，并将这批数据全部记为错误数据，但服务器端插入时的错误数据并不会记录到 BADFILE 中。

ERRORS 所统计的错误包含在数据转换和数据插入过程中所产生的数据错误，因此当服务器端插入数据记录的错误数据数加上客户端数据转换时的错误数据数超过 ERRORS 参数的指定值时，dmfldr 服务器会停止插入数据。

### 3.5 大字段数据处理

dmfldr 支持对 DM 数据库的大字段类型数据的载入和导出，DM 数据库支持的大字段数据类型有 TEXT、LONGVARCHAR、IMAGE、LONGVARBINARY、BLOB 以及 CLOB。

#### 3.5.1 大字段数据的导出

当 dmfldr 工作在导出模式即 MODE 为 OUT 时，dmfldr 生成大字段对应的数据文件名由 LOB_FILE_NAME 指定，若未指定默认为 dmfldr.lob，文件存放于 LOB_DIRECTORY 指定的目录，如果未指定 LOB_DIRECTORY 则存放于指定的导出数据文件同一目录。

例 展示大字段数据的导出。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 BLOB,C3 CLOB);
```

  2. 插入数据。



```
INSERT INTO TEST VALUES(1,0XAB121032DE,'abcdefg');

INSERT INTO TEST VALUES(2,0XAB121032DE,'abcdefg');

COMMIT;
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2,

C3

)
```

  4. 使用 dmfldr 进行导出数据。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
LOB_DIRECTORY=\'/opt/data/\' mode=\'out\'
```

在这个例子中，指定了 LOB_DIRECTORY，而没有指定 LOB_FILE_NAME，导出的大字段数据文件将存放在 LOB_DIRECTORY 指定的/opt/data 目录，文件名为 dmfldr.lob。

#### 3.5.2 DIRECT 为 TRUE 时大字段数据的载入

当 MODE 为 IN 且 DIRECT 为 TRUE 时，此时数据载入若涉及到大字段对象，需要用户指定大字段数据文件。若 CLIENT_LOB 为 TRUE，LOB_DIRECTORY 应指定大字段数据文件所在的客户端本地目录，且此时待载入的大字段长度不得超过 2G，否则无法成功载入大字段；若 CLIENT_LOB 为 FALSE，用户必须先把大字段数据文件传送到 DM 服务器主库所在的机器上，然后使用 LOB_DIRECTORY 指明大字段数据文件位于服务器主库所在机器上的目录；载入时 dmfldr 工具会从客户端本地读取控制文件和指定的数据文件。

大字段数据文件在数据文件中指定，可以是任意格式的文件。在数据文件中，大字段以“文件名：起始偏移：长度”的形式记录在数据文件中。指定的文件名无效时，dmfldr 会报错，装载失败。对于 CLOB 类型字段，当指定的偏移、长度范围内带有不完整字符时，dmfldr 将装载失败。

例 1 展示 DIRECT 为 TRUE，CLIENT_LOB 为 TRUE 时大字段数据的载入。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 BLOB,C3 CLOB);
```

  2. 编辑数据文件 test.txt，存放路径为 DM 客户端所在机器上的/opt/data/test.txt，文件内容如下：



```
1|testblob.txt:0:10|testclob.txt:0:10

2|testblob.txt:10:20|testclob.txt:10:20

3|testblob.txt:20:30|testclob.txt:20:30
```

其中，testblob.txt、testclob.txt 为文本文件，长度大于 30 字节，存放目录为 DM 客户端本地的/opt/data。

  3. 编辑控制文件 test.ctl，存放路径为 DM 客户端所在机器上的/opt/data/test.ctl，文件内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2,

C3

)
```

  4. 使用 dmfldr 进行导入数据。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'  LOB_DIRECTORY=\'/opt/data/\' CLIENT_LOB=TRUE
```

例 2 展示 DIRECT 为 TRUE，CLIENT_LOB 为 FALSE 时大字段数据的载入。

  1. 建表 TEST。



```
DROP TABLE TEST;
CREATE TABLE TEST(C1 INT,C2 BLOB,C3 CLOB);
```

  2. 编辑数据文件 test.txt，存放路径为 DM 客户端机器上的/opt/data/test.txt，文件内容如下：



```
1|testblob.txt:0:10|testclob.txt:0:10
2|testblob.txt:10:20|testclob.txt:10:20
3|testblob.txt:20:30|testclob.txt:20:30
```

其中，testblob.txt、testclob.txt 为文本文件，长度大于 30 字节，存放路径为 DM 服务器主库所在机器（本例中为 192.168.100.122）上的/opt/data/fldr。

  3. 编辑控制文件 test.ctl，存放路径为 DM 客户端机器上的/opt/data/test.ctl，文件内容如下：



```
LOAD DATA
INFILE '/opt/data/test.txt'
INTO TABLE test
FIELDS '|'
(
C1,
C2,
C3
)
```

  4. 使用 dmfldr 进行导入数据。



```
./dmfldr userid=SYSDBA/*****@192.168.100.122:5236 control=\'/opt/data/test.ctl\'  LOB_DIRECTORY=\'/opt/data/fldr/\' CLIENT_LOB=FALSE
```

#### 3.5.3 DIRECT 为 FALSE 时大字段数据的载入

当 MODE 为 IN 且 DIRECT 为 FALSE 时，数据文件中大字段列数据即字段内容。此时，大字段数据长度不允许超过 32KB。BLOB_TYPE 参数指定 BLOB 列内容为十六进制或者字符串：

  * BLOB_TYPE 为 HEX_CHAR 时，数据文件中 BLOB 列当作为十六进制内容；
  * BLOB_TYPE 为 HEX 时，数据文件中 BLOB 列为字符串形式内容，导入后会转换为十六进制。



BLOB_TYPE 参数只对 DIRECT 为 FALSE 时有效，默认为 HEX_CHAR。

> **注意**
>
> 不支持使用包含BLOB列的表生成的导出数据文件在导入时指定BLOB_TYPE为HEX_CHAR。



例 1 展示 DIRECT 为 FALSE 的大字段数据载入。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 BLOB,C3 CLOB);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1|0x12d3c8a7|abcdefg

2|0x12a4cbac|hijlkmn

3|0x22d3c8b3|adefhjd
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2,

C3

)
```

  4. 使用 dmfldr 进行导入数据，BLOB_TYPE 为 HEX_CHAR。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
direct=false blob_type=\'hex_char\'
```

  5. 查询表数据。



```
SELECT * FROM TEST;

行号 C1 C2 C3

---------- ----------- ---------- -------

1 1 0x12D3C8A7 abcdefg

2 2 0x12A4CBAC hijlkmn

3 3 0x22D3C8B3 adefhjd
```

例 2 展示 BLOB_TYPE 为 HEX 时的数据装载。

步骤 1、2、3 同例 1。

  4. 使用 dmfldr 进行导入数据，BLOB_TYPE 为 HEX。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
direct=false blob_type=\'hex\'
```

  5. 查询表数据。



```
select * from test;

行号 C1 C2 C3

---------- ----------- ---------------------- -------

1 1 0x30783132643363386137 abcdefg

2 2 0x30783132613463626163 hijlkmn

3 3 0x30783232643363386233 adefhjd
```

### 3.6 日志文件及日志信息

dmfldr 的日志文件路径由 LOG 参数设置，默认日志文件名为 fldr.log。文件记录了装载过程中的装载信息和错误信息以及统计信息。用户也可以通过设置控制文件中的 OPTIONS 选项来指定日志路径。如果参数及 OPTION 中同时指定了日志路径则其将以 OPTION 中指定的路径为最终路径。

例 展示日志文件的用法。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 INT,C3 DATE);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1|1 2018-11-06

2|2 2018-11-05

3|3 2018-11_04
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2 TERMINATED BY ' ',

C3 DATE FORMAT 'yyyy-mm-dd'

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
log=\'/opt/data/test.log\'
```

  5. 查看日志文件/opt/data/test.log，其内容如下：



```
dmfldr: 2018-12-03 13:46:33 dmfldr:

Copyright (c) 2011, 2015, Dameng. All rights reserved.

控制文件：

加载行数:全部

每次提交服务器行数:50000

跳过行数:0

允许错误数:100

是否直接加载:Yes

是否插入自增列:No

数据是否已按照聚集索引排序:No

字符集:GBK

dmfldr: 2018-12-03 13:46:33

数据文件共1个:

d:\test.txt

错误文件:fldr.bad

目标表:TEST

列名 包装数据类型 终止

C1 CHARACTER |

C2 CHARACTER WHT

C3 yyyy-mm-dd |

行缓冲区数量: 4

任务线程数量: 4

dmfldr: 2018-12-03 13:46:33

目标表:TEST

3 行加载成功。

由于数据格式错误,0行 丢弃。

由于数据错误,0行 没有加载。

跳过的逻辑记录总数:0

读取的逻辑记录总数:3

拒绝的逻辑记录总数:0

用时:20.522(ms)
```

### 3.7 自增列装载

自增列是比较特殊的列，为了保证数据库中自增列列值的正确性，用户在进行数据载入时需要特别注意。

当 DIRECT 参数为 FALSE 时，dmfldr 将把从数据文件中读取的自增列值作为目标值插入数据库表中，用户应当保证每一行的自增列的值符合自增列的规则，否则将造成数据混乱。

当 DIRECT 参数为 TRUE 时，dmfldr 提供了 SET_IDENTITY 参数（缺省为 FALSE）对数据载入时自增列的处理进行设置：

  * 如果指定 SET_IDENTITY 选项值为 TRUE，则 dmfldr 将把从数据文件中读取的自增列值作为目标值插入数据库表中，用户应当保证每一行的自增列的值符合自增列的规则，否则将造成数据混乱；
  * 如果 SET_IDENTITY 选项值设置为 FALSE，则 dmfldr 将忽略数据文件中对应自增列的值，服务器将根据自增列定义和表中已有数据自动生成自增列的值插入每一行的对应列。



另外需要注意的是，DMDPC 环境下暂不支持自增列装载。

例 1 展示自增列的装载。

  1. 建表 TEST，并插入两行数据。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT IDENTITY(1,1),C2 VARCHAR);

INSERT INTO TEST(C2) VALUES('AAA');

INSERT INTO TEST(C2) VALUES('BBB');

COMMIT;
```

此时表 TEST 中的数据为：

```
行号 C1 C2

---------- ----------- ---

1 1 AAA

2 2 BBB
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
2|aaa

3|bbb

4|ccc
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
direct=true set_identity=false
```

  5. 查看表 TEST 的数据。



```
SELECT * FROM TEST;

行号 C1 C2

---------- ----------- ---

1 1 AAA

2 2 BBB

3 3 aaa

4 4 bbb

5 5 ccc
```

在这个例子中，表 TEST 中已有两行数据，由于 SET_IDENTITY 置为 FALSE，因此在数据载入时 dmfldr 根据 C1 列的定义和表中已有数据，为 C1 列重新插入合适的值。

我们再看看如果将 SET_IDENTITY 置为 TRUE 结果会怎样。

例 2 展示 SET_IDENTITY 置为 TRUE 时的装载。

重复例 1 的 1、2、3 步骤。

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236  control=\'/opt/data/test.ctl\'  direct=true set_identity=true
```

  5. 查看表 TEST 的数据。



```
SELECT * FROM TEST;

行号 C1 C2

---------- ----------- ---

1 1 AAA

2 2 BBB

3 2 aaa

4 3 bbb

5 4 ccc
```

### 3.8 数据排序

SORTED 参数用来设置数据是否已经按照聚集索引排序，缺省为 FALSE。

如果设置为 TRUE，则用户必须保证数据已按照聚集索引排序完成，并且如果表中存在数据，需要插入的数据索引值要比表中数据的索引值大，服务器在做插入操作时顺序进行插入。若数据并未按照索引排序，则 dmfldr 会报错，装载失败。对于存在分区表的情况，当数据未按照索引排序时，使用 dmfldr 向分区表进行装载，可能能够成功装载全部数据；也可能仅能成功装载部分数据或装载失败，此时 dmfldr 会报错；成功装载入子表的数据排序正确。

如果设置为 FALSE，则服务器对于每条记录进行定位插入。

此参数仅在 MODE 为 IN 且 DIRECT 为 TRUE 的情况下有效。

在数据量大，并且确定数据已按照聚集索引排序完成的情况下，将 SORTED 参数设置为 TRUE，可以提升装载性能。

例 展示数据排序。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT CLUSTER PRIMARY KEY,C2 VARCHAR);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
2|aaa

3|bbb

4|ccc

5|ddd

1|zzz
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\' sorted=true
```

由于本例中数据文件中的数据并没有按照 C1 列排序，dmfldr 将会报错。

不能使用 NOSORT 选项，数据非有序

### 3.9 空值处理

dmfldr 通过设置 NULL_MODE 参数来处理空值。

  * 设置为 TRUE，载入时 NULL 字符串处理为 NULL，载出时空值处理为 NULL 字符串
  * 设置为 FALSE，载入时 NULL 字符串处理为字符串，载出时空值处理为空串



例 1 展示 null_mode 为 true 时的空值处理。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 VARCHAR);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1|aaa

2|NULL

3|null
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'  null_mode=true
```

  5. 查看表 TEST 的数据，数据文件中 C2 列的“NULL”和“null”字符串都被处理为空值。



```
SELECT C1, IFNULL(C2, 'NULL VALUE') FROM TEST;

行号 C1 IFNULL(C2,'NULLVALUE')

---------- ----------- ----------------------

1 1 aaa

2 2 NULL VALUE

3 3 NULL VALUE
```

例 2 展示 null_mode 为 false 时的空值处理。

步骤 1、2、3 与例 1 相同。

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'  null_mode=false
```

  5. 查看表 TEST 的数据，数据文件中 C2 列的“NULL”和“null”字符串直接被作为字符串值插入表中



```
SELECT C1, IFNULL(C2, 'NULL VALUE') FROM TEST;

行号 C1 IFNULL(C2,'NULLVALUE')

---------- ----------- ----------------------

1 1 aaa

2 2 NULL

3 3 null
```

### 3.10 类类型装载

dmfldr 支持对 CLASS 类型数据的装载。CLASS 类型装载和 LOB 类型一样，载入时需要指定 LOB_DIRECTORY，另外需要指定 ENABLE_CLASS_TYPE 为 TRUE；导出时，默认导出目录和数据文件所在目录一致，导出类类型形成的大字段文件默认为 dmfldr.lob，导出目录和大字段文件可通过 LOB_DIRECTORY 和 LOB_FILE_NAME 参数设置。

> **注意**
>
> CLASS类型大字段数据文件无法通过手动创建，只有从表中导出到大字段文件中，并使用数据文件CLASS字段对应的偏移、字长进行导入。



例 展示类类型装载。

  1. 建表 TEST。



数据准备，创建类 mycls 的类头与类体，之后创建表 TEST，其 C2 列类型为 mycls 类类型，并向 TEST 中插入两行数据。

```
//类头创建
CREATE CLASS mycls
AS
TYPE rec_type IS RECORD (c1 INT, c2 INT);	//类型声明
id	INT;				//成员变量
r	rec_type;			//成员变量
FUNCTION f1(a INT, b INT) RETURN rec_type;	//成员函数
FUNCTION mycls(id INT , r_c1 INT, r_c2 INT) RETURN mycls;//用户自定义构造函数
END;
/

//类体创建
CREATE OR REPLACE CLASS BODY mycls
AS
FUNCTION f1(a INT, b INT) RETURN rec_type
AS
BEGIN 
	r.c1 = a;
	r.c2 = b;
	RETURN r;
END;
FUNCTION mycls(id INT, r_c1 INT, r_c2 INT) RETURN mycls
AS
BEGIN
	this.id = id;		//可以使用this.来访问自身的成员
	r.c1 = r_c1;		//this也可以省略
	r.c2 = r_c2;
	RETURN this;		//使用return this 返回本对象
END;
END;
/

//建表TEST
DROP TABLE TEST;
CREATE TABLE TEST(C1 INT,C2 mycls);
//插入数据：
INSERT INTO TEST VALUES(1,mycls(1,1,1));
INSERT INTO TEST VALUES(2,mycls(2,2,2));
COMMIT;
```

  2. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2

)
```

  3. 使用 dmfldr 进行数据导出。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\' mode=\'out\' lob_directory=\'/opt/data\'
```

  4. 在指定的 LOB_DIRECTORY 目录会生成大字段文件 dmfldr.lob，在指定的数据文件路径生成的数据文件内容如下：



```
1|dmfldr.lob:0:70

2|dmfldr.lob:70:70
```

  5. 创建一张新表 TEST2，表的两列都为 mycls 类类型。



```
CREATE TABLE TEST2(C1 MYCLS,C2 MYCLS);
```

  6. 编辑数据文件 test2.txt，存放路径为/opt/data/test2.txt，文件内容如下：



```
dmfldr.lob:70:70|dmfldr.lob:0:70

dmfldr.lob:0:70|dmfldr.lob:70:70
```

  7. 编辑控制文件 test2.ctl，存放路径为/opt/data/test2.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test2.txt'

INTO TABLE test2

FIELDS '|'

(

C1,

C2

)
```

  8. 使用 dmfldr 进行数据导入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test2.ctl\' 
lob_directory=\'/opt/data/\' enable_class_type=true
```

  9. 查看表 TEST2 的数据。



```
行号 C1 C2

---------- ----------------------------- -----------------------------

1 SYSDBA.MYCLS(2,REC_TYPE(2,2)) SYSDBA.MYCLS(1,REC_TYPE(1,1))

2 SYSDBA.MYCLS(1,REC_TYPE(1,1)) SYSDBA.MYCLS(2,REC_TYPE(2,2))
```

### 3.11 条件过滤

通过在控制文件中指定 WHEN\<field_conditions>子句，可以在装载过程中对数据进行过滤，符合 field_conditions 条件的数据才会被装载。

对于条件过滤的使用需注意以下几点：

  * 判断条件中的操作符仅支持比较相等和不相等，即=、!=和\<>这三个比较操作符；
  * 目前仅支持使用 AND 连接多个过滤条件；
  * BLANKS 和 WHITESPACE 表示若干个空格；
  * 判断条件若使用(p1:p2)作为比较表达式，其意义与在 POSTION 子句中的意义相同，表示从该行指定位置获取数据进行比较，起始位置和结束位置表示的都是字节位置，包含边界 p1,p2；
  * 如果判断条件中使用 colid 作为比较表达式，该列必须在 INTO 表的 coldef_option 中进行说明；
  * 如果判断条件中使用 colid 作为比较表达式，判断条件中使用的列仅用于过滤，并没有对应表中的某个实际列，应在 col_def 中指明 FILLER 属性表示装载时跳过该列；
  * 如果判断条件中比较数据是字符常量值，其长度小于比较表达式长度，则在其之后补充空格；如果判断条件中比较数据是二进制串常量，其长度小于比较表达式长度，则在之后补充 0。



例 展示条件过滤。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 INT);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
12

23

32

48

91
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

WHEN C1 != '2'

(

C1 position (1:1),

C2 position (2:2)

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
```

  5. 查看表 TEST 的数据，可以看到数据文件中的行 2,3 被过滤掉了。



```
SELECT * FROM TEST;

行号 C1 C2

---------- ----------- -----------

1 1 2

2 3 2

3 4 8

4 9 1
```

### 3.12 多表装载

通过在控制文件中指定多个 INTO TABLE 子句，可以将一批数据同时向多个表进行装载。每个 INTO TABLE 子句中都可以指定 WHEN 过滤条件、FIELDS 子句和列定义子句。

对于多表装载的使用需注意以下几点：

  * 每个 INTO TABLE 子句的目标表必须是不同的表；
  * 多表装载时不支持直接装载分区表子表；
  * 对于第二个及其之后的 INTO TABLE 子句，在其 coldef_option 中，必须为第一列指定 POSITION 选项；



例 展示多表装载。

  1. 建表 TEST1、TEST2。



```
DROP TABLE TEST1;

DROP TABLE TEST2;

CREATE TABLE TEST1(C1 INT,C2 INT);

CREATE TABLE TEST2(C1 INT,C2 INT);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1,2

2,3

3,2

4,8

9,1
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA
INFILE '/opt/data/test.txt'
INTO TABLE test1
WHEN C1 != '1'
FIELDS ','
(
c1 position (1:1),
c2 position (3:3)
)
INTO TABLE test2
WHEN (3:3) = '2' AND c1 != '3'
FIELDS ','
(
c1 position (1:1),
c2 postion (3:3)
)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\'
```

  5. 查看表 TEST1 和 TEST2 的数据如下：



```
SELECT * FROM TEST1;

行号 C1 C2

---------- ----------- -----------

1 2 3

2 3 2

3 4 8

4 9 1

SELECT * FROM TEST2;

行号 C1 C2

---------- ----------- -----------

1 1 2
```

### 3.13 个性化设置

用户通过设置 dmfldr 的 SKIP、LOAD、ROWS 参数，可以根据自己的需求调整装载的起始行、装载最大行数以及每次提交的行数。

SKIP 参数用来设置跳过数据文件起始的逻辑行数，整型数值。默认的跳过起始行数为 0 行。如果用户指定了多个文件，且起始文件中的行数不足 SKIP 所指定的行数，则 dmfldr 工具会扫描下一个文件直至累加的行数等于 SKIP 所设置的行数或者所有文件都已扫描结束。

LOAD 参数用来设置装载的最大行数，整型数值。默认的最大装载行数为数据文件中的所有行数。LOAD 指定的值不包括 SKIP 指定的跳过的行数。

ROWS 参数用来设置每次提交的行数，整型数值。默认的提交行数为 50000 行。提交行数的值表示提交到服务器的行数，并不一定代表按照数据文件中的数据顺序的行数。用户可以根据实际情况调整每次提交的行数，以达到性能的最佳点。ROWS 参数作用于 MODE 为 IN 的情况下，当 MODE 为 OUT 时无效。

例 展示个性化设置。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 VARCHAR);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1|aaa

2|bbb

3|ccc

4|ddd

5|eee

6|fff

7|ggg

8|hhh

9|iii

10|jjj
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\' skip=3 load=5
```

  5. 查看表 TEST 的数据，载入时跳过了数据文件的前 3 行数据，且只载入了 5 条数据。



```
SELECT * FROM TEST;

行号 C1 C2

---------- ----------- ---

1 4 ddd

2 5 eee

3 6 fff

4 7 ggg

5 8 hhh
```

### 3.14 主备切换时的数据继续载入

在 DM 数据守护主备环境下，如果 dmfldr 数据载入中发生主备切换，dmfldr 支持主备切换完成后自动继续装载数据，并且能保持数据正确。

要使用此项功能，USERID 参数需要使用主备服务名方式进行配置，例如：

```
dmfldr USERID=SYSDBA/*****@dw CONTROL='c:\fldr.ctl'
```

同时在 dm_svc.conf 配置文件中配置主备服务名，例如：

```
dw=(192.168.0.101:5236, 192.168.0.102:5236)
```

目前主备切换时的数据继续载入功能还存在以下功能限制：

  * 目前仅支持单机的主备，不支持 MPP 主备。但是，若在 MPP 主备环境中使用 MPP_CLIENT=FALSE，等同于单机，也是支持的。
  * 目前不支持分区表装载。



### 3.15 MPP 本地分发

MPP_CLIENT 参数用来设置在 DM MPP 环境下使用 dmfldr 进行数据装载时的数据分发方式。

当 DM 数据库服务器环境为单站点时此参数无效。当服务器环境为 MPP 环境时，若 MPP_CLIENT 为 TRUE，dmfldr 采用客户端分发模式；若 MPP_CLIENT 为 FALSE，则采用本地分发模式。

客户端分发模式下，数据在 dmfldr 客户端分发好后直接往指定站点发送数据。

本地分发模式下，导入时，dmfldr 直接将数据发送到连接的站点，数据最终在连接的站点；导出时，dmfldr 只导出连接站点的数据。

MPP 环境下要配置 dmmal.ini 文件中的 MAL_INST_HOST 和 MAL_INST_PORT 参数。

例 当前 DM 数据库服务器环境为 MPP 环境，两个节点，SEQNO 号分别为 0、1。使用 dmfldr 采用本地分发方式进行数据装载，连接的节点为 SEQNO 为 0 的节点。

  1. 建表 TEST。



```
DROP TABLE TEST;

CREATE TABLE TEST(C1 INT,C2 INT);
```

  2. 编辑数据文件 test.txt，存放路径为/opt/data/test.txt，文件内容如下：



```
1|1

2|2

3|3

4|4

5|5

6|6

7|7

8|8

9|9

10|10
```

  3. 编辑控制文件 test.ctl，存放路径为/opt/data/test.ctl，内容如下：



```
LOAD DATA

INFILE '/opt/data/test.txt'

INTO TABLE test

FIELDS '|'

(

C1,

C2

)
```

  4. 使用 dmfldr 进行数据载入。



```
./dmfldr userid=SYSDBA/*****@localhost:5236 control=\'/opt/data/test.ctl\' mpp_client=false
```

  5. 查询 SEQNO 为 0 的节点上 TEST 表的数据。



```
SELECT * FROM TEST WHERE SF_GET_EP_SEQNO(ROWID)=0;

行号 C1 C2

---------- ----------- -----------

1 1 1

2 2 2

3 3 3

4 4 4

5 5 5

6 6 6

7 7 7

8 8 8

9 9 9

10 10 10
```

  6. 查询 SEQNO 为 1 的节点上 TEST 表的数据。



```
SELECT * FROM TEST WHERE SF_GET_EP_SEQNO(ROWID)=0;

未选定行
```

### 3.16 提升 dmfldr 性能

用户在使用 dmfldr 时根据系统和数据的具体情况对一些参数进行调整，可以获得更好的性能，这些参数包括：

  * **BUFFER_NODE_SIZE**



BUFFER_NODE_SIZE 设置读取文件缓冲区页大小，值越大，缓冲区的页越大，每次读取的数据就越多，每次发送到服务器的数据也就越多，效率越高。但其大小受 dmfldr 客户端内存大小限制。

  * **READ_ROWS**



在某些情况下，BUFFER_NODE_SIZE 读入的数据行数很大，而后续操作处理不了这么大的行数，此时可以用 READ_ROWS 来限制处理的行数。dmfldr 取 READ_ROWS 和 BUFFER_NODE_SIZE 中较小的值作为一次处理的行数。

  * **SEND_NODE_NUMBER**



指定 dmfldr 在数据载入时发送节点的个数，默认由系统计算一个初始值。若在数据载入时发现发送节点不够用，系统会动态增加分配。在系统内存足够的情况下，可以适当设大 SEND_NODE_NUMBER 值，提升 dmfldr 载入性能。

  * **TASK_THREAD_NUMBER**



指定 dmfldr 在数据载入时处理用户数据的线程数目。默认情况下，dmfldr 将该参数值设为系统 CPU 的个数，但当 CPU 个数大于 8 时，默认值都被置为 8。在 dmfldr 客户端所在机器 CPU 大于 8 环境中，提高 TASK_THREAD_NUMBER 值可以提升 dmfldr 装载性能。

  * **BLDR_NUM**



水平分区表装载时，指定服务器 BLDR 的最大个数，缺省为 64。

服务器的 BLDR 保存水平分区子表相关信息，BLDR_NUM 的设置也就指定了服务器能同时载入的水平分区子表的个数。若 BLDR_NUM 设置太大，当水平分区子表数过多时，可能会导致服务器内存不足。当载入时实际需要的 BLDR 个数超出 BLDR_NUM 设置时，会淘汰指定子表的 BLDR，并替换为新的子表 BLDR。

  * **BDTA_SIZE**



BDTA（Batch Data）的大小，缺省为 5000。

BDTA 代表 DM 数据库批量数据处理机制中一个批量，在内存、CPU 允许的条件下，增大 BDTA_SIZE 能加快装载速度；在网络是装载性能瓶颈时，增大 BDTA_SIZE 影响不大。

  * **INDEX_OPTION**



索引的设置选项，缺省为 1。INDEX_OPTION 的可选项有 1、2 和 3。

1 代表服务器装载数据时先不刷新二级索引，而是将新数据按照索引预先排序，在装载完成后，再将排好序的数据插入索引。如果在数据载入前，目标表中已有较多数据，建议 INDEX_OPTION 置为 1。

2 代表服务器在快速装载过程中不刷新二级索引数据，只在装载完成时重建所有二级索引。如果在数据载入前，目标表中没有数据或数据量较小，建议 INDEX_OPTION 置为 2。

3 代表服务器使用追加模式来进行二级索引的插入，在数据装载的过程中，同时进行二级索引的插入，若未禁用全局索引，则在非 DPC 环境下，全局索引可通过设置 INDEX_OPTION 为 3 进行插入。当原有数据量远大于插入数据量时，建议 INDEX_OPTION 置为 3。

### 3.17 dmfldr 使用限制

dmfldr 的使用存在以下一些限制：

  * 不支持向临时表、外部表装载数据
  * 不支持向系统表装载数据
  * 不支持向带有位图索引的表装载数据
  * 不支持向带有全文索引的表装载数据
  * 不支持向 DCP 代理装载数据
  * 不支持在 DPC 环境下，向带有全局索引的表装载数据
  * 仅支持在 index_option=2 的情况下向带有函数索引的表装载数据
  * dmfldr 装载时会对约束进行检查，对各种约束的处理机制如下表所示；由于 dmfldr 不处理约束的有效性，都视为有效，故需要用户自行处理约束有效性等相关内容



表3.1 dmfldr的约束检查机制

|**约束**|**数据不满足时**|**数据插入与否**|**约束是否有效**|
|------|------|------|------|
|非空约束(NOT NULL)|报错|不插入|有效|
|聚集索引(CLUSTER PRIMARY KEY)|报错|不插入|有效|
|唯一约束(UNIQUE， PRIMARY KEY)|不报错|插入|有效|
|引用约束(FOREIGN KEY)|不报错|插入|有效|
|CHECK 约束(CHECK)|不报错|插入|有效|


> **注意**
>
> 当数据不满足唯一约束时，唯一约束仍有效，但相关联的唯一索引将会失效。



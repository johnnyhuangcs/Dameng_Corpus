

# 如何在 DIsql 中使用脚本

用户不必在每次使用数据库的时候都编写常用的 SQL 语句和 PL/SQL 程序块，而是可以将它们保存到称为脚本的文件中。这些脚本专门为反复执行的各种任务而设计。本节主要介绍如何编写脚本、运行脚本。

## 5.1 编写脚本

使用一种文本编辑器来编写 SQL 脚本，比如 notepad 文本等。

一个简单的脚本例子，在文本中编写脚本并保存为 D:\test.sql，内容如下：

```
drop table t01;

create table t01(c1 varchar(100), c2 varchar(100));

begin

for i in 1..10 loop

insert into t01 values('a'||i, 'b'||i);

end loop;

end;

/
```

## 5.2 使用 START 命令运行脚本

运行脚本必须使用\<start>命令。\<start>命令中与脚本有关的是\<\`运行脚本> ,\<@ 运行脚本>，\<@@ 运行脚本>和\<start 运行脚本>。\<直接执行语句>在 DIsql 登录时候使用，与脚本无关，此处不做介绍。

语法如下：

```
<start>::=<`运行脚本>|<start 运行脚本>|<直接执行语句>
<`运行脚本>::=`<file_path> [<PARAMETER_VALUE>{ <PARAMETER_VALUE>}]
<@运行脚本>::=@<file_path> [<PARAMETER_VALUE>{ <PARAMETER_VALUE>}]
<@@运行脚本>::=@@<file_path> [<PARAMETER_VALUE>{ <PARAMETER_VALUE>}]
<start 运行脚本>::=START <file_path> [<PARAMETER_VALUE>{ <PARAMETER_VALUE>}]
<直接执行语句>::= -E "<SQL语句>{;<SQL语句>}"
```

\<file_path>：脚本的绝对路径。

\<PARAMETER_VALUE>：传递进入脚本的参数值。

脚本可以在启动 DIsql 时就运行，或者在进入 DIsql 之后再运行。如果在启动时运行，只能使用 \<\运行脚本>；如果在进入 DIsql 之后，使用 \<\运行脚本> 或者 \<start 运行脚本> 来运行脚本都可以。

\<@@ 运行脚本>跟其他几种用法区别在于，如果执行脚本嵌套多层，@@ 脚本如果是相对路径, 则其路径在父层节点脚本路径中查找，其他方式都是在当前工作目录查找。

> **注意**
>
> \<运行脚本>中的符号位于键盘第二排左起第一个。如果在LINUX环境下使用\<运行脚本>，则符号需要加\或'进行转义。例如：./disql
> SYSDBA/*****\\\`/dev/test.sql



1.启动 DIsql 时，运行脚本。

```
SQL>start D:\test.sql
或
SQL>`D:\test.sql
```

执行结果如下：

![启动 DIsql 时运行脚本结果](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图5.1-启动DIsql时运行脚本结果.png)

图5.1 启动DIsql时运行脚本结果

2. 进入 DIsql 之后，运行脚本。

```
SQL>start D:\test.sql或SQL>`D:\test.sql
```

执行结果如下：

![进入 DIsql 后运行脚本结果](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图5.2-进入DIsql后运行脚本结果.png)

图5.2 进入DIsql后运行脚本结果

> **注意**
>
> DISQL在运行完脚本后会自动执行一个提交动作



## 5.3 使用 EDIT 命令编辑脚本

DIsql 中使用 EDIT 命令来编辑指定的脚本文件。

语法如下：

```
ED[IT][<file_name>] 
```

\<file_name>：指定待编辑的脚本文件。

如果指定文件不存在，则创建该文件。

如果省略文件\<file_name>，则只会修改缓冲区中的最后一条 SQL 语句。DIsql 自动打开系统缺省的文本编辑器（windows 下使用 notepad），复制缓冲区中最后一条 SQL 语句到文本中，这时用户可以对其中的内容进行编辑。修改完成之前，DIsql 一直处于等待状态。修改完毕，保存文件后，被修改的内容就会被写入缓存区。这对于修改错误命令很方便。当操作系统为 linux 或 unix 时，使用 vi 进行编辑。

示例如下：

```
SQL>EDIT D:\test.sql
或
SQL>edit
```

## 5.4 如何在脚本中使用变量

替换变量主要用来进行 SQL、PLSQL 与用户的交互，可以运行时输入，也可提前输入。

替换变量前带有一个前缀标志符（默认是&），DIsql 在命令中遇到替换变量时，用真实值去代替，相当于 c 语言中的宏定义。真实值来源于三个地方：

1.脚本参数带入

2.脚本中直接定义

3.用户动态输入

DIsql 中根据 SET DEFINE 命令开启本地变量功能并定义变量前缀符号。默认符号&作为变量的前缀。详细用法请查看 DEFINE 命令。

### 5.4.1 脚本带参数值

脚本带参数值，参数名必须是数字。

#### 5.4.1.1 变量名是数字

在脚本中通过&n 来引用参数，n 为 1 表示为第一个参数，2 表示第二个参数，依次类推。如现有表 test，其建表和初始化数据语句如下：

```
create table test(id int) ;

insert into test values(11) ;

insert into test values(12) ;

insert into test values(15) ;
```

脚本 D:\test.sql 如下：

```
select * from test where id = &1;

select * from test where id = &2;

select * from test where id = &3;
```

DIsql 要求传入的参数值个数要与脚本中的变量个数一一对应。比如脚本 D:\test.sql 中有三个变量&1、&2、&3，则要求传入的参数值也必须是三个。如果传入参数值个数不匹配，如 n 为 3，但执行时只带了 2 个参数，DIsql 就会在屏幕上提示输入参数。

示例，输入三个参数值 11、12、13：

```
SQL>`D:\test.sql 11 12 13
```

执行结果如下：

![脚本带参数用法](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图5.3-脚本带参数用法.png)

图5.3 脚本带参数用法

#### 5.4.1.2 参数书写要求

因为参数是原样替换，因此如果 SQL 语句中字符串要求用单引号，那么定义的参数值也应该包含单引号；另外如果字符串中有特殊字符，需要使用双引号将整个字符串作为一个整体，需要注意的是，如果作为整体的字符串中有双引号作为内容，需要将内容的双引号转义。

如果参数值是数字，写法没有特殊要求。

如果参数值是字符串，应该用单引号扩起，如果字符串有空格，应该在单引号外面，再加上一个双引号扩起。如脚本 D:\test.sql：

```
create table test(a varchar);
insert into test values('hello');
insert into test values('hello world');
//脚本D:\test.sql内容如下：
select * from test where a = &1;
select* from test where a = &2;
```

注意参数的写法，执行语句如下：

```
SQL>`D:\test.sql 'hello' "'hello world'"
```

执行结果如下：

![字符串参数书写用法](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图5.4-字符串参数书写用法.png)

图5.4 字符串参数书写用法

### 5.4.2 脚本中定义参数值

使用 DEFINE 命令定义变量值，格式：DEFINE 标识符 = 值。

如脚本 D:\test.sql：

```
define n=1 

define s=DIsql

select &n from dual; 

select '&s' from dual;
```

如果变量没有定义，那么在通过&引用时，DIsql 会提示输入。

### 5.4.3 接收用户交互式输入参数值

很多时候，在执行脚本时，我们希望有些信息根据脚本的提示，让用户动态输入。这种情况非常好实现，满足下面两个条件即可。

  1. 运行脚本时不带参数
  2. 脚本中不定义参数



如脚本 D:\test.sql：

```
select &x from dual;
```

执行结果如下：

![交互式输入参数值用法](https://download.dameng.com/eco/docs/asset/pm/dIsql-manual/图5.5-交互式输入参数值用法.png)

图5.5 交互式输入参数值用法

## 5.5 使用 PROMPT 命令传递信息

PROMPT 命令会在屏幕上输出一行信息。这非常有助于在存储脚本中向用户传送信息。

语法如下：

```
PROMPT <输出内容>
```

例如，编写一个查询，要提供用户看到数据的纯文本描述信息。用户就可以使用 PROMPT 命令完成这项工作。将如下脚本存储到名为 prompt.sql 的文件中：

```
prompt 部分ini参数和dminit建库参数信息（系统值、最小值和最大值）;

select top 3 * from v$dm_ini;
```

执行脚本：

```
SQL> `f:\prompt.sql
```

执行结果如下：

```
SQL> `f:\prompt.sql

SQL> prompt 所有ini参数和dminit建库参数信息（系统值、最小值和最大值）;

所有ini参数和dminit建库参数信息（系统值、最小值和最大值）

SQL> select top 3 * from v$dm_ini;


行号       PARA_NAME    PARA_VALUE   MIN_VALUE MAX_VALUE MPP_CHK SESS_VALUE
---------- ------------ ----------------------------- --------- --------- ------- -----------------------------
           FILE_VALUE                    DESCRIPTION           PARA_TYPE
           ----------------------------- --------------------- ---------
1          CTL_PATH     D:\dmdbms\data\DAMENG\dm.ctl  NULL      NULL      N       D:\dmdbms\data\DAMENG\dm.ctl
           D:\dmdbms\data\DAMENG\dm.ctl  path of dm.ctl        READ ONLY

2          CTL_BAK_PATH D:\dmdbms\data\DAMENG\ctl_bak NULL      NULL      N       D:\dmdbms\data\DAMENG\ctl_bak
           D:\dmdbms\data\DAMENG\ctl_bak backup path of dm.ctl READ ONLY

3          CTL_BAK_NUM  10                            1         100       N       10
           10                            backup num of dm.ctl  SYS


已用时间: 3.931(毫秒). 执行号:29.

```

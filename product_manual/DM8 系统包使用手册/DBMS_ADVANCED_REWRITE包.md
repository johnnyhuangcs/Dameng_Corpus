

# DBMS_ADVANCED_REWRITE包

在不改变应用程序的前提下，在服务器端将查询语句替换成其他的查询语句执行，此时就需要查询重写（QUERY REWRITE）。DM 使用 DBMS_ADVANCED_REWRITE 包实现该功能，不支持安全策略。DM 支持对原始语句中的某些特定词的替换，以及整个语句的替换，不支持递归和变换替换。

## 3.1 相关方法

  1. DECLARE_REWRITE_EQUIVALENCE



声明一个等价重写规则。

语法如下：

```
PROCEDURE DECLARE_REWRITE_EQUIVALENCE (
  name				VARCHAR(128),
  source_stmt		VARCHAR(8188),
  destination_stmt	VARCHAR(8188),
  validate			BOOLEAN,
  rewrite_mode		VARCHAR(16)
);
```

参数详解

  * name



重写规则的唯一标识，在会话级唯一。

  * source_stmt



原始查询语句。

  * destination_stmt



目标语句。

  * validate



是否校验原始语句和目标语句等价。

  * rewrite_mode



包含 4 种：disabled，不允许重写；text_match，文本匹配重写；general，变换重写；recursive，递归重写。后面两种暂不支持，可以声明成功，但是不起作用。

使用说明：

  1. 如果一个 source_stmt 对应多个 destination_stmt，即用户创建很多相同的重写规则，只是名字不同，在重写时只有第一个才有效。也就是说只执行第一个重写规则，后面的所有重写规则无效。
  2. validate 置为“TRUE”时，要求 source_stmt 和 destination_stmt 等价，例如：“select c1 from x1 where c1 != 1;”与“select c1 from x1 where c1 > 1 or c1 \<1;”,否则报错。暂不支持为“TRUE”的情况。
  3. 不允许 source_stmt 和 destination_stmt 完全相同，否则报错：“源语句与目标语句相同”。
  4. source_stmt 和 destination_stmt 语句必须为查询语句，否则报错：“源语句与目标语句不兼容”。
  5. source_stmt 和 destination_stmt 语句必须经过语法和语义正确解析，否则报错。如果两个语句中都出错，则报 source_stmt 的错，即首先解析 source_stmt，如果出错直接报错，不再解析后面的 destination_stmt。
  6. rewrite_mode 为“TEXT_MATCH”时，只允许替换 source_stmt 中完全相同的语句，不区分大小写。若 SOURCE_STMT 为空，进行正则表达式替换时，是否大小写敏感与数据库建库参数保持一致。
  7. source_stmt 和 destination_stmt 语句不允许包含“{}”字符。
  8. 扩展功能说明：如果 source_stmt 为空，destination_stmt 格式为“table/[table]”，则可以将当前会话语句中的“table”换成“[table]”，“table”为正则表达式。DM 支持符合 POSIX 标准的正则表达式。如下表 4.1 所示：



表 4.1 正则表达式语法

|**语法**|**说明**|**示例**|
|--|--|--|
|.|匹配任何除换行符之外的单个字符|DA. 匹配“DAMENG”|
|*|匹配前面的字符 0 次或多次|a*b 匹配“bat”中的“b”和“about”中的“ab”|
|+|匹配前面的字符一次或多次|ac+ 匹配包含字母“a”和至少一个字母“c”的单词，如“race”和“ace”|
|^|匹配行首|^car 仅当单词“car”显示为行中的第一组字符时匹配该单词|
|$|匹配行尾|end$ 仅当单词“end”显示为可能位于行尾的最后一组字符时匹配该单词|
|[]|字符集，匹配任何括号间的字符|be[n-t] 匹配“between”中的“bet”、“beneath”中的“ben”和“beside”中的“bes”，但不匹配“below”中的“bel”|
|[^]|排除字符集。匹配任何不在括号间的字符|be[^n-t] 匹配“before”中的“bef”、“behind”中的“beh”和“below”中的“bel”，但是不匹配“beneath”中的“ben”|
|(表达式)|在表达式加上括号或标签在替换命令中使用|(abc)+ 匹配“abcabcabc”|
|||匹配 OR 符号 (|) 之前或之后的表达式。最常用在分组中|(sponge|mud) bath 匹配“sponge bath”和“mud bath”|
|\|按原义匹配反斜杠 (\) 之后的字符。这使您可以查找正则表达式表示法中使用的字符，如 { 和 ^|\^ 搜索 ^ 字符|
|{n[,m]}|区间表达式，匹配在它之前的单个字符重现的次数区间。{n}指重复 n 次；{n,}为至少出现 n 次重复；{n,m}为重现 n 至 m 次|zo{2} 匹配“zoone”中的“zoo”，但不匹配“zozo”|
|[[:alpha:]]|表示任意字母\w ([a-z]+) | ([A-Z]+)||
|[[:digit:]]|表示任意数字\d ([0-9]+)||
|[[:lower:]]|表示任意小写字母 ([a-z]+)||
|[[:alnum:]]|表示任意字母和数字([a-z0-9]+)||
|[[:space:]]|表示任意空格||
|[[:upper:]]|表示任意大写字母([A-Z]+)||
|[[:punct:]]|表示任意标点符号||
|[[:xdigit:]]|表示任意 16 进制数([0-9a-fA-F]+)||


  2. ALTER_REWRITE_EQUIVALENCE



修改重写模式。

语法如下：

```
PROCEDURE ALTER_REWRITE_EQUIVALENCE (
  name			CHAR(128),
  rewrite_mode	VARCHAR(16)
);
```

参数详解

  * name



重写规则的唯一标识。

  * rewrite_mode



包含 4 种，同上。

  3. VALIDATE_REWRITE_EQUIVALENCE



校验重写规则是否等效，暂不支持。提供该函数，但是不进行校验。

语法如下：

```
VALIDATE_REWRITE_EQUIVALENCE (
  name CHAR(128)
);
```

参数详解

  * name



重写规则的唯一标识。

  4. DROP_REWRITE_EQUIVALENCE。



删除重写规则。

语法如下：

```
PROCEDURE DROP_REWRITE_EQUIVALENCE (
  name CHAR(128)
);
```

参数详解

  * name



重写规则的唯一标识。

## 3.2 字典信息

所有的重写规则信息都保存到系统表 SYS_REWRITE_EQUIVALENCES 中，该系统表的结构如下表所示。所有的用户创建的语句重写信息都保存到该表中。

表4.2 系统表SYS_REWRITE_EQUIVALENCES结构

|**序号**|**列名**|**类型**|**是否允许为空**|**说明**|
|------|------|------|------|------|
|1|OWNER|VARCHAR(128)|N|重写规则的拥有者|
|2|NAME|VARCHAR(128)|N|重写规则名|
|3|SOURCE_STMT|VARCHAR(3200)|Y|原始语句|
|4|DESTINATION_STMT|VARCHAR(3200)|Y|目标语句|
|5|REWRITE_MODE|VARCHAR(16)|Y|重写模式|
|6|RESV1|INTEGER|Y|保留字段|
|7|RESV2|VARCHAR(128)|Y|保留字段|


主键（OWNER，NAME）。

通过查询语句可以获得重写规则的信息：

```
select * from sys_rewrite_equivalences;
```

## 3.3 使用说明

  1. **设置会话标记**



每个会话对应一个 QUERY_REWRITE_INTEGRITY 标记，该标记只在会话期间起作用，不保存到字典信息中，如果会话结束，则该标记也无效。会话重建后，该标记也需要重新赋值才有效。

QUERY_REWRITE_INTEGRITY 标记用于表示该会话是否可以执行查询语句重写，默认值为“ENFORCED”，另外两个值为：“TRUSTED”和“STALE_TOLERATED”。默认不允许语句重写；DM 未对后两个参数进行严格区分，一般设置后两个参数都允许查询重写。

语法格式：

```
ALTER SESSION SET QUERY_REWRITE_INTEGRITY = TRUSTED;
```

参数详解

  * TRUSTED

表示在保证物化视图数据是正确时才使用该视图替换。

  * STALE_TOLERATED

表示能容忍错误的数据，即可以使用不正确的物化视图执行重写。



  2. **重写规则的作用域**



重写规则只允许在当前会话中执行，禁止跨模式使用其他用户指定的重写规则。SYSDBA 也不可以使用其他用户的重写规则替换。

  3. **回滚**



执行方法 DECLARE_REWRITE_EQUIVALENCE 后，将自动提交，不能执行回滚。在该方法之前的所有操作也自动提交。

  4. **自动重写**



自动重写，指的是用户在执行任何查询语句时，可以自动调用重写规则执行重写，而不需要设置会话标记来控制是否执行查询重写。SYSDBA 可以设置某些用户自动重写标记，设置函数为：SP_USER_SET_AUTO_REWRITE_FLAG。该标记和会话标记中有一个有效，则允许查询重写。

```

```

语法格式：

```
SP_USER_SET_AUTO_REWRITE_FLAG (
  'USER_NAME',
  VALUE
);
```

参数详解

  * USER_NAME

用户名。

  * VALUE

0 表示不允许自动重写，1 表示允许自动重写。



  5. **查询重写时机**



查询重写的时机主要发生在如下几个地方：

(1) 一般的用户查询，例如 select count(*) from t1;

(2) 非动态的查询执行时，例如存储过程中重写：select count(*) from t1 where c1 >1;但是以下语句不可以重写：i int := 1; select count(*) from t1 where c1 > i;

(3) 视图中的查询重写，不支持递归替换。例如：create view v1 as select count(*)from t1;如果“select count(*) from t1;”存在重写规则，也可以重写。

  6. **关闭重用执行计划功能**



使用时，可以在 dm.ini 中将重用执行计划标记（USE_PLN_POOL）置为 0。否则，先执行过查询语句后，再设置查询规则，然后对比两次的查询结果会相同，即未被重写。如果不关闭，只能等到设置过查询规则之后，才能执行查询。

## 3.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_ADVANCED_REWRITE');
```

例 REWRITE 包的应用，注意 INI 参数 USE_PLN_POOL 应置为 0。

```
drop table x1;
drop table x2;
create table x1(c1 int, c2 char(20));
insert into x1 values(12, 'test12');
insert into x1 values(13, 'test13');
insert into x1 values(14, 'test14');
insert into x1 values(15, 'test15');
create table x2(d1 int, d2 char(30));
commit;
BEGIN
DBMS_ADVANCED_REWRITE.declare_rewrite_equivalence(
'TEST_REWRITE',
'SELECT COUNT(\*) FROM X1', //原始查询语句
'SELECT COUNT(\*) FROM X2', //目标语句
FALSE,
'TEXT_MATCH'
);
END;
/

SELECT COUNT(*) FROM X1; //执行结果4

alter session set QUERY_REWRITE_INTEGRITY = TRUSTED; //默认为'ENFORCED'
SELECT COUNT(*) FROM X1; //执行结果0

DBMS_ADVANCED_REWRITE.DROP_REWRITE_EQUIVALENCE ('TEST_REWRITE');
//drop rewrite

BEGIN
  DBMS_ADVANCED_REWRITE.DECLARE_REWRITE_EQUIVALENCE(
   'TEST_REWRITE',
  NULL, //为空
  'FRO./FROM', //使用正则表达式
  FALSE,
  'EXT_MATCH'
  );
END;
/
SELECT COUNT(*) FROK X1; //执行结果4
```

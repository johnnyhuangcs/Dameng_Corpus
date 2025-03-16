

# SQL 调优

## 23.1 简介

SQL 调优作为数据库性能调优中的最后一个环节，对查询性能产生着直接的影响。在进行正式的 SQL 调优前，用户首先要关注下列几点：

  1. 达梦数据库安装时的配置参数是否符合应用场景需求；
  2. 达梦数据库的 INI 配置文件中各项参数是否已经处于最优配置；
  3. 应用系统中数据库设计是否合理。



本章将介绍定位高负载的 SQL 语句的方法，利用自动 SQL 调整功能进行优化，以及如何开发有效的 SQL 语句和使用优化器提示来影响执行计划。

## 23.2 调优目标

SQL 调优的整体目标是使用最优的执行计划，这意味着 IO 以及 CPU 代价最小。具体而言调优主要关注下列方面：

  * **表扫描**



如果计划中对某大表使用了全索引扫描，那么用户需要关注是否存在着该表的某个查询条件使得过滤后可以淘汰至少一半的数据量。通过添加相应的索引，全索引扫描可能被转换为范围扫描或等值查找。添加的二级索引可以包含该表上所有被选择项以避免 BLKUP2 操作符的查找操作带来的第二次 IO 开销，但无疑这会增加二级索引的大小。用户需权衡二者的利弊以选择正确的处理方式。

  * **连接操作的顺序和类型**



多表连接时，不同的连接顺序会影响中间结果集数量的大小，这时调优的目标就是要找到一种能使中间结果保持最小的连接顺序。

对于给定的一个连接或半连接，DM 可以用 HASH 连接、嵌套循环连接、索引连接或者是归并连接实现。通过分析表的数据量大小和索引信息，SQL 调优目标是选择最适宜的操作符。

对半连接而言，HASH 连接还可细分为左半 HASH 和右半 HASH。用户可以通过始终对数据量小的一侧建立 HASH 来进行调优。

  * **分组操作**



分组操作往往要求缓存所有数据以找到属于同一组的所有数据，在大数据量情况下这会带来大量的 IO。用户应该检查 SQL 查询和表上索引信息，如果可以利用包含分组列的索引，那么执行计划就会使用排序分组从而不用缓存中间结果。

## 23.3 确定高负载的 SQL

在打开监控开关（ENABLE_MONITOR=1）后，可以通过查询动态视图 V$LONG_EXEC_SQLS 或 V$SYSTEM_LONG_EXEC_SQLS 来确定高负载的 SQL 语句。前者显示最近 1000 条执行时间较长的 SQL 语句，后者显示服务器启动以来执行时间最长的 20 条 SQL 语句。例如：

```
  SELECT * FROM V$LONG_EXEC_SQLS;
```

或者

```
  SELECT * FROM V$SYSTEM_LONG_EXEC_SQLS;
```

## 23.4 自动 SQL 调整

使用查询优化向导工具，输入需要进行调整的 SQL 语句，向导工具将在分析完执行计划后给出推荐索引的提示。用户只需按提示建立相应索引即可。

## 23.5 开发有效的 SQL 语句

SQL 语言是一种相当灵活的结构化查询语言。用户可以利用多种不同形式的查询语句完成相同的查询功能。为了使执行效率达到最优，用户需要参考以下原则以开发出有效的 SQL 语句：

  1. **避免使用 OR 子句**



OR 子句在实际执行中会被转换为类似于 UNION 的查询。如果某一个 OR 子句不能利用上索引则会使用全表扫描造成效率低下，应避免使用。

如果 OR 子句都是对同一列进行过滤，用户可以考虑使用 IN VALUE LIST 的过滤形式。如：

```
  SELECT ... WHERE city = 'ShangHai' OR city = 'WuHan' OR city = 'BeiJing';
```

调整为

```
  SELECT ... WHERE city IN( 'ShangHai','WuHan','BeiJing');
```

  2. **避免使用困难的正则表达式**



在 SQL 语言中，LIKE 关键字支持通配符匹配，含通配符的表达式被称为正则表达式。有的正则表达式可以自动优化为非匹配的。例如：a LIKE 'L%'可以优化为 a>='L' AND a \<'M',这样就可以用到 a 上的索引。即使没有索引，转换后的比较也更快。再如：a LIKE 'LM_'可以转化为 a>='LM' AND a\<'LN' AND a LIKE 'LM_'。虽然仍然包含着通配符匹配，但大大缩小了匹配的范围。

所谓困难的正则表达式是指开头和结尾都为通配符的正则表达式，如'L%'、'%L'，优化器没办法缩小它们的匹配范围，也不可能用到索引而必须使用全表扫描。因此要尽可能避免这样的正则表达式。

如果仅仅是开头为通配符，用户可以在列 a 上建立 REVERSE(a)这样一个函数索引，利用函数索引反转待匹配项从而使用函数索引进行范围扫描。

  3. **灵活使用伪表(SYSDUAL)**



首先，可以利用伪表进行科学计算，执行语句 SELECT 3*4 FROM SYSDUAL，则可以得到结果 12；

其次，在某些方面使用 SYSDUAL 可提高效率。例如：查询过程中要判断表 t1 中是否有满足 condition1 条件的记录存在，可执行以下语句：

```
  SELECT COUNT(*) INTO x FROM t1 WHERE condition1;
```

然后，根据变量 x 的取值来判断。但是当 t1 非常大时该语句执行速度很慢，而且由于不知道 SELECT 返回的个数，不能用 SELECT *代替。事实上这个查询可以利用伪表来完成：

```
  SELECT 'A' INTO y FROM SYSDUAL  WHERE EXISTS (SELECT 1 FROM t1 WHERE condition1);
```

判断 y 值，如等于'A'则 T1 中有记录。调整后的语句执行速度明显比上一句高。

另外，在 DM 的语法里是可以省略 FROM 子句的，这时系统会自动加上 FROM SYSDUAL。因此前面的科学计算例子可以简化为 SELECT 3*4;

  4. **SELECT 项避免‘*’**



除非用户确实要选择表中所有列，否则 SELECT *这种写法将让执行器背上沉重的负荷。因为每一列的数据不得不自下往上层层向上传递。不仅仅如此，如果用户查询的是列存储表，那么列存储所带来的 IO 优势将损耗殆尽。

任何时候，用户都要了解表结构和业务需求，小心地选择需要的列并一一给出名称，避免直接用 SELECT *。

  5. **避免功能相似的重复索引**



索引并非越多越好。抛开优化器面对众多索引逐一试探所耗费的时间不谈，如果表上增删改操作频繁，那么索引的维护将会成为大麻烦，尤其是函数索引的计算开销更不能忽略。

  6. **使用 COUNT(*)统计结果行数**



如果对单表查询 COUNT(*)且没有过滤条件，那么 DM 优化器会直接读取相关索引中存储的行数信息，加以回滚段中其他事务插入或删除元组的行数修正，迅速地给出最终结果而避免对实际数据的读取。相比之下，COUNT(列名)会对数据进行读操作，执行效率远低于 COUNT(*)。

即使查询中含有过滤条件，由于 DM 特有的批处理方式，COUNT(*)依旧快于其他写法。这是因为 COUNT(*)无需取得行的具体值而仅仅需要行数这一信息。

需要额外说明的是，COUNT(*)会将 NULL 值计算在内而 COUNT(列名)是不包含 NULL 值的，因此用户要结合应用场景决定是否可以使用 COUNT(*)。

  7. **使用 EXPLAIN 来查看执行计划**



在查询语句或者插入、删除、更新语句前增加 EXPLAIN 关键字，DM 将显示其执行计划而无需实际执行它。查阅 V$SQL_NODE_NAME 表中每个操作符的含义，用户可以很方便且直观地了解数据如何被处理及传递。如果启用了统计信息收集，那么对照执行计划和对动态视图 v$sql_node_history，v$sql_node_name 的查询结果，用户就可以知道在实际执行中每一个操作符执行的时间，进而找出性能瓶颈。

  8. **UNION 和 UNION ALL 的选择**



UNION 和 UNIONALL 的区别是前者会过滤掉值完全相同的元组，为此 UNION 操作符需要建立 HASH 表缓存所有数据并去除重复，当 HASH 表大小超过了 ini 参数指定的限制时还会做刷盘。

因此如果应用场景并不关心重复元组或者不可能出现重复，那么 UNION ALL 无疑优于 UNION。

  9. **优化 GROUP BY ... HAVING**



GROUP BY 最常见的实现有 HASH 分组（HAGR）和排序分组（SAGR）。前者需要缓存中间结果；如果用户在 GROUP BY 的列上建立索引，那么优化器就会判断并可能使用上该索引，这时的 GROUP BY 就会变为 SAGR。

HAVING 是分组后对结果集进行的过滤，如果过滤条件无关集函数操作，用户可以考虑将过滤条件放在 WHERE 而不是 HAVING 中。DM 优化器会判断并自动转换部分等效于 WHERE 的 HAVING 子句，但显式地给出最佳 SQL 语句会让优化器工作得更好。

  10. **使用优化器提示（HINT）**



利用经验对优化器的计划选择进行调整，HINT 是 SQL 调整不可或缺的一步。我们将在下一节给出详细说明。

## 23.6 使用优化器提示

DM 查询优化器采用基于代价的方法。在估计代价时，主要以统计信息或者普遍的数据分布为依据。在大多数情况下，估计的代价都是准确的。但在一些比较特殊的场合，例如缺少统计信息，或统计信息陈旧，或抽样数据不能很好地反映数据分布时，优化器选择的执行计划不是“最优”的，甚至可能是很差的执行计划。

DBA 对于数据分布是很清楚的，他们知道 SQL 语句按照哪种方法执行会最快。在这种情况下，DBA 可以主动进行人工干预，指示优化器按照指定的方法去选择 SQL 的执行计划。

DM 把这种人工干预优化器的方法称为 HINT，它使优化器根据 DBA 的 HINT 提示来生成指定的执行计划。如果优化器无法生成相应的执行计划，该 HINT 将会被忽略。

HINT 的常见格式如下所示：

```
  SELECT /*+ HINT1 [HINT2]*/ 列名 FROM 表名 WHERE_CLAUSE ;
  UPDATE 表名 /*+ HINT1 [HINT2]*/ SET 列名 =变量 WHERE_CLAUSE ;
  DELETE FROM 表名 /*+ HINT1 [HINT2]*/ WHERE_CLAUSE ;
```

需要注意的是：如果 HINT 的语法没有写对或指定的值不正确，DM 并不会报错，而是直接忽略 HINT 继续执行。

### 23.6.1 INI 参数提示

DBA 可以通过 HINT 方式对 INI 参数的值进行语句级的指定。语句中的 HINT 对 INI 参数值的设置优先级高于 INI 文件中参数值的设置。通过 HINT 方式只会修改 INI 参数的在本会话中的值，不会改变它在 INI 文件中的值。

支持使用 HINT 的 INI 参数可通过 V$HINT_INI_INFO 动态视图查询。支持 HINT 的 INI 参数分为两类：一是 HINT_TYPE 为“OPT”，表示分析阶段使用的参数；二是 HINT_TYPE 为“EXEC”，表示运行阶段使用的参数，运行阶段使用的参数对于视图无效。

例如：

```
  SELECT /*+ENABLE_HASH_JOIN(1)*/ * FROM T1,T2 WHERE C1=D1;
```

上面的语句中使用了 HINT，指明在执行此 SQL 时参数 ENABLE_HASH_JOIN 被置为 1。

### 23.6.2 索引提示

  1. **使用索引**



目前 DM 提供的 HINT 为表索引的选择 HINT，它指示使用指定索引进行数据检索。

语法：

```
表名 + INDEX + 索引名

或

/*+ INDEX (表名[,] 索引名) {INDEX (表名[,] 索引名)} */
```

一个语句中最多指定 8 个索引。在后一种语法格式中，如果查询中给出了表的别名那么必须使用别名。

假设表 t1 上 id 和 name 列上都存在着单列索引。

```
    //数据准备
    DROP TABLE T1 CASCADE;
    CREATE TABLE T1 (ID INTEGER,NAME VARCHAR(128));
    CREATE INDEX IDX_T1_ID ON T1(ID);
    CREATE INDEX IDX_T1_NAME ON T1(NAME);
```

例 1 在查询语句中指定索引。

```
  SELECT * FROM t1 WHERE id > 2011 AND name < 'xxx';
```

如果 id 列上能过滤更多数据，建议指示用索引 idx_t1_id。

```
  SELECT * FROM t1 index idx_t1_id WHERE id > 2011 AND name < 'xxx';
```

或

```
  SELECT /*+INDEX(t1, idx_t1_id) */ * FROM t1 WHERE id > 2011 AND name < 'xxx';
```

例 2 当有多个索引时，要指定使执行计划最优的。

```
  SELECT * FROM t1 WHERE id > 2011 AND name < 'xxx' ORDER BY name;
```

考虑到后面的 name 列排序操作，建议指示使用 name 列的索引 idx_t1_name，因为这样可以在执行过程中省略掉排序操作（执行计划中可以看出来），比使用 id 列索引代价小。

```
  SELECT * FROM t1 INDEX idx_t1_name WHERE id > 2011 AND name < 'xxx' ORDER BY name;
```

或

```
  SELECT /*+ INDEX(a idx_t1_name)*/ * FROM t1 a WHERE id > 2011 AND name < 'xxx' ORDER BY name;
```

  2. **不使用索引**



语法：

```
/*+ NO_INDEX (表名[,] 索引名) { NO_INDEX (表名[,] 索引名)} */
```

可以指定多个索引，则这些索引都不能被使用。一个语句中最多指定 8 个索引。

### 23.6.3 连接方法提示

DBA 可以通过指定两个表间的连接方法来检测不同连接方式的查询效率，指定的连接可能由于无法实现或代价过高而被忽略。如果连接方法提示中的表名(别名)或索引名无效也会被自动忽略。

```
//数据准备
DROP TABLE T1 CASCADE;
DROP TABLE T2 CASCADE;

CREATE TABLE T1 (ID INTEGER,NAME VARCHAR(128));
CREATE TABLE T2 (ID INTEGER,NAME VARCHAR(128));

begin
    for i in 1..1000 loop
    insert into T1 values(i,'dameng'||i);
    insert into T2 values(i+500,'damengsh'||i);
    end loop;
end;
```

  1. **USE_HASH**



强制两个表间使用指定顺序的哈希连接，例如：

```
EXPLAIN SELECT /*+ USE_HASH(T1, T2) */ * FROM T1, T2 WHERE T1.ID = T2.ID;

1   #NSET2: [1, 9800, 104]
2     #PRJT2: [1, 9800, 104]; exp_num(4), is_atom(FALSE)
3       #HASH2 INNER JOIN: [1, 9800, 104];  KEY_NUM(1); KEY(T1.ID=T2.ID) KEY_NULL_EQU(0)
4         #CSCN2: [1, 1000, 52]; INDEX33555602(T1); btr_scan(1)
5         #CSCN2: [1, 1000, 52]; INDEX33555603(T2); btr_scan(1)
```

  2. **NO_USE_HASH**



强制两个表间不能使用指定顺序的哈希连接，例如：

```
EXPLAIN SELECT /*+ NO_USE_HASH(T1, T2) */ * FROM T1, T2 WHERE T1.ID = T2.ID;

1   #NSET2: [1, 9800, 104]
2     #PRJT2: [1, 9800, 104]; exp_num(4), is_atom(FALSE)
3       #HASH2 INNER JOIN: [1, 9800, 104];  KEY_NUM(1); KEY(T2.ID=T1.ID) KEY_NULL_EQU(0)
4         #CSCN2: [1, 1000, 52]; INDEX33555603(T2); btr_scan(1)
5         #CSCN2: [1, 1000, 52]; INDEX33555602(T1); btr_scan(1)
```

NO_USE_HASH(T1,T2)表示不允许 T1 作为左表，T2 作为右表的哈希连接，但 T1 作为右表的哈希连接还是允许的。

  3. **USE_NL**



强制两个表间使用嵌套循环连接，例如：

```
EXPLAIN SELECT /*+ USE_NL(A, B) */ * FROM T1 A, T2 B WHERE A.ID = B.ID;

1   #NSET2: [24685, 9800, 104]
2     #PRJT2: [24685, 9800, 104]; exp_num(4), is_atom(FALSE)
3       #SLCT2: [24685, 9800, 104]; A.ID = B.ID
4         #NEST LOOP INNER JOIN2: [24685, 9800, 104]
5           #CSCN2: [1, 1000, 52]; INDEX33555602(T1 as A); btr_scan(1)
6           #CSCN2: [1, 1000, 52]; INDEX33555603(T2 as B); btr_scan(1)
```

  4. **NO_USE_NL**



强制两个表间不能使用嵌套循环连接，例如：

```
EXPLAIN SELECT /*+ NO_USE_NL(A, B) */ * FROM T1 A, T2 B WHERE A.ID = B.ID;

1   #NSET2: [1, 9800, 104]
2     #PRJT2: [1, 9800, 104]; exp_num(4), is_atom(FALSE)
3       #HASH2 INNER JOIN: [1, 9800, 104];  KEY_NUM(1); KEY(A.ID=B.ID) KEY_NULL_EQU(0)
4         #CSCN2: [1, 1000, 52]; INDEX33555602(T1 as A); btr_scan(1)
5         #CSCN2: [1, 1000, 52]; INDEX33555603(T2 as B); btr_scan(1)
```

  5. **USE_NL_WITH_INDEX**



当连接情况为左表 + 右表索引时，强制两个表间使用索引连接，例如：

```
//数据准备
CREATE INDEX IDX_T2_ID ON T2(ID);
//执行EXPLAIN
EXPLAIN SELECT /*+ USE_NL_WITH_INDEX(T1, IDX_T2_ID) */ * FROM T1, T2 WHERE T1.ID = T2.ID;

1   #NSET2: [6, 3000, 104]
2     #PRJT2: [6, 3000, 104]; exp_num(4), is_atom(FALSE)
3       #NEST LOOP INDEX JOIN2: [6, 3000, 104]
4         #CSCN2: [1, 1000, 52]; INDEX33555602(T1); btr_scan(1)
5         #BLKUP2: [6, 3, 4]; IDX_T2_ID(T2)
6           #SSEK2: [6, 3, 4]; scan_type(ASC), IDX_T2_ID(T2), scan_range[T1.ID,T1.ID], is_global(0)
```

  6. **NO_USE_NL_WITH_INDEX**



当连接情况为左表 + 右表索引时，强制两个表间不能使用索引连接，例如：

```
EXPLAIN SELECT /*+ NO_USE_NL_WITH_INDEX(T1, IDX_T2_ID) */ * FROM T1, T2 WHERE T1.ID = T2.ID;
1   #NSET2: [1, 9800, 104]
2     #PRJT2: [1, 9800, 104]; exp_num(4), is_atom(FALSE)
3       #HASH2 INNER JOIN: [1, 9800, 104];  KEY_NUM(1); KEY(T1.ID=T2.ID) KEY_NULL_EQU(0)
4         #CSCN2: [1, 1000, 52]; INDEX33555602(T1); btr_scan(1)
5         #CSCN2: [1, 1000, 52]; INDEX33555603(T2); btr_scan(1)

//使用完毕，请删除索引
DROP INDEX IDX_T2_ID;
```

  7. **USE_MERGE**



强制两个表间使用归并连接。归并连接所用的两个列都必须是索引列。例如：

```
//数据准备
CREATE INDEX IDX_T1_ID ON T1(ID);
CREATE INDEX IDX_T2_ID ON T2(ID);
STAT 100 ON T1(ID);
STAT 100 ON T2(ID);
//执行EXPLAIN
EXPLAIN SELECT /*+ USE_MERGE(T1,T2) */ * FROM T1, T2 WHERE T1.ID = T2.ID AND T1.ID < 1 AND T2.ID < 1;

1   #NSET2: [1, 1, 104]
2     #PRJT2: [1, 1, 104]; exp_num(4), is_atom(FALSE)
3       #MERGE INNER JOIN3: [1, 1, 104]; KEY_NUM(1); KEY(COL_0 = COL_0) KEY_NULL_EQU(0)
4         #BLKUP2: [1, 1, 52]; IDX_T1_ID(T1)
5           #SSEK2: [1, 1, 52]; scan_type(ASC), IDX_T1_ID(T1), scan_range(null2,1), is_global(0)
6         #BLKUP2: [1, 1, 52]; IDX_T2_ID(T2)
7           #SSEK2: [1, 1, 52]; scan_type(ASC), IDX_T2_ID(T2), scan_range(null2,1), is_global(0)
```

当连接类型为外连接时，无法使用归并连接，此时即使指定 USE_MERGE，也不起作用。

  8. **NO_USE_MERGE**



强制两个表间不能使用归并连接，例如：

```
EXPLAIN SELECT /*+ NO_USE_MERGE(T1,T2) */ * FROM T1, T2 WHERE T1.ID = T2.ID AND T1.ID > 1 AND T2.ID > 1;

1   #NSET2: [1, 999, 104]
2     #PRJT2: [1, 999, 104]; exp_num(4), is_atom(FALSE)
3       #SLCT2: [1, 999, 104]; T2.ID > 1
4         #HASH2 INNER JOIN: [1, 999, 104];  KEY_NUM(1); KEY(T1.ID=T2.ID) KEY_NULL_EQU(0)
5           #SLCT2: [1, 999, 52]; T1.ID > 1
6             #CSCN2: [1, 1000, 52]; INDEX33555602(T1); btr_scan(1)
7           #CSCN2: [1, 1000, 52]; INDEX33555603(T2); btr_scan(1)
```

  9. **SEMI_GEN_CROSS**



优先采用半连接转换为等价的内连接，仅 OPTIMIZER_MODE=1 有效。例如：

```
EXPLAIN SELECT /*+ SEMI_GEN_CROSS  OPTIMIZER_MODE(1) */ COUNT(*) FROM T1 A WHERE A.ID IN (SELECT B.ID FROM T1 B);

1   #NSET2: [2, 1, 8]
2     #PRJT2: [2, 1, 8]; exp_num(1), is_atom(FALSE)
3       #AAGR2: [2, 1, 8]; grp_num(0), sfun_num(1), distinct_flag[0]; slave_empty(0)
4         #HASH2 INNER JOIN: [2, 1000, 8]; LKEY_UNIQUE KEY_NUM(1); KEY(DMTEMPVIEW_889193998.colname=A.ID) KEY_NULL_EQU(0)
5           #DISTINCT: [1, 1000, 4]
6             #PRJT2: [1, 1000, 4]; exp_num(1), is_atom(FALSE)
7               #CSCN2: [1, 1000, 4]; INDEX33555602(T1 as B); btr_scan(1)
8           #CSCN2: [1, 1000, 4]; INDEX33555602(T1 as A); btr_scan(1)
```

  10. **NO_SEMI_GEN_CROSS**



不采用半连接转换为等价的内连接，仅 OPTIMIZER_MODE=1 有效。例如：

```
EXPLAIN SELECT /*+ NO_SEMI_GEN_CROSS  OPTIMIZER_MODE(1) */ COUNT(*) FROM T1 A WHERE A.ID IN (SELECT B.ID FROM T1 B);

1   #NSET2: [1, 1, 4]
2     #PRJT2: [1, 1, 4]; exp_num(1), is_atom(FALSE)
3       #AAGR2: [1, 1, 4]; grp_num(0), sfun_num(1), distinct_flag[0]; slave_empty(0)
4         #HASH LEFT SEMI JOIN2: [1, 1000, 4]; KEY_NUM(1);  KEY(A.ID=B.ID) KEY_NULL_EQU(0)
5           #CSCN2: [1, 1000, 4]; INDEX33555602(T1 as A); btr_scan(1)
6           #CSCN2: [1, 1000, 4]; INDEX33555602(T1 as B); btr_scan(1)
```

  11. **USE_CVT_VAR**

优先采用变量改写方式实现连接，适合驱动表数据量少而另一侧计划较复杂的场景，目前支持变量改写的连接方式有 NEST LOOP INNER JOIN2、NEST
LOOP LEFT JOIN2、NEST LOOP SEMI JOIN2，仅 OPTIMIZER_MODE=1 有效。例如：



```
EXPLAIN SELECT /*+ USE_CVT_VAR OPTIMIZER_MODE(1) */ COUNT(*) FROM T1 A WHERE A.id = 1001 AND EXISTS (SELECT 1 FROM T1 B, T1 C WHERE B.id = C.id AND A.name= B.name);
1   #NSET2: [1, 1, 52]
2     #PRJT2: [1, 1, 52]; exp_num(1), is_atom(FALSE)
3       #AAGR2: [1, 1, 52]; grp_num(0), sfun_num(1), distinct_flag[0]; slave_empty(0)
4         #NEST LOOP SEMI JOIN2: [1, 1, 52];  join condition(A.NAME = DMTEMPVIEW_889194003.colname)[with var]
5           #SLCT2: [1, 1, 52]; A.ID = 1001
6             #CSCN2: [1, 1000, 52]; INDEX33555602(T1 as A); btr_scan(1)
7           #PRJT2: [1, 25, 56]; exp_num(1), is_atom(FALSE)
8             #HASH2 INNER JOIN: [1, 25, 56];  KEY_NUM(1); KEY(B.ID=C.ID) KEY_NULL_EQU(0)
9               #SLCT2: [1, 25, 52]; B.NAME = var1
10                #CSCN2: [1, 1000, 52]; INDEX33555602(T1 as B); btr_scan(1)
11              #CSCN2: [1, 1000, 4]; INDEX33555602(T1 as C); btr_scan(1)
```

  12. **NO_USE_CVT_VAR**

不考虑变量改写方式实现连接，仅 OPTIMIZER_MODE=1 有效。例如：



```
EXPLAIN SELECT /*+ NO_USE_CVT_VAR OPTIMIZER_MODE(1) */ COUNT(*) FROM  T1 A WHERE A.id = 1001 AND EXISTS (SELECT 1 FROM T1 B, T1 C WHERE B.id = C.id AND A.name= B.name);

1   #NSET2: [2, 1, 52]
2     #PRJT2: [2, 1, 52]; exp_num(1), is_atom(FALSE)
3       #AAGR2: [2, 1, 52]; grp_num(0), sfun_num(1), distinct_flag[0]; slave_empty(0)
4         #HASH LEFT SEMI JOIN2: [2, 1, 52]; KEY_NUM(1);  KEY(A.NAME=DMTEMPVIEW_889194007.colname) KEY_NULL_EQU(0)
5           #SLCT2: [1, 1, 52]; A.ID = 1001
6             #CSCN2: [1, 1000, 52]; INDEX33555602(T1 as A); btr_scan(1)
7           #PRJT2: [1, 1000, 56]; exp_num(1), is_atom(FALSE)
8             #HASH2 INNER JOIN: [1, 1000, 56];  KEY_NUM(1); KEY(C.ID=B.ID) KEY_NULL_EQU(0)
9               #CSCN2: [1, 1000, 4]; INDEX33555602(T1 as C); btr_scan(1)
10              #CSCN2: [1, 1000, 52]; INDEX33555602(T1 as B); btr_scan(1)
```

  13. **ENHANCED_MERGE_JOIN**

一般情况下，归并连接需要左右孩子的数据按照连接列有序，使用此优化器提示时，优化器将考虑通过插入排序操作符的方式实现归并连接，仅
OPTIMIZER_MODE=1 有效。例如：



```
//不加HINT时计划，使用了哈希连接
EXPLAIN SELECT /*+ stat(T1 1M) stat(T2 1M) */COUNT(*) FROM T1, T2 WHERE T1.NAME=T2.NAME AND T1.ID=T2.ID;
1   #NSET2: [1, 1, 104]
2     #PRJT2: [1, 1, 104]; exp_num(1), is_atom(FALSE)
3       #AAGR2: [1, 1, 104]; grp_num(0), sfun_num(1), distinct_flag[0]; slave_empty(0)
4         #HASH2 INNER JOIN: [1, 9, 104];  KEY_NUM(2); KEY(T1.NAME=T2.NAME AND T1.ID=T2.ID) KEY_NULL_EQU(0, 0)
5           #CSCN2: [1, 1000, 52]; INDEX33555602(T1); btr_scan(1)
6           #CSCN2: [1, 1000, 52]; INDEX33555603(T2); btr_scan(1)

// 加HINT后计划，通过增加排序以使用了归并连接
EXPLAIN SELECT /*+ENHANCED_MERGE_JOIN OPTIMIZER_MODE(1) stat(T1 1M) stat(T2 1M) */COUNT(*) FROM T1, T2 WHERE T1.NAME=T2.NAME AND T1.ID=T2.ID;
1   #NSET2: [436, 1, 104]
2     #PRJT2: [436, 1, 104]; exp_num(1), is_atom(FALSE)
3       #AAGR2: [436, 1, 104]; grp_num(0), sfun_num(1), distinct_flag[0]; slave_empty(0)
4         #MERGE INNER JOIN3: [436, 2, 104]; KEY_NUM(2); KEY(COL_0 = COL_0 AND COL_1 = COL_1) KEY_NULL_EQU(0, 0)
5           #SORT3: [122, 1000000, 52]; key_num(2), partition_key_num(0), is_distinct(FALSE), top_flag(0), is_adaptive(0)
6             #CSCN2: [115, 1000000, 52]; INDEX33555602(T1); btr_scan(1)
7           #SORT3: [122, 1000000, 52]; key_num(2), partition_key_num(0), is_distinct(FALSE), top_flag(0), is_adaptive(0)
8             #CSCN2: [115, 1000000, 52]; INDEX33555603(T2); btr_scan(1)
```

### 23.6.4 连接顺序提示

多表连接时优化器会考虑各种可能的排列组合顺序。使用 ORDER HINT 指定连接顺序提示可以缩小优化器试探的排列空间，进而得到接近 DBA 所期望的查询计划。如果连接顺序和连接方法提示同时指定且二者间存在自相矛盾，优化器会以连接顺序提示为准。

语法

```
/*+ ORDER (T1, T2 , T3, … tn ) */
```

本节中的例子用到 4 个表 T1, T2 , T3, T4。

```
   CREATE TABLE T1(C1 INT,C2 VARCHAR);
   CREATE TABLE T2(D1 INT,D2 VARCHAR);
   CREATE TABLE T3(E1 INT,E2 VARCHAR);
   CREATE TABLE T4(F1 INT,F2 VARCHAR);
```

例如：

```
  SELECT * FROM t1, T2 , T3, T4 WHERE …
```

如果期望表的连接顺序是 T1, T2, T3，那么可以加入这样的提示：

```
  SELECT /*+ ORDER(T1, T2, T3 )*/* FROM T1, T2 , T3, T4 WHERE …
```

在指定上述连接顺序后，T4,T1,T2,T3 或 T1,T2,T4,T3 会被考虑；T3,T1,T2 或 T1,T3,T2 不被考虑。

连接顺序也可以和连接方法同时指定用于得到更特定的执行计划。

例如：

```
  EXPLAIN SELECT /*+ OPTIMIZER_MODE(1), ORDER(T1,T2,T3,T4) ,USE_HASH(T1,T2), USE_HASH(T2,T3), USE_HASH(T3,T4)*/* FROM T1,T2,T3,T4 WHERE T1.c1=T2.d1 AND T2.d2 = T3.e2 AND T3.e1 = T4.f1;
```

此时的执行计划如下：

```
    1   ##NSET2: [2, 1, 208]
    2     ##PRJT2: [2, 1, 208]; exp_num(8), is_atom(FALSE)
    3       ##HASH2 INNER JOIN: [2, 1, 208];  KEY_NUM(1);
    4         ##HASH2 INNER JOIN: [1, 1, 156];  KEY_NUM(1);
    5           ##HASH2 INNER JOIN: [0, 1, 104];  KEY_NUM(1);
    6             ##CSCN2: [0, 1, 52]; INDEX33555490(T1)
    7             ##CSCN2: [0, 1, 52]; INDEX33555491(T2)
    8           ##CSCN2: [0, 1, 52]; INDEX33555492(T3)
    9         ##CSCN2: [0, 1, 52]; INDEX33555493(T4)
```

### 23.6.5 统计信息提示

优化器在计划优化阶段会自动获取基表的行数。但是一些特殊类型的表行数估算并不准确，或者 DBA 希望了解表大小对计划影响的时候，需要手动设置表的行数。

语法：

```
/*+ STAT (表名, 行数) */
```

统计信息提示只能针对基表设置，视图和派生表等对象设置无效。如果表对象存在别名则必须使用别名。行数只能使用整数，或者整数 +K（千），整数 +M（百万），整数 +G（十亿）。行数提示设置后，统计信息的其它内容也会做相应的调整。

例如：

```
CREATE TABLE T_S(C1 INT);
INSERT INTO T_S SELECT LEVEL FROM DUAL CONNECT BY LEVEL<= 100;
COMMIT;
STAT 100 ON T_S(C1);
EXPLAIN SELECT /*+ STAT(T_S,1M) */ * FROM T_S WHERE C1 <= 10;
```

计划如下：

```
1   #NSET2: [117, 100000, 16]
2     #PRJT2: [117, 100000, 16]; exp_num(2), is_atom(FALSE)
3       #SLCT2: [117, 100000, 16]; T_S.C1 <= 10
4         #CSCN2: [117, 1000000, 16]; INDEX33555897(T_S)
```

不使用 HINT 时计划：

```
1   #NSET2: [0, 10, 16]
2     #PRJT2: [0, 10, 16]; exp_num(2), is_atom(FALSE)
3       #SLCT2: [0, 10, 16]; T_S.C1 <= 10
4         #CSCN2: [0, 100, 16]; INDEX33555897(T_S)
```

### 23.6.6 MPP 本地对象提示

MPP 环境下，提供一种将用户表或动态视图作为本地对象处理的方法，通过指示符 LOCAL_OBJECT(对象名/别名) 进行处理。

语法：

```
/*+ LOCAL_OBJECT(对象名/别名) */
```

对于系统表当做本地对象的处理，本方法不适用，系统表只能在主站点才能做本地对象处理。

例 MPP 环境下，执行下面测例：

```
drop table t1;

create table t1(c1 int) distributed by(c1);

insert into t1 values(0);

insert into t1 values(1);

commit;
```

将表 t1 作为本地对象进行处理：

```
select /*+local_object(t1)*/ * from t1;
```

将表 t1 作为本地对象进行处理，支持使用 t1 的别名 X。

```
select /*+local_object(x)*/ * from t1 x;
```

### 23.6.7 忽略重复键值提示

当执行 INSERT 操作时，如果存在 UNIQUE 索引，那么发生了重复键值冲突。使用 HINT IGNORE_ROW_ON_DUPKEY_INDEX 则可以忽略该冲突，冲突数据既不进行插入也不会报错，其他非冲突插入正常进行。

每一个该 HINT 仅对应一个索引，如需忽略多个索引上的重复键值冲突，请指定多个 HINT。

语法：

```
/*+IGNORE_ROW_ON_DUPKEY_INDEX(<表名> [(<列名>{,<列名>})])*/
```

若 INSERT 的目标表具有别名，则只有 \< 表名 > 与别名一致时 HINT 才生效。

支持使用该 HINT 时不指定 \< 列名 >，此时忽略表上除函数索引外的所有 UNIQUE 索引的报错。当指定 \< 列名 > 时，该 HINT 指定的 \< 列名 > 必须是某个 UNIQUE 索引的前导列或者全部列，否则报错；当作为前导列时，不能出现其它也将这些列作为前导列的 UNIQUE 索引，否则报错。

指定该 HINT 时，不支持 insert 操作的批量优化处理。

例 指定 IGNORE_ROW_ON_DUPKEY_INDEX 不会报错。

```
drop table t1;
create table t1(c1 int, c2 int, c3 int, c4 int);
create unique index t1i1 on t1(c1,c2,c3);
insert  into t1 values (1,2,1,1);
insert  into t1 values (1,2,1,1); //报错
insert  /*+IGNORE_ROW_ON_DUPKEY_INDEX(t1(c1,c2,c3))*/ into t1 values (1,2,1,1);  //不报错
insert  /*+IGNORE_ROW_ON_DUPKEY_INDEX(t1)*/ into t1 values (1,2,1,1);  //不报错
```

### 23.6.8 禁用计划缓存提示

使用 HINT PLAN_NO_CACHE 禁用计划缓存，当前语句的执行计划将不会被缓存。

语法：

```
/*+PLAN_NO_CACHE*/
```

例 下面三条 SELECT 语句中，第一条 select 指定了 PLAN_NO_CACHE，它的执行计划生成后将不会被缓存；第二条 select 未指定 PLAN_NO_CACHE，所以它的执行计划被缓存了；第三条 select 将不需要重新生成执行计划，可重用缓存的第二条 select 的执行计划。

```
drop table t1;

create table t1(c1 int);

select /*+PLAN_NO_CACHE*/ * from t1;

select * from t1;

select * from t1;
```

### 23.6.9   DMDPC 数据分发方式提示

DMDPC 环境下提供了一种专门针对指定的连接、分组、排序、去重和分析函数操作符数据分发方式进行人工干预的优化器提示。该提示被采纳的前提是指定的分发路径有效，因为代价原因没有被优化器选中。

语法：

```
/*+DPC(分发方式探测序号 分发方式字符串)*/
```

其中，分发方式探测序号请在 10053 trace event 生成的 TRACE 文件中查看，以 nth_try 标示；分发方式字符串请参考 [23.6.9.1 二元操作符](#23.6.9.1%二元操作符)和 [23.6.9.2 一元操作符](#23.6.9.2%一元操作符)。

例 在 SQL 语句中使用 DMDPC 数据分发方式提示。

```
select /*+ DPC(1 x_merge) */ para_name from v$dm_ini order by para_name;
```

#### 23.6.9.1 二元操作符

对于连接和集合运算二元操作符，可供选择的分发方式字符串参见下表。

表23.1 二元操作符的分发方式字符串

|**分发方式字符串**|**含义**|
|-------|-------|
|L_NO_R_NO|二元操作符两侧都不添加任何数据分发操作符|
|L_BRO|二元操作符左侧增加广播操作|
|R_BRO|二元操作符右侧增加广播操作|
|L_DIS_R_DIS|分别对二元操作符两侧数据进行分发|
|L_DIS|对二元操作符左侧数据进行分发|
|R_DIS|对二元操作符右侧数据进行分发|
|L_GAT_R_GAT|二元操作符两侧数据汇总到一个线程|
|ONE_GAT_ONLY|二元操作符一侧为指令，另一侧为非指令。非指令的一侧将数据发送到一个 SP 站点执行|
|ONE_PART_ONLY|二元操作符一侧为普通表，另一测将数据发送到此普通表所在的站点执行|


例 下面一段 trace 片段展示了采用优化器提示中的二元操作符分发方式的情况。

```
//连接片段，正在试探第5个需要分发的操作符hash left semi join(anti)。
*** probe best distribute method for hash left semi join(anti)(000000005089E500), DPC motion nth_try[5], restricts[0x12], self cost 1.000000
  left motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
  right motion cost: broadcast = 0.000023, dis = 0.000011, gather = 0.000011
  > try    R_BRO(404), cost 0.062523, n_parallel 2, n_sites 0, best*
  > try L_DIS_R_DIS(405), cost 0.062523, n_parallel 2, n_sites 0, best*
  > try L_GAT_R_GAT(408), cost 1.000023, n_parallel 1, n_sites 0
```

#### 23.6.9.2 一元操作符

对于分组（group）、排序（order）、去重（distinct）和分析函数（analytic function）一元操作符，可供选择的分发方式字符串参见下表。

下表中的 X 代表 group、order、distinct 或 analytic function。例如，当按照 group 进行分发时，x 即表示 group。

表23.2 一元操作符的分发方式字符串

|**分发方式字符串**|**含义**|
|-------|-------|
|X|直接进行 X|
|X_DIS_X|本地先 X，在数据分发后进行二次 X|
|DIS_X|数据分发后进行 X|
|DIS_SORT_X|数据分发后先进行排序，接下来执行 X|
|X_GAT_X|本地先 X，数据汇总到一个线程后进行二次 X|
|GAT_X|数据汇总到一个线程后进行 X|
|GAT_SORT_X|数据汇总到一个线程后排序，接下来执行 X|
|X_MERGE|每个线程先分别排序，汇总后由最终的线程进行归并排序|
|DIS_X_GAT_X|对无分组项且含有 DISTINCT 的集函数，先按集函数参数进行数据分发，然后再对分发的数据进行 X_GAT_X。仅当 INI 参数 GROUP_OPT_FLAG 取值包含 64 时考虑该方式|
|DIST_DIS_X_GAT_X|和 DIS_X_GAT_X 类似。区别为，在对集函数参数进行数据分发前先做一次去重处理。仅当 INI 参数 GROUP_OPT_FLAG 取值包含 64 时考虑该方式|


例 下面一段 trace 展示了采用优化器提示中的二元操作符分发方式的情况。

```
//分组group片段，正要探测第2个需要分发的group。
*** probe best distribute method for group(000000000070E1F8), DPC motion nth_try[2], restricts[0xd], self cost 1.000000
  left motion cost: broadcast = 236.980132, dis = 236.980132, gather = 236.980132
  self motion cost: broadcast = 0.000006, dis = 0.000006, gather = 0.000006
  > try      X(420), cost 1.100000, n_parallel 0, n_sites 0, best*
  > try   X_GAT_X(424), cost 3.000006, n_parallel 0, n_sites 0
  > try    GAT_X(425), cost 239.980132, n_parallel 0, n_sites 0
 
//排序order片段，正要探测第7个需要分发的order。
*** probe best distribute method for order(0000000050897128), DPC motion nth_try[7], restricts[0x45], self cost 1.000000
  left motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
  self motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
  > try      X(420), cost 1.100000, n_parallel 0, n_sites 0, best*
  > try    GAT_X(425), cost 3.000011, n_parallel 0, n_sites 0
  > try   X_MERGE(427), cost 12.000011, n_parallel 0, n_sites 0
 
//排序去重distinct片段，正要探测第1个需要分发的distinct。
*** probe best distribute method for distinct(00000000007112E8), DPC motion nth_try[1], restricts[0x1f], self cost 21.828696
  left motion cost: broadcast = 3.638299, dis = 1.819149, gather = 1.819149
  self motion cost: broadcast = 0.036383, dis = 0.018191, gather = 0.018191
  > try      X(420), not available
  > try   X_DIS_X(421), cost 114.344539, n_parallel 1, n_sites 1, best*
  > try    DIS_X(422), cost 2464.784487, n_parallel 1, n_sites 1
  > try   X_GAT_X(424), cost 14.150826, n_parallel 0, n_sites 0, best*
  > try    GAT_X(425), cost 26.647845, n_parallel 0, n_sites 0
 
//分析函数analytic function片段，正要探测第6个需要分发的analytic function。
*** probe best distribute method for analytic function(0000000050899AC0), DPC motion nth_try[6], restricts[0x181], self cost 1.000000
  left motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
  self motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
  > try      X(420), not available
  > try DIS_SORT_X(423), cost 20.000112, n_parallel 1, n_sites 1, best*
  > try GAT_SORT_X(426), cost 4.000011, n_parallel 0, n_sites 0
```

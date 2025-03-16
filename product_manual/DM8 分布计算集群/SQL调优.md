

# SQL调优

DM 查询优化器采用基于代价的方法。在估计代价时，主要以统计信息或者数据分布为依据。通常情况下，估计的代价都是准确的。但在一些估算代价的依据极度缺乏的情况下，例如：缺少统计信息、或统计信息陈旧、或抽样数据不能很好地反映数据分布时，优化器选择的执行计划不是最优的，甚至可能是很差的执行计划。

然而 DBA 对于数据分布是很清楚的，并且知道 SQL 语句按照哪种方法执行会最快。在面临上述优化器选择的执行计划不是最优的情况下，DBA 可以主动进行人工干预，指示优化器按照指定的方法去选择 SQL 的执行计划。

这种人工干预优化器的方法称为 HINT。优化器可根据 DBA 的 HINT 提示来生成指定的执行计划。如果优化器无法根据给定的 HINT 生成相应的执行计划，那么将忽略该 HINT。

HINT 的常见格式如下所示：

```
SELECT /*+ HINT1 [HINT2]*/  列名 FROM 表名 WHERE_CLAUSE ;
UPDATE 表名 /*+ HINT1 [HINT2]*/ SET 列名 =变量 WHERE_CLAUSE ;
DELETE FROM 表名  /*+ HINT1 [HINT2]*/ WHERE_CLAUSE ;
```

需要注意的是：如果 HINT 的语法没有写对或指定的值不正确，DM 并不会报错，而是直接忽略 HINT 继续执行。

## 11.1 DMDPC 数据分发方式提示

DMDPC 环境下提供了一种对指定的连接、分组、排序、去重操作符数据分发方式进行人工干预的优化器提示。该优化器提示被采纳的前提是指定的分发路径有效，因为代价原因没有被优化器选中。

语法：

```
/*+DPC(分发方式探测序号 分发方式字符串)*/
```

其中分发方式探测序号可以在 10053 trace event 生成的 TRACE 文件中查看到，以 nth_try 标示；分发方式请参考 [11.1.1 二元操作符](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-sql-tuning.html#11.1.1%20%E4%BA%8C%E5%85%83%E6%93%8D%E4%BD%9C%E7%AC%A6)和 [11.1.2 一元操作符](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-sql-tuning.html#11.1.2%20%E4%B8%80%E5%85%83%E6%93%8D%E4%BD%9C%E7%AC%A6)。

例  在 SQL 语句中使用 DMDPC 数据分发方式提示。

```
select /*+ DPC(1 x_merge) */ para_name from v$dm_ini order by para_name;
```

### 11.1.1  二元操作符

对于连接和集合运算二元操作符，可供选择的分发方式字符串参见表 11.1。

表11.1 二元操作符的分发方式字符串

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
|ONE_PART_ONLY|二元操作符一侧为普通表，另一侧将数据发送到此普通表所在的站点执行|


例 下面一段 trace 片段展示了采用优化器提示中的二元操作符分发方式的情况。

```
//连接片段，正在试探第5个需要分发的操作符hash left semi join(anti)。
*** probe best distribute method for hash left semi join(anti)(000000005089E500), DPC motion nth_try[5], restricts[0x12], self cost 1.000000
	left  motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
	right motion cost: broadcast = 0.000023, dis = 0.000011, gather = 0.000011
	> try       R_BRO(404), cost 0.062523, n_parallel 2, n_sites 0, best*
	> try L_DIS_R_DIS(405), cost 0.062523, n_parallel 2, n_sites 0, best*
	> try L_GAT_R_GAT(408), cost 1.000023, n_parallel 1, n_sites 0
```

### 11.1.2 一元操作符

对于分组（group）、排序（order）、去重（distinct）和分析函数（analytic function）一元操作符，可供选择的分发方式字符串参见表 11.2。

下表中的 X 代表 group、order、distinct 或 analytic function。例如：当按照 group 进行分发时，x 即表示 group。

表11.2 一元操作符的分发方式字符串

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
	left  motion cost: broadcast = 236.980132, dis = 236.980132, gather = 236.980132
	self  motion cost: broadcast = 0.000006, dis = 0.000006, gather = 0.000006
	> try           X(420), cost 1.100000, n_parallel 0, n_sites 0, best*
	> try     X_GAT_X(424), cost 3.000006, n_parallel 0, n_sites 0
	> try       GAT_X(425), cost 239.980132, n_parallel 0, n_sites 0

//排序order片段，正要探测第7个需要分发的order。
*** probe best distribute method for order(0000000050897128), DPC motion nth_try[7], restricts[0x45], self cost 1.000000
	left  motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
	self  motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
	> try           X(420), cost 1.100000, n_parallel 0, n_sites 0, best*
	> try       GAT_X(425), cost 3.000011, n_parallel 0, n_sites 0
	> try     X_MERGE(427), cost 12.000011, n_parallel 0, n_sites 0

//排序去重distinct片段，正要探测第1个需要分发的distinct。
*** probe best distribute method for distinct(00000000007112E8), DPC motion nth_try[1], restricts[0x1f], self cost 21.828696
	left  motion cost: broadcast = 3.638299, dis = 1.819149, gather = 1.819149
	self  motion cost: broadcast = 0.036383, dis = 0.018191, gather = 0.018191
	> try           X(420), not available
	> try     X_DIS_X(421), cost 114.344539, n_parallel 1, n_sites 1, best*
	> try       DIS_X(422), cost 2464.784487, n_parallel 1, n_sites 1
	> try     X_GAT_X(424), cost 14.150826, n_parallel 0, n_sites 0, best*
	> try       GAT_X(425), cost 26.647845, n_parallel 0, n_sites 0

//分析函数analytic function片段，正要探测第6个需要分发的analytic function。
*** probe best distribute method for analytic function(0000000050899AC0), DPC motion nth_try[6], restricts[0x181], self cost 1.000000
	left  motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
	self  motion cost: broadcast = 0.000011, dis = 0.000011, gather = 0.000011
	> try           X(420), not available
	> try  DIS_SORT_X(423), cost 20.000112, n_parallel 1, n_sites 1, best*
	> try  GAT_SORT_X(426), cost 4.000011, n_parallel 0, n_sites 0
```

## 11.2 HINT 实例

对上述部分 HINT 的用法和效果进行举例说明。

### 11.2.1 数据准备

数据准备。

```
create table t1(c1 int, c2 int) partition by hash(c1) partitions 3;
create table t2(d1 int, d2 int) partition by hash(d1) partitions 4;
```

表 t1，t2 分区方式不同，分别分布于 raft1，raft2 两个实例上。

### 11.2.2 带条件查询

本节优化的对象为带 WHERE 条件的查询语句。

例 1 采用对左侧数据进行广播方式通常适用连接两边数据量相差较大的场合。

```
explain select /*+ dpc(1 l_bro) */ * from t1, t2 where c1 = d1;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(1), n_key(0), recv_in_turn(0)
3       #ESEND: [1, 1, 16]; stask_no(1), type(DIRECT), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-)
4         #PRJT2: [1, 1, 16]; exp_num(4), is_atom(FALSE)
5           #HASH2 INNER JOIN: [1, 1, 16];  KEY_NUM(1); KEY(T1.C1=T2.D1) KEY_NULL_EQU(0)
6             #ERECV: [1, 1, 8]; stask_no(1), l_stask_no(0), n_key(0), recv_in_turn(0)
7               #ESEND: [1, 1, 8]; stask_no(0), type(BROADCAST), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
8                 #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
9                   #CSCN2: [1, 1, 8]; INDEX33651668(T1)
10            #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
11              #CSCN2: [1, 1, 8]; INDEX33651672(T2)
```

例 2 哈希连接一般选择小表一次建立哈希表，对右侧进行广播发送不太常见，但右侧广播可以保留左侧的分布特性，某些场合下有用。

```
explain select /*+ dpc(1 r_bro) */ * from t1, t2 where c1 = d1;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(0), n_key(0), recv_in_turn(0)
3       #ESEND: [1, 1, 16]; stask_no(0), type(DIRECT), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
4         #PRJT2: [1, 1, 16]; exp_num(4), is_atom(FALSE)
5           #HASH2 INNER JOIN: [1, 1, 16];  KEY_NUM(1); KEY(T1.C1=T2.D1) KEY_NULL_EQU(0)
6             #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
7               #CSCN2: [1, 1, 8]; INDEX33651668(T1)
8             #ERECV: [1, 1, 8]; stask_no(0), l_stask_no(1), n_key(0), recv_in_turn(0)
9               #ESEND: [1, 1, 8]; stask_no(1), type(BROADCAST), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
10                #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
11                  #CSCN2: [1, 1, 8]; INDEX33651672(T2)
```

例 3 连接两侧分别加入分发操作，可以使连接操作较好地利用并行特性，是一种常见的分发策略。

```
explain select  /*+ dpc(1 l_dis_r_dis) */ * from t1, t2 where c1 = d1;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(1), n_key(0), recv_in_turn(0)
3       #ESEND: [1, 1, 16]; stask_no(1), type(DIRECT), sites(3:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
4         #PRJT2: [1, 1, 16]; exp_num(4), is_atom(FALSE)
5           #HASH2 INNER JOIN: [1, 1, 16];  KEY_NUM(1); KEY(T1.C1=T2.D1) KEY_NULL_EQU(0)
6             #ERECV: [1, 1, 8]; stask_no(1), l_stask_no(0), n_key(0), recv_in_turn(0)
7               #ESEND: [1, 1, 8]; stask_no(0), type(N_DEST), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-) ,keys(T1.C1) ; INFO_BITS(0x4)
8                 #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
9                   #CSCN2: [1, 1, 8]; INDEX33651668(T1)
10            #ERECV: [1, 1, 8]; stask_no(1), l_stask_no(2), n_key(0), recv_in_turn(0)
11              #ESEND: [1, 1, 8]; stask_no(2), type(N_DEST), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-) ,keys(T2.D1)
12                #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
13                  #CSCN2: [1, 1, 8]; INDEX33651672(T2)
```

例 4 仅左侧分发，此处分发会按照右孩子的数据分布特征进行。保留了右孩子原始的分布特性，是一种部分分区连接优化。

```
explain select  /*+ dpc(1 l_dis) */ * from t1, t2 where c1 = d1;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(1), n_key(0), recv_in_turn(0)
3       #ESEND: [1, 1, 16]; stask_no(1), type(DIRECT), sites(2:2,1:2), sql_invoke(0), pwj_opt(0), table(-)
4         #PRJT2: [1, 1, 16]; exp_num(4), is_atom(FALSE)
5           #HASH2 INNER JOIN: [1, 1, 16];  KEY_NUM(1); KEY(T1.C1=T2.D1) KEY_NULL_EQU(0)
6             #ERECV: [1, 1, 8]; stask_no(1), l_stask_no(0), n_key(0), recv_in_turn(0)
7               #ESEND: [1, 1, 8]; stask_no(0), type(HASH), sites(2:1,1:1), sql_invoke(0), pwj_opt(1), table(T2) ,keys(T1.C1) ; INFO_BITS(0x4)
8                 #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
9                   #CSCN2: [1, 1, 8]; INDEX33651668(T1)
10            #GI: [1, 1, 8]; policy(USE_SQC_NO), gi_unit[0..0], scan_type[0](FULL)
11              #CSCN2: [1, 1, 8]; INDEX33651672(T2)
```

例 5 仅右侧分发，此处分发会按照左孩子的数据分布特征进行。保留了左孩子原始的分布特性，是一种部分分区智能连接优化。

```
explain select /*+ dpc(1 r_dis) */ * from t1, t2 where c1 = d1;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(0), n_key(0), recv_in_turn(0)
3       #ESEND: [1, 1, 16]; stask_no(0), type(DIRECT), sites(2:2,1:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
4         #PRJT2: [1, 1, 16]; exp_num(4), is_atom(FALSE)
5           #HASH2 INNER JOIN: [1, 1, 16];  KEY_NUM(1); KEY(T1.C1=T2.D1) KEY_NULL_EQU(0)
6             #GI: [1, 1, 8]; policy(USE_SQC_NO), gi_unit[0..0], scan_type[0](FULL)
7               #CSCN2: [1, 1, 8]; INDEX33651668(T1)
8             #ERECV: [1, 1, 8]; stask_no(0), l_stask_no(1), n_key(0), recv_in_turn(0)
9               #ESEND: [1, 1, 8]; stask_no(1), type(HASH), sites(2:1,1:1), sql_invoke(0), pwj_opt(1), table(T1) ,keys(T2.D1) ; INFO_BITS(0x4)
10                #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
11                  #CSCN2: [1, 1, 8]; INDEX33651672(T2)
```

例 6 两侧数据汇总到一个线程进行，通常适合两边都是小表的情况，汇总后数据仅在 SP 上。

```
explain select  /*+ dpc(1 l_gat_r_gat) */ * from t1, t2 where c1 = d1;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(1), n_key(0), recv_in_turn(0)
3       #ESEND: [1, 1, 16]; stask_no(1), type(DIRECT), sites(3:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
4         #PRJT2: [1, 1, 16]; exp_num(4), is_atom(FALSE)
5           #HASH2 INNER JOIN: [1, 1, 16];  KEY_NUM(1); KEY(T1.C1=T2.D1) KEY_NULL_EQU(0)
6             #ERECV: [1, 1, 8]; stask_no(1), l_stask_no(0), n_key(0), recv_in_turn(0)
7               #ESEND: [1, 1, 8]; stask_no(0), type(DIRECT), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
8                 #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
9                   #CSCN2: [1, 1, 8]; INDEX33651668(T1)
10            #ERECV: [1, 1, 8]; stask_no(1), l_stask_no(2), n_key(0), recv_in_turn(0)
11              #ESEND: [1, 1, 8]; stask_no(2), type(DIRECT), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-)
12                #GI: [1, 1, 8]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
13                  #CSCN2: [1, 1, 8]; INDEX33651672(T2)
```

### 11.2.3 分组查询

本节优化的对象为分组查询语句。

例 1 适合本地分组后数量能减少较多的场合。

```
explain select /*+ dpc(1 x_dis_x) stat(t1 1G) parallel(4) */ count(*) from t1 group by c2;
```

结果如下：

```
1   #NSET2: [169968, 10000000, 4]
2     #ERECV: [169968, 10000000, 4]; stask_no(-1), l_stask_no(1), n_key(0), recv_in_turn(0)
3       #ESEND: [169968, 10000000, 4]; stask_no(1), type(DIRECT), sites(3:4), sql_invoke(0), pwj_opt(0), table(-)
4         #PRJT2: [169968, 10000000, 4]; exp_num(1), is_atom(FALSE)
5           #HAGR2: [169968, 10000000, 4]; grp_num(1), sfun_num(1), distinct_flag[0]; slave_empty(0) keys(T1.C2)
6             #ERECV: [169968, 10000000, 4]; stask_no(1), l_stask_no(0), n_key(0), recv_in_turn(0)
7               #ESEND: [169968, 10000000, 4]; stask_no(0), type(N_DEST), sites(2:4,1:4), sql_invoke(0), pwj_opt(0), table(-) ,keys(T1.C2) ; INFO_BITS(0x4)
8                 #HAGR2: [169968, 10000000, 4]; grp_num(1), sfun_num(1), distinct_flag[0]; slave_empty(0) keys(T1.C2)
9                   #GI: [105723, 1000000000, 4]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
10                    #CSCN2: [105723, 1000000000, 4]; INDEX33651668(T1)
```

例 2 dis_x 适合本地分组并能不能就减少太多数据的场合。

```
explain select  /*+ dpc(1 dis_x) */ count(*) from t1 group by c2;
```

结果如下：

```
1   #NSET2: [1, 1, 4]
2     #PRJT2: [1, 1, 4]; exp_num(1), is_atom(FALSE)
3       #HAGR2: [1, 1, 4]; grp_num(1), sfun_num(1), distinct_flag[0]; slave_empty(0) keys(T1.C2)
4         #ERECV: [1, 1, 4]; stask_no(-1), l_stask_no(0), n_key(0), recv_in_turn(0)
5           #ESEND: [1, 1, 4]; stask_no(0), type(N_DEST), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-) ,keys(T1.C2) ; INFO_BITS(0x4)
6             #GI: [1, 1, 4]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
7               #CSCN2: [1, 1, 4]; INDEX33651668(T1)
```

例 3 适合本地分组后数据量较少的情况。

```
explain select /*+ dpc(1 x_gat_x) stat(t1 1G) parallel(4) */ count(*) from t1 group by c2;
```

结果如下：

```
1   #NSET2: [169968, 10000000, 4]
2     #PRJT2: [169968, 10000000, 4]; exp_num(1), is_atom(FALSE)
3       #HAGR2: [169968, 10000000, 4]; grp_num(1), sfun_num(1), distinct_flag[0]; slave_empty(0) keys(T1.C2)
4         #ERECV: [169968, 10000000, 4]; stask_no(-1), l_stask_no(0), n_key(0), recv_in_turn(0)
5           #ESEND: [169968, 10000000, 4]; stask_no(0), type(DIRECT), sites(2:4,1:4), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
6             #HAGR2: [169968, 10000000, 4]; grp_num(1), sfun_num(1), distinct_flag[0]; slave_empty(0) keys(T1.C2)
7               #GI: [105723, 1000000000, 4]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
8                 #CSCN2: [105723, 1000000000, 4]; INDEX33651668(T1)
```

例 4 适合数据量较少，全部汇总到一个线程后再进行分组。

```
explain select /*+ dpc(1 gat_x) stat(t1 1G) parallel(4) */ count(*) from t1 group by c2;
```

结果如下：

```
1   #NSET2: [169968, 10000000, 4]
2     #PRJT2: [169968, 10000000, 4]; exp_num(1), is_atom(FALSE)
3       #HAGR2: [169968, 10000000, 4]; grp_num(1), sfun_num(1), distinct_flag[0]; slave_empty(0) keys(T1.C2)
4         #ERECV: [105723, 1000000000, 4]; stask_no(-1), l_stask_no(0), n_key(0), recv_in_turn(0)
5           #ESEND: [105723, 1000000000, 4]; stask_no(0), type(DIRECT), sites(2:4,1:4), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
6             #GI: [105723, 1000000000, 4]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
7               #CSCN2: [105723, 1000000000, 4]; INDEX33651668(T1)
```

### 11.2.4 排序查询

本节优化的对象为分组排序语句。

例 1 适合数据量较少，全部汇总到一个线程后再进行排序。

```
explain select /*+ dpc(1 gat_x) */ * from t1 order by c2;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #PRJT2: [1, 1, 16]; exp_num(3), is_atom(FALSE)
3       #SORT3: [1, 1, 16]; key_num(1), is_distinct(FALSE), top_flag(0), is_adaptive(0)
4         #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(0), n_key(0), recv_in_turn(0)
5           #ESEND: [1, 1, 16]; stask_no(0), type(DIRECT), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x4)
6             #GI: [1, 1, 16]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
7               #CSCN2: [1, 1, 16]; INDEX33651668(T1)
```

例 2 适合数据量较多，每路数据各自排序后汇总到一个线程后进行最终的归并排序。

```
explain select /*+ dpc(1 x_merge) */ * from t1 order by c2;
```

结果如下：

```
1   #NSET2: [1, 1, 16]
2     #PRJT2: [1, 1, 16]; exp_num(3), is_atom(FALSE)
3       #ERECV: [1, 1, 16]; stask_no(-1), l_stask_no(0), n_key(1), recv_in_turn(0)
4         #ESEND: [1, 1, 16]; stask_no(0), type(DIRECT), sites(2:1,1:1), sql_invoke(0), pwj_opt(0), table(-); INFO_BITS(0x24)
5           #SORT3: [1, 1, 16]; key_num(1), is_distinct(FALSE), top_flag(0), is_adaptive(0)
6             #GI: [1, 1, 16]; policy(RANDOM), gi_unit[0..0], scan_type[0](FULL)
7               #CSCN2: [1, 1, 16]; INDEX33651668(T1)
```

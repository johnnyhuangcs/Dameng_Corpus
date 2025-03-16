

# DBMS_STATS 包

优化统计信息描述了数据库中的对象细节。查询优化使用这些信息选择最合适的执行计划。使用 DBMS_STATS 包来收集统计、删除信息，将收集的统计信息记录在数据字典中。

## 20.1 数据类型

DBMS_STATS 包中涉及到类型。如下统一说明。

ObjectElem 类型是 DBMS_STATS 专有类型。用户不能引用和改变该记录的内容。ObjectElem 记录类型定义如下：

```
TYPE OBJECTELEM IS RECORD (
	OWNNAME     		VARCHAR(128) , 
	OBJTYPE     		VARCHAR(6) , 
	OBJNAME    			VARCHAR(128) , 
	PARTNAME    		VARCHAR(128) ,  
	SUBPARTNAME 		VARCHAR(128)  
) ; 
```

参数详解

  * ownname

模式名。

  * objtype

对象类型，table 或 index。

  * objname

对象（表或索引）名称，区分大小写。

  * partname

分区名称，区分大小写。

  * subpartname

子分区名称，区分大小写。



例 ObjectTab 为 ObjectElem 类型的索引表：

```
TYPE ObjectTab is TABLE of ObjectElem INDEX BY INT;
```

## 20.2 相关方法

DBMS_STATS 包中包含的过程和函数如下详细介绍：

  1. COLUMN_STATS_SHOW



根据模式名，表名和列名获得该列的统计信息。用于经过 GATHER_TABLE_STATS 或 GATHER_SCHEMA_STATS 收集之后展示。返回两个结果集：一个是列的统计信息；另一个是直方图的统计信息。

语法如下：

```
PROCEDURE COLUMN_STATS_SHOW (
	OWNNAME		IN  		VARCHAR(128), 
	TABNAME		IN  		VARCHAR(128),
	COLNAME		IN  		VARCHAR(128)
);
```

列的统计信息和直方图的统计信息，格式分别如下表 20.1、表 20.2：

表20.1 列统计信息

|**名称**|**解释**|
|--|--|
|NUM_DISTINCT|不同列值的个数|
|LOW_VALUE|列最小值|
|HIGH_VALUE|列最大值|
|NUM_NULLS|空值的个数|
|NUM_BUCKETS|直方图桶的个数|
|SAMPLE_SIZE|样本容量|
|HISTOGRAM|直方图的类型|


表20.2 直方图的统计信息

|**名称**|**解释**|
|--|--|
|OWNER|模式名|
|TABLE_NAME|表名|
|COLUMN_NAME|列名|
|HISTOGRAM|直方图类型|
|ENDPOINT_VALUE|样本值|
|ENDPOINT_HEIGHT|对于频率直方图，样本值的个数；对于等高直方图，小于样本值大于前一个样本值的个数。|
|ENDPOINT_KEYHEIGHT|对于频率直方图无效；对于等高直方图，样本值的个数。|
|ENDPOINT_DISTINCT|对于频率直方图无效；对于等高直方图，小于样本值大于前一个样本值之间不同样本的个数。|


参数详解

  * OWNNAME

模式名，区分大小写。

  * TABNAME

表名，区分大小写。

  * COLNAME

列名，区分大小写。



  2. TABLE_STATS_SHOW



根据模式名，表名获得该表的统计信息。用于经过 GATHER_TABLE_STATS 或 GATHER_SCHEMA_STATS 收集之后展示。

语法如下：

```
PROCEDURE TABLE_STATS_SHOW (
	OWNNAME		IN  		VARCHAR(128), 
	TABNAME		IN  		VARCHAR(128)
);
```

表的统计信息，格式如下表 20.3：

表 20.3 表统计信息

|**名称**|**解释**|
|--|--|
|NUM_ROWS|表的总行数|
|LEAF_BLOCKS|总的页数|
|LEAF_USED_BLOCKS|已经使用的页数|


参数详解

  * OWNNAME

模式名，区分大小写。

  * TABNAME

表名，区分大小写。



  3. INDEX_STATS_SHOW



根据模式名，索引名获得该索引的统计信息。用于经过 GATHER_TABLE_STATS、GATHER_INDEX_STATS 或 GATHER_SCHEMA_STATS 收集之后展示。返回两个结果集：一个是索引的统计信息；另一个是直方图的统计信息。

语法如下：

```
PROCEDURE INDEX_STATS_SHOW (
	OWNNAME		IN  	VARCHAR(128), 
	INDEXNAME	IN 	 	VARCHAR(128)
);
```

索引的统计信息和直方图的统计信息，格式分别如下表 20.4、表 20.5：

表 20.4 索引统计信息

|**名称**|**解释**|
|--|--|
|BLEVEL|B_Tree 的层次|
|LEAF_BLOCKS|页数|
|DISTINCT_KEYS|不同样本的个数|
|CLUSTERING_FACTOR|聚集因子，表示索引的数据分布与物理分布之间的关系，暂不支持|
|NUM_ROWS|行数|
|SAMPLE_SIZE|样本容量|


表 20.5 直方图的统计信息

|**名称**|**解释**|
|--|--|
|OWNER|模式名|
|NAME|索引名|
|COLUMN_NAME|列名|
|HISTOGRAM|直方图类型|
|ENDPOINT_VALUE|样本值|
|ENDPOINT_HEIGHT|对于频率直方图，样本值的个数；对于等高直方图，小于样本值大于前一个样本值的个数。|
|ENDPOINT_KEYHEIGHT|对于频率直方图无效；对于等高直方图，样本值的个数。|
|ENDPOINT_DISTINCT|对于频率直方图无效；对于等高直方图，小于样本值大于前一个样本值之间不同样本的个数。|


参数详解

  * OWNNAME

模式名，区分大小写。

  * INDEXNAME

索引名，区分大小写。



  4. GATHER_TABLE_STATS



根据设定的参数，收集表、表中的列和表上的索引的统计信息。其中，对于表，只搜集表的总行数、总的页数、已经使用的页数等基本信息。

语法如下：

```
PROCEDURE GATHER_TABLE_STATS (
	OWNNAME			VARCHAR(128), 
	TABNAME			VARCHAR(128), 
	PARTNAME		VARCHAR(128)	DEFAULT NULL,
	ESTIMATE_PERCENT	DOUBLE		DEFAULT TO_ESTIMATE_PERCENT_TYPE(GET_PREFS('ESTIMATE_PERCENT')), 
	BLOCK_SAMPLE	BOOLEAN  	DEFAULT FALSE,
	METHOD_OPT		VARCHAR  	DEFAULT GET_PREFS('METHOD_OPT'),
	DEGREE			INT       	DEFAULT TO_DEGREE_TYPE(GET_PREFS('DEGREE')),
	GRANULARITY		VARCHAR  	DEFAULT GET_PREFS('GRANULARITY'), 
	CASCADE			BOOLEAN  	DEFAULT TO_CASCADE_TYPE(GET_PREFS('CASCADE')),
	STATTAB			VARCHAR  	DEFAULT NULL, 
	STATID			VARCHAR  	DEFAULT NULL,
	STATOWN			VARCHAR  	DEFAULT NULL,
	NO_INVALIDATE	BOOLEAN		DEFAULT TO_NO_INVALIDATE_TYPE(GET_PREFS('NO_INVALIDATE')),
	FORCE 			BOOLEAN  	DEFAULT FALSE
	MERGE_STAT       BOOLEAN  DEFAULT FALSE
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * TABNAME

表名，区分大小写。

  * PARTNAME

分区表名，缺省为 NULL，区分大小写。

  * ESTIMATE_PERCENT

收集的百分比，范围为 0~100，低于 0.000001 时按 0.000001 计算。默认系统自定。

  * BLOCK_SAMPLE

TRUE 表示以数据页为单位采样；FALSE 表示以数据行为单位采样。缺省为 FALSE。

  * METHOD_OPT

控制列的统计信息集合和直方图的创建的格式，缺省为 FOR ALL COLUMNS SIZE AUTO。其中
BLOB、IMAGE、LONGVARBINARY、CLOB、TEXT、LONGVARCHAR、自定义类型列和空间类型列等列类型不能被收集。格式选项如下：



```
FOR ALL [INDEXED | HIDDEN] COLUMNS [<size_clause>]<size_clause>：：= SIZE {INTEGER | REPEAT | AUTO | SKEWONLY}
或者
FOR COLUMNS[<size clause>] <<column_name>|[<size_clause>]>{,<column_name | [<size_clause>]>}
```

各参数解释如下：

  * INDEXED | HIDDEN 表示只统计索引或者隐藏的列，缺省为都统计。

  * INTEGER 直方图的桶数，取值范围 1~10000。

  * REPEAT 只统计已经有直方图的列。

  * AUTO 根据数据分布和工作量自动决定统计直方图的列。

  * SKEWONLY 根据数据分布决定统计直方图的列。

  * DEGREE

收集的并行度，缺省为 1。

  * GRANULARITY

收集的粒度，默认为 Auto；GRANULARITY 可选参数如下：AUTO | DEFAULT |ALL | PARTITION |
SUBPARTITION | GLOBAL | GLOBAL AND PARTITION。

各参数解释如下：

◆ALL 收集全部的统计信息（subpartition，partition 和 global）。

◆AUTO 缺省值，根据分区的类型来决定如何收集。

◆DEFAULT 和 auto 功能相同。

◆GLOBAL 收集 global 表的统计信息。

◆GLOBAL AND PARTITION 收集 GLOBAL 和 PARTITION 表的统计信息。

◆PARTITION 收集 PARTITION 表的统计信息。

◆SUBPARTITION 收集 subpartition 表的统计信息。当子分区表个数超过 50 时，因为采用跳跃采样，所以统计信息会有误差。即使采样率为
100，也还是会有误差。可以通过减少统计层次的方式降低这种误差，即将 granularity=>'subpartition'修改成
granularity=>'GLOBAL AND PARTITION'或 granularity=>'GLOBAL'。

  * CASCADE

是否收集索引信息，TRUE 或 FALSE，默认为 TRUE。当表对象类型为 HUGE 表时，CASCADE 参数应置为 FALSE。

  * STATTAB

统计信息存放的表，默认为 NULL。

  * STATID

统计信息的 ID，默认为 NULL。

  * STATOWN

统计信息的模式，默认为 NULL。

  * NO_INVALIDATE

保留参数，是否让依赖游标失效，缺省为 TRUE。NO_INVALIDATE 可选参数如下:TRUE | FALSE |
DBMS_STATS.AUTO_INVALIDATE。

各参数解释如下：

◆ TRUE 游标不失效，原有的执行计划保持原状态。

◆ FALSE 游标失效，移除原有的相关执行计划。

◆ DBMS_STATS.AUTO_INVALIDATE 和 TRUE 用法相同。

  * force

保留参数，是否强制收集统计信息，默认为 FALSE。

  * merge_stat

为分区子表收集统计信息后，是否级联更新祖先对应的统计信息，TRUE 是，FALSE 否；缺省为 FALSE。



  5. GATHER_INDEX_STATS



根据设定的参数，收集索引的统计信息。

语法如下：

```
PROCEDURE GATHER_INDEX_STATS (
	OWNNAME				VARCHAR(128), 
	INDNAME				VARCHAR(128), 
	PARTNAME			VARCHAR(128)	DEFAULT NULL,
	ESTIMATE_PERCENT	DOUBLE	DEFAULT TO_ESTIMATE_PERCENT_TYPE(GET_PREFS('ESTIMATE_PERCENT')),
	STATTAB				VARCHAR	DEFAULT NULL, 
	STATID				VARCHAR	DEFAULT NULL,
	STATOWN				VARCHAR	DEFAULT NULL,
	DEGREE				INT		DEFAULT TO_DEGREE_TYPE(GET_PREFS('DEGREE')),
	GRANULARITY			VARCHAR	DEFAULT GET_PREFS('GRANULARITY'),
	NO_INVALIDATE		BOOLEAN  DEFAULT TO_NO_INVALIDATE_TYPE(GET_PREFS('NO_INVALIDATE')),
	FORCE				BOOLEAN	DEFAULT FALSE
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * INDNAME

索引名，区分大小写。

  * PARTNAME

分区索引名，缺省为 NULL，区分大小写。

  * ESTIMATE_PERCENT

收集的百分比，范围为 0~100，低于 0.000001 时按 0.000001 计算。默认系统自定。

  * STATTAB

保留参数，统计信息存放的表，缺省为 NULL。

  * STATID

保留参数，统计信息的 ID，缺省为 NULL。

  * STATOWN

保留参数，统计信息的模式，缺省为 NULL。

  * DEGREE

保留参数，收集的并行度，缺省为 1。

  * GRANULARITY

保留参数，收集的粒度，缺省为 ALL。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。具体用法请参考 GATHER_TABLE_STATS 方法的 NO_INVALIDATE 参数。

  * FORCE

保留参数，是否强制收集统计信息，缺省为 FALSE。



  6. GATHER_SCHEMA_STATS



收集模式下对象的统计信息。

语法如下：

```
PROCEDURE GATHER_SCHEMA_STATS (
	OWNNAME				VARCHAR(128), 
	ESTIMATE_PERCENT	DOUBLE	DEFAULT TO_ESTIMATE_PERCENT_TYPE(GET_PREFS('ESTIMATE_PERCENT')), 
	BLOCK_SAMPLE		BOOLEAN	DEFAULT FALSE, 
	METHOD_OPT			VARCHAR	DEFAULT GET_PREFS('METHOD_OPT'),
	DEGREE				INT		DEFAULT TO_DEGREE_TYPE(GET_PREFS('DEGREE')), 
	GRANULARITY			VARCHAR	DEFAULT GET_PREFS('GRANULARITY'), 
	CASCADE				BOOLEAN	DEFAULT TO_CASCADE_TYPE(GET_PREFS('CASCADE')), 
	STATTAB				VARCHAR	DEFAULT NULL, 
	STATID				VARCHAR	DEFAULT NULL, 
	OPTIONS				VARCHAR	DEFAULT 'GATHER', 
	OBJLIST		OUT  	OBJECTTAB DEFAULT NULL,
	STATOWN				VARCHAR	DEFAULT NULL, 
	NO_INVALIDATE		BOOLEAN	DEFAULT TO_NO_INVALIDATE_TYPE(GET_PREFS('NO_INVALIDATE')),
	FORCE				BOOLEAN	DEFAULT FALSE,
	OBJ_FILTER_LIST		OBJECTTAB DEFAULT NULL
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * ESTIMATE_PERCENT

收集的百分比，范围为 0~100，低于 0.000001 时按 0.000001 计算。默认系统自定。

  * BLOCK_SAMPLE

TRUE 表示以数据页为单位采样；FALSE 表示以数据行为单位采样。缺省为 FALSE。

  * METHOD_OPT

控制列的统计信息集合和直方图的创建；缺省为 FOR ALL COLUMNS SIZE AUTO；只支持其中一种格式：



```
FOR ALL[INDEXED | HIDDEN] COLUMNS [<size_clause>]
<size_clause>：:= SIZE {integer | REPEAT | AUTO | SKEWONLY}
```

各参数解释如下：

  * INDEXED | HIDDEN 表示只统计索引或者隐藏的列，缺省为都统计。

  * INTEGER 直方图的桶数，取值范围 1~10000。

  * REPEAT 只统计已经有直方图的列。

  * AUTO 根据数据分布和工作量自动决定统计直方图的列。

  * SKEWONLY 根据数据分布决定统计直方图的列。

  * DEGREE

收集的并行度，缺省为 1。

  * GRANULARITY

收集的粒度，缺省为 ALL。

  * CASCADE

是否收集索引信息，TRUE 或 FALSE。缺省为 TRUE。

  * STATTAB

统计信息存放的表，缺省为 NULL。

  * STATID

统计信息的 ID，默认为 NULL。

  * OPTIONS

控制收集的列，缺省为 GATHER；选项如下：GATHER|GATHER AUTO|GATHER STALE|GATHER EMPTY|LIST
AUTO|LIST STALE|LIST EMPTY。各选项解释如下：

◆GATHER：收集模式下所有对象的统计信息。

◆GATHER AUTO：自动收集需要的统计信息。系统隐含的决定哪些对象需要新的统计信息，以及怎样收集这些统计信息。此时，只有
ownname，stattab， statid，objlist and statown 有效，返回收集统计信息的对象。

◆GATHER STALE：对旧的对象收集统计信息。返回找到的旧的对象。

◆GATHER EMPTY：收集没有统计信息对象的统计信息。返回这些对象。

◆LIST AUTO：返回 GATHER AUTO 方式处理的对象。

◆LIST STALE：返回旧的对象信息。

◆LIST EMPTY：返回没有统计信息的对象。

  * OBJLIST

返回 OPTION 选项对应的链表，缺省为 NULL。

  * STATOWN

统计信息的模式，缺省为 NULL。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。具体用法请参考 GATHER_TABLE_STATS 方法的 NO_INVALIDATE 参数。

  * FORCE

保留参数，是否强制收集统计信息，缺省为 FALSE。

  * OBJ_FILTER_LIST

存放过滤条件的模式名、表名和子表名，缺省为 NULL。



  7. DELETE_TABLE_STATS



根据设定参数，删除与表相关对象的统计信息。

语法如下：

```
PROCEDURE DELETE_TABLE_STATS (
	OWNNAME				VARCHAR(128), 
	TABNAME				VARCHAR(128), 
	PARTNAME			VARCHAR(128)	DEFAULT NULL,
	STATTAB				VARCHAR			DEFAULT NULL, 
	STATID				VARCHAR			DEFAULT NULL,
	CASCADE_PARTS		BOOLEAN			DEFAULT TRUE, 
	CASCADE_COLUMNS		BOOLEAN			DEFAULT TRUE,
	CASCADE_INDEXES		BOOLEAN			DEFAULT TRUE,
	STATOWN				VARCHAR			DEFAULT NULL,
	NO_INVALIDATE		BOOLEAN			DEFAULT TO_NO_INVALIDATE_TYPE(GET_PREFS('NO_INVALIDATE')),
	FORCE				BOOLEAN 			DEFAULT FALSE
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * TABNAME

表名，区分大小写。

  * PARTNAME

分区表名，缺省为 NULL，区分大小写。

  * STATTAB

保留参数，统计信息存放的表，缺省为 NULL。

  * STATID

保留参数，统计信息的 ID，缺省为 NULL。

  * CASCADE_PARTS

是否级联删除分区表信息，缺省为 TRUE。

  * CASCADE_COLUMNS

是否级联删除表中列的信息，取值 TRUE 或 FALSE。缺省为 TRUE。

  * CASCADE_INDEXES

是否级联删除表的索引信息，取值 TRUE 或 FALSE。缺省为 TRUE。

  * STATOWN

保留参数，统计信息的模式，缺省为 NULL。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。具体用法请参考 GATHER_TABLE_STATS 方法的 NO_INVALIDATE 参数。

  * FORCE

保留参数，是否强制收集统计信息，缺省为 FALSE。



  8. DELETE_SCHEMA_STATS



根据设定参数，删除模式下对象的统计信息。

语法如下：

```
PROCEDURE DELETE_SCHEMA_STATS (
	OWNNAME			VARCHAR(128), 
	STATTAB			VARCHAR 		DEFAULT NULL, 
	STATID			VARCHAR 		DEFAULT NULL,
	STATOWN			VARCHAR 		DEFAULT NULL,
	NO_INVALIDATE	BOOLEAN		DEFAULT TO_NO_INVALIDATE_TYPE(GET_PREFS('NO_INVALIDATE')),
	FORCE			BOOLEAN 		DEFAULT FALSE
); 
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * STATTAB

保留参数，统计信息存放的表，缺省为 NULL。

  * STATID

保留参数，统计信息的 ID，缺省为 NULL。

  * STATOWN

保留参数，统计信息的模式，缺省为 NULL。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。具体用法请参考 GATHER_TABLE_STATS 方法的 NO_INVALIDATE 参数。

  * FORCE

保留参数，是否强制收集统计信息，缺省为 FALSE。



  9. DELETE_INDEX_STATS



根据设定参数，删除索引的统计信息。

语法如下：

```
PROCEDURE DELETE_INDEX_STATS (
	OWNNAME			VARCHAR(128), 
	INDNAME			VARCHAR(128),
	PARTNAME		VARCHAR(128) 		DEFAULT NULL,
	STATTAB			VARCHAR 			DEFAULT NULL, 
	STATID			VARCHAR 			DEFAULT NULL,
	CASCADE_PARTS	BOOLEAN  			DEFAULT TRUE,
	STATOWN			VARCHAR 			DEFAULT NULL,
	NO_INVALIDATE	BOOLEAN				DEFAULT TO_NO_INVALIDATE_TYPE(GET_PREFS('NO_INVALIDATE')),
	FORCE			BOOLEAN  			DEFAULT FALSE
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * INDNAME

索引名，区分大小写。

  * PARTNAME

分区索引名，缺省为 NULL，区分大小写。

  * STATTAB

保留参数，统计信息存放的表，缺省为 NULL。

  * STATID

保留参数，统计信息的 ID，缺省为 NULL。

  * CASCADE_PARTS

是否级联删除分区表信息，TRUE 或 FALSE。缺省为 TRUE。

  * STATOWN

保留参数，统计信息的模式，缺省为 NULL。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。具体用法请参考 GATHER_TABLE_STATS 方法的 NO_INVALIDATE 参数。

  * FORCE

保留参数，是否强制收集统计信息，缺省为 FALSE。



  10. DELETE_COLUMN_STATS



根据设定参数，删除列的统计信息。

语法如下：

```
PROCEDURE DELETE_COLUMN_STATS (
	OWNNAME			VARCHAR(128), 
	TABNAME			VARCHAR(128), 
	COLNAME			VARCHAR(128), 
	PARTNAME		VARCHAR(128) 		DEFAULT NULL,
	STATTAB			VARCHAR 			DEFAULT NULL, 
	STATID			VARCHAR 			DEFAULT NULL,
	CASCADE_PARTS	BOOLEAN  			DEFAULT TRUE,
	STATOWN			VARCHAR 			DEFAULT NULL,
	NO_INVALIDATE	BOOLEAN				DEFAULT TO_NO_INVALIDATE_TYPE(GET_PREFS('NO_INVALIDATE')),
	FORCE			BOOLEAN  			DEFAULT FALSE,
	COL_STAT_TYPE	VARCHAR 			DEFAULT 'ALL'
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * TABNAME

表名，区分大小写。

  * COLNAME

列名，区分大小写。

  * PARTNAME

分区表名，默认为 NULL，区分大小写。

  * STATTAB

保留参数，统计信息存放的表，缺省为 NULL。

  * STATID

保留参数，统计信息的 ID，缺省为 NULL。

  * CASCADE_PARTS

是否级联删除分区表信息，TRUE 或 FALSE。缺省为 TRUE。

  * STATOWN

保留参数，统计信息的模式，缺省为 NULL。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。具体用法请参考 GATHER_TABLE_STATS 方法的 NO_INVALIDATE 参数。

  * FORCE

保留参数，是否强制收集统计信息，缺省为 FALSE。

  * COL_STAT_TYPE

保留参数，是否只删除直方图信息，缺省为 ALL。



  11. UPDATE_ALL_STATS



更新已有的统计信息。

语法如下：

```
PROCEDURE UPDATE_ALL_STATS ();
```

  12. CONVERT_RAW_VALUE



把二进制流转换为指定类型。

语法如下：

```
PROCEDURE CONVERT_RAW_VALUE (
	I_RAW 	VARBINARY(8188),
	M_N 	OUT NUMBER
);
```

```
PROCEDURE CONVERT_RAW_VALUE (
	I_RAW 	VARBINARY(8188),
	M_N 	OUT DATETIME
);
PROCEDURE CONVERT_RAW_VALUE (
	I_RAW 	VARBINARY(8188),
	M_N OUT VARCHAR(8188)
);
PROCEDURE CONVERT_RAW_VALUE (
	I_RAW 	VARBINARY(8188),
	M_N 	OUT DOUBLE
);
```

参数详解

  * I_RAW

输入参数，VARBINARY 类型的最大或最小值，期望来源于 SYSSTATS 表的 V_MAX 或 V_MIN 列。

  * M_N

输出参数，由 I_RAW 转换而来，支持 NUMBER、DATETIME、VARCHAR(8188)、DOUBLE 类型。



  13. CREATE_STAT_TABLE



根据用户指定的名称创建一个增加了前缀的临时统计信息表，用于保存待导出的统计信息。统计信息保存到该表中后，可以使用 DM 的数据导入导出工具进行跨实例导入导出。MPP 环境下暂不支持统计信息导入导出功能。

创建的统计信息表需要通过包方法 DROP_STAT_TABLE 进行删除，不支持使用 DROP TABLE 语句删除。

语法如下：

```
PROCEDURE CREATE_STAT_TABLE (
	STATOWN				VARCHAR(128),
	STATTAB				VARCHAR(128),
	TABLESPACE			VARCHAR(128) 		DEFAULT NULL,
	GLOBAL_TEMPORARY	BOOLEAN 			DEFAULT FALSE
);
```

参数详解

  * STATOWN

统计信息表的模式名，区分大小写。

  * STATTAB

统计信息表名，区分大小写，系统实际创建的表会增加前缀。

  * TABLESPACE

表空间名，默认为 NULL，区分大小写。

  * GLOBAL_TEMPORARY

是否创建为会话级的全局临时表。



  14. DROP_STAT_TABLE



删除统计信息表。

语法如下：

```
PROCEDURE DROP_STAT_TABLE (
	STATOWN			VARCHAR(128),
	STATTAB			VARCHAR(128)
);
```

参数详解

  * STATOWN

统计信息表的模式名，区分大小写。

  * STATTAB

创建时指定的统计信息表名，区分大小写。



  15. EXPORT_TABLE_STATS



把目标表的统计信息导出到指定统计信息表中。

语法如下：

```
PROCEDURE EXPORT_TABLE_STATS(
	OWNNAME			VARCHAR(128),
	TABNAME			VARCHAR(128),
	PARTNAME		VARCHAR(128) 	DEFAULT NULL,
	STATTAB			VARCHAR(128),
	STATID			VARCHAR(128) 	DEFAULT '',
	CASCADE			BOOLEAN 	  	DEFAULT TRUE,
	STATOWN			VARCHAR(128) 	DEFAULT NULL,
	STAT_CATEGORY	VARCHAR(128) 	DEFAULT NULL
);
```

参数详解

  * OWNNAME

目标表的模式名，区分大小写。

  * TABNAME

目标表名，区分大小写。

  * PARTNAME

目标表分区名，如果不指定分区，则一起导出所有子表的统计信息，缺省为 NULL。

  * STATTAB

统计信息表名，区分大小写。

  * STATID

由用户指定的统计信息标识名，缺省认为空字符串。

  * CASCADE

是否连列和索引的统计信息一起导出，缺省为 TRUE

  * STATOWN

统计信息表的模式名，区分大小写，缺省为 NULL。

  * STAT_CATEGORY

保留参数，无实际意义。



  16. IMPORT_TABLE_STATS



把统计信息表中的统计信息导入到目标表中。

语法如下：

```
PROCEDURE IMPORT_TABLE_STATS(
	OWNNAME			VARCHAR(128),
	TABNAME			VARCHAR(128),
	PARTNAME		VARCHAR(128) 	DEFAULT NULL,
	STATTAB			VARCHAR(128),
	STATID			VARCHAR(128)	DEFAULT '',
	CASCADE			BOOLEAN 	  	DEFAULT TRUE,
	STATOWN			VARCHAR(128) 	DEFAULT NULL,
	NO_INVALIDATE	BOOLEAN			DEFAULT NULL,
	FORCE			BOOLEAN      	DEFAULT FALSE, 
	STAT_CATEGORY	VARCHAR(128) 	DEFAULT NULL
);
```

参数详解

  * OWNNAME

目标表的模式名，区分大小写。

  * TABNAME

目标表名，区分大小写。

  * PARTNAME

目标表分区名，如果不指定分区，则一起导入所有子表的统计信息，缺省为 NULL。

  * STATTAB

统计信息表名，区分大小写。

  * STATID

由用户指定的统计信息标识名，缺省为空字符串。

  * CASCADE

是否连列和索引的统计信息一起导出，缺省为 TRUE

  * STATOWN

统计信息表的模式名，区分大小写，缺省为 NULL。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。具体用法请参考 GATHER_TABLE_STATS 方法的 NO_INVALIDATE 参数。

  * FORCE

保留参数，无实际意义。

  * STAT_CATEGORY

保留参数，无实际意义。



  17. SET_TABLE_PREFS



该过程设置普通用户表的静态属性值。

语法如下：

```
PROCEDURE SET_TABLE_PREFS (
	OWNNAME		VARCHAR(128),
	TABNAME		VARCHAR(128),
	PPNAME		VARCHAR(128),
	PVALUE		VARCHAR(128)
);
```

参数详解

  * OWNNAME

模式名，不区分大小写。

  * TABNAME

表名，不区分大小写。

  * PPNAME

属性名，不区分大小写。可以取 STALE_PERCENT
、ESTIMATE_PERCENT、METHOD_OPT、DEGREE、GRANULARITY、CASCADE、NO_INVALIDATE、PREFERENCE_OVERRIDES_PARAMETER、MONITOR_MODIFICATIONS_FLAG、MONITOR_MODIFICATIONS_RM_PLN_FLAG
和 MONITOR_MODIFICATIONS_REGATHER_COL。

STALE_PERCENT 决定表的统计信息过时且需要重收集统计信息时表的行数百分比。有效取值范围是非负数，缺省值是 10，即一张表有超过 10%
的数据改变则可认为统计信息过时。取值范围[0,100]，该值置为 0 时，则认为需要将该表的 STALE_PERCENT 属性值置为缺省值。

PREFERENCE_OVERRIDES_PARAMETER 决定是否用静态属性值覆盖参数的输入值。为 TRUE 时，忽略参数的输入值，使用对应的属性值；为
FALSE 时，使用参数的输入值。

MONITOR_MODIFICATIONS_FLAG 决定是否对表进行数据变化监控。为 TRUE 时，监控表的数据变化；为 FALSE
时，不监控表的数据变化。默认值为 FALSE。

MONITOR_MODIFICATIONS_RM_PLN_FLAG
决定对表进行数据变化监控时，当数据变化达到该阈值时，会移除该表相关的缓存计划，同时按照指定方式重新收集该表的统计信息。若未指定
MONITOR_MODIFICATIONS_REGATHER_COL，则重新对该表所有存在频率直方图统计信息的列和索引收集统计信息；若指定了
MONITOR_MODIFICATIONS_REGATHER_COL，则只对指定列及包含它的索引收集统计信息，同一张表上可指定多个不同的列。

其他属性值范围参考 GATHER_TABLE_STATS 中的参数描述。

  * PVALUE

属性名对应的属性值，不区分大小写。



  18. GET_PREFS



该函数返回普通用户表的静态属性值。

语法如下：

```
FUNCTION GET_PREFS(
	PPNAME  		VARCHAR(128),
	OWNNAME  		VARCHAR(128),
	TABNAME  		VARCHAR(128)
)RETURN VARCHAR;
```

参数详解

  * OWNNAME

模式名，不区分大小写。

  * TABNAME

表名，不区分大小写。

  * PPNAME

属性名，不区分大小写。可以取 STALE_PERCENT
、ESTIMATE_PERCENT、METHOD_OPT、DEGREE、GRANULARITY、CASCADE、NO_INVALIDATE、PREFERENCE_OVERRIDES_PARAMETER、MONITOR_MODIFICATIONS_FLAG、MONITOR_MODIFICATIONS_RM_PLN_FLAG
和 MONITOR_MODIFICATIONS_REGATHER_COL。



  19. COPY_TABLE_STATS



该过程将基表中指定源分区的统计信息复制到同一基表的指定的目的分区，目的分区原有统计信息将被覆盖。

语法如下：

```
PROCEDURE COPY_TABLE_STATS(
	OWNNAME               VARCHAR(128),
	TABNAME               VARCHAR(128),
	SRCPARTNAME          VARCHAR(128),
	DSTPARTNAME          VARCHAR(128),
	SCALE_FACTOR         VARCHAR(128) DEFAULT '1',
	FORCE                 BOOLEAN      DEFAULT FALSE
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * TABNAME

基表名，区分大小写。

  * SRCPARTNAME

源分区名，区分大小写。

  * DSTPARTNAME

目标分区名，区分大小写。

  * SCALE_FACTOR

保留参数，无实际意义。

  * FORCE

保留参数，无实际意义。



  20. SET_TABLE_STATS



该过程设置指定表的指定统计信息。

语法如下：

```
PROCEDURE SET_TABLE_STATS (
	OWNNAME         VARCHAR2,
	TABNAME         VARCHAR2,
	PARTNAME        VARCHAR2 DEFAULT NULL,
	STATTAB         VARCHAR2 DEFAULT NULL,
	STATID          VARCHAR2 DEFAULT NULL,
	NUMROWS         NUMBER DEFAULT NULL,
	NUMBLKS         NUMBER DEFAULT NULL,
	AVGRLEN         NUMBER DEFAULT NULL,
	FLAGS            NUMBER DEFAULT NULL,
	STATOWN         VARCHAR2 DEFAULT NULL,
	NO_INVALIDATE  BOOLEAN  DEFAULT TRUE,
	CACHEDBLK       NUMBER  DEFAULT NULL,
	CACHEHIT        NUMBER  DEFAULT NULL,
	FORCE           BOOLEAN DEFAULT FALSE,
	IM_IMCU_COUNT  NUMBER  DEFAULT NULL,
	IM_BLOCK_COUNT NUMBER  DEFAULT NULL,
	SCANRATE        NUMBER  DEFAULT NULL
);
```

参数详解

  * OWNNAME

模式名，区分大小写。

  * TABNAME

表名，区分大小写。

  * PARTNAME

分区表名，区分大小写；缺省为 NULL。

  * STATTAB

自定义统计信息表，区分大小写；缺省为 NULL，统计信息将直接更新到系统视图 SYS.SYSSTATS 中。

  * STATID

统计信息 ID，只有当 stattab 不为 NULL 的时候有效；缺省为 NULL。

  * NUMROWS

表/分区行数，缺省为 NULL，不做修改；若不为 NULL，则更新为指定行数，大小不超过 9223372036854775807(2^63-1)。

  * NUMBLKS

表/分区块数，缺省为 NULL，不做修改；若不为 NULL，则更新为指定块数，大小不超过 9223372036854775807(2^63-1)。

  * AVGRLEN

表/分区平均行长，缺省为 NULL，不做修改；若不为 NULL，则更新为指定行长，大小不超过 2147483647(2^31-1)。

  * FLAGS

保留参数，仅供内部使用。

  * STATOWN

自定义统计信息表所属的模式名。

  * NO_INVALIDATE

是否让依赖游标失效，缺省为 TRUE。目前仅为保留参数，无实际作用。

  * CACHEDBLK

保留参数，段(索引/表/索引分区/表分区)在 buffer cache 中的平均块数；缺省为 NULL，仅供内部使用。

  * CACHEHIT

保留参数，段的平均缓存命中率；缺省为 NULL；仅供内部使用。

  * FORCE

表的统计信息被锁时也强行通过此 PROC 设置统计信息。目前仅为保留参数，无实

  * IM_IMCU_COUNT

表/分区中内存压缩单元的个数。目前仅为保留参数，无实际作用。

  * IM_BLOCK_COUNT

表/分区中内存块的个数。目前仅为保留参数，无实际作用。

  * SCANRATE

数据库扫描外部表的速率，以 MB/s 为单位。目前仅为保留参数，无实际作用。



## 20.3 约束

以下对象不支持统计信息：

  1. 外部表、DBLINK 远程表、动态视图表、记录类型数组所用的临时表。
  2. 所在表空间为 OFFLINE 的对象。
  3. 位图索引，位图连接索引、虚索引、全文索引、空间索引、数组索引、无效的索引。
  4. BLOB、IMAGE、LONGVARBINARY、CLOB、TEXT、LONGVARCHAR、自定义类型列和空间类型列等列类型不支持统计信息，在使用 GATHER_TABLE_STATS()方法收集列统计信息时，这些类型的列会被忽略。



## 20.4 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_STATS');
SET SERVEROUTPUT ON;  //PRINT需要设置这条语句，才能打印出消息
```

例 1 收集模式 PERSON 下表 ADDRESS 的统计信息，并打印收集的表信息。

```
BEGIN
    DECLARE 
    OBJTAB DBMS_STATS.OBJECTTAB;
    OBJ_FILTER_LIST DBMS_STATS.OBJECTTAB;
    BEGIN
        OBJ_FILTER_LIST(0).OWNNAME = 'PERSON';
        OBJ_FILTER_LIST(0).OBJTYPE = 'TABLE';
        OBJ_FILTER_LIST(0).OBJNAME = 'ADDRESS';
    DBMS_STATS.GATHER_SCHEMA_STATS(
        'PERSON', 
        1.0,
        FALSE,
        'FOR ALL COLUMNS SIZE AUTO',
        1,
        'AUTO',
        TRUE,
        NULL,
        NULL,
        'GATHER',
        OBJTAB,
        NULL,
        TRUE,
        TRUE,
        OBJ_FILTER_LIST
    );
    PRINT OBJTAB.COUNT;
        FOR I IN 0..OBJTAB.COUNT-1 LOOP
            PRINT OBJTAB(I).OWNNAME;
            PRINT OBJTAB(I).OBJTYPE;
            PRINT OBJTAB(I).OBJNAME;
            PRINT OBJTAB(I).PARTNAME;
            PRINT OBJTAB(I).SUBPARTNAME;
            PRINT '-------- ';
        END LOOP;
    END;
END;
/
```

打印结果如下：

```
1
PERSON
TABLE
ADDRESS
NULL
NULL
--------
```

例 2 获得 PERSON 模式下表 ADDRESS 中列 ADDRESSID 的统计信息。

```
DBMS_STATS.COLUMN_STATS_SHOW('PERSON','ADDRESS','ADDRESSID');
```

返回结果集 1：

```
NUM_DISTINCT LOW_VALUE HIGH_VALUE NUM_NULLS NUM_BUCKETS SAMPLE_SIZE HISTOGRAM
16 1 16 
0 16 16 FREQUENCY
```

结果集 2：

```
OWNER TABLE_NAME COLUMN_NAME HISTOGRAM ENDPOINT_VALUE ENDPOINT_HEIGHT
ENDPOINT_KEYHEIGHT ENDPOINT_DISTINCT
PERSON ADDRESS ADDRESSID FREQUENCY 1 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 2 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 3 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 4 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 5 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 6 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 7 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 8 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 9 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 10 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 11 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 12 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 13 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 14 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 15 1 NULL NULL
PERSON ADDRESS ADDRESSID FREQUENCY 16 1 NULL NULL
```

例 3 删除 PERSON 模式下表 ADDRESS 的统计信息。

```
BEGIN
	DBMS_STATS.DELETE_TABLE_STATS ('PERSON', 'ADDRESS');
END;
/
```

例 4 自动收集用户表的统计信息。

第一步，打开监控。

设置 INI 参数 AUTO_STAT_OBJ 为 1 或 2。1：对所有表进行监控；2：只对用户通过 DBMS_STATS. SET_TABLE_PREFS 设置过 STALE_PERCENT 属性的表对象进行监控。如果 AUTO_STAT_OBJ=2，需进一步使用 DBMS_STATS.SET_TABLE_PREFS 设置 STALE_PERCENT 属性。

用 AUTO_STAT_OBJ=1 打开对 T 表的监控。

```
create table t(a int);
SP_SET_PARA_VALUE(1,'AUTO_STAT_OBJ',1);
```

用 AUTO_STAT_OBJ=2 打开对 T 表的监控。

```
create table t(a int);
SP_SET_PARA_VALUE(1,'AUTO_STAT_OBJ',2);
DBMS_STATS.SET_TABLE_PREFS('SYSDBA','T','STALE_PERCENT',15);
//对修改行数占总行数的比达到STALE_PERCENT要求的对象和总行数为0的对象收集统计信息
```

下面以 AUTO_STAT_OBJ=2 为例。

第二步，执行统计信息收集操作。自动收集统计信息使用 SP_CREATE_AUTO_STAT_TRIGGER 设置触发器。

```
SP_CREATE_AUTO_STAT_TRIGGER(1, 1, 1, 1,'11:20', '2021/1/11',0,1);
```

此处，也可将自动收集方法换成手动收集方法，其它三步用法不变。

第三步，对用户表 T 进行增删改操作。

```
insert into t select level connect by level<=20;
commit;
```

第四步，通过系统表 SYSSTATTABLEIDU 和动态视图 V$AUTO_STAT_TABLE_IDU 查看监控信息，通过系统表 SYSSTATS 查看统计信息。

打开监控后并执行统计信息收集操作之后才能查看到 SYSSTATTABLEIDU 和 SYSSTATS 的变化。SYSSTATTABLEIDU 为 AUTO_STAT_OBJ 等于 1 或 2 时对上一次的统计信息的监控。V$AUTO_STAT_TABLE_IDU 为 AUTO_STAT_OBJ 等于 1 时的统计信息实时监控，不需要执行收集统计信息操作也能查看。

下面以 AUTO_STAT_OBJ=2 为例。

查看监控信息：

```
//在触发器触发之后查看
SQL> SELECT * FROM SYSSTATTABLEIDU;

行号       ID          TOTAL_ROWS           INSERT_ROWS          DELETE_ROWS          UPDATE_ROWS      LAST_STAT_DT         MONITOR_FLAG RSVD1      TRUNCATED
---------- ----------- -------------------- -------------------- 
1          1074        20                   0                    0                    0
           2022-01-11 14:37:02.000000 2            NULL       0
```

查看统计信息：

```
SQL> SELECT * FROM SYSSTATS WHERE ID=1074;

行号       ID          COLID       T_FLAG T_TOTAL              N_SAMPLE             N_DISTINCT    N_NULL               V_MIN      V_MAX      BLEVEL      N_LEAF_PAGES         N_LEAF_USED_PAGES    CLUSTER_FACTOR N_BUCKETS    DATA    COL_AVG_LEN LAST_GATHERED              INFO1      INFO2
---------- ----------- ----------- ------ -------------------- ----------
1          1074        -1          T      20                   0                    0
           0                    NULL       NULL       0           1                    1
           0              0
           0X0000FFFF00000700
           -1          2022-01-11 14:37:02.279000 NULL       NULL

2          1074        0           C      20                   20                   20
           0                    0X01000000 0X14000000 0           1                    1
           0              20  
                      0X1400010008000700010000000100000001000000020000000100000003000000010000000400000001000000050000000100000006000000010000000700000001000000080000000100000009000000010000000A000000010000000B000000010000000C000000010000000D000000010000000E000000010000000F00000001000000100000000100000011000000010000001200000001000000130000000100000014000000
           4           2022-01-11 14:37:02.307000 NULL       NULL
```

如果用户想进一步对监控统计信息进行收集，可以参考《DM8 系统管理员手册》的 22.5 统计信息章节 SYSDBA.GET_AUTO_STAT_INFO_FUNC 用法。

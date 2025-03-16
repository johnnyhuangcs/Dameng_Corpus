

# ODCICONST 包

ODCICONST 包中定义了一系列适用于 DMSQL 程序的常量。

## 48.1   相关常量

下表中列出了 ODCICONST 系统包中的常量。

表 48.1 ODCICONST 系统包中的常量

|**常量**|**值**|**常量**|**值**|**常量**|**值**|
|--|--|--|--|--|--|
|Success|0|RowOption|2|FirstCall|1|
|Error|1|EstimateStats|1|IntermediateCall|2|
|Warning|2|ComputeStats|2|FinalCall|3|
|ErrContinue|3|Validate|4|RebuildIndex|4|
|Fatal|4|AlterIndexNone|0|RebuildPMO|5|
|PredExactMatch|1|AlterIndexRename|1|StatsGlobal|6|
|PredPrefixMatch|2|AlterIndexRebuild|2|StatsGlobalAndPartition|7|
|PredIncludeStart|4|AlterIndexRebuildOnline|3|StatsPartition|8|
|PredIncludeStop|8|AlterIndexModifyCol|4|FetchOp|1|
|PredObjectFunc|16|AlterIndexUpdBlockRefs|5|PopulateOp|2|
|PredObjectPkg|32|AlterIndexRenameCol|6|Sample|1|
|PredObjectType|64|AlterIndexRenameTab|7|SampleBlock|2|
|PredMultiTable|128|AlterIndexMigrate|8|TRUE|1|
|PredNotEqual|256|Local|1|FALSE|0|
|QueryFirstRows|1|RangePartn|2|QueryCoordinator|1|
|QueryAllRows|2|HashPartn|4|Shadow|2|
|QuerySortAsc|4|Online|8|Slave|4|
|QuerySortDesc|8|Parallel|16|FetchEOS|1|
|QueryBlocking|16|Unusable|32|CompFilterByCol|1|
|CleanupCall|1|IndexOnIOT|64|CompOrderByCol|2|
|RegularCall|2|TransTblspc|128|CompOrderDscCol|4|
|ObjectFunc|1|FunctionIdx|256|CompUpdatedCol|8|
|ObjectPkg|2|ListPartn|512|CompRenamedCol|16|
|ObjectType|4|UpdateGlobalIndexes|1024|CompRenamedTopADT|32|
|ArgOther|1|DefaultDegree|32767|ColumnExpr|1|
|ArgCol|2|DebuggingOn|1|AncOpExpr|2|
|ArgLit|3|NoData|2|SortAsc|1|
|ArgAttr|4|UserParamString|4|SortDesc|2|
|ArgNull|5|RowMigration|8|NullsFirst|4|
|ArgCursor|6|IndexKeyChanged|16|AddPartition|1|
|PercentOption|1|None|0|DropPartition|2|


## 48.2   举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES(1, 'ODCICONST');
```

举一个使用 ODCICONST 包中常量 SUCCESS 的例子。

```
CREATE OR REPLACE FUNCTION TEST_F(A IN INT) RETURN INT AS
BEGIN
    IF A > 60 THEN
        RETURN ODCICONST.SUCCESS;
    ELSE
        RETURN ODCICONST.ERROR;
    END IF;
END;
/
SELECT TEST_F(100) FROM DUAL;
```

结果：

```
行号     TEST_F(100)
---------- -----------
1          0
```

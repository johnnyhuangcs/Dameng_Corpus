

# DBMS_REDEFINITION包

DBMS_REDEFINITION 包提供了在线重定义数据库表的功能。这种功能尽可能地减少了对表的封锁时间，因此，数据库表在重定义的过程中也能正常提供服务。

使用须知：

1.对于临时表、物化视图日志表、系统表、外部表、记录类型数组或对象类型所用的临时表、动态性能视图、位图连接索引表、位图索引表等类型的表以及含有半透明加密列的表不支持进行重定义。

2.对于 SYSDBA 下的用户表与普通用户表同等对待，允许进行重定义。

3.DM MPP 环境下不支持 DBMS_REDEFINITION 包。

## 44.1  相关方法

  1. CAN_REDEF_TABLE



判断目标表是否可以进行在线重定义。

语法如下：

```
DBMS_REDEFINITION.CAN_REDEF_TABLE (
   	UNAME				IN  VARCHAR(128),
   	TNAME				IN  VARCHAR(128),
   	OPTIONS_FLAG		IN  INT := CONS_USE_PK,
   	PART_NAME			IN  VARCHAR(128) := NULL);
```

参数详解

● uname：模式名。

● tname：进行重定义的原表。

● options_flag：重定义方式，取值为 dbms_redefinition.cons_use_pk 或 dbms_redefinition.cons_use_rowid。默认为 cons_use_pk。对于不存在主键或是伪主键（某一个 UNIQUE 约束上的所有列都具有 NOT NULL 约束）的表，不允许使用 cons_use_pk 方式进行重定义。

● part_name：暂不支持，仅作参数兼容。分区名，默认为空。可以指定多个分区，用逗号隔开。必须与 START_REDEF_TABLE 指定的分区一致。

  2. START_REDEF_TABLE



开始重定义过程。

首先会执行一次 CAN_REDEF_TABLE，如果不能按照指定方式重定义，则会报错。在原表 ORIG_TABLE 上创建一个物化视图日志，创建的过程需要对原表上 X 锁；如果原表上已存在物化视图日志，则判断该物化视图日志的类型（ROWID 还是 PK）与指定的重定义方式是否一致，如果不一致则报错，如果一致，则跳过创建物化视图日志的过程。基于中间表建立物化视图，并进行完全刷新。

语法如下：

```
DBMS_REDEFINITION.START_REDEF_TABLE (
   	UNAME					IN  VARCHAR(128),
   	ORIG_TABLE				IN  VARCHAR(128),
   	INT_TABLE				IN  VARCHAR(128),
   	COL_MAPPING				IN  VARCHAR := NULL,
   	OPTIONS_FLAG			IN  INT := CONS_USE_PK,
   	ORDERBY_COLS			IN  VARCHAR(128) := NULL,
   	PART_NAME				IN  VARCHAR(128) := NULL,
  	CONTINUE_AFTER_ERRORS	IN  BOOLEAN := FALSE,  
   	COPY_VPD_OPT			IN  INT := 1,
  	REFRESH_DEP_MVIEWS		IN  VARCHAR(128) := 'N');
```

参数详解

● uname：模式名。

● orig_table：进行重定义的原表。

● int_table：按照新定义创建的中间表。中间表的表名、列名、列数都可以和原表不同。但需保证映射的列类型需相同。

● col_mapping：两个表之间的列映射关系。语法与查询项类似，例“T1.C1 as d1,c2 as d2, c3 as d3”。如果没有指定 COL_MAPPING，则根据原表的列名构造。如果指定列映射，需要包含所有主键列，且不能包含自增列和虚拟列等类型的列。

● options_flag：重定义方式。取值为 dbms_redefinition.cons_use_pk 或 dbms_redefinition.cons_use_rowid。默认为 cons_use_pk。不推荐使用 rowid 的方式进行重定义，可能导致性能较差。

● orderby_cols：暂不支持，仅作参数兼容。转移数据时按照指定列排序，格式与 order by 项类似，例'c2 desc'。

● part_name：暂不支持，仅作参数兼容。分区名，默认为空。可以指定多个分区，用逗号隔开。

● continue_after_errors：暂不支持，仅作参数兼容。批量进行分区重定义时，出错时允许进行下一个分区的重定义。

● copy_vpd_opt：DM 没有 VPD 策略，仅作参数兼容。

● refresh_dep_mviews：是否在 START_REDEF_TABLE、SYNC_INTERIM_TABLE 和 FINISH_REDEF_TABLE 执行时对其他依赖该表的物化视图进行快速刷新。Y 是，N 否。

  3. COPY_TABLE_DEPENDENTS



将原表上的依赖（索引、约束、授权、统计信息等）复制到新表。暂不支持触发器。

DMDPC 环境下暂不支持该方法。

语法如下：

```
DBMS_REDEFINITION.COPY_TABLE_DEPENDENTS(
   	UNAME					IN  VARCHAR(128),
   	ORIG_TABLE               	IN  VARCHAR(128),
   	INT_TABLE				IN  VARCHAR(128),
   	COPY_INDEXES             	IN  INT := 1,
   	COPY_TRIGGERS			IN  BOOLEAN     := TRUE,
   	COPY_CONSTRAINTS         IN  BOOLEAN     := TRUE,
   	COPY_PRIVILEGES			IN  BOOLEAN     := TRUE,
   	IGNORE_ERRORS			IN  BOOLEAN     := FALSE,
   	NUM_ERRORS              	OUT INT,
   	COPY_STATISTICS			IN  BOOLEAN     := FALSE, 
   	COPY_MVLOG				IN  BOOLEAN     := FALSE);
```

参数详解

● uname：模式名。

● orig_table：进行重定义的原表。

● int_table：按照新定义创建的中间表。

● copy_indexes：是否复制索引

● copy_triggers：暂不支持，仅作参数兼容。是否复制触发器。

● copy_constraints：是否复制约束。

● copy_privileges：是否复制授权。

● ignore_errors：复制过程遇到错误时，是否忽略错误。

● num_errors：出现的错误个数。

● copy_statistics：复制统计信息。

● copy_mvlog：暂不支持，仅作参数兼容。复制物化视图日志。

  4. SYNC_INTERIM_TABLE



重定义过程中手动数据同步。为了减轻最后一步 FINISH_REDEF_TABLE 同步的数据量，在重定义的过程中，可使用 SYNC_INTERIM_TABLE 先阶段性地完成部分数据同步。

语法如下：

```
DBMS_REDEFINITION.SYNC_INTERIM_TABLE (
   	UNAME					IN  VARCHAR(128),
   	ORIG_TABLE               	IN  VARCHAR(128),
   	INT_TABLE                	IN  VARCHAR(128),
   	PART_NAME                	IN  VARCHAR(128) := NULL,
   	CONTINUE_AFTER_ERRORS   	IN  BOOLEAN := FALSE);
```

参数详解

● uname：模式名。

● orig_table：进行重定义的原表。

● int_table：按照新定义创建的中间表。

● part_name：暂不支持，仅作参数兼容。分区名，默认为空。可以指定多个分区，用逗号隔开，数量需要与 int_table 一致。

● continue_after_errors：暂不支持，仅作参数兼容。批量进行分区重定义时，出错时允许进行下一个分区的重定义。

  5. SET_PARAM



修改重定义过程中的参数属性。即修改 START_REDEF_TABLE 过程中的各属性值。

语法如下：

```
DBMS_REDEFINITION.SET_PARAM (
   	REDEFINITION_ID			IN  VARCHAR(128),
   	PARAM_NAME				IN  VARCHAR(128),
   	PARAM_VALUE				IN  VARCHAR(128));
```

参数详解

● redefinition_id：进行重定义的原表的 ID。

● param_name：需要设置的参数名称。目前仅支持修改 START_REDEF_TABLE 中 refresh_dep_mviews 参数。

● param_value：设置参数的目标值。目标值只能设置为 0 或 1。0 否，1 是。

  6. FINISH_REDEF_TABLE



完成重定义过程。完成数据同步并清理重定义环境。根据物化视图日志进行最后的数据同步，并交换原表和中间表的表名。如果原表上的物化视图日志是由 START_REDEF_TABLE 创建的，则删除该物化视图日志，否则保留。本方法全程需要对原表上 X 锁。

语法如下：

```
DBMS_REDEFINITION.FINISH_REDEF_TABLE (
   uname                 IN  VARCHAR(128),
   orig_table            IN  VARCHAR(128),
   int_table             IN  VARCHAR(128),
   part_name             IN  VARCHAR(128) := NULL,
   dml_lock_timeout      IN  INT := NULL,
   continue_after_errors IN  BOOLEAN := FALSE); 
```

参数详解

● uname：模式名。

● orig_table：进行重定义的原表。

● int_table：按照新定义创建的中间表。

● part_name：暂不支持，仅作参数兼容。分区名，默认为空。可以指定多个分区，用逗号隔开。必须与 START_REDEF_TABLE 指定的分区一致。

● dml_lock_timeout：锁等待的超时时长，取值 0-1000000（秒），NULL 表示不限制等待时长，默认为 NULL。

● continue_after_errors：批量进行分区重定义时，出错时允许进行下一个分区的重定义

  7. ABORT_REDEF_TABLE



终止重定义过程。解除原表和中间表之间的依赖关系，清空中间表，并将中间表恢复成普通表状态。如果原表上的物化视图日志是由 START_REDEF_TABLE 创建的，则删除该物化视图日志，否则保留。

语法如下：

```
DBMS_REDEFINITION.FINISH_REDEF_TABLE (
   	UNAME					IN   VARCHAR(128),
   	ORIG_TABLE                	IN   VARCHAR(128),
   	INT_TABLE				IN   VARCHAR(128),
   	PART_NAME				IN   VARCHAR(128) := NULL,
   	DML_LOCK_TIMEOUT		IN   INT := NULL,
   	CONTINUE_AFTER_ERRORS	IN   BOOLEAN := FALSE); 
```

参数详解

● uname：模式名。

● orig_table：进行重定义的原表。

● int_table：按照新定义创建的中间表。

● part_name：暂不支持，仅作参数兼容。分区名，默认为空。可以指定多个分区，用逗号隔开。必须与 START_REDEF_TABLE 指定的分区一致。

  8. DELETE_REDUNDANT_RECORD



用于删除某些特定情况产生的系统表冗余记录。例如，如果重定义过程中原表遭到删除，那么原表在 SYSOBJECTS 表中的记录不存在，此时，本过程会将三张系统表系统表 SYS.SYSREDEFSTATUS、sys.sysredefobject 和 sys.sysredefderror 中与其相关的记录全部删除。

语法如下：

```
DBMS_REDEFINITION.REDUNDANT_RECORD(
obj_id   IN INT);
```

参数详解

● obj_id 对象 ID。

## 44.2  系统表

1. SYS.SYSREDEFSTATUS

表重定义过程中，上一步操作的状态。

|**列名**|**类型**|**描述**|**备注**|
|--|--|--|--|
|OBJ_ID|INTEGER|重定义对象的 ID||
|REDEF_ID|INTEGER|重定义过程 ID|自增列。该列不作为重定义系统表之间的连接依据|
|PREV_OPERATION|VARCHAR(128)|操作类型：  START_REDEF_TABLE  SYNC_INTERIM_TABLE  COPY_TABLE_DEPENDENTS  FINISH_REDEF_TABLE||
|OBJ_OWNER|VARCHAR(128)|对象模式名||
|OBJ_NAME|VARCHAR(128)|对象名||
|STATUS|VARCHAR(128)|状态：FAILURE 或 SUCCESS||
|RESTARTABLE|VARCHAR(1)|是否可以重新开始|暂不支持，一律为‘N’|
|CREATE_MVLOG|INTEGER|START_REDEF_TABLE 是否创建了物化视图日志|用于 ABORT 或 FINISH 时判断是否需要删除物化视图日志|
|REFRESH_DEP_MV|INTEGER|START_REDEF_TABLE、SYNC_INERTIM_TABLE、FINISH_REDEF_TABLE 时，是否同步刷新表上的物化视图||
|COL_MAPPING|VARCHAR|记录用户输入的列映射的原始字符串||
|OPTIONS_FLAG|INTEGER|记录是刷新方式  1：pk  2：rowid||


2. SYS.SYSREDEFOBJECT

表重定义过程中，涉及到的所有对象。仅当进行 COPY_TABLE_DEPENDENTS 时，同一个 OBJ_ID 下可能出现其他对象，否则只会有当前表。

|**列名**|**类型**|**描述**|**备注**|
|--|--|--|--|
|OBJ_ID|INTEGER|重定义对象的 ID||
|REDEF_ID|INTEGER|重定义过程 ID|列值直接从 SYSREDEFSTATUS 获取，该列不作为重定义系统表之间的连接依据|
|OBJ_TYPE|INTEGER|对象类型  1:TABLE  2:INDEX  3:CONSTRAINT||
|OBJ_OWNER|VARCHAR(128)|对象模式名||
|OBJ_NAME|VARCHAR(128)|对象名||
|INT_OBJ_ID|INTEGER|中间对象 ID||
|INT_OBJ_OWNER|VARCHAR(128)|中间对象模式名||
|INT_OBJ_NAME|VARCHAR(128)|中间对象名||
|BT_OWNER|VARCHAR(128)|所属基表模式名||
|BT_NAME|VARCHAR(128)|所属基表名||
|GENFLAG|INTEGER|未知|保留列，无实际意义|
|TYPFLAG|INTEGER|未知|保留列，无实际意义|


3. SYS.SYSREDEFDERROR

记录 COPY_TABLE_DEPENDENTS 过程中出现的错误。

|**列名**|**类型**|**描述**|**备注**|
|--|--|--|--|
|OBJ_ID|INTEGER|重定义对象的 ID||
|REDEF_ID|INTEGER|重定义过程 ID|列值直接从 SYSREDEFSTATUS 获取，该列不作为重定义系统表之间的连接依据|
|OBJ_TYPE|VARCHAR(20)|对象类型||
|OBJ_OWNER|VARCHAR(128)|对象模式名||
|OBJ_NAME|VARCHAR(128)|对象名||
|BT_OWNER|VARCHAR(128)|所属基表模式名||
|BT_NAME|VARCHAR(128)|所属基表名||
|DDL_TXT|CLOB|定义||
|ERR_NO|INTEGER|错误码||
|ERR_TXT|VARCHAR(1000)|错误信息||
|TIMESTAMP$|BIGINT|错误对应的执行过程开始时的时间戳|用于计算一次执行过程中出现错误的个数|


## 44.3  系统视图

1. DBA_REDEFINITION_STATUS

表重定义过程中，上一步操作的状态。

|**列名**|**类型**|**描述**|**备注**|
|--|--|--|--|
|REDEFINITION_ID|INTEGER|重定义对象的 ID||
|BASE_TABLE_OWNER|VARCHAR(128)|基表模式名||
|BASE_TABLE_NAME|VARCHAR(128)|基表名||
|BASE_PARTITION_NAME|VARCHAR(128)|分区名|暂不支持|
|INTERIM_OBJECT_OWNER|VARCHAR(128)|中间表模式名||
|INTERIM_OBJECT_NAME|VARCHAR(128)|中间表名||
|OPERATION|VARCHAR(128)|操作类型：START_REDEF_TABLE  SYNC_INTERIM_TABLE  COPY_TABLE_DEPENDENTS  FINISH_REDEF_TABLE||
|STATUS|VARCHAR(128)|状态：  FAILURE  SUCCESS||
|RESTARTABLE|VARCHAR(1)|是否可以重新开始|暂不支持，一律为‘N’|


2. DBA_REDEFINITION_OBJECTS

表重定义过程中，涉及到的所有对象。仅当进行 COPY_TABLE_DEPENDENTS 时，同一个 OBJ_ID 下可能出现其他对象，否则只会有当前表。

|**列名**|**类型**|**描述**|**备注**|
|--|--|--|--|
|OBJECT_TYPE|VARCHAR(12)|重定义对象类型||
|OBJECT_OWNER|VARCHAR(128)|对象模式名||
|OBJECT_NAME|VARCHAR(128)|对象名||
|BASE_TABLE_OWNER|VARCHAR(128)|基表模式名||
|BASE_TABLE_NAME|VARCHAR(128)|基表名||
|INTERIM_OBJECT_OWNER|VARCHAR(128)|中间表模式名||
|INTERIM_OBJECT_NAME|VARCHAR(128)|中间表名||
|EDITION_NAME|VARCHAR(128)|保留字段，将来使用|恒为 NULL|


3. DBA_REDEFINITION_ERRORS

记录 COPY_TABLE_DEPENDENTS 过程中出现的错误。

|**列名**|**类型**|**描述**|**备注**|
|--|--|--|--|
|OBJECT_TYPE|VARCHAR(12)|对象类型||
|OBJECT_OWNER|VARCHAR(128)|对象模式名||
|OBJECT_NAME|VARCHAR(128)|对象名||
|BASE_TABLE_OWNER|VARCHAR(128)|所属基表模式名||
|BASE_TABLE_NAME|VARCHAR(128)|所属基表名||
|DDL_TXT|CLOB|定义||
|EDITION_NAME|VARCHAR(128)|保留字段，将来使用|为 NULL|
|ERR_NO|INTEGER|错误编号||
|ERR_TXT|VARCHAR(1000)|错误信息||


## 44.4  一个完整的重定义过程

一个完整的重定义过程如下：

第 1 步  可选项。使用 CAN_REDEF_TABLE 判断目标表是否可以进行重定义。是则进入第 2 步，否则结束。提供给用户的预判选项。

第 2 步  必选项。使用 START_REDEF_TABLE 开始重定义过程。此时，系统会生成一个名为“MVIEW$_+ 目标表名”的物化视图，用来记录在重定义过程中用户对目标表的数据操作。

第 3 步  可选项。在重定义过程中，可使用 COPY_TABLE_DEPENDENTS 将原表上的依赖（索引、约束、触发器等）复制到新表。如果用户不再需要这些依赖关系或者想自己手动创建依赖对象，此步骤可省略。

第 4 步  可选项。在重定义过程中，可使用 SYNC_INTERIM_TABLE 阶段性地手动数据同步。此步骤是为了加速数据同步。如果省略此步骤，数据同步会最终在 FINISH_REDEF_TABLE 步骤中一次性完成。

第 5 步  可选项。使用 SET_PARAM 设置重定义过程中的参数属性。目前只支持设置同步刷新表上依赖的其他物化视图这一个属性。

第 6 步  必选项。使用 FINISH_REDEF_TABLE 完成重定义过程，完成数据同步并清理重定义环境。至此，整个重定义过程完成。

注意事项：

  1. 在重定义过程中，不允许对原表进行 DDL 操作，否则会产生错误。一旦遇到错误，可使用 ABORT_REDEF_TABLE 终止重定义过程，后续如仍需继续重定义，可在终止后重新开始一次重定义。
  2. 在重定义过程中，如果原表遭到删除，那么 ABORT_REDEF_TABLE 和 FINISH_REDEF_TABLE 无法执行，导致该次重定义过程在系统表中的记录无法删除，产生冗余。对于这些冗余的记录，可以通过 DELETE_REDUNDANT_RECORD 删除。



## 44.5  举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_REDEFINITION');
```

  1. 建表



```
CREATE SCHEMA "CSCOTT" AUTHORIZATION "SYSDBA";
DROP TABLE "CSCOTT".EMP;
DROP TABLE "CSCOTT".EMP_INT;
//创建原表
CREATE TABLE "CSCOTT"."EMP"(c1 int PRIMARY KEY, c2 int, c3 int) PARTITION BY range(c1) 
(
	partition p1 values less than(10),
	partition p2 values less than(20),
	partition p3 values less than(maxvalue)
);
//在原表上创建索引
CREATE INDEX "CSCOTT".emp_i1 on "CSCOTT".EMP(c2);

//创建中间表
CREATE TABLE "CSCOTT"."EMP_INT"(c1 int, c2 INT, c3 int) ;

INSERT INTO "CSCOTT".EMP(c1,c2,c3) SELECT level,level,level FROM DUAL connect by level <=1000;
INSERT INTO "CSCOTT".EMP_INT(c1) SELECT level FROM DUAL connect by level <=10;
```

  2. 使用包中的各方法



```
//可选项。判断原表是否可以重定义。
DBMS_REDEFINITION.CAN_REDEF_TABLE('CSCOTT', 'EMP', DBMS_REDEFINITION.CONS_USE_PK);

//必选项，开始重定义
DBMS_REDEFINITION.START_REDEF_TABLE('CSCOTT', 'EMP', 'EMP_INT',col_mapping =>'C1 as C1, C2 as C2, C3 as C3'); 

//可选项。在重定义过程中，阶段性地同步数据。
DBMS_REDEFINITION.SYNC_INTERIM_TABLE('CSCOTT', 'EMP', 'EMP_INT'); 

//可选项。拷贝原表上的依赖关系。
DECLARE
	n_err INT;
BEGIN
	 DBMS_REDEFINITION.COPY_TABLE_DEPENDENTS('CSCOTT', 'EMP', 'EMP_INT', num_errors=>n_err);
	 DBMS_OUTPUT.PUT_LINE(n_err);
END;   

//可选项。终止重定义。执行了此步骤，后续不再执行。
DBMS_REDEFINITION.ABORT_REDEF_TABLE('CSCOTT', 'EMP', 'EMP_INT');

//必选项。完成重定义。未进行终止重定义时为必选操作。
DBMS_REDEFINITION.FINISH_REDEF_TABLE('CSCOTT', 'EMP', 'EMP_INT');

//可选项。假设重定义过程中原表遭到删除，可手动删除系统表SYS.SYSREDEFSTATUS、SYS.SYSREDEFOBJECT和SYS.SYSREDEFDERROR中的数据冗余；下面完整执行该过程，先在重定义过程中删除原表
DBMS_REDEFINITION.START_REDEF_TABLE('CSCOTT', 'EMP', 'EMP_INT',col_mapping =>'C1 as C1, C2 as C2, C3 as C3');  //开始重定义
DROP TABLE "CSCOTT".EMP CASCADE;  //删除原表
SELECT OBJ_ID FROM SYS.SYSREDEFSTATUS WHERE OBJ_NAME='EMP';  //假定查询到的ID为1128
DBMS_REDEFINITION.DELETE_REDUNDANT_RECORD(1128);  //手动删除对应系统表SYS.SYSREDEFSTATUS、SYS.SYSREDEFOBJECT和SYS.SYSREDEFDERROR中的数据冗余
```

  3. 重定义的过程中可查看各系统视图和系统表的变化



```
SELECT * FROM DBA_REDEFINITION_STATUS;
SELECT * FROM DBA_REDEFINITION_OBJECTS;
SELECT * FROM DBA_REDEFINITION_ERRORS;
SELECT * FROM SYS.SYSREDEFSTATUS;
SELECT * FROM SYS.SYSREDEFOBJECT;
SELECT * FROM SYS.SYSREDEFDERROR;
```

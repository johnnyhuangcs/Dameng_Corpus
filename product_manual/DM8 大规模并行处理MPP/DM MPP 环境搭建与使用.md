

# DM MPP 环境搭建与使用

本章介绍在实际应用环境中应如何规划、配置与部署 DM MPP 系统。通过阅读本章，读者可以从根据应用实际情况制定一个合适的 DM MPP 方案开始，完成 DM MPP 的配置与部署，建立应用分布表并载入数据。

## 4.1 制定合适的 DM MPP 方案

对于一个应用系统，如果制定出一个合适的 DM MPP 解决方案呢？可以主要从以下几个方面入手：

  * **应用是否适合使用 DM MPP 方案**



DM MPP 解决方案具有高性价比、高可靠性、功能强大等优点，但也是有其局限性的，并非是万能解药。DM MPP 主要针对海量数据的 OLAP 应用而研发，在合理规划的前提下，对普通的、并发量不大的一般 OLTP 或混合类型应用也适用，但不适合于高并发操作的 OLTP 类型应用。

  * **需要使用多大规模的 DM MPP 方案**



确定应用适合使用 DM MPP 解决方案后，接下来需要根据应用规模确定 MPP 的节点数。DM MPP 最多支持 1024 个 EP 节点，在实际应用中具体使用几个 EP 节点则应根据应用数据规模、硬件规划、网络带宽和项目预算情况而定。理论上来说节点数越多越好，这样并行越充分，效率越高。但这也不是绝对的，方案设计者应同时考虑到 EP 节点间网络资源的情况，如果网络是瓶颈，则并不是节点数越多越好。

  * **需要配置怎样的硬件资源**



DM MPP 采用完全对等不共享架构，不需要专用硬件，可以采用普通的 PC 服务器组建集群。需要注意的是，DM
MPP 系统需要通过网络在各 EP 间传递数据，因此网络的带宽对于 MPP 系统的效率非常重要，建议配置千兆或万兆内部网络，用户应根据实际需求、配置成本及实际条件等综合评估决定。

  * **如何设计应用表**



DM MPP 系统中的数据分布在各 EP 中，支持表数据的哈希分布、随机分布、复制分布、范围分布、LIST 分布类型，用户应根据表在应用中的特点选择合适的分布类型。

除了表的分布类型，管理员还应根据应用的实际情况确定表的分布列、分布列的数据类型以及是否需要组合分布列等，对于查询连接中使用频率较高的连接键等可以考虑作为分布列。

  * **如何配置 DM MPP 系统参数**



DM 数据库实例配置文件 dm.ini 和 MAL 系统配置文件 dmmal.ini 中一些参数的配置会影响 MPP 系统的运行性能，管理员应根据应用和硬件的实际情况对这些参数进行适当的配置。同时应注意各 EP 必须保持一致的 INI 参数。

## 4.2 DM MPP 环境搭建

本节我们将以一个简单的两节点 MPP 为例，介绍手动配置与搭建 DM MPP 环境的步骤。DM 提供了 DM web 版数据库管理工具，可以很方便地搭建 DM MPP。但是阅读本节将更有利于读者掌握配置的流程，更好地在 MPP 系统运行时进行管理。

### 4.2.1 系统规划

本例配置一个两节点 MPP。两个节点都配置两块网卡，一块接入内部网络交换模块，一块接入到外部交换机。两节点实例名分别为 EP01 和 EP02，相关的 IP、端口等规划见下表。

表4.1 MPP系统规划

|**实例名**|**MAL_INST_HOST**|**MAL_INST_PORT**|**MAL_HOST**|**MAL 端口**|**MPP_SEQNO**|
|-------------|-------------|-------------|-------------|-------------|-------------|
|EP01|192.168.1.11|5236|192.168.0.12|5269|0|
|EP02|192.168.1.21|5237|192.168.0.22|5270|1|


> **注意**
>
> DM MPP各EP使用的DM服务器版本应一致，同时还应注意各EP所在主机的操作系统位数、大小端模式、时区及时间设置都应一致，否则可能造成意想不到的错误。



### 4.2.2 配置 dm.ini

首先，在 EP01 和 EP02 上分别创建数据库，用户可以使用 DM 的图形化客户端工具“数据库配置助手”或命令行工具 dminit 创建数据库。

> **注意**
>
> 在各EP上创建数据库时，要求有些初始化参数必须所有EP都相同，见3.3.1 dm.ini
> MPP相关配置项。我们建议各EP的数据库初始化参数都保持一致，以免产生错误。



分别对两个实例的 dm.ini 进行配置。

修改 EP01 的 dm.ini 的以下几个参数如下：

```
INSTANCE_NAME = EP01

PORT_NUM = 5236

MAL_INI = 1

MPP_INI = 1
```

修改 EP02 的 dm.ini 的以下几个参数如下：

```
INSTANCE_NAME = EP02

PORT_NUM = 5237

MAL_INI = 1

MPP_INI = 1
```

### 4.2.3 配置 dmmal.ini

为两个 EP 配置 dmmal.ini 如下，配置完全一样，EP 间可互相拷贝。dmmal.ini 与 dm.ini 放在相同的目录下。

```
[MAL_INST1]

MAL_INST_NAME = EP01

MAL_HOST = 192.168.0.12

MAL_PORT = 5269

MAL_INST_HOST = 192.168.1.11

MAL_INST_PORT = 5236

[MAL_INST2]

MAL_INST_NAME = EP02

MAL_HOST = 192.168.0.22

MAL_PORT = 5270

MAL_INST_HOST = 192.168.1.21

MAL_INST_PORT = 5237
```

### 4.2.4 配置 dmmpp.ctl

dmmpp.ctl 是一个二进制文件，用户不能直接配置，需要先配置 dmmpp.ini。

配置 dmmpp.ini 如下：

```
[SERVICE_NAME1]

MPP_SEQ_NO = 0

MPP_INST_NAME = EP01

[SERVICE_NAME2]

MPP_SEQ_NO = 1

MPP_INST_NAME = EP02
```

使用 DM 提供的工具 dmctlcvt 将 dmmpp.ini 转换成 dmmpp.ctl，dmctlcvt 工具在 DM 安装目录的“bin”子目录中。

转换生成的 dmmpp.ctl 需要放在与 dm.ini 同一个目录。假设 DM 的安装路径为 c 盘根目录，下面的命令将 dmmpp.ini 转换为 dmmpp.ctl，命令中的“TYPE=2”参数表示将文本文件转换成控制文件，也可以使用“TYPE=1”参数进行逆向转换。

```
dmctlcvt TYPE=2 SRC=c:\dmdbms\data\dameng\dmmpp.ini DEST=c:\dmdbms\data\dameng\dmmpp.ctl
```

将生成的 dmmpp.ctl 拷贝至另一 EP，保证 MPP 系统中所有 EP 的 dmmpp.ctl 完全相同。

### 4.2.5 运行 MPP

经过前面四个步骤，DM MPP 环境已经配置完成了。分别启动 EP01 和 EP02 的 DM 数据库实例（顺序不分先后），DM MPP 系统即能正常运行，用户就可以登录任一 EP 进行数据库操作了。

## 4.3 建立分布表

DM MPP 支持表数据的哈希分布、随机分布、复制分布、范围分布、LIST 分布类型，用户可根据实际情况选择合适的分布类型。

MPP 的数据分布类型和具体设置在建表时指定，语法如下，建表的其他语法分支可参见《DM8_SQL 语言使用手册》。

```
CREATE [[GLOBAL] TEMPORARY] TABLE <表名定义> <表结构定义>;
<表结构定义>::=<表结构定义1> | <表结构定义2>
<表结构定义1>::= (<列定义> {,<列定义>}[,<表级约束定义>{,<表级约束定义>}]) [ON COMMIT <DELETE | PRESERVE> ROWS] [<PARTITION子句>] [<空间限制子句>] [<STORAGE子句>] [<压缩子句>] [<ROW MOVEMENT子句>] [<DISTRIBUTE子句>]
<表结构定义2>::= [ON COMMIT <DELETE | PRESERVE> ROWS] [<空间限制子句>]
[<STORAGE子句>] [<压缩子句>]AS <不带INTO的SELECT语句>[<DISTRIBUTE子句>];

<DISTRIBUTE子句>::=DISTRIBUTED[<RANDOMLY>|<FULLY>]
	| DISTRIBUTED BY [<HASH>] (<列名> {,<列名>})
	| DISTRIBUTED BY RANGE (<列名> {,<列名>})(<范围分布项> {,<范围分布项>})
	| DISTRIBUTED BY LIST (<列名> {,<列名>})(<LIST分布项>
{,<LIST分布项>})
<范围分布项>::= VALUES LESS THAN (<表达式>{,<表达式>}) ON <实例名>
	|VALUES EQU OR LESS THAN (<表达式>{,<表达式>}) ON <实例名>
<LIST分布项>::= VALUES (<表达式>{,<表达式>}) ON<实例名>
```

下面给出几个简单的创建不同类型分布表的例子。

例 1 创建哈希分布表 T_HASH，分布列为 C1。

```
CREATE TABLE T_HASH(C1 INT, C2 CHAR(10))DISTRIBUTED BY HASH (C1);
```

例 2 创建随机分布表 T_RANDOM。

```
CREATE TABLE T_RANDOM(C1 INT, C2 CHAR(10))DISTRIBUTED RANDOMLY;
```

例 3 创建复制分布表 T_FULLY。

```
CREATE TABLE T_FULLY(C1 INT, C2 CHAR(10))DISTRIBUTED FULLY;
```

例 4 创建范围分布表 T_RANGE，分布列为 C1。

```
CREATE TABLE T_RANGE (C1 INT, C2 CHAR(10)) DISTRIBUTED BY RANGE (C1) (VALUES EQU OR LESS THAN (100) ON EP01, VALUES LESS

THAN(MAXVALUE) ON EP02);
```

例 5 创建 LIST 分布表 T_LIST，分布列为 C1。

```
CREATE TABLE T_LIST(C1 INT, C2 CHAR(10))

DISTRIBUTED BY LIST (C1) (VALUES(3) ON EP01,VALUES(4) ON EP02);
```

例 6 创建哈希分布表的范围水平分区表。

```
CREATE TABLE T_HASH_RANGE_PARTITION

(C1 INT, C2 CHAR(10), C3 CHAR(10))

PARTITION BY RANGE(C1)

(

PARTITION PART_1 VALUES LESS THAN(0) ,

PARTITION PART_2 VALUES LESS THAN(10) ,

PARTITION PART_3 VALUES LESS THAN(100) ,

PARTITION PART_4 VALUES LESS THAN(MAXVALUE)

)

DISTRIBUTED BY HASH (C1);
```

在创建分布表时，用户应注意以下一些使用限制：

  * 单机模式下建的分布表和普通表一样，但是不能创建指定实例名的分布表（如范围分布表和 LIST 分布表）；
  * 在 MPP 模式下创建分布表，如果未指定列则默认为 RANDOMLY(随机)分布表；
  * 分布列类型不支持 BLOB、CLOB、IMAGE、TEXT、LONGVARCHAR、BIT、BINARY、VARBINARY、LONGVARBINARY、BFILE、时间间隔类型、虚拟列和用户自定义类型；
  * HASH 分布、RANGE 分布、LIST 分布允许更新分布列，并支持包含大字段列的表的分布列更新，但包含 INSTEAD OF 触发器的表、堆表不允许更新分布列；
  * 对于 FULLY（复制）分布表，只支持单表查询的更新和删除操作，并且查询项或者条件表达式中都不能包含 ROWID 伪列表达式；
  * RANGE（范围）分布表和 LIST（列表）分布表，分布列与分布列值列表必须一致，并且指定的实例名不能重复；
  * 引用约束的引用列和被引用列都必需包含分布列，且分布情况完全相同；
  * 随机分布表不支持 UNIQUE 索引。



## 4.4 快速数据装载

DM MPP 特别适合于海量数据的存储和处理，因此在应用中常常面临将大量数据从某个或某些历史数据库中装载到 MPP 系统的需求。为了满足海量数据的快速装载需求，DM 提供了快速装载工具 dmfldr，能够对 DM 单机版和 MPP 系统进行海量数据的快速装载。

dmfldr 为命令行工具，使用时必须指定必要的执行参数，具体可参看《DM8_dmfldr 使用手册》，本文档就不再详细介绍了，这里仅对 dmfldr 中 MPP 相关的参数 MPP_CLIENT 进行说明。

dmfldr 支持 MPP 环境下的两种数据加载模式：客户端分发模式和本地分发模式，通过参数 MPP_CLIENT 进行设置。使用客户端分发模式时，数据在 dmfldr 客户端进行分发然后直接向指定 EP 发送数据；使用本地分发模式时，每个 EP 对应一个 dmfldr 和一份数据，每个 dmfldr 只选择出对应本节点的数据并发送，不管其他节点的数据。默认使用客户端分发模式。

## 4.5 停止 MPP 系统

需要停止 DM MPP 系统的运行时，只需要停止每个 EP 的 DM 实例即可，没有特别的顺序要求。

若在 DM MPP 系统的运行过程中，某一 EP 发生故障停机，则整个 MPP 系统将处于不能正常服务的状态。当前所有的用户会话会被系统断开，不能进行全局登录，只能进行本地登录。因此，为了保证 MPP 系统的高可用性，我们强烈建议采用 DM MPP 与数据守护相结合的部署方案。

## 4.6 MPP 相关系统过程与函数

为了方便 MPP 环境下数据库应用的编写，DM 提供了一些 MPP 相关的系统过程与函数。

  1. SP_SET_SESSION_MPP_SELECT_LOCAL



定义：

```
SP_SET_SESSION_MPP_SELECT_LOCAL(
local_flag int
)
```

功能说明：

MPP 系统下设置当前会话是否只查询本节点数据。如果不设置，表示可以查询全部节点数据。

参数说明：

local_flag: 设置标记，0：查询全部节点数据；1：只查询本节点数据。

举例说明：

```
SP_SET_SESSION_MPP_SELECT_LOCAL(1);
```

  2. SF_GET_SESSION_MPP_SELECT_LOCAL



定义：

```
INT

SF_GET_SESSION_MPP_SELECT_LOCAL()
```

功能说明：

查询 MPP 系统下当前会话是否只查询本节点数据。

返回值：

0 表示查询全部节点数据，1 表示只查询本节点数据。

举例说明：

```
SELECT SF_GET_SESSION_MPP_SELECT_LOCAL();
```

  3. SP_SET_SESSION_LOCAL_TYPE



定义：

```
SP_SET_SESSION_LOCAL_TYPE (
ddl_flag int
)
```

功能说明：

MPP 下本地登录时，设置本会话上是否允许 DDL 操作。本地登录默认不允许 DDL 操作。

举例说明：

ddl_flag：0 表示不允许当前本地会话执行 DDL 操作，1 表示允许。

例 MPP 下本地登录会话。

```
SP_SET_SESSION_LOCAL_TYPE (1);

CREATE TABLE TEST(C1 INT);

SP_SET_SESSION_LOCAL_TYPE (0);
```

  4. SF_GET_EP_SEQNO



定义：

```
INT

SF_GET_EP_SEQNO (

rowid rowid

)
```

功能说明：

根据查询出的行数据的 ROWID 获取本条数据来自哪个 EP。

参数说明：

rowid：行数据的 ROWID。

返回值：

MPP 系统内 EP 的序号。

举例说明：

```
SELECT SF_GET_EP_SEQNO(ROWID);
```

  5. SF_GET_SELF_EP_SEQNO



定义：

```
INT

SF_GET_SELF_EP_SEQNO ()
```

功能说明：

获取本会话连接的 EP 序号。

返回值：

本会话连接的 EP 序号。

举例说明：

```
SELECT SF_GET_SELF_EP_SEQNO();
```

  6. SP_GET_EP_COUNT



定义：

```
INT

SP_GET_EP_COUNT (

SCH_NAME VARCHAR(128),

TAB_NAME VARCHAR(128)

)
```

功能说明：

统计 MPP 环境下表在各个节点的数据行数。

参数说明：

SCH_NAME：表所在模式名

TAB_NAME：表名

举例说明：

```
SP_GET_EP_COUNT('SYSDBA','T');
```

## 4.7 MPP 下系统过程与系统视图常见用法

本节给出一些 DM MPP 环境下，数据库应用对系统过程与系统视图的常见用法示例，关于系统视图的具体介绍可参见《DM8 系统管理员手册》附录 2。

  1. 获取会话连接的 EP 的节点序号



```
SELECT SF_GET_SELF_EP_SEQNO();
```

  2. 根据 ROWID 获取本行数据来自哪个 EP



```
SELECT SF_GET_EP_SEQNO(ROWID);
```

  3. 获取 EP 节点配置信息



```
SELECT * FROM V$MPP_CFG_ITEM WHERE SF_GET_EP_SEQNO(ROWID) = SF_GET_SELF_EP_SEQNO();
```

  4. 获取当前会话连接的实例名



```
SELECT NAME FROM V$INSTANCE WHERE SF_GET_EP_SEQNO(ROWID) = SF_GET_SELF_EP_SEQNO();
```

  5. 获取 MPP 系统内所有 EP 的所有会话



```
SELECT * FROM V$SESSIONS;
```

  6. 获取当前连接的实例上的所有会话



```
SELECT * FROM V$SESSIONS WHERE SF_GET_EP_SEQNO(ROWID) = SF_GET_SELF_EP_SEQNO();
```

  7. 获取实例 EP01 上的所有会话



```
SELECT * FROM V$SESSIONS WHERE SF_GET_EP_SEQNO(ROWID) = (SELECT DISTINCT
EP_SEQNO FROM V$MPP_CFG_ITEM WHERE INST_NAME ='EP01');
```

  8. 获取表 TEST 在每个实例上的数据行数



```
CALL SP_GET_EP_COUNT('SYSDBA','TEST');
```

  9. 获取所接实例上的表 TEST 的使用空间



```
SELECT TABLE_USED_PAGES('SYSDBA','TEST');
```

  10. 获取每个实例上的表 TEST 使用空间



```
SELECT TABLE_USED_PAGES('SYSDBA','TEST'), NAME FROM V$INSTANCE;
```

  11. 获取所有实例上表 TEST 的总使用空间



```
SELECT SUM(TOTAL_SIZE) FROM (SELECT TABLE_USED_PAGES('SYSDBA','TEST')
TOTAL_SIZE, NAME FROM V$INSTANCE);
```

## 4.8 使用说明

DM MPP 支持绝大多数单机版 DM 的功能，但在某些小的功能点使用上存在一些使用限制，具体如下：

  * MPP 环境下，不支持创建 SET NULL 或 SET DEFAULT 约束检查规则的引用约束；
  * MPP 环境下不支持创建外部表、间隔分区表；
  * MPP 环境下不支持修改表的 ADD [COLUMN] \< 列名 >[\<IDENTITY 子句 >]子句；
  * MPP 环境下不支持创建位图索引、空间索引、数组索引；
  * MPP 环境下位图连接索引涉及的维度表需 FULLY 分布；
  * MPP 环境下不支持索引的 ONLINE 选项；
  * MPP 环境下不支持视图的 WITH CHECK OPTION 操作；
  * MPP 环境下不支持闪回功能；
  * MPP 环境下不支持物化视图日志；
  * MPP 各 EP 创建数据库时指定的编码格式应相同，否则可能造成乱码问题；
  * MPP 环境下 INI 参数 MVCC_RETRY_TIMES 无效，发生 MVCC 冲突时直接报错；
  * MPP 环境下不支持 DBMS_JOB 包；
  * MPP 环境下日志辅助表不支持 FULLY 分布；
  * MPP 环境下不支持自引用约束。



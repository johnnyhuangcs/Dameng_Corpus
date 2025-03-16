

# 从 SQL Server 移植到 DM

## 一、概述

DM 数据库和 SQL Server 结构存在差异，TSQL 语法也存在差异，DM 数据库针对 SQL Server 做了良好的兼容性适配。从 SQL Server 迁移到 DM 数据库，DM 数据库提供多样的迁移工具，本文主要介绍 SQL Server 移植到达梦数据库的具体步骤、移植过程中的注意点及常见适配问题，供用户参考。

## 二、迁移流程

### 2.1 迁移流程图

![企业微信截图_17019399363117.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207170626XQN3WQ8I36QVIEJ3IO)

### 2.2 流程介绍

#### 2.2.1 需求确认

移植会涉及诸多场景，如容灾备份、应用改造/替代、数据库版本升级/回退、数据库替代、业务分流等，不同的场景在数据流向、停机窗口、同步需求、数据处理等方面会有不同的需求，需要针对性地选择迁移工具和方案。

#### 2.2.2 数据库调研

考虑迁移工具版本、驱动版本、基础环境、操作方式、对象个数、对象大小、数据量等均会影响迁移工作的开展，需要对源端和目的端数据库及服务器、业务系统进行调研，确保在满足相关需求的前提下稳定完成迁移。  

确认迁移需求后，源端数据库需提前调研如下信息：

  1. 环境信息。提前了解操作系统层面，确定工具能否使用可视化界面，或者端口号开放情况，可以方便在后期部署安装过程中，及时避开处理问题时的一些干扰项。主要包括对服务器、内存、CPU、网络、端口、安全策略、是否具备可视化界面等信息的调研。
  2. 业务系统信息。提前了解应用系统层面信息，结合应用系统特性，为后面制定迁移策略、迁移时间评估等提供参考。主要包括对业务类型、业务运行时段、停机窗口、数据量、数据增量、并发访问量等信息的调研。
  3. 数据库信息。提前了解迁移数据量、字符编码、归档保留、数据库对象、表空间等信息，为后续迁移做好规划和相关准备工作。



#### 2.2.3 迁移评估

达梦提供了两种工具进行迁移前的评估：

[数据迁移工具 DTS](https://eco.dameng.com/info/products/dts)：提供了异构数据源之间的评估，迁移和对比功能。DM 数据迁移工具采用向导方式引导用户通过简单的步骤完成需要的操作。

[达梦企业管理器 DEM](https://eco.dameng.com/info/products/dem)：支持对 ORACLE、MySQL、SQL Server 等主流数据库迁移到达梦数据库进行在线采集评估和自动转化，并提供兼容报告。

除此之外，需要人工对大表、大字段表是否单独迁移进行评估及确定如何配置大表、大字段表的迁移策略。

#### 2.2.4 移植工具选择

达梦公司提供了三种移植工具：数据迁移工具 DTS、数据复制软件 DMDRS 和数据集成软件 DMDIS，可满足不同移植场景的使用需求，详细内容参考[移植工具介绍](https://eco.dameng.com/document/dm/zh-cn/start/oracle_dm#2.2.4%20%E7%A7%BB%E6%A4%8D%E5%B7%A5%E5%85%B7%E9%80%89%E6%8B%A9)。

#### 2.2.5 制定移植计划

根据需求分析和数据库调研，结合每个应用系统的具体要求，选择合适的迁移工具，基于数据迁移的基本原则和迁移工具评估结果，制定合理的移植计划避免任何可能遗漏的步骤，保障迁移工作的稳定实施。

#### 2.2.6 移植实施

对于异构数据库移植到达梦，在正式迁移前，需要根据源端数据库的相关调研信息，对目的库的实例、表空间、用户等进行配置，以提高数据库间的兼容性，保障后续移植工作稳定进行。

同时，达梦数据库的迁移工具均具有自动转换功能。大多数情况下，可通过相关迁移工具进行对象和数据移植，但由于异构数据库间语法并非 100% 兼容，少量数据则需要进行手动移植。

#### 2.2.7 移植结果校验

在进行正式环境的数据移植时，每一条数据都是真实的，有效的且完整的，在迁移完成后，必须要源端数据库的所有对象都准确无误的迁移到目的端，一旦出现缺少对象、缺少数据和数据内容不正确的情况，可能会导致历史记录缺少，甚至某些功能模块失效等严重后果。因此在迁移完成后，需要确认是否存在迁移后的数据量、数据内容和对象个数与源库不一致的问题，如果不一致应进行对应的维护。

#### 2.2.8 移植后收尾工作

移植后的收尾工作包括：索引补录、更新统计信息、备份、整理对象脚本等内容，保障移植工作的完整性。

#### 2.2.9 应用移植与优化

一般情况下，源端数据库迁移完成后，直接修改应用连接到达梦数据库。为了验证系统移植的完整性，还需要进行应用的相关功能和性能测试，并改造应用到连接达梦数据库的最佳状态。

此外，在对应用系统进行全面测试后，排除移植过程中错误的地方，还应对数据库中的慢 SQL 进行优化，保证移植后的系统高效运行。

## 三、移植过程

### 3.1 需求确认及调研

#### 3.1.1 需求确认

本例构建了 SQL Server 2022 单机示例库，并介绍利用 DTS 工具从 SQL Server 2022 移植到 DM8 数据库的详细步骤，以供参考。

#### 3.1.2 数据库调研

  * **源端信息**



|**调研项**|**调研结果**|
|----|----|
|数据库后台操作系统|Red Hat Linux|
|数据库架构|单机|
|数据库版本|Microsoft SQL Server 2022|
|待迁移数据库名|bk_stores|
|IP/端口信息|192.168.10.10/1433|
|用户名/密码|xx/xxxxx|
|字符集编码|简体中文 GBK|
|需要移植的对象|表（数据量）、视图、触发器、存储过程、函数|


> **注意**
>
> 针对替换 SQL Server 上线运行的正式移植情形下，还需调研源端服务器、CPU、网络、业务类型等信息，并根据实际情况填写。



相关信息查询方法如下：

```
--数据库版本
SELECT @@VERSION

--数据库名
select db_name()

--字符集编码
SELECT COLLATIONPROPERTY ( 'Chinese_PRC_Stroke_CI_AI_KS_WS', 'CodePage') 
/*
936 简体中文GBK；950 繁体中文BIG5；437 美国/加拿大英语；
932 日文；949 韩文；866 俄文；65001 unicode UFT-8 
*/

--根据指定用户并统计该用户下的各对象类型和数目
select type,COUNT(*) from sys.all_objects where schema_id=1 and
parent_object_id=0 and is_ms_shipped=0 group by type;

--统计指定用户下所有的对象，并记录到新的记录表中
create table sql_objects(obj_owner varchar(100),obj_name 
varchar(100),obj_type varchar(50)); 
insert into sql_objects select DB_NAME(),name,type from sys.all_objects 
where schema_id=1 AND type IN ('U','V','FN','P','TR'); 
select * from sql_objects;

--统计每个表的数据量到表数据记录表。
create table sql_tables(tab_owner varchar(100),tab_name
varchar(100),tab_count int);
insert into sql_tables
select DB_NAME(),a.name, b.rows
from sysobjects AS a INNER JOIN sysindexes AS b ON a.id = b.id
where (a.type = 'u') AND (b.indid IN (0, 1))
order by a.name,b.rows desc;
select * from sql_tables;
```

通过以上 SQL 统计出如下对象信息统计表：

|**类型**|**说明**|
|--|--|
|库名|bk_stores|
|模式|3|
|表数目|14|
|视图数目|2|
|存储过程|1|
|函数|1|
|触发器|1|
|数据量|9085 条|


  * **目的端信息**



清楚目的端系统环境信息以便于选择对应版本的 DM 数据库进行安装。

|**调研项**|**调研命令**|
|----|----|
|服务器品牌/型号|dmidecode|
|服务器操作系统|cat /etc/os-release|
|内存容量|cat /proc/meminfo|
|CPU 型号/核数|cat /proc/cpuinfo|
|端口策略|是否与目的端网络、端口互通|
|安全策略|是否有软件、硬件相关安全限制（比如堡垒机、网闸、文件摆渡）|
|是否具备可视化界面|可视化提供的方式（直连、Xmanager、VNC、BMC 等）|


### 3.2 迁移评估

本例中，使用 DTS 工具对源端数据库进行迁移评估，详细步骤如下。DEM 工具迁移评估步骤详见[《从 Oracle 移植到 DM 》-DEM 迁移评估](https://eco.dameng.com/document/dm/zh-cn/start/oracle_dm#3.2.1%20DEM%20%E8%BF%81%E7%A7%BB%E8%AF%84%E4%BC%B0)。

  1. 新建工程。工程名为 SQL Server-DM。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205161747OFCG5COPTIBXD7FGLN)

  2. 新建评估。评估名称为 access。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205161820A5H9TEOL9F8NDQOADB)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205161909HWHKLA6RD9U4OT3NJ2)

  3. 选择评估方式为 SQLServer==>DM。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023120516194087ZSCRU64W0FOVM9E1)

  4. 输入源端 SQL Server 数据库信息。一般默认的驱动都比较低，会造成迁移步骤或者迁移过程中出现异常，建议在此处手动设置指定驱动，并更换为对应数据库服务器上的驱动包。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205162005MW9UY5QC576M8811A7)

  5. 设置评估选项。本例中评估对象为所有的 DBO、Production 和 sales 模式下的所有表，并统计表信息，包括表类型、大小、索引信息、行数、字段信息、约束信息。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205162022RIADGL76OW49IDQTZU)

  6. 选择要评估的模式：DBO、Production 和 sales。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205162042ZFTH80XIYWJVZ7EBT9)

  7. 选择指定模式下的对象。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205162058KKCLYML0P6D6X6ASNZ)

  8. 检查评估任务，无误后点击“完成”，即可开始评估。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205162132CB39JT3B2HUMI68143)

  9. 评估完成，结果如下表。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205162153WSPJAH4ZTF9D7G393Y)

  10. 点击查看评估报告，可查看详细的兼容信息，包括：数据库信息、对象兼容信息、表统计信息。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231205162209XAN2AEKFPFAYDUNSVM)

由上图的迁移评估结果可知：经 DTS 工具自动转换功能后，识别到本例待迁移的 SQL Server 数据库中不存在与 DM 数据库不兼容的对象。

> **注意**
>
> 若存在不兼容的对象类型，需导出待移植对象脚本，修改后，再进行迁移。



### 3.3 制定移植计划

根据待移植的 SQL Server 系统信息分析的情况，制定迁移计划：先迁移常规对象及数据（表和视图），再迁移 TSQL（自定义类型、存储过程、函数、触发器）。

> **注意**
>
> 1．源端SQL Server数据库中存在数据量大的表，则建议单独迁移。  
>  2．若源端数据库存在分区表，如果数据量没有超过1亿建议迁移成普通表，在分区列上创建索引。  
>  3．若存在大字段较多的表，建议修改批量的行数，以免造成迁移工具内存溢出。



### 3.4 移植环境准备

本文将介绍利用 DTS 工具进行通用情况下的数据移植工作，其他特殊配置可根据实际需求进行调整。

#### 3.4.1 源端环境准备

在从 SQL Server 向 DM 进行移植准备阶段，需要注意 SQL Server 的移植环境。可下载安装 SQL Server 自带的 SQL Server Management Studio 工具，或第三方的工具 SqlDbx，连接到需要移植的 SQL Server 环境，以便进行移植数据的确认和初步的分析。

DTS 工具为静态迁移工具，在正式开始移植前需要停止所有对数据库的操作，避免数据变化或新数据产生。

#### 3.4.2 目的端环境准备

  * **选择数据库版本**



DM 数据库会定期进行产品更新迭代。在进行项目移植前，需要先确定使用的 DM 数据库版本：

  1. 建议使用当前最新版本的数据库，以保证功能全面和兼容性更强；
  2. 版本优先选择安装完整版；
  3. 版本与硬件环境一定要严格匹配，以减少干扰性的问题出现。



  * **数据库架构选择**



达梦数据库为用户提供多样的数据库架构适配用户不同的业务需求，用户可以根据业务系统需求选择达梦合适的数据库架构进行部署。DM 数据库架构可参考：

[达梦数据库管理系统 DM8](https://eco.dameng.com/info/products/dm8)

[数据共享存储集群 DMDSC](https://eco.dameng.com/info/products/dmdsc)

[数据数据守护集群 DMDataWatch](https://eco.dameng.com/info/products/dmdatawactch)

[达梦新一代分布式集群 DMDPC](https://eco.dameng.com/info/products/dmdpc)

详细安装部署步骤可参考：[达梦在线服务平台-运维指南-数据库规范化部署](https://eco.dameng.com/document/dm/zh-cn/ops/before-installation.html)相关内容，本例中选择目的端数据库架构为单机。

  * **初始化参数设置**



初始化库关键在于初始化参数的设置，从 SQL Server 移植到 DM 数据库初始化参数建议配置如下：

|**数据库参数**|**参数值**|
|-----|-----|
|DB_NAME（数据库名）|DAMENG（根据需求设置）|
|INSTANCE_NAME（实例名）|DMSERVER（根据需求设置）|
|PORT_NUM（端口）|5236（为保证数据库安全，不建议使用默认端口）|
|管理员、审计员、安全员密码|建议首次初始化实例时立即修改密码|
|EXTENT_SIZE（簇大小）|16|
|PAGE_SIZE（页大小）|32|
|LOG_SIZE 日志大小|2048M|
|CHARSET（字符集）|GB18030|
|CASE_SENSITIVE（大小写敏感）|不敏感|
|BLANK_PAD_MODE（尾部空格填充）|否|


部分参数说明：

  1. 页大小 PAGE_SIZE。一旦创建好了数据库，在该库的整个生命周期内，页大小都不能够改变。在 DM 数据库中，页大小可以为 4KB、8KB、16KB 或者 32KB，建议设置页大小为 8KB，除了每个字段的最大长度限制外，每条记录总长度不能大于页面大小的一半。如果系统中存在或者以后可能存在含有较长的字符串类型的表，建议该参数设置为 16 或者 32。页大小设置越大，最后数据文件的物理大小就会越大，系统运行时，每次从磁盘调入内存的数据单位也就越大，所以此处要慎重。
  2. 关于簇大小 EXTENT_SIZE。数据文件使用的簇大小，即每次分配新的段空间时连续的页数，只能是 16 页或 32 页，缺省使用 16 页，使用默认值就可。
  3. 关于大小写敏感 CASE_SENSITIVE。DM 为了兼容不同的数据库，在初始化数据库的时候有一个参数字符串比较大小写敏感，用于确定数据库对象及数据是否区分大小写，默认为区分，不可更改。建议 SQLSERVER 迁移过来的系统，使用大小写不敏感以便和原来系统匹配。
  4. 关于字符集 CHARSET。建议采用默认值 GB18030，如果需要国际字符可以采用 Unicode，GB18030 数字字母占 1 个字节，普通汉字占 2 个字节，部分繁体及少数民族文字占 4 字节，Unicode 在达梦中采用 UTF-8 编码格式，欧洲的字母字符占 1 到 2 个字节，亚洲的大部分字符占 3 个字节，附加字符为 4 个字节。如果只存储中文和字母数字，一般来说 GB18030 更节省空间一些。



更多初始化参数的详细说明可参考达梦数据库安装目录下 doc 目录中的《 DM8_dminit 使用手册》或在数据库运行目录 bin 目录下执行以下命令查看部分初始化参数说明。

```
./dminit help
```

> **注意**
>
> 强烈建议用户在首次安装数据库初始化实例时，立即修改数据库系统用户的初始密码，并设置一定的密码强度，以保障数据安全性。



  * **兼容性参数设置**



从 SQL Server 移植到 DM，需额外注意以下参数。

|**compatibility**|**含义**|**建议值**|
|-------------|-------------|-------------|
|COMPATIBLE_MODE|是否兼容其他数据库模式。0:不兼容，1：兼容 SQL92 标准，2：兼容 ORACLE，3:兼容 MSSQLSERVER，4:兼容 MYSQL，5:兼容 DM6，6:兼容 Teradata。|建议设置为 3，表示部分语法兼容 SQL Server|
|MS_PARSE_PERMIT|是否支持 MSSQLSERVER 的语法。0:不支持；1:支持；2：在 MS_PARSE_PERMIT=1 的基础上，兼容 MSSQLSERVER 的查询项中支持"标识符=列名"或"@ 变量名=列名"用法。当 COMPATIBLE_MODE=3 时，MS_PARSE_PERMIT 的实际值为 1。默认为 0。|建议设置为 1。若开启该参数，需注意检查是否存在 dblink，需要改变 dblink 中 @linkname 语法的使用方式|


可参考如下参数修改步骤：

```
SP_SET_PARA_VALUE(2,'COMPATIBLE_MODE',3);
SP_SET_PARA_VALUE(2,'MS_PARSE_PERMIT',2);
--或
ALTER SYSTEM SET 'COMPATIBLE_MODE'=3 SPFILE;
ALTER SYSTEM SET 'MS_PARSE_PERMIT'=2 SPFILE;
--执行完成后，重启数据库生效
```

在移植准备工作阶段，需先调整以上两个参数，其他兼容性相关参数建议先保持默认值。

  * **创建用户和表空间**



从 SQL Server 移植到 DM，要求必须创建用户自己的用户模式名和表空间，不要把数据移植到系统默认的管理 SYSDBA 用户下和 MAIN 表空间下。

首先需要分析本次移植 SQL Server 源库需要移植的是哪一个或者哪几个库的数据，针对不同的库，对应达梦数据库中不同的用户，然后分别创建这些需要移植的用户的独立表空间；移植设计过程中，建议设计达梦数据库用户名称和 SQL Server 中库名相同。创建属于各个表空间的用户及模式。便于数据的导入导出，同时增加数据隔离性，在遇到问题的时候，更容易分析问题数据所在的位置。

本例中，SQL Server 源库需要移植 bk_stores 数据库中的所有表，对应在 DM 数据库中创建 bk_stores 用户和表空间来管理这些表。DM 数据库中创建用户及表空间 SQL 语句如下：

```
--创建BK_STORES表空间
create tablespace "BK_STORES" datafile 'D:\DM8_20231012\data\DAMENG3\dbo.dbf' size 2048;

--创建用户
CREATE USER "BK_STORES" IDENTIFIED BY "Dameng123" LIMIT FAILED_LOGIN_ATTEMPS 3, PASSWORD_LOCK_TIME 1, PASSWORD_GRACE_TIME 10 DEFAULT TABLESPACE "BK_STORES";

--授予用户权限
grant "PUBLIC","RESOURCE","SOI","SVI","VTI" to "BK_STORES";
```

#### 3.4.3 DTS 工具准备

DTS 迁移工具版本：V8 (Build 2023.04.17)

DTS 迁移工具安装：该工具在安装数据库客户端时已安装完成可以直接使用，工具存放在 DM 数据库安装目录下 tool 文件夹中。

开启 DTS 工具：Linux 环境下进入 tool 目录中执行 ./dts 即可运行 DMDTS 工具，windows 环境下可直接双击启动。

### 3.5 常规对象及数据迁移

#### 3.5.1 表对象迁移

  * **一次性迁移**



对于表比较少，数据量不大的系统，可以通过 DTS 采取一次性迁移，全部选中即可。

  1. 选择迁移方式：SQL SERVER-->DM 迁移。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312041006402S23XU0ZUG00Y93SU5)

  2. 输入源端 SQL Server 数据库连接信息。



（1）主机名：SQL Server 数据库所在的主机名或 IP 地址。

（2）端口：SQL Server 端口号，默认 1433。

（3）用户名：SQL Server 的用户名，默认为 sa。

（4）口令：SQL Server 数据库用户名对应的密码。

（5）数据库名：点【刷新】按钮获取已有的数据库，在数据库下拉列表中选择需要连接的库。如果选择【默认】，则连接到 master 库上。本例选择迁移 bk_stores 数据库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312051622421XSJNN66XZ9P2DHHPP)

  3. 输入目的端 DM 数据库连接信息。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206102102VDTCI71PWQCJ075CK1)

  4. 设置迁移选项。



（1）获取迁移对象方式。迁移对象可以通过 4 种方式指定，如下：

① 导入迁移对象：即从文本文件中读取要迁移的对象，文本文件中内存格式参见导入文件说明，同时也可以通过设置文件格式来设置文件的列分隔符，文本限定符，文件编码等信息。

② 用一条或多条 SQL 指定要迁移的对象：即迁移指定 SQL 的查询结果集。

③ 迁移指定类型的对象：即迁移所有的指定类型的对象，例如迁移指定模式的所有表等，这种方式同类型的对象所有迁移策略一致。

④ 选择迁移对象：即列出所有指定类型的对象供选择，用户可以随意选择其中的对象进行迁移，并可以对每个对象单独配置迁移策略。

（2）迁移策略：可以配置的迁移策略如下：

① 保持对象名大小写。选择保持对象名大小写选项，则迁移对象时目的对象名和源对象名保持一致，否则目的对象名会自动转换为大写，默认的 sql server 数据源迁移到 dm 时自动转换为大写，其他迁移保持对象名大小写。

② 使用默认数据类型映射关系。选中时，使用默认的数据类型映射关系,详细的默认数据类型映射关系可以通过"查看默认类型映射关系"按钮进行映射，否则，可以点击"配置类型映射关系"按钮进行配置，也可以根据全局的配置的数据类型映射关系来映射。

③ 字符长度 x(Z)。对于多字节字符类型，迁移到目的时数据精度扩展 n 倍，如果扩展后精度超过目的支持的最大精度长度，则使用最大的精度长度。SQL Server->dm 具体可以扩展的数据类型为 NCHAR，NVARCHAR。

④ 出错后继续执行。选中该选项，执行迁移任务时，在前面的任务出错后仍然会继续执行后面的任务，否则如果一个迁移任务执行失败，则其后面的任务就不会被执行。

⑤ 使用多线程迁移数据。选中该选项，执行迁移任务时，将使用多线程迁移。

本例中，根据前期调研和迁移评估结果，选择如下配置即可。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204100954PASX0G6Q6JVYK53JEY)

  5. 选择待迁移的模式。本例中，选择迁移 DBO、production 和 sales 模式下的表迁移到 DM 中 BK_STORES 模式下。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206103545BMTM3BF4IAIMF7ONSM)

  6. 选择待迁移模式下的对象。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023120610371789P8XI1P09PH4WX52O)

  7. 配置迁移策略，并将迁移策略应用到其它同类对象。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206103948Z9WE0MLNULBORZ08CS)

  8. 勾选“应用当前选项到其它同类对象”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206143030B3F4PA1IXG3074PRZF)

  9. 检查迁移任务。核对迁移任务，无误后点击“完成”即可开始迁移。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104227BOYU0NQBJRVLOX05XX)

  10. 完成迁移。更多详细迁移信息可点击“查看迁移报告”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023120610424772Z28C3AJ6C9GYIV60)

  * **分批次迁移**



对于表比较多，数据量大的系统，建议分批次迁移，例如先迁移小表再进行大表的迁移。分批次迁移可参考如下步骤：

  1. 在迁移选项页面，选择“迁移指定类型的对象”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312041013302BP0E6X79IJ7PZQ33T)

  2. 选择待迁移模式。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061043146CQ06SNEBBOR3QJ8LI)

  3. 根据实际需求配置表迁移策略。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312041014329KB44U1AGA58ZTGDQV)

#### 3.5.2 视图对象迁移

视图对象迁移需在表对象迁移完成后进行。分为普通视图和索引（物化）视图，不同视图类型迁移方法有所区别：

  1. 普通视图对象迁移。



方法一：使用 DTS 工具迁移视图，此方法适用于批量迁移视图对象。本例中使用本方法迁移，详细步骤如下：

（1）指定视图对象迁移的源模式。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104346PMI9G842ISP5LVSJQF)

（2）选择指定源模式中，需要迁移的视图。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104412Z8TCTONHHS8R1Z3A9L)

（3）审阅迁移任务，无误后，点击“完成”即可开始迁移。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104434JJ52ROR42PFKNT0CQ9)

（4）完成迁移。更多迁移详细内容可点击“查看迁移报告”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023120610445535PD3GCKPAP042V38J)

方法二：从源 SQL Server 中获取视图定义，在目的库手动创建视图。此方法适用于所需迁移对象较少，或者对方法一中迁移出错的视图单独处理。

2．索引（物化）视图对象迁移。

从源 SQL Server 中获取物化视图定义，在目的库手动创建物化视图。

> **注意**
>
>
> 1．视图查询对象依赖迁移及权限授予。由于视图查询依赖于相关的表或者其他数据库对象，在迁移视图之前需要首先迁移视图所依赖的对象，并授予视图用户相关对象的权限。  
>  2．目的端物化视图刷新方式支持及设置。由于目的端 DM
> 视具体架构（单库、MPP）的不同存在对物化视图日志表的支持程度的差异，迁移物化视图时，需要视目的端架构，考虑是否变更物化视图刷新方式，例如：增量刷新改为全量刷新。



### 3.6 TSQL 迁移

接下来对自定义类型、存储过程、函数、触发器进行移植。可通过两种方法进行迁移：DTS 工具迁移或手动迁移。

#### 3.6.1 DTS 工具迁移

使用 DTS 工具进行迁移。此方法适用于迁移与 DM 数据库兼容的对象。根据前期调研和迁移评估，本例中，可使用 DTS 工具进行存储过程、函数、触发器迁移。详细步骤如下：

  1. 指定待迁移的模式。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104531QKDVV7QX48EA6WLD18)

  2. 指定待迁移模式下的 TSQL 对象。可在“转换”中查看 TSQL 的详细内容，并支持手动编辑相关 SQL 语句。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104552FLWFY20UJK8EN8NGS0)

  3. 检查迁移任务，无误后，点击“完成”即可开始迁移。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104617YPL6IFCI1Q392L8VHS)

  4. 迁移完成。更多迁移信息可点击“查看迁移报告”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206104637VRGO42BVQ0P2B96EMH)

#### 3.6.2 手动迁移

由于 SQL Serve 与 DM 的语法差异较大，可能存在部分语法不兼容的情况，此时建议手动迁移。详细可参考如下步骤：

  1. 导出待移植对象的脚本。



方法一：使用 SQL Server Management Studio、SqlDbx 等工具导出自定义类型、存储过程、函数、触发器等 PL/SQL 脚本。可导出表、存储过程、用户定义函数、用户、视图等内容。以下为使用 Server Management Studio 进行对象脚本导出。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204102659NXU6KIWP2ZT4IGLQXJ)

方法二：通过 SQL 导出。以下提供常用的导出 SQL 命令。

（1）导出某个库中的全部触发器：

```
DECLARE @trname VARCHAR(100)
DECLARE CURSOR_DATA CURSOR FOR
SELECT name from sys.all_objects where schema_id=1 AND type = 'TR'
open CURSOR_DATA
FETCH NEXT FROM CURSOR_DATA INTO @trname
WHILE @@FETCH_STATUS=0
BEGIN
EXEC sp_helptext @trname
FETCH NEXT FROM CURSOR_DATA INTO @trname
END
CLOSE CURSOR_DATA
DEALLOCATE CURSOR_DATA
```

（2）导出存储过程：

```
DECLARE @trname VARCHAR(100)
DECLARE CURSOR_DATA CURSOR FOR
SELECT name from sys.all_objects where schema_id=1 AND type = 'P'
open CURSOR_DATA
FETCH NEXT FROM CURSOR_DATA INTO @trname
WHILE @@FETCH_STATUS=0
BEGIN
EXEC sp_helptext @trname
FETCH NEXT FROM CURSOR_DATA INTO @trname
END
CLOSE CURSOR_DATA
DEALLOCATE CURSOR_DATA
```

（3）导出函数：

```
DECLARE @trname VARCHAR(100)
DECLARE CURSOR_DATA CURSOR FOR
SELECT name from sys.all_objects where schema_id=1 AND type = 'FN'
open CURSOR_DATA
FETCH NEXT FROM CURSOR_DATA INTO @trname
WHILE @@FETCH_STATUS=0
BEGIN
EXEC sp_helptext @trname
FETCH NEXT FROM CURSOR_DATA INTO @trname
END
CLOSE CURSOR_DATA
DEALLOCATE CURSOR_DATA
```

（4）SQL Server 的作业：

由于和达梦机制不同，需要了解具体意义后在达梦的代理中重新配置，可以在最后进行该部分的移植：使用管理工具登录，代理-> 创建代理环境-> 作业-> 新建作业-> 根据需求配置作业步骤和作业调度即可。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204102908WZRGZIH590K2WVTDSI)

2．运行脚本并处理错误。

在 DM 数据库中，运行从 SQL Server 中导出的脚本文件，并处理相关错误。例如：迁移从 SQL SERVER 中导出的脚本文件报错：语法分析错误。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312041029384T31G72UJ8HPK1YBQ2)

此时可通过如下方式解决：

  1. 由于使用达梦的保留字冲突导致，建议如果可以更换尽量更换，如果不行可以采用屏蔽关键字的方法进行屏蔽。
  2. 字符兼容问题，是否使用了中文的标点符号。
  3. SQL Server 和 DM 有语法不一致的地方，需要根据具体问题具体分析。



### 3.7 核对移植结果

  1. 统计达梦数据基础信息。



```
--统计页大小
select page;
--通过编码格式
select unicode;
--统计大小写敏感参数
select case_sensitive;
```

  2. 统计达梦数据中的对象以及表数据量。



（1）根据指定用户统计用户下的各对象类型和数目。

```
--创建辅助表记录对象数目
create table dm_objects(obj_owner varchar(100),obj_name varchar(100),obj_type varchar(50));
insert into dm_objects select owner,object_name,object_type from all_objects where owner='bk_stores';---用户名根据实际情况调整

--查询用户bk_stores下的各对象类型和数目
SELECT OBJ_TYPE,COUNT(*) FROM dm_objects GROUP BY OBJ_TYPE;
```

达梦数据库中对象查询结果如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061418557BN1Z3P5BD5V3GOE2D)

源端 SQL server 调研时统计的对象如下：

|**类型**|**说明**|
|--|--|
|模式|3|
|表数目|14|
|视图数目|2|
|存储过程|1|
|函数|1|
|触发器|1|


（2）DM 端统计各个表的数据量到表数据记录表。

```
--创建辅助表
create table dm_tables(tab_owner varchar(100),tab_name
varchar(100),tab_count int);

--统计各个模式下的表中数据量
begin
for rec in (select owner,object_name from all_objects where
owner='bk_stores' and object_type='TABLE') loop ---owner用户名根据实际情况调整
Begin
execute immediate 'insert into dm_tables select '''|| rec.owner
||''','''|| rec.object_name ||''',count(*) from '|| rec.owner || '.'
|| rec.object_name;
exception when others then
print rec.owner || '.' || rec.object_name || 'get count error';
end;
end loop;
end;

select * from dm_tables;
```

DM 端表的数据量统如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206142058EVA0F78GMTHSH31923)

源端 SQL server 调研时统计的表的数据量结果如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061417505XZJL372EZ60SKQ9ZY)

  3. 如果对象较多可通过以下 SQL 对比达梦数据库中对象和 Sql Server 库中对象以及数据差异。



（1）在 DM 数据库中通过以下 SQL 比对对象，找出没有迁移的对象。

```
select * from bk_stores.sql_objects where (obj_owner,obj_name) not in (
select obj_owner,obj_name from dm_objects
) --and obj_type='TABLE'
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206142209VT7O9Z99M7IHP0IUPR)

（2）在 DM 数据库中通过以下 SQL 对比表数据量，找出数据量不相等的表。其中，sql_tables 为迁移过来的记录 SQL server 端数据量的辅助表，dm_tables 为 DM 端记录数据量的辅助表。

```
select a.tab_owner,a.tab_name,a.tab_count-b.tab_count from sql_tables
a, dm_tables b
where a.tab_owner=b.tab_owner and a.tab_name=b.tab_name
and a.tab_count-b.tab_count<>0
```

![企业微信截图_17018286266555.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206142220D5DGN1HFMI66WAE0Y0)

如果查询结果为空，则表示源端与目的端对象或数据量一致。这里的 sql_tables 表出现数据量不一致是因为 SQL server 中没有统计辅助表 sql_tables 的数据量，而 DM 中统计了 SQL server 辅助表 sql_tables 的数据量。所以迁移后两端数据量一致。

### 3.8 数据库移植完毕后收尾工作

#### 3.8.1 更新统计信息

数据核对完成无问题后，应进行一次全库的统计信息更新工作。统计信息更新脚本示例如下：

```
DBMS_STATS.GATHER_SCHEMA_STATS('HNSIMIS',--HNSIMIS 为模式名
100,TRUE,'FOR ALL COLUMNS SIZE AUTO',8);
```

更新统计信息的目的在于大批量迁移数据后数据库系统未对用户数据进行全面分析存在错误的统计信息，可能会导致数据库优化器根据错误的统计信息得到错误的查询计划，严重影响查询性能。

更多更新[统计信息](#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)方式可参考：[统计信息](#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)。

#### 3.8.2 数据备份

在对数据更新完统计信息后，在数据量不大，磁盘空间足够的情况下应进行一次数据备份工作。数据备份有三种方式：

  1. 正常停止数据库后，拷贝备份到实例目录或保存数据文件的其他目录。
  2. 开启归档日志后，进行物理备份。
  3. 逻辑备份，使用 dexp 工具进行逻辑导出。



此外，通常生产系统都需要制定定时备份任务，备份时间点建议避开业务高峰期，可根据需要配置备份作业任务。

#### 3.8.3 整理对象脚本

整理所有数据库对象脚本，这是为了对移植情况进行记录和备份，方便再次进行数据迁移。备份的数据库对象脚本包括：序列定义及当前值，表定义，索引定义，视图定义，函数定义，存储过程定义，包及包体定义、自定义类型和同义词定义。

脚本对象导出可通过达梦数据迁移 DTS 工具来进行。执行步骤如下：

  1. 选择 DM-->SQL 迁移方式，将所有对象脚本迁移为 SQL 文件。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023120410342326M6CD2QMBP2AVDWD1)

  2. 输入 SQL 脚本文件信息，可按实际需求进行选择。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312041034507CUFU4JVK27XNOS9N2)

  3. 选择迁移方式和迁移策略。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204103602HLJSZ8U6IC496QQTG0)

  4. 选择待迁移的模式。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206112547NZQDHVVOCCM4BLUPIU)

  5. 选择待迁移模式下的对象。后面直接点击“下一步”确认迁移信息后，即可完成迁移。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206112607RC7Q8F882Y5APXWEIT)

### 3.9 应用移植与系统优化

一般情况下，SQL Server 数据库迁移完成后，应用程序几乎无需修改，仅需做少量配置修改就可以完成移植。通常需要检查以下修改项：驱动包，数据库连接串，数据库连接池配置，中间件日志级别，以及应用服务器中 hosts 文件配置。应用移植及测试步骤此处不再详述。

应用适配可参考：[达梦在线服务平台-应用开发指南](https://eco.dameng.com/document/dm/zh-cn/app-dev/)模块。

数据库和应用系统移植完毕后开启 sql 日志，对系统进行全面测试，排除移植过程中错误的地方，对慢 sql 语句进行优化。可使用 DMLOG 工具对 SQL 日志进行分析，详细内容见：[达梦 SQL 日志分析工具 DMLOG](https://eco.dameng.com/document/dm/zh-cn/ops/tool-monitor.html#%E4%B8%89%E3%80%81%E8%BE%BE%E6%A2%A6%20SQL%20%E6%97%A5%E5%BF%97%E5%88%86%E6%9E%90%E5%B7%A5%E5%85%B7%20DMLOG)。

## 四、常见问题

### 4.1 字符串截断

#### 4.1.1 问题描述

从 SQL Server 迁移到 DM 的时候，报错：字符串截断。

出现字符串截断的一般都是字段中含有中文。可能因为 DM 初始化的时候选择的字符集是 Unicode（即 utf-8），该字符集的国际标准是一个汉字占 3 个字节，而 SQL Server 中默认情况下一个汉字占 2 个字节，此时迁移的时候会报：字符串截断错误。

#### 4.1.2 处理措施

遇到该错误有 3 种解决办法。

（1）是在初始化的时候，字符集选择 gb18030，如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312041038504M98NRTJPCHNSTSG4N)

（2）是选择 VARCHAR 类型以字符为单位，如图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204103926HVTT9R5F6AYBY05LMG)

（3）因为前面 2 种都需要重新初始化数据库，第三种不需要重新初始化数据库即可解决，即在选择迁移方式的时候，选择字符长度映射关系为 2，如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204103953GI908ASHCDPPGRU4J3)

### 4.2 记录超长

#### 4.2.1 问题描述

DM 在初始化的时候，选择的页大小影响后面表每行数据的长度，表每行的长度之和（普通数据类型）不能超过一页大小，如果超过 1 页大小即报错：记录超长。

#### 4.2.2 处理措施

  1. 找到表中 varchar 类型比较长的（如 varchar（8000）这种），修改成 text 类型；
  2. 初始化的时候页大小选择 32k。（对于表中 varchar2 类型较长，并且字段较多的情况不太适合，这种情况采用方法 1 解决）。



### 4.3 违反唯一性约束

#### 4.3.1 问题描述

迁移过程中报错：“违反表 XXX 唯一性约束”。

这种情况是因为表中设置了唯一性约束或者主键约束，但是数据中有重复记录造成的。这种情况有可能是原始库的约束被禁用了，或者数据重复迁移造成的。

#### 4.3.2 处理措施

在确定源数据没有问题的情况下，迁移的时候选择删除后再拷贝，在迁移界面中，选中迁移的表，然后点击转换。如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206112631H9LP84G7F2IBVPWF67)

在弹出的窗口中选择删除后拷贝，设置完成后即可迁移成功。如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023120410414838H33GR3HZQVSGBHNT)

### 4.4 违反引用约束

#### 4.4.1 问题描述

迁移过程中，出现报错：“违反引用约束 xxx”。

这种问题主要是由外键约束造成的，父表的数据没有迁移，先迁移了子表的数据。

#### 4.4.2 处理措施

迁移的时候先不迁移外键等约束，在选择好要迁移的表时，点击转换，按照下面步骤迁移。

（1）第一次只选择表定义，不选择约束等。如图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204104231FFJV1LT1IFRSPEM7H8)

（2）第一次迁移完成后（确保没有错误），第二次只选择数据，如图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204104337JMH6WM0UG3U0XAJFAW)

（3）第三步选择约束、索引等，如下图。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312041044474DHF0BJ0H5CVMC7ZW6)

按照上面步骤，即可完成迁移。

### 4.5 无效的用户对象

#### 4.5.1 问题描述

迁移过程中报错：无效的表或视图名 xxx。

这个问题一般是视图迁移过程中顺序问题。因为在迁移视图之前，没有将视图依赖的表迁移过去。

#### 4.5.2 处理措施

严格按照迁移的顺序，先迁移表，然后再迁移视图、存储过程、函数等的顺序迁移即可。

### 4.6 无效的日期或时间类型

#### 4.6.1 问题描述

迁移过程中报错：无效的日期或者时间类型。

此问题一般是使用 DTS 迁移工具从 SQL Server 迁移数据时，对于表的定义，会将 DATETIME 时间类型的字段定义转换成 TIMESTAMP 类型；但是对于表的数据，会将 DATETIME 类型的字段数据转换成 TIMESTAMP WITH TIME ZONE，格式变成 ’2021-10-24 12:00:00.000 +08:00’。使得实际数据和字段定义不相匹配，导入失败报错。

#### 4.6.2 处理措施

可以通过将迁移的目标 DM 库迁移用户下的所有 TIMESTAMP 类型字段转换为 TIMESTAMP WITH TIME ZONE 类型，待表数据迁移完后再改回来解决。

## 五、更多帮助

更多 SQL SERVER 移植到 DM 相关常见问题处理可参考：[从 SQL Server 迁移到 DM](https://eco.dameng.com/document/dm/zh-cn/faq/faq-sqlserver-dm8-migrate.html)。

更多 DTS 工具使用详情可参考 DTS 工具“帮助主题”，具体位置见下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206113201FH9UKK98WMLA7YKJSK)

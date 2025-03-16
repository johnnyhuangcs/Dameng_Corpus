

# 从 MySQL 移植到 DM

## 一、概述

随着国家对信创工程的日益重视，目前拥有自主知识产权的国产数据库将成为主流。DM 数据库和 MySQL 体系结构上存在差异，SQL 语法也存在一定的差异，DM 数据库针对 MySQL 做了良好的兼容性适配。本文主要介绍 MySQL 移植到达梦数据库的具体步骤、移植过程中的注意点及常见适配问题，供用户参考。

## 二、迁移流程

### 2.1 迁移流程图

![企业微信截图_17019399363117.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207170558W07NVO7WS8ICXQ7LKU)

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

达梦提供了三种工具进行迁移前源端数据库兼容性评估：

[数据迁移工具 DTS](https://eco.dameng.com/info/products/dts)：提供了异构数据源之间的评估，迁移和对比功能。DM 数据迁移工具采用向导方式引导用户通过简单的图形化进行兼容性评估操作。

[达梦企业管理器 DEM](https://eco.dameng.com/info/products/dem)：支持对 ORACLE、MySQL、SQL Server 等主流数据库迁移到达梦数据库进行在线采集评估和自动转化，并提供兼容报告。

[SQLark 百灵连接](https://www.sqlark.com/?s=eco-com-doc)：支持对 ORACLE、MySQL 等主流数据库迁移到达梦数据库进行在线采集评估和自动转化，一键生成源数据库画像，获取源库对象、不兼容对象、大表、大字段表等迁移重难点情况，评估本次迁移需要投入的工作量。SQLark 会根据源库画像，生成合理的迁移策略，为开发者后续进行的自动/手动迁移提供迁移方案参考。

除此之外，需要人工对大表、大字段表是否单独迁移进行评估及确定如何配置大表、大字段表的迁移策略。

#### 2.2.4 移植工具选择

达梦公司提供了三种移植工具：数据迁移工具 DTS、SQLark 百灵连接、数据复制软件 DMDRS 和数据集成软件 DMDIS，可满足不同移植场景的使用需求，详细内容参考[移植工具介绍](https://eco.dameng.com/document/dm/zh-cn/start/oracle_dm#2.2.4%20%E7%A7%BB%E6%A4%8D%E5%B7%A5%E5%85%B7%E9%80%89%E6%8B%A9)。

#### 2.2.5 制定移植计划

根据需求分析和数据库调研，结合每个应用系统的具体要求，选择合适的迁移工具，基于数据迁移的基本原则和迁移工具评估结果，制定合理的移植计划避免任何可能遗漏的步骤，保障迁移工作的稳定实施。

#### 2.2.6 移植实施

对于异构数据库移植到达梦，在正式迁移前，需要根据源端数据库的相关调研信息，对目的库的实例参数、表空间、用户等进行配置，提高达梦对于 MySQL 数据库的兼容性，保障后续移植工作稳定进行。

同时，达梦数据库的迁移工具均具有自动转换功能。大多数情况下，可通过相关迁移工具进行对象和数据移植，但由于异构数据库间语法并非 100% 兼容，少量数据则需要进行手动移植。

#### 2.2.7 移植结果校验

在进行正式环境的数据移植时，每一条数据都是真实的，有效的且完整的，在迁移完成后，必须源端数据库的所有对象都准确无误地迁移到目的端，一旦出现缺少对象、缺少数据和数据内容不正确的情况，可能会导致历史记录缺少，甚至某些功能模块失效等严重后果。因此在迁移完成后，需要确认是否存在迁移后的数据量、数据内容和对象个数与源库不一致的问题，如果不一致应进行对应的维护。

#### 2.2.8 移植后收尾工作

移植后的收尾工作包括：索引补录、更新统计信息、备份、整理对象脚本等内容，保障移植工作的完整性。

#### 2.2.9 应用移植与优化

一般情况下，源端数据库迁移完成后，直接修改应用连接到达梦数据库。为了验证系统移植的完整性，还需要进行应用的相关功能和性能测试，确保改造后的应用系统和数据库处于一个最佳状态。

此外，在对应用系统进行全面测试后，排除移植过程中错误的地方，还应对数据库中的慢 SQL 进行优化，保证移植后的系统高效运行。

## 三、移植过程

### 3.1 需求确认及调研

#### 3.1.1 需求确认

本例构建了 MySQL 8.0 的单机示例库，并介绍利用 DTS 工具从 MySQL 8.0 移植 dbtest 库中的所有对象到 DM8 数据库的详细步骤，以供参考。

#### 3.1.2 数据库调研

  1. **MySQL 源端信息**



  * **环境信息**



|**调研项**|**说明**|
|---|---|
|应用后台操作系统|Red Hat Linux|
|数据库后台操作系统|Red Hat Linux|
|后台数据库|MySQL|
|应用开发平台|JAVA|
|应用开发接口|JDBC|
|需要移植的数据库对象|表（数据量）、分区表、视图、自定义类型、触发器、存储过程、函数、其他|


  * **数据库信息**



提前对源端 MySQL 数据库做相关了解，一方面为后面的安装提供参考依据，另一方面提前了解迁移数据量、字符编码、归档保留等信息为后续迁移提前做好充分准备。如下对源端 MySQL 的调研：

|**调研项**|**说明**|
|---|---|
|数据库架构|单机|
|节点数|1|
|数据库版本|MySQL 8.0|
|待迁移库|dbtest|
|IP 地址/端口|192.168.32.128/3306|
|服务器运维用户名(密码)|root|
|数据库用户名(密码)|xxxxx|
|字符集编码|UTF-8|
|大小写敏感|不敏感|
|是否以字节为单位|/|
|归档保留策略|/|


> **注意**
>
> 如果 MySQL 是以字节为单位，在使用 DMDTS 进行迁移时需设置数据类型映射，将 varchar 和 char 类型映射为 varchar(
> char) 类型。



使用  DMDTS 将 varchar 和 char 类型映射为 varchar( char) 类型详细设置方法可参考：[使用 DMDTS 迁移 mysql 到 DM 设置 varchar 转 varchar( N char)](https://eco.dameng.com/document/dm/zh-cn/faq/faq-mysql-dm8-migrate.html#%E4%BD%BF%E7%94%A8%20DMDTS%20%E8%BF%81%E7%A7%BB%20mysql%20%E5%88%B0%20DM%20%E8%AE%BE%E7%BD%AE%20varchar%20%E8%BD%AC%20varchar(%20N%20char))。

  * **迁移对象统计**



迁移前可先统计出需要迁移的库中的对象，提前了解迁移数据量、迁移数据对象、迁移数据类型为考虑迁移时长、停机窗口提供依据，MySQL 8.0 中统计库中的对象方法如下：

（1）统计指定库中表的数目。

```
SELECT COUNT(*) TABLES, TABLE_SCHEMA FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '数据库名称' GROUP BY TABLE_SCHEMA;
```

（2）统计指定库中视图的数目。

```
SELECT TABLE_SCHEMA,COUNT(*) VIEWS FROM INFORMATION_SCHEMA.VIEWS  
WHERE TABLE_SCHEMA  = '数据库名称'  GROUP BY TABLE_SCHEMA;
```

（3）统计指定库中所有的存储过程。

```
SELECT SPECIFIC_NAME FROM INFORMATION_SCHEMA.ROUTINES
WHERE ROUTINE_TYPE='PROCEDURE' AND ROUTINE_SCHEMA='数据库名称';
```

（4）统计指定库中所有的函数。

```
SELECT SPECIFIC_NAME FROM INFORMATION_SCHEMA.ROUTINES
WHERE ROUTINE_TYPE='FUNCTION' AND ROUTINE_SCHEMA='数据库名称';
```

（5）统计指定库中所有的触发器。

```
SELECT TRIGGER_SCHEMA,TRIGGER_NAME FROM INFORMATION_SCHEMA.TRIGGERS 
WHERE TRIGGER_SCHEMA= '数据库名称';
```

（6）将指定库中所有表数据量记录到辅助表。

```
CREATE TABLE MYSQL_TABLES(TAB_OWNER VARCHAR(100),TAB_NAME VARCHAR(100),TAB_COUNT INT);
INSERT INTO MYSQL_TABLES SELECT TABLE_SCHEMA,TABLE_NAME,TABLE_ROWS FROM INFORMATION_SCHEMA.TABLES 
WHERE TABLE_SCHEMA = '数据库名称' ORDER BY TABLE_ROWS DESC;
```

通过以上 SQL 统计出如下对象信息统计表：

|**调研项**|**说明**|
|---|---|
|库名|dbtest|
|表数目|14|
|视图数目|3|
|存储过程|4|
|函数|1|
|触发器|1|
|数据量|3931 条|


> **注意**
>
> 由于 MySQL 5.7 与 MySQL 8.0 的语法有所区别，MySQL 5.7 中统计库中的对象方法如下：



（1）统计指定库中表的数目。

```
SELECT COUNT(*) TABLES, TABLE_SCHEMA FROM INFORMATION_SCHEMA.TABLES
WHERE TABLE_SCHEMA = '数据库名称' GROUP BY TABLE_SCHEMA;
```

（2）统计指定库中视图的数目。

```
*SELECT TABLE_SCHEMA,COUNT(*) VIEWS FROM INFORMATION_SCHEMA.VIEWS  WHERE TABLE_SCHEMA  = '数据库名称'  GROUP BY TABLE_SCHEMA;
```

（3）统计指定库中所有的存储过程。

```
SELECT `NAME` FROM MYSQL.PROC WHERE DB = '数据库名称' AND `TYPE` = 'FUNCTION';
```

（4）统计指定库中所有的函数。

```
SELECT `NAME` FROM MYSQL.PROC WHERE DB = '数据库名称' AND `TYPE` = 'FUNCTION';
```

（5）统计指定库中所有的触发器。

```
SELECT TRIGGER_SCHEMA ,TRIGGER_NAME FROM MYSQL. TRIGGERS WHERE TRIGGER_SCHEMA= '数据库名称';
```

（6）将指定库中所有表数据量记录到辅助表。

```
CREATE TABLEMYSQL_TABLES(TAB_OWNER VARCHAR(100),TAB_NAME VARCHAR(100),TAB_COUNT INT);
INSERT INTO MYSQL_TABLES SELECT TABLE_SCHEMA,TABLE_NAME,TABLE_ROWS FROM INFORMATION_SCHEMA.TABLES WHERE TABLE_SCHEMA = '数据库名称' ORDER BY TABLE_ROWS DESC;
```

  2. **DM 目的端信息**



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
|其他|/|


### 3.2 迁移评估

本例中，介绍使用 DEM 工具进行迁移评估工作，对源端数据库进行迁移评估，包括数据对象和 SQL，最终形成迁移评估报告。可通过迁移评估报告提前了解哪些数据对象或 sql 需要单独处理，方便后续迁移的顺利进行，详细步骤如下（ DTS 工具迁移评估详细步骤可参考[《从 Oracle 移植到 DM 》-DTS 迁移评估](https://eco.dameng.com/document/dm/zh-cn/start/oracle_dm#3.2.2%20DTS%20%E8%BF%81%E7%A7%BB%E8%AF%84%E4%BC%B0)）。

|**DEM 版本**|**V7.1.5**|
|------|------|
|DEM 安装|参考达梦企业管理器 DEM|


  1. 创建评估。登录 DEM 后，第 ① 步点击左侧数据迁移图标，第 ② 步点击上面的评估，第 ③ 步点击右侧 “+”号弹出“新建评估”卡片，按要求填写相关详细信息。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112624JVPN4E6YZ3NYL3OYAQ)

  2. 新建评估完成后选择评估方式为 “MySQL==>DM”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112559WXPW1TRL2PIVS144HP)

  3. 连接 MySQL 源端与 DM 目的端。在连接 MySQL 源端时点击数据库名右侧的刷新后可以下拉选择对应的 MySQL 库。勾选“指定目的库”可以指定目的端的 DM 库进行评估。使用指定的目的库需要用户提前安装好目的端的数据库。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112537VV93QJ2VTDNHUZXHQZ)

连接 DM 目的端数据库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112510BP4HL0FMWLDSS3LDI5)

  4. 勾选评估内容。勾选相应的评估内容后，下面将对这些内容进行迁移评估。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281124365KUBI46UUZTDJ8CS3J)

  5. 指定要评估的数据库模式，勾选 MySQL 中需要迁移的库进行评估。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281123570ZIQTZHDQR5ZTCOZSG)

  6. 选择 MySQL 库中具体需要评估的对象。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112330T9XDYXOUE7OFFSMBOQ)

  7. 确认评估任务后开始评估。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023112811221332N5H87E4KOXXC9LDP)

  8. 评估完成后可点击右下方的“查看报告”，查看详细的评估报告。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112150G6YVV7EKBVU7XMSD4P)

（1）通过表格和图形的方式生成总体的评估报告。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112121PV92I4768O8J3S3M2F)

（2）“对象评估详细”模块呈现了所有对象的详细兼容性的评估结果。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281120535NZEZLVS37LAOHXYZI)

（3）若存在不兼容的对象，可通过点击右侧”查看详情“按钮查看详细信息。例如：第 23 行的触发器是由于 MySQL 与 DM8 语法差异导致不兼容，经 DEM 工具自动转换后，转换后的 SQL 仍然语法不兼容。此时，可点击不兼容对象右侧的修改建议，可根据系统给出的建议进行相应语法修改。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112727809BU3AGRHBPPPWK7V)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112819YK3844YRSN2J2ECSEN)

（4）在“表信息统计详细”模块对表的详细信息以表格的形式进行了统计，包括：表类型统计、表行数统计、表大小统计、表字段统计、约束统计、约束分布、索引统计、索引分布。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128112909TMT6FZ30IDC4AH2UZ6)

通过迁移评估可以将 DM 数据库不兼容的部分先整理出来，使用 DM 语法进行手动修改，在正式迁移时不兼容的对象就不使用工具进行迁移，待其它对象迁移完成后再将修改好的对象导入到 DM 数据库中。

### 3.3 制定移植计划

根据待移植的 MySQL 系统信息分析的情况，制定迁移计划：先对整库进行一次性迁移，再对不兼容的对象进行补迁。

### 3.4 迁移准备

本文将介绍利用 DTS 工具进行通用情况下的数据移植工作，其他特殊配置可根据实际需求进行调整。

#### 3.4.1 源端 MySQL 准备

DTS 工具为静态数据迁移工具，在正式开始移植前需要停止所有对源端 MySQL 数据库的变更操作，避免数据变化或新数据的产生导致源端与目的端数据不一致。

#### 3.4.2 目的端达梦准备

  1. **数据库版本选择**



DM 数据库会定期进行产品更新迭代。在进行项目移植前，需要先确定使用的 DM 数据库版本：

（1） 建议使用当前最新版本的数据库，以保证更高的兼容性。

（2）版本优先选择安装完整版。

（3）版本与硬件环境一定要严格匹配，以减少干扰性的问题出现。

  2. **数据库架构选择**



达梦数据库为用户提供多样的数据库架构适配用户不同的业务需求，用户可以根据业务系统需求选择达梦合适的数据库架构进行部署。DM 数据库架构可参考：

  [达梦数据库管理系统 DM8](https://eco.dameng.com/info/products/dm8)

  [数据共享集群 DMDSC](https://eco.dameng.com/info/products/dmdsc)

  [数据守护集群 DMDataWatch](https://eco.dameng.com/info/products/dmdatawactch)

  [新一代分布式集群 DMDPC](https://eco.dameng.com/info/products/dmdpc)

详细安装部署步骤可参考：[达梦在线服务平台-运维指南-数据库规范化部署](https://eco.dameng.com/document/dm/zh-cn/ops/before-installation.html)相关内容，本例中选择目的端数据库架构为单机。

  3. **初始化参数设置**



在安装好达梦数据库后还需要初始实例用于对数据的管理，在初始实例时初始化参数尤为重要。

|**数据库参数**|**参数值**|
|-----|-----|
|DB_NAME（数据库名）|DAMENG（根据需求设置）|
|INSTANCE_NAME（实例名）|DMSERVER（根据需求设置）|
|PORT_NUM（端口）|5236（正式移植环境下，为保证数据库安全，不建议使用默认端口 5236）|
|管理员、审计员、安全员密码（安全版本特有）|建议首次初始化实例时立即修改密码|
|EXTENT_SIZE（簇大小）|16|
|PAGE_SIZE（页大小）|32|
|LOG_SIZE （日志大小）|2048M|
|CHARSET（字符集）|UTF-8（一般是 UTF8，根据实际要求设置）|
|CASE_SENSITIVE（大小写敏感）|不敏感（一般是不敏感，根据实际要求设置）|
|BLANK_PAD_MODE（尾部空格填充）|否|


其中页大小（page_size）、簇大小（extent_size）、大小写敏感（case_sensitive）、字符集（charset）、结尾空格填充（BLANK_PAD_MODE）一旦确定无法修改，需谨慎设置。

（1）CASE_SENSITIVE 大小写是否敏感设置。CASE_SENSITIVE=1 大小写敏感，包含 2 层意思：

  ① 表中数据：区分大小写。

  ② 对象名：对象名区分大小写。

> **注意**
>
> 通过管理工具建表时，创建对象时不对对象名加双引号，工具会自动将其转为大写。



MYSQL 建表默认的字符编码是 UTF8_GENERAL_CI，所以建议在 MYSQL 迁移到 DM 时，在达梦端设置成大小写不敏感 CASE_SENSITIVE=0。如果 MYSQL 系统中使用的字符编码是 UTF8_GENERAL_CS，那么建议达梦端设置成大小写敏感 CASE_SENSITIVE=1。MYSQL 设置大小写敏感的细粒度可到字段级别，达梦是实例级别的，一旦设置，后续不可修改，最终还需根据实际情况进行权衡后再设置。

（2） MYSQL 中字符集编码含义：

  ① UTF8_GENERA_CI：不区分大小写，CI 为 CASE INSENSITIVE 的缩写，即大小写不敏感。

  ② UTF8_GENERAL_CS：区分大小写，CS 为 CASE SENSITIVE 的缩写，即大小写敏感。

更多初始化参数的详细说明可参考达梦数据库安装目录下 doc 目录中的《 DM8_dminit 使用手册》或在数据库运行目录 bin 目录下执行以下命令查看部分初始化参数说明。

```
./dminit help
```

> **注意**
>
> 强烈建议用户在首次安装数据库初始化实例时，立即修改数据库系统用户的初始密码，并设置一定的密码强度，以保障数据安全性。



  5. **兼容性参数设置**



DM 数据库为了更好的兼容其它数据库提供了兼容性参数供用户选择需要兼容的数据库。

|**参数名**|**含义**|**建议值**|
|---|---|---|
|COMPATIBLE_MODE|是否兼容其他数据库模式。0：不兼容，1：兼容 SQL92 标准 2：兼容 ORACLE 3：兼容 MS SQL SERVER 4：兼容 MYSQL 5：兼容 DM6 6：兼容 TERADATA。|4（表示部分语法兼容 MYSQL），重启数据库生效。|
|ORDER_BY_NULLS_FLAG|控制排序时 NULL 值返回的位置，取值 0、 1、2。 0 表示 NULL 值始终在最前面返回； 1 表示 ASC 升序排序时 NULL 值在最后返回， DESC 降序排序时 NULL 值在最前面返回， 在参数等于 1 的情况下， NULL 值的返回与 ORACLE 保持一致； 2 表示 ASC 升序排序时 NULL 值在最前面返回， DESC 降序排序时 NULL 值在最后返回，在参数等于 2 的情况下， NULL 值的返回与 MYSQL 保持一致。|2（兼容 MYSQL）。|
|MY_STRICT_TABLES|是否开启 STRICT 模式（严格模式），仅在 COMPATIBLE_MODE=4 时有效。0：不开启，数据超长时自动截断；字符类型转换数值类型（包括 INT、SMALLINT、TINYINT、BIGINT、DEC、FLOAT、DOUBLE）失败时，转换为 0；1：开启，数据超长或计算错误时报错。|建议值：1。|


在进行 MySQL 迁移前我们需要将这些参数调整为合适的值使达梦数据库更好地兼容 MySQL，参数调整后需要重启 DM 数据库生效。

  5. **创建迁移用户和表空间**



从 MySQL 移植到 DM，要先创建好待使用的用户和这个用户的表空间，不要把数据迁移到系统默认的管理员 SYSDBA 用户下和 MAIN 表空间下。

MySQL 的体系架构是单实例多库，DM 数据库是单库单实例多模式的架构，MySQL 可能是一个 root 用户访问多个库，访问前切换一下当前库即可。从 MySQL 迁移到达梦的时候就需要针对 MySQL 中的每一个库在达梦里面创建一个用户和表空间来对应。例如 MySQL 中有一个库 dbtest，达梦里面先创建一个表空间 dbtest，然后创建一个用户 DBTEST，指定默认表空间为 dbtest。示例如下：

创建 DBTEST 表空间存储 MySQL 中 dbtest 库迁移过来的数据。

```
create tablespace "dbtest" datafile '/data/dmdata/DAMENG/DBTEST.DBF' size 2048 ;--创建表空间dbtest，数据文件为DBTEST.DBF。
```

创建 DBTEST 用户并授予权限，使用 dbtest 表空间。

```
create user "DBTEST" identified by "密码" --创建用户
default tablespace "dbtest"--指定用户DBTEST表空间为dbtest
default index tablespace "dbtest";--指定用户DBTEST索引表空间为dbtest
grant "PUBLIC","RESOURCE","SOI","SVI","VTI" to "DBTEST";--授予用户DBTEST常规权限
```

迁移 MySQL 中 dbtest 库的数据的时候，用 root 用户连接 MySQL，指定当前库为 dbtest；用 DBTEST 用户连接达梦，这样就把 MySQL dbtest 库中的数据迁移到了达梦 DBTEST 用户中。

在做 MySQL 迁移的时候要先分析本次迁移需要从源库中移植哪一个库或者哪几个库的数据，然后为每一个库分别在达梦中创建独立的表空间和用户。大多数情况下，需要迁移的数据所在的 MySQL 实例里面有多个库，并不是所有的库都需要迁移，所以在迁移准备阶段，需要明确要迁移哪些库。

#### 3.4.3 迁移工具准备

本文选择“ DM 数据迁移工具 V8 (Build 2023.04.17) ”作为本次迁移要使用到的迁移工具，不同版本界面显示上可能会有一些差异。该工具在安装数据库客户端时已安装完成可以直接使用，工具存放在 DM 数据库安装目录下 tool 文件夹中。

Linux 环境下进入 tool 目录中执行 ./dts 即可运行 DM DTS 工具，windows 环境下可直接双击启动。

### 3.5 迁移步骤

#### 3.5.1 创建迁移

  1. 打开 DMDTS 迁移工具点击左上方的 3 色小图标新建迁移工程。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023112814134220YTVCRESDXJQOSX96)

  2. 打开刚刚创建的工程，右键点击“迁移”，选择“新建迁移”，并自定义迁移名称。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204143154LEU76BOQG6J3SE7X3F)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312071720559G9R6IADNZHCYJL5KM)

  3. 新建迁移完成后点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128142005PDDN9R5DC47J2QXXD4)

  4. 在“其它数据库迁移到达梦”选项中选择迁移方式为 “MySQL ==> DM”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281421166VC2DOZVGEM8TGH9EJ)

#### 3.5.2 连接数据库

迁移方式选择完毕后开始连接数据库，首先连接源端 MySQL 数据库，再连接目的端 DM 数据库。

  1. **连接源端 MySQL 数据库。**



输入源端 MySQL 数据库相关登录信息，在“数据库名”选项中选择需要迁移的数据库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128142243VR7HMLXB8OSAC5Q28E)

在创建连接 MySQL 数据库时建议通过指定驱动的方式来连接数据库，避免因为驱动版本不适配等问题导致迁移失败。驱动可以在 MySQL 官网获取与 MySQL 迁移版本相对应的驱动。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204144920HP9BQHT7K3KR0HI89F)

  2. **连接目的端 DM 数据库。**



输入目的端 DM 数据库相关登录信息，选择与源端对应的迁移用户连接数据库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128142441J34KVXKN5KU1LLX6MD)

#### 3.5.3 配置迁移对象及策略

  1. 迁移对象方式及迁移策略中勾选“保持对象名大小写”。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128142529P6FI9PQWPRACTWTQ3Y)

当勾选了“使用默认数据类型映射关系”后在迁移时 DTS 会将源端 MySQL 数据库中相应的数据类型采用默认的映射关系映射到目的端 DM 数据库中。如果在这里勾选了“使用默认数据类型映射关系”，后面又自定义了数据类型映射关系，DTS 会优先选择使用自定义的数据映射关系。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281426005E2INV5NWBW28EX0PY)

在“迁移策略”中点击“查看数据类型映射关系”可以查看源端 MySQL 到目的端 DM 的数据类型映射关系，包括“源数据类型名”、“源精度”、“源标度”、“目的数据类型名”、“目的精度”等等。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128142628BGEZ0HIB0JHM0NL0B6)

  2. 勾选源端待迁移的数据库。



这里需要勾选源端待迁移的数据库，由于 MySQL 端没有模式所以这里模式显示空，并不影响迁移。在 MySQL 数据库连接阶段指定了连接的数据库，所以这里只显示了一条信息。

在指定模式阶段，用户可以通过“源模式”选择源端要迁移的库，通过“目的模式”来指定源端要迁移到 DM 的模式，通过是否勾选“创建模式”、“表”、“视图”、“存储过程/函数”、“触发器”来指定目的端 DM 是否要迁入源端 MySQL 中的这些对象。由于在 DM 数据库准备阶段已经提前将 DBTEST 模式创建好了，所以这里就不勾选“创建模式”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128142708TP2LU55KWS3JMY5AAX)

  3. 勾选源端数据库中需要迁移的对象。



这里可以看到源端待迁移库中所有的对象，用户可以自定义选择 MySQL 需要迁移的具体对象。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128142750UMGB3RZIN514LQDGV6)

> **注意**
>
> 在 SQL 评估阶段不兼容的对象不需要勾选，待其它对象迁移完成后，再手动修改和导入这些不兼容的对象。



用户可以通过点击右上方的“分析源对象”统计选中的源端待迁移对象。用户可以通过该功能对源端迁移对象进行统计分析，包括“源对象统计”、“源表统计”、“源表详细”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281428262N7U3QRI3X1HDSK8T2)

待迁移具体对象勾选完毕后可以通过点击转换进行自定义对象迁移策略。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281430241HAL4923WXGVRNFXW1)

  4. 自定义对象迁移策略。点击转换后可以设置表的映射关系，包括迁移策略和列映射选项。



（1）迁移策略
在迁移策略中可根据需要设置表及数据迁移的策略。在左侧选项中可以选择“表定义”、“主键”、“约束”、“索引”等的迁移策略；在右侧选项中可以配置与迁移数据相关的策略。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128143046PLAKD8X1XR6SAIFB3I)

部分选项说明：

① 压缩：指定迁移的目的表是否按照压缩方式存储。

② 强制聚集索引：即使源表的主键为非聚集主键，创建目的表时也会被转换为聚集主键。

③ 强制非聚集索引：即使源表的主键为聚集主键，创建目的表时也会被转换为非聚集主键。

④ 启用标志列插入：如果表上有标志列，则迁移数据时会强制向标志列插入值，以保证源和目的数据完全一致。

⑤ 显示行数：将在迁移任务过程中，显示数据的行数。

⑥ 拷贝记录：如果目的表已存在，直接拷贝记录，不需要创建表。

⑦ 删除后拷贝记录：迁移过程中先删除已存在的目的表，再重新创建新目的表。

⑧ 源一次读取行数：设置从数据源中读取数据时每次读取数据的行数，该参数决定内存中缓存结果集的大小，对于数据量很大的数据源，设置该参数，可以控制内存的使用。

⑨ 目的一次提交行数：设置向目的数据库中每次写入数据的行数。当数据量比较大时，减小该参数的值可以减少内存的使用。但会影响迁移的速度。

⑩ 缓存批数：设置缓存队列的长度。调整该参数可以调整迁移过程中内存的使用。

> **注意**
>
>
> 如果数据量较大，可以选择先迁移表结构定义相关内容，再迁移数据，最后迁移索引。大字段建议单独迁移，且迁移大字段时建议把一次读取和一次提交的值调小，一般在20或以下效率可能会更好，设置较大值时迁移效率较低。



（2）列映射选项

在列映射选项中可根据需求修改源端迁移到目的端表的列名、数据类型、精度、小数位数、默认值、是否可空、主键、自增列、起始值、增量信息等。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128143226GGKW3B2PN6VUTIFJVV)

完成映射关系的配置后，需要勾选“应用当前选择项到其他同类对象”，选择该选项后，将弹出对话框，选择其他同类对象，将此策略应用到相同对象上。如果不勾选“应用当前选择项到其他同类对象”，那么配置的迁移策略只会对当前选中的表生效。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281433047V56K7QV3LN49PH9B9)

#### 3.5.4 开始迁移

  1. 检查迁移任务，确认迁移对象是否正确。检查确认后点击“完成”即可开始迁移。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128143426OFF2TV7EVNNJ3Q74R8)

  2. 迁移完成后可以看到由于 MySQL 和 DM 数据库在某些存储过程和触发器的语法上使用不同需要用户在 DM 数据库中手动创建迁移失败的存储过程及触发器。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311281434582C0OBIWJCB9WEBACL7)

通过点击“查看详细信息”可以查看详细的迁移错误信息，便于定位问题。

#### 3.5.5 对象补迁

由于 MySQL 和 DM 数据库在某些语法使用上存在差异，导致某些对象可能会迁移失败，再加上在迁移评估阶段语法不兼容的对象，用户需要根据 DM 语法手动修改这些无法使用工具迁移的对象再导入到 DM 数据库中。

### 3.6 数据校验

通过 SQL 脚本分别统计源端 MySQL 和目的端 DM 的对象和数据量，通过对比判断是否迁移完成。脚本验证步骤如下：

  1. 统计用户下各类对象的数量，在源端和目的端通过对应的系统表进行查询记录对比是否一致。
  2. 统计用户下的表数量及对应的数据条目，在源端和目的端分别创建辅助表，使用脚本将源端和目的端的表的数量和表的数据量插入到辅助表中，通过查看辅助表内的数据进行比对，验证表的数量和数据量是否一致。



#### 3.6.1 统计 MySQL 端对象及数据

MySQL 的对象统计可参考 3.1.2 章节数据库调研-MySQL 源端信息-迁移对象统计。

#### 3.6.2 统计 DM 端对象及及数据

  1. 统计达梦数据库中相关用户的对象数。



```
SELECT OBJECT_TYPE,COUNT(*) FROM ALL_OBJECTS WHERE OWNER='USER_NAME' GROUP BY OBJECT_TYPE;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231204152446MJMRQK5DCOX3FF9EX7)

  2. 统计 MySQL 迁移过来的表的数据量并记录到辅助表。



```
CREATE TABLE DM_TABLES
(
TAB_OWNER VARCHAR(100),
TAB_NAME  VARCHAR(100),
TAB_COUNT INT
);

DECLARE BEGIN FOR REC IN
(SELECT OWNER,
OBJECT_NAME
FROM ALL_OBJECTS
WHERE OWNER='USER_NAME'
AND OBJECT_TYPE='TABLE'
)
LOOP
EXECUTE IMMEDIATE 'INSERT INTO DM_TABLES SELECT '''|| REC.OWNER ||''','''|| REC.OBJECT_NAME ||''',COUNT(*) FROM '|| REC.OWNER || '.' || REC.OBJECT_NAME;
END LOOP;
END;
```

#### 3.6.3 对象及数据量对比

  1. **对象对比**



通过比较在 MySQL 中和在 DM 中统计的对象数量及对象名来检查是否完成所有的对象迁移，将不对应或者缺少的对象重新迁移。

MySQL 端前期调研统计出的对象如下：

|**调研项**|**说明**|
|---|---|
|库名|dbtest|
|表数目|14|
|视图数目|3|
|存储过程|4|
|函数|1|
|触发器|1|


目的端 DM 迁移后的对象统计如下：

|**调研项**|**说明**|
|---|---|
|库名|dbtest|
|表数目|14|
|视图数目|3|
|存储过程|4|
|函数|1|
|触发器|1|


通过对比 MySQL 端与 DM 端的统计结果，确定两端对象数量一致。

  2. **数据量对比**



通过以下 SQL 命令可以比对表数据量，找出数据量不相等的表重新迁移数据，结果集为空表示源端和目的端数据量一致。其中 MYSQL_TABLES 为 MySQL 迁移前统计的记录所有表数据量的辅助表，DM_TABLES 为 DM 数据库中记录表数据量的辅助表。

```
SELECT A.TAB_OWNER,
       A.TAB_NAME,
       A.TAB_COUNT-B.TAB_COUNT
  FROM MYSQL_TABLES A,
       DM_TABLES B
 WHERE A.TAB_OWNER=B.TAB_OWNER
   AND A.TAB_NAME=B.TAB_NAME 
   AND A.TAB_COUNT-B.TAB_COUNT<>0;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206141109CS0O9L1923P861GJRP)

这里的 MYSQL_TABLES 表出现数据量不一致是因为 MYSQL 中没有统计辅助表 MYSQL_TABLES 的数据量，而 DM 中统计了 MYSQL 辅助表 MYSQL_TABLES 的数据量，所以迁移后 DM 端与 MySQL 端数据量一致。

### 3.7 统计信息与备份

#### 3.7.1 更新统计信息

数据核对完成无问题后，应进行一次全库的统计信息更新工作。统计信息更新脚本示例如下：

  1. 按模式更新统计信息：



```
DBMS_STATS.GATHER_SCHEMA_STATS(
'DBTEST', --DBTEST 为模式名，需要根据实际情况修改为自己的模式名。
100,
FALSE,
'FOR ALL COLUMNS SIZE AUTO');
```

如果数据量较大，该过程执行较慢，需要等待一段时间。

  2. 按照表进行统计信息的收集：



```
DBMS_STATS.GATHER_TABLE_STATS(
'username',--用户名
'table_name',--表名
null,100,TRUE,'FOR ALL COLUMNS SIZE AUTO');
```

更新统计信息的目的在于大批量迁移数据后数据库系统未对用户数据进行全面分析存在错误的统计信息，可能会导致数据库优化器根据错误的统计信息得到错误的查询计划，严重影响查询性能。

更多更新[统计信息](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)方式可参考：[统计信息](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)。

#### 3.7.2 备份

在对数据更新完统计信息后，在数据量不大，磁盘空间足够的情况下应进行一次数据备份工作，避免在数据验证过程中对数据产生修改需要重新迁移。数据备份有三种方式：

  1. 正常停止数据库后，拷贝备份到实例目录或保存数据文件的其他目录；
  2. 开启归档日志后，进行物理备份；
  3. 逻辑备份，使用 dexp 工具进行逻辑导出。



此外，通常生产系统都需要制定定时备份任务，备份时间点建议避开业务高峰期，可根据实际需要配置备份作业任务。

### 3.8 应用迁移

一般情况下，MySQL 数据库迁移完成后，需要更换应用连接 MySQL 数据源到达梦数据库。为了验证系统移植的完整性，还需要进行应用的相关功能和性能测试，并改造应用到连接达梦数据库一个最佳状态。

#### 3.8.1 语言、接口、框架

应用在适配时，可以前往[达梦在线服务平台-应用开发指南](https://eco.dameng.com/document/dm/zh-cn/app-dev/)模块参考相关语言的适配指南。

## 四、性能优化

数据库和应用系统移植完毕后开启 sql 日志，对系统进行全面测试，排除移植过程中错误的地方，对慢的 sql 语句进行优化。可以通过对 SQL 日志记录的慢 SQL 进行优化提升 SQL 执行效率。在开启 SQL 日志时可参考如下两个 SQL 日志参数的配置，通过在 sqllog.ini 中设置 SQL 过滤规则来记录需要优化的 SQL。

```
--设置SQL过滤规则
SQL_TRACE_MASK=2:3:22:23:25:28---指定 SQL 日志中需要被记录的语句类型，详细说明可参考达梦数据库安装目录下doc目录中《DM8系统管理员手册》。
MIN_EXEC_TIME=500 --记录执行时间大于500毫秒的SQL，用户需根据实际情况设置。
```

SQL 日志开启方法可参考[达梦在线服务平台-运维指南-单机安装部署-配置 sql 日志](https://eco.dameng.com/document/dm/zh-cn/ops/installation-install.html#2.3%20%E9%85%8D%E7%BD%AE%20SQL%20%E6%97%A5%E5%BF%97)。

在功能测试和性能测试的时候可以开启 SQL 日志，然后通过日志分析工具从执行时间和执行次数两个维度对 SQL 日志进行分析，生产分析结果，然后根据分析结果对系统性能进行优化。使用日志分析工具时需采用 32k 页大小的 DM 作为分析库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128150830L5LQKIL2O1DJUKSEBN)

更多性能优化可参考[达梦在线服务平台-运维指南-性能诊断与优化](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html)。

## 五、常见问题

### 5.1 PERIOD_DIFF 函数改写

PERIOD_DIFF 在 mysql 中的执行效果：

```
mysql> sELECT PERIOD_DIFF(202101,202001);
+----------------------------+
| PERIOD_DIFF(202101,202001) |
+----------------------------+
|                         12 |
+----------------------------+
1 row in set (0.00 sec)
```

PERIOD_DIFF 改写到达梦：

方式一：

```
SQL> SELECT months_between(to_date(202101,'yyyymm'),to_date(202001,'yyyymm'));
LINEID MONTHS_BETWEEN(TO_DATE(202101,'yyyymm'),TO_DATE(202001,'yyyymm'))

---

1          12
used time: 0.220(ms). Execute id is 704.
```

方式二：

```
SQL> SELECT DATEDIFF(month,to_date(202101,'yyyymm'),to_date(202001,'yyyymm'));
LINEID DATEDIFF(MONTH,TO_DATE(202101,'yyyymm'),TO_DATE(202001,'yyyymm'))

---

1          -12
used time: 0.197(ms). Execute id is 705.
SQL> SELECT DATEDIFF(month,to_date(202001,'yyyymm'),to_date(202101,'yyyymm'));
LINEID DATEDIFF(MONTH,TO_DATE(202001,'yyyymm'),TO_DATE(202101,'yyyymm'))

---

1          12
used time: 0.732(ms). Execute id is 706.
-- 达梦需要转换为时间类型来进行运算
```

### 5.2 yearweek 改写

yearweek 在 mysql 中的执行效果：

```
mysql>
mysql> select YEARWEEK('2019-07-11',1);
+--------------------------+
| YEARWEEK('2019-07-11',1) |
+--------------------------+
|                   201928 |
+--------------------------+
1 row in set (0.08 sec)

mysql>
```

yearweek 改写到达梦：

```
SQL> select year('2019-07-11')||WEEK('2019-07-11',1);

LINEID     YEAR('2019-07-11')||WEEK('2019-07-11',1)

---

1          201928
```

### 5.3  mysql 累加写法改写

例如：在 mysql 中累加写法如下：

```
SELECT id , name , age ,
(@age:=@age + age ) as  age_sum
FROM user , ( SELECT @age := 0  ) s
ORDER BY id
```

dm 数据库中通过分析函数实现改写。

```
SELECT id , name , age ,
sum(age) OVER (ORDER BY id asc) AS ageRank
FROM user;
```

## 六、更多帮助

更多 MySQL 到 DM 迁移常见问题可参考[达梦在线服务平台-常见问题-从 MySQL 迁移到 DM](https://eco.dameng.com/document/dm/zh-cn/faq/faq-mysql-dm8-migrate.html)。

更多 DTS 工具使用详情可参考 DTS 工具“帮助主题”，具体位置见下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206113201FH9UKK98WMLA7YKJSK)

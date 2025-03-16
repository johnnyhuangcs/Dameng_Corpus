

# 从 Oracle 移植到 DM

## 一、概述

随着国家对自主可控的日益重视，目前在各个行业和区域中面临越来越多的国产化，采用有自主知识产权的国产数据库将成为主流。DM 数据库以其丰富的产品架构、优异的性能表现、绝对的安全保证适应各种应用场景需求得到了众多客户的信任。DM 数据库针对 ORACLE 数据库可以做到完全替代，本文主要介绍 ORACLE 移植到达梦数据库的具体步骤及移植过程中的注意点和常见适配问题，供用户参考。

## 二、迁移流程

### 2.1 迁移流程图

![企业微信截图_17019399363117.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207170608IN3PJHXRQ36H9LV0XC)

### 2.2 流程介绍

#### 2.2.1 需求确认

移植会涉及诸多场景，如容灾备份、应用改造/替代、数据库版本升级/回退、数据库替代、业务分流等，不同的场景在数据流向、停机窗口、同步需求、数据处理等方面会有不同的需求，需要针对性地选择迁移工具和方案。

#### 2.2.2 数据库调研

考虑迁移或同步工具版本、驱动版本、基础环境、操作方式、对象个数、对象大小、数据量等均会影响迁移工作的开展，需要对源端和目的端数据库及服务器、业务系统进行调研，确保在满足相关需求的前提下稳定完成迁移。

确认迁移需求后，源端数据库需提前调研如下信息：

  1. 环境信息。提前了解操作系统层面，对工具能否使用可视化界面，或者端口号开放情况，可以方便在后期部署安装过程中，及时避开处理问题时的一些干扰项。主要包括对服务器、内存、CPU、网络、端口、安全策略、是否具备可视化界面等信息的调研。
  2. 业务系统信息。提前了解应用系统层面信息，结合应用系统特性，为后面制定迁移策略、迁移时间评估等提供参考。主要包括对业务类型、业务运行时段、停机窗口、数据量、数据增量、并发访问量等信息的调研。
  3. 数据库信息。提前了解迁移数据量、字符编码、归档保留、数据库对象、表空间等信息，为后续迁移做好规划和相关准备工作。



#### 2.2.3 迁移评估

达梦数据提供了三种工具进行迁移前源端数据库兼容性评估：

[数据迁移工具 DTS](https://eco.dameng.com/info/products/dts)：提供了异构数据源之间的评估，迁移和对比功能。DM 数据迁移工具采用向导方式引导用户通过简单的图形化进行兼容性评估操作。

[达梦企业管理器 DEM](https://eco.dameng.com/info/products/dem)：支持对 ORACLE、MySQL、SQL Server 等主流数据库迁移到达梦数据库进行在线采集评估和自动转化，并提供兼容报告。

[SQLark 百灵连接](https://www.sqlark.com/?s=eco-com-doc)：支持对 ORACLE、MySQL 等主流数据库迁移到达梦数据库进行在线采集评估和自动转化，一键生成源数据库画像，获取源库对象、不兼容对象、大表、大字段表等迁移重难点情况，评估本次迁移需要投入的工作量。SQLark 会根据源库画像，生成合理的迁移策略，为开发者后续进行的自动/手动迁移提供迁移方案参考。

除此之外，需要人工对大表、大字段表这些是否单独迁移进行评估及如何配置大表、大字段表的迁移策略。

#### 2.2.4 移植工具选择

达梦公司提供了四种移植工具：数据迁移工具 DTS、SQLark 百灵连接、数据复制软件 DMDRS 和数据集成软件 DMDIS，以满足不同移植场景的使用需求。

  1. **数据迁移工具 DTS**



DTS 是一款免费的数据迁移工具，在数据库安装时自带有图形化的版本，同时也可以部署 DEM (达梦企业管理器)，以 WEB 客户端方式提供。此工具主要适用于静态数据迁移场景。

  * **功能简介 DM**



DTS 基于成熟的关系数据模型和标准接口，跨越多种主流大型数据库，能以极少的系统开销实现数据迁移工作。DM DTS 的技术原理图如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128100946E9ZQF24MCHJPS2QTKP)

DM 数据迁移工具提供了主流大型数据库迁移到 DM、DM 迁移到主流大型数据库、DM 到 DM、文件迁移到 DM 以及 DM 迁移到文件的功能，DM 数据迁移工具采用向导方式引导用户通过简单的步骤完成需要的操作，数据迁移粒度灵活，DTS 客户端部署简单，对移植环境要求较低。

  * **产品特性**



（1）支持视图、存储过程/函数、包、类、同义词、触发器等对象迁移。

（2）支持数据类型的自动映射，编码转换。

（3）支持根据条件自定义迁移部分数据。

（4）向导式迁移步骤，上手简单。

（5）支持 web 端操作、监控。

（7）支持迁移评估。

  * **典型场景**



（1）支持全量静态数据迁移，无法实现数据增量迁移方式；

（2）为保障迁移全量数据的一致性，需要充足的业务系统停机窗口；

（3）迁移过程中，源端数据库不能有数据变更以及对象变更。

  2. **SQLark 百灵连接**



  * **功能简介**



[SQLark 百灵连接](https://www.sqlark.com/?s=eco-com-doc)是一款面向信创应用开发者的数据库开发和管理工具，其数据迁移功能，专注于提供全流程的异构数据库迁移服务 ，通过迁移评估和数据迁移两个环节和自动化语法解析，提前识别可能存在的改造工作，生成最佳迁移策略，一键迁移到目标数据库，最大化降低用户的数据库迁移成本。

![](https://wdcdn.qpic.cn/MTY4ODg1Njk4MDQyNTU0NQ_870531_VDXxDyWUKa5SuXNX_1735540580?w=800&h=476&type=image/png)

  * **产品特性**



（1）迁移评估：通过分析源库对象、兼容性、大表大字段表等迁移重难点情况，生成源库画像和迁移策略，评估本次迁移需要投入的工作量。

（2）迁移实施：基于迁移策略对数据库对象和表数据开展自动化迁移和语法转换，为迁移异常提供错误分析和修改建议，以任务管理的方式保障迁移工作完成。

（3）迁移校验：通过比对源库和目标库的对象数量、表数据量是否一致，检验迁移工作是否正确、完整。

（4）支持分布式迁移：可多节点部署，线性提升迁移效率；迁移期间节点可随时扩展、缩减，具备高可用性。

  * **典型场景**



Oracle/MySQL 全量迁移至达梦数据库。

  3. **数据复制软件 DMDRS**



  * **功能简介**



DMDRS 是支持异构环境的高性能、高可靠和高可扩展的数据库实时同步系统。通过秒级数据实时同步有效避免传统备份系统导致的无法完全满足企业对于信息系统不中断服务的问题；通过变化日志捕捉可以有效降低传统 ETL 工具因创建触发器、影子表等对业务系统带来的性能影响；通过实时的数据同步，构建“双活”的主备系统，解决传统基于数据库自身的主备系统中备机只读而无法对外提供写服务的问题。该产品可广泛应用于应急系统、容灾备份、负载均衡、数据移植、联机维护、订阅分发和多业务中心等业务领域。

DMDRS 的基础实现原理如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231128103856969CC2J1X0K82K564N)

  * **产品特性**



（1）部署形态支持单链路、文件转换、跨网闸、文件摆渡、主备、一对多同步、多对一同步、双向同步、级联同步、环状同步等。

（2）支持数据清洗转换 CVT 功能，比如：列名转换、字段映射、表合并、表拆分等。

（3）支持 web 端操作、监控。

（4）支持 DMDRS 主备功能。

（5）支持目标端为 kafka、redis、hdfs（ Hadoop 分布式文件系统）等。

  * **典型场景**



DMDRS 移植场景下，源端数据库需要开启归档和逻辑附加日志。

（1）主要场景是停机窗口较短的业务系统，使用增量同步数据的方式缩短停机窗口，适用于平滑迁移或升级。

（2）支持数据全量和增量实时同步，非介入式捕获增量对源端数据库几乎无影响。

（3）可实现数据级灾备、业务分流，支持远距离异地容灾。

  4. **数据集成软件 DMDIS**



  * **功能简介**



DMDIS 实现了对数据抽取、传输、整合、以及装载的一站式支持，是构建数据中心、数据仓库、数据交换和数据同步等数据集成类应用的理想平台。DMDIS 体系结构如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206161824BSMXY1A5IX18EGY81S)

  * **产品特性**



（1）支持 TXT、CSV、Excel、XML 文件、消息服务器、WebService、Restful。

（2）支持全量抽取、增量抽取，双向同步、文件同步、批量文件读取。

（3）支持对于简繁体、汉字拼音、乱码处理、字符集转换、中文数字的中国特有的问题提供内置的转换规则进行处理。

（4）支持中英文日期时间格式自动转换。

（5）支持 web 端操作、监控。

（6）支持二次开发接口，比如数据源的抽取装载接口、数据转换接口、元数据存取接口等。

  * **典型场景**



（1）周期性地从数据源中抽取变化数据加载到目的数据库中。

（2）数据种类丰富，来源多样且数据质量参差不齐，需要数据清洗。

（3）数据持续集成，不同类型数据集成周期可能不同，需要功能强大的作业调度与监控功能。

（4）跨部门、跨地域的数据共享和业务协同，不同系统间需要同步共享的数据，而系统中的数据可能是异构的，结构和类型都不相同，无法使用常规手段。

#### 2.2.5 制定移植计划

根据需求分析和数据库调研，结合每个应用系统的具体要求，选择合适的迁移工具，基于数据迁移的基本原则和迁移工具评估结果，制定合理的移植计划避免任何可能遗漏的步骤，保障迁移工作的稳定实施。

#### 2.2.6 移植实施

对于异构数据库移植到达梦，在正式迁移前，需要根据源端数据库的相关调研信息，对目的库的实例、表空间、用户等进行配置，以提高数据库间的兼容性，保障后续移植工作稳定进行。

同时，达梦数据的迁移工具均具有自动转换功能。大多数情况下，可通过相关迁移工具进行对象和数据移植，但由于异构数据库间语法并非 100% 兼容，少量数据则需要进行手动移植。

#### 2.2.7 移植结果校验

在进行正式环境的数据移植时，每一条数据都是真实的，有效的且完整的，在迁移完成后，必须要源端数据库的所有对象都准确无误的迁移到目的端，一旦出现缺少对象、缺少数据和数据内容不正确的情况，可能会导致历史记录缺少，甚至某些功能模块失效等严重后果。因此在迁移完成后，需确认是否出现迁移后的数据量、数据内容和对象个数不一致的情况，如果不一致应进行对应的维护。

#### 2.2.8 移植后收尾工作

移植后的收尾工作包括：索引补录、更新统计信息、备份、整理对象脚本等内容，保障移植工作的完整性。

#### 2.2.9 应用移植与优化

一般情况下，源端数据库迁移完成后，直接更换应用连接源端数据源到达梦数据库，应用代码适配不用修改。为了验证系统移植的完整性，还需要进行应用的相关功能和性能测试，并改造应用到连接达梦数据库一个最佳状态。

此外，在对应用系统进行全面测试后，排除移植过程中错误的地方，还应对数据库中的慢 SQL 进行优化，保证移植后的系统高效运行。

## 三、移植过程

### 3.1 需求确认及调研

#### 3.1.1 需求确认

本例构建了一套 Oracle 11g 的单机示例库，并介绍利用 DTS 工具从 Oracle 11g 移植 OT 用户下的所有对象到 DM8 数据库的详细步骤，以供参考。

#### 3.1.2 数据库调研

考虑迁移或同步工具版本、驱动版本、基础环境、操作方式、对象个数、对象大小、数据量等均会影响迁移方案的制定、迁移工作的开展，需要对源端和目的端数据库及服务器、业务系统进行调研，确保在满足相关需求的前提下稳定完成迁移。

  1. **oracle ** **源**** 端信息**



|**调研项**|**调研结果**|
|----|----|
|数据库后台操作系统|Red Hat Linux|
|数据库架构|单机|
|数据库版本|Oracle 11g|
|待迁移数据库名|oracletest1|
|带迁移的模式名|OT|
|IP/端口信息|192.168.91.130/1521|
|用户名/密码|xxxxx|
|字符集编码|简体中文 GBK|
|需要移植的对象|表（数据量）、物化视图、触发器、存储过程、函数|


部分调研项查询方法如下：

```
--迁移对象统计
select a.username  用户,
        (select count(1) from dba_tables b where b.owner = a.username) 表数量,
        ( SELECT COUNT(1) FROM DBA_INDEXES I WHERE UNIQUENESS = 'UNIQUE' AND OWNER =A.USERNAME OR INDEX_NAME NOT LIKE 'SYS_%' AND OWNER =A.USERNAME) "索引数量",
        (select count(distinct c.table_name)
           from dba_tab_partitions c
          where c.table_owner = a.username) 分区表数量,
        (select count(1)
           from dba_tab_cols d
          where d.OWNER = a.username
            and d.DATA_TYPE like '%LOB%') 包含lob表数量,
        (select sum(e.bytes) / 1024 / 1024 / 1024
           from dba_extents e
          where exists (select 1
                   from dba_lobs f
                  where f.owner = a.username
                    and f.segment_name = e.segment_name)) lob占用空间,
        (select count(1) from dba_views g where g.OWNER = a.username) 视图数量,
        (select count(1) from dba_triggers h where h.owner = a.username) 触发器数量,
        (select count(DISTINCT I.NAME)
           from DBA_SOURCE I
          WHERE I.OWNER = A.username
            AND I.TYPE = 'FUNCTION') 函数数量,
        (select COUNT(1)
           FROM DBA_SEQUENCES j
          WHERE j.sequence_owner = A.username) 序列数量,
        (select count(1) from dba_synonyms where owner= A.username) 同义词,
        (select COUNT(1) FROM DBA_MVIEWS K WHERE K.owner = A.username) 物化视图数量,
        (select count(DISTINCT l.NAME)
           from DBA_SOURCE L
          WHERE L.OWNER = A.username
            AND L.TYPE = 'PROCEDURE') 存储过程数量,
        (select COUNT(1) FROM DBA_DB_LINKS M WHERE M.owner = A.username) dblink数量,
        (select max(n.DATA_LENGTH)
           from dba_tab_cols n
          where n.OWNER = a.username) 最大单字段宽度,
        (select SUM(O.DATA_LENGTH)
           from dba_tab_cols o
          where o.OWNER = a.username
            and o.DATA_TYPE not like '%LOB%') 最大行宽度
   from dba_users a where username in ('OT');

--数据量
select distinct segment_type,sum(BYTES) / 1024 / 1024 / 1024 , COUNT(*) FROM DBA_SEGMENTS where owner = '用户' group by segment_type order by 2 desc;


```

通过以上 SQL 统计出如下对象信息统计表：

|**类型**|**说明**|
|--|--|
|用户|OT|
|表数量|13|
|索引数量|12|
|分区表数量|0|
|包含 LOB 表数量|0|
|LOB 占用空间|0|
|视图数量|0|
|触发器数量|9|
|函数数量|0|
|序列数量|9|
|同义词|0|
|物化视图数量|1|
|存储过程数量|0|
|DBLINK 数量|0|
|最大单字段宽度|2000|
|最大行宽度|7359|
|数据量|2981 条|


其它 Oracle 端信息统计可参考如下 SQL：

```
--字符集编码
SELECT value FROM NLS_Database_Parameters WHERE PARAMETER='NLS_NCHAR_CHARACTERSET';
select userenv('language') from dual;

--是否以字节为单位、大小写敏感
Show parameter NLS_LENGTH_SEMANTICS;

--归档保留策略
select * from v$archived_log;

--定时作业
select * from user_jobs;

--权限查询语句
--查看用户系统权限
select PRIVILEGE from dba_sys_privs WHERE GRANTEE='用户名';

--查看用户对象权限
select * from dba_tab_privs WHERE GRANTEE='用户名'; 

--查看用户角色权限
select GRANTED_ROLE from dba_role_privs where grantee='用户名';

--表空间相关信息查询语句
--查询每个表空间的数据文件路径、实际使用大小，上限等及所属用户
select ee.username,dd.* from (select aa.*, bb.file_name, cc.NEXT_EXTENT
  from (select tbs_used_info.tablespace_name,
               tbs_used_info.alloc_mb,
               tbs_used_info.used_mb,
               tbs_used_info.max_mb,
               tbs_used_info.free_of_max_mb,
               tbs_used_info.used_of_max || '%' used_of_max_pct
          from (select a.tablespace_name,
                       round(a.bytes_alloc / 1024 / 1024) alloc_mb,
                       round((a.bytes_alloc - nvl(b.bytes_free, 0)) / 1024 / 1024) used_mb,
                       round((a.bytes_alloc - nvl(b.bytes_free, 0)) * 100 /
                             a.maxbytes) used_of_max,
                       round((a.maxbytes - a.bytes_alloc +
                             nvl(b.bytes_free, 0)) / 1048576) free_of_max_mb,
                       round(a.maxbytes / 1048576) max_mb
                  from (select f.tablespace_name,
                               sum(f.bytes) bytes_alloc,
                               sum(decode(f.autoextensible,
                                          'YES',
                                          f.maxbytes,
                                          'NO',
                                          f.bytes)) maxbytes
                          from dba_data_files f
                         group by tablespace_name) a,
                       (select f.tablespace_name, sum(f.bytes) bytes_free
                          from dba_free_space f
                         group by tablespace_name) b
                 where a.tablespace_name = b.tablespace_name(+)) tbs_used_info
         order by tbs_used_info.used_of_max desc) aa,
       dba_data_files bb,
       dba_tablespaces cc
 where aa.tablespace_name = bb.tablespace_name
   and aa.tablespace_name = cc.tablespace_name) dd
   right join dba_users ee on dd.tablespace_name=ee.default_tablespace
   where ee.username in ('OT');


--查询每个用户对象所占的表空间大小
select *
  from (select owner, tablespace_name, sum(b) GB
          from (select owner, 
                       t.segment_name,t.partition_name,round(bytes / 1024 / 1024/1024 , 2) b,tablespace_name
                  from dba_segments t)
         where owner in ('OT')
         group by owner,tablespace_name);
```

  3. **DM ** **目的端**** 信息**



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
|是否安装 ODBC|odbcinst -j|


### 3.2 迁移评估

达梦提供两种方式进行迁移评估，以下将详细介绍。

#### 3.2.1 DEM 迁移评估

利用 DEM 的迁移评估工具，对源端数据库进行迁移评估，包括数据对象和 SQL ，最终形成迁移评估报告。可通过迁移评估报告提前了解哪些数据对象或 sql 需要单独处理，方便后续迁移的顺利进行。

下图为 DEM 界面，选择迁移评估模块。

  1. 右键新建迁移评估工程，输入迁移评估名称。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206162152U02Z5MBN12TZII47Q7)

  2. 选择迁移评估方式，这里选择 Oracle ==> DM 。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206162223KS8RFB9REK5EYDVZOO)

  3. 输入 Oracle 数据库连接信息，点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206154212DATZO30JKI8H3DZJ3M)

  4. 选择评估对象模式以及数据对象类型，点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206162248EMNUZEWMBKWOPPPUQI)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206162716GD98BNFGUSZZRNOJPB)

  5. 选择评估的具体数据对象，点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206155505C3TNF8CE853SU6QG45)

  6. 迁移评估对象详情总览，点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206162846PX3EU7ITR0QTSFU1L5)

  7. 迁移评估开始。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206155541F1ULZJRO5Y6SWJJMI8)

  8. 等待迁移评估结束，查看迁移报告。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061634072QK3LUB1TBKMFQV7HQ)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206155639UFR4P84LL4V0GL1UA4)

  9. 对于不兼容情况，可以点击“不兼容”，查看详情。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206163731K4C7292HALIXFURK3S)

比如点击不兼容，点击详情我们可以了解到具体不兼容原因，并进行修改，补充到“转换后 SQL”模块中。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206164114OAAH2C5ETIBPWIV9OB)

迁移评估完成后点击“导出”，方便后续查看。

#### 3.2.2 DTS 迁移评估

使用 DTS 迁移工具进行迁移评估，详细步骤如下：

  1. 下图为 DTS 界面，选择评估模块。右键新建评估。



![企业微信截图_1701941579961.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207173620DPVPIVHQ37XJ52HUBU)

  2. 连接到源端数据库后，可以选择不同的评估项目，点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206164744Z5I4FI7EO8D7EJOIKJ)

  3. 选择需要评估的模式，点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129173149RH8I2XZFUNICT5CNUR)

  4. 选择评估的具体数据对象，点击下一步。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129191506V20TFFUWF8RPL5U1XJ)

  5. 迁移评估对象详情总览，点击完成。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311291915262IUBDO3ZQ2JYMO6U1Q)

  6. 等待评估结束，查看评估结果。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129191543OMHGNE7HOOA9L5OIRW)

  7. 点击【查看评估报告】，可以查看评估概述、数据库信息、对象兼容详细、SQL 兼容详情，并且根据需要选择对应的导出报告类型。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206164956U55CCYMZX8QFCUF2YB)

对于不兼容情况，可以点击“不兼容”，查看详情。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061656272WPTJ7J6KRITAMB05L)

比如点击不兼容 sql，点击详情我们可以了解到具体不兼容原因，并进行修改。

### 3.3 制定移植计划

根据待移植的 Oracle 系统信息分析的情况，制定迁移计划：先对整库进行一次性迁移，再对迁移失败的或不兼容的对象进行手动迁移。

### 3.4 数据库迁移

#### 3.4.1 迁移准备

  1. **Oracle 源端数据库准备**



DTS 工具为静态迁移工具，在正式开始移植前需要停止所有对源端数据库的操作，避免数据变化或新数据的产生导致源端与目的端数据不一致。

  2. **目的端达梦数据库准备**



  * **数据库版本选择**



DM 数据库会定期进行产品更新迭代。在进行项目移植前，需要先确定使用的 DM 数据库版本：

  1. 建议使用当前最新版本的数据库，以保证功能全面和兼容性更强。
  2. 版本优先选择完整版本。
  3. 版本与硬件环境一定要严格匹配，以减少干扰性的问题出现。



  * **数据库架构选择**



达梦数据库为用户提供多样的数据库架构适配用户不同的业务需求，用户可以根据业务系统需求选择达梦合适的数据库架构进行部署。DM 数据库架构可参考：

[达梦数据库管理系统 DM8](https://eco.dameng.com/info/products/dm8)

[数据共享集群 DMDSC](https://eco.dameng.com/info/products/dmdsc)

[数据守护集群 DMDataWatch](https://eco.dameng.com/info/products/dmdatawactch)

[新一代分布式集群 DMDPC](https://eco.dameng.com/info/products/dmdpc)

详细安装部署步骤可参考：[达梦在线服务平台-运维指南-数据库规范化部署](https://eco.dameng.com/document/dm/zh-cn/ops/before-installation.html)相关内容，本例中选择目的端数据库架构为单机。

  * **初始化参数设置**



源端为 Oracle 的情况下，对于目的端是达梦数据库的初始化参数，需要做好规划，初始化参数建议配置如下：

|**数据库参数**|**参数值**|
|-----|-----|
|DB_NAME（数据库名）|DAMENG（根据需求设置）|
|INSTANCE_NAME（实例名）|DMSERVER（根据需求设置）|
|PORT_NUM（端口）|5236（正式移植环境下，为保证数据库安全，不建议使用默认端口 5236）|
|管理员、审计员、安全员密码（安全版本特有）|建议首次初始化实例时立即修改密码|
|EXTENT_SIZE（簇大小）|32|
|PAGE_SIZE（页大小）|32|
|LOG_SIZE （日志大小）|2048M|
|CHARSET（字符集）|GB18030（根据实际要求设置）|
|CASE_SENSITIVE（大小写敏感）|敏感（根据实际要求设置）|
|BLANK_PAD_MODE（尾部空格填充）|否|


部分参数说明：

（1）EXTENT_SIZE：簇大小默认为 16 页，建议设置成 32 页。

（2）CASE_SENSITIVE：默认是大小写敏感，源端为 Oracle 情况下，建议保持默认大小写敏感即可。

（3）BLANK_PAD_MODE：空格填充参数，是否要兼容 Oracle 进行设置，即在 BLANK_PAD_MODE = 0 的情况下，’A’ 和 ’A ’ 被认为是相同的值，参数为 1 的情况下，认为是两个不同的值，根据现场具体应用的需求进行设置。此为初始化参数，只能在初始化时候指定，后续不可以修改，需要提前做好评估，但是源端为 Oracle 数据库的时候，建议设置为 1。

（4）CHARSET：字符集编码，可选 GB18030、UTF-8，默认为 GB18030，如果只存储中文和字母数字，使用 GB18030 更节省空间。

（5）PAGE_SIZE：页大小默认为 8K，建议设置成 32K，一条记录的长度，受到页大小的限制，不可以超过页大小的一半，所以建议一开始规划页大小为 32K。

  * **兼容性参数设置**



从 Oracle 移植到 DM，需额外注意以下参数。

|**参数名**|**含义**|**建议值**|
|---|---|---|
|COMPATIBLE_MODE|是否兼容其他数据库模式。0：不兼容，1：兼容 SQL92 标准 2：兼容 ORACLE 3：兼容 MS SQL SERVER 4：兼容 MYSQL 5：兼容 DM6 6：兼容 TERADATA。|推荐值：2，重启数据库生效。|


COMPATIBLE_MODE：兼容参数，置为 2 为兼容 Oracle 参数，在 dm.ini 中可以进行配置，如果默认 0 的情况下，达梦视 null 不等同于空字符串，用户可能会插入空串，会导致 is null 查不出全部数据，所以视情况而定是否需要修改。

更多初始化参数的详细说明可参考达梦数据库安装目录下 doc 目录中的《 DM8_dminit 使用手册》或在数据库运行目录 bin 目录下执行以下命令查看部分初始化参数说明。

```
./dminit help
```

  * **表空间规划**



目的端的表空间规划，参照源库表空间使用情况即可，这里前期调研的源端数据库 oracle 信息可直接引用。另需注意，如源库存储业务数据并不存在独立表空间，目的端则需要创建单独的业务表空间（包括数据表空间以及索引表空间）。

目的端达梦的表空间需要根据源端数据量情况，规划表空间数据文件个数以及单个数据文件上限，单个数据文件不宜过大，可以创建多个数据文件。

需要根据源端表空间使用情况进行规划目的端数据文件初始大小，避免在迁移过程中频繁自动扩展，影响迁移速度，值得注意的是从 oracle 迁移到达梦，由于 oracle 高水位线的存在，会导致直接在 dba_segments 查出来的表大小比表实际大小要大，所以需要查看实际空间使用情况。

  * **用户规划**



目的端用户参照源库用户进行创建，对于权限设置、资源限制要求等内容需要按照目的端达梦实际上线要求进行设置，在用户规划时，需要注意：创建用户授予权限，不建议授予 DBA 角色，一般的权限，授予 resource、public、vti、soi、svi 这几个角色即可满足使用要求，其中各个角色的介绍，如下图所示：

|**角色**|**权限**|
|--|--|
|DBA|DM 数据库系统中对象与数据操作的最高权限集合，拥有构建数据库的全部特权，只有 DBA 才可以创建数据库结构。|
|RESOURCE|可以创建数据库对象，对有权限的数据库对象进行数据操纵，不可以创建数据库结构。|
|PUBLIC|不可以创建数据库对象，只能对有权限的数据库对象进行数据操纵。|
|VTI|具有系统动态视图的查询权限，VTI 默认授权给 DBA 且可转授。|
|SOI|具有系统表的查询权限。|
|SVI|具有基础 v 视图的查询权限。|


设置用户的资源限制参数中，比较常用的几个参数如下图，图中显示参数缺省值均为在通用机情况下：

|**资源设置项**|**说明**|**最大值**|**最小值**|**缺省值**|
|-----|-----|-----|-----|-----|
|CONNECT_IDLE_TIME|会话最大空闲时间（单位：1 分钟）。|1440（1 天）|1|无限制|
|FAILED_LOGIN_ATTEMPS|将引起一个账户被锁定的连续注册失败的次数。|100|1|3|
|PASSWORD_LIFE_TIME|一个口令在其终止前可以使用的天数。|365|1|无限制|
|PASSWORD_REUSE_TIME|一个口令在可以重新使用前必须经过的天数。|365|1|无限制|
|PASSWORD_REUSE_MAX|一个口令在可以重新使用前必须改变的次数。|32768|1|无限制|
|PASSWORD_LOCK_TIME|如果超过 FAILED_LOGIN_ATTEMPS 设置值，一个账户将被锁定的分钟数以天为单位的口令过期宽限时间，过期口令超过该期限后，禁止执行除修改口令以外的其他操作。|1440（1 天）|1|1|
|PASSWORD_GRACE_TIME|以天为单位的口令过期宽限时间，过期口令超过该期限后，禁止执行除修改口令以外的其他操作。|30|1|10|


口令限制，在通用机中创建的用户口令长度，复杂度默认只有口令长度九位，即 PWD_POLICY=2，参数值含义如下：

用户口令最长为 48 字节，创建用户语句中的 PASSWORD POLICY 子句用来指定该用户的口令策略，系统支持的口令策略有：

0：无策略。
1：禁止与用户名相同。
2：口令长度不小于 9。
4：至少包含一个大写字母（A-Z）。
8：至少包含一个数字（0-9）。
16：至少包含一个标点符号（英文输入法状态下，除“和空格外的所有符号）口令策略可单独应用，也可组合应用。组合应用时，如需要应用策略 2 和 4，则设置口令策略为 2+4=6 即可。

该参数不可以在 dm.ini 中直接修改，需要用 SP_SET_PARA_VALUE 系统过程来修改，如 SP_SET_PARA_VALUE(1, 'PWD_POLICY',8)。

> **注意**
>
> 强烈建议用户在首次安装数据库初始化实例时，立即修改数据库系统用户的初始密码，并设置一定的密码强度，以保障数据安全性。



  * 创建迁移用户和表空间



从 Oracle 移植到 DM，要先创建好待使用的用户和这个用户的表空间，不要把数据迁移到系统默认的管理员 SYSDBA 用户下和 MAIN 表空间下。

Oracle 的体系架构是单库多实例的模式，DM 数据库也是单库多实例的架构，从 Oracle 迁移到达梦的时候就需要针对 Oracle 中的库在达梦里面创建一个用户和表空间来对应。例如 Oracle 中有一个库 oracletest1，需要移植库中的 OT 模式下的数据对象，达梦里面先创建一个表空间 dbtest ，然后创建一个用户 OT-DM，指定默认表空间为 dbtest。示例如下：

（1）创建 dbtest 表空间存储 Oracle 中 oracletest1 库迁移过来的数据。

```
create tablespace "dbtest" datafile '/data/dmdata/DAMENG/DBTEST.DBF' size 2048;
--创建表空间dbtest，数据文件为DBTEST.DBF，打开数据库文件自动扩展。
```

（2）创建 OT-DM 用户并授予权限，使用 dbtest 表空间。

```
create user "OT-DM" identified by "密码" --创建用户
default tablespace "dbtest"--指定用户DBTEST表空间为dbtest
default index tablespace "dbtest";--指定用户DBTEST索引表空间为dbtest
grant "PUBLIC","RESOURCE","SOI","SVI","VTI" to "OT-DM";--授予用户DBTEST常规权限
```

迁移 Oracle 库的数据的时候，用 OT 用户连接 oracletest1；用 OT-DM 用户连接达梦，在迁移过程中指定迁移到的用户模式，这样就把 oracletest1 库的 OT 模式下的数据迁移到了达梦 OT-DM 用户模式下。

在做 oracletest 迁移的时候要先分析本次迁移需要从源库中移植库中的那个模式或者那几个模式下的数据，然后为每一个模式，分别在达梦中创建独立的表空间和用户，达梦同时生成相应的模式，大多数情况下，并不是所有的数据都需要迁移，所以在迁移准备阶段，一定要明确迁移那些模式下的哪些数据。

  3. 迁移工具准备



本文选择“DM 数据迁移工具 V8 (Build 2023.04.17)”作为本次迁移要使用到的迁移工具，不同版本界面显示上可能存在一些差异。该工具在安装数据库客户端时已安装完成可以直接使用，工具存放在 DM 数据库安装目录下 tool 文件夹中。

Linux 环境下进入 tool 目录中执行./dts 即可运行 DM DTS 工具，windows 环境下可直接双击启动。

#### 3.4.2 迁移步骤

  1. **创建迁移**



（1）打开 DMDTS 迁移工具点击左上方的 3 色图标新建迁移工程。

![企业微信截图_17019415478641.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207173412SJ9MM0RQU3RNU8IW2M)

（2）打开刚刚创建的工程右键点击“迁移”，选择“新建迁移”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206170015TKPEUYW832H82U1WH0)

新建迁移，自定义迁移名称。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206142405LP49HUSASDREVSIO9W)

（3）新建迁移完成后点击下一步。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206170444D1JIH1M0XPICPEDUQ0)

（4）从“其他数据库迁移到达梦”选项中选择迁移方式为“Oracle ==> DM”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206170152OT1H7QJBH9AHQ2KCGC)

  2. **连接数据库**



迁移方式选择完毕后开始连接数据库，首先连接源端 Oracle 数据库，再连接目的端 DM 数据库。

（1）连接源端 Oracle 数据库。

输入源端 Oracle 数据库相关登录信息，在“数据库名”选项中选择需要迁移的数据库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129191851EBQL72IRVSYZGL0ZX3)

在创建连接 Oracle 数据库时建议通过指定驱动的方式来连接数据库，避免因为驱动版本不适配等问题导致迁移失败。驱动可以在 Oracle 官网获取与 Oracle 迁移版本相对应的驱动。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061425317K9H50UI42I52WJB9N)

（2）连接目的端 DM 数据库。

输入目的端 DM 数据库相关登录信息，选择与源端对应的迁移用户连接数据库。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129191931NECN4ETKUSF02LKN3B)

  3. **配置迁移对象及策略** 。



（1）迁移对象方式及迁移策略中勾选“保持对象名大小写”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206170828QBC5KTU29U5BN7DHG3)

当勾选了“使用默认数据类型映射关系”后在迁移时 DTS 会将源端 Oracle 数据库中相应的数据类型采用默认的映射关系映射到目的端 DM 数据库中。如果在这里勾选了“使用默认数据类型映射关系”，后面又自定义了数据类型映射关系，DTS 会优先选择使用自定义的数据映射关系。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206170847K3EAAX926K7OR0YO1H)

在“迁移策略”点击“查看数据类型映射关系”可以查看源端 Oracle 到目的端 DM 的数据类型映射关系，包括“源数据类型名”、“源精度”、“源标度”、“目的数据类型名”、“目的精度”等等。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129192044IJV3SZKT8B5WPOPATX)

（2）勾选源端待迁移的模式。

这需要勾选源端待迁移的模式下的数据。

在指定模式阶段，用户可以通过“源模式”选择源端要迁移的模式，通过“目的模式”来指定迁移到 DM 的模式，通过是否勾选“创建模式”、“表”、“视图”、“存储过程/函数”、“触发器”来指定目的端 DM 是否要迁入源端 Oracle 中的这些对象。由于在 DM 数据库准备阶段已经提前将 DBTEST 模式创建好了，这里就不勾选“创建模式”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206172535OI7H7TKLUNR0XOGV29)

（3）勾选源端数据库中需要迁移的模式下的数据对象。

这里可以看到源端待迁移模式下所有的数据对象，用户可以自定义选择 Oracle 需要迁移的具体对象。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129192138CE7SAS9FNP03B7P37R)

> **注意**
>
> 在 SQL 评估阶段不兼容的对象不需要勾选，待其它对象迁移完成后，再手动修改和导入这些不兼容的对象。



用户可以通过点击右上方的“分析源对象”统计选中的源端待迁移对象。用户可以通过该功能对源端迁移对象进行统计分析，包括“源对象统计”、“源表统计”、“源表详细”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206172749WIWAM4BPHMPFXLMK90)

待迁移具体对象勾选完毕后可以通过点击转换进行自定义对象迁移策略。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206172911VQ3HCD8WMPFLSFNRW8)

（4）自定义对象迁移策略。

点击转换后可以设置表的映射关系，包括迁移策略和列映射选项。

① 迁移策略。

在迁移策略中可根据需要设置表及数据迁移的策略。在左侧选项中可以选择“表定义”、“主键”、“约束”、“索引”等的迁移策略；在右侧选项中可以配置与迁移数据相关的策略。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129192345T5G7NWRCFNRVRGS09Y)

部分选项说明：

a. 压缩：指定迁移的目的表是否按照压缩方式存储。

b. 强制聚集索引：即使源表的主键为非聚集主键，创建目的表时也会被转换为聚集主键。

c.  强制非聚集索引：即使源表的主键为聚集主键，创建目的表时也会被转换为非聚集主键。

d. 启用标志列插入：如果表上有标志列，则迁移数据时会强制向标志列插入值，以保证源和目的数据完全一致

e. 显示行数：将在迁移任务过程中，显示数据的行数。

f. 拷贝记录：如果目的表已存在，直接拷贝记录，不需要创建表。

g. 删除后拷贝记录：迁移过程中先删除已存在的目的表，再重新创建新目的表。

h. 源一次读取行数：设置从数据源中读取数据时每次读取数据的行数，该参数决定内存中缓存结果集的大小，对于数据量很大的数据源，设置该参数，可以控制内存的使用。

i. 目的一次提交行数：设置向目的数据库中每次写入数据的行数。当数据量比较大时，减小该参数的值可以减少内存的使用。但会影响迁移的速度。

j. 缓存批数：设置缓存队列的长度。调整该参数可以调整迁移过程中内存的使用。

> **注意**
>
> 如果数据量较大，可以选着先迁移表结构定义相关内容，再迁移数据，最后迁移索引。



② 列映射选项。

在列映射选项中可根据需求修改源端迁移到目的端表的列名、数据类型、精度、小数位数、默认值、是否可空、主键、自增列、起始值、增量信息等。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129192405RMKP3AORGWZ98ZKUZ2)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207161250X9KJUFS2FQ0WEKPVYM)

  4. **开始迁移**



（1）检查迁移任务，确认迁移对象是否正确。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129192459LDPWRWQU4RXKH0D446)

（2）检查确认后点击“完成”即可开始迁移。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231207100020GVZUDRZJJFMH2QP8ZO)

  5. **对象补迁**



由于 Oracle 和 DM 数据库在某些语法使用上存在差异，导致某些对象可能会迁移失败，再加上在迁移评估阶段语法不兼容的对象，用户需要根据 DM 语法手动修改这些无法使用工具迁移的对象再导入到 DM 数据库中。

### 3.5 数据校验

通过 SQL 脚本分别统计 Oracle 端和 DM 端的对象和数据量，通过对比判断是否迁移完成。

使用脚本进行验证介绍如下：

  1. 统计用户下各类对象的数量，在源端和目的端通过对应的系统表进行查询记录对比是否一致。
  2. 统计用户下的表数量及对应的数据条目，在源端和目的端分别创建辅助表，使用脚本将源端和目的端的表的数量和表的数据量插入到辅助表中，通过查看辅助表内的数据进行比对，验证表的数量和数据量是否一致。



#### 3.5.1 统计源端对象及数据

  1. 统计各个表的数据量。



（1）在源端创建辅助表 table_count 用来统计模式下所有表的数据量。

```
create table table_count (owner varchar(100),table_name varchar(100),cnt int);
```

（2）分别在源端和目的端执行脚本，将模式下表的数据量插入到辅助表 table_count 中，需要统计哪个模式将  ’OT’ 替换为模式名即可。

```
declare
v_owner VARCHAR2(100);
v_tabname VARCHAR2(100);
stmt  VARCHAR2(200);
num_rows number;
begin
for rec in (select owner,table_name from dba_tables where owner='OT' order by 1, 2)---owner根据实际情况调整
            loop
            select rec.owner,rec.table_name into v_owner,v_tabname from dual; 
            stmt := 'select count(*) from "' || v_owner || '"."' || v_tabname || '"';
            EXECUTE IMMEDIATE stmt INTO num_rows;  
EXECUTE IMMEDIATE 'insert into table_count values('''||v_owner||''','''||v_tabname||''','''||to_number(num_rows)||''')';
           end loop;
end;
```

查询结果：

|**表名**|**数据量**|
|---|---|
|contacts|319|
|countries|25|
|customers|319|
|employees|107|
|inventories|1112|
|locations|23|
|order_items|665|
|orders|105|
|product_categories|5|
|products|288|
|regions|4|
|warehouses|9|
|regions_mv|1|
|table_count|1|


  2. 统计各种对象的数量。



在数据库中除了表还有索引，视图，序列，存储过程，PKG，自定义类型等都需要验证。

Oracle 端执行如下语句。

```
SELECT
 A.USERNAME "用户名",
 (SELECT COUNT(1) FROM DBA_TABLES B WHERE B.OWNER = A.USERNAME) "表数量",
 ( SELECT COUNT(1) FROM DBA_VIEWS G WHERE G.OWNER = A.USERNAME ) "视图数量",
  (SELECT COUNT(1) FROM DBA_MVIEWS K WHERE K.OWNER = A.USERNAME) 物化视图数量,
 ( SELECT COUNT(1) FROM DBA_TRIGGERS H WHERE H.OWNER = A.USERNAME ) "触发器数量",
 ( SELECT COUNT(DISTINCT I.NAME) FROM DBA_SOURCE I WHERE I.OWNER = A.USERNAME AND I.TYPE = 'FUNCTION' ) "函数数量",
 ( SELECT COUNT(1) FROM DBA_SEQUENCES J WHERE J.SEQUENCE_OWNER = A.USERNAME ) "序列数量",
 ( SELECT COUNT(DISTINCT L.NAME) FROM DBA_SOURCE L WHERE L.OWNER = A.USERNAME AND L.TYPE = 'PROCEDURE' ) "存储过程数量",
 ( SELECT COUNT(1) FROM DBA_DB_LINKS M WHERE M.OWNER = A.USERNAME ) "DBLINK数量",
 ( SELECT COUNT(1) FROM DBA_INDEXES I WHERE UNIQUENESS = 'UNIQUE' AND OWNER =A.USERNAME OR INDEX_NAME NOT LIKE 'SYS_%' AND OWNER =A.USERNAME) "索引数量",
 ( SELECT COUNT(1) FROM DBA_OBJECTS WHERE OBJECT_TYPE='TYPE' AND OWNER =A.USERNAME ) "自定义类型",
 ( SELECT COUNT(1) FROM DBA_OBJECTS WHERE OBJECT_TYPE='PACKAGE' AND OWNER =A.USERNAME) "PKG数量"
FROM
 DBA_USERS A WHERE A.USERNAME IN ('OT');
```

查询结果：

|**用户名**|**OT**|
|---|---|
|表数量|14|
|视图数量|0|
|物化视图|1|
|触发器数量|9|
|函数数量|0|
|序列数量|9|
|存储过程数量|0|
|DBLINK 数量|0|
|索引数量|12|
|自定义类型|0|
|PKG 数量|0|


#### 3.5.2 统计目的端对象及数据

  1. 统计各个表的数据量。



（1）分别在源端和目的端创建辅助表 table_count 用来统计模式下所有表的数据量。

```
create table table_count (owner varchar(100),table_name varchar(100),cnt int);
```

（2）在目的端执行脚本，将模式下表的数据量插入到辅助表 table_count 中。

```
declare
v_owner VARCHAR2(100);
v_tabname VARCHAR2(100);
stmt  VARCHAR2(200);
num_rows number;
begin
for rec in (select owner,table_name from dba_tables where owner='OT-DM' order by 1, 2)---owner根据实际情况调整
            loop
            select rec.owner,rec.table_name into v_owner,v_tabname from dual; 
            stmt := 'select count(*) from "' || v_owner || '"."' || v_tabname || '"';
            EXECUTE IMMEDIATE stmt INTO num_rows;  
EXECUTE IMMEDIATE 'insert into table_count values('''||v_owner||''','''||v_tabname||''','''||to_number(num_rows)||''')';
           end loop;
end;
```

查询结果：

|**表名**|**数据量**|
|---|---|
|contacts|319|
|countries|25|
|customers|319|
|employees|107|
|inventories|1112|
|locations|23|
|order_items|665|
|orders|105|
|product_categories|5|
|products|288|
|regions|4|
|warehouses|9|
|mtab$_regions_mv|1|
|table_count|1|


  2. 统计各种对象的数量。



在数据库中除了表还有索引，视图，序列，存储过程，PKG，自定义类型等都需要验证。

达梦端执行如下语句:

下面的 sql 索引数量只统计达梦端用户自建索引的个数，其次是 ORACLE 数据库中自定义类型为 TYPE ，但是在达梦中自定义类型除了 TYPE 外还有一部分会归于 CLASS ，所以在对比 ORACLE 的自定义类型在达梦中应该是自定义类型 TYPE+CLASS。

```
SELECT
 A.USERNAME  "用户名",
 (SELECT COUNT(1) FROM DBA_TABLES B WHERE B.OWNER = A.USERNAME)  "表数量",
 ( SELECT COUNT(1) FROM DBA_VIEWS G WHERE G.OWNER = A.USERNAME )  "视图数量",
 ( SELECT COUNT(1) FROM DBA_TRIGGERS H WHERE H.OWNER = A.USERNAME )  "触发器数量",
 ( SELECT COUNT(DISTINCT I.NAME) FROM DBA_SOURCE I WHERE I.OWNER = A.USERNAME AND I.TYPE = 'FUNCTION' )  "函数数量",
 ( SELECT COUNT(1) FROM DBA_SEQUENCES J WHERE J.SEQUENCE_OWNER = A.USERNAME )  "序列数量",
 ( SELECT COUNT(DISTINCT L.NAME) FROM DBA_SOURCE L WHERE L.OWNER = A.USERNAME AND L.TYPE = 'PROCEDURE' )  "存储过程数量",
 ( SELECT COUNT(1) FROM DBA_DB_LINKS M WHERE M.OWNER = A.USERNAME )  "DBLINK数量",
 ( SELECT COUNT(1) FROM DBA_INDEXES I WHERE UNIQUENESS = 'UNIQUE' AND OWNER =A.USERNAME OR INDEX_NAME NOT LIKE 'INDEX335%' AND OWNER =A.USERNAME)  "索引数量",
 ( SELECT COUNT(1) FROM DBA_OBJECTS WHERE OBJECT_TYPE='TYPE' AND OWNER =A.USERNAME OR OBJECT_TYPE='CLASS' AND OWNER =A.USERNAME )  "自定义类型",
 ( SELECT COUNT(1) FROM DBA_OBJECTS WHERE OBJECT_TYPE='PACKAGE' AND OWNER =A.USERNAME)  "PKG数量"
FROM
 DBA_USERS A WHERE A.USERNAME IN ('OT-DM');
```

查询结果：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061058067PCXPPH3Z7JRV5I1V2)

检查 DM 目的端的查询结果和 ORACLE 源端是否一致，本例中可以看到查询结果与 oracle 源端查询结果一致，表明迁移数据无缺失。

### 3.6 数据库移植完毕后收尾工作

#### 3.6.1 索引补录

在之前的测试迁移，适配完成之后做的一些优化工作，可以创建一张记录表，将优化记录在一张表中，在正式迁移完成之后，将前期优化过程中添加的索引等内容进行补录。

#### 3.6.2 更新统计信息

先进行模式级别更新，再更新索引统计信息，也可以根据需求，进行单独表或者单独列进行更新，下面列出来一些更新统计信息的方式：

> **注意**
>
> 大表更新统计信息尽量采用更新关联列、条件列以及索引的统计信息，不要更新全表。



  1. 模式更新。



当迁移后全库数据量较小时，可以使用全模式更新的方法：

```
DBMS_STATS.GATHER_SCHEMA_STATS( '模式名',100,TRUE,'FOR ALL COLUMNS SIZE AUTO');
```

> **注意**
>
> 如果数据量较大，该过程可能较慢，请耐心等待。



  2. 收集表的统计信息。



对于需要单独收集统计信息的表，可以按照如下方式收集：

```
DBMS_STATS.GATHER_TABLE_STATS('模式名','表名',NULL,100,TRUE,'FOR ALL COLUMNS SIZE AUTO');
```

  3. 收集所有列的统计信息。



当全库数据量较大时，按模式更新统计信息比较慢，可以使用全列收集统计信息的方式进行收集，通过存储过程完成对全库全部列的收集：

STAT \< 统计信息采样百分比 > ON [\< 模式名 >.]\< 表名 >(\< 列名 >);
如：STAT 100 ON TEST(A);

```
--更新指定表的所有列的统计信息可使用 SYSDBA 或者用户自身
--DROP TABLE SYSDBA.stat_history;
--SELECT * FROM SYSDBA.stat_history;
--创建日志记录表
create table SYSDBA.stat_history (beg_time TIMESTAMP,end_time timestamp,sql_str varchar2(500),table_name varchar2(200),col_name VARCHAR2(200));

--创建存储过程
CREATE OR REPLACE PROCEDURE "SYSDBA"."TABLE_STATS"
AUTHID DEFINER
AS
	declare --更新所有列的统计信息
v_sql varchar(4000);
v_begtime VARCHAR2(200);
v_endtime varchar2(200);
CURSOR c1 IS SELECT
  SCH.NAME AS SCHEMA_NAME,
        TAB.NAME AS TABLE_NAME ,
        SYSCOL.NAME AS COLUMN_NAME
FROM
        SYSOBJECTS AS TAB,
        SYSOBJECTS AS SCH,
        SYSCOLUMNS AS SYSCOL
WHERE
        SCH.ID =TAB.SCHID
    AND TAB.ID =SYSCOL.ID
    AND SCH.NAME in ('SYSDBA')   --填写实际用户名
    AND SYSCOL.TYPE$ NOT IN ('BLOB','CLOB','TEXT')
    and TAB.NAME  IN(select  TABLE_NAME  from ALL_TABLES WHERE OWNER in ('SYSDBA') and TABLE_NAME not like '%BM$_%' AND TABLE_NAME NOT LIKE 'MTAB$%');  
begin
  execute immediate 'truncate table SYSDBA.STAT_HISTORY;';
  for i in c1 
  loop  
  v_sql='stat 100 on '||i.SCHEMA_NAME||'."'||i.TABLE_NAME||'"("'||i.COLUMN_NAME||'");';
  begin  
  v_begtime := sysdate();  
  execute immediate v_sql;
  v_endtime := sysdate();
  insert into SYSDBA.stat_history VALUES (v_begtime,v_endtime,v_sql,i.TABLE_NAME,i.COLUMN_NAME);
  commit;
     EXCEPTION WHEN OTHERS THEN
                PRINT SQLERRM;  
  end;
  end loop; 
  end;   
--调用存储过程
call "SYSDBA"."TABLE_STATS" ();

select * from SYSDBA.stat_history;
```

  4. 收集分区较多的分区表的统计信息



1）使用“`STAT 100 on 表名（列名）`”的方法收集统计信息时，水平分区表子表采样数受 HP_STAT_SAMPLE_COUNT 参数影响。需要适当调大此参数，参数值大于实际分区数，否则分区表统计信息收集不准确，参数修改方法如下：

```
sp_set_para_value(1,'HP_STAT_SAMPLE_COUNT',2000);
```

2）若不调整 HP_STAT_SAMPLE_COUNT 参数，可以使用 DBMS 系统包进行 GLOBAL 全部分区收集，此方法收集统计信息速度较慢：

```
--按列收集：
DBMS_STATS.GATHER_TABLE_STATS ('模式名','表名',null,100,false,'FOR COLUMNS "列名" SIZE AUTO',1,'GLOBAL');
--按表收集：
DBMS_STATS.GATHER_TABLE_STATS ('模式名','表名',null,100,false,'FOR ALL COLUMNS SIZE AUTO',1,'GLOBAL');
```

  5. 清除列的统计信息。



```
SP_COL_STAT_DEINIT('模式名','表名','列名');
```

  6. 收集索引的统计信息 。



```
CALL SP_INDEX_STAT_INIT('模式名','索引名');
DBMS_STATS.GATHER_INDEX_STATS(user,'索引名');
STAT <统计信息采样百分比> ON INDEX [<模式名>.]<索引名>;
```

  7. 对表上所有索引生成统计信息。



```
CALL SP_TAB_INDEX_STAT_INIT('模式名','表名');
```

  8. 收集表中某列的统计信息。



```
STAT <统计信息采样百分比> ON [<模式名>.]<表名>(<列名>);
--如：STAT 100 ON TEST(A);
```

因为更新索引统计信息比较常用，可以建立一个 sql 脚本，将更新一个模式下的所有索引统计信息的语句输出到一个 sql 文件中，在需要执行时候，直接执行 sql 脚本文件，在索引数量很多情况下，执行 sql 脚本可以看到执行到哪条语句，语句如下：

```
declare
        HANDLE UTL_FILE.FILE_TYPE;
begin
        HANDLE  :=UTL_FILE.FOPEN('D:\dm8\data\DAMENG', 'utl_test.sql', 'W');
        for rec in
        (
                select INDEX_NAME from dba_indexes WHERE table_owner='SYSDBA'
        )
        loop
                UTL_FILE.PUTF(HANDLE,'stat 100 on index  %s;',rec.INDEX_NAME);
                UTL_FILE.FFLUSH(HANDLE);

        end loop;
        UTL_FILE.FCLOSE(HANDLE);
end;
/
```

> **注意**
>
> 1.如果是非 WINDOWS 环境，且运行达梦服务器的进程有效用户为 root，则禁止文件操作。  
>  2.执行操作的数据库用户必须有 DBA 权限。  
>  3.只能创建、修改在达梦系统目录（含子目录）下的文件。用户可以通过 select * from v$dm_ini where para_name
> like '%SYSTEM_PATH%';查看系统目录。  
>  4.SYSDBA 换成现场需要的业务模式名，将 UTL_FILE.FOPEN 中指定的路径地址以及脚本文件，换成现场实际环境的信息。  
>



#### 3.6.3 备份

完成整体的迁移工作后，如果为正式迁移，需要对系统做一次全库备份，如果是测试迁移，则需要将迁移过程中的问题处理等内容整理成迁移报告，以便后续正式迁移时，可以快速解决问题，完成迁移工作。

除此之外，通常情况下生产系统都需要制定定时备份任务，备份时间点建议避开业务高峰期，可根据配置备份作业任务。

### 3.7 应用迁移

一般情况下，oracle 数据库迁移完成后，直接更换应用连接 oracle 数据源到达梦数据库，应用代码适配不用修改。为了验证系统移植的完整性，还需要进行应用的相关功能和性能测试，并改造应用到连接达梦数据库一个最佳状态。

## 四、常见问题

#### 4.1 记录超长

问题原因：一条记录的长度，超过页大小的一半。

解决方法：

  1. 修改初始化页大小为 32K，如果本身表中存在大量的 varchar2 类型，并且长度都很长的情况下不适用。
  2. 将类似 varchar(8000) 这些字段类型修改为 text。
  3. 开启超长记录。



#### 4.2 字符串截断

问题原因：数据中含有中文，目的端达梦为 UTF-8，源端为 GBK，因两个字符集中文所占字节数不同，导致目的端存储数据报错。

解决方法：

  1. 初始化时选择 GB18030。
  2. 初始化时选择 varchar 类型以字符为单位。
  3. varchar 和 varchar2 类型的数据在迁移时指定对应数据类型字符映射关系 X2。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613103635CDLDKLI1MO0EUYYNBI)

#### 4.3 违反唯一性约束

问题原因：因为表中设置了唯一性约束或者主键约束，但是数据中有重复记录。

源端因为是 Oracle，Oracle 默认就是区分结尾空格，达梦端如果初始化时候没有指定 BLANK_PAD_MODE=1，则不区分，如果这个字段上有唯一约束，会报错。

解决方式：如果是就有重复数据，那就和应用确认重复数据的处理方式；如果是因为空格问题，就需要考虑重新初始化实例，指定初始化参数 BLANK_PAD_MODE=1 后，再重新迁移。

#### 4.4 函数索引表达式超长

问题描述：源端 Oracle 的函数索引，迁移时候报错“函数索引表达式超长”。

解决方式：迁移工具可能将函数索引表达式写了好多遍，获取源端函数索引定义语句，手动在目的端上创建。

#### 4.5 精度超出范围

问题描述：数据迁移过程中报错精度超出范围。

解决方式：判断是否源端和目的端字符集不一致，GBK 和 UTF-8 中文所占字节数不一致，可能导致该问题，可以在迁移的时候，指定数据类型映射关系，字符长度扩大几倍。

#### 4.6 被引用列未添加索引

问题描述：在迁移外键的时候，报错被引用列未添加索引。

解决方式：先添加唯一约束再添加外键约束。

#### 4.7 无效的表或视图

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202311291933127U71R93YM9PZWL1HC0)

问题原因：所依赖基表未提前迁移完成。

解决方法：先将所依赖基表迁移完成后再尝试重新迁移视图。

#### 4.8 物化视图对象已存在

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231129193339ZAE0IQ3BA3N14TFK4E)

问题原因：Oracle 里面视图名称和表名称允许同名，达梦不允许。

解决方法：修改其中一个数据库对象的名。

## 五、更多帮助

更多 Oracle 到 DM 迁移常见问题可参考[达梦在线服务平台-常见问题-从 Oracle 迁移到 DM](https://eco.dameng.com/document/dm/zh-cn/faq/faq-oracle-dm8-migrate.html)。

更多 DTS 工具使用详情可参考 DTS 工具“帮助主题”，具体位置见下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231206113201FH9UKK98WMLA7YKJSK)

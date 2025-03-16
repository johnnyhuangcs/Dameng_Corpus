

# DMASM 介绍

## 10.1 DMASM 概述

DM 自动存储管理器（DM Auto Storage Manager，简称 DMASM）是一个专用的分布式文件系统。

DMDSC 如果直接使用块设备作为共享存储来存放数据库文件，会因为块设备本身的诸多功能限制，造成 DMDSC 集群在使用、维护上并不是那么灵活方便。为了克服块设备的这些使用限制，DM 专门设计了一款分布式文件系统 DMASM，来管理块设备的磁盘和文件。DMASM 的出现为 DMDSC 灵活管理和使用块设备提供了完美的解决方案。

使用 DMASM 自动存储管理方案，可以帮助用户更加便捷地管理 DMDSC 集群的数据库文件。DMASM 的主要部件包括：提供存储服务的块设备、DMASMSVR 服务器、DMASMAPI 接口、初始化工具 DMASMCMD 和管理工具 DMASMTOOL 等。

下图为一个部署了 DMASM 的 DMDSC 集群结构图。

![图 10.1 一个部署了 DMASM 的 DMDSC 集群结构图.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202301121527079BLX9IA3SPH4TT1NIN)

图10.1 一个部署了DMASM的DMDSC集群结构图

## 10.2 使用 DMASM 的好处

下面通过对比的方式，展示在 DMDSC 中使用 DMASM 的好处。

如果直接将块设备作为 DMDSC 的共享存储，存在多项限制，增加了 DBA 的管理难度。具体限制在以下几点：

  1. 不支持动态扩展文件大小；在创建数据文件时，就必须指定文件大小，并且文件无法动态扩展。
  2. 数据文件必须占用整个裸或块设备盘，造成空间浪费。
  3. 不支持类 linux 的文件操作命令，使用不方便。
  4. 操作系统支持最大块设备数目较小，无法创建足够的数据库文件。



为了克服上述限制，推荐用户使用 DMASM 来管理块设备的磁盘和文件。DMASM 提供了基本的数据文件访问接口，可以有效降低 DMDSC 共享存储的维护难度。DMASM 提供的主要功能包括：

  1. 分布式管理

支持多台机器并发访问 ASM 磁盘和文件，提供全局并发控制。

  2. 磁盘组管理

支持创建和删除磁盘组，将块设备格式化为 DMASM 格式，并由 DMASMSVR 统一管理；一个磁盘组可以包含一个或者多个 ASM 磁盘；磁盘组支持在线增加
ASM 磁盘，实现动态存储扩展。

  3. 文件管理

支持创建、删除、截断文件等功能；支持创建目录；支持动态扩展文件；文件可以存放在一个磁盘组的多个磁盘中，文件大小不再受限于单个磁盘大小。

  4. 完善、高效的访问接口

通过 DMASMAPI 可以获得各种文件管理功能。

  5. 通用功能的管理工具

DMASMTOOL 提供一套类 Linux 的文件操作命令用于管理 ASM 文件，降低用户学习、使用 DMASM 文件系统的难度。



## 10.3 DMASM 术语和基本概念

### 10.3.1 DMASM 术语

本节介绍 DMASM 所用到的术语。术语的详细概念请参考 [10.3.2 DMASM 基本概念](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html#10.3.2%20DMASM%20%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)。

表10.1 中英文对照术语表

|**中文术语**|**英文术语**|
|----|----|
|DM 自动存储管理器|DM Auto Storage Manager，简称 DMASM|
|ASM 磁盘|ASM DISK|
|磁盘组|DISK GROUP|
|ASM 文件|ASM FILE|
|簇|extent|
|数据分配单元|Allocation Units，简称 AU|
|DM 集群注册表|DM Clusterware Registry，简称 DCR|
|DCR 磁盘|DCR DISK|
|VOTE 磁盘|VOTE DISK，简称 VTD|
|DMASM 文件系统初始化工具|DMASMCMD  （专门用于 DMASM 非镜像环境）|
|DMASM 服务器|DMASMSVR  （专门用于 DMASM 非镜像环境）|
|DMASM 应用程序访问接口|DMASMAPI  （专门用于 DMASM 非镜像环境）|
|DMASM 管理工具|DMASMTOOL （专门用于 DMASM 非镜像环境）|


### 10.3.2   DMASM 基本概念

下面详细介绍各概念：

DM 自动存储管理器（DM Auto Storage Manager，简称 DMASM）

DMASM 是一个分布式文件系统，用来管理块设备的磁盘和文件。

ASM 磁盘

ASM 磁盘是指经过 DMASMCMD 工具格式化，可以被 DMASMSVR 识别的物理磁盘。ASM 磁盘是组成磁盘组的基本单位，一个块设备只能格式化为一个 ASM 磁盘，不支持分割使用。

磁盘组

磁盘组由一个或多个 ASM 磁盘组成，是存储 ASM 文件的载体；一块 ASM 磁盘只能属于一个磁盘组。DMASM 支持动态添加 ASM 磁盘。DMDSC 集群中，一般建议将日志文件和数据文件保存到不同的磁盘组中。

ASM 文件（ASMFILE）

在 ASM 磁盘组上创建的文件，称之为 ASM 文件。一个 ASM 文件只能保存在一个磁盘组中，但一个 ASM 文件的数据可以物理存放在同一磁盘组的多个 ASM 磁盘中。DMDSC 集群中，需要多个节点共享访问的数据库文件、日志文件、控制文件等，一般会创建为 ASM 文件。

DMASM 文件路径都以“+GROUP_NAME”开头，使用“/”作为路径分隔符，任何以“+”开头的文件，DM 都认为是 DMASM 文件，“GROUP_NAME”是磁盘组名称。比如，“+DATA/ctl/dm.ctl”表示 dm.ctl 文件，保存在 DMASM 文件系统的“DATA”磁盘组的 ctl 目录下。"+"号只能出现在全路径的第一位，出现在任意其他地方的路径都是非法的。

DMASM 只提供文件级别并发控制，访问 ASM 文件时系统会进行封锁操作。比如，正在访问的数据文件不允许被删除。但是 DMASM 并不提供数据文件的读写并发控制。DMASM 允许多个用户同时向同一个文件的相同偏移写入数据，一旦发生这种并发写，系统无法预知最终写入磁盘的数据是什么。因此，DMASM 必须由使用 DMASM 的应用程序来控制数据文件的读写并发，即避免多个程序同时写同一个数据文件的相同数据块。

不提供读写并发控制主要原因有两个：

原因一 达梦数据库管理软件已经提供了数据读写的并发控制机制，确保不会同时读、写相同的数据页，因此 DMASM 不需要再实现一套重复的并发控制策略。

原因二 DMASM 镜像不提供读写并发控制可提升 ASM 文件的读写效率。如果提供读、写操作的全局并发控制，虽然可避免并发写入导致数据不一致，但这种策略会影响读、写性能。

簇（Extent）

簇是 ASM 文件的最小分配单位，一个簇由物理上连续的一组 AU 构成。簇的大小为 4，也就是说一个 ASM 文件至少占用 4 个 AU，也就是 4M 的物理存储空间。

数据分配单元（Allocation Units，简称 AU）

DMASM 存储管理的最小单位。AU 的大小为 1M，为系统固定大小，无需用户指定。DMASM 以 AU 为单位将磁盘划分为若干逻辑单元，ASM 文件也是由一系列 AU 组成。根据 AU 的不同用途，系统内部定义了一系列 AU 类型，包括：desc AU、inode AU、REDO AU、和 data AU。

DCR 磁盘（DCR DISK）

DM 集群注册表（DM Clusterware Registry，简称 DCR）磁盘专门用于存储 DCR 文件。DCR 文件记录了存储、维护集群配置的详细信息。整个集群环境共享 DCR 磁盘信息，包括集群（DMDSC、DMASM、DMCSS）资源、实例名、监听端口、集群中故障节点信息等。DCR 必须存储在集群中所有节点都可以访问到的共享存储中，并且只支持保存在 DMASM 文件系统管辖范围之外的共享存储上。在一个集群环境中只能配置一个 DCR 磁盘。

VOTE 磁盘（VOTE DISK）

VOTE 磁盘专门用于存储 VTD 文件。VTD 文件记录了集群成员信息。DM 集群通过 VOTE DISK 进行心跳检测，确定集群中节点的状态，判断节点是否出现故障。当集群中出现网络故障时，使用 VOTE DISK 来确定哪些 DMDSC 节点应该被踢出集群。VOTE 磁盘还用来传递命令，在集群的不同状态（启动、节点故障、节点重加入等）DMCSS 通过 VOTE DISK 传递控制命令，通知节点执行相应命令。VOTE DISK 必须存储在集群中所有节点都可以访问到的共享存储中，并且只支持保存在 DMASM 文件系统管辖范围之外的共享存储上。在一个集群环境中只能配置一个 VOTE 磁盘。

集群中各实例启动时，通过访问 DCR DISK 获取集群配置信息。被监控实例从 VOTE DISK 读取监控命令，并向 VOTE DISK 写入命令响应以及自身心跳信息；DMCSS 也向 VOTE DISK 写入自己的心跳信息，并从 VOTE DISK 访问各被监控节点的运行情况，并将监控命令写入 VOTE DISK，供被监控实例访问执行。

DMASM 文件系统初始化工具（DMASMCMD）

专门用于 DMASM 非镜像环境。将在 [10.6 DMASM 主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html#10.6%20DMASM%20%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

DMASM 服务器（DMASMSVR）

专门用于 DMASM 非镜像环境。将在 [10.6 DMASM 主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html#10.6%20DMASM%20%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

DMASM 应用程序访问接口（DMASMAPI）

专门用于 DMASM 非镜像环境。将在 [10.6 DMASM 主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html#10.6%20DMASM%20%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

DMASM 管理工具（DMASMTOOL）

专门用于 DMASM 非镜像环境。将在 [10.6 DMASM 主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html#10.6%20DMASM%20%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

### 10.3.3   DMASM 和达梦数据库文件系统对照表

DMASM 的实现主要参考了达梦数据库文件系统，因此，一些概念和实现原理与达梦数据库基本类似，熟悉达梦数据库的用户，就会更加容易理解 DMASM。

表 10.2 DMASM/达梦文件系统概念对照表

|**DMASM**|**达梦文件系统**|
|------|------|
|磁盘组（DISK GROUP）|表空间（TABLESPACE）|
|ASM 磁盘（DISK）|数据文件（DATAFILE）|
|ASM 文件（FILE）|段（SEGMENT）|
|簇（EXTENT）|簇（EXTENT）|
|AU（ALLOCATION UNITS）|页（PAGE）|
|描述 AU（DESC AU）|描述页（DESC PAGE）|
|INODE AU（INODE AU）|INODE 页（INODE PAGE）|


## 10.4 DMASM 关键技术

为了帮助用户更好的理解、使用 DMASM，本节从 ASM 磁盘与文件管理、DMASM REDO 日志、簇映射表等方面介绍 DMASM 原理。

### 10.4.1   ASM 磁盘与文件管理

DMASMCMD 将物理磁盘格式化后，变成可识别、可管理的 ASM 磁盘，再通过 ASM 磁盘组将一个或者多个 ASM 磁盘整合成一个整体提供文件服务。

![图 10.2 ASM 磁盘组结构.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112152757S5LRIUJZ1FI95ICWWN)

图10.2 ASM磁盘组结构

ASM 磁盘格式化以后，会逻辑划分为若干簇（Extent），簇是管理 ASM 磁盘的基本单位，ASM 文件的最小分配单位也是簇。

这些逻辑划分的簇根据其用途可以分为 DESC 描述簇、INODE 簇和 DATA 数据簇。

描述簇由多个描述项组成。每一个描述项存储一个 INODE 簇或一个数据簇的元数据。元数据包括簇所属文件 ID，簇的前一位簇，簇的后一位簇等。

INODE 簇由多个 INODE 项组成。每一个 INODE 项存放一个 ASM 文件的元数据。元数据包括文件完整路径、大小、创建时间等信息。

数据簇用于存储用户数据。

![图 10.3 ASM 磁盘逻辑结构.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112152825DMM2E6K8RAYMA05PKO)

图10.3 ASM磁盘逻辑结构

创建、删除 ASM 文件操作，在 DMASM 系统内部会转换成修改、维护 INODE AU 的具体动作。而扫描全局的 INODE AU 链表就可以获取到磁盘组上所有的 ASM 文件信息。

### 10.4.2 DMASM REDO 日志

DMASM 采用重做日志机制保证在各种异常（比如系统掉电重启）情况下数据不被损坏。创建、删除 ASM 文件等 DDL 操作过程中，所有针对 DMASM 描述 AU、INODE AU 的修改，都会生成 REDO 日志，并且在描述 AU、INODE AU 的修改写入磁盘之前，必须确保 REDO 日志已经写入磁盘。DMASM 中，只针对描述 AU 和 INODE AU 的修改产生 REDO 日志，用户修改数据 AU 的动作并不会产生 REDO 日志。

DMASM 所有 DDL 操作（创建文件、删除文件、增加磁盘等）都是串行执行的，并且在操作完成之前，会确保所有修改的描述项、INODE 项写入磁盘；一旦 DDL 操作完成，所有 REDO 日志就可以被覆盖了。

DDL 操作过程中出现异常时，如果 REDO 日志尚未写入磁盘，则当前操作对系统没有任何影响；如果 REDO 日志已经写入磁盘，那么重新启动后，系统会重演 REDO 日志，修改描述 AU 和 INODE AU，将此 DDL 继续完成。

### 10.4.3 簇映射表

创建 ASM 文件后，用户操作 ASM 文件的一般流程是：调用 ASM 文件的 OPEN、READ、WRITE 接口，打开 ASM 文件并获取一个句柄，再使用这个句柄从文件的指定偏移读取数据、或者写入数据。用户在使用 DMASM 的过程中，只需要获取一个 ASM 文件句柄，并不需要知道数据最终保存在物理磁盘的什么位置。

DMASM 使用簇映射表机制维护 ASM 文件与物理磁盘地址的映射关系，访问 ASM 文件时，根据文件号、文件偏移等信息，通过簇映射表可以快速获取到物理磁盘地址。

由于 DMASM 并不缓存任何用户数据，与直接读、写块设备相比，ASM 文件的读、写操作仅仅增加了簇映射的代价，而这个代价与 IO 代价相比几乎可以忽略，因此，使用 DMASM 并不会引起读、写性能的降低。

![图 10.4 DMASM 数据访问.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202301121528530R4IONKBWDKLDUNX2D)

图10.4 DMASM数据访问

## 10.5 DMASM 技术指标

本节主要介绍一下 DMASM 中的一些重要技术指标。

表10.3 DMASM技术指标

|**项**|**大小**|**说明**|
|--|--|--|
|AU 大小|1024* 1024|一个 AU 占用 1MB 存储空间|
|簇大小|4|一个簇包含 4 个物理上连续的 AU|
|描述项大小|32|一个簇描述项占用 32 个字节的存储空间|
|描述 AU 管理的最大簇数目|16* 1024|一个描述 AU 最多管理 16384 个簇|
|描述 AU 管理的最大 AU 数|64* 1024|一个描述 AU 最多可以管理 65536 个 AU|
|描述 AU 管理的最大磁盘空间|64GB|一个描述 AU 最多可以管理 64GB 磁盘空间|
|Inode 项大小|512|一个 ASM 文件描述项大小，每个 ASM 文件/目录都对应着一个文件描述项目|
|Inode AU 可管理的最大文件数|2046|一个 Inode AU 最多可以管理 2046 个文件|
|ASM 文件最小尺寸|4MB|每个 ASM 文件最少包含一个簇|
|ASM 文件最大尺寸|4PB|一个 ASM 文件最多可以包含 4294967295 个 AU，每个 AU 是 1MB，理论上单个 ASM 文件的最大尺寸是 4PB|
|一个用户连接可同时打开的 ASM 文件数|65536|一个用户连接，最多打开 65536 个 ASM 文件|
|ASM 文件数上限|8388607|一个磁盘组，最多可以创建 8388607 个 ASM 文件|
|磁盘组个数上限|124|最多可创建 124 个磁盘组|
|每 10M 共享内存大小能管理的磁盘大小|约 440G|每个簇描述项大概占用 64byte 内存空间，每个簇描述项对应 4MB 磁盘空间，共享内存还有部分内存用来保持必要的控制信息。440GB 左右的磁盘，需要 10MB 大小的共享内存能保证使用过程中簇描述项不被淘汰|


## 10.6 DMASM 主要部件

### 10.6.1   DMASMCMD

DMASMCMD 是 DMASM 文件系统初始化工具，用来将块设备格式化为 ASM 磁盘，并初始化 DCR DISK、VOTE DISK。格式化 ASM 磁盘就是在块设备的头部写入 ASM 磁盘特征描述符号，包括 DMASM 标识串、ASM 磁盘名、以及 ASM 磁盘大小等信息。其中 VOTE DISK 和 DCR DISK 也会被格式化为 ASM 磁盘。

DMASMCMD 工具的主要功能包括：

  1. 通过 HELP 指令查看帮助信息。
  2. 创建空文件模拟快设备。
  3. 创建用于模拟块设备的磁盘文件（用于单机模拟 DMDSC 环境）。
  4. 初始化 DCR DISK，同时指定密码。
  5. 初始化 VOTE DISK。
  6. 导出、导入 DCR DISK 配置信息。
  7. 联机修改 DCR 磁盘，增加节点。
  8. 校验 DCR 磁盘。
  9. 清理 DCR DISK 中指定组的故障节点信息。
  10. 列出指定路径下面磁盘属性。



#### 10.6.1.1 使用脚本文件

##### 10.6.1.1.1 查看 DMASMCMD HELP

通过 DMASMCMD HELP 可查看 DMASMCMD 工具的用法。

```
格式: dmasmcmd KEYWORD=value
例如: dmasmcmd SCRIPT_FILE=asmcmd.txt
关键字             说明（默认值）
----------------------------------------------------------------------------
SCRIPT_FILE      asmcmd脚本文件路径
RET_FLAG          执行asmcmd脚本文件时，出错是否立即返回(0)
DFS_INI           dmdfs.ini文件路径
HELP               打印帮助信息
```

参数说明：

SCRIPT_FILE：用于指定 ASMCMD 脚本文件绝对路径。脚本文件（例如 asmcmd.txt）集中存放了 DMASMCMD 用到的命令。

  * 用户没有通过 SCRIPT_FILE 指定脚本文件，则 DMASMCMD 进入交互模式运行，逐条解析、运行命令。
  * 用户通过 SCRIPT_FILE 指定脚本文件（比如 asmcmd.txt），则以行为单位读取文件内容，并依次执行，执行完成以后，自动退出 DMASMCMD 工具。脚本文件必须以“#asm script file”开头，否则认为是无效脚本文件；脚本中其它行以“#”开头表示注释；脚本文件大小不超过 1MB。



RET_FLAG：用于设置执行 ASMCMD 脚本文件时，出错是否立即返回。取值 0 或 1，0 不返回，1 返回。缺省值为 0。

##### 10.6.1.1.1 脚本文件用法

通过 SCRIPT_FILE 参数来指定脚本文件。

语法如下：

dmasmcmd SCRIPT_FILE=filepath [RET_FLAG=value]

参数说明：

SCRIPT_FILE：用于指定 asmcmd 脚本文件路径。脚本文件（例如 asmcmd.txt）中存放一条或多条 DMASMCMD 命令。用户没有通过 SCRIPT_FILE 指定脚本文件，则 DMASMCMD 进入交互模式运行，逐条解析、运行命令。用户通过 SCRIPT_FILE 指定脚本文件，则以行为单位读取文件内容，并依次执行，执行完成后，自动退出 DMASMCMD 工具。脚本文件必须以”#asm script file”开头，否则认为是无效脚本文件；脚本中其他行以“#”开头表示注释。

RET_FLAG：用于设置执行 ASMCMD 脚本文件时，出错是否立即返回。取值 0 或 1。0 不返回，即执行出错时忽略当前出错的行，继续执行下一条；1 返回，即执行出错时立即停止，并返回一个错误码。缺省值为 0。

举例说明：

第一步，创建脚本文件 asmcmd.txt。例如脚本文件内容如下：

```
#asm script file
create dcrdisk '/home/test/dameng/asmdisks/disk1.asm' 'dcr1'
create votedisk '/home/test/dameng/asmdisks/disk2.asm' 'vote'
create asmdisk '/home/test/dameng/asmdisks/disk3.asm' 'data1'
create asmdisk '/home/test/dameng/asmdisks/disk4.asm' 'data2'
```

第二步，通过使用 SCRIPT_FILE 参数指定脚本，执行出错立即停止并返回一个错误码。

```
./dmasmcmd SCRIPT_FILE=/home/test/dameng/asmcmd.txt RET_FLAG=1
```

执行结果如下：

```
dmasmcmd V8
ASM>create dcrdisk '/home/test/dameng/asmdisks/disk1.asm' 'dcr1'
[TRACE]The ASM initialize dcrdisk /home/test/dameng/asmdisks/disk1.asm to name DMASMdcr1
Used time: 5.384(ms).
ASM>create votedisk '/home/test/dameng/asmdisks/disk2.asm' 'vote'
[TRACE]The ASM initialize dcrdisk /home/test/dameng/asmdisks/disk2.asm to name DMASMvote
Used time: 19.871(ms).
ASM>create asmdisk '/home/test/dameng/asmdisks/disk3.asm'  'data1'
[TRACE]The ASM initialize asmdisk /home/test/dameng/asmdisks/disk3.asm to name DMASMdata1
Used time: 6.754(ms).
ASM>create asmdisk '/home/test/dameng/asmdisks/disk4.asm'  'data2'
[TRACE]The ASM initialize asmdisk /home/test/dameng/asmdisks/disk4.asm to name DMASMdata2
Used time: 10.430(ms).
```

#### 10.6.1.2  DMASMCMD 命令用法

##### 10.6.1.2.1  查看工具的 HELP 指令

在 DMASMCMDM 工具中输入 HELP，可查看 DMASMCMDM 工具如何使用 DMASMCMDM 命令。

```
ASM>help
Format: create emptyfile file_path size(M) num
Usage: create emptyfile '/dev_data/asmdisks/disk0.asm' size 100
 
Format: create asmdisk disk_path disk_name [size(M)]
Usage: create asmdisk '/dev_data/asmdisks/disk0.asm' 'DATA0'
Usage: create asmdisk '/dev_data/asmdisks/disk0.asm' 'DATA0' 100
 
Format: create dcrdisk disk_path disk_name [size(M)]
Usage: create dcrdisk '/dev_data/asmdisks/disk0.asm' 'DATA0'
Usage: create dcrdisk '/dev_data/asmdisks/disk0.asm' 'DATA0' 100
 
Format: create votedisk disk_path disk_name [size(M)]
Usage: create votedisk '/dev_data/asmdisks/disk0.asm' 'DATA0'
Usage: create votedisk '/dev_data/asmdisks/disk0.asm' 'DATA0' 100
 
Format: init dcrdisk disk_path from ini_path identified by password
Usage: init dcrdisk '/dev_data/asmdisks/disk0.asm' from '/data/dmdcr_cfg.ini' identified by 'aaabbb'
 
Format: init votedisk disk_path from ini_path
Usage: init votedisk '/dev_data/asmdisks/disk0.asm' from '/data/dmdcr_cfg.ini'
 
Format: export dcrdisk disk_path to ini_path
Usage: export dcrdisk '/dev_data/asmdisks/disk0.asm' to '/data/dmdcr_cfg.ini'
 
Format: import dcrdisk ini_path to disk_path
Usage: import dcrdisk '/data/dmdcr_cfg.ini' to '/dev_data/asmdisks/disk0.asm'
 
Format: extend dcrdisk disk_path from ini_path
Usage: extend dcrdisk '/dev_data/asmdisks/disk0.asm' from '/data/dmdcr_cfg.ini'

Format: check dcrdisk disk_path
Usage: check dcrdisk '/dev_data/asmdisks/disk0.asm'

Format: clear dcrdisk err_ep_arr disk_path group_name
Usage: clear dcrdisk err_ep_arr '/dev_data/asmdisks/disk0.asm' 'GRP_RAC'

Format: listdisks path
Usage: listdisks '/dev_data/asmdisks/'
```

##### 10.6.1.2.2  创建空文件模拟块设备

创建名为 xxx.asm 的模拟块设备，注意必须以.asm 结尾。模拟测试用，真实环境不建议使用。块设备作为普通数据磁盘时，size 最小值为 64MB；块设备作为 DCR 或 VOTE 磁盘时，size 最小值为 32MB，无上限限制。

语法格式：

```
CREATE EMPTYFILE file_path SIZE num
```

举例说明：

创建名为 DISK0.asm 的模拟块设备。

```
CREATE EMPTYFILE '/OPT/DATA/ASMDISKS/DISK0.asm' SIZE 100
```

##### 10.6.1.2.3  创建磁盘文件

  * **创建 ASM 磁盘**



用来将块设备格式化为 ASM DISK，会在块设备头部写入 ASM DISK 标识信息。size 取值最小 64。

语法格式：

```
CREATE ASMDISK disk_path disk_name [size]
```

举例说明：

```
CREATE ASMDISK '/HOME/ASMDISKS/DISK0.ASM' 'DATA0'
CREATE ASMDISK '/HOME/ASMDISKS/DISK0.ASM' 'DATA0' 100
```

  * **创建 DCR 磁盘**



用来将块设备格式化为 DCR 磁盘，会在块设备头部写入 DCR 标识信息。size 取值最小 32。

语法格式：

```
CREATE DCRDISK disk_path disk_name [size]
```

举例说明：

```
CREATE DCRDISK '/HOME/ASMDISKS/DISK0.ASM' 'DATA0'
CREATE DCRDISK '/HOME/ASMDISKS/DISK0.ASM' 'DATA0' 100
```

  * **创建 VOTE 磁盘**



用来将块设备格式化为 VOTE DISK，会在块设备头部写入 VOTE DISK 标识信息。size 取值最小 32。

语法格式：

```
CREATE VOTEDISK disk_path disk_name [size]
```

举例说明：

```
CREATE VOTEDISK '/HOME/ASMDISKS/DISK0.ASM' 'DATA0'
CREATE VOTEDISK '/HOME/ASMDISKS/DISK0.ASM' 'DATA0' 100
```

以上三个命令中 size 参数可以省略，程序会计算 disk_path 的大小；但是某些操作系统计算 disk_path 大小会失败，这时候还是需要用户指定 size 信息。

##### 10.6.1.2.4  初始化 DCR DISK & VOTE DISK

  * **初始化 DCR DISK**



根据配置文件 DMDCR_CFG.INI 的内容，初始化 DCR 磁盘。设置登录 ASM 文件系统的密码，密码要用单引号括起来。

语法格式：

```
INIT DCRDISK disk_path FROM ini_path IDENTIFIED BY password
```

举例说明：

```
INIT DCRDISK '/DEV/RAW/RAW2' FROM '/HOME/ASM/DMDCR_CFG.INI' IDENTIFIED BY 'AAAAAA'
```

  * **初始化 VOTE DISK**



根据配置文件 DMDCR_CFG.INI 的内容，初始化 VOTE DISK。

语法格式：

```
INIT VOTEDISK disk_path FROM ini_path
```

举例说明：

```
INIT VOTEDISK '/DEV/RAW/RAW3' FROM '/HOME/ASM/DMDCR_CFG.INI'
```

> **注意**
>
>
> **1.DMASM要求同一目录下只能有一套ASM文件系统，为了避免操作失误违反这一要求，不允许在已有DCR（VOTE）磁盘的目录下新初始化其它的DCR（VOTE）磁盘。  
>
> 2.重新初始化DCR（VOTE）磁盘时，需要将原DCR（VOTE）磁盘重新初始化为新DCR（VOTE）磁盘，或者先将原DCR（VOTE）磁盘格式化再选择其它磁盘初始化为DCR（VOTE）磁盘，否则会由于违反1.的要求报错。
> **



##### 10.6.1.2.5  导出 DCR 的配置文件

解析 DCR 磁盘内容，导出到 DMDCR_CFG.INI 文件。

语法格式：

```
EXPORT DCRDISK disk_path TO ini_path
```

举例说明：

```
EXPORT DCRDISK '/DEV/RAW/RAW2' TO '/HOME/ASM/DMDCR_CFG.INI' 
```

##### 10.6.1.2.6  导入 DCR 的配置文件

将修改后的 DMDCR_CFG.INI 文件导入到 DCR 磁盘。

必须以组为单位进行修改，其中 DCR_VTD_PATH 、DCR_N_GRP、DCR_GRP_N_EP、DCR_GRP_NAME、DCR_GRP_TYPE、DCR_GRP_EP_ARR、DCR_EP_NAME、EP 所属的组类型、CHECKSUM 值、密码不可修改。特别的，在动态增加节点时，可以并需要对 DCR_GRP_N_EP 和 DCR_GRP_EP_ARR 进行修改。

语法格式：

```
IMPORT DCRDISK ini_path TO disk_path
```

举例说明：

```
IMPORT DCRDISK '/DATA/DMDCR_CFG.INI' TO '/DATA/ASMDISKS/DISK0.asm' 
```

##### 10.6.1.2.7  联机修改 DCR 磁盘，增加节点

联机修改 DCR 磁盘，增加节点，会将新增节点信息写回 dcr 磁盘。

语法格式：

```
EXTEND DCRDISK disk_path FROM ini_path
```

举例说明：

```
EXTEND DCRDISK 'D:\ASMDISKS\DISK0.asm' FROM 'D:\DMDCR_CFG.INI'
```

##### 10.6.1.2.8  校验 DCR 磁盘

校验 DCR 磁盘信息是否正常。打印“ASMCMD check DCRDISK success”，表示 DCR 磁盘正常；打印“ASMCMD check DCRDISK failed……”，需要根据打印的信息进一步判断。如果 DCR 磁盘故障，需要重新初始化。

语法格式：

```
CHECK DCRDISK disk_path
```

举例说明：

```
CHECK DCRDISK '/DEV/RAW/RAW2'
```

##### 10.6.1.2.9  清理指定组的故障节点信息

清理 DCR DISK 中指定组的故障节点信息，可借助 export 命令查看对应组的 DCR_GRP_N_ERR_EP 和 DCR_GRP_ERR_EP_ARR 信息，清理成功后，指定组的 DCR_GRP_N_ERR_EP 值为 0，DCR_GRP_ERR_EP_ARR 内容为空。

语法格式：

```
CLEAR DCRDISK err_ep_arr disk_path group_name
```

举例说明：

```
CLEAR DCRDISK ERR_EP_ARR '/DEV/RAW/RAW2' 'GRP_DSC'
```

##### 10.6.1.2.10 显示指定路径下面磁盘属性

显示 path 路径下面所有磁盘的信息，分为三种类型：NORMAL DISK：普通磁盘；UNUSED ASMDISK：初始化未使用的 ASMDISK；USED ASMDISK：已经使用的 ASMDISK。

语法格式：

```
LISTDISKS path{,path}
```

举例说明：

```
LISTDISKS '/DEV/RAW/'
LISTDISKS '/DEV/RAW0/,/DEV/RAW1/,/DEV/RAW2/'
```

限制：

支持指定多个路径，显示多路径下的磁盘信息，各路径以英文逗号','进行分隔，所有配置的路径都必须为有效路径，最多可以同时配置 8 个路径，且不支持中文路径。

如果查询模拟 ASM 磁盘的磁盘信息，则对 path 路径没有限制；如果查询真实环境中共享存储路径下的磁盘信息，则 path 路径的根目录名称必须以“dev”开头，例如：listdisks '/dev/DMDATA/' 或 listdisks '/dev_data/DMDATA/'，否则将不会显示该路径下的磁盘信息。

### 10.6.2 DMASMSVR

DMASMSVR 是提供 DMASM 服务的主要载体，每个提供 DMASM 服务的节点都必须启动一个 DMASMSVR 服务器，这些 DMASMSVR 一起组成共享文件集群系统，提供共享文件的全局并发控制。DMASMSVR 启动时扫描/dev/raw/路径下的所有块设备，加载 ASM 磁盘，构建 ASM 磁盘组和 DMASM 文件系统。DMASMSVR 实例之间使用 MAL 系统进行信息和数据的传递。

DMASMSVR 集群的启动、关闭、故障处理等流程由 DMCSS 控制，DMASMSVR 定时向 VOTE DISK 写入时间戳、状态、命令、以及命令执行结果等信息，DMCSS 控制节点定时从 VOTE DISK 读取信息，检查 DMASMSVR 实例的状态变化，启动相应的处理流程。

DMASMSVR 集群中，只有一个控制节点，控制节点以外的其他节点叫做普通节点，DMASMSVR 控制节点由 DMCSS 选取；所有 DDL 操作（比如创建文件，创建磁盘组等）都是在控制节点执行，用户登录普通节点发起的 DDL 请求，会通过 MAL 系统发送到控制节点执行并返回；而 ASM 文件的读、写等操作，则由登录节点直接完成，不需要传递到控制节点执行。

通过 DMASMSVR HELP 可查看 DMASMSVR 的启动用法。

```
格式: dmasmsvr.exe KEYWORD=value
例如: dmasmsvr.exe DCR_INI=/home/data/DAMENG/dmdcr.ini
关键字说明（默认）
----------------------------------------------------------------
DCR_INI            dmdcr.ini路径
-NOCONSOLE         以服务方式启动
HELP                打印帮助信息
```

参数说明：

DCR_INI：DMDCR.INI 的路径。DMDCR.INI 配置文件记录了 DCR 磁盘路径、实例序列号等信息；如果不指定 DCR_INI 参数，DMASMSVR 默认在当前路径下查找 DMDCR.INI 文件。

> **说明**
>
>
> 使用DMASMTOOL或DMASMAPI函数dmasm_connect()登录DMASMSVR时，用户名只能为“ASMSYS”。若为本地登录，不校验密码；若为远程登录，应使用初始化DCR磁盘时设置的登录ASM文件系统的密码。



### 10.6.3 DMASMAPI

DMASMAPI 是 DMASM 文件系统的应用程序访问接口，通过调用 DMASMAPI 接口，用户可以访问、操作 ASM 文件。与达梦数据库接口 DPI 类似，访问 ASM 文件之前，必须先分配一个 conn 对象，并登录到 DMASMSVR 服务器，再使用这个 conn 对象进行创建磁盘组、创建文件、删除文件、读取数据和写入数据等 DMASM 相关操作。

DMASMAPI 接口的详细使用说明请参考 [19 附录](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-appendix.html)。

### 10.6.4 DMASMTOOL

DMASMTOOL 是 DMASM 文件系统管理工具，提供了一套类 Linux 文件操作命令，用于管理 ASM 文件，是管理、维护 DMASM 的好帮手。DMASMTOOL 工具使用 DMASMAPI 连接到 DMASMSVR，并调用相应的 DMASMAPI 函数，实现创建、拷贝、删除等各种文件操作命令；DMASMTOOL 还支持 ASM 文件和操作系统文件的相互拷贝。

DMASMTOOL 可以登录本地 DMASMSVR，也可以登录位于其他节点的 DMASMSVR，并执行各种文件操作命令。一般建议登录本地 DMASMSVR 服务器，避免文件操作过程中的网络开销，提升执行效率。

通过 DMASMTOOL HELP 可查看 DMASMTOOL 的启动方法。

```
格式: dmasmtool.exe KEYWORD=value
例如: dmasmtool.exe DCR_INI=/home/data/DAMENG/dmdcr.ini
关键字说明（默认）
----------------------------------------------------------------
DCR_INI            dmdcr.ini文件路径
HOST                asm服务器地址
PORT_NUM           asm服务器端口号
USERID              登录asm服务器用户名密码，（格式：USER/PWD）
SCRIPT_FILE       asmtool脚本文件路径
HELP                打印帮助信息
```

> **注意**
>
> 1） DMASMTOOL的最大命令长度是1024。  
>  2）
> 用户没有指定脚本文件，则DMASMTOOL进入交互模式运行，逐条解析、运行命令；用户指定脚本文件(比如asmtool.txt)，则以行为单位读取文件内容，并依次执行，执行完成以后，自动退出DMASMTOOL工具。脚本文件必须以"#asm
> script file"开头，否则认为是无效脚本文件；脚本中其他行以"#"开始表示注释。  
>  3）
> DMASMTOOL命令直接输入的host/port信息配置的是连接ASMSVR的信息，可以在DMDCR_CFG.INI里面找到，分别为要连接的ASM节点的DCR_EP_HOST和DCR_EP_PORT。



DMASMTOOL 支持的命令说明：

  1. **创建 ASM 磁盘组，添加 ASM 磁盘，删除 ASM 磁盘组**

创建磁盘组，或为磁盘组添加磁盘时，以下情况可能导致失败：

![](media/mark.png)DMASMSVR 服务器没有访问对应磁盘的权限；

![](media/mark.png)磁盘路径不在 DMDCR_CFG.INI 配置文件中配置的 DCR_EP_ASM_LOAD_PATH 路径下；

![](media/mark.png)磁盘大小不够，最少需要 32MB。



在磁盘组中添加 ASM 磁盘时，可能出现组中的 ASM 磁盘版本号不一致的情况，此时 ASM 磁盘自动向下兼容，即高版本 ASM 磁盘自动转换为低版本 ASM 磁盘。关于 ASM 磁盘版本的介绍请参考 [17.13 ASM 磁盘版本](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-instructions-use.html#17.13%20ASM%20%E7%A3%81%E7%9B%98%E7%89%88%E6%9C%AC)。

  * **创建 ASM 磁盘组**



语法格式：

```
CREATE DISKGROUP name ASMDISK file_path
```

参数说明：

name：磁盘组名，最长不能超过 32 字节。

file_path：磁盘组路径，必须是全路径，不能是相对路径。

举例说明：

```
CREATE DISKGROUP 'DMDATA' ASMDISK '/DEV/RAW/RAW3'
```

  * **添加磁盘**



语法格式：

```
ALTER DISKGROUP name ADD ASMDISK file_path
```

参数说明：

name：磁盘组名，最长不能超过 32 字节。

file_path：ASMDISK 路径。必须是全路径，不能是相对路径。

举例说明：

```
ALTER DISKGROUP 'DMDATA' ADD ASMDISK '/DEV/RAW/RAW4'
```

  * **删除 ASM 磁盘组**



语法格式：

```
DROP DISKGROUP name
```

参数说明：

name：磁盘组名。

举例说明：

```
DROP DISKGROUP 'DMDATA'
```

  2. **创建文件，扩展文件，截断文件，删除文件**



  * **创建文件**



语法格式：

```
CREATE ASMFILE file_path SIZE num
```

参数说明：

file_path：ASM 文件路径。必须是全路径，不能是相对路径。

num：ASM 文件大小，单位 M。取值范围：0~2^24 个 AU，需转换为单位 M。

举例说明：

```
CREATE ASMFILE '+DMDATA/SAMPLE.DTA' SIZE 20
```

  * **扩展文件**



语法格式：

```
ALTER ASMFILE file_path EXTEND TO size
```

参数说明：

file_path：ASM 文件路径。必须是全路径，不能是相对路径。

size：扩展到指定大小，单位 M。取值范围：0~2^24 个 AU，需转换为单位 M。

举例说明：

```
ALTER ASMFILE '+DMDATA/SAMPLE.DTA' EXTEND TO 20
```

  * **截断文件**



语法格式：

```
ALTER ASMFILE file_path TRUNCATE TO size
```

参数说明：

file_path：ASM 文件路径。必须是全路径，不能是相对路径。

size：截断到指定大小，单位 M。取值范围：0~224 个 AU，需转换为单位 M。

举例说明：

```
ALTER ASMFILE '+DMDATA/SAMPLE.DTA' TRUNCATE TO 20
```

  * **删除文件**



删除 ASM 文件。已经 open、正在访问的 DMASM 文件不允许删除。

语法格式：

```
DELETE ASMFILE file_path
```

参数说明：

file_path：ASM 文件路径。必须是全路径，不能是相对路径。

举例说明：

```
DELETE ASMFILE '+DMDATA/SAMPLE.DTA'
```

  * **重定向输出文件**



重定向输出文件。如果多次重定向文件，第一次成功打开重定向文件之后，如果未关闭，则不再打开其他重定向文件。

语法格式：

```
SPOOL file_path [CREATE|REPLACE|APPEND]
```

参数说明：

file_path：输出文件路径。

CREATE：如果重定向文件不存在，则创建；如果存在，创建失败。

REPLACE：如果重定向文件不存在，则创建；如果存在，则替换掉。缺省为 REPLACE。

APPEND：如果重定向文件不存在，则创建；如果存在，则追加到文件末尾。

举例说明：

```
SPOOL /HOME/DATASPOOL.TXT
```

  * **关闭重定向文件**



语法格式：

```
SPOOL OFF
```

举例说明：

```
SPOOL OFF
```

  3. **兼容 LINUX 一些命令，功能受限，但是很实用**



  * **到达某目录**



语法格式：

```
CD [path]
```

参数说明：

path：目标路径。

举例说明：

```
CD +DMDATA/TEST
```

  * **拷贝**



语法格式：

```
CP [-RF] <src_file_path> <dst_file_path >
或
CP [-RF] <src_dir_path> < dst_dir_path>
```

参数说明：

-R：递归拷贝，将指定目录下的子文件和子目录一并拷贝。

-F：执行拷贝，不给出提示。

src_file_path：源文件路径。必须是全路径，不能是相对路径。

dst_file_path：目标文件路径。必须是全路径，不能是相对路径。

src_dir_path：源文件所在磁盘路径。

dst_file_path：目标文件所在磁盘路径。

举例说明：

```
CP '+DMDATA/AA/SAMPLE.DTA' '+DMDATA/A/B.DTA'
CP -R '+DMDATA/AA' '+DMDATA/BB'
CP –F '+DMDATA/AA/SAMPLE.DTA' '+DMDATA/A/B.DTA'
```

  * **删除**



语法格式：

```
RM [-RF] <file_path|dir_path>
```

参数说明：

-R：递归删除，将指定目录下的子文件和子目录一并删除。

-F：执行删除，不给出提示。

file_path：文件路径。必须是全路径，不能是相对路径。

dir_path：磁盘路径。

举例说明：

```
RM '+DMDATA/A/SAMPLE.DTA'
RM -R '+DMDATA/A/'
RM –F '+DMDATA/B/'
```

  * **创建目录**



语法格式：

```
MKDIR [-P] dir_path
```

参数说明：

-P：自动创建不存在的中间目录。

dir_path：磁盘路径。

举例说明：

```
MKDIR '+DMDATA/A'
MKDIR -P '+DMDATA/NODIR/BB'
```

  * **查找**



语法格式：

```
FIND path <file_name>
```

参数说明：

path：磁盘路径。

file_name：文件名。

举例说明：

```
FIND +DMDATA/A 'SAMPLE.DTA'
```

  * **显示**



语法格式：

```
LS [-LR] <file_name|dir_name>
```

参数说明：

-L：显示文件详细信息。

-R：递归显示。

file_name：文件名。

dir_name：磁盘名。

举例说明：

```
LS '+DMDATA/A'
LS -L '+DMDATA/A'
LS –R '+DMDATA/A'
```

  * **显示存储信息**



语法格式：

```
DF
```

举例说明：

```
DF
```

  * **当前目录**



语法格式：

```
PWD
```

举例说明：

```
PWD
```

  * **估算文件空间已使用情况**



语法格式：

```
DU [dir_path{,dir_path}]
```

参数说明：

dir_path：要估算的文件目录，未指定则默认为当前目录。

举例说明：

```
du
du DMDATA
du DMDATA,DMLOG
du DMDATA DMLOG
```

估算结果中，出现的单位有字节（缺省）、K、M 和 G 等情况。

```
ASM>du
5.3G     +DATA/data/rac00/arch
5.3G     +DATA/data/rac00
586.6M   +DATA/data/rac01/arch
586.6M   +DATA/data/rac01
0        +DATA/data/rac/bak
631.5K   +DATA/data/rac/ctl_bak
0     	+DATA/data/rac/HMAIN
1012.6M  +DATA/data/rac
6.8G     +DATA/data
6.8G     +DATA
```

  4. **DMASM 特有的一些命令**



  * **列出所有的磁盘组**



语法格式：

```
LSDG
```

举例说明：

```
LSDG
```

  * **列出所有的 ASM 磁盘**



语法格式：

```
LSDSK
```

举例说明：

```
 LSDSK
```

  * **列出文件的详细信息**



语法格式：

```
LSATTR
```

举例说明：

```
LSATTR
```

  * **列出所有的信息，包括文件等**



语法格式：

```
LSALL
```

举例说明：

```
LSALL
```

  * **修改密码**



语法格式：

```
PASSWORD
```

举例说明：

```
PASSWORD
```

  * **登录，在断开连接后，重新登录**



语法格式：

```
LOGIN
```

举例说明：

```
LOGIN
```

  * **测试 ASM 环境下磁盘的读写速度，包括随机读/写速度和顺序读/写速度**



语法格式：

```
IOTEST [DISKGROUP=<diskgroup_name>] [ASMFILE=<file_name>] [SIZE=<file_size>]
```

参数说明：

\<diskgroup_name>：磁盘组名。若指定则在指定磁盘组下生成临时文件，若不指定则在当前路径下生成临时文件，如果当前路径为“+”目录，则默认在 0 号 group 根目录下生成临时文件。

\<file_name>：指定生成的临时文件名称，若不指定则系统自动生成临时文件名。

\<file_size> ：指定临时文件大小，取值范围 0~2147483647，单位 MB。若不指定则默认临时文件大小为 1024MB。

使用说明：

DM 系统文件 IO 接口封装了 Windows 的 ReadFile/WriteFile 函数和 Linux 的 pread/pwrite 函数，DM 系统 IO 线程从系统 IO 任务链表中获取 IO 任务后调用 DM 系统文件 IO 接口读取指定文件相关内容。

Iotest 命令属于 ASM 文件系统，ASM 文件系统的存储最小单位为扇区，ASM 文件读写接口读写的字节数必须是扇区(512 字节)的倍数，如果 ASM 文件系统和 ASM 应用部署在同一台机器上，则使用本地读写接口，如果在不同的机器上，则使用远程读写接口。ASM 本地读写接口设置好 IO 任务参数后将 IO 任务加入 DM 系统 IO 任务链表，等待 DM 系统 IO 线程执行完该 IO 任务后回调唤醒 ASM 本地读写接口线程，完成文件读写操作。ASM 远程读写接口使用 MAL 链路网络通信，通知 ASM 文件系统向 DM 系统 IO 任务链表添加对应的文件 IO 任务，待文件 IO 完成后发送完成信件给 IO 调用节点，唤醒 IO 调用节点的对应线程，完成文件读写操作。

测试随机读写和顺序读写的方式：

随机读写的文件读写偏移随机为文件大小之内的当前读写单位大小的倍数，即如果文件大小为 1G，当前读写单位大小为 8M，则会进行 1G/8M=128 次文件读写，读写偏移为 1G 内的 8M 倍数，如 8M、16M 等。顺序读写的文件读写偏移则从 0 开始以当前读写单位顺序增长到文件大小，即如果文件大小为 1G，当前读写单位大小为 8M，则会进行 1G/8M=128 次文件读写，读写偏移为 0，8M，16M 直到 1016M。

Iotest 命令以单线程执行，并且不会走服务器缓存。

现有 ASM 系统只能测试磁盘组的文件读写速度，如果一个磁盘组由多个磁盘组成，则只能测试正在使用的未满磁盘的文件读写速度。预期磁盘组的 AU_SIZE 越小，文件读取速度越快。

执行 iotest 命令时，会在指定路径或当前路径下创建一个后缀名为.iotest 的临时文件，测试完成后自动删除该临时文件。出现以下情况时将报错：

若磁盘空间不足，则报错；
若临时文件生成路径下已经存在同名文件，则报错，需要手动删除同名文件后才能继续执行 iotest 命令。

举例说明：

```
IOTEST DISKGROUP=DMDATA SIZE=128
```

> **注意**
>
> 1）
> DMASMTOOL工具的上下键，查找历史记录，以及TAB键的自动补齐功能是基于readline实现的，由于readline输入不支持中文，因此目前DMASMTOOL工具不支持中文输入。  
>  2） 若文件名或路径中包含中文字符，当客户端与服务器的编码不一致时，可能出现乱码，因此建议不要使用包含中文字符的文件名或路径。  
>  3） 在DMASMTOOL工具执行命令时，如果路径名或者文件名首字母为数字，需要给字符串加单引号'。  
>  4） 在DMASMTOOL工具使用cd命令，首字母为'+', '\', '/'符号都会从目录最上层开始查找目录。  
>  5） ASM文件全路径长度不能超过256字节。



  * **文件校验**



检查 ASM 文件各个 AU 分布情况。

语法格式：

```
CHECK [file_path]
```

参数说明：

file_path：ASM 文件路径。

举例说明：

```
CHECK
或
CHECK +DMDATA/SAMPLE.DTA
```

## 10.7 DMASM 使用说明

DMASM 的配置和使用大致分为三步。

第一步 环境准备

  * 硬件



准备多台部署 DMDSC 集群的机器。机器数和集群节点数一致。

  * 存储设备



准备共享磁盘。用户根据实际数据量来准备共享磁盘的数量。至少一块。

  * 操作系统



为所有机器配置相同的操作系统。支持 Windows 和 Linux 系统。用户根据实际需求选择操作系统。

  * 网络配置



每台机器至少配置两块网卡。一块用于配置内网网段，一块用于配置外网网段。

  * 用户权限准备



如果用户选择 Linux 操作系统，那么需要在每台机器上使用 root 用户创建用于搭建环境的用户和组。

  * 目录规划



为每台机器各创建三个目录。

目录一 使用 DMDBA 用户创建用于镜像环境搭建的目录，不同机器上此目录必须相同。

目录二 DM 执行码和工具存放于目录，不同机器上此目录必须相同。

目录三 配置文件存放于目录，不同的机器上此目录可以不同。

  * 磁盘准备



首先使用多路径软件为共享存储的磁盘创建目录名，即将磁盘链接到一个目录中。然后将磁盘的用户权限修改为和目录的用户权限一致。

第二步 搭建 DMASM

搭建按照下面顺序依次进行：

  1. 使用 DMASMCMD 工具创建所有磁盘。具体为 DCR 磁盘、VOTE 磁盘和 ASM 磁盘，其中 ASM 磁盘用于存放数据(DATA)和日志(LOG)。
  2. 配置并启动 DMCSS。
  3. 配置并启动 DMASMSVR。
  4. 使用 DMASMTOOL 工具创建 ASMDISK 磁盘组。具体为 DATA 磁盘组和 LOG 磁盘组两种，分别管理 DATA 磁盘和 LOG 磁盘。
  5. 配置并启动 DMSERVER。
  6. 配置并启动 DMCSSM。



至此，一个使用了 DMASM 镜像的 DMDSC 部署完成。

第三步 正常使用数据库

用户登录到任意一个 DM 实例，即可获得完整的达梦数据库服务。同时，用户可通过两种方式来管理 ASM 文件：一是编写用户代码，通过调用 DMASMAPI 接口来管理 ASM 文件；二是使用 DMASMCMD 和 DMASMTOOL 工具来管理 ASM 文件。

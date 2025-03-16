

# DMASM 镜像介绍

## 11.1 DMASM 镜像概述

共享存储上的数据非常宝贵，为了保障这些数据的安全性和高可用性，达梦提供了 DMASM 镜像功能。镜像是 DMASM 的一个重要功能。

DMASM 镜像提供了多副本和条带化功能。多副本技术保证同一数据的多个副本会分别写入到不同的磁盘中。多个副本中只有一个作为主副本对外提供服务，其余副本均作为镜像副本。当主副本发生故障后，系统会从镜像副本中重新自动挑选一个继续提供服务。条带化技术可保证写入的数据均匀分布到磁盘组内的不同磁盘中，实现负载均衡。

DMDSC 采用配置镜像功能的 DMASM 管理的块设备作为共享存储，当出现磁盘损坏或数据丢失时，既可以利用其他镜像副本继续提供数据库服务，又可以使用其他镜像副本进行数据恢复。

下图为一个部署了 DMASM 镜像的 DMDSC 集群结构图。

![图 11.1 一个部署了 DMASM 镜像的 DMDSC 集群.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112153647OR3ZTTSRK25XV4OXGU)

图 11.1 一个部署了DMASM镜像的DMDSC集群

一个 DMDSC 系统中可能同时拥有多个磁盘组。一个磁盘组中完整的 DMASM 镜像结构包括副本、磁盘和故障组。副本数由用户指定。故障组专门用来存放 ASM 文件的不同镜像副本。一个磁盘组中包含多个故障组，故障组是磁盘组的子集。下图以一个磁盘组中的二副本 DMASM 镜像为例，展示 DMASM 镜像的结构。

![图 11.2  一个磁盘组中的两副本 DMASM 镜像结构.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112153706KXCPOPL71X0N5YFJXY)

图11.2 一个磁盘组中的两副本DMASM镜像结构

## 11.2 DMASM 镜像术语和基本概念

### 11.2.1   DMASM 镜像术语

本章介绍 DMASM 镜像所用到的术语。术语的详细概念介绍请参考 [11.2.2DMASM 镜像基本概念](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-image-introduction.html#11.2.2%20DMASM%20%E9%95%9C%E5%83%8F%E5%9F%BA%E6%9C%AC%E6%A6%82%E5%BF%B5)。

表11.1 DMASM镜像术语中英文对照表

|**中文术语**|**英文术语**|
|----|----|
|磁盘|DISK|
|磁盘组|DISK GROUP|
|系统磁盘|DCRV DISK|
|系统磁盘组|DCRV DISK GROUPS|
|ASM 磁盘|ASM DISK|
|ASM 磁盘组|ASM DISK GROUP|
|数据分配单元|ALLOCATION UNITS，简称 AU|
|ASM 文件|ASMFILE|
|文件镜像|MIRRORING|
|故障组|FAILURE GROUPS|
|DMASM 文件系统初始化工具|DMASMCMDM（专门用于 DMASM 镜像环境）|
|DMASM 服务器|DMASMSVRM（专门用于 DMASM 镜像环境）|
|DMASM 应用程序访问接口|DMASMAPIM（专门用于 DMASM 镜像环境）|
|DMASM 管理工具|DMASMTOOLM（专门用于 DMASM 镜像环境）|


### 11.2.2   DMASM 镜像基本概念

磁盘（DISK）

磁盘用于存储 ASM 文件的数据。DMASM 镜像环境中用到的磁盘均为经过 DMASMCMDM 工具创建的磁盘，并非普通的裸盘。

DMASM 镜像系统的磁盘分为 DCRV 磁盘和 ASM 磁盘两种。DCRV 磁盘由 CREATE DCRVDISK……语法创建。ASM 磁盘由 CREATE ASMDISK……语法创建。

磁盘组（DISK GROUP）

DMASM 镜像使用磁盘组管理 ASM 文件；每个磁盘组可以包含多块磁盘。ASM 文件数据均匀存储在各个磁盘上，能充分利用多块磁盘 IO。

DMASM 镜像系统的磁盘组分为 DCRV 系统磁盘组和 ASM 磁盘组两种。

DCRV 磁盘组通过 CREATE SYSTEM DISKGROUP ASMDISK……语法创建。DCRV 磁盘组名称为 SYS，系统自动生成，不需要用户指定。

ASM 磁盘组通过 CREATE DISKGROUP diskgroup_name……语法创建。

![图 11.3 DMASM 镜像系统中磁盘组的结构.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112153729B6FAYSK8HV3DUQ01JA)

图11.3 DMASM镜像系统中磁盘组的结构

ASM 磁盘（ASM DISK）

ASM 磁盘是组成 ASM 磁盘组的基本组成单元。ASM 磁盘可以是一块完整的磁盘，也可以是磁盘的某一块分区。所有的 ASM 磁盘都需要在指定的搜索路径下面。

创建 ASM DISK 时可以为 ASM DISK 指定 ASM 磁盘名称。在操作系统层，同一块 ASM Disk 在不同机器需要被绑定为相同路径。

ASM 磁盘组（ASM DISK GROUP）

ASM 磁盘组可以联机添加或者删除某块磁盘。添加磁盘后，用户可以通过 DMASMTOOLM 执行指定命令发起 AU 重平衡，DMASM 系统会将 ASM 文件有效数据在各磁盘中重新分布；删除磁盘（或者磁盘故障）后，DMASM 系统会定期检查磁盘组，通过 AU 重平衡恢复丢失的数据。整个 AU 重分布的过程不影响 DMDSC 集群环境正常使用。当软硬件资源充裕的情况下，可通过调大重平衡并行度来提高执行重平衡的速度。

一个 ASM 磁盘组内包含多块 ASM 磁盘，是 ASM 文件系统的基本组成元素。每个 ASM 磁盘组都是自描述的，包含运行 ASM 文件系统所需的所有信息；DMASMSVRM 实例自动扫描指定路径下所有可访问的磁盘，解析磁盘头格式，加载符合 ASM 磁盘组特征的磁盘，进一步解析得到磁盘组内日志信息、文件信息、文件数据映射等信息。

ASM 文件系统支持多个 ASM 磁盘组。一个 ASM 磁盘组内支持多个故障组；ASM 磁盘个数有限的场景，应该尽量保证磁盘组内有多个故障组（至少 3 个），每个故障组内有多个磁盘（至少 2 个），建议磁盘组一般不超过两个。

系统磁盘（DCRV DISK）

系统磁盘用于存放镜像系统的系统信息。每块 DCRV 磁盘均有一份 DCR 文件和一份 VTD 文件。无故障情况下，每个磁盘上的 DCR 文件和 VTD 文件均相同。数据写入 DCR 文件和 VTD 文件时，会向所有 DCRV 磁盘同时写入，只有超过半数的 DCRV 磁盘写入成功才能最终写入成功，否则写入失败。读取 DCR 文件或 VTD 文件时，只有从超过半数 DCRV 磁盘读取到一致内容才能最终读取到数据，否则读取失败。

系统磁盘组（DCRV GROUP）

DCRV 系统磁盘组是专供 DMDSC 集群使用的，与其余 ASM 磁盘组不同。DCRV 磁盘组专用于存储 DCR 文件和 VTD 文件。用户既不能删除 DCRV 磁盘组，也不能在系统磁盘组内创建文件、修改系统磁盘组属性、修改 DCR 文件和 VTD 文件。仅可以读取 DCR 文件和 VTD 文件。DCRV 磁盘组仅以 ASM 磁盘组的形式加载到 ASM 文件系统中，实现机制（读写一致与故障容错不同）与 ASM 磁盘组并不相同。

DCRV 系统磁盘组可以由 1~5 块 DCRV 磁盘组成。系统磁盘组提供联机增删磁盘的功能，每次仅允许增删一块磁盘。该命令虽然同样由 ASMTOOLM 发起，但实际由主 DMCSS 控制，DMCSS、DMASMSVRM 与 DMSERVER 协同完成。

DCRV 系统磁盘组在 ASM 文件系统内是一个单副本磁盘组，每块磁盘分别属于单独的故障组，每份 DCR 文件或 VTD 文件都是一个单独的 ASM 文件。

数据分配单元（ALLOCATION UNITS）

ASM DISK GROUP 的每块磁盘按同一大小分为一个个 AU，创建 DISK GROUP 时可以指定 AU 大小（1、2、4、8、16、32、64M）。创建完 DISK GROUP，AU SIZE 不能更改。不同 DISK GROUP 允许使用不同 AU SIZE。

AU 分为描述 AU、INODE AU 和数据 AU 三种。

描述 AU 由多个描述 AU 项组成。每一个描述 AU 项存储一个 INODE AU 或一个数据 AU 的元数据。元数据包括 AU 所属文件 ID，AU 的前一位 AU，AU 的后一位 AU、其他副本地址等。

INODE AU 由多个 INODE 项组成。每一个 INODE 项存放一个 ASM 文件的元数据。元数据包括文件完整路径、大小、副本数、条带化大小、创建时间等信息等。

数据 AU 用于存储用户数据。

ASM 文件

在 DMASM 镜像环境中创建 ASM 文件时，可以指定文件副本数（单副本、两副本或三副本），在出现某块磁盘故障时，也可以保证数据完整。

在 DISK GROUP 里面创建 ASM 文件，系统会在 INODE AU 为该文件分配一个 INODE 项存放 ASM 文件元数据信息，在数据区分配多个数据 AU 存放 ASM 文件数据。一个 ASM 文件的所有 AU 都在同一 DISK GROUP 内，不会跨 DISK GROUP。

文件镜像（MIRRORING）

文件镜像是一种文件存储形式。将一个磁盘上的数据在其他磁盘上存储一份完全相同的副本，这种存储方式即为镜像。镜像是采用数据冗余的方式来保证数据的安全性。

故障组（FAILURE GROUPS）

故障组专门用来存放 ASM 文件的不同镜像副本。将在 [11.3.6 故障组](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-image-introduction.html#11.3.6%20%E6%95%85%E9%9A%9C%E7%BB%84)中详细介绍。

DMASM 文件系统初始化工具（DMASMCMDM）

专门用于 DMASM 镜像环境。将在 [11.5 DMASM 镜像主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-image-introduction.html#11.5%20DMASM%20%E9%95%9C%E5%83%8F%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

DMASM 服务器（DMASMSVRM）

专门用于 DMASM 镜像环境。将在 [11.5 DMASM 镜像主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-image-introduction.html#11.5%20DMASM%20%E9%95%9C%E5%83%8F%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

DMASM 应用程序访问接口（DMASMAPIM）

专门用于 DMASM 镜像环境。将在 [11.5 DMASM 镜像主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-image-introduction.html#11.5%20DMASM%20%E9%95%9C%E5%83%8F%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

DMASM 管理工具（DMASMTOOLM）

专门用于 DMASM 镜像环境。将在 [11.5 DMASM 镜像主要部件](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-image-introduction.html#11.5%20DMASM%20%E9%95%9C%E5%83%8F%E4%B8%BB%E8%A6%81%E9%83%A8%E4%BB%B6)中详细介绍。

## 11.3 DMASM 镜像关键技术

为了帮助用户更好的理解、使用 DMASM 镜像，本节从 ASM 磁盘与文件管理、DMASM REDO 日志、AU 描述项等方面介绍 DMASM 镜像原理。

### 11.3.1   磁盘与文件管理

DMASMCMDM 将物理磁盘格式化后，变成可识别可管理的磁盘。再通过磁盘组将一个或者多个格式化后的磁盘整合成一个整体提供文件服务。

磁盘被格式化后会被逻辑划分为一个个 AU。AU 是管理 DMASM 文件系统中磁盘的基本单位，ASM 文件的最小分配单位也是 AU。这些逻辑划分的 AU 根据其用途可以分为描述 AU、INODE AU 和数据 AU。

在 DMASM 文件系统中，一个磁盘中可有多个描述 AU，一个描述 AU 可管理多个 INODE AU 和数据 AU。在不同的场景中，每个描述 AU 管理的 INODE AU 和数据 AU 数量和顺序均不相同。下图展示了 DMASM 镜像环境下一个磁盘的逻辑结构，其中 desc、inode 和 data 分别为描述 AU、INODE AU 和数据 AU，MAX 为一个描述 AU 管理的最大磁盘空间。

![图 11.4 一个 DMASM 镜像磁盘的逻辑结构.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112153800GP3QOSRBOCM7158K14)

图11.4 一个DMASM镜像磁盘的逻辑结构

创建、删除 ASM 文件的操作，在 DMASM 系统内部被转换成修改、维护 INODE AU 的具体动作。扫描全局的 INODE AU 链表就可以获取到磁盘组上所有的 ASM 文件信息。

### 11.3.2   DMASM REDO 日志

DMASM 采用重做日志机制，保证在各种异常（比如系统掉电）情况下数据不被损坏。创建、删除、扩展、截断（可以称为 DDL 操作）ASM 文件过程中，所有针对 DMASM 描述 AU、INODE AU 的修改，都会生成 REDO 日志，并且在描述 AU、INODE AU 的修改写入磁盘之前，必须确保 REDO 日志已经写入磁盘。DMASM 中，只有针对描述 AU 和 INODE AU 的修改会产生 REDO 日志，用户修改数据 AU 的动作并不会产生 REDO 日志。

DMASM 所有 DDL 操作都是串行执行的，并且在操作完成之前，会确保所有修改的描述 AU、INODE AU 写入磁盘；DDL 操作过程中出现异常时，如果 REDO 日志尚未写入磁盘，则当前操作对系统没有任何影响；如果 REDO 日志已经写入磁盘，那么重新启动后，系统会重演 REDO 日志，修改描述 AU 和 INODE AU，将此次 DDL 继续完成。

### 11.3.3  AU 映射表

创建 ASM 文件后，用户操作 ASM 文件的一般流程是：调用 ASM 文件的 OPEN、READ、WRITE 接口；打开 ASM 文件并获取一个句柄，再使用这个句柄从文件的指定偏移读取或者写入数据。用户在使用 DMASM 的过程中，只需要获取一个 ASM 文件句柄，并不需要知道数据最终保存在物理磁盘的什么位置。

DMASM 使用 AU 映射表机制维护 ASM 文件与物理磁盘的映射管理，访问 ASM 文件时，根据文件句柄、文件偏移信息，通过 AU 映射表可以快速获取到数据对应的物理磁盘地址。

由于 DMASM 并不缓存任何用户数据，与直接读、写块设备相比，ASM 文件的读、写操作仅仅增加了获取 AU 映射表的代价，而这个代价与 IO 相比几乎可以忽略，因此，使用 DMASM 并不会引起读、写性能的下降。

![图 11.5 AU 映射表示意图.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112153823OIH9PDXI1GP46UN4HL)

图11.5 AU映射表示意图

### 11.3.4   条带化

ASM 文件条带化技术是将一块连续的数据按照条带化粒度分割成多个数据块，并把它们分别存储到不同的磁盘中。

条带化分为两种类型：粗粒度条带化和细粒度条带化。条带化粒度是执行条带化的重要参数，取值 0、32、64、128 和 256，单位 KB。

  * 数据文件的条带化粒度通过 DMINIT.INI 文件的 DATA_STRIPING 参数进行配置。
  * 控制文件的条带化粒度固定为粗粒度，无需用户指定。
  * 联机日志的条带化粒度通过 DMINIT.INI 文件的 LOG_STRIPING 参数进行配置。
  * 归档日志的条带化粒度通过 DMARCH.INI 文件的 ARCH_ASM_STRIPING 参数进行配置。
  * ASM 文件的条带化粒度在创建 ASM 文件时指定。
  * IOTEST 临时文件的条带化粒度在执行 IOTEST 命令测试 ASM 环境下磁盘读写速度时指定。



条带化粒度取值 0 表示粗粒度。粗粒度条带是将文件按 AU 大小分割为一个个数据块。下图展示了粗粒度条带化分割示意图，其中以 AU 大小 1MB，文件大小 3MB 为例，文件被分为 3 个块，3 个块均匀地分布在磁盘组的三个磁盘上。

![图 11.6 粗粒度条带化示意图.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112153920JOG09ED188G0I8P492)

图11.6 粗粒度条带化示意图

条带化粒度取值 32~256 表示细粒度。细粒度条带是将数据按照条带化粒度大小分割为一个个数据块。下图展示了细粒度条带化的分割示意图，其中以 AU 大小 1MB，文件大小 192KB，条带化粒度取值 32K 为例，文件被分为 6 个块，6 个块均匀地分布在磁盘组的三个磁盘上。

![图 11.7 细粒度条带化示意图.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112153943LNDGTGNSY54GMV75BK)

图11.7 细粒度条带化示意图

条带化的性能与使用的共享存储紧密相关，条带化粒度需要根据实际情况配置。一般情况下，推荐数据文件使用 32K 的粒度，联机日志、归档日志等使用 256K 的粒度。粗粒度条带化基本等效于没有条带化，连续分配文件空间的情况。细粒度条带化有合并读写功能，对于大量内容的读写不是逐条带进行的，而是将同一 AU 的内容合并后一次完成的。因此，对于大量顺序读写的文件，也可以使用细粒度条带化。细粒度条带化提升性能的原理是将 IO 分散到多块磁盘，同时利用多块磁盘的性能。如果磁盘组内磁盘较少，不存在分散 IO 利用更多磁盘的条件，推荐使用粗粒度条带化。细粒度条带化提升的是 IO 性能，磁盘本身性能较高（SSD 等）且使用中数据库 IO 量较小的情况，条带化对性能没有影响。

使用 ASM 文件条带化将数据均匀分布到 Disk Group 内的磁盘中以后，多个用户同时访问数据的不同部分时，分别向多个不同的 AU 发出请求，由于这些 AU 存储在不同的磁盘上，因此不会造成磁盘冲突，可以获得最大程度的 IO 并行能力，从而提高数据库操作性能。

在 DMASM 镜像中，不建议用户将一块共享存储逻辑划分为多块 ASM 磁盘，进而使用条带化和文件镜像功能。因为一块共享存储逻辑划分成的多块磁盘并不能利用条带化和文件镜像的优势，无法并行地利用多块共享存储的 IO，反而可能破坏共享存储在顺序读写方面的优化能力，降低 IO 速度；只有相互独立的物理磁盘才能发挥出优势。如果用户需要在逻辑划分成的多块磁盘上使用 DMASM 镜像，请将所有文件配置为粗粒度条带化。

### 11.3.5  文件镜像

为了保障数据安全性，可采用文件镜像的方式对文件进行存储，即将待存储的文件数据分割成大小相同的多个数据块，再将每个数据块按镜像类型指定的副本数分别存储在不同的磁盘上。数据镜像是一种高级的冗余技术，通过增加数据冗余来提高数据安全性。

文件镜像类型有三种：单副本、两副本和三副本。在创建文件的语法中分别用 EXTERNAL、NORMAL、HIGH 表示。单副本表示外部冗余（EXTERNAL），不提供任何镜像，完全依靠数据外部的磁盘高度可靠来保证数据的安全。双副本表示正常冗余（NORMAL），提供双向镜像，可通过副本较好的保证数据完整性。三副本表示高度冗余（HIGH），提供三向镜像，能够通过副本更好的保证数据的完整性。

多个镜像副本中只有一个作为主镜像副本对外提供服务，其余均作为从镜像副本。当主镜像副本发生故障后，系统会从镜像副本中重新自动挑选一个继续提供服务。

下图展示一个 ASM 文件经过细粒度的条带化技术分割成多个数据块 ABC 之后，按照两副本的镜像类型分别存储在不同的磁盘中。

![图 11.8 两副本的文件镜像示意图.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112154019E00B95XY7A7F7PZ15I)

图11.8 两副本的文件镜像示意图

不同类型的文件指定镜像类型的方式不同。文件镜像类型指定的副本数需要小于等于待存储的磁盘组的副本数，否则创建不成功。

  * 数据库文件（控制文件、数据文件和日志文件）的镜像类型通过 DMINIT.INI 文件的 CTL_MIRROR、DATA_MIRROR 和 LOG_MIRROR 参数进行配置。其中日志文件包含联机日志和归档日志。
  * 磁盘组元数据文件的镜像类型在创建磁盘组时指定。磁盘组元数据是指描述 AU、INODE AU 和 DMASM REDO 日志的相关信息。
  * ASM 文件的镜像类型在创建 ASM 文件时指定。
  * 拷贝之后 ASM 文件的镜像类型在执行 CP 命令时指定。
  * IOTEST 临时文件的镜像类型在执行 iotest 命令测试 ASM 环境下磁盘读写速度时指定。



### 11.3.6   故障组

一个故障组是由多个磁盘组成的集合。一个磁盘组中可包含 1 个或多个故障组。此处的磁盘均为经过 DMASMCMDM 工具创建的磁盘，并非普通的裸盘。一个故障组只能属于一个磁盘组。

故障组专门用来存放文件的不同镜像副本。故障组以 AU 为基本单位来存储不同的镜像副本。故障组中的一个 AU 可存储多个数据副本。为了数据的安全，系统会将同一个数据块的多个副本分别存储在不同的故障组中。

故障组的数量在创建 ASM Disk Group 时指定。创建两副本磁盘组时，至少需要两个故障组；创建三副本磁盘组时，至少需要三个故障组。如果只创建了一个故障组，则不能使用多副本功能。

下图展示了一个磁盘组中的故障组示意图。一个磁盘组中含有 6 块磁盘、三个故障组。两个 ASM 文件条带化粒度假定均为 32K。两副本 ASM 文件被条带化技术分割为 A、B、C、D、E 五个数据块；单副本 ASM 文件被条带化技术分割为 X、Y 二个数据块。

![图 11.9 一个磁盘组中的故障组示意图.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230112154114Z9K8HA4SI25SWJMWVZ)

图11.9 一个磁盘组中的故障组示意图

### 11.3.7   数据重分布

手动删除 ASM 磁盘或者物理磁盘故障可能导致 ASM 文件镜像数据丢失。在磁盘空间足够，且开启自动重平衡（DMDCR_AUTO_RBL_TIME）的条件下，系统会自动为 ASM 文件重新分配 AU 并补齐镜像数据。也可以手动触发故障磁盘重平衡，从而立即尝试恢复 ASM 文件丢失的镜像数据。当新添加磁盘时，用户可以手动执行命令发起磁盘空间重平衡，使磁盘使用率整体上是均衡的。

### 11.3.8 伙伴磁盘

伙伴磁盘用于限制磁盘 AU 的镜像副本在磁盘组中的分布，AU 的镜像副本仅分布在该 AU 所在磁盘的伙伴磁盘上。

伙伴磁盘具有如下性质（A、B、C 代指磁盘）：

1．A 是 B 的伙伴，则 B 一定也是 A 的伙伴。

2．A 和 B 是伙伴，B 和 C 是伙伴，A 和 C 不一定是伙伴。

3．伙伴磁盘与原磁盘位于不同故障组。

一般情况下，一个磁盘最多有 7 个伙伴磁盘，系统预留了第 8 个伙伴用于实现磁盘替换等功能。在创建磁盘组、添加磁盘时，系统会自动为新磁盘分配伙伴，添加磁盘也可以手动指定伙伴。日常维护时，允许手动增加磁盘伙伴关系。

### 11.3.9 磁盘状态

磁盘状态是磁盘的基本属性，表明了 DMASM 使用该磁盘的限制和该磁盘操作的限制。

磁盘状态有如下 5 种：

正常（NORMAL）：最基础的状态，磁盘加入磁盘组后未发生过故障或删除时就处于该状态。DMASM 会读写该磁盘，会分配、释放该磁盘的 AU。允许删除、替换该磁盘。

离线（OFFLINE）：磁盘发生故障后的状态，表示该磁盘发生故障不久可以通过重连快速恢复正常。DMASM 不会读写该磁盘，仅会释放该磁盘的 AU。允许删除、替换、重连，不允许故障重平衡该磁盘。

重连中（RECONNECT）：重连操作过程中磁盘的状态。DMASM 会读写该磁盘，仅会释放该磁盘的 AU。不允许删除、替换、重连、故障重平衡该磁盘。

故障（FAIL）：磁盘处于离线状态超过指定时间后的状态，表示该磁盘彻底故障，对应的物理磁盘无法重连回磁盘组。DMASM 不会读写该磁盘，仅会释放该磁盘的 AU。允许替换、故障重平衡，不允许删除、重连该磁盘。

已删除（DELETED）：手动删除的磁盘的状态，类似故障状态。DMASM 不会读写该磁盘，仅会释放该磁盘的 AU。允许替换、故障重平衡，不允许删除、重连该磁盘。

磁盘状态转换示意图：

![图 11.10 磁盘状态转换示意图.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240925152529XYCU449PGVX4GMCU4X)

### 11.3.10 系统磁盘组在 DMASM 中的映射

系统磁盘组与普通的磁盘组不同，虽然也遵循 DMASM 的物理存储格式，但不由 DMASM 管理和使用。系统磁盘组以特殊的方式映射到 DMASM 文件系统中，通过 DMASM 提供有限的功能：

  1. 读取、导出每副本 DCR、VTD 的内容。系统磁盘组仅支持读取，且不存储任何用户数据。
  2. 查询 DCRV 磁盘的详细信息。DCRV 磁盘仅有 NORMAL、UNUSED、FAILED 共 3 种状态，对应正常、未使用、故障的磁盘。
  3. 联机增删 DCRV 磁盘。该功能以 DMASM 为入口，由 CSS 协调 DMASMSVRM、DMSERVER 共同完成。



## 11.4 DMASM 镜像技术指标

本节主要介绍一下 DMASM 中的一些重要技术指标。

表11.2 DMASM技术指标

|**项**|**大小**|**说明**|
|--|--|--|
|AU 大小（AU_SIZE）|支持 1、2、4、8、16、32、64|一个 AU 占用的存储空间。单位 M|
|AU 描述项大小|占用物理存储空间为 32 字节；  占用共享内存空间为 64 字节|一个 AU 描述项占用 32 字节的物理存储空间；加载到共享内存中占用 64 字节|
|一个描述 AU 管理的最大 AU 数|AU_SIZE / 32|以 AU_SIZE=4 为例，一个描述 AU 最多可以管理 131072 个 AU|
|一个描述 AU 管理的最大磁盘空间（假设为 AU_MAX）|AU_SIZE / 32 * AU_SIZE|以 AU_SIZE=4 为例，一个描述 AU 最多可以管理 512G 磁盘空间|
|INODE 项大小|512 字节|一个 ASM 文件描述项大小，每个 ASM 文件对应着一个文件描述项|
|INODE AU 可管理的最大文件数|AU_SIZE / 512|一个 INODE AU 最多可以管理 2046 个文件|
|ASM 文件最小尺寸|AU_SIZE|每个 ASM 文件最少包含一个 AU，占用一个 AU_SIZE 大小的空间。目录只分配 INODE 项不分配 AU，不占用磁盘空间|
|ASM 文件最大尺寸|0X1000000 * AU_SIZE|一个 ASM 文件最多看包含 0X1000000 个 AU，以 AU_SIZE=4 为例，理论上单个 ASM 文件的最大尺寸是 64PB|
|一个用户连接可以同时打开的 ASM 文件个数|65536|一个用户连接，最多打开 65536 个 ASM 文件|
|ASM 文件数上限|65532|一个磁盘组，最多可以创建 65532 个 ASM 文件|
|ASM 磁盘组个数上限|125|一个 DMASM 中可创建 1~125 个磁盘组|
|一个 ASM 磁盘组中的磁盘个数上限|8192|一个磁盘组中可包含 1~8192 个 ASM 磁盘|
|DCRV 磁盘组个数上限|1|一个 DMASM 镜像中只能有一个 DCRV 磁盘组|
|一个 DCRV 磁盘组中 DCRV 磁盘个数上限|5|一个 DCRV 磁盘可包含 1、3 或 5 个 DCRV 磁盘|
|每 100M 共享内存大小能管理的磁盘大小上限|64 * AU_SIZE|每个 AU 描述项占用 64byte 内存空间，共享内存空间包含一些额外的控制信息，以 AU_SIZE=1 计算结果为 1.6T|


## 11.5 DMASM 镜像主要部件

DMASM 镜像的主要部件包括：提供存储功能的共享存储、初始化工具 DMASMCMDM、服务器 DMASMSVRM、应用程序访问接口 DMASMAPIM、文件系统管理工具 DMASMTOOLM。

### 11.5.1   DMASMCMDM

DMASMCMDM 是 DMASM 镜像系统初始化工具，用来创建 ASM 磁盘和 DCRV 系统磁盘组等。

DMASMCMDM 工具用法分两种：一是在 DMASMCMDM 工具中使用脚本文件，脚本文件中存放一条或多条 DMASMCMDM 命令。二是在 DMASMCMDM 工具中使用 DMASMCMDM 命令。

#### 11.5.1.1 使用脚本文件

##### 11.5.1.1.1 查看 DMASMCMDM HELP

通过 DMASMCMDM HELP 可查看 DMASMCMDM 工具如何使用脚本文件。

```
d:\dmdbms\bin>DMASMCMDM HELP

dmasmcmdm V8

格式: dmasmcmdm.exe KEYWORD=value

例程: dmasmcmdm.exe SCRIPT_FILE=asmcmd.txt


关键字       说明（默认值）

------------------------------------------------------------------------------

SCRIPT_FILE     asmcmd脚本文件路径

RET_FLAG      执行asmcmd脚本文件时，出错是否立即返回。0：忽略，1：返回。(0)

HELP        打印帮助信息
```

##### 11.5.1.1.2 脚本文件用法

通过 SCRIPT_FILE 参数来指定脚本文件。

语法如下：

```
dmasmcmdm SCRIPT_FILE=filepath [RET_FLAG=value]
```

参数说明：

SCRIPT_FILE：用于指定 asmcmd 脚本文件路径。脚本文件（例如 asmcmd.txt）中存放一条或多条 DMASMCMDM 命令。用户没有通过 SCRIPT_FILE 指定脚本文件，则 DMASMCMDM 进入交互模式运行，逐条解析、运行命令。用户通过 SCRIPT_FILE 指定脚本文件，则以行为单位读取文件内容，并依次执行，执行完成后，自动退出 DMASMCMDM 工具。脚本文件必须以”#asm script file”开头，否则认为是无效脚本文件；脚本中其他行以“#”开头表示注释。

RET_FLAG：用于设置执行 ASMCMD 脚本文件时，出错是否立即返回。取值 0 或 1。0 不返回，即执行出错时忽略当前出错的行，继续执行下一条；1 返回，即执行出错时立即停止，并返回一个错误码。缺省值为 0。

举例说明：

第一步，创建脚本文件 asmcmd.txt。例如脚本文件内容如下：

```
create dcrvdisk '/dev_DSC_HDD/DCRV1' 'dcrv1'

create dcrvdisk '/dev_DSC_HDD/DCRV2' 'dcrv2'

create asmdisk '/dev_DSC_HDD/MIR_HDD_1' 'data1'

create asmdisk '/dev_DSC_HDD/MIR_HDD_2' 'data2'
```

第二步，通过使用 SCRIPT_FILE 参数指定脚本，执行出错立即停止并返回一个错误码。

```
./dmasmcmdm SCRIPT_FILE=/opt/dmdbms/script/asmcmd.txt RET_FLAG=1
```

#### 11.5.1.2   使用 DMASMCMDM 命令

##### 11.5.1.2.1 查看 ASM HELP

在 DMASMCMDM 工具中输入 HELP，可查看 DMASMCMDM 工具如何使用 DMASMCMDM 命令。

```
d:\dmdbms\bin>DMASMCMDM

dmasmcmdm V8

ASM>HELP

Format: create emptyfile file_path size(MB) num

Usage: create emptyfile 'd:\asmdisks\disk0.asm' size 100

 

Format: create asmdisk disk_path disk_name [size(MB)]

Usage: create asmdisk 'd:\asmdisks\disk0.asm' 'DATA0'

Usage: create asmdisk 'd:\asmdisks\disk0.asm' 'DATA0' 100

 

Format: create dcrvdisk disk_path disk_name [size(MB)]

Usage: create dcrvdisk 'd:\asmdisks\dmdcrv0.asm' 'DCRV0'

Usage: create dcrvdisk 'd:\asmdisks\dmdcrv0.asm' 'DCRV0' 100

 

Format: create system diskgroup asmdisk disk_path{,disk_path} attribute config=ini_path, passwd=password

Usage: create system diskgroup asmdisk 'd:\asmdisks\dmdcrv0.asm' attribute config='d:\dmdcr_cfg.ini', passwd='aaaaaa'

Usage: create system diskgroup asmdisk 'd:\asmdisks\dmdcrv0.asm','d:\asmdisks\dmdcrv1.asm','d:\asmdisks\dmdcrv2.asm' attribute config='d:\dmdcr_cfg.ini', passwd='aaaaaa'

 

Format: export dcrvdisk disk_path to ini_path

Usage: export dcrvdisk 'd:\asmdisks\dmdcrv0.asm' to 'd:\dmdcr_cfg.ini'

 

Format: import dcrvdisk ini_path to disk_path{,disk_path}

Usage: import dcrvdisk 'd:\dmdcr_cfg.ini' to 'd:\asmdisks\dmdcrv0.asm'

Usage: import dcrvdisk 'd:\dmdcr_cfg.ini' to 'd:\asmdisks\dmdcrv0.asm', 'd:\asmdisks\dmdcrv1.asm', 'd:\asmdisks\dmdcrv2.asm'

 

Format: extend dcrvdisk disk_path{,disk_path} from ini_path

Usage: extend dcrvdisk 'd:\asmdisks\dmdcrv0.asm' from 'd:\dmdcr_cfg.ini'

Usage: extend dcrvdisk 'd:\asmdisks\dmdcrv0.asm', 'd:\asmdisks\dmdcrv1.asm', 'd:\asmdisks\dmdcrv2.asm' from 'd:\dmdcr_cfg.ini'

 

Format: check dcrvdisk disk_path

Usage: check dcrvdisk 'd:\asmdisks\dmdcrv0.asm'

 

Format: recover dcrvdisk load_path

Usage: recover dcrvdisk 'd:\asmdisks\'

 

Format: clear dcrvdisk err_ep_arr disk_path_dir group_name

Usage: clear dcrvdisk err_ep_arr 'd:\asmdisks\' 'GRP_DSC'

 

Format: listdisks path

Usage: listdisks 'd:\asmdisks\'

 

Format: check_grp disk_path

Usage: check_grp 'd:\asmdisks\dmdata0.asm'
```

##### 11.5.1.2.2 命令用法

当 SCRIPT_FILE 和 RET_FLAG 参数全部缺省时，即进入命令行界面。

语法如下：

```
dmasmcmdm 
```

可在 DMASMCMDM 命令行界面中直接输入 DMASMCMDM 命令并执行。下面详细介绍 DMASMCMDM 工具支持的所有 DMASMCMDM 命令。

###### 11.5.1.2.2.1 创建空磁盘文件

专门用于单机模拟 DMDSC 环境。

创建名为 xxx.asm 的空磁盘文件，用于模拟块设备，注意必须以.asm 结尾。模拟测试用，真实环境不建议使用。

语法如下：

```
CREATE EMPTYFILE file_path SIZE num
```

参数说明：

num：指定空文件大小。取值为大于等于 32 的整数。单位 MB。

举例说明：

创建名为 disk0.asm 的空磁盘文件。

```
CREATE EMPTYFILE 'd:\asmdisks\disk0.asm' SIZE 100
```

###### 11.5.1.2.2.2 创建 ASM 磁盘

创建 ASM 磁盘，即将块设备格式化为 ASM DISK，并在块设备头部写入 ASM DISK 标识信息（DMASM 标识串、ASM 磁盘名以及 ASM 磁盘大小等信息）。

语法如下：

```
CREATE ASMDISK disk_path disk_name [size]
```

参数说明：

size：指定 ASMDISK 大小。取值为大于等于 32 的整数。单位 MB。size 缺省时系统自动获取磁盘大小。

举例说明：

```
CREATE ASMDISK 'd:\asmdisks\disk0.asm' 'DATA0'

或

CREATE ASMDISK 'd:\asmdisks\disk0.asm' 'DATA0' 100
```

###### 11.5.1.2.2.3 创建 DCRV 磁盘

创建 DCRV 磁盘，即将块设备格式化为 DCRV 磁盘，并在块设备头部写入 DCRV 标识信息。

语法如下：

```
CREATE DCRVDISK disk_path disk_name [size(MB)]
```

参数说明：

size：指定 VCRDISK 大小。取值为大于等于 32 的整数。单位 MB。size 缺省时系统自动获取磁盘大小。

举例说明：

```
CREATE DCRVDISK 'd:\asmdisks\dmdcrv0.asm' 'DCRV0'

或

CREATE DCRVDISK 'd:\asmdisks\dmdcrv0.asm' 'DCRV0' 100
```

###### 11.5.1.2.2.4 创建 DCRV 系统磁盘组

根据配置文件 DMDCR_CFG.INI 内容，创建 DCRV 系统磁盘组。同时设置登录 ASM 文件系统的密码，密码必须用单引号括起来。DCRV 系统磁盘组名称为 SYSTEM，系统自动生成，无需用户指定。

语法如下：

```
CREATE SYSTEM DISKGROUP ASMDISK disk_path{,disk_path} ATTRIBUTE CONFIG=ini_path, PASSWD=password
```

参数说明：

CONFIG：DMDCR_CFG.INI 路径。

举例说明：

```
CREATE SYSTEM DISKGROUP ASMDISK '/dev/DMDATA/dmdcrv0' ATTRIBUTE config='/home/dmdbms/config/dmdcr_cfg.ini', passwd='aaaaaa'

或

CREATE SYSTEM DISKGROUP ASMDISK '/dev/DMDATA/dmdcrv0’,'/dev/DMDATA/dmdcrv1','/dev/DMDATA/dmdcrv2' ATTRIBUTE config='/home/dmdbms/config/dmdcr_cfg.ini', passwd='aa123456'
```

###### 11.5.1.2.2.5 导出 DCRV 配置文件

用户如果要查看当前 DCRV 磁盘的配置信息，可以使用 EXPORT 命令将 DCRV 磁盘内容导出到文本文件。导出过程不影响系统正常使用。

语法如下：

```
EXPORT DCRVDISK disk_path TO ini_path
```

举例说明：

```
EXPORT DCRVDISK '/dev/DMDATA/dmdcrv0' TO '/home/dmdbms/config/dmdcr_cfg1.ini'
```

###### 11.5.1.2.2.6 导入 DCRV 配置文件

用户如果需要修改当前 DCRV 磁盘的配置信息，可以使用 IMPORT 命令修改 DCRV 磁盘内容。只有所有数据库实例都退出的情况才能使用 IMPORT 命令修改 DCRV 磁盘内容。

语法如下：

```
IMPORT DCRVDISK ini_path TO disk_path{,disk_path}
```

参数说明：

ini_path：DMDCR_CFG.INI 路径。

举例说明：

```
IMPORT DCRVDISK '/home/dmdbms/config/dmdcr_cfg.ini' TO '/dev/DMDATA/dmdcrv0'

或

IMPORT DCRVDISK '/home/dmdbms/configdmdcr_cfg.ini' TO '/dev/DMDATA/dmdcrv0', '/dev/DMDATA/dmdcrv1', '/dev/DMDATA/dmdcrv2'
```

> **注意**
>
>
> **DDCR_VTD_PATH、DCR_N_GRP、DCR_GRP_N_EP、DCR_GRP_NAME、DCR_GRP_TYPE、DCR_GRP_EP_ARR、DCR_EP_NAME、EP所属的组类型、checksum值，密码不可修改。必须以组为单位进行修改。  
>  特别的，在动态增加节点时，可以并需要对DCR_GRP_N_EP和DCR_GRP_EP_ARR进行修改 **



###### 11.5.1.2.2.7 联机修改 DCRV 磁盘

联机修改 DCRV 磁盘，增加节点，会将新增节点信息写回 DCRV 磁盘。支持同时修改多个 DCRV 磁盘。

语法格式：

```
EXTEND DCRVDISK disk_path{,disk_path} FROM ini_path
```

参数说明：

ini_path：DMDCR_CFG.INI 路径。

举例说明：

```
EXTEND DCRVDISK '/dev/DMDATA/dmdcrv0' FROM '/home/dmdbms/config/dmdcr_cfg.ini'

或

EXTEND DCRVDISK '/dev/DMDATA/dmdcrv0', '/dev/DMDATA/dmdcrv1', '/dev/DMDATA/dmdcrv2' FROM '/home/dmdbms/config/dmdcr_cfg.ini'
```

###### 11.5.1.2.2.8 校验 DCRV 磁盘

校验 DCRV 磁盘。

语法如下：

```
CHECK DCRVDISK disk_path
```

参数说明：

disk_path：磁盘的路径。

举例说明：

```
CHECK DCRVDISK 'd:\asmdisks\dmdcrv0.asm'
```

###### 11.5.1.2.2.9 离线修复 VTD 文件

如果因为强杀等原因造成 VTD 文件副本不一致，导致 DMDSC 无法启动，那么可使用此命令将最新的 VTD 副本同步到其余副本，使 VTD 恢复到一致的状态。前提要求 DMCSS、DMASM、DMSERVER 全部退出，且超过半数 VTD 副本可以正常读写。

如果某一块 VTD 的所有副本全部被破坏，使用此命令则会将该块内容清零。清零之后 DMDSC 可能仍然无法启动，那么需要使用 DMASMCMDM 重新初始化 VTD 文件。

语法如下：

```
RECOVER DCRVDISK load_path{,load_path}
```

参数说明：

load_path：DCRV 磁盘所在路径。

支持指定多路径，各路径以英文逗号','进行分隔，所有配置的路径都必须为有效路径，最多可以同时配置 8 个路径，且暂不支持中文路径。

举例说明：

```
RECOVER DCRVDISK '/dev_data/asmdisks/'
RECOVER DCRVDISK '/dev_data/asmdisks0/,/dev_data/asmdisks1/,/dev_data/asmdisks2/'
```

###### 11.5.1.2.2.10 清理 DCRV 中指定组的故障节点信息

清理 DCRV 中指定组的故障节点信息。

语法如下：

```
CLEAR DCRVDISK ERR_EP_ARR disk_path_dir{,disk_path_dir} group_name
```

参数说明：

disk_path_dir 磁盘的搜索路径。支持指定多路径，各路径以英文逗号','进行分隔，所有配置的路径都必须为有效路径，最多可以同时配置 8 个路径，且暂不支持中文路径。

group_name：磁盘组名。

举例说明：

```
CLEAR DCRVDISK ERR_EP_ARR 'd:\asmdisks\' 'GRP_DSC'
CLEAR DCRVDISK ERR_EP_ARR 'd:\asmdisks0\,d:\asmdisks1\,d:\asmdisks2\' 'GRP_DSC'
```

###### 11.5.1.2.2.11 显示指定路径下的磁盘属性

显示 path 路径下面所有磁盘的信息，分为三种类型：normal disk、unused asmdisk 和 used asmdisk。normal disk 为不属于 ASM 文件系统的常规磁盘。unused asmdisk 为未使用的 ASMDISK。used asmdisk 为已使用的 ASMDISK。

支持指定多路径，各路径以英文逗号','进行分隔，所有配置的路径都必须为有效路径，最多可以同时配置 8 个路径，且暂不支持中文路径。

语法如下：

```
LISTDISKS path{,path}
```

举例说明：

```
LISTDISKS '/dev/DMDATA'
LISTDISKS '/dev/DMDATA,/dev/DMLOG,/dev/RAW'
```

> **注意**
>
>
> 如果查询模拟ASM磁盘的磁盘信息，对path路径没有限制；如果查询真实环境中共享存储路径下的磁盘信息，则path路径的根目录名称必须以“/dev/‘开头，例如：listdisks
> ‘/dev/DMDATA/’或者listdisks ‘/dev_data/DMDATA/’，否则将不会显示该路径下的磁盘信息。



###### 11.5.1.2.2.12 校验用户创建的磁盘组数据

校验用户创建的磁盘组数据，包括元数据和用户数据。

语法如下：

```
CHECK_GRP disk_path load_path{,load_path}
```

参数说明：

disk_path：磁盘的路径。

load_path：磁盘的加载路径，为文件夹路径。支持指定多路径，各路径以英文逗号','进行分隔，所有配置的路径都必须为有效路径，最多可以同时配置 8 个路径，且暂不支持中文路径。

举例说明：

```
CHECK_GRP 'd:\asmdisks\dmdata0.asm' 'd:\asmdisks\'
CHECK_GRP 'd:\asmdisks\dmdata0.asm' 'd:\asmdisks0\,d:\asmdisks1\,d:\asmdisks2\'
```

### 11.5.2   DMASMSVRM

DMASMSVRM 是 DMASM 服务器，负责管理 DMASM 文件系统。每个提供 DMASM 服务的节点都必须启动一个 DMASMSVRM 服务器，这些 DMASMSVRM 服务器一起组成 DMASMSVRM 集群。DMASMSVRM 集群提供 DMASM 文件系统的全局并发控制。DMASMSVRM 集群需要和 DMCSS 集群配合使用，由 DMCSS 管理 DMASMSVRM 的启动、故障处理和节点重加入。

DMASMSVRM 启动时扫描指定路径（比如：/dev/DMDATA/）下的所有块设备，加载 ASM 磁盘，构建 ASM 磁盘组和 DMASM 文件系统。DMASMSVRM 服务器之间使用 MAL 系统进行信息和数据的传递。

DMASMSVRM 集群的启动、关闭、故障处理等流程由 DMCSS 控制，DMASMSVRM 定时向 DCRV 磁盘写入时间戳、实例状态等信息，DMCSS 控制节点定时从 DCRV 读取信息，检查 DMASMSVRM 实例的状态变化，启动响应的处理流程。

DMASMSVRM 集群中，只有一个控制节点，控制节点以外的其他节点叫做普通节点，DMASMSVRM 控制节点由 DMCSS 选取；所有 DDL 操作（比如创建、删除文件，创建、删除磁盘等）都是在控制节点执行，用户登录普通节点发起的 DDL 请求，会通过 MAL 系统转发到控制节点执行并返回；而 ASM 文件的读、写操作，则由登录节点直接完成，不需要经过 DMASMSVRM 实例。

通过 DMASMSVRM HELP 可查看 DMASMSVRM 的启动用法。

```
格式: dmasmsvrm.exe KEYWORD=value
例程: dmasmsvrm.exe DCR_INI=d:\data\DAMENG\dmdcr.ini
关键字             说明
----------------------------------------------------------------
DCR_INI            	dmdcr.ini路径
-NOCONSOLE       	以服务方式启动
HELP               	打印帮助信息
```

参数说明：

DCR_INI：DMDCR.INI 配置文件路径。DMDCR.INI 记录了 DCRV 磁盘路径、实例序列号等信息；如果不指定 DCR_INI 参数，dmasmsvrm 默认在当前路径下查找 DMDCR.INI 文件。

> **注意**
>
>
> 使用DMASMTOOLM或者DMASMAPIM函数dmasmm_conect()登录DMASMSVRM时，用户名只能为”ASMSYS”。若为本地登录，不校验密码；若为远程登录，应使用初始化DCR磁盘时设置的登录ASM文件系统的密码。



### 11.5.3 DMASMAPIM

DMASMAPIM 是 DMASM 文件系统的应用程序访问接口，通过调用 DMASMAPIM，用户可以访问和操作 ASM 文件。

与达梦数据库 DPI 接口类似，访问 ASM 文件之前，必须先分配一个 conn 连接对象，并登录到 DMASMSVRM 服务器，再使用这个 conn 对象进行创建磁盘组、创建文件、删除文件、读取数据和写入数据等 DMASM 相关操作。

DMASMAPIM 接口的详细介绍请参考 [19 附录](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-appendix.html)。

### 11.5.4 DMASMTOOLM

DMASMTOOLM 是 DMASM 文件系统管理工具，提供了一套类 Linux 文件操作命令，用于管理 ASM 文件，是管理和维护 DMASM 的好帮手。DMASMTOOLM 工具使用 DMASMAPIM 连接到 DMASMSVRM，并调用相应的 DMASMAPIM 函数，实现创建、拷贝、删除等各种文件操作命令；DMASMTOOLM 还支持 ASM 文件和操作系统文件的相互拷贝。

DMASMTOOLM 可以登录本地 DMASMSVRM，也可以登录位于其他节点的 DMASMSVRM，并执行各种文件操作命令。一般建议登录本地 DMASMSVRM 服务器，避免文件操作过程中的网络开销，提升执行效率。

> **注意**
>
>
> DMASMTOOLM工具不支持中文输入。若文件名或路径中包含中文字符，当客户端与服务器的编码不一致时，可能出现乱码，因此建议不要使用包含中文字符的文件名或路径。  
>  在DMASMTOOLM工具执行命令时，如果路径名称或者文件名首字母为数字，需要给字符串加单引号’。  
>  在DMASMTOOLM工具中使用cd命令，首字母为’+’，’\’，‘/’符号，都会从目录最上层开始查找目录。



通过 DMASMTOOLM HELP 可查看 DMASMTOOLM 的启动用法。

```
格式: dmasmtoolm.exe KEYWORD=value
例程: dmasmtoolm.exe DCR_INI=d:\data\DAMENG\dmdcr.ini
关键字				说明
---------------------------------------------------------------------------
DCR_INI				dmdcr.ini文件路径
HOST				asm服务器地址，IPV6地址要用[]包围
PORT_NUM			asm服务器端口号
USERID				登录asm服务器用户名密码，（格式：ASMSYS/PASSWORD）
SCRIPT_FILE			asmtool脚本文件路径
HELP				打印帮助信息
```

> **注意**
>
>
> 1.用户没有指定脚本文件，则DMASMTOOLM进入交换模式运行，逐条解析、运行命令；用户指定脚本文件（比如asmtool.txt），则以行为单位读取文件内容，并依次执行，执行完成以后，自动退出DMASMTOOLM工具。脚本文件必须以”#asm
> script file”开头，否则认为是无效脚本文件；脚本中其它行以”#”开始标识注释。  
>
> 2.dmasmtoolm命令直接输入的host/port信息配置的是连接dmasmsvrm的信息，可以在DMDCR_CFG.INI里面找到，分别为要连接的DMASM节点的DCR_EP_HOST和DCR_EP_PORT。  
>  3.当asm服务器地址使用IPv6地址时，为了方便区分端口，需要用[]封闭IP地址。



DMASMTOOLM 支持的命令主要分三类：一是磁盘组相关命令；二是 ASM 文件相关命令；三是兼容 Linux 相关命令。

#### 11.5.4.1 磁盘组命令

  * 创建 ASM 磁盘组



语法如下：

```
CREATE DISKGROUP diskgroup_name [<EXTERNAL | NORMAL | HIGH> REDUNDANCY] <磁盘组子句> {<磁盘组子句>}
<磁盘组子句>::= FAILGROUP  failgroup_name ASMDISK file_path{, file_path} [ATTRIBUTE AU_SIZE=num, REDO_SIZE=num]
```

参数说明：

diskgroup_name：为磁盘组名。最长不能超过 32 个字符。

failgroup_name：为故障组名。最长不能超过 32 个字符。

EXTERNAL、NORMAL、HIGH：用于指定镜像类型，即文件的副本数。EXTERNAL 表示单副本；NORMAL 表示双副本；HIGH 表示三副本。缺省为单副本。

FILE_PATH：为磁盘路径，路径必须是全路径，不能是相对路径。全路径长度不能超过 256 字节。

ATTRIBUTE：为属性子句，用于指定的 AU_SIZE 和 DMASM REDO 日志的 REDO_SIZE 大小，以 MB 为单位。

举例说明：

创建一个名为 DMDATA3 的磁盘组，镜像类型为 3 副本（HIGH REDUNDANCY）。3 副本至少需要 3 个故障组，本例以 3 个故障组和 6 块磁盘为例，将 6 块磁盘均匀放入 3 个故障组 data1、data2、data3 中。

```
CREATE DISKGROUP 'DMDATA3' HIGH REDUNDANCY FAILGROUP 'data1' asmdisk '/dev/DMDATA/disk0','/dev/DMDATA/disk1' failgroup 'data2' asmdisk '/dev/DMDATA/disk2', '/dev/DMDATA/disk3' failgroup 'data3' asmdisk '/dev/DMDATA/disk4','/dev/DMDATA/disk5' attribute au_size=4, redo_size=128
```

创建一个名为 DMDATA1 的磁盘组，镜像类型为 1 副本（EXTERNAL REDUNDANCY）。1 个副本可以使用 1 个或多个故障组，磁盘数可以是 1 个或多个，数据会自动均匀地分布在各磁盘中。本例以 1 个故障组和 2 块磁盘为例，将 2 块磁盘放入故障组 data1 中。

```
CREATE DISKGROUP 'DMDATA1' external REDUNDANCY FAILGROUP 'data1' asmdisk '/dev/DMDATA/disk0','/dev/DMDATA/disk1' attribute au_size=4, redo_size=128
```

  * 添加 ASM 磁盘



语法如下：

```
ALTER DISKGROUP name ADD <磁盘子句> {<磁盘子句>} [FREE_RATE num]
<磁盘子句>::= [FAILGROUP failgroup_name] ASMDISK file_path {, file_path} [PARTNER (disk_name1{,disk_name2})]
```

参数说明：

file_path：ASMDISK 路径。必须是全路径，不能是相对路径。全路径长度不能超过 256 字节。

failgroup_name：指定故障组名。如未指定故障组，则系统会自动创建一个故障组，名称为 file_path 中的磁盘名称，例如/dev/DMDATA/disk1 中的 disk1。

disk_name1、disk_name2：磁盘名。为新磁盘指定伙伴磁盘，可以是老磁盘，也可以是新磁盘。如果指定的伙伴磁盘为新磁盘，则另一个新磁盘也需要指定当前磁盘为伙伴磁盘。通过指定伙伴磁盘方式添加磁盘将不自动生成伙伴关系。

num：空闲比例值。为小于等于该指定空闲比例值的老磁盘增加一个新磁盘作为伙伴磁盘，num 取值范围为 0~100。未配置则默认为 100，表示不检查磁盘空闲空间大小；为 0 表示只为磁盘空间已满的老磁盘新增一个新磁盘作为伙伴磁盘；为 10 表示为磁盘空闲比例小于等于 10% 的老磁盘新增一个新磁盘作为伙伴磁盘。

举例说明：

例 1 为 DMDATA 磁盘组添加两块磁盘。

```
//磁盘放入故障组disk1中。
ALTER DISKGROUP 'DMDATA' ADD FAILGROUP 'data1' ASMDISK '/dev/DMDATA/disk1'
//两个磁盘分别放入故障组data1和data2中
alter diskgroup 'DMDATA' add FAILGROUP 'data1' ASMDISK '/dev/DMDATA/disk1','/dev/DMDATA/disk2' FAILGROUP 'data2' ASMDISK '/dev/DMDATA/disk3','/dev/DMDATA/disk4' FREE_RATE 10
```

例 2 如原有三个故障组，每个故障组各一个磁盘，分别为 DMASMDATA0、DMASMDATA1、 DMASMDATA2，为每个故障组各增加一块磁盘。

```
ALTER DISKGROUP 'DMDATA' ADD FAILGROUP 'data1' ASMDISK '/dev/DMDATA/disk3' PARTNER('DMASMDATA4', 'DMASMDATA5', 'DMASMDATA1') FAILGROUP 'data2' ASMDISK '/dev/DMDATA/disk4' PARTNER('DMASMDATA3', 'DMASMDATA5', 'DMASMDATA2') FAILGROUP 'data3' ASMDISK '/dev/DMDATA/disk5' PARTNER('DMASMDATA3', 'DMASMDATA4', 'DMASMDATA0')
```

> **注意**
>
> 创建磁盘组，或为磁盘组添加磁盘时，以下情况可能导致失败：  
>  1.DMASMSVRM进程没有访问对应磁盘的权限。  
>  2.磁盘路径不在DMDCR_CFG.INI配置文件中配置的DCR_DISK_LOAD_PATH路径下。  
>  3.磁盘大小不够，最少需要32M。  
>  4.磁盘名称重复。



  * 指定磁盘之间的伙伴关系



语法如下：

```
ALTER DISKGROUP SET PARTNER DISK disk_name1 WITH disk_name2{, disk_name3 WITH disk_name4}
```

参数说明：

disk_name1、disk_name2、disk_name3、disk_name4：磁盘名称。

举例说明：

指定磁盘 DMASMDATA1 和 DMASMDATA2、DMASMDATA3 和 DMASMDATA4 的伙伴关系。

```
ALTER DISKGROUP 'DMDATA' SET PARTNER DISK 'DMASMDATA1' WITH 'DMASMDATA2', 'DMASMDATA3' WITH 'DMASMDATA4'
```

  * 文件减少副本恢复



可以对单个文件或磁盘组中所有文件尝试进行减少副本异步恢复，尽可能多地恢复副本。

语法如下：

```
ALTER DISKGROUP name REPAIR [ASMFILE file_path] [POWER value]
```

参数说明：

name：磁盘组名称。

file_path：ASMDISK 路径。必须是全路径，不能是相对路径。全路径长度不能超过 256 字节。

value：指定恢复时并行度，取值范围 1~16，未指定则默认为 8。

举例说明：

指定恢复时的并行度为 2。

```
ALTER DISKGROUP 'DMDATA' REPAIR ASMFILE '+DMDATA/sample.dta' POWER 2
ALTER DISKGROUP 'DMDATA' REPAIR POWER 2
```

  * 删除 ASM 磁盘



语法如下：

```
ALTER DISKGROUP name DROP ASMDISK disk_name{,disk_name}
```

参数说明：

name：磁盘组名称。

disk_name：ASM 磁盘名，包含“DMASM”前缀的完整名称。

举例说明：

删除 DMDATA 磁盘组中的磁盘 DMASMDISK1 和 DMASMDISK2。

```
ALTER DISKGROUP 'DMDATA' DROP ASMDISK 'DMASMDISK1', 'DMASMDISK2'
```

  * 添加 DCRV 磁盘



语法如下：

```
ALTER SYSTEM DISKGROUP ADD ASMDISK file_path
```

参数说明：

file_path：DCRV 磁盘的路径。路径必须是全路径，不能是相对路径。全路径长度不能超过 256 字节。

举例说明：

在 DCRV 系统磁盘组中添加一块 DCRV 磁盘，名为 dmdcrv1。

```
alter system diskgroup add asmdisk '/dev/DMDATA/dmdcrv1'
```

  * 删除 DCRV 磁盘



语法如下：

```
ALTER SYSTEM DISKGROUP DROP ASMDISK disk_name
```

参数说明：

disk_name：ASM 磁盘名，包含“DMASM”前缀的完整名称。

举例说明：

在 DCRV 系统磁盘组中删除一块 DCRV 磁盘，名为 DMASMDCRV1。

```
alter system diskgroup drop asmdisk 'DMASMDCRV1'
```

  * 删除 ASM 磁盘组



语法如下：

```
DROP DISKGROUP name
```

参数说明：

name：磁盘组名称。

举例说明：

删除名为 DMDATA 的磁盘组。

```
drop diskgroup 'DMDATA'
```

  * 修改重平衡并行度



当磁盘资源充裕（比如大量空闲）且希望每一次重平衡操作能快速完成，可适当调大重平衡并行度。当磁盘资源紧张，为了既节约磁盘资源，又不影响数据库或其他软件的正常使用，可适当调小重平衡并行度。

语法如下：

```
ALTER DISKGROUP name SET REBALANCE POWER num
```

参数说明：

name：磁盘组名称。

num：重平衡并行度。取值范围 1~16。

举例说明：

将磁盘组 DMDATA 的重平衡并行度修改为 8。

```
alter diskgroup 'DMDATA' set rebalance power 8
```

  * 修改磁盘组自动重平衡时间间隔



用于修改 DMDCR.INI 参数 DMDCR_AUTO_RBL_TIME。

语法如下：

```
ALTER DISKGROUP SET ATTRIBUTE AUTO_RBL_TIME = value
```

参数说明：

value：修改 DMDCR_AUTO_RBL_TIME 的值。取值范围 0~86400，单位 S。

举例说明：

```
alter diskgroup set attribute auto_rbl_time = 300
```

  * 启用/禁止重平衡



语法如下：

```
ALTER DISKGROUP name REBALANCE <ENABLE | DISABLE>
```

参数说明：

name：磁盘组名称。

举例说明：

启用或禁止磁盘组 DMDATA 的重平衡功能。

```
alter diskgroup 'DMDATA' rebalance enable
```

  * 磁盘组重平衡



语法如下：

```
ALTER DISKGROUP name REBALANCE [POWER value]
```

参数说明：

name：磁盘组名称。

value：重平衡并行度。取值范围 1~16。

举例说明：

对磁盘组 DMDATA 的执行重平衡。

```
alter diskgroup 'DMDATA' rebalance power 8
```

  * 停止当前正在执行的重平衡、磁盘重连和磁盘替换



包括故障重平衡、新加磁盘重平衡、磁盘重连、磁盘替换。

语法如下：

```
ALTER SYSTEM REBALANCE STOP
```

举例说明：

```
alter system rebalance stop
```

  * 新加磁盘重平衡



语法如下：

```
ALTER DISKGROUP name REBALANCE DISK name [POWER value]
```

参数说明：

name：磁盘组名称。

value：重平衡并行度。取值范围 1~16。

举例说明：

新加磁盘后，对磁盘组 DMDATA 的执行重平衡。

```
alter diskgroup 'DMDATA' rebalance disk 'DMASMdisk1' power 8
```

  * 磁盘替换



语法如下：

```
ALTER DISKGROUP name REPLACE disk <替换子句> [POWER value]
<替换子句>：：= disk_name WITH disk_path{,disk_name WITH disk_path}
```

参数说明：

name：磁盘组名称。

disk_path：文件路径必须是新创建的磁盘，不能是已被使用的磁盘。

value：重平衡并行度。取值范围 1~16。

举例说明：

```
alter diskgroup 'DMDATA' replace disk 'DMASMdisk1' with '/dev/DMDATA/disk2'
alter diskgroup 'DMDATA' replace disk 'DMASMdisk1' with '/dev/DMDATA/disk2','DMASMdisk3' with '/dev/DMDATA/disk4'
```

  * 故障磁盘重平衡的手动触发命令



语法如下：

```
ALTER DISKGROUP name FAIL DISK REBALANCE START [POWER value]
```

参数说明：

name：磁盘组名称。

value：重平衡并行度。取值范围 1~16。

举例说明：

手动触发磁盘组 DMDATA 中的故障磁盘重平衡。

```
alter diskgroup 'DMDATA' fail disk rebalance start power 8
```

  * 重启被暂停的磁盘替换



语法如下：

```
ALTER DISKGROUP name DISK REPLACE CONTINUE [POWER value]
```

参数说明：

name：磁盘组名称。

value：重平衡并行度。取值范围 1~16。

举例说明：

重启磁盘组 DMDATA 中暂停的磁盘替换。

```
alter diskgroup 'DMDATA' disk replace continue
```

  * 磁盘重连



用于将离线磁盘重新连回磁盘组，需要保证磁盘上的数据在离线后没有发生过变化，即整块磁盘和离线时内容一致。

语法如下：

```
ALTER DISKGROUP name ONLINE DISK <重连子句> [POWER value]
<重连子句>：：= disk_name WITH disk_path{, disk_name WITH disk_path}
```

参数说明：

name：磁盘组名称。

disk_name：磁盘名称。

disk_path：文件路径必须是原磁盘所在路径。通常，磁盘路径不会发生变化，此路径即为创建磁盘组时指定的路径、lsdsk 显示的路径。

value：重平衡并行度。取值范围 1~16。

举例说明：

```
alter diskgroup 'DMDATA' online disk 'DMASMdisk1' with '/dev/DMDATA/disk1'
alter diskgroup 'DMDATA' online disk 'DMASMdisk1' with '/dev/DMDATA/disk1', 'DMASMdisk2' with '/dev/DMDATA/disk2'
```

  * 列出所有的磁盘组



语法如下：

```
LSDG
```

举例说明：

```
lsdg
```

  * 列出所有的 ASM 磁盘



语法如下：

```
lsdsk
```

举例说明：

```
lsdsk
```

  * 列出指定 ASM 磁盘的属性



语法如下：

```
lsdskattr dg_id, disk_id
```

参数说明：

dg_id：磁盘组 ID。

disk_id：磁盘 ID。

举例说明：

列出组 ID=0 和磁盘 ID=0 的磁盘的属性。

```
lsdskattr 0, 0
```

  * 测试 ASM 环境下磁盘读写速度



包括随机读/写速度和顺序读/写速度。速度会打印在屏幕上。

执行 iotest 命令时，会在当前路径下创建一个后缀名为.iotest 的临时文件，大小为 1G。支持指定条带化粒度和镜像类型，测试完成后会自动删除该临时文件。如果当前路径为”+”目录，则默认在 0 号 Disk Group 根目录下创建临时文件。更多关于 iotest 命令实现机制的介绍请参考 [10.6.4 DMASMTOOL](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html#10.6.4%20DMASMTOOL)。

出现以下情况将报错：

  1. 若磁盘空间不足 1G，则报错，创建文件失败。
  2. 若临时文件生成路径下已经存在.iotest 文件，则报错，需要手动删除后才能执行 iotest 命令。



语法如下：

```
IOTEST [DISKGROUP name][<EXTERNAL | NORMAL | HIGH> REDUNDANCY] [STRIPING value]
```

参数说明：

name：待测试的磁盘组名。缺省为测试所有磁盘组。

EXTERNAL、NORMAL、HIGH：用于指定镜像类型，即文件的副本数。分别表示单副本、两副本、三副本。缺省为 EXTERNAL。

value：指定 IOTEST 测试的条带化粒度。取值 0、32、64、128 和 256，单位 KB。用户可根据系统软硬件资源的实际情况选取条带化粒度，资源充裕时，可适当调高条带化粒度。缺省为 0，表示粗粒度。

举例说明：

测试所有磁盘组的读写速度。

```
Iotest
```

测试 DMDATA 磁盘组的读写速度。采用默认值单副本和条带化粒度 0。

```
iotest diskgroup 'DMDATA'
```

测试 DMDATA 磁盘组的读写速度。采用两副本和条带化粒度 32。

```
iotest diskgroup 'DMDATA' normal redundancy striping 32
```

#### 11.5.4.2  ASM 文件命令

  * 创建文件



语法如下：

```
CREATE ASMFILE file_path SIZE num[<EXTERNAL | NORMAL | HIGH> REDUNDANCY] [STRIPING value]
```

参数说明：

file_path：ASM 文件路径。

num：用于指定文件大小。取值范围 0~16777216*AU_SIZE，单位 MB。

EXTERNAL、NORMAL、HIGH：用于指定镜像类型，即文件的副本数。分别表示单副本、两副本、三副本。创建文件时指定的副本数需要小于等于待存储的磁盘组的副本数。缺省为单副本。

value：用于指定条带化粒度。取值 0、32、64、128 和 256。缺省为 0。

举例说明：

```
create asmfile '+DMDATA/sample.dta' size 20
或
create asmfile '+DMDATA/sample.dta' size 20 high redundancy striping 128
```

  * 扩展文件



将文件扩大到指定的大小。扩展文件时指定的 num 大小一定要比原文件更大，否则不予执行，直接退出。

语法如下：

```
ALTER ASMFILE file_path EXTEND TO num
```

参数说明：

file_path：ASM 文件路径。

num：目标大小。取值范围 0~16777216*AU_SIZE。单位 MB。

举例说明：

```
alter asmfile '+DMDATA/sample.dta' extend to 20
```

  * 截断文件



将文件截断缩小到指定大小。截断时指定的 num 大小一定要比原文件更小，否则不予执行，直接退出。

语法如下：

```
ALTER ASMFILE file_path TRUNCATE TO num
```

参数说明：

file_path：ASM 文件路径。

num：目标大小。取值范围 0~16777216*AU_SIZE。单位 MB。

举例说明：

```
alter asmfile '+DMDATA/sample.dta' truncate to 20
```

  * 删除文件



已经打开、正在使用的 ASM 文件不能被删除。

语法如下：

```
DELETE ASMFILE file_path
```

参数说明：

file_path：ASM 文件路径。

举例说明：

```
delete asmfile '+DMDATA/sample.dta'
```

  * 文件校验



查看 ASM 文件各个 AU 分布情况或校验文件是否存在缺失副本。

语法如下：

```
CHECK [file_path] [fast]
```

参数说明：

file_path：ASM 文件路径。

fast：不指定 fast 关键字则校验各镜像副本数据是否一致，否则不校验（非本地登录 asm 也不会校验）。

举例说明：

```
check
或
check +DMDATA/sample.dta
```

校验文件是否存在缺失副本（n_copy 表示文件应有副本数，r_copy 表示文件最大缺失副本数）。

```
check sample1.dta fast
file: +DATA/sample1.dta fil_id:0x80040021 n_copy:3 r_copy:1
check file consistency skipped, require local connection and without fast keyword.
(extent_no group_id [disk_id, disk_auno])
(0         0        [0, 203] [2, 203] [65535, -1])
(1         0        [1, 203] [0, 204] [65535, -1])
```

  * 重定向输出文件



重定向功能可以将工具控制台执行命令写入指定文本文件中。多次 SPOOL 重定向文件，第一次成功打开重定向文件之后，如果未关闭，则不再打开其他重定向文件。文件记录了控制台输入命令和输出结果。

语法如下：

```
SPOOL file_path [CREATE|REPLACE|APPEND]
```

参数说明：

file_path：输出文件路径。

CREATE：如果重定向文件不存在，则创建；如果已经存在，创建失败。

REPLACE：如果重定向文件不存在，则创建；如果已经存在，则替换掉。默认为 REPLACE。

APPEND：如果重定向文件不存在，则创建；如果已经存在，则追加到文件末尾。

举例说明：

```
spool d:\asmdisks\spool.txt
```

  * 关闭重定向文件



语法如下：

```
SPOOL OFF
```

举例说明：

```
spool off
```

  * 列出系统属性



系统属性包括：磁盘组 ID、AU_SIZE、系统编码语言、自动重平衡时间等。

语法如下：

```
lsattr
```

举例说明：

```
lsattr
```

  * 列出所有的信息。



所有信息包括：磁盘组、磁盘、所有文件等。

语法如下：

```
lsall
```

举例说明：

```
lsall
```

  * 修改密码



修改 DMASMSVRM 的密码。当 DMASMTOOLM 和 DMASMSVRM 不在同一台机器上时，DMASMTOOLM 登录 DMASMSVRM 时使用此密码。DMASMTOOLM 和 DMASMSVRM 在同一台机器时免密。

语法如下：

```
password
```

举例说明：

```
password
```

  * 登录，在断开连接后，重新登录



语法如下：

```
LOGIN
```

举例说明：

```
LOGIN
```

#### 11.5.4.3 Linux 兼容命令

DMASMTOOLM 兼容了部分 Linux 命令用法。这些命令对辅助管理和使用 DMASM 非常有用。

  * 到达指定目录



语法如下：

```
CD [path]
```

参数说明：

path：目标目录。缺省为当前目录。

举例说明：

```
cd +DMDATA/test
```

  * 文件拷贝



语法如下：

```
CP [<-RF>|<-R>|<-F>] src_file_path dst_file_path [<EXTERNAL | NORMAL | HIGH> REDUNDANCY]
```

参数说明：

-F：执行拷贝，不给出提示。

-R：递归拷贝，将指定目录下的子文件和子目录一并拷贝。

src_file_path：源文件路径。必须是全路径，不能是相对路径。

dst_file_path：目标文件路径。必须是全路径，不能是相对路径。

EXTERNAL、NORMAL 和 HIGH：指定镜像类型，即文件副本数。只对拷贝本地文件到 DMASM 有效。缺省为单副本。

举例说明：

```
cp '+DMDATA/a/sample.dta' '+DMDATA/a/b.dta'
cp -r '+DMDATA/a/newfile.dat' '+DMDATA/b'
cp -f '+DMDATA/a/*' '+DMDATA/c'
cp '/home/test/a.txt' '+DMDATA' HIGH REDUNDANCY
```

  * 删除文件或目录



语法如下：

```
RM [-F] file_path
RM -R[F] directories
```

参数说明：

-F：执行拷贝，不给出提示。

-R：递归拷贝，将指定目录下的子文件和子目录一并拷贝。

file_path：待删除的文件。

directories：待删除的目录。

举例说明：

```
rm '+DMDATA/a/sample.dta'
rm -r '+DMDATA/a/'
```

  * 重命名文件



语法如下：

```
MV src_file_path dst_file_path
```

参数说明：

src_file_path：源文件路径。

dst_file_path：目标文件路径。

举例说明：

```
mv '+DMDATA/a/sample.dta' '+DMDATA/a/sample2.dta'
```

  * 创建目录



语法如下：

```
MKDIR [-P] dir_path
```

参数说明：

file_path：目标目录。

-p：表示自动创建不存在的中间目录。如未指定-P 且不存在中间目录，则报错。

举例说明：

```
mkdir '+DMDATA/a'
mkdir -p '+DMDATA/a'
```

  * 查找文件



语法如下：

```
FIND PATH file_name
```

参数说明：

file_name：目标文件名。

举例说明：

```
find +DMDATA/a 'sample.dta'
```

  * 显示文件详细信息



语法如下：

```
LS [<-L>|<-R>|<-LR>] [dir_path]
```

参数说明：

-L：显示详细信息。

-R：表示递归的意思，不是 linux 中的逆序显示。

dir_path：目标文件路径。

举例说明：

```
ls
ls -l
ls -r b*
```

  * 显示存储信息



语法如下：

```
DF
```

举例说明：

```
df
```

  * 当前目录



语法如下：

```
PWD
```

举例说明：

```
pwd
```

  * 估算文件空间已使用情况



语法如下：

```
DU [dir_path{,dir_path}]
```

参数说明：

dir_path：要估算的文件目录，未指定则默认为当前目录

举例说明：

```
du
du DMDATA
du DMDATA,DMLOG
du DMDATA DMLOG

```

估算结果中，出现的单位有缺省（字节）、K 和 M 等情况。

```
ASM>du
0	 +DATA/dsc_mirror2/bak
18.0K	 +DATA/dsc_mirror2/ctl_bak
0	 +DATA/dsc_mirror2/HMAIN
660.1M	 +DATA/dsc_mirror2
792.1M	 +DATA
```

## 11.6 DMASM 镜像配置文件

镜像环境下需用到 DMDCR_CRG.INI、DMDCR.INI、DMINIT.INI、DMARCH.INI、MAL 系统配置文件和 DM.INI 配置文件。其中，DMDCR_CRG.INI、DMDCR.INI、DMINIT.INI 和 DMARCH.INI 配置文件中增加了镜像相关参数。MAL 系统配置文件和 DM.INI 参数未发生改变，与 [9 DMDSC 配置文件](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-configuration-instructions.html)中用法完全相同。

增加了镜像参数的配置文件包括：

  * DMDCR_CFG.INI
  * DMDCR.INI
  * DMINIT.INI
  * DMARCH.INI



下面分别介绍配置文件中各配置项的含义。

### 11.6.1 DMDCR_CFG.INI

DMDCR_CFG.INI 是 DMASMCMDM 格式化 DCRV 磁盘的配置文件。配置信息包括三类：集群环境全局信息、集群组信息、以及组内节点信息。

在 DMASM 镜像环境中，DMDCR_CFG.INI 取消了 DCR_EP_ASM_LOAD_PATH 和 DCR_VTD_PATH 参数，新增了 DCR_DISK_LOAD_PATH 参数。除此之外，其他的 DMDCR_CFG.INI 参数保持不变，请参考 [9 DMDSC 配置文件](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-configuration-instructions.html)。

表11.3 DMDCR_CFG.INI配置项



举例说明

一个完整的 DMDCR_CFG.INI 文件。

```
DCR_N_GRP                   = 3
DCR_DISK_LOAD_PATH        = /dev/DMDATA/
DCR_OGUID                   = 63635
   
[GRP]						#[GRP]表示新建一个Group
    DCR_GRP_TYPE            = CSS
    DCR_GRP_NAME            = GRP_CSS
    DCR_GRP_N_EP            = 2
    DCR_GRP_DSKCHK_CNT      = 60
   
[GRP]
    DCR_GRP_TYPE            = ASM
    DCR_GRP_NAME            = GRP_ASM
    DCR_GRP_N_EP            = 2
    DCR_GRP_DSKCHK_CNT      = 60

[GRP]
    DCR_GRP_TYPE            = DB
    DCR_GRP_NAME            = GRP_DSC
    DCR_GRP_N_EP            = 2
    DCR_GRP_DSKCHK_CNT      = 60
  
[GRP_CSS]					#[ ]里的是组名，与DCR_GRP_NAME对应
    DCR_EP_NAME             = CSS0
    DCR_EP_HOST             = 192.168.1.102
    DCR_EP_PORT             = 9941
  
[GRP_CSS]
    DCR_EP_NAME             = CSS1
    DCR_EP_HOST             = 192.168.1.102
    DCR_EP_PORT             = 9942   

[GRP_ASM]
    DCR_EP_NAME             = ASM0
    DCR_EP_SHM_KEY          = 78910
    DCR_EP_SHM_SIZE         = 10
    DCR_EP_HOST             = 192.168.1.102
    DCR_EP_PORT             = 9949
   
[GRP_ASM]
    DCR_EP_NAME             = ASM1
    DCR_EP_SHM_KEY          = 78911
    DCR_EP_SHM_SIZE         = 10
    DCR_EP_HOST             = 192.168.1.102
    DCR_EP_PORT             = 9950
  
[GRP_DSC]
    DCR_EP_NAME             = DSC0
    DCR_EP_PORT             = 5236

[GRP_DSC]
    DCR_EP_NAME             = DSC1
    DCR_EP_PORT             = 5237
```

### 11.6.2   DMDCR.INI

DMDCR.INI 是 DMCSSM、DMASMSVRM、DMASMTOOLM 等工具的输入参数。记录了当前节点序列号以及 DCR 磁盘路径。

在 DMASM 镜像环境中，对 DMDCR_PATH、DMDCR_IPC_CONTROL 参数进行了进一步定义，同时新增了 DMDCR_AUTO_RBL_TIME 参数。除此之外，其他的 DMDCR.INI 参数保持不变，请参考 [9 DMDSC 配置文件](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-configuration-instructions.html)。

表11.4 镜像环境中新增的DMDCR.INI配置项

|**字段**|**字段意义**|
|----|----|
|DMDCR_PATH|记录 DCR 磁盘路径。和 DMDCR_CFG.INI 里 DCR_DISK_LOAD_PATH 相同。支持多路径配置，各路径以英文逗号','进行分隔，所有配置的路径都必须为有效路径，且暂不支持中文路径，例如：/dev/disk1,/dev/disk2,/dev/disk3。最多可以同时配置 8 个磁盘扫描路径，但配置的所有路径长度之和必须小于 256，若超过该长度，只能减小磁盘扫描路径个数|
|DMDCR_IPC_CONTROL|进程间互斥实现方式，0 表示用全局信号量控制；1 表示用全局互斥量控制；2 表示用全局互斥量控制，并且有分片功能。默认为 2|
|DMDCR_AUTO_RBL_TIME|镜像自动重平衡时间间隔，单位秒，取值范围 0~86400s，缺省或配置 0 则不进行自动重平衡。主从节点配置不一致以主 ASM 节点配置为准|


### 11.6.3   DMINIT.INI

DMINIT.INI 是 DMINIT 工具初始化数据库环境的配置文件。

在 DMASM 镜像环境中，DMINIT.INI 中新增了 CTL_MIRROR、DATA_MIRROR、LOG_MIRROR、DATA_STRIPING 和 LOG_STRIPING 参数，修改了 DCR_PATH 参数。除此之外，其他的 DMINIT.INI 参数保持不变，请参考 [9 DMDSC 配置文件](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-configuration-instructions.html)。

表11.5 镜像环境中新增的DMINIT.INI配置项



### 11.6.4   DMARCH.INI

DMARCH.INI 是开启本地归档和远程归档使用的配置文件。

在 DMASM 镜像环境中，DMARCH.INI 中新增了 ARCH_ASM_MIRROR 和 ARCH_ASM_STRIPING 参数。除此之外，其他的 DMARCH.INI 参数保持不变，请参考 [9 DMDSC 配置文件](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-configuration-instructions.html)。

表11.6 DMARCH.INI的配置项



## 11.7 DMASM 镜像使用说明

DMASM 镜像的配置和使用大致分为三步。

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

目录一 使用 dmdba 用户创建用于镜像环境搭建的目录，不同机器上此目录必须相同。

目录二 DM 执行码和工具存放于目录，不同机器上此目录必须相同。

目录三 配置文件存放于目录，不同的机器上此目录可以不同。

  * 磁盘准备



首先使用多路径软件为共享存储的磁盘创建目录名，即将磁盘链接到一个目录中。然后将磁盘的用户权限修改为和目录的用户权限一致。

第二步 搭建 DMASM 镜像

搭建按照下面顺序依次进行：

  1. 使用 DMASMCMDM 工具创建所有磁盘。具体为 DCRV 磁盘和 ASM 磁盘，其中 ASM 磁盘用于存放数据和日志。
  2. 使用 DMASMCMDM 工具创建 DCRV 系统磁盘组。用于统一管理 DCRV 磁盘。
  3. 配置并启动 DMCSS。
  4. 配置并启动 DMASMSVRM。
  5. 使用 DMASMTOOLM 工具创建 ASMDISK 磁盘组。具体为 DATA 磁盘组和 LOG 磁盘组两种，分别管理 DATA 磁盘和 LOG 磁盘。在创建磁盘组的时候指定副本数。
  6. 配置并启动 DMSERVER。
  7. 配置并启动 DMCSSM。



至此，一个使用了 DMASM 镜像的 DMDSC 部署完成。

第三步 正常使用数据库

用户登录到任意一个 DM 实例，即可获得完整的达梦数据库服务。同时，用户可通过两种方式来管理 ASM 文件：一是编写用户代码，通过调用 DMASMAPIM 接口来管理 ASM 文件；二是使用 DMASMCMDM 和 DMASMTOOLM 工具来管理 ASM 文件。

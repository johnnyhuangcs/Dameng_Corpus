

# DMDSC 注意事项

DMDSC 集群针对同一份数据，提供了多个活动的数据库实例，因此具有负载均衡、高可靠性等特征。但与单机系统相比，DMDSC 具有更复杂的架构、更多的组件，因此对 DMDSC 集群的使用也提出了更高的要求。本章主要说明使用 DMDSC 集群的一些注意事项。

## 17.1 统一组件版本

搭建 DMDSC 环境时，应注意 DMDSC 中各实例使用的 DM 服务器版本应一致，同时还应注意各实例所在主机的操作系统位数、大小端模式、时区及时间设置都应一致。

此外，DMDSC 集群包含 DMCSS、非镜像环境下的 DMASMSVR/DMASMAPI/DMSERVER、镜像环境下的 DMASMSVRM/DMASMAPIM/DMSERVERM 等诸多组件。并且 DMASMSVR 和 DMASMAPI 之间，DMASMSVRM 和 DMASMAPIM 之间均需要用到共享内存进行数据交换。因此，搭建和使用 DMDSC 集群时，要特别注意各个组件的版本号是否一致。是否同为 32 位或 64 位。如果各组件版本号不一致，可能会导致系统异常，无法正常使用。

## 17.2 提升 DMDSC 性能

DMDSC 通过缓存交换和全局事务管理等机制，协调、管理多个节点的 CPU、内存等计算资源，以提升数据库管理系统的事务处理能力。数据和信息需要在节点间通过网络频繁地进行传输，如果多个数据库实例过多地访问、修改相同的数据页，就会导致缓存交换频繁，内部网络流量过高，形成 DMDSC 集群的性能瓶颈。为了提升 DMDSC 集群的整体性能，建议：

1.使用带宽更大、速度更快的网络设备，降低数据和信息在节点之间传递的从网络延迟，比如使用 InfiniBand 或者万兆的以太网。

2.根据业务逻辑，把不同类别的数据库请求分别部署到不同的节点，减少不同节点对相同数据页或者数据库对象的共享访问、和修改。

3.优化应用逻辑和每一句 SQL，DMDSC 集群并不是解决性能问题的万能方案，未经优化、糟糕的 SQL 查询可能会极大降低数据库的吞吐量。

4.充分分析应用系统特征，以及性能瓶颈在哪里。比如一些 IO 密集型的应用系统，性能瓶颈往往出现在存储的 IO 上，这种情况下单纯地增加 DMDSC 节点数并不会提高性能，反而可能由于节点数增加，缓存交换更加频繁，进一步降低系统性能。针对这种情况，正确的选择是增加磁盘，将 IO 分散到更多的磁盘；或者使用速度更快的固态盘来提升 IO 性能。

5.使用’ALTER TABLE XXX WITHOUT COUNTER’语句，关闭频繁插入、删除记录表的快速计数功能。DM8 默认开启表快速记录功能，系统动态维护表上的记录总数，并将记录数写入到表的控制页中；DMDSC 环境开启这个功能，并且多个节点并发向同一张表插入记录时，控制页的访问权限在节点间不断地进行回收和授权，控制页的内容也会不断地在节点间进行传递，进而影响 DMDSC 整体的并发性能。

## 17.3 心跳说明

DMDSC 集群中，DMCSS 依赖心跳机制来判断集群中的各个实例是否处于正常状态。DMDSC 集群支持磁盘心跳检测机制。

磁盘心跳的载体是共享存储上的 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下），DMCSS、DMASMSVR、和 DMSERVER 启动后，每间隔 1 秒往 VOTE 磁盘或 DCRV 磁盘各自固定的偏移写入时间戳，DMCSS 定时读取 VOTE 磁盘或 DCRV 磁盘信息，根据时间戳变化情况来判断被监控对象是否活动。实例故障认定时间取决于配置参数 DCR_GRP_DSKCHK_CNT，一般建议 DCR_GRP_DSKCHK_CNT 至少配置为 60 秒以上，避免在系统极度繁忙情况下，由于操作系统调度原因导致误判实例故障。在规划 DMDSC 集群存储时，最好将磁盘（VOTE 或 DCRV）与 IO 密集的数据库文件和日志文件分开存放，分别保存在不同的物理磁盘上，避免由于数据库 IO 影响心跳信息的写入和读取，导致极端情况下误判实例故障。

DMDSC 集群中磁盘心跳检测失败，DMCSS 会认为对应实例故障，启动故障处理流程。

## 17.4 重新格式化 DMASM

DMASM 文件系统格式是自描述的，DMASMSVR 启动时会从指定路径扫描块设备，根据块设备的头信息判读是否是 ASM 磁盘，进而获取 ASM 磁盘组元数据信息，初始化 DMASM 文件系统。所以要求一套硬件环境只能搭建一套 DMASM 文件系统，否则会产生冲突，会导致 dmasmsvr 启动失败。如果要重新初始化 DMASM 文件系统，需要将指定路径下的所有块设备头信息格式化一遍，避免新老环境 ASM 磁盘信息冲突。

如果出现磁盘 id 相同导致 dmasmsvr 启动失败的情况，在可以确定某一磁盘为无效磁盘的情况下，用 dmasmcmd 工具执行 create asmdisk disk_path name 的方式清理无效磁盘信息。

## 17.5 重新初始化 DMDSC 库

DMSERVER 发生异常的情况下，DMCSS 会根据配置的心跳时间来确认节点故障，并写入故障信息到 DCR Disk 中，然后执行对应的故障处理或故障恢复。

对于 DMSERVER 节点全部故障的情况，如果想直接重新初始化数据库，则需要手动清理 DCR Disk 中的故障信息（DCR Disk 没有重新初始化），避免 DMCSS 对新初始化的库再次执行故障处理。

注意手动清理的时机，需要在 DMCSS 写入故障信息到 DCR Disk 之后，否则手动清理完成后，DMCSS 再写入故障信息（DMSERVER 的心跳时间配置比较长时会出现这种情况），清理操作仍然起不到作用。

DMCSS 手动清理之前，可通过下面三种方式避免 DMCSS 在清理之后再次修改 DCR Disk 中的故障信息：

  1. 等待配置的心跳时间之后，确保 DMCSS 已写入故障信息之后，再执行手动清理。
  2. 确定 DMSERVER 确实故障时，通过监视器的 ep halt 命令，通知 DMCSS 写入故障信息，然后再执行手动清理。
  3. 直接退出 DMCSS，在手动清理完成之后再重启 DMCSS。



可使用以下两种方式对指定组的故障节点信息进行清理：

  1. 通过 dmasmcmd 的 export 命令，将 DCR Disk 的配置信息导出，手动修改 DMSERVER 对应组中的 DCR_GRP_N_ERR_EP 值为 0，DCR_GRP_ERR_EP_ARR 内容为空，则通过 asmcmd 的 import 命令导入到 DCR Disk 中。
  2. 通过 dmasmcmd 的 clear 命令，直接清理指定组的故障节点信息。



命令的具体用法请参考 [10.6.1 DMASMCMD](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html#10.6.1%20DMASMCMD) 的用法说明。

## 17.6 内部网络异常

DMDSC 环境多个 DMSERVER 之间的很多功能都通过 MAL 系统来传递控制命令或数据（事务管理、封锁管理等等，详见 [4 DMDSC 关键技术](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-technology.html)。如果网络故障导致 DMSERVER 间 MAL 通讯失败，出现 MAL 邮件丢失等情况，会导致 DMSERVER 一直收不到请求响应，整个系统卡住。

遇到这种情况，DMSERVER 会通过 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）通知 DMCSSMAL 链路故障。两节点实例中，由 DMCSS 挑选出节点号大的 DMSERVER 强制其主动 HALT 退出，保留节点号小的 DMSERVER。例如，0，1 两节点，会杀掉 1 号节点。对于两节点实例来说，接下来进行故障处理，将 DMDSC 集群恢复为单节点，尽快对外提供数据库服务。如果多节点实例中，需要根据网络联通状态来进一步判断，最终保留节点数比较多的组。

由于 DMSERVER 的这种主动通知机制，DMDSC 系统可能会比 MAL_CONN_FAIL_INTERVAL 和 MAL_CHECK_INTERVAL 设置值更早发现内部网络故障，从而进行处理。

## 17.7 创建 DBLink

DMDSC 系统可支持 DBLink 的创建和访问。DMDSC 系统和单节点之间、DMDSC 和 DMDSC 系统之间都支持 DBLink 的创建，由于同构的 DBLink 之间依赖于 MAL 系统，只需要通过配置 DMMAL.INI 文件即可实现。将每个实例 mal 项都加入 DMMAL.INI 文件，并拷贝到每个实例目录下，即可创建 DBLink。

DMDSC 和单节点的 DBLink 配置，需在 DSC 节点的 DMMAL.INI 中加入单节点 mal 项，同时复制到单节点，打开单节点的 MAL 配置，全部实例重新启动后即可创建 DBLink。

举例如下：

```
[MAL_INST1]
MAL_INST_NAME              = DSC01
MAL_HOST                    = 10.0.2.101
MAL_PORT                    = 7236
MAL_INST_HOST              = 192.168.0.101
MAL_INST_PORT              = 5336
MAL_LINK_MAGIC             = 0
[MAL_INST2]
MAL_INST_NAME              = DSC02
MAL_HOST                    = 10.0.2.102
MAL_PORT                    = 7237
MAL_INST_HOST              = 192.168.0.102
MAL_INST_PORT              = 5336
MAL_LINK_MAGIC             = 0
[MAL_INST3]   #单节点的配置项
MAL_INST_NAME              = EP01
MAL_HOST                    = 10.0.2.103
MAL_PORT                    = 7238
MAL_INST_HOST              = 192.168.0.103
MAL_INST_PORT              = 5336
	MAL_LINK_MAGIC             = 0
```

两个 DMDSC 系统之间也类似，需在一套 DMDSC 的 DMMAL.INI 中加入另一套的 DMMAL.INI 内容，并拷贝到两套 DSC 系统的所有实例。

举例如下：

```
[MAL_INST1]
MAL_INST_NAME              = DSC011
MAL_HOST                    = 10.0.2.101
MAL_PORT                    = 7236
MAL_INST_HOST              = 192.168.0.101
MAL_INST_PORT              = 5336
MAL_LINK_MAGIC             = 0
[MAL_INST2]
MAL_INST_NAME              = DSC012
MAL_HOST                    = 10.0.2.102
MAL_PORT                    = 7237
MAL_INST_HOST              = 192.168.0.102
MAL_INST_PORT              = 5337
MAL_LINK_MAGIC             = 0

#另一套DSC系统
[MAL_INST3]
MAL_INST_NAME              = DSC021
MAL_HOST                    = 10.0.2.102
MAL_PORT                    = 7238
MAL_INST_HOST              = 192.168.0.102
MAL_INST_PORT              = 5338
MAL_LINK_MAGIC             = 0
[MAL_INST4]
MAL_INST_NAME              = DSC022
MAL_HOST                    = 10.0.2.101
MAL_PORT                    = 7239
MAL_INST_HOST              = 192.168.0.101
MAL_INST_PORT              = 5339
	MAL_LINK_MAGIC             = 0
```

某些应用场景下，DBLINK 需要跨网段访问数据库实例，我们只要根据实际情况，修改 MAL 配置参数，将处于相同网段实例的 MAL_LINK_MAGIC 配置为相同的值，不同网段实例的 MAL_LINK_MAGIC 配置为不同的值，就可以实现跨网段访问。详情可参考《DM8 系统管理员手册》DMMAL.INI 配置章节。

## 17.8 节点硬件故障，如何启动 DMDSC 集群

正常的 DMDSC 集群启动流程，dmcss 会在监控到所有节点都启动后，再执行正常的数据库启动流程。当某个节点出现硬件故障无法启动时，DMDSC 集群无法自动启动。针对这种情况，dmcssm 监视器提供了 open force 命令，通知 dmcss 将未启动节点设置为故障状态，强制启动活动节点，以尽快恢复数据库服务。

## 17.9 MOUNT/OPEN 操作

登录 DSC 下任意节点都可以执行 MOUNT/OPEN 操作，仅在登录节点生效，如果需要所有节点都 MOUNT 或者 OPEN，需要登录所有节点执行命令。

## 17.10 裸设备路径变化

基于 DMASM 的 DMDSC 在运行过程中，如果磁盘重新进行规划，可能会导致磁盘对应的块设备名称有变化，从而导致 DMASM 环境配置可能也需要进行改变。

DMASM 文件系统是自描述系统，会自动扫描/dev/raw/下的所有块设备，根据头信息来获取信息，和块设备名路径没有关系。只是 DCR 和 VOTE 磁盘的路径必须指定，若这两个路径没有变化，可以不用修改 DMASM 环境配置。

如果 DCR 和 VOTE 磁盘的路径发生了改变，则需要用 DMASMCMD 工具重新格式化 DCR 和 VOTE 磁盘，不会影响其他 ASM 磁盘，也不影响已经存在的 ASM 文件。格式化后需要对 DCR 和 VOTE 磁盘进行初始化：

● 如果保存了原始的 dmdcr_cfg.ini 文件，修改 dmdcr_cfg.ini 的 DCR_VTD_PATH 路径信息，再用 dmasmcmd 工具初始化就可以；

● 如果没有保存原始 dmdcr_cfg.ini 文件，可以用 dmasmcmd 工具先 export 出一份 dmdcr_cfg.ini 文件，再修改其中 DCR_VTD_PATH 路径信息，然后重新初始化。

## 17.11 滚动升级

DMDSC 可以确保在不中断数据库服务的情况下，实现滚动升级。

滚动升级过程中，各节点轮流进行升级，最后升级主控 CSS 所在节点。将待升级的 DMDSC 节点单独退出并升级，活动节点继续对外提供服务。

滚动升级操作步骤如下：

以两节点的 DMDSC 举例说明，假设节点 2 为主控 CSS 所在节点。

第一步 退出节点 1、升级节点 1、再启动节点 1。此时活动节点 2 继续对外提供服务。

首先，正常退出本节点上的各服务(dmcss、dmasmsvr 和 dmserver)。先退出 dmserver，可通过执行 SQL 语句“stop instance;”完成，再依次退出 DMASMSVR 和 DMCSS。

其次，重新安装新版本服务器，或者直接将新的执行程序和动态库替换旧版本。

最后，再启动本节点上的各服务。依次启动 dmcss、dmasmsvr 和 dmserver。

第二步 退出节点 2、升级节点 2、再启动节点 2。此时活动节点 1 继续对外提供服务。

节点 2 的升级步骤和节点 1 完全相同。至此，两节点的 DMDSC 滚动升级完成。

## 17.12  正常 HALT 现象

以下 DMDSC 情景中，节点出现 HALT 属于正常现象，符合预期。

情景一 节点 A 不是 OPEN 状态（MOUNT/SUSPEND），如果此时出现其他节点故障，且故障处理无法进行，那么节点 A 会主动 HALT。

情景一 系统长时间处于 SUSPEND 状态（例如：数据守护为自动切换模式，且没有配置确认监视器），日志刷盘执行不了，致使数据页 REMOTE READ 无法进行，并最终导致节点 HALT。

## 17.13 ASM 磁盘版本

当 DM 针对 ASM 磁盘做了较大改进时，会升级 ASM 磁盘版本号（磁盘版本号增大），可以通过查询 V$ASMDISK 视图获取 ASM 磁盘版本号。

DM 自 V8.1.3.129 版本起，将 ASM 磁盘版本号升级至 4100。对 ASM 磁盘做的主要改进为针对磁盘头信息进行偏移调整。ASM 磁盘需要在磁盘头部位置写入标识信息，老版本 ASM 磁盘会在 0 号偏移位置写入标识信息，新版本磁盘则往后偏移固定位置写入标识信息：普通数据磁盘往后偏移 32M 开始写入信息，DCR 和 VOTE 磁盘往后偏移 1M 开始写入信息。

当数据库服务器版本为 V8.1.3.129 或更高版本时，如果 DCR 和 VOTE 磁盘版本号低于 4100，则需要使用 DMASMCMD 工具重新初始化 DCR 和 VOTE 磁盘，否则 DCR 和 VOTE 磁盘将无法正常使用。初始化的步骤说明请参考 [18.1.2 升级步骤](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-version-upgrade.html#18.1.2%20%E5%8D%87%E7%BA%A7%E6%AD%A5%E9%AA%A4)。

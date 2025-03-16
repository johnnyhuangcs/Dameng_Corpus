

# DMDSC 故障处理

## 7.1  故障处理

DMDSC 集群出现数据库实例或者节点硬件故障时，DMSERVER 的 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）心跳信息不再更新，DMCSS 一旦监控到 DMSERVER 发生故障，会马上启动故障处理（参考 [5 DMCSS 介绍](https://eco.dameng.com/document/dm/zh-cn/pm/css-introduction.html)），各节点 DMSERVER 收到故障处理命令后，启动故障处理流程。

在 DMDSC 故障处理机制下，一旦产生节点故障，登录到故障节点的所有连接将会断开，所有未提交事务将被强制回滚；活动节点上的用户请求可以继续执行，但是一旦产生节点间信息传递（比如：向故障节点发起 GBS/LBS 请求、或者发起 Remote Read 请求），当前操作就会被挂起；在 DMDSC 故障处理完成后，这些被挂起的操作可以继续执行。也就是说，DMDSC 产生节点故障时，活动节点上的所有连接会保留，正在执行的事务可能被阻塞一段时间，但最终可以正常完成，不会被强制回滚，也不会影响结果的正确性。

DMDSC 环境 DMSERVER 故障处理主要包括以下工作：

  1. 暂停所有会话线程、工作线程、日志刷盘线程、检查点线程等，避免故障处理过程中产生并发错误；
  2. 收集所有活动节点的 Buffer，丢弃无效数据页，获取最新数据页和需要重做 REDO 日志的数据页信息，并在排除故障节点后重新构造 LBS/GBS 信息；
  3. 重做 REDO 日志；
  4. 收集所有节点事务信息，重新构造锁对象，并重构相应的 LLS/GLS/LTV/GTV 系统；
  5. 控制节点执行故障节点活动事务回滚和故障节点已提交事务修改的 PURGE 操作。



DMDSC 故障处理分为三个阶段；第一阶段由所有活动节点共同参与，进行全局的信息收集、重构；第二阶段由控制节点执行，重演 REDO 日志；第三阶段由控制节点执行，将故障节点的活动事务回滚、并 PURGE 故障节点已提交事务的修改。在第一阶段执行期间，数据库实例不提供数据库服务，所有用户请求将被挂起。根据故障处理策略（由 INI 参数 DSC_CRASH_RECV_POLICY 控制）的不同，在第二阶段操作之前，会选择是否唤醒所有活动节点，正常提供数据库服务，也就是说故障处理第二阶段的操作与正常的数据库操作在系统内部同时进行。默认故障处理策略时，第二阶段重做所有节点 REDO 日志，且在此期间数据库服务挂起，直到第三阶段结束重新唤醒所有活动节点。选择新的故障处理策略，第二阶段只重做故障节点 REDO 日志，且在第二阶段开始前唤醒所有活动节点。第三阶段执行完成之前，DMDSC 故障处理仍然没有真正结束，在此期间，不能处理节点重加入。

DMDSC 支持多节点连续故障处理，即在故障处理过程中剩余活动节点又出现新的节点故障时，DMDSC 可以继续完成故障处理，不会造成其余活动节点退出，连接和事务的处理方法不变。需要注意的是，节点重加入流程中出现节点故障和集群非 OPEN 状态出现节点故障并不是多节点连续故障，仍然会导致节点 HALT。DMCSS 监控到发生节点连续故障后，立即发出故障回滚命令中止当前的故障处理流程。各节点 DMSERVER 收到故障回滚命令后，回滚当前正在执行的操作，中止日志重做和本次故障处理，重置和清理部分内容以便于执行下次故障处理。所有节点全部回滚完成后，DMCSS 发起新的对当前所有故障节点的故障处理命令。DMSERVER 收到新的故障处理命令后，重新开始故障处理流程，并根据情况跳过部分步骤。

> **说明**
>
> 1.通常意义上讲的故障处理，包含故障检测和故障处理两部分。  
>  2.故障检测时间由DCR_GRP_DSKCHK_CNT决定。  
>
> 3.影响故障处理时间的因素比较多，主要包括：故障节点REDO日志数量、修改涉及的数据页数量、需要ROLLBACK、PURGE的事务数，以及活动节点Buffer使用情况等。



## 7.2  故障日志

在 DMDSC 集群中出现实例故障时，DMCSS/DMASM/DMSERVER 实例都会打印大量日志对于故障原因以及故障处理过程进行说明，这些日志目前已添加“[!!!DSC INFO!!!]”关键标识。由于日志量过大，本节内容将对于故障处理过程中的特定日志以及故障排查流程进行相关说明，便于当 DMDSC 集群出现实例故障时，服务人员能够快速进行故障原因排查。

集群的绝大多数故障原因都会打印在 DMCSS/DMCSSM 监视器上，因此在进行故障检查时一般先检查 DMCSS/DMCSSM 日志，通过一些特定的关键日志找出故障节点数目、站点号以及故障节点类型。可以先通过[!!!DSC INFO!!!]关键字进行日志初步筛选，再根据关键日志信息，进行相关方向排查。

下面介绍一些常用的故障日志以及相应的故障排查方向。

### 7.2.1 实例故障

日志格式：

```
!!!DSC INFO!!!][CSS]: dead instance detected, detect ep %s[%d] broken.

例：[!!!DSC INFO!!!][CSS]: dead instance detected, detect ep CSS0[0] broken.
```

日志说明：

DMCSS/DMCSSM 出现此条日志信息，说明对应实例已故障，一般在 DMCSSM 中也有相关的故障原因打印，根据故障日志的信息可进行相应方向故障排查。常见的故障原因有掉电、网络故障、存储异常等。若 DMCSSM 中没有直接的故障原因打印，可直接通过故障实例的日志查找实例故障原因，根据[!!!DSC INFO!!!]关键字或节点 halt 特定日志"[for dem]SYSTEM SHUTDOWN ABORT"进行日志筛查，快速定位故障原因。

### 7.2.2   实例组故障

日志格式：

```
[!!!DSC INFO!!!]All processers on EP[%d] have detected disk heartbeat issues.

例：[!!!DSC INFO!!!]All processers on EP[1] have detected disk heartbeat issues.
```

日志说明：

DMCSS/DMCSSM 出现此条日志信息，说明整个实例组，即属于这个实例组的 DMCSS/DMASM/DMSERVER 都已故障，一般在 DMCSSM 中也有相关的故障原因打印，根据故障日志的信息可进行相应方向故障排查。常见的故障原因有掉电、网络故障、存储异常等，可对于整个实例组进行整体排查。该故障打印信息与上述的单个实例故障打印信息一般不同时出现。

### 7.2.3   共享存储异常

日志格式：

```
[%s][!!!DSC INFO!!!]Unable to access VOTING DISK! disk may be not mounted, suggest to check shared storage.

例：[CSS0][!!!DSC INFO!!!]Unable to access VOTING DISK! disk may be not mounted, suggest to check shared storage.
```

日志说明：

DMCSS/DMCSSM 出现此条日志信息，说明可能由于磁盘未挂载等原因导致共享存储异常，VTD 磁盘信息无法正常写入而导致实例故障，可以对于共享储存进行故障原因排查。

### 7.2.4   网络异常

日志格式：

```
[!!!DSC INFO!!!]Network connect to IP address %s has been disconnected.

或 [!!!DSC INFO!!!]Network interface with IP address %s no longer running.

例：[!!!DSC INFO!!!]Network connect to IP address 0.0.0.0 has been disconnected.

或 [!!!DSC INFO!!!]Network interface with IP address 0.0.0.0 no longer running.
```

日志说明：

实例（DMCSS/DMASM/DMSERVER）出现此条日志信息，说明集群中出现了网络故障。该日志常与 DMCSS/DMCSSM 监视器上的打印日志“设置命令[LINK_CHECK], 目标站点...”以及“The [%d]th subnet of network connectivity of [ep_name]:[%d] ERROR”日志共同出现。当实例故障后，若查看 DMCSS/DMCSSM 监视器出现此类日志，需要对于故障节点的网络进行相关排查。

### 7.2.5   机房整体故障

日志格式：

```
[!!!DSC INFO!!!]CSSM detected all EP inactive, the entire cluster may be faulty or the entire cluster has already exited
```

日志说明：

DMCSSM 出现此条日志信息，说明有可能是机房整体故障，或者机房整体实例安全退出，可对于整体集群状态进行排查。

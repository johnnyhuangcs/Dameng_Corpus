

# DMCSS 介绍

达梦集群同步服务（Dameng Cluster Synchronization Services，简称 DMCSS）使用 DMASM 集群或 DMDSC 集群都必须配置 DMCSS 服务。在 DMASM 集群或 DMDSC 集群中，每个节点都需要配置一个 DMCSS 服务。这些 DMCSS 服务自身也构成一个集群，DMCSS 集群中负责监控、管理整个 DMASM 集群和 DMDSC 集群的节点称为控制节点(Control Node)，其他 DMCSS 节点称为普通节点(Normal Node)。DMCSS 普通节点不参与 DMASM 集群和 DMDSC 集群管理，当 DMCSS 控制节点故障时，会从活动的普通节点中重新选取一个 DMCSS 控制节点。

DMCSS 工作的基本原理是：在 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）中，为每个被监控对象（DMASMSVR、DMSERVER、DMCSS）分配一片独立的存储区域，被监控对象定时向 VOTE 或 DCRV 磁盘写入信息（包括时间戳、状态、命令、以及命令执行结果等）；DMCSS 控制节点定时从 VOTE 或 DCRV 磁盘读取信息，检查被监控对象的状态变化，启动相应的处理流程；被监控对象只会被动的接收 DMCSS 控制节点命令，执行并响应。

DMCSS 主要功能包括：写入心跳信息、选举 DMCSS 控制节点、选取 DMASM/DMDSC 控制节点、管理被监控对象的启动流程、集群状态监控、节点故障处理、节点重加入等，DMCSS 还可以接收并执行 DMCSSM 指令。

## 5.1 启动命令

```
格式: dmcss.exe KEYWORD=value
例如: dmcss.exe DCR_INI=/home/data/DAMENG/dmdcr.ini
关键字说明（默认）
----------------------------------------------------------------
DCR_INI            dmdcr.ini路径
DFS_INI            dmdfs.ini文件路径
-NOCONSOLE         以服务方式启动
HELP               打印帮助信息
```

## 5.2 心跳信息

DMCSS 实例启动后，每间隔 1 秒向 Voting Disk 指定区域写入心跳信息（包括自身的状态、时间戳等），表示 DMCSS 节点处于活动状态。

## 5.3 选举 DMCSS 控制节点

DMCSS 启动后向 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）写入信息，并读取其他 DMCSS 节点的信息，如果 DMCSS 集群中还没有活动的控制节点，则选举 DMCSS 控制节点。DMCSS 选举的原则有两条：

  1. 先启动的 DMCSS 作为控制节点。
  2. 如果 DMCSS 同时启动，那么则选择节点号小的节点为控制节点。
  3. 如果 DMCSS 控制节点挂掉，那么会将先向 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）写入心跳信息的节点设置为控制节点；若同时有多个节点先向 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）写入心跳信息，那么选择节点号小的节点为控制节点。



## 5.4 选取监控对象控制节点

DMCSS 控制节点启动后，会为基于 DMASM/裸设备的 DMDSC 集群指定控制节点。DMCSS 选取监控对象控制节点的原则有两条：

  1. 只有一个活动节点，则设置活动节点为控制节点。
  2. 存在多个活动节点，则选择节点号小的节点为控制节点。



## 5.5 启动流程管理

DMASM 和 DMDSC 集群中的实例启动后，一直处于 waiting 状态，等待 DMCSS 的启动命令。DMCSS 控制节点在选取监控对象控制节点后，通知控制节点启动，在控制节点启动完成后，再依次通知其他普通节点启动。

## 5.6 状态检测

DMCSS 维护集群状态，随着节点活动信息的变化，集群状态也会产生变化，DMCSS 控制节点会通知被监控节点执行不同命令，来控制节点启动、故障处理、故障重加入等操作。

DMCSS 控制节点每秒从 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）读取被监控对象的心跳信息。一旦被监控对象的时间戳在 DCR_GRP_DSKCHK_CNT 秒内没有变化，则认为被监控对象出现异常。

DMCSS 普通节点定时读取 DMCSS 控制节点的心跳信息，监控 DMCSS 运行状态。

## 5.7 故障处理

DMCSS 控制节点检测到实例故障后，首先向故障实例的 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）区域写入 Kill 命令（所有实例一旦发现 Kill 命令，无条件自杀），避免故障实例仍然处于活动状态，引发脑裂，然后启动故障处理流程，不同类型实例的故障处理流程存在一些差异。

  * DMCSS 控制节点故障处理流程



  1. 活动节点重新选举 DMCSS 控制节点
  2. 新的 DMCSS 控制节点通知出现 DMCSS 故障节点对应的 dmasmsvr、dmserver 强制退出



  * DMASMSVR 实例故障处理流程



  1. 挂起工作线程
  2. 更新 DCR（参考 [10 DMASM 介绍](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html)）故障节点信息
  3. 通知故障节点对应 DMSERVER 强制退出
  4. DMASMSVR 进行故障恢复
  5. 恢复工作线程



  * DMSERVER 实例故障处理流程



  1. 更新 DCR 故障节点信息
  2. 重新选取一个控制节点
  3. 通知 DMSERVER 控制节点启动故障处理流程（参考 [7 DMDSC 故障处理](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-fault-handling.html)）
  4. 等待 DMSERVER 故障处理结束



## 5.8 节点重加入

如果检测到故障节点恢复，DMCSS 会通知控制节点启动节点重加入流程。

  * 数据库实例重加入



  1. 挂起工作线程
  2. 修改节点的状态为故障节点重加入状态
  3. 执行恢复操作
  4. 节点进入 STARTUP 状态，准备启动
  5. OPEN 重加入的节点
  6. 重启工作线程
  7. 执行 OPEN 数据库实例的操作



  * DMASM 实例重加入



  1. 挂起工作线程
  2. 修改节点的状态为故障节点重加入状态
  3. 执行恢复操作
  4. 节点进入 STARTUP 状态，准备启动
  5. OPEN 重加入的节点
  6. 重启工作线程



## 5.9 集群指令

DMCSS 控制节点通过一系列的集群指令控制被监控对象的启动、故障处理、状态切换等。DMCSS 控制节点向目标对象的 VOTE 磁盘（非镜像环境下）或 DCRV 磁盘（镜像环境下）指令区写入命令，通知目标对象执行相应命令，并等待执行响应。每条指令的功能都比较单一，比如修改状态、设置控制节点、执行一条 SQL 等，复杂的集群流程控制就是由这些简单的指令组合起来完成的。

## 5.10 状态查看

在 DMCSS 控制台输入 show 命令可以看到所监控的集群状态。DMCSS 控制节点会显示 DMCSS、DMASM、DMDSC 三个集群的信息，而 DMCSS 普通节点只会显示 DMCSS 集群的信息。

具体显示的内容如下所示。

group[]行显示的内容为：

name： 集群名称。

seq：   集群编号。

type：   集群组类型。包含：CSS、ASM、DB。

control_node： 集群内控制节点。

ep 行显示的内容为：

inst_name：节点实例名。

seqno：节点编号。

port：实例对外提供服务的端口号。

mode：节点模式。包括：CONTROL（控制节点）、NORMAL（普通节点）。

inst_status：实例系统状态。包括：INSTALL（初始化数据库）、STARTUP（节点启动）、AFTER REDO（启动，REDO 完成）、MOUNT（挂载）、OPEN（正常打开）、SUSPEND（挂起）、SHUTDOWN（关闭）、CRASH_RECV（出现节点故障）、INVALID EP STATUS（集群非 OPEN 状态下，不支持故障处理，可能是集群同步服务 DMCSS 或监视器 DMCSSM 版本过旧导致）。

vtd_status：实例的集群状态。包括：STARTUP（启动）、STARTUP2（启动第二步）、WORKING（工作中）、TO SHUTDOWN（准备关闭）、SHUTDOWN（已经关闭）、SYSHALT（系统崩溃）、STOP（停止）、ADD_DCRV（添加 DCRV 磁盘）、DEL_DCRV（删除 DCRV 磁盘）。

is_ok：实例在集群内是否正常。OK 是，ERROR 否。ERROR 节点暂时从集群内踢出。

active： 实例是否活动。TRUE 是，FALSE 否。

guid：实例的 GUID 值。

ts：实例的时间戳。

```
======= group[name = GRP_CSS, seq = 0, type = CSS, Control Node = 0] ===================
ep:     inst_name  seqno  port  mode     inst_status    vtd_status    is_ok     active    guid           ts
        CSS1       0      8060  Control Node OPEN          WORKING       OK        TRUE      670744319      670748263   
        CSS2       1      8061  Normal Node OPEN          WORKING       OK        TRUE      670744832      670748770   
        CSS3       2      8062  Normal Node OPEN          WORKING       OK        TRUE      670745332      670749266   

self css info:
[ASM1] auto restart = FALSE
[RAC1] auto restart = FALSE

======= group[name = GRP_ASM, seq = 1, type = ASM, Control Node = 0] ===================
ep:     inst_name  seqno  port  mode     inst_status    vtd_status    is_ok     active    guid           ts
        ASM1       0      8063  Control Node OPEN          WORKING       OK        TRUE      670746265      670750197   
        ASM2       1      8064  Normal Node OPEN          WORKING       OK        TRUE      670746960      670750891   
        ASM3       2      8065  Normal Node OPEN          WORKING       OK        TRUE      670747502      670751432   
===================================================================================

n_ok_ep = 3
(0, 0)
(1, 1)
(2, 2)
sta = OPEN, sub_sta = STARTUP
break ep = NULL
recover ep = NULL
crash process over flag is TRUE
```

## 5.11 注意事项

如果节点 A 的 DMCSS 退出或者故障，活动 DMCSS 会给节点 A 上所监控的 DMASMSVR 和 DMSERVER 发送 halt 命令，确保节点 A 上的 DMASMSVR 和 DMSERVER 自动退出。

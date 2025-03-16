

# 监控 DMDSC

DMDSC 集群的运行情况可以通过 DMCSSM 监视器（Dameng Cluster Synchronization Services Monitor，DMCSSM）进行查看，也可以查询 DMDSC 相关的动态视图获取更详细的信息。DMCSSM 监视器支持一些控制命令，可以用来启动、关闭 DMDSC 集群，还可以进行手动控制节点故障处理和节点重加入。

## 15.1 DMCSSM 监视器

配置了 DMCSS 的集群中，可配置 DMCSSM 对集群进行统一管理，可配置 0~10 个 DMCSSM。各 DMCSSM 作用一样，相互独立，互不干扰。

### 15.1.1 功能说明

  1. 监控集群状态



DMCSS 每秒会发送集群中所有节点的状态信息、当前连接到 DMCSS 的监视器信息以及 DCR 的配置信息到活动的监视器上，监视器提供对应的 show 命令用于查看各类信息。

  2. 打开/关闭指定组的自动拉起



DMCSSM 提供 SET AUTO RESTART ON/SET AUTO RESTART OFF 命令，通知 DMCSS 打开或关闭对指定组的自动拉起功能，此功能和 DMCSS 的监控打开或关闭没有关系。

  3. 强制 OPEN 指定组



DMCSSM 提供 OPEN FORCE 命令，在启动 ASM 或 DB 组时，如果组中某个节点发生硬件故障等原因导致一直无法启动，可执行此命令通知 DMCSS 将 ASM 组或 DB 组强制 OPEN，不再等待故障节点启动成功。

  4. 启动/退出集群



DMCSSM 提供 EP STARTUP/EP STOP 命令，可以通知 DMCSS 启动/退出指定的 ASM 或 DB 组。

  5. 集群故障处理



DMCSSM 提供 EP BREAK/EP RECOVER 命令，在主 CSS 的监控功能被关闭的情况下，可以通过执行这些命令手动进行故障处理和故障恢复。另外在某些特殊场景下还可通过 EP HALT 命令强制退出指定节点，具体可参考 [15.1.4 命令说明](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-monitor.html#15.1.4%20%E5%91%BD%E4%BB%A4%E8%AF%B4%E6%98%8E)。

### 15.1.2 配置文件 DMCSSM.INI

DMCSSM 的配置文件名称为 dmcssm.ini，所支持的配置项说明如下。

表15.1 dmcssm.ini配置项

|**配置项**|**配置含义**|
|----|----|
|CSSM_OGUID|用于和 DMCSS 通信校验使用，和 DMDCR_CFG.INI 中的 DCR_OGUID 值保持一致|
|CSSM_CSS_IP|集群中所有 DMCSS 所在机器的 IP 地址，以及 DMCSS 的监听端口，配置格式为“IP:PORT”的形式，其中 IP 和 PORT 分别对应 DMDCR_CFG.INI 中 DMCSS 节点的 DCR_EP_HOST 和 DCR_EP_PORT。如果使用 IPv6 地址，为了方便区分端口，需要用[]封闭 IP 地址。对于 IPv6，若当前环境存在多块网卡，需要用 % 号指定具体有效的网卡序号或网卡名称；若只有一块网卡或者已配置默认网卡，则可以不指定序号或名称。例如：CSSM_CSS_IP = [fe80::6aa7:3f02:59b3:bcb4%3]:52184|
|CSSM_LOG_PATH|日志文件路径，日志文件命名方式为 “dmcssm_年月日时分秒.log”，例如“dmcssm_20160614131123.log”。  如果 DMCSSM.INI 中配置有 CSSM_LOG_PATH 路径，则将 CSSM_LOG_PATH 作为日志文件路径，如果没有配置，则将 DMCSSM.INI 配置文件所在的路径作为日志文件路径|
|CSSM_LOG_FILE_SIZE|单个日志文件大小，取值范围 16~2048，单位为 MB，缺省为 64MB，达到最大值后，会自动生成并切换到新的日志文件中|
|CSSM_LOG_SPACE_LIMIT|日志总空间大小，取值 0 或者 256~4096，单位为 MB。缺省为 0，表示没有空间限制。如果达到设定的总空间限制，会自动删除创建时间最早的日志文件|
|CSSM_MESSAGE_CHECK|是否对 CSSM 通信消息启用通信体校验（只有当消息的发送端和接收端都配置为 1 才启用通信体校验）。0：不启用；1：启用。缺省为 1|


> **说明**
>
>
> 在有日志写入操作时，如果日志路径下没有日志文件，会自动创建一个新的日志文件，如果已经有日志文件，则根据设定的单个日志文件大小（CSSM_LOG_FILE_SIZE）决定继续写入已有的日志文件或者创建新的日志文件写入。  
> 创建新的日志文件时，根据设定的日志总空间大小（CSSM_LOG_SPACE_LIMIT）决定是否删除创建时间最早的日志文件。



### 15.1.3 配置步骤

同一个 DMDSC 集群中，允许最多同时启动 10 个监视器，建议监视器放在独立的第三方机器上，避免由于节点间网络不稳定等原因导致监视器误判节点故障。

下面举例说明监视器的配置步骤，以 [12 DMDSC 搭建](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html)中搭建的 2 节点 DSC 环境为基础，配置对应的监视器，监视器放在第三方机器上，为 windows 操作系统，DMCSSM.INI 配置文件路径为 D:\cssm，可根据实际情况调整配置环境及路径。

  1. 配置 DMCSSM.INI 文件



```
#和DMDCR_CFG.INI中的DCR_OGUID保持一致
CSSM_OGUID = 63635 

#配置所有CSS的连接信息，
#和DMDCR_CFG.INI中CSS配置项的DCR_EP_HOST和DCR_EP_PORT保持一致
CSSM_CSS_IP = 10.0.2.101:9341
CSSM_CSS_IP = 10.0.2.102:9343

CSSM_LOG_PATH =D:\cssm\log		#监视器日志文件存放路径
CSSM_LOG_FILE_SIZE = 32			#每个日志文件最大32M
CSSM_LOG_SPACE_LIMIT = 0		#不限定日志文件总占用空间
```

  2. 启动 dmcssm 监视器



```
dmcssm.exe INI_PATH=D:\cssm\dmcssm.ini
```

### 15.1.4 命令说明

监视器提供一系列命令，支持集群的状态信息查看以及节点的故障处理，可输入 help 命令，查看命令使用说明。

表15.2 监视器命令

|**命令名称**|**含义**|
|----|----|
|help|显示帮助信息|
|show [group_name]|显示指定的组信息，如果没有指定 group_name，则显示所有组信息|
|show config|显示 dmdcr_cfg.ini 的配置信息|
|show monitor|显示当前连接到主 CSS 的所有监视器信息|
|set group_name auto restart on|打开指定组的自动拉起功能（只修改 dmcss 内存值）|
|set group_name auto restart off|关闭指定组的自动拉起功能（只修改 dmcss 内存值）|
|open force group_name|强制 open 指定的 ASM 或 DB 组|
|ep startup group_name|启动指定的 ASM 或 DB 组|
|ep stop group_name|退出指定的 ASM 或 DB 组|
|ep halt group_name.ep_name|强制退出指定组中的指定节点|
|extend node|联机扩展节点|
|ep crash group_name.ep_name|手动指定节点故障|
|check crash over group_name|检查指定组故障处理是否真正结束|
|exit|退出监视器|


命令使用说明：

  1. help



显示帮助信息。

  2. show [group_name]



显示指定的组信息，如果没有指定 group_name，则显示所有组信息。

返回的组信息优先从主 CSS 获取，如果主 CSS 故障或尚未选出，则任选一个从 CSS 返回组信息。

  3. show config



显示 DMDCR_CFG.INI 的配置信息，对于 DB 类型的节点，会比 CSS/ASM 节点多一项 DCR_EP_SEQNO 的显示值，如果原本的 ini 文件中没有手动配置，则显示的是自动分配的序列值。

返回的配置信息优先从主 CSS 获取，如果主 CSS 故障或尚未选出，则任选一个从 CSS 返回信息。

  4. show monitor



显示当前连接到主 CSS 的所有监视器信息，如果主 CSS 故障或尚未选出，则任选一个从 CSS 显示连接信息。

返回的信息中，第一行为当前执行命令的监视器的连接信息。

在数据守护环境中，守护监视器 dmmonitor 的部分命令（启动/停止/强杀实例）最终是由 dmcss 执行的，守护进程 dmwatcher 在命令执行中充当了 dmcssm 的角色，守护进程通知 dmcss 执行完成后，再将执行结果返回给守护监视器。因此 dmcss 上会有 dmwatcher 的连接信息，show monitor 命令也会显示 dmwatcher 的连接信息，可以根据 from_name 字段值进行区分。

  5. set group_name auto restart on



打开指定组的自动拉起功能。

可借助 show css 命令查看每个节点的自动拉起标记，每个 css 只能控制和自己的 DMDCR.INI 中配置的 DMDCR_SEQNO 相同节点的自动拉起。

  6. set group_name auto restart off



关闭指定组的自动拉起功能。

可借助 show css 命令查看每个节点的自动拉起标记，每个 css 只能控制和自己的 DMDCR.INI 中配置的 DMDCR_SEQNO 相同节点的自动拉起。

  7. open force group_name



在启动 ASM 或 DB 组时，如果某个节点故障一直无法启动，可借助此命令将 ASM 或 DB 组强制 OPEN。

此命令需要发送到主 CSS 执行，并且主 CSS 的监控需要处于打开状态，如果主 CSS 故障或尚未选出，则命令执行失败。

  8. ep startup group_name



通知 CSS 启动指定的 ASM 或 DB 组，如果 CSS 已经打开了指定组的自动拉起功能，则命令不允许执行，需要等待 CSS 自动检测故障并执行拉起操作。

每个 CSS 只负责拉起和自己的 dmdcr.ini 中配置的 DMDCR_SEQNO 相同的 ASM 或 DB 节点，因此需要所有 CSS 都处于活动状态，否则只通知当前活动的 CSS 自动拉起相对应的节点。

> **说明**
>
>
> 只有在ASM组正常启动到OPEN状态，并且所有活动的ASM节点都处于OPEN状态时，才允许启动DB组，否则执行DB组的启动命令会报错，CSS自动拉起DB组时也需要满足此条件。  
> 在命令执行前，如果CSS对指定组的自动拉起功能是关闭的，在节点拉起成功后，会打开对指定组的自动拉起功能。



  9. ep stop group_name



退出指定的 ASM 或 DB 组，如果主 CSS 故障或尚未选出，则命令执行失败。

> **注意**
>
> 在退出ASM组时，需要保证DB组已经退出，否则会报错处理。  
>
> 在命令执行前，如果CSS对指定组的自动拉起功能是打开的，则会先通知CSS关闭对指定组的自动拉起功能，再通知指定组退出，避免命令执行成功后节点再次被自动拉起。



  10. ep halt group_name.ep_name



强制退出指定组的指定 EP。适用于下述场景：

场景一 某个 ASM 或 DB 集群节点故障，CSS 的心跳容错时间 DCR_GRP_DSKCHK_CNT 配置值很大，在容错时间内，CSS 不会调整故障节点的 active 标记，一直是 TRUE，CSS 认为故障 EP 仍然处于活动状态，不会自动执行故障处理，并且不允许手动执行故障处理。

另外，执行 EP STARTUP 或 EP STOP 命令时，会误认为故障 EP 仍然处于活动状态，导致执行结果与预期不符。此时可以通过执行 EP HALT 命令，通知 CSS 再次 HALT 故障 EP，确认 EP 已经被 HALT 后，CSS 会及时调整 active 标记为 FALSE，在此之后，对自动/手动故障处理，EP STARTUP/EP STOP 命令都可以正常执行。

场景二 需要强制 HALT 某个正在运行的 ASM 或 DB 节点，也可以通过此命令完成。

  11. extend node



DMDSC 集群联机增加节点时使用。

程序会通知所有实例(CSS/ASMSVR/dmserver)更新 dcr 信息。

使用 show 命令能看到新增节点信息，新增 ASMSVR/dmserver 为 ERROR 状态。

  12. ep crash group_name.ep_name



手动指定节点故障，节点故障后，css 只有在 DCR_GRP_DSKCHK_CNT 配置的时间过后才会判定实例故障，开始故障处理流程。用户如果明确知道实例已经故障，可以收到执行此命令，CSS 可以立即开始故障处理流程。

  13. check crash over group_name



DB 集群环境故障处理后，需要满足一定条件（控制节点重做 REDO 日志产生的数据页修改都已经刷盘完成），才允许故障节点重新加回集群环境。此命令用来显示 DB 集群环境故障处理是否真正结束。

  14. exit



退出监视器。

## 15.2 动态视图

DMDSC 集群提供一系列动态视图来查看当前的系统运行信息。部分视图是全局的，任意节点登录查询的结果都是相同的，部分视图仅显示登录节点的信息。

### 15.2.1   DMDSC 通用视图

#### 15.2.1.1   V$DSC_EP_INFO

显示实例信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|EP_NAME|VARCHAR(128)|实例名称|
|2|EP_SEQNO|INTEGER|DSC 节点序号|
|3|EP_GUID|BIGINT|EP 唯一标识码|
|4|EP_TIMESTAMP|BIGINT|EP 时间戳|
|5|EP_MODE|VARCHAR(32)|EP 模式，CONTROL NODE 或 NORMAL NODE|
|6|EP_STATUS|VARCHAR(32)|EP 状态，OK 或 ERROR|


#### 15.2.1.2   V$DSC_GBS_POOL

显示 GBS 控制结构的信息，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|N_CTL|INTEGER|GBS 控制块总数|
|2|N_FREE_CTL|INTEGER|空闲的 GBS 控制块数目|
|3|N_SUB_POOL|INTEGER|GBS_POOL 个数|


#### 15.2.1.3   V$DSC_GBS_POOLS_DETAIL

显示分片的 GBS_POOL 详细信息，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|N_USED_CTL|INTEGER|正在使用的 GBS 控制块数目|
|3|N_REQUEST|INTEGER|正在等待 GBS 控制块的请求数目|
|4|N_FREE_REQUEST|INTEGER|空闲的 GBS 请求控制块数目|


#### 15.2.1.4   V$DSC_GBS_CTL

显示 GBS 控制块信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_OWNER|INTEGER|拥有权限的 EP 数|
|11|N_REQUEST|INTEGER|请求授权的 EP 数|
|12|N_REVOKING|INTEGER|正在回收权限的 EP 数|
|13|N_REVOKE_X_ONLY|INTEGER|页的优化次数|


#### 15.2.1.5   V$DSC_GBS_CTL_DETAIL

显示 GBS 控制块详细信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_OWNER|INTEGER|拥有权限的 EP 数|
|11|N_REQUEST|INTEGER|请求授权的 EP 数|
|12|N_REVOKING|INTEGER|正在回收权限的 EP 数|
|13|TYPE|VARCHAR(32)|详细信息类型（OWNER/REQUEST/REVOKING）|
|14|MODE|INTEGER|封锁模式，0/1/2/4：N_LATCH/X_LATCH/S_LATCH/F_LATCH|
|15|EP_SEQNO|INTEGER|拥有、请求、或者回收封锁的 EP|
|16|REAL_FLUSH|CHAR|是否真正执行刷盘请求（Y/N）|
|17|N_REVOKE_X_ONLY|INTEGER|页的优化次数|


#### 15.2.1.6   V$DSC_GBS_CTL_LRU_FIRST

显示 GBS 控制块 LRU 链表首页信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_OWNER|INTEGER|拥有权限的 EP 数|
|11|N_REQUEST|INTEGER|请求授权的 EP 数|
|12|N_REVOKING|INTEGER|正在回收权限的 EP 数|
|13|N_REVOKE_X_ONLY|INTEGER|页的优化次数|


#### 15.2.1.7   V$DSC_GBS_CTL_LRU_FIRST_DETAIL

显示 GBS 控制块 LRU 链表首页详细信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_OWNER|INTEGER|拥有权限的 EP 数|
|11|N_REQUEST|INTEGER|请求授权的 EP 数|
|12|N_REVOKING|INTEGER|正在回收权限的 EP 数|
|13|TYPE|VARCHAR(32)|详细信息类型（OWNER/REQUEST/REVOKING）|
|14|MODE|INTEGER|封锁模式，0/1/2/4: N_LATCH/X_LATCH/S_LATCH/F_LATCH|
|15|EP_SEQNO|INTEGER|拥有、请求、或者回收封锁的 EP|
|16|REAL_FLUSH|CHAR|是否真正执行刷盘请求（Y/N）|
|17|N_REVOKE_X_ONLY|INTEGER|页的优化次数|


#### 15.2.1.8   V$DSC_GBS_CTL_LRU_LAST

显示 GBS 控制块 LRU 链表尾页信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_OWNER|INTEGER|拥有权限的 EP 数|
|11|N_REQUEST|INTEGER|请求授权的 EP 数|
|12|N_REVOKING|INTEGER|正在回收权限的 EP 数|
|13|N_REVOKE_X_ONLY|INTEGER|页的优化次数|


#### 15.2.1.9   V$DSC_GBS_CTL_LRU_LAST_DETAIL

显示 GBS 控制块 LRU 链表尾页详细信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_OWNER|INTEGER|拥有权限的 EP 数|
|11|N_REQUEST|INTEGER|请求授权的 EP 数|
|12|N_REVOKING|INTEGER|正在回收权限的 EP 数|
|13|TYPE|VARCHAR(32)|详细信息类型（OWNER/REQUEST/REVOKING）|
|14|MODE|INTEGER|封锁模式，0/1/2/4： N_LATCH/X_LATCH/S_LATCH/F_LATCH|
|15|EP_SEQNO|INTEGER|拥有、请求、或者回收封锁的 EP|
|16|REAL_FLUSH|CHAR|是否真正执行刷盘请求（Y/N）|
|17|N_REVOKE_X_ONLY|INTEGER|页的优化次数|


#### 15.2.1.10   V$DSC_GBS_REQUEST_CTL

显示等待 GBS 控制块的请求信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|GBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|MODE|INTEGER|封锁模式，0/1/2/4： N_LATCH/X_LATCH/S_LATCH/F_LATCH|
|6|EP_SEQNO|INTEGER|请求封锁的 EP|
|7|FREQ_CONFLICT|INTEGER|是否为高频冲突数据页|


#### 15.2.1.11   V$DSC_GBS_FREQ_CFLT

显示高频冲突数据页的历史信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|EPSEQ|INTEGER|节点号|
|2|POOL_ID|INTEGER|GBS POOL 序号|
|3|TS_ID|INTEGER|表空间号|
|4|FILE_ID|INTEGER|文件号|
|5|PAGE_NO|INTEGER|数据页号|
|6|EP_FROM|INTEGER|被延迟处理的 LBS 请求节点|
|7|LOCK_MODE|INTEGER|封锁模式，0/1/2/4：  N_LATCH/X_LATCH/S_LATCH/F_LATCH|
|8|N_FREQ_REVOKE|INTEGER|DSC_FREQ_INTERVAL 时间内，产生的 REVOKE 请求次数，用于统计高频冲突数据页，与 INI 参数 DSC_FREQ_CONFLICT 对应|
|9|FREQ_INTERVAL|INTEGER|最近第 N_FREQ_REVOKE 次冲突的产生时间|


#### 15.2.1.12   V$DSC_LBS_POOL

显示 LBS 控制结构的信息，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|N_CTL|INTEGER|LBS 控制块总数|
|2|N_FREE_CTL|INTEGER|空闲的 LBS 控制块数目|
|3|N_SUB_POOL|INTEGER|LBS_POOL 个数|
|4|N_FAST_REQUEST|INTEGER|LBS 请求本地直接命中的次数|
|5|N_GBS_REQUEST|INTEGER|LBS 请求发送给 GBS 节点的次数|
|6|N_PAGE_CREATE_REQUEST|INTEGER|创建数据页请求次数|
|7|TOTAL_REQUEST|INTEGER|总的 LBS 请求次数|


#### 15.2.1.13   V$DSC_LBS_POOLS_DETAIL

显示分片的 LBS_POOL 详细信息。多个 pool，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|LBS_POOL 编号|
|2|N_USED_CTL|INTEGER|正在使用的 LBS 控制块数目|
|3|N_REQUEST_SPACE|INTEGER|正在等待 LBS 控制块的请求数目|
|4|N_FREE_REQUEST|INTEGER|空闲的 LBS 请求控制块数目|
|5|N_FAST_REQUEST|INTEGER|LBS 请求本地直接命中的次数|
|6|N_GBS_REQUEST|INTEGER|LBS 请求发送给 GBS 节点的次数|
|7|N_PAGE_CREATE_REQUEST|INTEGER|创建数据页请求次数|
|8|TOTAL_REQUEST|INTEGER|总的 LBS 请求次数|


#### 15.2.1.14   V$DSC_LBS_CTL_LRU_FIRST

显示 LBS 的 LRU_FIRST 控制块信息。多个 POOL，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|LBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_FIXED|INTEGER|引用计数|
|11|MODE|INTEGER|获得 GBS 授权的封锁模式|
|12|PHY_LSN|BIGINT|数据页上的最新 LSN 值|
|13|N_REQUEST|INTEGER|请求获得授权的工作线程数|
|14|N_REVOKE_X_ONLY|INTEGER|页的优化次数|


#### 15.2.1.15   V$DSC_LBS_CTL_LRU_LAST

显示 LBS 的 LRU_LAST 控制块信息。多个 POOL，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|LBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_FIXED|INTEGER|引用计数|
|11|MODE|INTEGER|获得 GBS 授权的封锁模式|
|12|PHY_LSN|BIGINT|数据页上的最新 LSN 值|
|13|N_REQUEST|INTEGER|请求获得授权的工作线程数|


#### 15.2.1.16  V$DSC_LBS_CTL_DETAIL

显示 LBS 控制块详细信息。多个 POOL，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|LBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_FIXED|INTEGER|引用计数|
|11|MODE|INTEGER|获得 GBS 授权的封锁模式|
|12|PHY_LSN|BIGINT|数据页上的最新 LSN 值|
|13|N_REQUEST|INTEGER|请求获得授权的工作线程数|
|14|REQUEST_MODE|INTEGER|请求的封锁模式|
|15|REVOKE_LSN|BIGINT|回收权限时，GBS 上的最新 LSN 值|
|16|N_REMOTE_READ|INTEGER|数据页 REMOTE READ 的次数，数值越大，说明该数据页在节点间冲突越高|
|17|TOTAL_REQUEST|INTEGER|数据页的总访问次数|


#### 15.2.1.17   V$DSC_LBS_CTL_LRU_FIRST_DETAIL

显示 LBS 的 LRU_FIRST 控制块详细信息。多个 POOL，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|LBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_FIXED|INTEGER|引用计数|
|11|MODE|INTEGER|获得 GBS 授权的封锁模式|
|12|PHY_LSN|BIGINT|数据页上的最新 LSN 值|
|13|N_REQUEST|INTEGER|请求获得授权的工作线程数|
|14|REQUEST_MODE|INTEGER|请求的封锁模式|
|15|REVOKE_LSN|BIGINT|回收权限时，GBS 上的最新 LSN 值|


#### 15.2.1.18   V$DSC_LBS_CTL_LRU_LAST_DETAIL

显示 LBS 的 LRU_LAST 控制块详细信息。多个 POOL，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|LBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_FIXED|INTEGER|引用计数|
|11|MODE|INTEGER|获得 GBS 授权的封锁模式|
|12|PHY_LSN|BIGINT|数据页上的最新 LSN 值|
|13|N_REQUEST|INTEGER|请求获得授权的工作线程数|
|14|REQUEST_MODE|INTEGER|请求的封锁模式|
|15|REVOKE_LSN|BIGINT|回收权限时，GBS 上的最新 LSN 值|


#### 15.2.1.19   V$DSC_GTV_SYS

显示 GTV 控制结构的信息，仅登录集群环境控制节点才能获取数据，登录其他节点返回数据无效。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|T_INFO_NUM|INTEGER|系统已提交、未 PURGE 事务所修改的表对象个数|
|2|NEXT_TRXID|BIGINT|下一个事务 ID|
|3|MAX_PURGABLE_TRIXID|BIGINT|最大可 PURGE 的事务 ID|
|4|UNDO_TRIXID|BIGINT|回滚段中，正在被访问的最小事务 ID|
|5|UNDO_CNT|INTEGER|UNDO_TRXID 被设置的次数|


#### 15.2.1.20   V$DSC_LBS_CTL

显示 LBS 控制块信息。多个 POOL，依次扫描，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|POOL_ID|INTEGER|LBS_POOL 编号|
|2|TS_ID|INTEGER|表空间号|
|3|FILE_ID|INTEGER|文件号|
|4|PAGE_NO|INTEGER|页号|
|5|ACCESS_MAP|INTEGER|曾经访问此数据页的 EP|
|6|FRESH_EP|INTEGER|最新数据所在 EP|
|7|FRESH_LSN|BIGINT|最新修改对应的 LSN 值|
|8|N_REPLACED|INTEGER|控制块被替换次数|
|9|N_REVOKED|INTEGER|权限被回收次数|
|10|N_FIXED|INTEGER|引用计数|
|11|MODE|INTEGER|获得 GBS 授权的封锁模式|
|12|PHY_LSN|BIGINT|数据页上的最新 LSN 值|
|13|N_REQUEST|INTEGER|请求获得授权的工作线程数|
|14|N_REMOTE_READ|INTEGER|数据页 REMOTE READ 的次数，数值越大，说明该数据页在节点间冲突越高|
|15|TOTAL_REQUEST|INTEGER|数据页总的访问次数|
|16|N_REVOKE_X_ONLY|INTEGER|页的优化次数|
|17|REVOKE_DELAY|INTEGER|BS_CTL 延迟权限回收的次数|


#### 15.2.1.21   V$DSC_LOCK

显示全局活动的事务锁信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|EP_SEQNO|INTEGER|拥有该锁的节点号|
|2|ADDR|BIGINT|锁地址|
|3|TRX_ID|BIGINT|所属事务 ID|
|4|LTYPE|VARCHAR(10)|锁类型：TID 锁、对象锁|
|5|LMODE|CHAR(2)|锁模式：S 锁、X 锁、IX 锁、IS 锁|
|6|BLOCKED|INTEGER|是否阻塞|
|7|TABLE_ID|INTEGER|对应表锁、字典对象 ID|
|8|ROW_IDX|BIGINT|被封锁事务 ID|
|9|IGN_FLAG|INTEGER|锁对象的 IGNORABLE 标记，INI 参数 LOCK_DICT_OPT 开启时有效。取值 0：表示锁正在使用中，IGN_FLAG 最低位置为 0；取值 1：表示事务 TRX1 已经提交，但是锁资源未释放，TRX1 重新封锁时可以直接使用，此时 IGN_FLAG 最低位置为 1；取值 2：为 0+2 的组合值。当锁正被使用时，另一事务要封锁同一对象，将当前正被使用的锁的 IGN_FLAG 次低位置为 1；取值 3：为 1+2 的组合值。当 TRX1 已提交，但锁未释放时，另一事务 TRX2 要封锁同一对象，此时 TRX2 可以忽略此 IGN_FLAG=1 的锁，但是要将此锁的 IGN_FLAG 次低位置为 1|


#### 15.2.1.22   V$DSC_TRX

显示所有活动事务的信息。通过该视图可以查看所有系统中所有的事务以及相关信息，如锁信息等，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|EP_SEQNO|INTEGER|事务所在节点号|
|2|ID|BIGINT|当前活动事务的 ID 号|
|3|NEXTID|BIGINT|下一个事务 ID 号|
|4|MIN_ACTIVE_ID|BIGINT|所有活动事务 ID 号最小者|
|5|STATUS|VARCHAR(20)|当前事务的状态|
|6|ISOLATION|INTEGER|事务的隔离级别  0：读未提交  1：读提交  2：可重复读  3：串行化|
|7|READ_ONLY|CHAR(1)|是否为只读事务（Y/N）|
|8|SESS_ID|BIGINT|当前事务所在的会话 ID|
|9|SESS_SEQ|INTEGER|会话序列号，用来唯一标识会话|
|10|INS_CNT|INTEGER|插入回滚记录个数|
|11|DEL_CNT|INTEGER|删除回滚记录个数|
|12|UPD_CNT|INTEGER|更新回滚记录个数|
|13|UPD_INS_CNT|INTEGER|更新插入回滚记录个数|
|14|UREC_SEQNO|INTEGER|当前 Undo 记录的递增序列号|
|15|WAITING|BIGINT|事务等待的锁|
|16|THRD_ID|INTEGER|当前事务对应的线程 ID|


#### 15.2.1.23 V$DSC_TRXWAIT

显示事务等待信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|EP_SEQNO|INTEGER|事务所在节点号|
|2|TRX_ID|BIGINT|事务 ID|
|3|WAIT_FOR_ID|BIGINT|所等待的事务 ID|
|4|WAIT_SEQNO|TINYINT|等待的事务序列号|
|5|WAIT_TIME|INTEGER|当前已等待时间，单位毫秒|
|6|TRX_OBJ|BIGINT|事务对象|
|7|WAIT_TRX_OBJ|BIGINT|等待事务对象|


#### 15.2.1.24   V$DSC_TRX_VIEW

显示当前事务可见的所有活动事务视图信息。根据达梦多版本规则，通过该视图可以查询系统中自己所见的事务信息；可以通过与 v$dsc_trx 表的连接查询它所见事务的具体信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|SELF_ID|BIGINT|活动事务 ID|
|2|EP_SEQNO|INTEGER|可见事务所在节点号|
|3|ACTIVE_ID|BIGINT|所见的事务活动事务 ID|


#### 15.2.1.25  V$DCR_INFO

查看 DCR 配置的全局信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|VERSION|INTEGER|DCR 版本号|
|2|N_GROUP|INTEGER|DCR 配置的组个数|
|3|VTD_PATH|VARCHAR(256)|VOTE 磁盘路径|
|4|UDP_FLAG|INTEGER|是否使用 UDP 心跳机制，已无效|
|5|UDP_OGUID|BIGINT|校验用|
|6|DCR_PATH|VARCHAR(256)|DCR 磁盘路径|


#### 15.2.1.26   V$DCR_GROUP

查看 DCR 配置的组信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_TYPE|VARCHAR(32)|组类型，CSS/ASM/DB|
|2|GROUP_NAME|VARCHAR(128)|组名称|
|3|N_EP|INTEGER|组中配置的 EP 个数|
|4|DSKCHK_CNT|INTEGER|磁盘容错时间，单位秒|
|5|NETCHK_TIME|INTEGER|网络容错时间，单位秒|


#### 15.2.1.27  V$DCR_EP

查看 DCR 配置的节点信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_NAME|VARCHAR(128)|节点所属的组名|
|2|EP_NAME|VARCHAR(128)|节点名称|
|3|EP_SEQNO|INTEGER|节点的组内序号：  对 CSS/ASM 组的节点，是自动分配的序号，对 DB 组的节点，如果没有配置，也是自动分配的序号，否则是实际的配置序号。|
|4|EP_HOST|VARCHAR(128)|节点的 IP 地址，对 CSS/ASM 组的节点有效，表示登录节点的 IP 地址|
|5|EP_PORT|INTEGER|节点的 TCP 监听端口，对 CSS/ASM 组的节点有效，对应登录节点的端口号|
|6|UDP_PORT|INTEGER|节点的 UDP 监听端口，已无效|
|7|SHM_KEY|INTEGER|共享内存标识，初始化共享内存的标识符，对 ASM 组的节点有效|
|8|SHM_SIZE|INTEGER|共享内存大小，单位 MB，初始化共享内存大小，对 ASM 组的节点有效|
|9|ASM_LOAD_PATH|VARCHAR(256)|ASM 磁盘扫描路径，对 ASM 组的节点有效|


#### 15.2.1.28 V$DMDCR_INI

查看 DMDCT_INI 参数信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|NAME|VARCHAR(128)|参数名称|
|2|VALUE|VARCHAR(1024)|参数的值|


#### 15.2.1.29  V$DSC_REQUEST_STATISTIC

统计 DSC 环境内 TYPE 类型请求时间，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|TYPE|VARCHAR(64)|请求类型|
|2|TOTAL_REQUEST_COUNT|BIGINT|总请求次数|
|3|MAX_REQUEST_TIME|INTEGER|最大请求时间，单位为微秒|
|4|MIN_REQUEST_TIME|INTEGER|最小请求时间，单位为微秒|
|5|AVERAGE_REQUEST_TIME|INTEGER|平均请求时间，单位为微秒|
|6|AVERAGE_RLOG_FLUSH_TIME|INTEGER|平均等待日志刷盘时间，单位为微秒|


#### 15.2.1.30  V$DSC_REQUEST_PAGE_STATISTIC

统计 lbs_XX 类型最耗时的前 100 页地址信息，仅显示登录节点的信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|TYPE|VARCHAR(64)|请求类型|
|2|TS_ID|INTEGER|表空间 ID|
|3|FILE_ID|INTEGER|文件 ID|
|4|PAGE_NO|INTEGER|页号|
|5|REQUEST_TIME|INTEGER|花费时间，单位为微秒|


#### 15.2.1.31  V$DSC_CRASH_OVER_INFO

显示 DSC 环境各节点数据页最小 first_modified_lsn，以及故障节点 file_lsn。

如果活动节点 buffer 中不存在更新页则 min_first_modified_lsn 为 NULL；节点故障后，只有在所有 OK 节点 min_first_modified_lsn 都大于或等于故障节点 file_lsn 之后，才允许故障节点重加入；满足所有 OK 节点 min_first_modified_lsn 都大于 crash_lsn 之后，crash_lsn 会清零。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|EP_SEQNO|INTEGER|DSC 节点号|
|2|OK_FLAG|INTEGER|节点是否 OK。1 是，表示 OK；0 否，表示故障|
|3|MIN_FIRST_MODIFIED_LSN|BIGINT|最小 first_modified_lsn|
|4|CRASH_LSN|BIGINT|内存中记录的故障节点 file_lsn|


#### 15.2.1.32  V$DSC_GLS_SYS

显示 DSC 环境下的系统全局封锁相关信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|TOTAL_SUCCESS|INTEGER|封锁成功的次数|
|2|TOTAL_WAIT|INTEGER|封锁冲突等待的次数|
|3|TOTAL_DEADLOCK|INTEGER|发生死锁的次数|
|4|HLCK_SEQ|BIGINT|DSC 集群全局封锁的缓存系统当前使用的序列号|


### 15.2.2   DMASM 通用视图

#### 15.2.2.1   V$ASMATTR

如果使用有 ASM 文件系统，可通过此视图查看 ASM 文件系统相关属性，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|AU_SIZE|INTEGER|单个 AU 大小，单位字节。在镜像环境中，该值为 NULL|
|2|EXTENT_SIZE|INTEGER|一个簇包含的 AU 个数|
|3|LOCAL_CODE|VARCHAR(64)|当前所连接的 ASMSERVER 的编码格式|
|4|LOCAL_LANG|VARCHAR(64)|当前所连接的 ASMSERVER 使用的语言：  CN：中文  EN：英文|
|5|USE_SHM|VARCHAR(8)|是否使用共享内存，TRUE/FALSE|
|6|EXTENT_ARR_SIZE|INTEGER|共享内存能存放的 extent 个数，可以通过修改 shm_size 来控制，EXTENT_ARR_SIZE 等于 FREE_EXTENTS_SIZE 与 LRU_EXTENTS_SIZE 的和|
|7|AUTO_RBL_TIME|INTEGER|镜像专用。INTEGER ASMSERVER 自动重平衡间隔，单位为 s。非镜像环境下无意义|
|8|FREE_EXTENTS_SIZE|INTEGER|共享内存中的空闲簇个数|
|9|LRU_EXTENTS_SIZE|INTEGER|共享内存中的已使用的簇个数|


#### 15.2.2.2   V$ASMGROUP

如果使用有 ASM 文件系统，可通过此视图查看 ASM 磁盘组信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_ID|INTEGER|磁盘组 ID|
|2|GROUP_NAME|VARCHAR(128)|磁盘组名称|
|3|N_DISK|INTEGER|磁盘组中包含的磁盘个数|
|4|AU_SIZE|INTEGER|单个 AU 大小，单位为字节|
|5|EXTENT_SIZE(AU)|INTEGER|一个簇包含的 AU 个数|
|6|TOTAL_SIZE|INTEGER|磁盘组总大小，单位为 AU 个数|
|7|FREE_SIZE|INTEGER|磁盘组空闲大小，单位为 AU 个数|
|8|TOTAL_FILE_NUM|INTEGER|磁盘组中总的文件个数，包括文件和目录|
|9|TYPE|VARCHAR|镜像专用。磁盘组副本数：EXTERNAL 单副本；NORMAL 两副本；HIGH 三副本；UNDEF：未知。非镜像环境下为 EXTERNAL|
|10|REDO_SIZE(MB)|INTEGER|镜像专用。磁盘组日志文件大小，单位 MB。非镜像环境下为 1|
|11|RBL_STAT|INTEGER|镜像专用。磁盘组重平衡状态。DISABLE：未启用；ENABLE 已启用。非镜像环境下为 DISABLE|
|12|RBL_PWR|VARCHAR(8)|镜像专用。磁盘组重平衡并行度。非镜像环境下为 0|
|13|AU_SIZE_MB|INTEGER|单个 AU 大小，单位为 MB|
|14|TOTAL_MB|INTEGER|磁盘组总大小，单位为 MB|
|15|FREE_MB|INTEGER|磁盘组空闲大小，单位为 MB|


#### 15.2.2.3   V$ASMDISK

如果使用有 ASM 文件系统，可通过此视图查看所有的 ASM 磁盘信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_ID|INTEGER|所在的磁盘组 ID，  如果是未使用的磁盘，则值为-1|
|2|DISK_ID|INTEGER|磁盘 ID，  如果是未使用的磁盘，则值为-1|
|3|DISK_NAME|VARCHAR(128)|磁盘名称|
|4|DISK_PATH|VARCHAR(256)|磁盘路径|
|5|SIZE|BIGINT|磁盘大小，单位为 M|
|6|FREE_AUNO|BIGINT|磁盘最大 AU 号|
|7|CREATE_TIME|VARCHAR(64)|磁盘创建时间|
|8|MODIFY_TIME|VARCHAR(64)|磁盘最近一次修改时间|
|9|FAILGROUP_NAME|VARCHAR(128)|镜像专用。故障磁盘组组名|
|10|FREE_MB|INTEGER|镜像专用。磁盘剩余空间。非镜像环境下为-1|
|11|STATUS|VARCHAR(8)|镜像专用。磁盘状态。非镜像环境下为 NORMAL|
|12|VERSION|INTEGER|磁盘版本号|
|13|IS_DESTROYED|INTEGER|磁盘物理信息是否损坏。0 表示未损坏，1 表示损坏。每隔 10min 检测一次磁盘|


#### 15.2.2.4   V$ASMFILE

如果使用有 ASM 文件系统，可通过此视图查看所有的 ASM 文件信息，登录任意节点查询得到的结果一致。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|FILE_ID|BIGINT|文件 ID|
|2|TYPE|VARCHAR(32)|类型，目录或文件|
|3|PATH|VARCHAR(256)|文件完整路径|
|4|SIZE_BYTES|BIGINT|文件实际大小，单位为字节，  目录类型的文件不占用空间，值为 0|
|5|SIZE_TOTAL|BIGINT|文件占用总空间大小，单位为字节，  目录类型的文件不占用空间，值为 0|
|6|CREATE_TIME|VARCHAR(64)|文件创建时间|
|7|MODIFY_TIME|VARCHAR(64)|文件修改时间|
|8|GROUP_ID|INTEGER|所在磁盘组 ID|
|9|DISK_ID|INTEGER|inode 项所在磁盘 ID|
|10|DISK_AUNO|INTEGER|inode 项所在磁盘 AU 编号|
|11|AU_OFFSET|INTEGER|inode 项在 AU 内的偏移|
|12|REDUNDANCY|VARCHAR(6)|镜像专用。文件副本数。非镜像环境下为 UNPROT|
|13|STRIPED|VARCHAR(6)|镜像专用。文件条带化。非镜像环境下为 NONE|
|14|REDUNDANCY_LOWERED|VARCHAR(2)|标记文件是否存在缺失副本，取值 Y/N|


### 15.2.3   DMASM 镜像专用视图

本节中 V$ASMPARTNER、V$ASM_OPERATION、V$ASM_REBALANCE_HISTORY 和 V$ASM_FAIL_AU 为镜像环境专用视图。

#### 15.2.3.1   V$ASMPARTNER

如果使用有 DMASM 镜像，可通过此视图查看所有的 ASM 磁盘伙伴关系信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_ID|INTEGER|磁盘组 ID|
|2|DISK_ID|INTEGER|磁盘 ID|
|3|PARTNER_ID|INTEGER|伙伴磁盘 ID|
|4|PARTNER_FAILGROUP|VARCHAR(128)|所属故障组名|


#### 15.2.3.2   V$ASM_OPERATION

如果使用有 DMASM 镜像，可通过此视图查看系统中重平衡或减少副本恢复进度信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_ID|INTEGER|磁盘组 ID|
|2|DISK_ID|INTEGER|磁盘 ID|
|3|OPERATION|VARCHAR(6)|操作类型  l   REBAL(重平衡)|
|4|STATE|VARCHAR(5)|操作状态  l   RUN（正在运行）  l   WAIT（等待运行）  l   ERRS（发生错误停止）|
|5|POWER|INTEGER|重平衡并行度|
|6|SOFAR|INTEGER|已经迁移 AU 数|
|7|EST_WORK|INTEGER|预估需要迁移 AU 总数|
|8|EST_RATE|INTEGER|预估每分钟迁移 AU 数|
|9|EST_MINUTES|INTEGER|预估操作完成剩余时间（分钟）|
|10|BEGIN_TIME|TIMESTAMP|操作开始时间|
|11|END_TIME|TIMESTAMP|操作结束时间（发生错误停止运行时间）|
|12|PROGRESS|DOUBLE|操作进度（百分比）|
|13|ERR_CODE|INTEGER|操作错误码|


#### 15.2.3.3   V$ASM_REBALANCE_HISTORY

DMASM 镜像环境中，查看 AU 重平衡历史信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_ID|INTEGER|磁盘组 ID|
|2|SRC_DISK_ID|INTEGER|源磁盘 ID|
|3|SRC_AUNO|INTEGER|源 AU 号|
|4|DST_DISK_ID|INTEGER|目的磁盘 ID|
|5|DST_AUNO|INTEGER|目的 AU 号|


#### 15.2.3.4   V$ASM_FAIL_AU

DMASM 镜像环境中，查看故障 AU 信息。

|**序号**|**列**|**数据类型**|**说明**|
|----|----|----|----|
|1|GROUP_ID|INTEGER|磁盘组 ID|
|2|DISK_ID|INTEGER|磁盘 ID|
|3|AU_NO|INTEGER|AU 编号|
|4|FILE_ID|BIGINT|所属文件 ID|
|5|AU_SEQ|INTEGER|文件内 AU 编号|


## 15.3  DMDSC 日志文件

DMDSC 集群、DMCSS 集群和 DMASM 集群在运行过程中，各节点均会生成各自的日志文件，日志文件位于各节点的 log 目录里。

### 15.3.1   DMDSC 集群日志

对于 DMDSC 集群，每个 DMSERVER 节点会生成相应的事件日志文件。事件日志文件记录了 DM 数据库运行时的关键事件。例如：系统启动、关闭、内存申请失败、IO 错误等一些致命错误；数据库运行过程中的日志信息；备份还原过程中备份还原操作的阶段性信息等。

事件日志文件主要用于系统出现严重错误时进行查看并定位问题。事件日志文件随着 DMSERVER 的运行一直存在。

事件日志文件打印的是中间步骤的信息，所以出现部分缺失属于正常现象。事件日志信息格式为：时间 + 日志类型（INFO/WARNING/ERROR/FATAL）+ 进程（database）+ 进程 ID（P 开头）+ 线程（dm_sql_thd/main_thread 等）+ 日志内容。

DMSERVER 启动时，如遇到 dm.key、dm.ini 不存在等原因无法正常启动，因尚未获取到实例名，因此将生成的事件日志文件命名为“dm_unknown_年月”。例如：dm_unknown_202212。

DMSERVER 运行过程中产生的事件日志生成到当月的月度事件日志文件中，命名为“dm_实例名_年月”。例如：dm_DSC01_202212.log。

更多事件日志文件的介绍请参考《DM8 系统管理员手册》。

### 15.3.2   DMCSS 集群日志

对于 DMCSS 集群，每个 DMCSS 节点会产生各自的事件日志文件，记录 DMCSS 运行过程中的关键事件。例如：DMCSS 启动、关闭、操作数据库等。

DMCCS 事件日志文件命名为“dm_实例名_年月”。例如：dm_CSS1_202212.log。DMCSS 事件日志信息格式为：时间 + 日志类型（INFO/WARNING/ERROR/FATAL）+ 进程（dmcss）+ 进程 ID（P 开头）+ 线程 ID（T 开头）+ 日志内容。

### 15.3.3   DMASM 集群日志

DMASM 集群分为非镜像环境和镜像环境。两种环境下日志不同。下面分别进行介绍：

非 DMASM 镜像环境下的日志

在非 DMASM 镜像环境中，对于 DMASM 集群，每个节点会产生以下几种日志文件：

一 DMASM 事件日志文件，记录 DMASMSVR 运行过程中的关键事件。例如：DMASMSVR 启动，关闭、MAL 通信检查、DMCSS 命令检查等。该日志文件命名为“dm_实例名_年月”。例如：dm_ASM1_202212.log。DMASM 事件日志信息格式为：时间 + 日志类型（INFO/WARNING/ERROR/FATAL）+ 进程（dmasmsvr）+ 进程 ID（P 开头）+ 线程 ID（T 开头）+ 日志内容。

二 DMASMCMD 工具日志文件，记录 DMASMCMD 执行的各种操作。该日志文件命名为“dm_dmasmcmd_年月”。例如：dm_dmasmcmd_202212.log。该日志信息格式为：时间 + 日志类型（INFO/WARNING/ERROR/FATAL）+ 进程（dmasmcmd）+ 进程 ID（P 开头）+ 线程 ID（T 开头）+ 日志内容。

三 DMASMAPI 日志文件，记录通过 DMASMAPI 接口操作 DMASM 文件的内容。该日志文件命名为“dmasmapi_进程 ID（P 开头）_年月”。例如：dmasmapi_P0000540862_202210.log。DMASMAPI 日志信息格式为：时间 + 线程 ID（T 开头）+ 日志类型（TRACE/WARNING/ERROR/FATAL）+ 日志内容。

四 DMASMTOOL 工具日志文件，记录通过 DMASMTOOL 执行的各种操作。该日志文件命名为“dm_dmasmtool_年月”。例如：dm_dmasmtool_202212.log。该日志信息格式为：时间 + 线程 ID（T 开头）+ 日志类型（TRACE/WARNING/ERROR/FATAL）+ 日志内容。

五 DMASM 文件操作日志文件，记录系统对 DMASM 文件的打开、关闭、增加，删除等操作。该日志文件命名为“dmasm_节点序号_年月”，节点序号为 01、02……。例如：dmasm02_202212.log。DMASM 文件操作日志信息格式为：时间 +DMASM+ 日志类型（TRACE/WARNING/ERROR/FATAL）+ 日志内容。

DMASM 镜像环境下的日志

在 DMASM 镜像环境中，对于 DMASM 集群，每个节点会产生几种日志文件：

一 DMASM 事件日志文件，记录 DMASMSVRM 运行过程中的关键事件。该日志文件在镜像环境下和非镜像环境下，用法完全一致。具体请参考非镜像环境下的 DMASM 事件日志文件。

二 DMASMCMDM 工具日志文件，记录 DMASMCMDM 执行的各种操作。该日志文件命名为“dm_dmasmcmdm_年月”。例如：dm_dmasmcmdm_202212.log。该日志信息格式为：时间 + 日志类型（INFO/WARNING/ERROR/FATAL）+ 进程（dmasmcmdm）+ 进程 ID（P 开头）+ 线程 ID（T 开头）+ 日志内容。

三 DMASMAPIM 日志文件，记录通过 DMASMAPIM 接口操作 DMASM 文件的内容。该日志文件命名为“dmasmapi3_进程 ID（P 开头）_年月”。例如：dmasmapi3_P0000540862_202210.log。DMASMAPI 日志信息格式为：时间 + 线程 ID（T 开头）+ 日志类型（TRACE/WARNING/ERROR/FATAL）+ 日志内容。

四 DMASMTOOLM 工具日志文件，记录通过 DMASMTOOLM 执行的各种操作。该日志文件命名为“dm_dmasmtoolm_年月”。例如：dm_dmasmtoolm_202212.log。该日志信息格式为：时间 + 线程 ID（T 开头）+ 日志类型（TRACE/WARNING/ERROR/FATAL）+ 日志内容。

五 DMASM 文件操作日志文件，记录系统对 DMASM 文件的打开、关闭、增加，删除等操作。该日志文件命名为“dmasm_trace_进程 ID（P 开头）_年月日”。例如：dmasm_trace_P0000002432_20221202.log。DMASM 文件操作日志信息格式为：时间 +DMASM+ 日志类型（TRACE/WARNING/ERROR/FATAL）+ 日志内容。

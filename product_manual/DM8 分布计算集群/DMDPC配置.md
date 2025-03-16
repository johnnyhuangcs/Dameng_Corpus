

# DMDPC配置

## 6.1 相关参数

### 6.1.1 DM.INI

DM.INI 是 DM 数据库实例的配置文件，通过配置该文件可以设置 DM 数据库服务器的各种功能和性能选项。

#### 6.1.1.1 DMDPC 相关的参数

DM.INI 中的配置项多达数百个，涉及 DM 数据库运行各个方面的设置，这里我们只介绍与 DMDPC 相关的几个配置项，读者若对其他配置项感兴趣可以参看《DM8 系统管理员手册》的相关章节。

表6.1 DM.INI DMDPC相关配置项

|**参数**|**说明**|**属性**|**缺省值**|
|---|---|---|---|
|INSTANCE_NAME|实例名称。推荐以角色开头，例如：SP_1，BP_2|静态|DMSERVER|
|PORT_NUM|数据库服务器监听端口，SP/BP 本地设置的 PORT_NUM 如果和自身在 MP 上注册的不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖|静态|5236|
|AP_PORT_NUM|分布式环境下协同工作的监听端口，SP/BP 本地设置的 AP_PORT_NUM 如果和自身在 MP 上注册的 AP_PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖|静态|6000|
|DPC_OPT_FLAG|执行计划优化策略。取值 1、2、4、8、16、32、64、128、256、512、1024、2048、4096、8192、16384、32768、65536、131072、262144 或上述任意取值的和。1：表示忽略 UNIONALL，将分支的内容直接发给 UNIONALL 之上的 ERECV；  2：表示根据 HSCN 的使用情况，对 HTAB 的分布信息调整，可以减少 HSCN 之后的数据分发量；  4：表示在通讯符之前将 afun 转换成两个，可以减少通讯量；  8： 表示按照实际执行的 BP 个数调整子任务并行度；  16：根据利用统计信息最大、最小值计算相同表不同别名的选择率。例如 t1.c1 + 10 > t1.c1 ==> t1.c1 \<  max(t1.c1) +10；  32：同时开启 SP、MP 和 BP 节点的计划缓存的功能，减少事务密集型应用中计划发送带来的耗时。非 32 的情况下，只有 SP 具有计划缓存功能；64：多列 primary key 用于连接时的行数估算调整优化；  128：根据估算的行数对连接、分组等使用到的哈希表大小进行缩放优化；  256：哈希连接时，如果连接左边数据来自广播，启用共享哈希表优化减少哈希表构造开销；  512：huge 表考虑 hfsek 与 hfscn+slct 的代价；  1024：计划只从几个子任务开始，而不是所有的叶子节点；  2048：DISTINCT 尝试下放至集合运算的左右侧的子任务先做；  4096：afun/sagr+sort+dis 的 sort 下放到 dis 下面，变成 afun/sagr+dis（归并信息）+sort。sort 放到 dis 下面后，每一路可先做排序，如果子任务并行度比 sagr/afun 的并行度大时能较快的完成排序；  8192：esend 发送时对 link_id 进行优化。优化后，消息可以多条链路同时发生，而非一条链路串行；16384：表示 DMDPC 环境下支持变量跨层引用；32768：支持 L_FULL 优化。具体为支持 LEFT JOIN 操作符左边使用全部数据，右边使用部分数据的情况；65536：表示开启子任务为空连接提前终止的优化。若某个子任务的结果为空，上层有连接操作且可以推断出连接的结果也为空时，那直接终止此连接以及此连接的所有子任务。这个过程是可以向上传递的，直到某个子任务结果不为空或者无法推断为空为止；131072：表示当某任务的子任务都在同一个站点上时，那么系统会优先让这个任务也在此站点做。因为数据在同站点不同线程内传输，相比不同站点会减小数据传输时间；262144：表示将 GROUP BY 最大值下放到 HTAB|动态，会话级|131071|
|ALTER_MODE_STATUS|在多副本系统中使用。  是否允许手工修改服务器的模式、状态和 OGUID。1：允许；0：不允许。建议设置为 0|动态，系统级|1|
|DPC_2PC|DMDPC 系统是否采用两阶段提交策略及是否使用优化方案。  0：关闭两阶段提交，没有故障的情况下直接提交效率更高，但是出现故障就无法保证事务一致性了；  1：启用两阶段提交；  2 或 3：启用两阶段提交，启用 trxid 缓存，启用 snap_seq 缓存方案优化；  4 或 5：启用两阶段提交，启用 trxid 缓存，启用 snap_seq 本地优化；  6 或 7：启用两阶段提交，启用 trxid 缓存，启用 snap_seq 缓存，启用 snap_seq 本地优化|手动|1|
|SWITCH_CONN|JDBC 客户端是否进行连接切换，执行过程中发现计划只和一个 BP 相关时，客户端是否切换到该 BP 执行。  0：否；1：是。  此参数需要和 JDBC 驱动/dm_svc.conf 配合使用，只用于 BS 模式|动态，系统级|0|
|TRC_LOG|TRACE 日志开关。需和 TRC_LOG_MODULE 配合使用。  0：表示关闭；  非 0：为功能组合值，组合规则为：  switch_mod4 + asyn_flag2 + 1。其中，\<switch_mode> 日志文件切换方式： 0 不切换；1 按文件记录行数，满 10000 行切换；2 按文件大小进行切换，满 128M 切换； 3 按时间间隔进行切换，每小时切换。  \<asyn_flag> 日志文件是否采用异步方式：0 否，采用同步方式； 1 是，采用异步方式 。  TRACE 日志位于安装目录下的/log 中，名称为 dmtrc_instname.log（不含切换方式）或 dmtrc_instname_日期时间.log（含有切换方式）|动态，系统级|0|
|TRC_LOG_MODULE|当打开 TRC_LOG 开关时，用于配置 TRACE 日志中记录的内容。该参数为字符类型，最大长度 128。  书写格式为：TRC_LOG_MODULE=服务器记录项,DMDPC 记录项。  服务器记录项和 DMDPC 记录项设置方法相同。缺省表示不记录该模块中的任何内容。  具体记录项和代号： 内存 MEM (0)、任务线程 TSK (1) 、站点信息 SITE (2) 、事务 TRX (3) 、通信消息 MSG (4) 、 会话 SESS (5) 、计划 PLN_GEN (6) 、HUGE 文件系统 HFS (7)、LOCK 相关(8)、快速装载服务器端模块 BLDR(9)、字典 DICT(10)、错误堆栈 ESTK(11) 、用户自定义向日志写入内容(12)、012 全部记录(ALL)。多个记录项之间使用冒号(:)分割。  例如：  1）记录 012 项所有的信息，需设置 trc_log_module = ALL  2）只服务器模块记录 HFS 和 TRX，DMDPC 模块不记录，需设置 trc_log_module=  3:7,  3）只 DMDPC 模块记录 MSG，服务器模块不记录，需设置 trc_log_module=,4  4）若服务器记录 HFS 和 TRX，DMDPC 记录 MSG，需设置 trc_log_module=3:7,4|动态，系统级|ALL|
|DPC_LOG_INTERVAL|MP 定时通知各节点生成相同时间点日志的时间间隔。取值范围 0~1440（24 小时）。以分钟为单位。0 表示关闭定时通知功能。  此参数打开后，可支持使用归档恢复到指定时间点功能，在对系统性能有要求的情况下，可以调大或关闭此参数值|动态，系统级|0|
|SQC_GI_NUM_PER_TAB|每个表拆分的扫描单元个数。取值范围 1~1024。  扫描表对象时，每个表可以被拆分成多个单元以提升并行性能。参数值表示拆分的单元个数，每个单元可以独立用于工作线程的扫描任务。例如：需要扫描一个子表且子表数据量较大，为充分利用工作线程资源，可以调大此参数以使用分区内并行|动态，会话级|1|
|HP_TAB_COUNT_PER_BP|创建一级、二级哈希分区时，如果分区数指定为 DEFAULT 而不是具体数值，系统会根据本参数值决定每个 BP RAFT 上创建的哈希子表个数。  取值范围 1~1024|动态，会话级|1|
|DPC_SYNC_STEP|打开 DMDPC 限流（MPP_MOTION_SYNC 非 0）时使用。  表示检查接收端数据堆积的步长，即每发送 DPC_SYNC_STEP 次 BDTA 后检查一次是否堆积。取值范围 1~5000|动态，会话级|16|
|DPC_SYNC_TOTAL|打开 DMDPC 限流（MPP_MOTION_SYNC 非 0）时使用。XBOX 上所有来源路数（并行线程数）的总限流资源数。取值范围 0~10000。0 表示不限总资源数|动态，会话级|0|
|XBOX_DUMP_THRESHOLD|XBOX 邮件内存阈值，超过该值则保存到文件中。取值范围 0~409600。单位 MB。  和 XBOX_DUMP_PATH 一起设置才生效|动态，系统级|0|
|XBOX_DUMP_PATH|XBOX 邮件保存到临时文件的目录。  和 XBOX_DUMP_THRESHOLD 一起设置才生效|手动|空串|
|XBOX_SPACE_LIMIT|配置了 XBOX_DUMP_THRESHOLD 和 XBOX_DUMP_PATH 时有效。  表示单个 XBOX 的空间限制。取值范围 0 ~ 409600。单位  MB。0 表示不限制。  XBOX_SPACE_LIMIT 和 XBOXS_SPACE_LIMIT 可分别单独配置，也可同时配置，同时配置时须保证 XBOXS_SPACE_LIMIT >  XBOX_SPACE_LIMIT|动态，系统级|0|
|XBOXS_SPACE_LIMIT|配置了 XBOX_DUMP_THRESHOLD 和 XBOX_DUMP_PATH 时有效。  表示系统所有 XBOX 的总空间限制。取值范围 0 ~ 1000000。单位 MB。0 表示不限制|动态，系统级|0|
|STMT_XBOX_REUSE|DMDPC 会话的语句是否重用 box_id。  0：否，每个语句都请求分配一个 box_id；  1：是|动态，会话级|1|
|XBOX_SHORT_MSG_SIZE|在配置了 XBOX_DUMP_THRESHOLD 后，即使内存达到阈值的情况下，小于此长度的短消息也不会保存到文件，防止大量的文件读写。 取值范围 0~32768。单位 BYTE|系统，会话级|1024|
|DPC_TRX_TIMEOUT|用于 DMdpc 中，表示事务等待超时时间。取值范围 0~604800。单位秒|动态，会话级|10|
|DPC_GUP_SESS_TIMEOUT|用于 DMdpc 中，表示 sP 升级时事务、会话超时断开时间。取值范围 60~600。单位秒|动态，会话级|180|
|DPC_TABLESPACE_BALANCE|当在 DPC 下创建分区表，子表个数少于所选择的表空间个数时，是否启用随机选择起始表空间以使得表空间负载均衡。0：不启用；1：启用，表示从已有子表所使用的表空间中挑选；2：启用，在新增子表时同时考虑从所有表空间中挑选|动态，系统级|1|
|DPC_DCT_REFRESH_POLICY|MP 广播实例字典信息优化开关。  0：表示关闭，MP 会定时广播实例字典信息；  1：表示打开，MP 只会在每次修改实例字典信息时进行广播|动态，系统级|1|
|ENET_SESS_CHECK_INTERVAL|当 BP/SP 处于 MP 无效服务状态时，BP/SP 断开会话连接的时间间隔阈值。如果 BP/SP 处于 MP 无效服务状态的时间超过该阈值，则会主动断开所有会话连接。取值范围 0~4294967294，单位 S。0 表示关闭|动态，系统级|10|
|STHD_FLAG|是否启用 ESESS/SESS 线程池。取值范围 0~3。0：表示关闭；1：表示打开 ESESS 线程池；2：表示打开 SESS 线程池；3：表示同时打开 ESESS 和 SESS 线程池|静态|0|
|STHD_THREAD_NUM|每个 ESESS 线程池初始化的线程数量。取值范围 1~10000|静态|8|
|STHD_GRP_NUM|初始化的 ESESS 线程池数量。取值范围 1~64。当打开 SESS 线程池（即 STHD_FLAG=2 或 3）时，该参数强制等于 WORKER_THREADS 的值|静态|8|
|RLOG_RAFT_NEED_WAIT|主库是否进行日志包发送等待控制，仅 RAFT 主库生效。  0：主库不进行日志包发送控制；1：任意备库存在日志堆积时，根据 RLOG_RAFT_WAIT_TIME 暂缓日志包发送；2：存在日志堆积的备库个数超过备库总数的半数时，根据 RLOG_RAFT_WAIT_TIME 暂缓日志包发送；3：任意归档状态有效的备库存在日志堆积，根据 RLOG_RAFT_WAIT_TIME 暂缓日志包发送；4：存在日志堆积的归档状态有效的备库个数超过备库总数的半数时，根据 RLOG_RAFT_WAIT_TIME 暂缓日志包发送|动态，  系统级|3|
|RLOG_RAFT_WAIT_TIME|备库出现堆积后，RAFT 主库延迟发送等待时间，取值范围 0~3600000，单位 MS。仅 RAFT 主库生效|动态，  系统级|1000|
|SHADOW_CHECK_INTERVAL|用于设置清理影子库的本地归档文件的时间间隔，取值范围 0~3600，单位 S。如果设为 0，则关闭自动清理功能。此参数仅对影子库有用|动态，系统级|60|
|GROUP_OPT_FLAG|分组项优化参数开关。0：不优化；1：非 MYSQL 兼容模式下（即 COMPATIBLE_MODE 不等于 4），支持查询项不是 GROUP BY 表达式；2：外层分组项下放到内层派生表中提前分组优化；4：表示对于多级分区，并行下允许尝试不生成多个 AGR；8：进行哈希分组时，依赖分组项列中的核心项分组列来分组；16：位图索引覆盖简单分组查询中的所有列时，允许使用位图索引对查询进行优化；32：进行哈希分组时，对 TOP 查询进行优化，提前返回指定数量的分组；64：DMDPC 下，对无分组项且含有 DISTINCT 集函数的查询，允许对集函数参数先进行一次分发处理。支持使用上述有效值的组合值，如 3 表示同时进行 1 和 2 的优化|动态，会话级|52|
|STAT_CACHE_FLAG|SP 是否缓存表行数。  0：不缓存；  1：SP 缓存表行数，而不是每次都向 MP 请求表行数，从本地缓存中获取表行数以降低 MP 的性能瓶颈。和 STAT_CACHE_CAPACITY 搭配使用|静态|1|
|STAT_CACHE_CAPACITY|当 STAT_CACHE_FLAG=1 时使用。sp 缓存表行数的缓存容量。取值范围 100~100000000，单位缓存表的个数|动态，系统级|1000|
|TSMV_RAFIL_SIZE|指定表空间迁移的临时归档文件的最大空间。取值范围 2~1024。单位 Mb|静态|64|
|TSMV_FILE_COPY_PLL|表空间迁移时物理文件拷贝并行度。取值范围 1~128|动态，系统级|1|
|TSMV_WAIT_TIMEOUT|表空间迁移上锁等待时间。取值范围 0~12000，单位 S|动态，系统级|120|
|TSMV_SEND_BUF_SIZE|表空间迁移发送文件缓冲区大小。单位 MB，取值范围 1~64。值越小，迁移越慢，但迁移中对并发业务影响越小；值越大迁移速度越快，但迁移中对并发业务影响越大，可结合实际情况设置|动态，系统级|4|
|MAX_ESESSIONS|指定分支 ESESSION 个数上限，取值范围 0~650000，0 表示无限制。ESESSION 为 SP 和其他节点之间的内部会话，SP 上的 ESESSION 为主 ESESSION，其他节点上的 ESESSION 为分支 ESESSION|动态，系统级|100000|


> **注意**
>
> DMDPC环境下不允许修改TS_MAX_ID和TS_FIL_MAX_ID参数。



#### 6.1.1.2 自动覆盖不一致的参数

部分 DM.INI 参数，当 SP/BP 本地设置的 DM.INI 参数值和自身在 MP 上注册的参数值不一致时，按照在 MP 上实际注册值为准，系统启动时会将 SP/BP 本地的参数自动覆盖掉。如果 MP 为多副本架构，则以 MP 主节点为准。

会被覆盖的 DM.INI 参数为：一是包括和 DMDPC 相关的 PORT_NUM、AP_PORT_NUM；以及二是 DM 数据库通用的参数。目前有以下这些：LENGTH_IN_CHAR、PARALLEL_POLICY、USE_NEW_HASH、COMPRESS_MODE、LIST_TABLE、LIST TABLE BRANCH、LIST TABLE NON BRANCH、COMPATIBLE_MODE、COUNT_64BIT、BLANK_PAD_MODE、ALTER_TABLE_OPT、DECIMAL_FIX_STORAGE、ENABLE_HUGE_SECIND、PK_WITH_CLUSTER、DATETIME_FMT_MODE、SLCT_ERR_PROCESS_FLAG、FORCE_CERTIFICATE_ENCRYPTION、CAST_VARCHAR_MODE、SPACE_COMPARE_MODE、JSON_MODE、DPC_2PC、MPP_MOTION_SYNC、BDTA_SIZE、USE_PLN_POOL、MAX_SESSION_STATEMENT、MAX_EP_SITES、DPC_SYNC_STEP、PC_SYNC_TOTAL、STMT_XBOX_REUSE、HUGE_ENABLE_DEL_UPD、ENABLE_INJECT_HINT（只会同步 MP、SP 和 BS。BP 不支持该参数，永远为 0）、ENABLE_FLASHBACK、UNDO_RETENTION、ALLOWED_CLIENT_VERSIONS、SELECT_LOCK_MODE、ENABLE_CS_CVT、HFI_HP_MODE、NUMBER_MODE、VIEW_ACCESS_MODE、TRUNC_CHECK_MODE、PL_SQL_STRIP、CAST_CLOB_MODE、WM_CONCAT_LOB、CALC_AS_DECIAML、ORA_REVERSE_MODE、USE_JSON_DATATYPE 等。

当试图修改上述参数的内存值时，DM 服务器会拒绝并报错。

#### 6.1.1.3 从主库同步参数

在主备环境中，当备库 DM.INI 参数与主库不一致时，可在备库执行 INT SF_SYNC_INI( LEVEL
INT)函数来同步主库上的 DM.INI 参数。LEVEL 为同步级别，取值 0 和 1。0 表示同步所属环境下必须同步的参数；1 表示同步所有可以同步的参数。

例 在备库上执行 SF_SYNC_INI 函数，备库同步主库上所有可以同步的 INI 参数。

```
SELECT SF_SYNC_INI (1);
```

SF_SYNC_INI 函数的详细用法请参考《DM8 SQL 语言使用手册》。

### 6.1.2 DMINIT 工具

通过 DMINIT 工具指定初始化参数。 和 DMDPC 集群搭建相关的参数如下表 6.2 所示。

使用 DMINIT 工具初始化时，如果同一个 INI 参数在 DM.INI 和 DMINIT 中分别指定了不同的值，那么以 DMINIT 工具中指定的为准。

表6.2 DMINIT工具配置参数

|**参数**|**说明**|**缺省值**|
|---|---|---|
|PATH|初始数据库存放的路径|无。不指定时会提示输入|
|INSTANCE_NAME|实例名|DMSERVER|
|PORT_NUM|数据库服务器监听端口号，取值范围 1024~65534|5236|
|AP_PORT_NUM|分布式环境下协同工作的监听端口号，取值范围 1024~65534|6000|
|DPC_MODE|指定 DMDPC 集群中的实例角色。 0：无；1/MP：MP；2/BP：BP；3/SP：SP。 使用 DMINIT 搭建环境时，DPC_MODE 参数值既可以使用数字 0/1/2/3，也可以使用 MP/BP/SP 字符串代替，二者作用等价|0|


只要 DMINIT 初始化库时，指定了 DPC_MODE 参数值，那么在 DMSERVER 启动时，启动参数 DPC_MODE 值必须和 DMINIT 保持一致。否则，可能导致服务器起不来。DPC_MODE 在 DMINIT 和 DMSERVER 中的取值对应关系如表 6.3 所示。

表6.3 DMINIT和DMSERVER中DPC_MODE参数值对应关系 

|**DMINIT 的 DPC_MODE**|**DMSERVER 的 DPC_MODE**|
|-------------------|-------------------|
|0|0|
|1 或 MP|1 或 MP|
|2 或 BP|2 或 BP|
|4 或 BS||
|3 或 SP|3 或 SP|


### 6.1.3 MP.INI

DMDPC 集群中除 MP 自身外，SP、BP 都存在和 MP 通讯的可能，因此在 SP、BP 和 MP 初始化的 PATH 相同目录下需要用 MP.INI 来写明 MP 的 IP 地址和端口号。同一集群中 SP、BP 的 MP.INI 内容完全一致。

表6.4 MP.INI中的参数

|**参数名**|**说明**|
|---|---|
|MP_HOST|mp 实例的 IP 地址|
|MP_PORT|mp 实例的监听端口号|
|MP_RECONN_TIMEOUT|连接 MP 失败时，尝试重连的超时时间。在设定的时间段内，如果与 MP 连接断开，会不断的尝试连接 MP。单位为秒。取值范围 0~4294967294，缺省为 4294967294‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬‬|


例 一个完整的 MP.INI 文件内容如下：

```
mp_host = 223.254.30.136
mp_port = 9000 #与MP、BP和SP上的ap_port_num不冲突的端口号
```

### 6.1.4 DMARCH.INI

DMARCH.INI 是 DM 数据库实例的归档配置文件，用于配置数据库本地归档和 RAFT 归档。这里仅对多副本系统相关配置项进行介绍。若读者对其他配置项感兴趣，可参考《DM8 系统管理员手册》的相关章节。

表6.5 DMARCH.INI多副本相关配置项



说明：

  1. ARCH_TYPE 表示 Redo 日志归档类型，多副本系统中需要使用到 RAFT 归档和本地归档，配置 RAFT 归档时，必须配置至少一个本地归档；
  2. XMAL_PORT 是多副本用于选举和消息通信的专用端口号。为了防止端口通信冲突，XMAL_PORT 配置项必须不同于 dm.ini 中 PORT_NUM 配置；
  3. 由于多副本系统总的节点个数必须为奇数，因此 RAFT 归档目标个数要配置为偶数个（除去本机后剩余的节点个数），也就是最少要配置 2 个，最多允许配置 8 个；
  4. 可以通过 RAFT_VOTE_INTERVAL 设置选举优先级，RAFT_VOTE_INTERVAL 配置值越小的节点实例，被选举为新主库的优先级越高。



### 6.1.5 DMSERVER 启动参数

DMSERVER 程序指定启动参数，表示实例以何种模式工作，相关参数如下表 6.6 所示。

表6.6 DMSERVER启动参数

|**参数**|**说明**|**缺省值**|
|---|---|---|
|PATH|数据库 dm.ini 存放的路径|dm.ini 绝对路径或者 dmserver 当前目录的 dm.ini|
|DPC_MODE|实例运行模式|指定 DPC 中的实例角色，0：无角色，缺省为 0、1：MP、2：BP、3：SP、4：BS，取值 1/2/3/4 时也可以分别用 MP/BP/SP/BS 代替。其中 BS 模式表示直连 BP 模式，详见 3.1 基本概念中的 BS 模式说明。DMSERVER 启动时，DPC_MODE 在 DMINIT 和 DMSERVER 中的取值须按照对应关系正确对应，对应关系请参考 DMINIT 工具介绍|


## 6.2 配置方法

本节对 DMDPC 进行配置的多种过程进行详细介绍。

### 6.2.1 SP_CREATE_DPC_BP_GROUP

定义：

```
SP_CREATE_DPC_BP_GROUP(
	GROUP_NAME	 	VARCHAR(128),
	DESC				VARCHAR(256)
)

```

功能说明：

向 MP 注册一个新的 BP 组。

参数说明：

GROUP_NAME：待注册 BP 组名称；

DESC：描述信息。

举例说明：

注册名字为 BG_1 的 BP 组，描述信息为“bp group1”。

```
SP_CREATE_DPC_BP_GROUP('BG_1', 'bp group1');
```

### 6.2.2 SP_DROP_DPC_BP_GROUP

定义：

```
SP_DROP_DPC_BP_GROUP(
	NAME VARCHAR(128)
)
```

功能说明：

删除一个 BP 组。

参数说明：

NAME：待移除 BP 组名称，必须是已经注册过的 BP 组。

举例说明：

删除名字为 BG_1 的 BP 组。

```
SP_DROP_DPC_BP_GROUP('BG_1');
```

### 6.2.3 SP_CREATE_DPC_SP_GROUP

定义：

```
SP_CREATE_DPC_SP_GROUP(
	GROUP_NAME	 	VARCHAR(128),
	DESC				VARCHAR(256)
)
```

功能说明：

向 MP 注册一个新的 SP 组。

参数说明：

GROUP_NAME：待注册 SP 组名称；

DESC：描述信息。

举例说明：

注册名字为 SPG_1 的 SP 组，描述信息为“sp group1”。

```
SP_CREATE_DPC_SP_GROUP('SPG_1', 'sp group1');
```

### 6.2.4  SP_DROP_DPC_SP_GROUP

定义：

```
SP_DROP_DPC_SP_GROUP(
	NAME varchar(128)
)
```

功能说明：

删除一个 SP 组。

参数说明：

NAME：待移除 SP 组名称，必须是已经注册过的 SP 组。

举例说明：

删除名字为 SPG_1 的 SP 组。

```
SP_DROP_DPC_SP_GROUP('SPG_1');
```

### 6.2.5 SP_CREATE_DPC_RAFT

定义：

```
SP_CREATE_DPC_RAFT（
	DPC_MODE         VARCHAR(8),
	RAFT_NAME         VARCHAR(128)
）
```

功能说明：

向 MP 注册一个新的 RAFT 组。

参数说明：

DPC_MODE：待注册 RAFT 组类型，取值只能为 BP 或者 SP。一个 SP 类型的 RAFT 组中最多只能加入一个 SP 节点；

RAFT_NAME：待注册 RAFT 组名称。

举例说明：

注册名字为 RAFT_1 的 BP RAFT 组，隶属于 BG_1。

```
SP_CREATE_DPC_RAFT('BP', 'RAFT_1');
```

### 6.2.6 SP_DROP_DPC_BP_RAFT/SP_DROP_DPC_RAFT

定义：

```
SP_DROP_DPC_BP_RAFT(
	NAME	VARCHAR(128)
)
```

或

```
SP_DROP_DPC_RAFT(
	NAME	VARCHAR(128)
)
```

功能说明：

删除一个 RAFT 组。

参数说明：

NAME：待删除 RAFT 组名称。

举例说明：

删除名字为 RAFT_1 的 RAFT 组。

```
SP_DROP_DPC_BP_RAFT('RAFT_1');
或
SP_DROP_DPC_RAFT('RAFT_1');
```

### 6.2.7 SP_CREATE_DPC_INSTANCE

SP_CREATE_DPC_INSTANCE 过程有两个。

1.内外网地址相同情景

定义：

```
SP_CREATE_DPC_INSTANCE (
	RAFT_NAME	 	VARCHAR(128),
	NAME           VARCHAR(128),
	DPC_MODE     VARCHAR(8),
	AP_PORT       INT,
	INST_PORT     INT,
	IP_ADDR        VARCHAR(256),
	SYS MODE       VARCHAR(32),
	STATUS         INT,
	DESC            VARCHAR(256) 
）
```

功能说明：

注册一个 BP/SP 实例。

参数说明：

RAFT_NAME：RAFT 名称，和 SP_CREATE_DPC_RAFT 中保持一致。如果是 MP，该值可写成'MP_RAFT'，也可缺省为空串''或 NULL，因为 MP 多副本系统只有一个 RAFT 组，所以 RAFT 组名固定为 MP_RAFT，缺省值也为 MP_RAFT。如果 BP 或 SP，则该值不能缺省；

NAME：待注册的实例名称，和 DM.INI 中 INSTANCE_NAME 保持一致；

DPC_MODE：注册的实例类型，取值：BP、SP 或 MP。需要和 RAFT_NAME 所属类型一致；

AP_PORT： AP_PORT 端口号，和 DM.INI 中 AP_PORT_NUM 保持对应。和 DPC_INSTANCE 表中 XMAL_PORT 值对应。SP/BP 本地设置的 AP_PORT_NUM 如果和自身在 MP 上注册的 AP_PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖；

INST_PORT： 实例端口号，和 DM.INI 中 PORT_NUM 保持对应。和 DPC_INSTANCE 表中 INST_PORT 值对应。SP/BP 本地设置的 PORT_NUM 如果和自身在 MP 上注册的 PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖；

IP_ADDR： IP 地址；

SYS_MODE： 主备模式，取值有 NORMAL，PRIMARY，STANDBY；

STATUS： 实例状态，1：有效；0：无效；2：表示 SP 为正常退出状态，可动态删除此 SP 实例；

DESC： 描述信息。

举例说明：

在 RAFT_1 组注册一个新的 BP 节点 BP_1。

```
SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP_1', 'BP', 1822, 5238, '192.168.1.76',
'NORMAL', 1, 'BP instance');
```

2.内外网地址不同或相同的情景

定义：

```
SP_CREATE_DPC_INSTANCE (
	RAFT_NAME	 	VARCHAR(128),
	NAME           VARCHAR(128),
	DPC_MODE     VARCHAR(8),
	AP_PORT       INT,
	INST_PORT     INT,
	IP_INTER      VARCHAR(256),
	IP_EXTER      VARCHAR(256),
	SYS MODE       VARCHAR(32),
	STATUS         INT,
	DESC            VARCHAR(256) 
）
```

功能说明：

注册一个 BP/SP 实例。

参数说明：

RAFT_NAME：RAFT 名称，和 SP_CREATE_DPC_RAFT 中保持一致。如果是 MP，该值可写成'MP_RAFT'，也可缺省为空串''或 NULL，因为 MP 多副本系统只有一个 RAFT 组，所以 RAFT 组名固定为 MP_RAFT，缺省值也为 MP_RAFT。如果 BP 或 SP，则该值不能缺省。

NAME： 待注册的实例名称，和 DM.INI 中 INSTANCE_NAME 保持一致；

DPC_MODE： 注册的实例类型，取值：BP、SP 或 MP。需要和 RAFT_NAME 所属类型一致；

AP_PORT：AP_PORT 端口号，和 DM.INI 中 AP_PORT_NUM 保持对应。和 DPC_INSTANCE 表中 XMAL_PORT 值对应。SP/BP 本地设置的 AP_PORT_NUM 如果和自身在 MP 上注册的 AP_PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖；

INST_PORT： 实例端口号，和 DM.INI 中 PORT_NUM 保持对应。和 DPC_INSTANCE 表中 INST_PORT 值对应。SP/BP 本地设置的 PORT_NUM 如果和自身在 MP 上注册的 PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖；

IP_INTER： 实例的内网 IP 地址；

IP_EXTER： 实例的外网 IP 地址；可传空串，表示内外网地址相同；

SYS_MODE： 主备模式，取值有 NORMAL，PRIMARY，STANDBY；

STATUS： 实例状态，1：有效；0：无效；2：表示 SP 为正常退出状态，可动态删除此 SP 实例；

DESC： 描述信息。

举例说明：

在 RAFT_1 组注册一个新的 BP 节点 BP_1。

```
SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP_1', 'BP', 1822, 5238, '192.168.1.76',
'192.168.1.77', 'NORMAL', 1, 'BP instance');
```

### 6.2.8 SP_DROP_DPC_INSTANCE

定义：

```
SP_DROP_DPC_INSTANCE (
	NAME VARCHAR(128)
)
```

功能说明：

移除多副本中的一个 BP/SP/MP 实例。只剩最后一个实例则不能删除。

参数说明：

NAME：待移除实例名称，必须是已经注册过的实例名。

举例说明：

移除名字为 BP_1 的 BP 服务器。

```
SP_DROP_DPC_INSTANCE('BP_1');
```

### 6.2.9 SP_MODIFY_DPC_INSTANCE

定义：

```
SP_MODIFY_DPC_INSTANCE (
	NAME           VARCHAR(128),
	IP_INTER      VARCHAR(256),
	IP_EXTER      VARCHAR(256),
	AP_PORT       INT,
	INST_PORT     INT
)
```

或

```
SP_MODIFY_DPC_INSTANCE (
	NAME           VARCHAR(128),
	ATTR            VARCHAR(128),
	VALUE            VARCHAR(512)
)
```

功能说明：

修改注册在 MP 上的实例信息。修改成功后需要重启全部环境来应用新参数。

SP/BP 本地设置的 AP_PORT_NUM/PORT_NUM 如果和自身在 MP 上注册的 AP_PORT_NUM/PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖。

参数说明：

NAME： 待修改的实例名称，非空；

IP_INTER： 修改为新的内网 IP 地址，可为空串，表示不修改；

IP_EXTER： 修改为新的外网 IP 地址，可为空串，表示不修改；

AP_PORT： AP_PORT 端口号，和 DM.INI 中 AP_PORT_NUM 保持对应。和 DPC_INSTANCE 表中 XMAL_PORT 值对应。可为空串，表示不修改。SP/BP 本地设置的 AP_PORT_NUM 如果和自身在 MP 上注册的 AP_PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖；

INST_PORT： 实例端口号，和 DM.INI 中 PORT_NUM 保持对应。和 DPC_INSTANCE 表中 INST_PORT 值对应。可为空串，表示不修改。SP/BP 本地设置的 PORT_NUM 如果和自身在 MP 上注册的 PORT_NUM 不一致，按照在 MP 上实际注册的为准，启动时会自动覆盖；

ATTR： 属性，不可为空串，目前 ATTR 参数仅支持使用'CFG_PORT'，用于设置 MP 的 CFG_PORT 参数；

VALUE： 属性值，不可为空串。

举例说明：

修改 BP_1 实例的 IP 地址、ap 端口号和实例端口号。可同时修改多个字段，也可以只修改部分字段值，不需要修改的字段传入空串''值即可，要求必须修改至少一个字段。

```
SP_MODIFY_DPC_INSTANCE('BP_1', '192.168.1.76', '192.168.100.77', 2822, 5338);
```

### 6.2.10 SP_BP_GROUP_ADD_RAFT

定义：

```
SP_BP_GROUP_ADD_RAFT（
	BP_GROUP_NAME    VARCHAR(128),
	RAFT_NAME         VARCHAR(128)
）
```

功能说明：

向 BP 组中添加一个 RAFT 组。

参数说明：

BP_GROUP_NAME：指定添加 RAFT 组的 BP 组名；

RAFT_NAME：待添加的 RAFT 组名。

举例说明：

向 BP 组 BG_1 中添加一个名为 RAFT_1 的 RAFT 组。

```
SP_BP_GROUP_ADD_RAFT('BG_1', 'RAFT_1');
```

### 6.2.11 SP_BP_GROUP_DEL_RAFT

定义：

```
SP_BP_GROUP_DEL_RAFT（
	BP_GROUP_NAME    VARCHAR(128),
	RAFT_NAME         VARCHAR(128)
）
```

功能说明：

从 BP 组中删除一个 RAFT 组。

参数说明：

BP_GROUP_NAME：指定删除的 BP 组名；

RAFT_NAME：待删除的 RAFT 组名。

举例说明：

从 BP 组 BG_1 中删除名为 RAFT_1 的 RAFT 组。

```
SP_BP_GROUP_DEL_RAFT('BG_1', 'RAFT_1');
```

### 6.2.12 SP_SP_GROUP_ADD_RAFT

定义：

```
SP_SP_GROUP_ADD_RAFT（
	SP_GROUP_NAME    VARCHAR(128),
	RAFT_NAME         VARCHAR(128)
）
```

功能说明：

向 SP 组中添加一个 RAFT 组。

参数说明：

SP_GROUP_NAME：指定添加 RAFT 组的 SP 组名；

RAFT_NAME**：**待添加的 RAFT 组名。

举例说明：

向 SP 组 SPG_1 中添加一个名为 SP_RAFT_1 的 RAFT 组。

```
SP_SP_GROUP_ADD_RAFT('SPG_1', 'SP_RAFT_1');
```

如果传入的参数 sp_group_name 并不是 SP 组或者 raft_name 不是 SP RAFT 对象，系统会因指定的对象不存在而报错。

### 6.2.13 SP_SP_GROUP_DEL_RAFT

定义：

```
SP_SP_GROUP_DEL_RAFT（
	SP_GROUP_NAME    VARCHAR(128),
	RAFT_NAME         VARCHAR(128)
）
```

功能说明：

从 SP 组中删除一个 SP RAFT。

参数说明：

SP_GROUP_NAME：指定删除的 SP 组名；

RAFT_NAME：待删除的 RAFT 组名。

举例说明：

从 SP 组 SPG_1 中删除名为 SP_RAFT_1 的 RAFT 组。

```
SP_SP_GROUP_DEL_RAFT('SPG_1', 'SP_RAFT_1');
```

### 6.2.14 SP_SET_DPC_NET_CONF

定义：

```
SP_SET_DPC_NET_CONF(
	PARA_NAME 		varchar(128),
	PARA_VALUE	varchar(128)
);
```

功能说明：

修改注册在 MP 上的通信参数。

参数说明：

PARA_NAME: 待修改的参数名称，非空；

PARA_VALUE: 修改为新的参数值，非空。

举例说明：

修改 MP 与 BP，MP 与 SP 通信时使用的参数信息，调用时同步修改系统表 SYS.DPC_NET_CONF，修改成功后需要重启全部环境来应用新参数。目前支持修改的参数为 TIMEOUT（通信超时，单位为秒）、XLNK_NUM（链路数）、MONITOR_XLNK_TIME（监控链路消息收发用时，单位为微秒）、CRC_CHECK（消息的 CRC 检验）、COMPRESS_LEVEL（消息压缩级别）。

```
SP_SET_DPC_NET_CONF('TIMEOUT', '1000');
```

为了增加网络传输效率和减少带宽利用，对 XLNK 消息统一增加 COMPRESS_LEVEL 参数，配置在 DPC_NET_CONF 系统表中。修改成功后需要重启全部节点才能生效。登录 SP 或者 MP 执行系统过程设置 XLNK 消息压缩等级。

```
SP_SET_DPC_NET_CONF('COMPRESS_LEVEL',10);
```

### 6.2.15 SP_TS_GROUP_CREATE

定义：

```
SP_TS_GROUP_CREATE(
	NAME		VARCHAR(128),
	DESC		VARCHAR(256)
)
```

功能说明：

创建表空间组。

参数说明：

NAME：表空间组名称。

DESC：表空间描述。

举例说明：

```
SP_TS_GROUP_CREATE('TSG_1', 'tablespace group1');
```

### 6.2.16 SP_TS_GROUP_DROP

定义：

```
SP_TS_GROUP_DROP(
	GROUP_NAME VARCHAR(128)
)
```

功能说明：

删除表空间组。

参数说明：

NAME：表空间组名称。

举例说明：

```
SP_TS_GROUP_DROP('TSG_1');
```

### 6.2.17 SP_TS_GROUP_ADD_TS

定义：

```
SP_TS_GROUP_ADD_TS(
	GROUP_NAME	VARCHAR(128),
    TS_NAME		VARCHAR(128)
)
```

功能说明：

表空间组添加表空间。

参数说明：

GROUP_NAME：表空间组名称；

TS_NAME：表空间名称。

举例说明：

```
SP_TS_GROUP_ADD_TS('TSG_1', 'TS1');
```

### 6.2.18 SP_TS_GROUP_REMOVE_TS

定义：

```
SP_TS_GROUP_REMOVE_TS(
	GROUP_NAME	VARCHAR(128),
	TS_NAME		VARCHAR(128)
)
```

功能说明：

表空间组移除表空间。

参数说明：

GROUP_NAME：表空间组名称；

TS_NAME：表空间名称。

举例说明：

```
SP_TS_GROUP_REMOVE_TS('TSG_1', 'TS1');
```

### 6.2.19 SP_RENAME_DPC_INSTANCE

定义：

```
SP_RENAME_DPC_INSTANCE(
	OLD_NAME	   VARCHAR(128),
	NEW_NAME	   VARCHAR(128)
)
```

功能说明：

重命名实例。

参数说明：

OLD_NAME：旧实例名；

NEW_NAME：新实例名。

举例说明：

```
SP_RENAME_DPC_INSTANCE('BP1_A', 'BP_INSTANCE_A');
```

### 6.2.20 SP_ALTER_DPC_INSTANCE

定义：

```
SP_ALTER_DPC_INSTANCE(
	NAME        	VARCHAR(128),
	SYS_MODE		VARCHAR(32),
	SYS_STATUS    INT,
	STATUS         INT   
)
```

功能说明：

修改实例的系统模式、系统状态、实例状态信息。

参数说明：

NAME：实例名；

SYS_MODE：系统模式；

SYS_STATUS：系统状态；

STATUS：实例状态。

举例说明：

```
SP_ALTER_DPC_INSTANCE('BP1','STANDBY',4,1);
```

### 6.2.21 SP_DPC_MOVE_TS_OFFLINE

定义：

```
SP_DPC_MOVE_TS_OFFLINE(
	TS_NAME		VARCHAR(128),
    RAFT_NAME		VARCHAR(128)
);
```

功能说明：

脱机方式表空间迁移时，MP 上手动修改系统表信息。只在脱机表空间迁移过程中使用。

参数说明：

TS_NAME：迁移的表空间名称；

RAFT_NAME：目标 RAFT 名。

举例说明：

将表空间 TS_1 迁移到 RAFT_1 上。

```
SP_DPC_MOVE_TS_OFFLINE('TS_1', 'RAFT_1');
```

### 6.2.22 SP_SET_DPC_INST_AUX

定义：

```
SP_SET_DPC_INST_AUX(
	 INST_NAME   VARCHAR(128),
 	 AUX_FLAG        INT
);
```

功能说明：

设置 SP 实例是否作为辅助计算节点。缺省为是。

参数说明：

INST_NAME：SP 节点的实例名；

AUX_FLAG：是否作为辅助计算节点。1 是；0 否。

举例说明：

取消 SP_1 实例作为辅助计算节点。

```
SP_SET_DPC_INST_AUX('SP_1', 0);
```

### 6.2.23 SP_ADD_RAFT_LEARNER

定义：

```
SP_ADD_RAFT_LEARNER(
	DEST VARCHAR,
	DEST_IP VARCHAR,
	DEST_PORT INT,
	DEST_ID INT
);
```

功能说明：

增加 LEARNER 节点。需在当前多副本集群的主节点上执行。

参数说明：

DEST：LEARNER 节点实例名，对应 ARCH_DEST 字段；

DEST_IP：LEARNER 节点 IP 地址，对应 ARCH_DEST_IP 字段；

DEST_PORT：LEARNER 节点通信 PORT，对应 ARCH_DEST_PORT 字段；

DEST_ID：LEARNER 节点编号，对应 ARCH_DEST_ID 字段。

返回值：

无

举例说明：

增加多副本集群的 LEARNER 节点 BP_04。

```
SP_ADD_RAFT_LEARNER('BP_04', '192.168.1.68', 10010 , 3);
```

### 6.2.24 SP_DELETE_RAFT_LEARNER

定义：

```
SP_DELETE_RAFT_LEARNER(
	DEST VARCHAR
)
```

功能说明：

删除 LEARNER 节点。需在当前多副本集群的主节点上执行。

使用细则如下：

  1. 每次执行只允许删除 1 个节点；
  2. 只允许在 LEARNER 主节点上执行，其他节点不允许执行；
  3. 仅多数 RAFT 节点正常的情况下，才允许执行；
  4. DMDPC 下的删除 LEARNER 的顺序为，先执行 SP_DELETE_RAFT_LEARNER 从 RAFT 组中删除，再使用 SP_DROP_DPC_INSTANCE 从系统表中删除节点；
  5. 和 SP_ADD_RAFT_LEARNER 策略一样，此函数仅会删除 RAFT 节点上的 LEARNER 归档配置，对于 LEARNER 节点上的配置则仍然是以手工方式进行修改。



参数说明：

DEST：LEARNER 节点实例名，对应 ARCH_DEST 字段。

返回值：

无

举例说明：

删除多副本集群的 LEARNER 节点 BP_04。

```
SP_DELETE_RAFT_LEARNER('BP_04');
```

### 6.2.25 SP_ALTER_RAFT_NODE

定义：

```
SP_ALTER_RAFT_NODE(
	NEW_NODE VARCHAR
)
```

功能说明：

执行多副本集群成员变更。将当前多副本集群变更为仅含有 NEW_NODE 节点的集群。不管是增加、删除还是替换，都可以使用这个系统函数来完成。

需在当前多副本集群的主节点上执行。

参数说明：

NEW_NODE：指定新配置的所有节点实例名字符串。可以是一个或多个实例名，多个实例名之间用“/”符号分隔，目前最多支持 9 个节点。

返回值：

无

举例说明：

将当前多副本集群变更为仅含有 BP_01、BP_02、BP_03、BP_04、BP_05 节点的集群。

```
SP_ALTER_RAFT_NODE('BP_01/BP_02/BP_03/BP_04/BP_05');
```

### 6.2.26 SP_ADD_RAFT_NODE

```
SP_ADD_RAFT_NODE(
	NEW_NODE VARCHAR
)
```

功能说明：

增加多副本集群节点。如果集群变更还包含删除或替换节点，则不建议使用这个系统函数。

需在当前多副本集群的主节点上执行。

参数说明：

NEW_NODE：指定新增的所有节点实例名。实例名之间用“/”符号分隔。加入新增节点后，新集群的节点总数最多支持 9 个节点。

返回值：

无

举例说明：

增加多副本集群的节点 BP_04、BP_05。

```
SP_ADD_RAFT_NODE('BP_04/BP_05');
```

### 6.2.27 SP_DELETE_RAFT_NODE

```
SP_DELETE_RAFT_NODE(
	DELETE_NODE VARCHAR
)
```

功能说明：

删除多副本集群节点。如果集群变更还包含增加或替换节点，则不建议使用这个系统函数。

需在当前多副本集群的主节点上执行。

参数说明：

DELETE_NODE：指定要删除的所有节点实例名。各实例名以“/”符号分隔。

返回值：

无

举例说明：

删除多副本集群的节点 BP_04、BP_05。

```
SP_DELETE_RAFT_NODE('BP_04/BP_05');
```

### 6.2.28 SP_REPLACE_RAFT_NODE

定义：

```
SP_REPLACE_RAFT_NODE(
	OLD_NODE VARCHAR,
	NEW_NODE VARCHAR
)
```

功能说明：

替换多副本集群的节点。即同时包含新增和删除节点的情况下，使用该系统函数来完成。

需在当前多副本集群的主节点上执行。

参数说明：

OLD_NODE：指定要删除的所有节点实例名。各实例名先“/”符号分隔，OLD_NODE 必须是旧配置下的多副本节点；

NEW_NODE：指定要增加的所有节点实例名。各实例名以“/”符号分隔，NEW_NODE 必须是 learner 节点。

返回值：

无

举例说明：

例 1 将 BP_02/BP_03 替换为 BP_04/BP_05。

```
SP_REPLACE_RAFT_NODE('BP_02/BP_03', 'BP_04/BP_05');
```

例 2 将 BP_02/BP_03 替换为 BP_04/BP_05，再新增 BP_06/BP_07。

```
SP_REPLACE_RAFT_NODE('BP_02/BP_03', 'BP_04/BP_05/BP_06/BP_07');
```

### 6.2.29 SP_DPC_DUMP_INST

定义：

```
SP_DPC_DUMP_INST (
	FILE_PATH			VARCHAR(256)
)
```

功能说明：

将 dpc_instance 的配置以追加的形式，转储到文本文件，以便于在 MP 故障时查看。文件路径不存在时报错。在 MP/BP/BS/SP 均能执行。

参数说明：

FILE_PATH：导出的文本文件路径。

举例说明：

```
SP_DPC_DUMP_INST('S:\DPC\C\dpc_instance.md');
```

### 6.2.30 SP_SET_SP_UPGRADE

定义：

```
SP_SET_SP_UPGRADE(
	INST_NAME		 VARCHAR(128)
)
```

功能说明：

将一个正常状态的 SP 标记为升级状态。非正常状态的 SP 无效。

只能在 SP 上运行；BP 和 MP 上无法运行。只能升级 SP。

参数说明：

INST_NAME：待升级的 SP 的实例名称。

返回值：

无

举例说明：

```
SP_SET_SP_UPGRADE('SP2');
```

### 6.2.31 SP_RESET_SP_UPGRADE

定义：

```
SP_RESET_SP_UPGRADE(
	INST_NAME		 VARCHAR(128)
)
```

功能说明：

将一个升级状态的 SP 恢复正常；非升级状态的 SP 无效。

只能在 SP 或 MP 上运行；BP 上无法运行。只能恢复 SP。

参数说明：

INST_NAME：待恢复的 SP 的实例名称。

返回值：

无

举例说明：

```
SP_RESET_SP_UPGRADE('SP2');
```

### 6.2.32 SP_DPC_REBANLANCE_SESSION

定义：

```
SP_DPC_REBANLANCE_SESSION(
	BALANCE_FLAG	 INT
)
```

功能说明：

收集当前 SP 可见的所有 SP 的会话数，并产生或清空所有节点各自的平衡方案。

参数说明：

BALANCE_FLAG：取 1 表示产生节点各自的平衡方案；取 0 表示清空各个节点的方案；不论取 0 还是取 1，都会收集可见的所有 SP 的会话数。

返回值：

无

举例说明：

```
SP_DPC_REBANLANCE_SESSION(1);
```

### 6.2.33 SP_RAFT_SUSPEND_THREAD

定义：

```
SP_RAFT_SUSPEND_THREAD()
```

功能说明：

多副本环境中，主库挂起工作线程。直连主库执行。

举例说明：

主库挂起自己的工作线程。

```
SP_RAFT_SUSPEND_THREAD();
```

### 6.2.34 SP_RAFT_SWITCHOVER

定义：

```
SP_RAFT_SWITCHOVER()
```

功能说明：

多副本环境中，将备库手动切换为主库。直连备库执行。raft 影子节点不支持通过 SP_RAFT_SWITCHOVER()过程切换为主节点。

举例说明：

备库手动切换为主库流程：

1.连接主库执行挂起

```
SP_RAFT_SUSPEND_THREAD();
```

2.连接备库执行手动切换

```
SP_RAFT_SWITCHOVER();
```

### 6.2.35 SP_RAFT_RESUME_THREAD

定义：

```
SP_RAFT_RESUME_THREAD(
	INST_NAME   VARCHAR(128)
)
```

功能说明：

多副本环境中，唤醒指定多副本组内的节点的工作线程，直连任意一个备库执行。

参数说明：

INST_NAME：待唤醒的实例名称。

举例说明：

唤醒 BP01 的工作线程。

```
SP_RAFT_RESUME_THREAD('BP01');
```

### 6.2.36 SP_TS_DROP_INVALID

定义：

```
SP_TS_DROP_INVALID()
```

功能说明：

删除本地节点有，但是 MP 中没有注册的表空间。

只能在 BP 或 BS 节点执行该系统函数。

参数说明：

无。

举例说明：

```
SP_TS_DROP_INVALID;
```

### 6.2.37   SP_CLEAR_TAB_ROWCNT_CACHE

定义：

```
SP_CLEAR_TAB_ROWCNT_CACHE (
RAFT_ID INT,
TAB_ID INT
)
```

功能说明：

清除所有或指定 SP/BS 节点上的全部或部分表行数缓存。

虽站点号指定为其他类型的节点也能成功执行，并不报错，但因其他类型节点不存在表行数缓存，执行该过程没有实际意义。

参数说明：

RAFT_ID：SP/BS 站点号。

TAB_ID：表 ID。

如果表 ID 和 SP/BS 站点号都为 NULL，就清除所有 SP/BS 站点上的全部表行数缓存；提供表 ID，但是 SP/BS 站点号为 NULL，就清除所有 SP/BS 站点上的指定表 ID 的缓存；如果表 ID 为 NULL，提供 SP/BS 站点号，就清除指定 SP/BS 站点上的所有表行数缓存。

举例说明：

清理所有 SP/BS 站点上的全部表行数缓存：

```
SP_CLEAR_TAB_ROWCNT_CACHE(NULL,NULL);
```

### 6.2.38   SP_GET_ALL_TS_BY_TSGROUP

定义：

```
SP_GET_ALL_TS_BY_TSGROUP (
TGNAME VARCHAR(128)
)
```

功能说明：

显示表空间组内的所有表空间信息。显示的列信息和系统表 DPC_INSTANCE 相同。该存储过程在单机环境下无效。

参数说明：

TGNAME：表空间组名。

举例说明：

显示表空间组 TSG_1 内的所有表空间信息：

```
SP_GET_ALL_TS_BY_TSGROUP('TSG_1');
```

### 6.2.39   SP_SET_RAFT_ASYNC_INTERVAL

定义：

```
SP_SET_RAFT_ASYNC_INTERVAL(
ARCH_DEST VARCHAR,
ASYNC_INTERVAL INT
)
```

功能说明：

重置一个 RAFT 备库或 LEARNER 备库的异步恢复时间间隔。

仅在多副本环境主库上执行有效。

对于 BS 主库，需要以 LOCAL 方式登录执行。

参数说明：

ARCH_DEST：待重置异步恢复时间间隔的节点实例名。

ASYNC_INTERVAL： 异步恢复时间间隔，单位：S(秒)，取值范围 3~86400。

返回值：

无

举例说明：

在主库上执行，设置 RAFT 备库 BP_2 的异步恢复时间间隔为 30s。

```
SP_SET_RAFT_ASYNC_INTERVAL('BP_2', 30);
```

### 6.2.40   SP_GET_RAFT_ASYNC_INTERVAL

定义：

```
SP_GET_RAFT_ASYNC_INTERVAL(

ARCH_DEST    VARCHAR

)
```

功能说明：

获取一个 RAFT 备库或 LEARNER 备库的异步恢复时间间隔。

仅在多副本环境主库上执行有效。

对于 BS 主库，需要以 LOCAL 方式登录执行。

参数说明：

ARCH_DEST：待重置异步恢复时间间隔的节点实例名。

返回值：

异步恢复时间间隔。

举例说明：

在主库上执行，获取 RAFT 备库 BP_2 的异步恢复时间间隔。

```
SP_GET_RAFT_ASYNC_INTERVAL('BP_2');
```

### 6.2.41 SP_CREATE_FAULT_DOMAIN

定义：

```
SP_CREATE_FAULT_DOMAIN(

NAME       varchar(128),

DESC       varchar(256)

)
```

功能说明：

创建一个容错域，创建后同步字典信息。可在 MP、BS、SP 上执行，BP 报错。

参数说明：

NAME：容错域名称，与已有相同名称时报错。

DESC：容错域描述，可以为空。

返回值：

无

举例说明：

创建一个名为 FDOM_1 的容错域。

```
SP_CREATE_FAULT_DOMAIN('FDOM_1', 'shanghai');
```

### 6.2.42   SP_DROP_FAULT_DOMAIN

定义：

```
SP_DROP_FAULT_DOMAIN(

NAME       varchar(128),

FORCE      INT

)
```

功能说明：

删除一个容错域，删除后同步字典信息。可在 MP、BS、SP 上执行，BP 报错。

参数说明：

NAME：容错域名称，原本就没有该容错域时报错。

FORCE：是否强制删除，0 为不强制删除：该容错域不为空时报错；1 为强制删除：将移出容错域内所有实例，然后删除容错域。

返回值：

无

举例说明：

删除一个名为 FDOM_1 的容错域。

```
SP_DROP_FAULT_DOMAIN('FDOM_1', 0);
```

### 6.2.43   SP_MODIFY_FAULT_DOMAIN

定义：

```
SP_MODIFY_FAULT_DOMAIN(

DOMAIN_ID    INT,

NAME       varchar(128),

DESC       varchar(128)

)
```

功能说明：

修改一个容错域，修改后同步字典信息。可在 MP、BS、SP 上执行，BP 报错。

参数说明：

DOMAIN_ID：容错域 ID，指定要修改哪一个容错域，id 不存在时报错。

NAME：修改后的容错域名称，与已有相同名称时报错，为 NULL 表示不修改。

DESC：修改后的容错域描述，为 NULL 时表示不修改。

返回值：

无

举例说明：

修改一个名为 FDOM_1 的容错域。

```
SP_MODIFY_FAULT_DOMAIN(1, 'FDOM_2', 'shanghai123'); --同时修改0号域的名称和描述

SP_MODIFY_FAULT_DOMAIN(1, NULL, ''); --不修改名称，同时描述清空

SP_MODIFY_FAULT_DOMAIN(1, 'FDOM_3', NULL); --修改名称，描述不修改
```

### 6.2.44   SP_FAULT_DOMAIN_MV_INST

定义：

```
SP_FAULT_DOMAIN_MV_INST(

DOMAIN_NAME   varchar(128),

INST_NAME    varchar(128)

)
```

功能说明：

将实例移入或移出某个容错域，调整后同步字典信息。可在 MP、BS、SP 上执行，BP 报错。

参数说明：

DOMAIN_NAME：容错域名称，容错域不存在时报错；取 NULL 时将尝试实例移出其所在当前容错域；容错域不为 NULL 且存在时，需要容错域中无相同 RAFT 组时才能成功移入，否则将报错。

INST_NAME：实例名称，实例不存在时报错。仅允许 MP,BP,BS 实例加入，SP 实例会报错。

返回值：

无

举例说明：

将实例 MP1 移入容错域 FDOM_1。将实例 BP3 移出当前容错域。

```
SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'MP1'); --将MP1移入FDOM_1

SP_FAULT_DOMAIN_MV_INST(NULL, 'BP3'); --将BP3移出它所在的当前容错域
```

### 6.2.45 SP_SET_RAFT_ELECT

定义：

```
SP_SET_RAFT_ELECT(

ELECT_FLAG       dmbool

)
```

功能说明：

设置当前 RAFT 库节点的选举开关。关闭选举开关的节点不会进行超时检测，不会主动发起选举，但收到其他节点的投票请求后，如果对方符合条件，可以正常投出选票。打开选举开关的节点检测超时后可以正常发起选举和投出选票。

选举开关默认情况下均为打开状态。SP_SET_RAFT_ELECT 仅修改内存值，节点重启后失效。对于 BS 主库节点需要 LOCAL 方式登录执行。

参数说明：

ELECT_FLAG：设置选举开关，0：关闭选举开关；1：打开选举开关。

返回值：

无

举例说明：

关闭当前 RAFT 库节点的选举开关。

```
SP_SET_RAFT_ELECT(0);
```

### 6.2.46 SF_GET_RAFT_ELECT

定义：

```
SF_GET_RAFT_ELECT()
```

功能说明：

获取当前 RAFT 库节点的选举开关。对于 BS 主库节点需要 LOCAL 方式登录执行。

参数说明：

无

返回值：

0：选举开关为关闭状态；1：选举开关为打开状态。

举例说明：

获取当前 RAFT 库节点的选举开关。

```
SELECT SF_GET_RAFT_ELECT();
```

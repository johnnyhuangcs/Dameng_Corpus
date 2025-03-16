

# DMDSC 配置文件

与 DMDSC 相关的配置文件包括：

  * DMDCR_CFG.INI
  * DMDCR.INI
  * DMINIT.INI
  * MAL 系统配置文件（DMMAL.INI、DMASVRMAL.INI）
  * DM.INI
  * DMARCH.INI



下面分别介绍配置文件中各配置项的含义。

## 9.1 DMDCR_CFG.INI

DMDCR_CFG.INI 是格式化非镜像环境下 DCR DISK、VOTE DISK 和镜像环境下 DCRV 磁盘的配置文件。

配置信息包括三类：集群环境全局信息、集群组信息、以及组内节点信息。

集群环境全局信息为所有集群的公共信息，整个文件中只需要出现一次。例如:设置 DMCSS 集群、DMASM 集群和 DMDSC 集群三个集群的公共信息。

集群组信息用来设置一个集群中所有节点的公共信息，一个集群只需配置一个集群组信息。例如:设置 DMCSS 集群中两个节点 CSS0 和 CSS1 的公共信息。

组内节点信息，集群组中各节点的信息。例如：分别设置节点 CSS0 和 CSS1 的各自的节点信息。

使用 DMASMCMD 工具，可以根据 DMDCR_CFG.INI 配置文件，格式化 DCR DISK 和 VOTE DISK。

表9.1 DMDCR_CFG.INI配置项



使用说明

  1. 在用 dmasmcmd 工具执行 init votedisk disk_path from dcr_cfg_path 时，指定的 disk_path 必须和 dcr_cfg_path 里面配置的 DCR_VTD_PATH 相同。
  2. 如果配置 dmcssm，dmcssm 的 OGUID 必须和 DCR_OGUID 保持一致。
  3. DCR_N_GRP 必须和实际配置的组数目保持一致。
  4. CSS 和 ASM 组的 DCR_GRP_N_EP 要相等，DB 的 DCR_GRP_N_EP 要小于等于 CSS/ASM 的 DCR_GRP_N_EP。
  5. ASM 节点的 DCR_EP_NAME 必须和 DMASM 系统使用的 DMASVRMAL.INI 配置文件里的 MAL_INST_NAME 保持一致。
  6. DB 节点的 DCR_EP_NAME 必须和数据库实例使用 DMMAL.INI 配置文件里的 MAL_INST_NAME、以及 DM.INI 配置文件里的 INSTANCE_NAME 保持一致。
  7. 所有 DB 节点的 DCR_EP_NAME 都不能重复，DB 组内的 DCR_EP_SEQNO 不能重复。
  8. DMDCR_CFG.INI 配置文件中的所有路径均不支持中文路径。



举例说明

```
//集群环境全局信息
DCR_N_GRP           = 3
DCR_VTD_PATH        = /dev/raw/raw2
DCR_OGUID       = 63635

//DMCSS集群组的信息
[GRP]                //[GRP]表示新建一个Group
DCR_GRP_TYPE        = CSS
DCR_GRP_NAME        = CSS
DCR_GRP_N_EP        = 2
DCR_GRP_DSKCHK_CNT  = 60
//CSS0节点的信息
[CSS]                 //[]里的是组名，与DCR_GRP_NAME对应
DCR_EP_NAME         = CSS0
DCR_EP_HOST         = 10.0.2.101
DCR_EP_PORT         = 9341
//CSS1节点的信息
[CSS]                 //[]里的是组名，与DCR_GRP_NAME对应
DCR_EP_NAME         = CSS1
DCR_EP_HOST         = 10.0.2.102
DCR_EP_PORT         = 9343

//DMASM集群组的信息
[GRP]                //[GRP]表示新建一个Group
DCR_GRP_TYPE        = ASM
DCR_GRP_NAME        = ASM
DCR_GRP_N_EP        = 2
DCR_GRP_DSKCHK_CNT  = 60
//ASM0节点的信息
[ASM]                 //[]里的是组名，与DCR_GRP_NAME对应
DCR_EP_NAME         = ASM0
DCR_EP_SHM_KEY      = 93360
DCR_EP_SHM_SIZE     = 20
DCR_EP_HOST         = 10.0.2.101
DCR_EP_PORT         = 9349
DCR_EP_ASM_LOAD_PATH = /dev/raw
//ASM1节点的信息
[ASM]                 //[]里的是组名，与DCR_GRP_NAME对应
DCR_EP_NAME         = ASM1
DCR_EP_SHM_KEY      = 93361
DCR_EP_SHM_SIZE     = 20
DCR_EP_HOST         = 10.0.2.201
DCR_EP_PORT         = 9351
DCR_EP_ASM_LOAD_PATH = /dev/raw

//DMDSC集群组的信息
[GRP]               //[GRP]表示新建一个Group
DCR_GRP_TYPE        = DB
DCR_GRP_NAME        = DSC
DCR_GRP_N_EP        = 2
DCR_GRP_DSKCHK_CNT  = 60
//DSC01节点的信息
[DSC]                 //[]里的是组名，与DCR_GRP_NAME对应
DCR_EP_NAME        = DSC01
DCR_EP_SEQNO	  = 0
DCR_EP_PORT		  = 5236
//DSC02节点的信息
[DSC]                 //[]里的是组名，与DCR_GRP_NAME对应
DCR_EP_NAME     = DSC02
DCR_EP_SEQNO	  = 1
DCR_EP_PORT		  = 5237
```

## 9.2 DMDCR.INI

DMDCR.INI 是 DMCSS、DMASMSVR、DMASMTOOL 等工具的输入参数。记录了当前节点序列号以及 DCR 磁盘路径。

表9.2 DMDCR.INI配置项



使用说明

  1. DMASMSVR 和 DMSERVER 使用不同的 MAL 系统，需要配置两套 MAL 系统，配置文件 DMMAL.INI 需要分别生成，保存到不同的目录下，并且 DMMAL.INI 中的配置项不能重复、冲突。
  2. DMDSC 集群环境下，只有当所有 OK 节点实例都启动时，整个集群环境才能启动。可能存在某些场景，部分 OK 节点无法正常启动，导致整个集群环境无法正常启动。指定参数 DMDCR_AUTO_OPEN_CHECK 后，如果超过指定时间节点实例还未启动，DMCSS 自动将未启动节点踢出集群环境，变为 ERROR 节点，之后其他活动 OK 节点可以正常启动。



DMDSC 中至少一个 OK 节点启动后 DMCSS 才开始检查，所有 OK 节点都未启动情况下，DMCSS 不会主动踢出节点。

  3. DMCSS 自动拉起故障 DMASMSVR 或 DMSERVER 实例。



故障认定间隔和启动命令串是配合使用的，DMASMSVR 和 DMSERVER 实例需要各自配置，如果没有配置启动命令串，故障间隔即使配置为大于 0 的值 DMCSS 也不会执行自动拉起操作，DMDCR_ASM_RESTART_INTERVAL 和 DMDCR_DB_RESTART_INTERVAL 默认的 60S 只有在配置有启动命令串时才会起作用。

DMDCR_ASM_STARTUP_CMD 和 DMDCR_DB_STARTUP_CMD 的配置方法相同，只是执行码名称和 ini 配置文件路径有区别，可以配置为服务名或命令行启动方式。

  * DMASMSVR 实例启动命令举例如下：



1）linux 命令行方式启动（不能出现带有空格的路径）：

```
DMDCR_ASM_STARTUP_CMD = /opt/dmdbms/bin/dmasmsvr dcr_ini=/home/data/dmdcr.ini
```

2）linux 服务方式启动：

```
DMDCR_ASM_STARTUP_CMD = service dmasmserverd restart
```

3）Windows 命令行启动：

```
DMDCR_ASM_STARTUP_CMD = c:\dm\bin\dmasmsvr.exe  dcr_ini=d:\asmtest\dmdcr.ini
```

4）Windows 服务方式启动:

```
DMDCR_ASM_STARTUP_CMD = net start 注册服务名
```

  * DMSERVER 实例启动命令如下：



1）linux 命令行方式启动（不能出现带有空格的路径）：

```
DMDCR_DB_STARTUP_CMD = /opt/dmdbms/bin/dmserver path=/home/data/dsc0_config/dm.ini dcr_ini=/home/data/dmdcr.ini
```

2）linux 服务方式启动：

```
DMDCR_DB_STARTUP_CMD = service DmServiceDSC01 restart
```

3）Windows 命令行启动：

```
DMDCR_DB_STARTUP_CMD = c:\dm\bin\dmserver.exe path=d:\asmtest\dsc0_config\dm.ini dcr_ini=d:\asmtest\dmdcr.ini
```

4）Windows 服务方式启动:

```
DMDCR_DB_STARTUP_CMD = net start 注册服务名
```

  4. DMDCR_LINK_CHECK_IP 使用场景



当 2 节点 DSC 集群节点间出现 MAL 网络异常时，如果两个节点实例都正常运行，DMCSS 的处理逻辑是主动 HALT 节点号大的 DSC1 节点，保留节点号小的 DSC0 节点。如果真实情况是 DSC0 和应用的网络断了，而 DSC1 和应用的网络是好的，那么 DMCSS 这种保留节点号小的逻辑就不合适。因此，须配置 DMDCR_LINK_CHECK_IP 参数来解决上述问题。

DMDCR_LINK_CHECK_IP 成功配置之后，当集群中出现节点间 MAL 网络异常时，节点还需要尝试联通一下 DMDCR_LINK_CHECK_IP 配置的 IP（类似于 ping 功能），DMCSS 将联通结果共同作为处理网络异常的参考依据。例如：上述场景下，DSC0 和第三方确认 IP 无法联通，DSC1 和第三方确认 IP 可以联通，那么 DMCSS 综合判断之后主动 HALT 掉 DSC0 节点，保留了正常的 DSC1 节点。

如果没有配置 DMDCR_LINK_CHECK_IP，或者配置错误，或者没有赋予进程权限，集群所有节点尝试联通 DMDCR_LINK_CHECK_IP 时都会失败，那么 DMCSS 依然是保留节点号小的逻辑。

举例说明

```
DMDCR_PATH=/dev/raw/raw1
DMDCR_SEQNO=0
DMDCR_MAL_PATH=/home/data/dmasvrmal.ini

//故障认定间隔，以及启动命令中的执行码路径和INI路径需要根据实际情况调整
DMDCR_ASM_RESTART_INTERVAL = 60
DMDCR_ASM_STARTUP_CMD = /opt/dmdbms/bin/dmasmsvr dcr_ini=/home/data/dmdcr.ini

DMDCR_DB_RESTART_INTERVAL = 60
DMDCR_DB_STARTUP_CMD = /opt/dmdbms/bin/dmserver path=/home/data/dsc0_config/dm.ini dcr_ini=/home/data/dmdcr.ini
```

## 9.3 DMINIT.INI

DMINIT.INI 是 DMINIT 工具初始化数据库环境的配置文件。与初始化库使用普通文件系统不同，使用 DMASM 文件系统，必须使用 DMINIT 工具的 control 参数指定 DMINIT.INI 文件。DMINIT 工具的命令行参数都可以放在 DMINIT.INI 中，比如 db_name、auto_overwrite 等，DMINIT.INI 参数分为全局参数和节点参数。DMINIT 工具具体用法可以参考《DM8_dminit 使用手册》，下面列出常用的一些参数。

表9.3 DMINIT.INI配置项



使用说明

  1. SYSTEM_PATH 支持 ASM 文件系统，如果不指定 MAIN/SYSTEM/ROLL/CTL_PATH/HUGE_PATH，则数据文件、控制文件和 MAIN 表空间的 HUGE 数据文件目录都会默认生成在 SYSTEM_PATH/db_name 下面。
  2. 表空间路径和 DM.CTL 路径支持普通操作系统路径、块设备、ASM 文件路径，如果指定必须指定 SIZE。
  3. 只有 DMINIT 工具使用 DMASM 文件系统，才会用到 DCR_PATH 和 DCR_SEQNO，并且不会写入其他配置文件。
  4. CONFIG_PATH 暂时只支持本地磁盘，不支持 ASM 文件系统。DSC 环境配置，各节点的 CONFIG_PATH 要指定不同路径。
  5. MAL_HOST 和 MAL_PORT 是为了自动创建 DMMAL.INI 文件使用，只有在初始化 DSC 环境时需要指定。
  6. LOG_PATH 可以不指定，默认会在 SYSTEM_PATH 生成。如果指定，必须指定两个以上。
  7. 必须指定实例名，也就是必须配置[XXX]。
  8. DMINIT 工具的 control 参数只能独立使用，不能和其他任何参数一起使用。



举例说明

```
system_path= +DMDATA/data
db_name= dsc
main= +DMDATA/data/dsc/main.dbf
main_size= 128
roll = +DMDATA/data/dsc/roll.dbf
roll_size= 128
system = +DMDATA/data/dsc/system.dbf
system_size= 128
ctl_path = +DMDATA/data/dsc/dm.ctl
ctl_size = 8
log_size = 256
dcr_path = /dev/raw/raw1
dcr_seqno = 0
auto_overwrite=1

[dsc01]
config_path = /home/data/config1
port_num = 5236
mal_host = 10.0.2.101
mal_port = 9340
log_path = +DMDATA/log/log101.log
log_path = +DMDATA/log/log102.log
[dsc02]
config_path = /home/data/config2
port_num = 5237
mal_host = 10.0.2.102
mal_port = 9341
log_path = +DMDATA/log/log201.log
log_path = +DMDATA/log/log202.log
```

## 9.4 MAL 系统配置文件

MAL 配置文件包括 DMMAL.INI 和 DMASVRMAL.INI。使用同一套 MAL 系统的所有实例，MAL 系统配置文件须严格保持一致。

DMDSC 使用 MAL 配置文件的前提条件：

一 DMASMSVR 和 DMDSC 集群中的 DMSERVER 实例需要分别配置一套独立的 MAL 系统，两者配置的 MAL 环境不能冲突。

二 DMASMSVR 组成的集群环境使用一套 MAL 系统进行通讯，需要在 DMDCR.INI 配置文件中配置 DMDCR_MAL_PATH 参数，指定 MAL 配置文件路径。例如：DMDCR_MAL_PATH =/home/data/dmasvrmal.ini。

三 使用 MAL 系统的 DMSERVER 实例，需要将 DM.INI 配置项 MAL_INI 设置为 1。同时 MAL 系统配置文件名称必须为 DMMAL.INI。

### 9.4.1 DMMAL.INI

 表 9.4 DMMAL.INI 配置项 

|**配置项**|**配置含义**|
|----|----|
|MAL_CHECK_INTERVAL|MAL 链路检测时间间隔，取值范围 0~1800，单位秒（s），缺省为 30s，配置为 0 表示不进行 MAL 链路检测|
|MAL_CONN_FAIL_INTERVAL|判定实例之间 MAL 链路断开的时间，取值范围 2~1800，单位秒（s），缺省为 10s。  仅当 MAL_CHECK_INTERVAL 不为 0 时有效|
|MAL_CHECK_TIMEOUT|单个实例的 MAL 网络最大延迟时间，单位毫秒（ms），取值范围 0~65535，缺省为 0，表示不进行 MAL 网络延迟时间检测。需要和 MAL_CHECK_IP 配合使用。  仅当 MAL_CHECK_INTERVAL 不为 0 时有效|
|MAL_CHECK_IP|第三方确认机器的 IP，用于检测各个实例的 MAL 网络延迟时间。需要和 MAL_CHECK_TIMEOUT 配合使用。确认机器为 MAL 链路所有实例均可访问到的独立外网机器，MAL 链路实例包括 DMSERVER、DMASMSVR（非 DMASM 镜像）和 DMASMSVRM（DMASM 镜像）。  实例每隔 MAL_CHECK_INTERVAL 时间 ping 一次 MAL_CHECK_IP，若连接耗时超过 MAL_CHECK_TIMEOUT 指定时间，则该实例主动 HALT。  开启 MAL 网络延迟时间检测后，需要为 MAL 链路所有实例赋予 ping 权限，具体方法可以参考 DMDCR_LINK_CHECK_IP 参数介绍|
|MAL_LOGIN_TIMEOUT|MPP/DBLINK 等实例间登录时的超时检测间隔，取值范围 3~1800，单位 S，缺省为 15s|
|MAL_BUF_SIZE|单个 MAL 缓存大小限制，以兆为单位。当此 MAL 的缓存邮件超过此大小，则会将邮件存储到文件中。取值范围 0~500000，缺省为 100|
|MAL_SYS_BUF_SIZE|MAL 系统总内存大小限制，单位 MB。取值范围 0~500000，缺省为 0，表示 MAL 系统无总内存限制|
|MAL_VPOOL_SIZE|MAL 系统使用的内存初始化大小，单位 MB。取值范围 1~500000，缺省为 128，此值一般要设置的比 MAL_BUF_SIZE 大一些|
|MAL_COMPRESS_LEVEL|MAL 消息压缩等级，取值范围 0~10。缺省为 0，不进行压缩；1~9 表示采用 zip 算法，从 1 到 9 表示压缩速度依次递减，压缩率依次递增；10 表示采用 snappy 算法，压缩速度高于 zip 算法，压缩率相对低|
|MAL_MESSAGE_CHECK|是否对 MAL 通信消息启用消息体校验（只有当消息的发送端和接收端都配置为 1 才启用消息体校验）。0：不启用；1：启用。缺省为 1|
|[MAL_NAME]|MAL 名称，同一个配置文件中 MAL 名称需保持唯一性|
|MAL_INST_NAME|数据库实例名，与 DM.INI 的 INSTANCE_NAME 配置项保持一致，MAL 系统中数据库实例名要保持唯一|
|MAL_HOST|MAL IP 地址，使用 MAL_HOST+MAL_PORT 创建 MAL 链路。 对于 IPv6 地址，若当前环境存在多块网卡，需要用 % 号指定具体有效的网卡序号或网卡名称；若只有一块网卡或者已配置默认网卡，则可以不指定序号或名称。例如：MAL_HOST = fe80::610e:9715:5ec6:4ea8%ens01，其中 ens01 为当前环境使用的网卡名称|
|MAL_PORT|MAL 监听端口，用于数据守护、DSC、MPP 等环境中各节点实例之间 MAL 链路配置，监听端端口配置此参数，取值范围 1024~65534，发起连接端的端口在 1024~65535 之间随机分配|
|MAL_INST_HOST|MAL_INST_NAME 实例对外服务 IP 地址|
|MAL_INST_PORT|MAL_INST_NAME 实例服务器监听通讯端口号，服务器配置此参数，取值范围 1024~65534，发起连接端的端口在 1024~65535 之间随机分配  此参数的配置应与 DM.INI 中的 PORT_NUM 保持一致|
|MAL_DW_PORT|MAL_INST_NAME 实例守护进程的监听端口，其他守护进程或监视器使用 MAL_HOST  + MAL_DW_PORT 创建与该实例守护进程的 TCP 连接，监听端配置此参数，取值范围 1024~65534，发起连接端的端口在 1024~65535 之间随机分配。缺省为 0，表示不配置端口号，无法监听|
|MAL_USE_RDMA|Linux 环境下，服务器是否支持 RDMA 通讯方式。0：否，使用 TCP 方式；1：是，使用 RDMA 方式。 当 USE_RDMA=1 时，DPI 可以使用 RDMA 方式登录服务器。使用 RDMA 之前需先安装好迈络思 ofed 驱动（RDMA 协议的基础库 libibverbs.so 和 librdmacm.so）|


### 9.4.2   DMASVRMAL.INI

表 9.5 DMASVRMAL.INI 配置项

|**配置项**|**配置含义**|
|----|----|
|MAL_CHECK_INTERVAL|MAL 链路检测时间间隔，取值范围 0~1800，单位秒（s），缺省为 30s，配置为 0 表示不进行 MAL 链路检测|
|MAL_CONN_FAIL_INTERVAL|判定实例之间 MAL 链路断开的时间，取值范围 2~1800，单位秒（s），缺省为 10s。  仅当 MAL_CHECK_INTERVAL 不为 0 时有效|
|MAL_CHECK_TIMEOUT|单个实例的 MAL 网络最大延迟时间，单位毫秒（ms），取值范围 0~65535，缺省为 0，表示不进行 MAL 网络延迟时间检测。需要和 MAL_CHECK_IP 配合使用。  仅当 MAL_CHECK_INTERVAL 不为 0 时有效|
|MAL_CHECK_IP|第三方确认机器的 IP，用于检测各个实例的 MAL 网络延迟时间。需要和 MAL_CHECK_TIMEOUT 配合使用。确认机器为 MAL 链路所有实例均可访问到的独立外网机器，MAL 链路实例包括 DMSERVER、DMASMSVR（非 DMASM 镜像）和 DMASMSVRM（DMASM 镜像）。  实例每隔 MAL_CHECK_INTERVAL 时间 ping 一次 MAL_CHECK_IP，若连接耗时超过 MAL_CHECK_TIMEOUT 指定时间，则该实例主动 HALT。  开启 MAL 网络延迟时间检测后，需要为 MAL 链路所有实例赋予 ping 权限，具体方法可以参考 DMDCR_LINK_CHECK_IP 参数介绍|
|MAL_LOGIN_TIMEOUT|MPP/DBLINK 等实例间登录时的超时检测间隔，取值范围 3~1800，单位 S，缺省为 15s|
|MAL_BUF_SIZE|单个 MAL 缓存大小限制，以兆为单位。当此 MAL 的缓存邮件超过此大小，则会将邮件存储到文件中。取值范围 0~500000，缺省为 100|
|MAL_SYS_BUF_SIZE|MAL 系统总内存大小限制，单位 MB。取值范围 0~500000，缺省为 0，表示 MAL 系统无总内存限制|
|MAL_VPOOL_SIZE|MAL 系统使用的内存初始化大小，单位 MB。取值范围 1~500000，缺省为 128，此值一般要设置的比 MAL_BUF_SIZE 大一些|
|MAL_COMPRESS_LEVEL|MAL 消息压缩等级，取值范围 0~10。缺省为 0，不进行压缩；1~9 表示采用 zip 算法，从 1 到 9 表示压缩速度依次递减，压缩率依次递增；10 表示采用 snappy 算法，压缩速度高于 zip 算法，压缩率相对低|
|MAL_MESSAGE_CHECK|是否对 MAL 通信消息启用消息体校验（只有当消息的发送端和接收端都配置为 1 才启用消息体校验）。0：不启用；1：启用。缺省为 1|
|[MAL_NAME]|MAL 名称，同一个配置文件中 MAL 名称需保持唯一性|
|MAL_INST_NAME|数据库实例名，与 DM.INI 的 INSTANCE_NAME 配置项保持一致，MAL 系统中数据库实例名要保持唯一|
|MAL_HOST|MAL IP 地址，使用 MAL_HOST+MAL_PORT 创建 MAL 链路。 对于 IPv6 地址，若当前环境存在多块网卡，需要用 % 号指定具体有效的网卡序号或网卡名称；若只有一块网卡或者已配置默认网卡，则可以不指定序号或名称。例如：MAL_HOST = fe80::610e:9715:5ec6:4ea8%ens01，其中 ens01 为当前环境使用的网卡名称|
|MAL_PORT|MAL 监听端口，用于数据守护、DSC、MPP 等环境中各节点实例之间 MAL 链路配置，监听端端口配置此参数，取值范围 1024~65534，发起连接端的端口在 1024~65535 之间随机分配|
|MAL_INST_HOST|MAL_INST_NAME 实例对外服务 IP 地址|
|MAL_INST_PORT|MAL_INST_NAME 实例服务器监听通讯端口号，服务器配置此参数，取值范围 1024~65534，发起连接端的端口在 1024~65535 之间随机分配  此参数的配置应与 DMDCR_CFG.INI 中的 PORT_NUM 保持一致|
|MAL_LINK_MAGIC|用以区分不同网段。取值范围 0~65534。默认为 0。|


## 9.5 DM.INI

DM.INI 是 DMSERVER 使用的配置文件，详细介绍可以参考《DM8 系统管理员手册》相关章节。

各 DMDSC 节点之间的部分 INI 参数必须保持一致，如果不一致，会导致后启动的节点启动失败，日志会记录失败原因。每个节点使用 SQL 语句或者系统函数修改本节点的 INI 时，DMDSC 会将新修改值同步到其它节点，始终保持 INI 参数值的一致性。

对于节点间必须保持一致的 INI 参数，用户使用 SQL 语句或者系统函数修改参数值时，建议指定修改 INI 文件中的参数值，若仅修改内存中的参数值，则当部分节点故障重启时，可能出现节点间参数不一致的情况。

须保持一致的 INI 参数可通过 V$DM_INI 视图查询：

```
SELECT * FROM V$DM_INI  WHERE SYNC_LEVEL='MUST_SYNC';
```

另外，下面几个参数需要按要求进行配置：

  * INSTANCE_NAME：DMSERVER 如果需要使用 DMASM 文件系统，INSTANCE_NAME 必须和 DMDCR_CFG.INI 里面配置的 DCR_EP_NAME 相同；
  * MAL_INI：必须为 1，可缺省。在 DMDSC 系统中，为了确保通信正常，MAL_INI 缺省或为 0 的情况下，系统会将 MAL_INI 强制设置为 1；
  * DSC_USE_SBT： 是否使用辅助的平衡二叉树。取值 0 或 1。1：是，0：否。缺省为 0。DM 的检查点机制要求 BUFFER 更新链表保持有序性，所有被修改过的数据页，要根据其第一次修改的 LSN 值（FIRST_MODIFIED_LSN）从小到大有序排列。而 DMDSC 的缓存交换机制要求数据页在节点间传递，当一个更新页 P1 由普通节点 EP0 传递到节点 EP1 时，为了不破坏节点 EP1 更新链表 FIRST_MODIFIED_LSN 的有序性，需要扫描更新链，将 P1 加入到更新链中的合适位置。极端情况下，可能需要扫描整个更新链，才能找到 P1 的插入位置，在 BUFFER 比较大，更新链表比较长的情况下，这种扫描、定位代价十分昂贵，特别是在高并发情况下，会引发严重的并发冲突，影响并发性能。开启 DSC_USE_SBT 参数后，系统内部维护一个平衡二叉树，在将数据页加入更新链表时，根据这个平衡二叉树进行 log2N 次比较，就可以找到插入位置；
  * DSC_N_POOLS：LBS/GBS 池数目。取值范围 2~1024，缺省为 19。与 BUFFER_POOLS 类似，DSC_N_POOLS 将系统中的 LBS/GBS 根据页号进行分组，以降低 LBS/GBS 处理的并发冲突；
  * CONFIG_PATH：指定 DMSERVER 所读取的配置文件（DMMAL.INI、DMARCH.INI、DMTIMER.INI 等）的路径。不允许指定 ASM 目录。缺省使用 SYSTEM_PATH 路径。如果 SYSTEM_PATH 保存在 ASM 上，则直接报错；
  * TRACE_PATH：存放系统 trace 文件的路径。不允许指定 ASM 目录。默认的 TRACE_PATH 是 SYSTEM_PATH；如果 SYSTEM_PATH 保存在 ASM 上，则../config_path/trace 作为 TRACE_PATH。



## 9.6  DMARCH.INI

DMARCH.INI 是开启本地归档或远程归档时使用的配置文件。不开启归档，可不用配置 DMARCH.INI。更多 DMARCH.INI 用法请参考《DM8 数据守护与读写分离集群 V4.0》。

## 9.7  DMCSSM.INI

DMCSSM.INI 是 DMCSSM 监视器的配置文件。具体请参考 [15.1.2 配置文件 DMCSSM.INI](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-monitor.html#15.1.2%20%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6%20DMCSSM.INI)。

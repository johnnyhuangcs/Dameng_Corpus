

# DMDPC 集群部署

一个最小的 DMDPC 集群包含一个 BP、一个 SP 和一个 MP。可以在部署完毕后随时增添新的 BP、SP 节点。至少需要两台 BP 才方便看出子任务的各种计划形态和调度。

Windows 和 Linux 环境下部署 DMDPC 集群并无区别，目前支持借助命令行工具部署和 DEM 图形化界面方式部署两种方式。

## 7.1 命令行工具部署 DMDPC（单副本架构）

DMDPC 集群规划部署 1 个 SP，2 个 BP 和 1 个 MP。其中，BP 采用单机模式，未配置为多副本系统。

IP 和端口分配如下表所示，仅用作示例，需按实际 IP 进行设置。

表7.1 集群规划示意

|**RAFT 组名**|**角色**|**实例名称**|**IP**|**PORT_NUM**|**AP_PORT_NUM**|**路径**|
|-----------|-----------|-----------|-----------|-----------|-----------|-----------|
|RAFT_SP1|SP|SP1|223.254.30.136|5236|6000|d:\dpc_data_mac\sp1|
|RAFT_1|BP|BP1|223.254.30.136|5237|6001|d:\dpc_data_mac\bp1|
|RAFT_2|BP|BP2|223.254.30.136|5238|6002|d:\dpc_data_mac\bp2|
|缺省为 NULL 或者 MP_RAFT|MP|MP|223.254.30.136|5239|6003|d:\dpc_data_mac\mp|


以下章节就以在同一台 Windows 主机为例介绍如何部署。

### 7.1.1 初始化数据库实例

初始化 4 个数据库实例，实例名称分别为 SP1、BP1、BP2 和 MP。

```
dminit path=d:\dpc_data_mac\sp1 instance_name=SP1 port_num=5236 ap_port_num=6000 dpc_mode=SP 
dminit path=d:\dpc_data_mac\bp1 instance_name=BP1 port_num=5237 ap_port_num=6001 dpc_mode=BP 
dminit path=d:\dpc_data_mac\bp2 instance_name=BP2 port_num=5238 ap_port_num=6002 dpc_mode=BP
dminit path=d:\dpc_data_mac\mp  instance_name=MP  port_num=5239 ap_port_num=6003 dpc_mode=MP
```

### 7.1.2 为 SP、BP 和 MP 配置 MP.INI 文件

为 SP、BP 和 MP 实例配置 MP.INI 文件。

MP.INI 文件内容如下：

```
mp_host = 223.254.30.136
mp_port = 9000 #与MP、BP和SP上的ap_port_num不冲突的端口号
```

将 MP.INI 文件内容分别写入 SP（SP1）、BP（BP1、BP2）和 MP 中。打开 cmd 命令行工具执行:

```
//为SP1配置MP.INI文件
echo mp_host = 223.254.30.136 >d:\dpc_data_mac\sp1\DAMENG\mp.ini
echo mp_port = 9000      #与MP、BP和SP上的ap_port_num不冲突的端口号>> d:\dpc_data_mac\sp1\DAMENG\mp.ini
//为BP1配置MP.INI文件
echo mp_host = 223.254.30.136 > d:\dpc_data_mac\bp1\DAMENG\mp.ini
echo mp_port = 9000    #与MP、BP和SP上的ap_port_num不冲突的端口号>> d:\dpc_data_mac\bp1\DAMENG\mp.ini
//为BP2配置MP.INI文件
echo mp_host = 223.254.30.136 > d:\dpc_data_mac\bp2\DAMENG\mp.ini
echo mp_port = 9000     #与MP、BP和SP上的ap_port_num不冲突的端口号>> d:\dpc_data_mac\bp2\DAMENG\mp.ini
//为MP配置MP.INI文件
echo mp_host = 223.254.30.136 >d:\dpc_data_mac\mp\DAMENG\mp.ini
echo mp_port = 9000     #与MP、BP和SP上的ap_port_num不冲突的端口号>> d:\dpc_data_mac\mp\DAMENG\mp.ini
```

### 7.1.3 启动 MP

启动 MP。

```
dmserver d:\dpc_data_mac\mp\DAMENG\dm.ini dpc_mode=MP
```

DMDPC 运行过程中，MP 需要始终处于开启状态。

### 7.1.4 将 MP、SP 和 BP 加入集群

增加 1 个 MP、1 个 SP 和 2 个 BP 节点。只有在注册当前登录 MP 节点后，才可以注册其余节点。后续增加 MP、SP 节点和 BP 节点无先后之分。

```
//搭建DMDPC过程中加入MP、SP和BP，必须登录MP进行操作
DIsql SYSDBA/******@223.254.30.136:5239

//增加MP节点
//注册当前MP实例，MP的RAFT组名可以指定为NULL或者'MP_RAFT'
SP_CREATE_DPC_INSTANCE(NULL,'MP','MP',6003,5239, '223.254.30.136', '223.254.30.136','NORMAL',1,'MP instance');

//增加两个BP节点：BP1和BP2
//注册RAFT组，名为RAFT_1
SP_CREATE_DPC_RAFT('BP','RAFT_1');
//在RAFT_1组内注册BP实例BP1
SP_CREATE_DPC_INSTANCE('RAFT_1','BP1','BP',6001,5237, '223.254.30.136', '223.254.30.136','NORMAL',1,'BP instance');

//注册RAFT_2
SP_CREATE_DPC_RAFT('BP', 'RAFT_2');
//在RAFT_2内注册BP实例BP2
SP_CREATE_DPC_INSTANCE('RAFT_2','BP2','BP',6002,5238, '223.254.30.136', '223.254.30.136', 'NORMAL', 1, 'BP instance');

//注册一个BP组，名为BG_1
SP_CREATE_DPC_BP_GROUP('BG_1', 'bp group1');
//往BP组中添加RAFT组
SP_BP_GROUP_ADD_RAFT('BG_1', 'RAFT_1');
SP_BP_GROUP_ADD_RAFT('BG_1', 'RAFT_2');

//增加SP节点：SP1
//增加SP，也要注册RAFT组
SP_CREATE_DPC_RAFT('SP', 'RAFT_SP1');
//在RAFT_SP1内注册SP实例SP1
SP_CREATE_DPC_INSTANCE('RAFT_SP1','SP1','SP',6000,5236, '223.254.30.136', '223.254.30.136','NORMAL', 2, 'SP instance');

//注册一个容错域：FDOM_1 （可选）
SP_CREATE_FAULT_DOMAIN ('FDOM_1','shanghai');
//往容错域中添加实例
SP_FAULT_DOMAIN_MV_INST('FDOM_1','MP');
SP_FAULT_DOMAIN_MV_INST('FDOM_1','BP1');
SP_FAULT_DOMAIN_MV_INST('FDOM_1','BP2');
```

> **注意**
>
> 将SP和BP节点加入DMDPC集群中的步骤必须在SP、BP第一次启动前完成！



### 7.1.5 检查注册是否成功

查询系统表，检查上一步骤的注册是否成功。能查到相关信息表示注册成功。

```
select * from DPC_BP_GROUP;
//查询结果如下：
行号     ID       NAME   DESCRIPTION   RAFT_NUM  RAFT_INFO  INFO1  INFO2  INFO3
------- ------- -------- ----------------   ------------   ------------  -------   --
1        1        BG_1   bp group1         2    0x01000200    0    NULL   NULL

select * from DPC_BP_RAFT;
//查询结果如下：
行号       RAFT_ID     GROUP_ID    DPC_MODE NAME     IS_VALID    INFO1       INFO2                INFO3
---------- ----------- ----------- -------- -------- ----------- ----------- -------------------- ----------
1          0           -1          MP       MP_RAFT  1           NULL        NULL                 NULL
2          1           -1          BP       RAFT_1   1           NULL        NULL                 NULL
3          2           -1          BP       RAFT_2   1           NULL        NULL                 NULL
4          3           -1          SP       RAFT_SP1 1           NULL        NULL                 NULL

select * from DPC_INSTANCE;
//查询结果如下：
行号       RAFT_ID     INST_ID     NAME DPC_MODE XMAL_PORT   INST_PORT   IP_INTERNAL     SYS_MODE SYS_STATUS  STATUS      DESCRIPTION    IP_EXTERNAL   INFO1       INFO2                INFO3
---------- ----------- ----------- ---- -------- ----------- ----------- ------------ -------- ----------- ----------- ----------- ----------- -------------------- ----------  ----------
1          0           4096        MP   MP       6003        5239        223.254.30.136 NORMAL   6           1           MP instance    223.254.30.136  NULL        NULL                 NULL
2          1           4097        BP1  BP       6001        5237        192.168.1. 27 NORMAL   6           1           BP instance   223.254.30.136  NULL        NULL                 NULL
3          2           4098        BP2  BP       6002        5238        192.168.1. 27 NORMAL   6           1           BP instance   223.254.30.136  NULL        NULL                 NULL
4          3           4099        SP1  SP       6000        5236        192.168.1. 27 NORMAL   6           2           SP instance   223.254.30.136  NULL        NULL                 NULL

select * from DPC_INSTANCE;
//查询结果如下：
行号       RAFT_ID     INST_ID     NAME DPC_MODE XMAL_PORT   INST_PORT   IP_INTERNAL     SYS_MODE SYS_STATUS  STATUS      DESCRIPTION    IP_EXTERNAL   INFO1       INFO2                INFO3
---------- ----------- ----------- ---- -------- ----------- ----------- ------------ -------- ----------- ----------- ----------- ----------- -------------------- ----------  ----------
1          0           4096        MP   MP       6003        5239        223.254.30.136 NORMAL   6           1           MP instance    223.254.30.136  65536        NULL                 NULL
2          1           4097        BP1  BP       6001        5237        192.168.1. 27 NORMAL   6           1           BP instance   223.254.30.136  65536         NULL                 NULL
3          2           4098        BP2  BP       6002        5238        192.168.1. 27 NORMAL   6           1           BP instance   223.254.30.136  65536         NULL                 NULL
4          3           4099        SP1  SP       6000        5236        192.168.1. 27 NORMAL   6           2           SP instance   223.254.30.136  NULL        NULL                 NULL
```

### 7.1.6 启动 SP 和 BP

启动 SP 和 BP 没有先后之分。

启动 SP。

```
dmserver d:\dpc_data_mac\sp1\DAMENG\dm.ini dpc_mode=SP
```

启动 BP。

```
dmserver d:\dpc_data_mac\bp1\DAMENG\dm.ini dpc_mode=BP
dmserver d:\dpc_data_mac\bp2\DAMENG\dm.ini dpc_mode=BP
```

至此，DMDPC 集群搭建完毕。

MP、SP、BP 全部正常启动，且均处于 OPEN 状态，才是一个正常的 DMDPC 系统！

将 SP 和 BP 节点加入 DMDPC 集群中的步骤必须在 SP、BP 第一次启动前完成！

> **注意**
>
> 当BP处于下述情况时，表示无可用BP，系统异常，报错“无效的系统状态”。无可用BP的情况为：  
> 1.BP处于非OPEN状态；  
> 2.BP尚未成功启动；  
> 3.BP宕机。



### 7.1.7 连接 SP

DMDPC 搭建完成后，用户只需要连接对外提供服务的 SP，即可获得完整的数据库服务。

验证环境搭建是否成功，可以在 SP 上执行查询 V$instance，看是否所有 RAFT 组中实例都能够查到。

只有出于监控目的进行 V$视图的查询，或出于维护需要时，才允许直连 MP 和 BP，否则搭建完成后，不允许再连接 MP 和 BP。切勿在 MP 和 BP 上执行大量 SQL 语句，否则可能会导致系统崩溃！

### 7.1.8 退出 DMDPC

使用 exit 命令退出 DMDPC 需要按照正确的顺序有序进行。

第一步 退出 SP；

第二步 退出 BP；

第三步 退出 MP。

> **注意**
>
>
> 如果没有按正常顺序退出，可能会导致剩下的机器直接宕机。此时，只能对其他的机器采用强杀。这种服务器异常退出的情况，当再次重启的时候，服务器需要做大量的REDO日志，因此重启的时间会稍长一些，其它没有影响。



## 7.2 命令行工具部署 DMDPC（BP 多副本架构）

DMDPC 集群规划部署 1 个 SP，2 个 BP 和 1 个 MP。MP 采用单机模式，BP 采用多副本模式，每个 BP 配置 2 个副本。

IP 和端口分配如下表所示，仅用作示例，需按实际 IP 进行设置。

表7.2 集群规划示意



以下章节就以在同一台 Windows 主机为例介绍如何部署。

### 7.2.1 初始化数据库实例

初始化 8 个实例，分别为 SP、MP 和 6 个 BP 角色。

```
dminit path=d:\dpc_data_mac\sp1 instance_name=SP1 port_num=5236 ap_port_num=6000 dpc_mode=SP 
dminit path=d:\dpc_data_mac\mp  instance_name=MP  port_num=5237 ap_port_num=6001 dpc_mode=MP

//初始化RAFT_1组中的BP实例
dminit path=d:\dpc_data_mac\bp11 instance_name=BP11 port_num=5238 ap_port_num=6002 dpc_mode=BP 
dminit path=d:\dpc_data_mac\bp12 instance_name=BP12 port_num=5239 ap_port_num=6003 dpc_mode=BP
dminit path=d:\dpc_data_mac\bp13 instance_name=BP13 port_num=5240 ap_port_num=6004 dpc_mode=BP

//初始化RAFT_2组中的BP实例
dminit path=d:\dpc_data_mac\bp21 instance_name=BP21 port_num=5241 ap_port_num=6005 dpc_mode=BP 
dminit path=d:\dpc_data_mac\bp22 instance_name=BP22 port_num=5242 ap_port_num=6006 dpc_mode=BP
dminit path=d:\dpc_data_mac\bp23 instance_name=BP23 port_num=5243 ap_port_num=6007 dpc_mode=BP
```

### 7.2.2 为 SP、BP 和 MP 配置 MP.INI 文件

为 SP、BP 和 MP 实例配置 MP.INI 文件。

MP.INI 文件内容如下：

```
mp_host = 223.254.30.136
mp_port = 9000   #与MP、BP和SP上的ap_port_num不冲突的端口号
```

将 MP.INI 文件内容分别写入 SP（SP1）、两个 RAFT 组内的所有 BP（BP11、BP12、BP13、BP21、BP22、BP23）和 MP 中。打开 cmd 命令行工具执行：

```
//向SP写入MP.INI
echo mp_host = 223.254.30.136 > d:\dpc_data_mac\sp1\DAMENG\mp.ini
echo mp_port = 9000      #与MP、BP和SP上的ap_port_num不冲突的端口号 >> d:\dpc_data_mac\sp1\DAMENG\mp.ini
//向两个RAFT组内的所有BP写入MP.INI，不同BP需要切换到不同的路径下，这里以BP11的路径为例（或者直接拷贝SP上的MP.INI到所有BP的库目录下）
echo mp_host = 223.254.30.136 > d:\dpc_data_mac\bp11\DAMENG\mp.ini
echo mp_port = 9000      #与MP、BP和SP上的ap_port_num不冲突的端口号 >> d:\dpc_data_mac\bp11\DAMENG\mp.ini
//为MP配置MP.INI文件
echo mp_host = 223.254.30.136 >d:\dpc_data_mac\mp\DAMENG\mp.ini
echo mp_port = 9000     #与MP、BP和SP上的ap_port_num不冲突的端口号>> d:\dpc_data_mac\mp\DAMENG\mp.ini
```

### 7.2.3 启动 MP

启动 MP。

```
dmserver d:\dpc_data_mac\mp\DAMENG\dm.ini dpc_mode=MP
```

DMDPC 运行过程中，MP 需要始终处于开启状态。

### 7.2.4 将 MP、SP 和 BP 加入集群

增加 1 个 MP、1 个 SP 和 6 个 BP 节点。只有在注册当前登录 MP 节点后，才可以注册其余节点。后续增加 MP、SP 节点和 BP 节点无先后之分。

```
//搭建DMDPC过程中加入MP、SP和BP，必须登录MP进行操作
DIsql SYSDBA/*****@223.254.30.136:5237

//增加MP节点
//注册当前MP实例，MP的RAFT组名可以指定为NULL或者'MP_RAFT'
SP_CREATE_DPC_INSTANCE(NULL,'MP','MP',6001,5237, '223.254.30.136', '223.254.30.136','NORMAL',1,'MP instance');

//增加BP节点
//注册RAFT组，名为RAFT_1
SP_CREATE_DPC_RAFT('BP', 'RAFT_1');
//在RAFT_1组内注册BP实例BP11、BP12、BP13
//所有实例均以STANDBY模式、INVALID状态注册进MP，实例启动后，在RAFT组内选举出有效主库后，会自动更新MP中的模式、状态信息。
SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP11', 'BP', 6002, 5238, '223.254.30.136', '223.254.30.136','STANDBY', 0, 'BP instance');
SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP12', 'BP', 6003, 5239, '223.254.30.136', '223.254.30.136','STANDBY', 0, 'BP instance');
SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP13', 'BP', 6004, 5240, '223.254.30.136', '223.254.30.136','STANDBY', 0, 'BP instance');

//注册RAFT组，名为RAFT_2
SP_CREATE_DPC_RAFT('BP', 'RAFT_2');
//在RAFT_2内注册BP实例BP21、BP22、BP23
SP_CREATE_DPC_INSTANCE('RAFT_2', 'BP21', 'BP', 6005, 5241, '223.254.30.136', '223.254.30.136','STANDBY', 0, 'BP instance');
SP_CREATE_DPC_INSTANCE('RAFT_2', 'BP22', 'BP', 6006, 5242, '223.254.30.136', '223.254.30.136','STANDBY', 0, 'BP instance');
SP_CREATE_DPC_INSTANCE('RAFT_2', 'BP23', 'BP', 6007, 5243, '223.254.30.136', '223.254.30.136','STANDBY', 0, 'BP instance');

//注册一个BP组，名为BG_1
SP_CREATE_DPC_BP_GROUP('BG_1', 'bp group1');
//在BP组内添加RAFT组
SP_BP_GROUP_ADD_RAFT('BG_1', 'RAFT_1');
SP_BP_GROUP_ADD_RAFT('BG_1', 'RAFT_2');

//增加SP节点
//增加SP，也要注册RAFT组，参数1为空串
SP_CREATE_DPC_RAFT('SP', 'RAFT_SP1');
//在RAFT_SP1内注册SP实例SP1
SP_CREATE_DPC_INSTANCE('RAFT_SP1', 'SP1', 'SP', 6000, 5236, '223.254.30.136', '223.254.30.136','NORMAL', 2, 'SP instance');

//注册三个容错域：FDOM_1 FDOM_2 FDOM_3
SP_CREATE_FAULT_DOMAIN ('FDOM_1', 'shanghai_1');
SP_CREATE_FAULT_DOMAIN ('FDOM_2', 'shanghai_2');
SP_CREATE_FAULT_DOMAIN ('FDOM_3', 'shanghai_3');

//往容错域中添加实例
SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'MP');
SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'BP11');
SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'BP21');
SP_FAULT_DOMAIN_MV_INST('FDOM_2', 'BP12');
SP_FAULT_DOMAIN_MV_INST('FDOM_2', 'BP22');
SP_FAULT_DOMAIN_MV_INST('FDOM_3', 'BP13');
SP_FAULT_DOMAIN_MV_INST('FDOM_3', 'BP23');
```

> **注意**
>
> 将SP和BP节点加入DMDPC集群中的步骤必须在SP、BP第一次启动前完成！



### 7.2.5 检查注册是否成功

查询系统表，检查上一步骤的注册是否成功。能查到相关信息表示注册成功。

```
select * from DPC_BP_GROUP;
//查询结果如下：
行号     ID       NAME   DESCRIPTION   RAFT_NUM  RAFT_INFO  INFO1  INFO2  INFO3
------- ------- -------- ----------------   ------------   ------------  -------   --
1        1        BG_1   bp group1         2    0x01000200    0    NULL   NULL

select * from DPC_BP_RAFT;
//查询结果如下：
行号       RAFT_ID     GROUP_ID    DPC_MODE NAME     IS_VALID    INFO1       INFO2                INFO3
---------- ----------- ----------- -------- -------- ----------- ----------- -------------------- ----------
1          0           -1          MP       MP_RAFT  1           NULL        NULL                 NULL
2          1           -1          BP       RAFT_1   1           NULL        NULL                 NULL
3          2           -1          BP       RAFT_2   1           NULL        NULL                 NULL
4          3           -1          SP       RAFT_SP1 1           NULL        NULL                 NULL

select * from DPC_INSTANCE;
//查询结果如下：
行号       RAFT_ID     INST_ID     NAME DPC_MODE XMAL_PORT   INST_PORT   IP_INTERNAL      SYS_MODE SYS_STATUS  STATUS      DESCRIPTION  IP_EXTERNAL  INFO1       INFO2                INFO3
---------- ----------- ----------- ---- -------- ----------- ----------- ------------ -------- ----------- ----------- ----------- ----------- -------------------- --------------------
1          0           4096        MP   MP       6001        5237        223.254.30.136 NORMAL   6           1           MP instance 223.254.30.136  65536        NULL                 NULL
2          1           4097        BP11 BP       6002        5238        223.254.30.136 STANDBY  6           0           BP instance 223.254.30.136  65536       NULL                 NULL
3          1           4098        BP12 BP       6003        5239        223.254.30.136 STANDBY  6           0           BP instance 223.254.30.136  131072        NULL                 NULL
4          1           4099        BP13 BP       6004        5240        223.254.30.136 STANDBY  6           0           BP instance 223.254.30.136  196608       NULL                 NULL
5          2           4100        BP21 BP       6005        5241        223.254.30.136 STANDBY  6           0           BP instance 223.254.30.136  65536        NULL                 NULL
6          2           4101        BP22 BP       6006        5242        223.254.30.136 STANDBY  6           0           BP instance 223.254.30.136  131072        NULL                 NULL
7          2           4102        BP23 BP       6007        5243        223.254.30.136 STANDBY  6           0           BP instance 223.254.30.136  196608        NULL                 NULL
8          3           4103        SP1  SP       6000        5236        223.254.30.136 NORMAL   6           2           SP instance 223.254.30.136  NULL        NULL                 NULL
```

### 7.2.6 配置 BP11

  1. 配置 BP11 的 dm.ini



```
ARCH_INI = 1   #打开归档配置
```

  2. 配置 BP11 的本地归档 dmarch.ini。此处先只配本地归档，避免提前发起 RAFT 选举



```
[ARCHIVE_LOCAL1]
ARCH_TYPE = LOCAL #本地归档类型
ARCH_DEST = d:\dpc_data_mac\bp11\DAMENG\arch  #本地归档文件路径
ARCH_FILE_SIZE = 128   #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT = 0   #本地归档文件总大小,0表示无限制
```

  3. 正常启动 BP11 到 Open 状态



```
dmserver d:\dpc_data_mac\bp11\DAMENG\dm.ini dpc_mode=bp
```

### 7.2.7 准备 BP RAFT 组内数据

RAFT_1 和 RAFT_2 组内的 BP 数据准备方式完全相同，只是路径不同，这里对组 RAFT_1 的数据准备步骤进行说明，RAFT_2 组参考这个步骤执行即可。注意，RAFT_2 组和 RAFT_1 组要各自按照此小节介绍的方式准备数据，RAFT_2 组不能直接使用 RAFT_1 组的备份集执行还原恢复。

#### 7.2.7.1 退出 BP11 并进行脱机备份

首先，退出 BP11。

其次，对 BP11 进行脱机备份。

```
dmrman CTLSTMT="BACKUP DATABASE 'd:\dpc_data_mac\bp11\DAMENG\dm.ini' FULL TO BACKUP_01 BACKUPSET 'd:\dpc_data_mac\bp11\BACKUP_01'" USE_AP=2
```

  1. 将 BP11 的备份文件脱机还原到 BP12



```
//还原数据库：
dmrman CTLSTMT="RESTORE DATABASE 'd:\dpc_data_mac\bp12\DAMENG\dm.ini' FROM BACKUPSET 'd:\dpc_data_mac\bp11\BACKUP_01'" USE_AP=2
//因为脱机备份没有产生任何REDO日志，所以此处省略恢复数据库的操作步骤
//更新数据库：
dmrman CTLSTMT="RECOVER DATABASE 'd:\dpc_data_mac\bp12\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
```

  2. BP11 的备份文件脱机还原到 BP13



```
//还原数据库：
dmrman CTLSTMT="RESTORE DATABASE 'd:\dpc_data_mac\bp13\DAMENG\dm.ini' FROM BACKUPSET 'd:\dpc_data_mac\bp11\BACKUP_01'" USE_AP=2
//因为脱机备份没有产生任何REDO日志，所以此处省略恢复数据库的操作步骤
//更新数据库：
dmrman CTLSTMT="RECOVER DATABASE 'd:\dpc_data_mac\bp13\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
```

#### 7.2.7.2 配置 BP dm.ini

RAFT_1 组各实例（BP11、BP12、BP13）和 RAFT_2 组各实例（BP21、BP22、BP23）中 dm.ini 文件的配置方法完全相同。

下面以 BP11 的 dm.ini 为例进行介绍。dm.ini 文件配置如下：

```
ARCH_INI = 1   #打开归档配置
ALTER_MODE_STATUS = 0   #不允许用户直接通过SQL语句修改服务器模式
```

#### 7.2.7.3 配置 BP RAFT 归档文件

修改 DMARCH.INI，配置 RAFT 归档与本地归档。RAFT_1 组配置如下，RAFT_2 组配置可参考此步骤进行配置。

除了本地归档外，其他归档配置项中的 ARCH_DEST 表示实例是 Primary 模式时，需要同步归档数据的目标实例名。

  1. 配置 BP11 归档文件 DMARCH.INI



当前实例为 BP11，需要向 BP12、BP13 同步数据，因此 RAFT 归档中的 ARCH_DEST 分别配置为 BP12、BP13。

DMARCH.INI 文件内容如下：

```
XMAL_HB_INTERVAL = 5   #节点通信检测间隔
RAFT_HB_INTERVAL = 150   #选举心跳间隔
RAFT_VOTE_INTERVAL = 1500  #选举超时时间,三个库设置不同以尽快选出主库
RAFT_SELF_ID = 1   #多副本自身节点ID

[ARCHIVE_RAFT1]
ARCH_TYPE = RAFT   #RAFT归档
ARCH_DEST = BP12  #归档目标实例名
ARCH_DEST_ID = 2   #归档目标多副本节点ID

[ARCHIVE_RAFT2]
ARCH_TYPE = RAFT   #RAFT归档
ARCH_DEST = BP13   #归档目标 实例名
ARCH_DEST_ID = 3   #归档目标多副本节点ID

[ARCHIVE_LOCAL1]
ARCH_TYPE = LOCAL  #本地归档类型
ARCH_DEST = d:\dpc_data_mac\bp11\DAMENG\arch   #本地归档文件路径
ARCH_FILE_SIZE = 128   #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT = 0   #本地归档文件总大小,0表示无限制
```

2.配置 BP12 归档配置文件 DMARCH.INI

当前实例为 BP12，系统配置完成后，可能在各种故障处理中，BP12 可能选为 Leader。而原来的 Leader 会切换为 Follower。此时 BP12 需要向 BP11 和 BP13 同步数据，因此 RAFT 归档的 ARCH_DEST 分别配置为 BP11 和 BP13。

```
XMAL_HB_INTERVAL = 5  #节点通信检测间隔
RAFT_HB_INTERVAL = 150   #选举心跳间隔
RAFT_VOTE_INTERVAL = 2000   #选举超时时间
RAFT_SELF_ID = 2   #多副本自身节点ID

[ARCHIVE_RAFT1]
ARCH_TYPE = RAFT  #RAFT归档
ARCH_DEST = BP11  #归档目标实例名
ARCH_DEST_ID = 1  #归档目标多副本节点ID

[ARCHIVE_RAFT2]
ARCH_TYPE = RAFT   #RAFT归档
ARCH_DEST = BP13   #归档目标实例名
ARCH_DEST_ID = 3   #归档目标多副本节点ID

[ARCHIVE_LOCAL1]
ARCH_TYPE = LOCAL   #本地归档类型
ARCH_DEST = d:\dpc_data_mac\bp12\DAMENG\arch   #本地归档文件路径
ARCH_FILE_SIZE = 128   #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT = 0   #本地归档文件总大小,0表示无限制
```

3.配置 BP13 归档配置文件 DMARCH.INI

当前实例为 BP13，系统配置完成后，可能在各种故障处理中，BP13 可能选为组内 Leader。而原来的 Leader 会切换为 Follower。此时 BP13 需要向 BP11 和 BP12 同步数据，因此 RAFT 归档的 ARCH_DEST 分别配置为 BP11 和 BP12。

```
XMAL_HB_INTERVAL = 5   #节点通信检测间隔
RAFT_HB_INTERVAL = 150   #选举心跳间隔
RAFT_VOTE_INTERVAL = 2500   #选举超时时间
RAFT_SELF_ID = 3   #多副本自身节点ID

[ARCHIVE_RAFT1]
ARCH_TYPE = RAFT   #RAFT归档
ARCH_DEST = BP11   #归档目标实例名
ARCH_DEST_ID = 1   #多副本归档目标节点ID

[ARCHIVE_RAFT2]
ARCH_TYPE = RAFT   #RAFT归档
ARCH_DEST = BP12   #归档目标实例名
ARCH_DEST_ID = 2   #多副本归档目标节点ID

[ARCHIVE_LOCAL1]
ARCH_TYPE = LOCAL   #本地归档类型
ARCH_DEST = d:\dpc_data_mac\bp13\DAMENG\arch  #本地归档文件路径
ARCH_FILE_SIZE = 128  #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT = 0   #本地归档文件总大小,0表示无限制
```

全部修改完成后，RAFT_1 组中的三个 BP 即配置完成。

RAFT_2 组中的三个 BP 配置方式和 RAFT_1 完全相同，只是需要对路径、实例名等进行区分，具体可参考 7.2.5~7.2.7 小节进行配置。

### 7.2.8 启动 SP 和 RAFT 组内所有 BP

启动 SP 和 BP 没有先后之分。

启动 SP。

```
dmserver d:\dpc_data_mac\sp1\DAMENG\dm.ini dpc_mode=SP
```

启动 BP。此处以 RAFT_1 组的启动方式为例，三个 BP 的启动顺序没有要求，RAFT_2 组配置完成后，也按照相同方式启动即可。

```
dmserver d:\dpc_data_mac\bp11\DAMENG\dm.ini dpc_mode=BP MOUNT
dmserver d:\dpc_data_mac\bp12\DAMENG\dm.ini dpc_mode=BP MOUNT
dmserver d:\dpc_data_mac\bp13\DAMENG\dm.ini dpc_mode=BP MOUNT
```

启动完成后，RAFT 组内的 BP 会自动选出主库，待主库选举完成后，整个 DMDPC 系统即可正常运行。

至此，DMDPC 集群搭建完毕。

MP、SP、BP 全部正常启动，且均处于 OPEN 状态，才是一个正常的 DMDPC 系统！

> **注意**
>
> 当BP处于下述情况时，表示无可用BP，系统异常，报错“无效的系统状态”。无可用BP的情况为：  
> 1.BP处于非OPEN状态；  
> 2.BP尚未成功启动；  
> 3.BP全部宕机；  
> 4．BP如果是多副本环境，确认是否所有BP组都已经选举出主库，并且主库也已切换为Primary模式和Open状态。



### 7.2.9 连接 SP

DMDPC 搭建完成后，用户只需要连接对外提供服务的 SP，即可获得完整的数据库服务。

只有出于监控目的进行 V$视图的查询，或出于维护需要时，才允许直连 MP 和 BP，否则不允许再连接 MP 和 BP。切勿在 MP 和 BP 上执行大量 SQL 语句，否则可能会导致系统崩溃。

### 7.2.10 退出 DMDPC

退出副本架构的 DMDPC 需要按正确顺序使用指定命令退出各节点。

第一步 使用 exit 命令退出所有 SP；

第二步 使用 exit all 命令依次退出每个 RAFT 组。在 BP 的每个 RAFT 组内中任一有效节点输入 exit all 命令或者对其发出信号 SIGUSR1（如 kill -10）即可对整个 RAFT 组执行协同退出，由有效 Leader 协同其余有效节点正常退出，无效节点不会参与协同退出。如果执行 exit all 命令的是无效节点，则会转为只在此节点进行退出（exit），多数情况下，无效节点的退出会由于无法推进 C_LSN 强制 HALT；

第三步 退出未参与 exit all 命令的 BP。使用 exit 命令依次退出所有剩余的无效 BP；

第四步 使用 exit 命令退出 MP。

> **注意**
>
>
> 如果没有按正常顺序退出，可能会导致剩下的机器直接宕机。此时，只能对其他的机器采用强杀。这种服务器异常退出的情况，当再次重启的时候，服务器需要做大量的REDO日志，因此重启的时间会稍长一些，其它没有影响。



## 7.3 命令行工具部署 DMDPC（MP 多副本架构）

DMDPC 集群规划部署 1 个 SP，1 个 BP 和 1 个 MP。BP 采用单机模式，MP 采用多副本模式，为 MP 配置 2 个副本。

IP 和端口分配如下表所示，仅用作示例，需按实际 IP 进行设置。

表7.3 集群规划示意



以下章节就以在同一台 Windows 主机为例介绍如何部署。

### 7.3.1 初始化数据库实例

初始化 5 个实例，分别为 SP、BP 和 3 个 MP 角色。

```
dminit path=e:\mpraft_data\sp1 instance_name=SP1 port_num=5236 ap_port_num=6000 dpc_mode=SP AUTO_OVERWRITE=1
dminit path=e:\mpraft_data\bp1 instance_name=BP1 port_num=5336 ap_port_num=6100 dpc_mode=BP AUTO_OVERWRITE=1
dminit path=e:\mpraft_data\mp1 instance_name=MP1 port_num=5436 ap_port_num=6200 dpc_mode=MP AUTO_OVERWRITE=1
dminit path=e:\mpraft_data\mp2 instance_name=MP2 port_num=5437 ap_port_num=6201 dpc_mode=MP AUTO_OVERWRITE=1
dminit path=e:\mpraft_data\mp3 instance_name=MP3 port_num=5438 ap_port_num=6202 dpc_mode=MP AUTO_OVERWRITE=1
```

### 7.3.2 为 SP、BP 和 MP 配置 MP.INI 文件

为 SP、BP 和 MP 实例配置 MP.INI 文件。

MP.INI 文件内容如下：

```
# mp_port端口号必须与MP、BP和SP上的ap_port_num端口号不冲突
[MP1]
mp_host = 192.168.1.118
mp_port = 10622

[MP2]
mp_host = 192.168.1.118
mp_port = 10623

[MP3]
mp_host = 192.168.1.118
mp_port = 10624
```

将 MP.INI 文件内容分别写入 SP（SP1）、BP（BP1）和 MP（MP1、MP2、MP3）的库目录 DAMENG 下。例如：SP 的 MP.INI 位于 e:\mpraft_data\sp1\DAMENG\mp.ini。

### 7.3.3 配置并启动 MP1

#### 7.3.3.1 配置 MP1 的 dmarch.ini

配置 MP1 的本地归档 dmarch.ini。

```
[ARCHIVE_LOCAL1]
ARCH_TYPE = LOCAL #本地归档类型
ARCH_DEST = e:\mpraft_data\mp1\DAMENG\arch  #本地归档文件路径
ARCH_FILE_SIZE = 128  #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT = 0  #本地归档文件总大小,0表示无限制
```

#### 7.3.3.2 配置 MP1 的 dm.ini

打开 MP1 归档配置。

```
ARCH_INI = 1
```

#### 7.3.3.3 启动 MP1

```
dmserver e:\mpraft_data\mp1\DAMENG\dm.ini dpc_mode=MP
```

### 7.3.4 将 MP、SP 和 BP 加入集群

增加 3 个 MP、1 个 SP 和 1 个 BP 节点。只有在注册当前登录 MP 节点后，才可以注册其余节点。后续增加 MP、SP 节点和 BP 节点无先后之分。

```
//搭建DMDPC过程中加入MP、SP和BP，必须登录MP进行操作
DIsql SYSDBA/*****@192.168.1.118:5436

//增加MP节点
//注册当前MP实例，MP的RAFT组名可以指定为'MP_RAFT'或缺省NULL
SP_CREATE_DPC_INSTANCE(NULL, 'MP1', 'MP', 6200, 5436, '192.168.1.118','', 'STANDBY', 0, 'MP instance');
SP_CREATE_DPC_INSTANCE(NULL, 'MP2', 'MP', 6201, 5437, '192.168.1.118', '','STANDBY', 0, 'MP instance');
SP_CREATE_DPC_INSTANCE(NULL, 'MP3', 'MP', 6202, 5438, '192.168.1.118', '', 'STANDBY', 0, 'MP instance');

//增加BP节点
//注册RAFT组，名为BPRAFT_1
SP_CREATE_DPC_RAFT('BP', 'BPRAFT_1');
//在BPRAFT_1组内注册BP实例BP1
SP_CREATE_DPC_INSTANCE('BPRAFT_1', 'BP1', 'BP', 6100, 5336, '192.168.1.118', '','NORMAL', 1, 'BP instance');
//注册一个BP组，名为BG_1
SP_CREATE_DPC_BP_GROUP('BG_1', 'bp group1');
//在BP组内添加RAFT组
SP_BP_GROUP_ADD_RAFT('BG_1', 'BPRAFT_1');

//增加SP节点
//增加SP，也要注册RAFT组
SP_CREATE_DPC_RAFT('SP', 'SPRAFT_1');
//在SPRAFT_1内注册SP实例SP1
SP_CREATE_DPC_INSTANCE('SPRAFT_1', 'SP1', 'SP', 6000, 5236, '192.168.1.118', '','NORMAL', 1, 'SP instance');

//注册三个容错域：FDOM_1 FDOM_2 FDOM_3
SP_CREATE_FAULT_DOMAIN ('FDOM_1', 'shanghai_1');
SP_CREATE_FAULT_DOMAIN ('FDOM_2', 'shanghai_2');
SP_CREATE_FAULT_DOMAIN ('FDOM_3', 'shanghai_3');

//往容错域中添加实例
SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'MP1');
SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'BP1');
SP_FAULT_DOMAIN_MV_INST('FDOM_2', 'MP2');
SP_FAULT_DOMAIN_MV_INST('FDOM_3', 'MP3');
```

### 7.3.5 检查注册是否成功

查询系统表，检查上一步骤的注册是否成功。能查到相关信息表示注册成功。

```
select * from DPC_BP_GROUP;
//查询结果如下：
行号     ID       NAME   DESCRIPTION   RAFT_NUM  RAFT_INFO  INFO1  INFO2  INFO3
------- ------- -------- ----------------   ------------   ------------  -------   --
1        1       BG_1   bp group1         1      0x0100    0          NULL   NULL

select * from DPC_BP_RAFT;
//查询结果如下：
行号       RAFT_ID     GROUP_ID    DPC_MODE NAME     IS_VALID    INFO1       INFO2                INFO3
---------- ----------- ----------- -------- -------- ----------- ----------- -------------------- ----------
1          0           -1          MP       MP_RAFT  1           NULL        NULL                 NULL
2          1           -1          BP       BPRAFT_1 1           NULL        NULL                 NULL
3          2           -1          SP       SPRAFT_1 1           NULL        NULL                 NULL

select * from DPC_INSTANCE;
//查询结果如下：
行号       RAFT_ID     INST_ID     NAME DPC_MODE XMAL_PORT   INST_PORT   IP_INTERNAL     SYS_MODE  SYS_STATUS  STATUS      DESCRIPTION IP_EXTERNAL INFO1       INFO2          INFO3
---------- ----------- ----------- ---- -------- ----------- ----------- ------------ -------- ---------------------- ----------- ----------- -------------------- ----------   
1          0           4096        MP1  MP       6200        5436        192.168.1.118 STANDBY  6   0           MP instance 192.168.1.118  65536        NULL                 NULL   
2          0           4097        MP2  MP       6201        5437        192.168.1.118 STANDBY  6   0           MP instance 192.168.1.118  131072        NULL                 NULL   
3          0           4098        MP3  MP       6202        5438        192.168.1.118 STANDBY  6   0           MP instance 192.168.1.118  196608        NULL                 NULL   
4          1           4099        BP1  BP       6100        5336        192.168.1.118 NORMAL   6   1           BP instance 192.168.1.118  65536       NULL                 NULL  
5          2           4100        SP1  SP       6000        5236        192.168.1.118 NORMAL   6   1           SP instance 192.168.1.118  NULL        NULL                 NULL
```

### 7.3.6 准备 MP RAFT 组内数据

#### 7.3.6.1 退出 MP1 并进行脱机备份

首先，退出 MP1。

其次，对 MP1 进行脱机备份。

```
dmrman CTLSTMT="BACKUP DATABASE 'e:\mpraft_data\mp1\DAMENG\dm.ini' FULL TO BACKUP_01 BACKUPSET 'e:\mpraft_data\mp1\BACKUP_01'" USE_AP=2
```

#### 7.3.6.2 将 MP1 的备份文件脱机还原到 MP2 及 MP3

将 MP1 的备份文件 BACKUP_01 脱机还原到 MP2 及 MP3。

此处不需要重启 MP2，MP3。如果用户有特殊需要，必须要启动 MP2，MP3，则一定要以 MOUNT 方式启动，否则可能导致 MP2，MP3 生成日志，引发三个节点日志内容不匹配。

```
//还原到MP2
dmrman CTLSTMT="RESTORE DATABASE 'e:\mpraft_data\mp2\DAMENG\dm.ini' FROM BACKUPSET 'e:\mpraft_data\mp1\BACKUP_01'" USE_AP=2
dmrman CTLSTMT="RECOVER DATABASE 'e:\mpraft_data\mp2\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
//还原到MP3
dmrman CTLSTMT="RESTORE DATABASE 'e:\mpraft_data\mp3\DAMENG\dm.ini' FROM BACKUPSET 'e:\mpraft_data\mp1\BACKUP_01'" USE_AP=2
dmrman CTLSTMT="RECOVER DATABASE 'e:\mpraft_data\mp3\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
```

#### 7.3.6.3 配置 MP dm.ini

MP 的 RAFT 组各实例（MP1、MP2、MP3）中 dm.ini 文件的配置方法完全相同。

下面以 MP1 的 dm.ini 为例进行介绍。dm.ini 文件配置如下：

```
ARCH_INI = 1  #打开归档配置
ALTER_MODE_STATUS = 0  #不允许用户直接通过SQL语句修改服务器模式
```

#### 7.3.6.4 配置 MP RAFT 归档文件

修改 DMARCH.INI，配置 RAFT 归档与本地归档。

除了本地归档外，其他归档配置项中的 ARCH_DEST 表示实例是 Primary 模式时，需要同步归档数据的目标实例名。

  1. 配置 MP1 归档文件 DMARCH.INI



当前实例为 MP1，需要向 MP2、MP3 同步数据，因此 RAFT 归档中的 ARCH_DEST 分别配置为 MP2、MP3。

DMARCH.INI 文件内容如下：

```
XMAL_HB_INTERVAL         = 5                #节点通信检测间隔
RAFT_HB_INTERVAL         = 150              #选举心跳间隔
RAFT_VOTE_INTERVAL       = 1500            #选举超时时间,三个库设置不同以尽快选出主库
RAFT_SELF_ID              = 1               #多副本自身节点ID

[ARCHIVE_RAFT1] 
ARCH_TYPE                 = RAFT                #RAFT归档
ARCH_DEST                 = MP2                #归档目标实例名  
ARCH_DEST_ID              = 2                   #归档目标多副本节点ID

[ARCHIVE_RAFT2] 
ARCH_TYPE                 = RAFT                #RAFT归档
ARCH_DEST                 = MP3             #归档目标实例名
ARCH_DEST_ID              = 3                   #归档目标多副本节点ID

[ARCHIVE_LOCAL1]
ARCH_TYPE                 = LOCAL            #本地归档类型
ARCH_DEST                 = e:\mpraft_data\mp1\DAMENG\arch    #本地归档文件路径
ARCH_FILE_SIZE          = 128                #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT         = 0                #本地归档文件总大小,0表示无限制
```

  2. 配置 MP2 归档文件 DMARCH.INI



```
XMAL_HB_INTERVAL         = 5                #节点通信检测间隔
RAFT_HB_INTERVAL         = 150              #选举心跳间隔
RAFT_VOTE_INTERVAL       = 2000                #选举超时时间
RAFT_SELF_ID              = 2              #多副本自身节点ID

[ARCHIVE_RAFT1] 
ARCH_TYPE                 = RAFT                #RAFT归档
ARCH_DEST                 = MP1              #归档目标实例名
ARCH_DEST_ID              = 1                   #归档目标多副本节点ID

[ARCHIVE_RAFT2] 
ARCH_TYPE                 = RAFT                #RAFT归档
ARCH_DEST                 = MP3             #归档目标实例名
ARCH_DEST_ID              = 3                   #归档目标多副本节点ID

[ARCHIVE_LOCAL1]
ARCH_TYPE                 = LOCAL            #本地归档类型
ARCH_DEST                 = e:\mpraft_data\mp2\DAMENG\arch    #本地归档文件路径
ARCH_FILE_SIZE           = 128                #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT         = 0                #本地归档文件总大小,0表示无限制
```

  3. 配置 MP3 归档文件 DMARCH.INI



```
XMAL_HB_INTERVAL         = 5                #节点通信检测间隔
RAFT_HB_INTERVAL         = 150              #选举心跳间隔
RAFT_VOTE_INTERVAL      = 2500                #选举超时时间
RAFT_SELF_ID              = 3              #多副本自身节点ID

[ARCHIVE_RAFT1] 
ARCH_TYPE                 = RAFT                #RAFT归档
ARCH_DEST                 = MP1             #归档目标实例名
ARCH_DEST_ID            	 = 1                   #归档目标多副本节点ID

[ARCHIVE_RAFT2] 
ARCH_TYPE                 = RAFT                #RAFT归档
ARCH_DEST                 = MP2             #归档目标实例名
ARCH_DEST_ID              = 2                  #归档目标多副本节点ID

[ARCHIVE_LOCAL1]
ARCH_TYPE                 = LOCAL            #本地归档类型
ARCH_DEST                 = e:\mpraft_data\mp3\DAMENG\arch    #本地归档文件路径
ARCH_FILE_SIZE           = 128                #本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT         = 0                #本地归档文件总大小,0表示无限制
```

### 7.3.7 启动所有 MP

启动 MP。

```
dmserver e:\mpraft_data\mp1\DAMENG\dm.ini dpc_mode=MP MOUNT
dmserver e:\mpraft_data\mp2\DAMENG\dm.ini dpc_mode=MP MOUNT
dmserver e:\mpraft_data\mp3\DAMENG\dm.ini dpc_mode=MP MOUNT
```

启动完成后，RAFT 组内的 MP 会自动选出主库，待主库选举完成后，整个 MP 系统即可正常运行。

DMDPC 运行过程中，MP 需要始终处于开启状态。

> **注意**
>
> 搭建时需要严格按照搭建步骤执行并确保配置正确，否则可能会导致集群状态不正确无法选主等问题。



### 7.3.8 启动 SP 和 BP

启动 SP 和 BP 没有先后之分。

启动 SP。

```
dmserver e:\mpraft_data\sp1\DAMENG\dm.ini dpc_mode=SP
```

启动 BP。

```
dmserver e:\mpraft_data\bp1\DAMENG\dm.ini dpc_mode=BP
```

至此，DMDPC 集群搭建完毕。

MP、SP、BP 全部正常启动，且均处于 OPEN 状态，才是一个正常的 DMDPC 系统！

### 7.3.9 连接 SP

DMDPC 搭建完成后，用户只需要连接对外提供服务的 SP，即可获得完整的数据库服务。

验证环境搭建是否成功，可以在 SP 上执行查询 V$instance，看是否所有 RAFT 组中实例都能够查到。

只有出于监控目的进行 V$视图的查询，或出于维护需要时，才允许直连 MP 和 BP，否则搭建完成后，不允许再连接 MP 和 BP。切勿在 MP 和 BP 上执行大量 SQL 语句，否则可能会导致系统崩溃！

### 7.3.10 退出 DMDPC

退出副本架构的 DMDPC 需要按正确顺序使用指定命令退出各节点。

第一步 使用 exit 命令退出所有 SP；

第二步 使用 exit 命令退出所有 BP；

第三步 使用 exit all 命令依次退出每个 RAFT 组。在 MP 的每个 RAFT 组内中任一有效节点输入 exit all 命令或者对其发出信号 SIGUSR1（如 kill-10）即可对整个 RAFT 组执行协同退出，由有效 Leader 协同其余有效节点正常退出，无效节点不会参与协同退出。如果执行 exit all 命令的是无效节点，则会转为只在此节点进行退出（exit），多数情况下，无效节点的退出会由于无法推进 C_LSN 强制 HALT；

第四步 退出未参与 exit all 命令的 MP。使用 exit 命令依次退出所有剩余的无效 MP。

> **注意**
>
>
> 如果没有按正常顺序退出，可能会导致剩下的机器直接宕机。此时，只能对其他的机器采用强杀。这种服务器异常退出的情况，当再次重启的时候，服务器需要做大量的REDO日志，因此重启的时间会稍长一些，其它没有影响。



## 7.4 图形化部署 DMDPC

DM WEB 版数据库管理工具（DEM）提供 DMDPC 的图形化部署与管理功能。关于 DEM 工具的启动，请查看 DEM 相关文档。DEM 启动后，通过浏览器访问。

DEM 对 DMDPC 的部署与管理包括集群部署和监控。集群部署在“客户端工具”栏点击“部署集群”可以打开，集群监控在“监控及告警”栏的“数据库”监控中添加对集群的监控即可打开，也可以在集群部署完成后通过“打开监控”按钮，自动添加对所部署的集群的监控。

本章主要对 DMDPC 的部署和监控进行介绍。

### 7.4.1 DMDPC 图形化部署

在 DEM 的左侧边栏，选择“集群部署”工具，打开新建集群部署的对话框，如下图：

![图 7.1 新建集群部署界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202211281449587RUG9OZGPCVY58HRDK)

图7.1 新建集群部署界面

给定集群名称，点击“确定”以后，在 DEM 右边面板打开部署面板，进入“选择部署集群类型”界面：

![图 7.2 选择部署集群类型.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221128145010Y1GO663K9LEOKZYHGK)

图7.2 选择部署集群类型

选择“分布式”，进入“环境准备”界面：

![图 7.3 环境准备.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202211281450249W52672NVXK2TADG5Q)

图7.3 环境准备

选择要部署的主机，点击“下一步”，进入“实例规划”界面：

![图 7.4 实例规划.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221128145034VHGVNMF37AH1B2DOHJ)

图7.4 实例规划

根据需要配置好实例的分布并规划好端口后，点击“下一步”，进入“BP 组和 BP 域配置”界面：

![图 7.5 BP 组和 BP 域配置.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221128145048GY6W32T2KUSQK8DF94)

图7.5 BP组和BP域配置

在该页面可以配置 BP 组和 BP 域信息，可根据实际情况进行配置，配置完成后，点击“下一步”，进入“地域、数据中心和容错域配置”界面：

![图 7.6 地域、数据中心和容错域配置.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322151243YI8I0ATVVG95ELW48Z)

图7.6 地域、数据中心和容错域配置

用户可以根据需求对集群节点的地域、数据中心和容错域进行配置（如上图）；若不需要配置，可以直接进行下一步。点击“下一步”，进入“初始化参数配置”界面：

![图 7.7 初始化参数配置.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322151312LWGANBL4DEXDQ6YDQ0)

图7.7 初始化参数配置

在该页面配置初始化参数信息，目前，每个实例的初始化参数保持一致，配置完成后，点击“下一步”，进入“dm.ini 配置”界面：

![图 7.8 dm.ini 配置.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322151402MXLGKHWI3XYO18Y0WD)

图7.8 dm.ini配置

在该页面配置各实例的 dm.ini，默认为初始化生成的默认值，如需修改，则通过自定义参数配置，以“参数名=参数值”且多个参数用逗号分开方式配置，配置完成后，点击“下一步”，进入“dmarch.ini 配置”界面：

![图 7.9 dmarch.ini 配置.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322151504NTVWXZA2KNBMFLH25K)

图7.9 dmarch.ini配置

在该页面配置 BP 和 MP 的 dmarch.ini，默认为初始化生成的默认值，如需修改，则通过自定义参数配置，以“参数名=参数值”且多个参数用逗号分开方式配置，配置完成后，点击“下一步”，进入“上传服务器文件”界面，点击“＋”后，进入选择上传的服务器文件的界面。点选“选择本地文件”并点击“确认”后，可以选择本地服务器文件进行上传：

![图 7.10 上传服务器文件.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322151539KHBIPWR3SQDA0QLLF2)

图7.10 上传服务器文件

上传完服务器文件后，点击“下一步”，进入“详情总览”界面。列出将要部署的集群环境的所有配置信息：

![图 7.11 详情总览.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322151606NPUH0UF8QAPLOLSXDV)

图7.11 详情总览

点击“下一步”，开始执行部署任务。执行过程如下：

![图 7.12 执行部署任务-正在部署.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202403221516380UEK24HLO1083DZZNR)

图7.12 执行部署任务-正在部署

部署任务执行完成后显示如下：

![图 7.13 执行部署任务-部署完成.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322151706JZ8PFF71E4BEMIIG5U)

图7.13 执行部署任务-部署完成

至此，基于 DMDPC 搭建完成。

点击“添加到监控”可以把该集群添加到数据库监控中，并且可以直接打开数据库监控界面。

### 7.4.2 DMDPC 图形化监控

在 DEM 的左侧边栏中找到“资源监控”，单击进入后找到“数据库监控”，点击“＋”即可打开添加集群监控的界面，根据配置信息添加站点后，就能够对集群进行监控。

![图 7.14 监控面板.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202403221517524LMN221NPHE97GRGG8)

图7.14 监控面板

DMDPC 监控把 DMDPC 的每个节点当成数据库实例来监控，其支持的监控项与普通数据库实例相同，详细参见 DEM 相关文档。

## 7.5 命令行工具搭建多副本影子库

影子库的搭建和现有的 RAFT 库搭建基本相同，只有备份还原的步骤有差异，其他配置方法完全相同，除备份还原步骤外，其他步骤都可以参考 [7.2 命令行工具部署 DMDPC（BP 副本架构）](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-cluster-deploy.html#7.2%20%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7%E9%83%A8%E7%BD%B2%20DMDPC%EF%BC%88BP%20%E5%A4%9A%E5%89%AF%E6%9C%AC%E6%9E%B6%E6%9E%84%EF%BC%89)和 [7.3 命令行工具部署 DMDPC（MP 副本架构）](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-cluster-deploy.html#7.3%20%E5%91%BD%E4%BB%A4%E8%A1%8C%E5%B7%A5%E5%85%B7%E9%83%A8%E7%BD%B2%20DMDPC%EF%BC%88MP%20%E5%A4%9A%E5%89%AF%E6%9C%AC%E6%9E%B6%E6%9E%84%EF%BC%89)小节。

### 7.5.1 影子库的备份还原

影子库的备份还原有两种方式，分别说明如下：

  1. 可以对 RAFT 主库做联机或脱机备份，备份时指定 SHADOW 关键字，然后按照正常的还原恢复步骤还原出一个影子库。



例 以脱机备份为例。

```
//备份数据库
dmrman CTLSTMT="BACKUP DATABASE 'd:\dpc_data_mac\bp11\DAMENG\dm.ini' FULL SHADOW TO BACKUP_02 BACKUPSET 'd:\dpc_data_mac\bp11\BACKUP_02'" USE_AP=2
//还原数据库：
dmrman CTLSTMT="RESTORE DATABASE 'd:\dpc_data_mac\bp12\DAMENG\dm.ini' FROM BACKUPSET 'd:\dpc_data_mac\bp11\BACKUP_02'" USE_AP=2
//因为脱机备份没有产生任何REDO日志，所以此处省略恢复数据库的操作步骤
//更新数据库：
dmrman CTLSTMT="RECOVER DATABASE 'd:\dpc_data_mac\bp12\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
```

  2. 可以对 RAFT 主库做联机或脱机备份，备份时不指定 SHADOW 关键字，在还原时指定 TO SHADOW 关键字，还原出一个影子库。



例 以脱机备份为例。

```
//备份数据库
dmrman CTLSTMT="BACKUP DATABASE 'd:\dpc_data_mac\bp11\DAMENG\dm.ini' FULL TO BACKUP_02 BACKUPSET 'd:\dpc_data_mac\bp11\BACKUP_02'" USE_AP=2
//还原数据库：
dmrman CTLSTMT="RESTORE DATABASE 'd:\dpc_data_mac\bp12\DAMENG\dm.ini' TO SHADOW FROM BACKUPSET 'd:\dpc_data_mac\bp11\BACKUP_02'" USE_AP=2
//因为脱机备份没有产生任何REDO日志，所以此处省略恢复数据库的操作步骤
//更新数据库：
dmrman CTLSTMT="RECOVER DATABASE 'd:\dpc_data_mac\bp12\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
```

### 7.5.2 影子库与 RAFT 库之间的转换

RAFT 库和影子库之间的转换方式说明如下：

  1. 将 RAFT 库转换为影子库

    1. 选择集群中某个 RAFT 库执行单独退出，不能是协同退出方式。
    2. 在相同机器上新初始化一个影子库，保留 IP/PORT、实例名等信息不变，这样不需要改其他 RAFT 库的配置。
    3. 使用 SHADOW 方式备份主库、还原影子库。
    4. 启动影子库，预期可以正常跟随主库同步日志。
  2. 将影子库转换为 RAFT 库

    1. 退出影子库。
    2. 使用正常备份还原方式，备份主库，还原一个新的 RAFT 目标库。
    3. 新的 RAFT 库使用和影子库相同的 IP/PORT、实例名等配置信息，这样不需要改其他 RAFT 库的配置。
    4. 启动新的 RAFT 库，预期可以正常跟随主库同步日志。



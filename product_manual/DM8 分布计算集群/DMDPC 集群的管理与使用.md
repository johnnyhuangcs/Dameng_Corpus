

# DMDPC 集群的管理与使用

## 8.1 集群信息查看

集群对外提供服务的有 SP、BS 模式的 BP 节点。BP 和 MP 在集群搭建完毕后一般不需登录。连接 SP 或 BS 模式的 BP 节点，通过查询相关系统表和动态视图可了解集群信息。具体的系统表和视图请参考 [12 相关系统表和动态视图](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-system_table-dynamic_view.html)。

例 1 查看当前节点的 DPC_MODE 模式信息。0：无；1：MP；2：BP；3：SP；4：BS。

```
select * from V$dm_ini where para_name='DPC_MODE' and sf_get_ep_seqno(rowid)=SF_GET_SELF_EP_SEQNO;
```

例 2 连接 SP，查询集群中 BP GROUP 的相关信息。

```
select * from DPC_BP_GROUP;
```

例 3 获取会话所在实例的 RAFT 序号 RAFT_ID。其中，MP 只有一个 RAFT，因此 RAFT_ID 定为 0。

```
SELECT SF_GET_SELF_EP_SEQNO();
```

例 4 根据 ROWID 获取本行数据所在的实例 RAFT_ID。

```
SELECT SF_GET_EP_SEQNO(ROWID);
//此语句查询的是SYSDUAL系统表，都认为是从MP获取的数据，因此返回MP的RAFT_ID为0
```

例 5 获取当前会话连接的实例名。

```
SELECT NAME FROM V$INSTANCE WHERE SF_GET_EP_SEQNO(ROWID) = SF_GET_SELF_EP_SEQNO();
```

例 6 获取 DMDPC 系统内所有实例的会话信息。

```
SELECT * FROM V$SESSIONS;
```

例 7 获取当前连接的实例上的所有会话信息。

```
SELECT * FROM V$SESSIONS WHERE SF_GET_EP_SEQNO(ROWID) = SF_GET_SELF_EP_SEQNO();
```

例 8 获取表 TEST 在每个实例上的数据行数。

```
CALL SP_GET_EP_COUNT('SYSDBA','TEST');
```

例 9 获取表 TEST 已使用的页数。

```
SELECT TABLE_USED_PAGES('SYSDBA','TEST');
```

## 8.2 动态增删节点

本章详细介绍动态增删节点的操作步骤。更多的理论介绍请参考 [5.10 动态增删节点](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-key-technology.html#5.10%20%E5%8A%A8%E6%80%81%E5%A2%9E%E5%88%A0%E8%8A%82%E7%82%B9)。

### 8.2.1 横向增删

横向增删支持动态增删 BP 节点或 SP 节点。

横向增删既可以登录到 SP 上执行，也可以登录到 MP 上执行。

单副本 DMDPC 或多副本 DMDPC 中均支持横向增删。下面以单副本 DMDPC 为例，介绍 BP、SP 节点的增删操作。

#### 8.2.1.1 BP 节点（单副本架构）

##### 8.2.1.1.1 增加 BP

动态添加 BP 节点的流程：第一步，注册一个新的 RAFT 组；第二步，在 RAFT 组中注册实例；第三步，注册一个新的 BP 组并添加 RAFT 组；第四步，将 MP.INI 文件拷贝到 BP 的库目录 DAMENG 下。

如果使用已经注册的 RAFT 组和 BP 组，则前两步可以省略，在第三步中直接使用已有的 RAFT 组即可。

使用的过程如下：

```
//第一步 创建一个新的RAFT组，名称为RAFT_1
SP_CREATE_DPC_RAFT('BP', 'RAFT_1');
//第二步 注册一个BP实例，名称为BP_1。此处的实例BP_1是提前初始化好的
SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP_1', 'BP', 6001, 5237, '223.254.30.136', '','NORMAL', 1, 'BP instance');
//第三步 创建一个新的BP组，名称为BG_1
SP_CREATE_DPC_BP_GROUP('BG_1', 'bp group1');
//在BP组内添加RAFT组
SP_BP_GROUP_ADD_RAFT('BG_1', 'RAFT_1');
//第四步 将MP.INI文件拷贝到BP的库目录DAMENG下
```

##### 8.2.1.1.2 删除 BP

同样，当业务规模缩小时，用户也可以从集群中移除 BP。删除 BP 节点的流程：第一步，移除实例；第二步，删除 RAFT 组，必须保证 RAFT 组内的 BP 实例已全部移除，才能删掉 RAFT；第三步，删除 BP 组。

使用的过程如下：

```
//第一步 移除BP实例
SP_DROP_DPC_INSTANCE('BP_1');
//第二步 删除RAFT组
SP_DROP_DPC_BP_RAFT('RAFT_1');
//第三步 删除BP组
SP_DROP_DPC_BP_GROUP('BG_1');
```

#### 8.2.1.2 SP 节点（单副本架构）

##### 8.2.1.2.1 增加 SP

动态添加 SP 节点的流程：第一步，注册一个新的 RAFT 组；第二步，在 RAFT 组中注册实例；第三步，将 MP.INI 文件拷贝到 SP 的库目录 DAMENG 下。

如果使用已经注册的 RAFT 组，则第一步可以省略，在第二步中直接使用已有的 RAFT 组即可。

一个 SP 类型的 RAFT 组最多只能加入一个 SP 节点。

使用的过程如下：

```
//第一步，为SP创建一个新的RAFT组，名称为RAFT_SP1

SP_CREATE_DPC_RAFT('SP', 'RAFT_SP1');

//第二步，在RAFT_SP1内注册SP实例，名称为SP_1。此处的实例SP_1是提前初始化好的

SP_CREATE_DPC_INSTANCE('RAFT_SP1', 'SP_1', 'SP', 6000, 5236, '223.254.30.136', '','NORMAL', 2, 'SP instance');

//第三步 将MP.INI文件拷贝到SP的库目录DAMENG下
```

##### 8.2.1.2.2 删除 SP

动态移除 SP 的流程：第一步，移除实例；第二步，移除 RAFT 组，必须保证 RAFT 组内的 SP 实例已经移除，才能删掉 RAFT。

使用的过程如下：

```
//第一步 移除SP实例

SP_DROP_DPC_INSTANCE('SP_1');

//第二步 删除RAFT组

SP_DROP_DPC_BP_RAFT('RAFT_SP1');
```

### 8.2.2 纵向增删

纵向增删支持动态增删 MP 和 BP 节点。

纵向增删 MP 副本节点，需要登录到 MP 主节点上执行。纵向增删 BP 多副本节点，需要以 BS 模式登录到 BP 主节点上执行。

仅多副本系统支持纵向增删。下面以多副本 DMDPC 为例，介绍 BP 多副本系统、MP 多副本系统的增删操作。

#### 8.2.2.1 BP 节点（多副本架构）

##### 8.2.2.1.1 增加 BP

多副本架构下动态添加 BP 节点的流程与非多副本架构存在差异。在多副本架构下对增删节点操作存在部分限制，说明如下：

  1. 增加或删除后的多副本集群中节点需要为奇数。
  2. 暂时不允许删除主节点，若要删除主节点，则需要执行手动主库切换动作，将待删除的节点变为备库。



下面介绍多副本集群下增删节点操作流程：

多副本架构增加 BP 节点流程如下：

第一步，初始化节点。

初始化 BP 节点，并将 BP 节点注册对应 RAFT 组中。

```
//初始化BP4，BP5节点

dminit path=e:\dpc_data\bp4 instance_name=BP4 port_num=5242 ap_port_num=6606 dpc_mode=BP

dminit path=e:\dpc_data\bp5 instance_name=BP5 port_num=5243 ap_port_num=6607 dpc_mode=BP

//将新初始化的节点注册进待新增节点的RAFT_1组中

SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP4', 'BP', 6606, 5242, '192.168.1.68', 'STANDBY', 0, 'BP instance');

SP_CREATE_DPC_INSTANCE('RAFT_1', 'BP5', 'BP', 6607, 5243, '192.168.1.68', 'STANDBY', 0, 'BP instance');
```

第二步，修改配置。

  1. 打开 BP4，BP5 节点 dm.ini 归档选项 ARCH_INI = 1。
  2. 配置 dmarch.ini 文件。



BP4 的归档配置如下：

```
XMAL_HB_INTERVAL	     = 5				#节点通信检测间隔
RAFT_HB_INTERVAL	     = 150  			#选举心跳间隔
RAFT_VOTE_INTERVAL	     = 1500			#选举超时时间,三个库设置不同以尽快选出主库
XMAL_IP				     = 192.168.1.68   #本地通信IP
XMAL_PORT				 = 6606		  #本地通信端口
RAFT_SELF_ID              = 4				#指定自身编号
RAFT_LEARNER	          =  1		#指定自己是Learner节点

[ARCHIVE_RAFT1] 
ARCH_TYPE				= RAFT    		#RAFT归档
ARCH_DEST			     = BP1			#归档目标实例名
ARCH_DEST_IP			= 192.168.1.68      #归档目标IP
ARCH_DEST_PORT		     = 10008			    #归档目标通信端口
ARCH_DEST_ID              = 1               #指定归档目标节点编号

[ARCHIVE_RAFT2] 
ARCH_TYPE				 = RAFT		    #RAFT归档
ARCH_DEST				 = BP2		#归档目标实例名
ARCH_DEST_IP			 = 192.168.1.68	#归档目标IP
ARCH_DEST_PORT		      = 10009		#归档目标通信端口
ARCH_DEST_ID               = 2               #指定归档目标节点编号

[ARCHIVE_RAFT3] 
ARCH_TYPE				 = RAFT		     #RAFT归档
ARCH_DEST				 = BP3	         #归档目标实例名
ARCH_DEST_IP			 = 192.168.1.68	     #归档目标IP
ARCH_DEST_PORT		      = 10010		#归档目标通信端口
ARCH_DEST_ID               = 3               #指定归档目标节点编号

[ARCHIVE_RAFT4] 
ARCH_TYPE				 = LEARNER		#RAFT归档
ARCH_DEST				 = BP5 	         #归档目标实例名
ARCH_DEST_IP			 = 192.168.1.68	     #归档目标IP
ARCH_DEST_PORT		      = 6607			#归档目标通信端口
ARCH_DEST_ID               = 5              #指定归档目标节点编号

[ARCHIVE_LOCAL1]
ARCH_TYPE			     = LOCAL			#本地归档类型
ARCH_DEST			     = E:\RAFT_DB\ARCH\BP4 #本地归档文件路径
ARCH_FILE_SIZE	 	     = 128				#本地单个归档文件最大值，单位MB
ARCH_SPACE_LIMIT	     = 0				#本地归档文件总大小，0表示无限制

```

BP5 的归档配置如下：

```
XMAL_HB_INTERVAL	     = 5				#节点通信检测间隔
RAFT_HB_INTERVAL	     = 150  			#选举心跳间隔
RAFT_VOTE_INTERVAL	     = 1500			#选举超时时间，三个库设置不同以尽快选出主库
XMAL_IP				     = 192.168.1.68   #本地通信IP
XMAL_PORT				 = 6607		  #本地通信端口
RAFT_SELF_ID              = 5				#指定自身编号
RAFT_LEARNER	          =  1		#指定自己是Learner节点

[ARCHIVE_RAFT1] 
ARCH_TYPE				= RAFT    		#RAFT归档
ARCH_DEST			     = BP1			#归档目标实例名
ARCH_DEST_IP			= 192.168.1.68      #归档目标IP
ARCH_DEST_PORT		     = 10008			    #归档目标通信端口
ARCH_DEST_ID              = 1               #指定归档目标节点编号

[ARCHIVE_RAFT2] 
ARCH_TYPE				 = RAFT		    #RAFT归档
ARCH_DEST				 = BP2		#归档目标实例名
ARCH_DEST_IP			 = 192.168.1.68	#归档目标IP
ARCH_DEST_PORT		      = 10009			#归档目标通信端口
ARCH_DEST_ID               = 2               #指定归档目标节点编号

[ARCHIVE_RAFT3] 
ARCH_TYPE				 = RAFT		     #RAFT归档
ARCH_DEST				 = BP3	         #归档目标实例名
ARCH_DEST_IP			 = 192.168.1.68	     #归档目标IP
ARCH_DEST_PORT		      = 10010			#归档目标通信端口
ARCH_DEST_ID               = 3               #指定归档目标节点编号

[ARCHIVE_RAFT4] 
ARCH_TYPE				 = LEARNER		#RAFT归档
ARCH_DEST				 = BP4 	         #归档目标实例名
ARCH_DEST_IP			 = 192.168.1.68	     #归档目标IP
ARCH_DEST_PORT		      = 6606			#归档目标通信端口
ARCH_DEST_ID               = 4              #指定归档目标节点编号

[ARCHIVE_LOCAL1]
ARCH_TYPE			     = LOCAL			#本地归档类型
ARCH_DEST			     = E:\RAFT_DB\ARCH\BP5 #本地归档文件路径
ARCH_FILE_SIZE	 	     = 128				#本地单个归档文件最大值，单位MB
ARCH_SPACE_LIMIT	     = 0				#本地归档文件总大小，0表示无限制
```

  3. 配置 mp.ini 与本集群的其他节点 mp.ini 文件内容一致。



第三步，准备节点数据。

  1. 启动 DMAP，连接 BP1，联机备份 RAFT_1 组中主节点 BP1。



```
SQL>BACKUP DATABASE BACKUPSET 'E:\dpc\bp1\DAMENG\bak';
```

```
得到BP1的备份集E:\dpc\bp1\DAMENG\bak_BP1。
```

  2. 还原 BP4，BP5 节点。



```
//还原BP4。其中BAK_MAGIC可通过DMRMAN工具SHOW 命令查看备份集得知。
RMAN>SHOW BACKUPSET 'E:\dpc\bp1\DAMENG\bak_BP1';
//假设BAK_MAGIC 为1903999294。
RMAN>RESTORE DATABASE 'E:\dpc\bp4\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\bp1\DAMENG\bak_BP1' USE BAK_MAGIC 1903999294;
RMAN>RECOVER DATABASE 'E:\dpc\bp4\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc\bp1\DAMENG\bak_BP1' USE BAK_MAGIC 1903999294 UNTIL END_LSN;
RMAN>RECOVER DATABASE 'E:\dpc\bp4\DAMENG\dm.ini' UPDATE DB_MAGIC;
//还原BP5
RMAN>RESTORE DATABASE 'E:\dpc\bp5\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\bp1\DAMENG\bak_BP1' USE BAK_MAGIC 1903999294;
RMAN>RECOVER DATABASE 'E:\dpc\bp5\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc\bp1\DAMENG\bak_BP1' USE BAK_MAGIC 1903999294 UNTIL END_LSN;
RMAN>RECOVER DATABASE 'E:\dpc\bp5\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

第四步，启动节点，执行增加节点操作。

  1. 启动 BP4，BP5。
  2. 连接 RAFT_1 组中主库 BP1，执行增加节点流程。



```
//将BP4，BP5以learner节点的方式加入集群，从而在不影响集群使用条件下向新节点同步日志
SP_ADD_RAFT_LEARNER('BP4','192.168.1.68',6606,4);
SP_ADD_RAFT_LEARNER('BP5','192.168.1.68',6607,5);
//将新节点加入集群
SP_ALTER_RAFT_NODE('BP1/BP2/BP3/BP4/BP5');
```

##### 8.2.2.1.2 删除 BP

执行节点删除操作如下：

  1. 连接 RAFT_1 中主节点 BP1，执行删除节点操作。



```
//执行
SP_ALTER_RAFT_NODE('BP1/BP2/BP3');
或
SP_DELETE_RAFT_NODE('BP4/BP5');
```

  2. 连接 MP 移除 BP 实例。



```
//移除BP4，BP5实例
SP_DROP_DPC_INSTANCE('BP4');
SP_DROP_DPC_INSTANCE('BP5');
```

#### 8.2.2.2 MP 节点（多副本架构）

在多副本架构下对增删 MP 节点操作存在部分限制，说明如下：

  1. 由于 MP.INI 暂不支持动态修改，因此若执行 MP 的动态增删操作时，需要额外手动修改集群内其他节点的 MP.INI 文件。
  2. 增加或删除后的多副本集群中节点需要为奇数。
  3. 暂时不允许删除主节点。若要删除主节点，则需要执行手动主库切换动作，将待删除的节点变为备库，才可删除。



下面介绍多副本集群下增删节点操作流程。

##### 8.2.2.2.1 增加 MP

多副本架构增加 MP 节点流程如下：

第一步，初始化节点。

初始化 MP 节点，并将 MP 节点注册对应 RAFT 组中。

```
//初始化MP4，MP5节点

dminit path=e:\dpc_data\mp4 instance_name=MP4 port_num=5242 ap_port_num=6606 dpc_mode=MP

dminit path=e:\dpc_data\mp5 instance_name=MP5 port_num=5243 ap_port_num=6607 dpc_mode=MP

//将新初始化的节点注册仅待新增节点的MP_RAFT组中

SP_CREATE_DPC_INSTANCE('', 'MP4', 'MP', 6606, 5242, '192.168.1.68', 'STANDBY', 0, 'MP instance');

SP_CREATE_DPC_INSTANCE('', 'MP5', 'MP', 6607, 5243, '192.168.1.68', 'STANDBY', 0, 'MP instance');
```

第二步，修改配置。

  1. 打开 MP4，MP5 节点 dm.ini 归档选项 ARCH_INI = 1。
  2. 配置 dmarch.ini 文件。



MP4 的归档配置如下：

```
XMAL_HB_INTERVAL	     = 5				#节点通信检测间隔
RAFT_HB_INTERVAL	     = 150  			#选举心跳间隔
RAFT_VOTE_INTERVAL	     = 1500			#选举超时时间,三个库设置不同以尽快选出主库
RAFT_SELF_ID              = 4				#指定自身编号
RAFT_LEARNER	          =  1		#指定自己是Learner节点

[ARCHIVE_RAFT1] 
ARCH_TYPE				= RAFT    		#RAFT归档
ARCH_DEST			     = MP1			#归档目标实例名
ARCH_DEST_ID              = 1               #指定归档目标节点编号

[ARCHIVE_RAFT2] 
ARCH_TYPE				 = RAFT		    #RAFT归档
ARCH_DEST				 = MP2		#归档目标实例名
ARCH_DEST_ID              = 2               #指定归档目标节点编号

[ARCHIVE_RAFT3] 
ARCH_TYPE				 = RAFT		     #RAFT归档
ARCH_DEST				 = MP3	         #归档目标实例名

ARCH_DEST_ID               = 3               #指定归档目标节点编号

[ARCHIVE_RAFT4] 
ARCH_TYPE				 = LEARNER		#RAFT归档
ARCH_DEST				 = MP5 	         #归档目标实例名
ARCH_DEST_ID               = 5              #指定归档目标节点编号

[ARCHIVE_LOCAL1]
ARCH_TYPE			     = LOCAL			#本地归档类型
ARCH_DEST			     = E:\RAFT_DB\ARCH\MP4 #本地归档文件路径
ARCH_FILE_SIZE	 	     = 128				#本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT	     = 0				#本地归档文件总大小,0表示无限制
```

MP5 的归档配置如下：

```
XMAL_HB_INTERVAL	     = 5				#节点通信检测间隔
RAFT_HB_INTERVAL	     = 150  			#选举心跳间隔
RAFT_VOTE_INTERVAL	     = 1500			#选举超时时间,三个库设置不同以尽快选出主库
RAFT_SELF_ID              = 5				#指定自身编号
RAFT_LEARNER	          =  1			#指定自己是Learner节点

[ARCHIVE_RAFT1] 
ARCH_TYPE				= RAFT    		#RAFT归档
ARCH_DEST			     = MP1			#归档目标实例名
ARCH_DEST_ID              = 1               #指定归档目标节点编号

[ARCHIVE_RAFT2] 
ARCH_TYPE				 = RAFT		   	#RAFT归档
ARCH_DEST				 = MP2			#归档目标实例名
ARCH_DEST_ID              = 2               #指定归档目标节点编号

[ARCHIVE_RAFT3] 
ARCH_TYPE				 = RAFT		     #RAFT归档
ARCH_DEST				 = MP3	       	#归档目标实例名
ARCH_DEST_ID               = 3               #指定归档目标节点编号

[ARCHIVE_RAFT4] 
ARCH_TYPE				 = LEARNER		#RAFT归档
ARCH_DEST				 = MP4 	         #归档目标实例名
ARCH_DEST_ID               = 4              #指定归档目标节点编号

[ARCHIVE_LOCAL1]
ARCH_TYPE			     = LOCAL			#本地归档类型
ARCH_DEST			     = E:\RAFT_DB\ARCH\MP5 #本地归档文件路径
ARCH_FILE_SIZE	 	     = 128				#本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT	     = 0				#本地归档文件总大小,0表示无限制
```

  3. 配置 MP4，MP5 的 mp.ini，并将本集群所有节点的 mp.ini 文件内容修改到一致。



```
# mp_port端口号必须与MP、BP和SP上的ap_port_num端口号不冲突

[MP1]

mp_host = 192.168.1.68

mp_port = 10622

[MP2]

mp_host = 192.168.1.68

mp_port = 10623

[MP3]

mp_host = 192.168.1.68

mp_port = 10624

[MP4]

mp_host = 192.168.1.68

mp_port = 10625

[MP5]

mp_host = 192.168.1.68

mp_port = 10626
```

第三步，准备节点数据。

  1. 启动 DMAP， 连接 MP1，联机备份主节点 MP1。



```
SQL>BACKUP DATABASE BACKUPSET 'E:\dpc\mp1\DAMENG\bak';
```

  2. 还原 MP4，MP5 节点。



```
//还原MP4。BAK_MAGIC值可通过DMRMAN工具的SHOW 命令查看备份集得知。
RMAN>SHOW BACKUPSET 'E:\dpc\mp1\DAMENG\bak_MP1';
//BAK_MAGIC 为1903999294。
RMAN>RESTORE DATABASE 'E:\dpc\mp4\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\mp1\DAMENG\bak_MP1' USE BAK_MAGIC 1903999294;
RMAN>RECOVER DATABASE 'E:\dpc\mp4\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc\mp1\DAMENG\bak_MP1' USE BAK_MAGIC 1903999294 UNTIL END_LSN;
RMAN>RECOVER DATABASE 'E:\dpc\mp4\DAMENG\dm.ini' UPDATE DB_MAGIC;
//还原MP5
RMAN>RESTORE DATABASE 'E:\dpc\mp5\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\mp1\DAMENG\bak_MP1' USE BAK_MAGIC 1903999294;
RMAN>RECOVER DATABASE 'E:\dpc\mp5\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc\mp1\DAMENG\bak_MP1' USE BAK_MAGIC 1903999294 UNTIL END_LSN;
RMAN>RECOVER DATABASE 'E:\dpc\mp5\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

第四步，启动节点，执行增加节点操作。

  1. 启动 MP4，MP5。
  2. 连接主库 MP1，执行增加节点流程。



```
//将MP4，MP5以learner节点的方式加入集群，从而在不影响集群使用条件下向新节点同步日志

SP_ADD_RAFT_LEARNER('MP4','192.168.1.68',10624,4);

SP_ADD_RAFT_LEARNER('MP5','192.168.1.68',10625,5);                                                                                                                                                                       
//将新节点加入集群

SP_ALTER_RAFT_NODE('MP1/MP2/MP3/MP4/MP5');
```

##### 8.2.2.2.2 删除 MP

执行节点删除操作如下：

  1. 连接 RAFT_1 中主节点 MP1，执行删除节点操作。



```
//执行

SP_ALTER_RAFT_NODE('MP1/MP2/MP3');

或

SP_DELETE_RAFT_NODE('MP4/MP5');
```

  2. 连接 MP 删除 MP 实例。



```
//移除MP4，MP5实例

SP_DROP_DPC_INSTANCE('MP4');

SP_DROP_DPC_INSTANCE('MP5');
```

  3. 修改所有节点的 mp.ini 文件。



```
# mp_port端口号必须与MP、BP和SP上的ap_port_num端口号不冲突

[MP1]

mp_host = 192.168.1.68

mp_port = 10622

[MP2]

mp_host = 192.168.1.68

mp_port = 10623

[MP3]

mp_host = 192.168.1.68

mp_port = 10624
```

## 8.3 管理 DMDPC 表空间

DMDPC 支持表空间的功能，包括表空间的创建，建表时指定表空间，创建分区组时指定表空间，基于表空间实现的节点间数据迁移从而实现数据重分布等功能。

DMDPC 创建表空间语法和单机类似，不同的地方有两点：一是 DMDPC 增加了一个指定表空间所在 RAFT 组和 BP 组的\<STORAGE 子句>；二是在 DMDPC 中增加了一个\<HUGE 路径子句>，和\<数据文件子句>搭配使用，可创建一个混合表空间，可以同时存储 HUGE 表和非 HUEG 表。

语法如下：

```
CREATE TABLESPACE <表空间名><数据文件子句>[<数据页缓冲池子句>][<存储加密子句>][<HUGE路径子句>][<STORAGE子句>]
<STORAGE子句> ::= 
	STORAGE (ON <RAFT组名>)|
	STORAGE (ON <BP组名>)
<HUGE路径子句> ::= WITH HUGE PATH <文件路径>
其他语法请参考《DM8_SQL语言使用手册》中管理表空间章节
```

\<HUGE 路径子句>和\<数据文件子句>同时使用，可创建一个混合表空间。所谓的混合表空间就是一次创建两个同名的表空间：一个常规（非 HUGE）表空间和一个 HUGE 表空间。常规表空间位于\<数据文件子句>指定的路径下，HUGE 表空间位于\<HUGE 路径子句>指定的路径下。因为两个表空间同名，建表时指定表空间的用法又一样，因此对用户来说仿佛就是一个表空间，且既能存储 HUGE 表又能存储非 HUGE 表，因此又称为一个混合表空间。

\<STORAGE 子句 > 通过 \<RAFT 组名 > 或 \<BP 组名 > 指定一个 RAFT 组作为表空间的存储位置。RAFT 组名或 BP 组名必须是已存在的组名。\< 表空间名 >、\<RAFT 组名 > 、 \<BP 组名 > 三者不能同名。若指定的是 \<RAFT 组名 >，则为明确指定 RAFT 组名。若指定的是 \<BP 组名 >，则系统会从指定的 BP 组中随机挑选一个 RAFT 组。如果 \<STORAGE 子句 > 缺省，即 \<RAFT 组名 > 和 \<BP 组名 > 均未指定，则系统会从现有的 BP RAFT 组中随机挑选一个作为表空间的存储位置。

例 1，分别创建存储在 RAFT_1 的非 HUGE 普通表空间 TS_1 和存储在 RAFT_2 上的混合表空间 TS_2。

```
CREATE TABLESPACE TS_1 DATAFILE 'TS_1.DBF' SIZE 128 STORAGE( ON RAFT_1);
CREATE TABLESPACE TS_2 DATAFILE 'TS_2.DBF' SIZE 128 WITH HUGE PATH 'e:\mpraft_data\hts' STORAGE( ON RAFT_2);
```

## 8.4 管理 DMDPC 表空间组

表空间组是一组位于相同 RAFT 或者不同 RAFT 上的表空间集合，用于用户建表或者指定用户默认的存储位置。表空间组中的表空间可动态扩展和收缩。且能同时包含普通表空间和混合表空间。

表空间组对象存储在系统表 SYSTSGROUPS 中，表空间组通过系统过程进行管理。详细的系统过程请参考[6.2 配置方法](#6.2 配置方法)。

### 8.4.1 创建表空间组

定义：

```
SP_TS_GROUP_CREATE(
	ts_group_name varchar(128),
	desc varchar(256)
)
```

功能说明：

创建一个表空间组。

参数说明：

ts_group_name：表空间组名称；

desc：描述信息。

举例说明：

创建一个表空间组 TSG_1。

```
SP_TS_GROUP_CREATE('TSG_1', 'tablespace group1');
```

### 8.4.2 删除表空间组

定义：

```
SP_TS_GROUP_DROP(
	ts_group_name varchar(128)
)
```

功能说明：

删除一个表空间组。

参数说明：

ts_group_name：表空间组名称。

举例说明：

删除表空间组 TSG_1。

```
SP_TS_GROUP_DROP('TSG_1');
```

### 8.4.3 向表空间组中添加表空间

定义：

```
SP_TS_GROUP_ADD_TS(
	ts_group_name varchar(128),
	ts_name varchar(256)
)
```

功能说明：

向表空间组中添加一个表空间。表空间组内只能添加用户创建的表空间，不能添加系统表空间。表空间组内可同时添加普通表空间和带 HUGE 路径的表空间。实际使用时会根据建表的 HUGE 属性自动选择。

参数说明：

ts_group_name：表空间组名称；

ts_name：表空间名称。

举例说明：

向表空间组 TSG_1 中添加表空间 TS_1。

```
SP_TS_GROUP_ADD_TS('TSG_1', 'TS_1');
```

### 8.4.4 删除表空间组中的表空间

定义：

```
SP_TS_GROUP_REMOVE_TS(
	ts_group_name varchar(128),
	ts_name varchar(256)
)
```

功能说明：

删除表空间组中的一个表空间。

参数说明：

ts_group_name：表空间组名称；

ts_name：表空间名称。

举例说明：

删除表空间组 TSG_1 中的表空间 TS_1。

```
SP_TS_GROUP_REMOVE_TS('TSG_1', 'TS_1');
```

## 8.5 管理用户

用户可以指定和修改默认表空间/表空间组。

### 8.5.1 创建用户

语法格式

```
CREATE USER <用户名> IDENTIFIED <身份验证模式>  [PASSWORD_POLICY <口令策略>][<锁定子句>][<存储加密密钥>][<空间限制子句>][<只读标志>][<资源限制子句>][<允许IP子句>][<禁止IP子句>][<允许时间子句>][<禁止时间子句>][<TABLESPACE子句>]
<TABLESPACE子句> ::= DEFAULT TABLESPACE <表空间名>  |
                         DEFAULT TABLESPACE GROUP <表空间组名> 
```

\<TABLESPACE 子句>用于指定用户的默认表空间或表空间组。

\<表空间名>默认表空间名。\<表空间名>缺省或者设置为 NULL 表示未设置默认用户表空间。

\<表空间组名>默认表空间组名。不能和\<表空间名>重名。\<表空间组名>缺省或者设置为 NULL 表示未设置用户默认表空间组。

例 创建一个指定默认表空间组的用户。

```
CREATE USER USER_1 IDENTIFIED BY 123456789 DEFAULT TABLESPACE GROUP TSG_1;
```

### 8.5.2 修改用户

语法格式

```
ALTER USER <用户名> [[IDENTIFIED <身份验证模式>] [PASSWORD_POLICY <口令策略>] [<锁定子句>] [<存储加密密钥>] [<空间限制子句>] [<只读标志>][<资源限制子句>][<允许IP子句>][<禁止IP子句>][<允许时间子句>][<禁止时间子句>][<TABLESPACE子句>]
<TABLESPACE子句> ::=DEFAULT TABLESPACE <表空间名> |
                        DEFAULT TABLESPACE GROUP <表空间组名>
```

\<TABLESPACE 子句>用于修改用户的默认表空间或表空间组。如果\<表空间名>或\<表空间组名>设置的是 NULL，表示清除之前设置的用户默认表空间或者表空间组。

例 修改用户的默认表空间组。

```
ALTER USER USER_1 DEFAULT TABLESPACE GROUP TSG_1;
```

## 8.6 管理 DMDPC 表

在 DMDPC 环境中数据存储在各个 BP，用户创建表之前必须先创建用户表空间，如果没有创建任何用户表空间则建表会报错。

有两种指定存储位置的方式：一，建表时指定表空间或表空间组，该方式既可以创建分区表又可以创建非分区表；二，基于分区组创建表，该方式先创建分区组，在分区组中指定表空间名，然后基于分区组创建分区表。方式二只能创建分区表。如果应用场景中存在多个分区方式相同且分区列之间有关联关系的分区表，推荐使用方式二。

### 8.6.1 建表时指定表空间或表空间组

DMDPC 创建表语法和单机类似。不同的是：指定 HASH 分区个数 \<分区数>时，可以使用关键字 DEFAULT 代替具体的分区数字，此时分区个数为建表语句涉及的 BP RAFT 个数和 DM.INI 参数 hp_tab_count_per_bp 的乘积，但是上限不能超过当前系统允许的最大子表个数。

在 DMDPC 中使用[ON \<表空间名>]或[ON \<表空间组名>]来指定表空间名。其他语法请参考《DM8_SQL 语言使用手册》中定义数据库基表章节。

语法如下：

```
……
<STORAGE子句> ::= STORAGE(<STORAGE项> {,<STORAGE项>})
<STORAGE项> ::= 
	[INITIAL <初始簇数目>] |
	[NEXT <下次分配簇数目>] |
	[MINEXTENTS <最小保留簇数目>] |
	[ON <表空间名>] |  
	[ON <表空间组名>] |
	[FILLFACTOR <填充比例>]|
	[BRANCH  <BRANCH数>]|
	[BRANCH  (<BRANCH数>, <NOBRANCH数>)]|
	[NOBRANCH]|
	[<CLUSTERBTR>]|
	[WITH COUNTER]|
	[WITHOUT COUNTER] |
    [USING LONG ROW]
……
```

[ON \<表空间名>]或[ON \<表空间组名>]用来指定存储的表空间名或表空间组名。非 HUGE 表既可以存储在非 HUGE 普通表空间也可以存储在混合表空间中；但 HUGE 表只能使用混合表空间进行存储。对包含引用约束的引用表和被引用表，要求二者的对应分区存储在同一个表空间中。

建表时系统选择存储表空间的规则为：

1.允许显式指定表空间/表空间组的方式建表；

2.如果未显式指定存储选项，则先使用用户默认的表空间/表空间组；

3.如果用户未指定默认表空间/组，再从系统的用户表空间中挑选；

4.如果未创建任何用户表空间会报错，提示用户需要创建表空间；

5.创建水平分区表时，如果参数 DPC_TABLESPACE_BALANCE 不为 0，且未显式指定存储选项，或者仅指定了分区主表存储于某表空间组，那么系统会计算将要创建的单层的叶子子表数 N1和可用表空间数 N2。当 N1\<N2时，系统会从众多可用表空间中随机选择起始表空间，其目的是确保表空间的存储均匀。哈希分区使用了 Partitions default 的情况不在此列。DPC_TABLESPACE_BALANCE 取值 1 和 2 的区别：为一级水平分区表增加新的子表且未指定存储位置时，取值为 1 时系统会根据已有的兄弟子表存储位置来确定新增子表存储；而取值为 2 时系统会考虑所有可用表空间来选择新的子表存储。如果创建分区表时指定了存储位置，那么在增加子表时建议不要指定以便能保持子表分布的规律性；

6.向间隔分区表中插入超出了指定分区范围的数据，将会自动新增分区，新增的分区只会落在建表时已有的表空间上，不会落在建表后新增的表空间上。因为在建表时，每个子分区（包括模板）的存储表空间已经固化，后面新增分区时只会按照上述固化的表空间遍历挑选。

例 1 将表 test1 的 P1 分区存储在表空间 TS_1 上，P2 分区存储在表空间 TS_2 上，P3 分区存储在表空间 TS_3 上。

```
create table test1(c1 int, c2 int) PARTITION BY RANGE(c1)(
PARTITION p1 VALUES LESS THAN (10) storage (on TS_1),
PARTITION p2 VALUES LESS THAN (20) storage (on TS_2),
PARTITION p3 VALUES EQU OR LESS THAN(MAXVALUE) storage (on TS_3));
```

例 2 将表 test2 中的 P1 分区存储在表空间 TS_1 上，P2 分区存储在表空间组 TSG_1 上。P3 未明确指定表空间或表空间组，缺省存储在表空间组 TSG_2 上。

```
create table test2(c1 int, c2 int) PARTITION BY RANGE(c1)(
PARTITION p1 VALUES LESS THAN (10) storage (on TS_1),
PARTITION p2 VALUES LESS THAN (20) storage (on TSG_1),
PARTITION p3 VALUES EQU OR LESS THAN(MAXVALUE)
) storage (on TSG_2);
```

### 8.6.2 基于分区组创建分区表

#### 8.6.2.1 分区组简介

分区组（Partition Group，PG）是 DMDPC 为了简化水平分区表建表操作而提供的一项新功能。分区组是将水平分区表创建时使用的分区规则提取成一个独立的逻辑概念。使用分区组创建水平分区表的步骤是先创建分区组，然后再基于分区组创建分区表。如果创建水平分区表的语句中未使用分区组，那么还是按照常规的方式处理，将分区规则直接写在建表语句中。使用分区组建表既可以简化建表操作，又可以提升事务处理的性能。

不同分区表可以使用同一个分区组，每个分区表只允许属于一个分区组。

使用分区组后，隶属相同分区组的对应分区始终在同一表空间中，基于分区列的表连接处理可以减少跨机事务和连接时数据交换的开销。因此，对于需要进行连接操作的两张表，推荐使用相同的分区组 PG。

图 8.1 中预先定义了分区组 PG1、PG2、PG3 和 PG4，其中 PG1 有三个分区 P1、P2、P3 分别存储于表空间 TS1、TS2 和 TS3。PG2 定义了三个分区 P1、P2、P3 全部存储于 TS1。PG3 有二个分区 P1、P2 分别存储于 TS2 和 TS3 中。PG4 只有一个分区 P1 存储于 TS2 中。

![图 8.1 分区组.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221128145717AN87PNTWVXO5SKAC5A)

图 8.1 分区组

同一分区组可以跨多个表空间。例如：PG1 和 PG3 都横跨了几个表空间。

隶属于同一分区组的多个表，分区组中定义的分区可以保证这些表的相同分区会分布在相同的表空间上。例如：若 T1 和 T2 表都使用了 PG1 分区组，则 PG1 的分区可以保证 T1 和 T2 表的相同分区会分布在相同的表空间上。若 T3 和 T4 表都使用了 PG2 分区组，则它们所对应的分区分布情况也相同。

#### 8.6.2.2 分区组定义语句

目前，DMDPC 系统仅支持一级水平分区和二级水平分区。

语法如下：

```
CREATE PARTITION GROUP <组名> <PARTITION子句> [<DMDPC_存储位置子句>]
<PARTITION子句>::= PARTITION BY <PARTITION项>
<PARTITION项>::=
	RANGE (<分区列类型>{,<分区列类型>}) [INTERVAL <间隔表达式>] [<SUBPARTITION 子句>{,< SUBPARTITION 子句>}](<RANGE分区项> {,<RANGE分区项>}) | 
	HASH (<分区列类型>{,<分区列类型>}) [<SUBPARTITION 子句>{,< SUBPARTITION 子句>}]PARTITIONS <分区数> [<STORAGE HASH 子句>]| 
	HASH(<分区列类型>{,<分区列类型>}) [<SUBPARTITION 子句>{,< SUBPARTITION 子句>}] (<HASH分区项> {,<HASH分区项>})| 
	LIST(<分区列类型>)[<SUBPARTITION 子句>{,< SUBPARTITION 子句>}](<LIST分区项> {,<LIST分区项>}) 
<RANGE分区项>::= PARTITION <分区名> VALUES [EQU OR] LESS THAN (< <常量表达式>|<日期函数表达式>|MAXVALUE >{,< <常量表达式>|<日期函数表达式>|MAXVALUE >}) [<DMDPC_STORAGE子句>][<子分区描述项>]
<日期函数表达式>::= <to_date函数表达式> | <to_datetime函数表达式> | <to_timestamp函数表达式>
<HASH分区项>::= PARTITION <分区名> [<DMDPC_STORAGE子句>][<子分区描述项>]
<LIST分区项>::= PARTITION <分区名> VALUES (DEFAULT|<表达式>,{<表达式>}>) [<DMDPC_STORAGE子句>][<子分区描述项>]
<子分区描述项>::=
	(<RANGE子分区描述项>{,<RANGE子分区描述项>})|
	(<HASH子分区描述项>{,<HASH子分区描述项>})|
	SUBPARTITIONS <分区数> [<DMDPC_存储位置子句>]|
	(<LIST子分区描述项>{,<LIST子分区描述项>})
<RANGE子分区描述项>::= <RANGE子分区项>[<子分区描述项>]
<HASH子分区描述项>::= <HASH子分区项>[<子分区描述项>]
<LIST子分区描述项>::= <LIST子分区项>[<子分区描述项>]
<RANGE子分区项>::=
SUBPARTITION <分区名> VALUES [EQU OR] LESS THAN (<常量表达式>|<日期函数表达式>|MAXVALUE){,<常量表达式|<日期函数表达式>|MAXVALUE>}) [<DMDPC_STORAGE子句>]
<HASH子分区项>::= SUBPARTITION <分区名> [<DMDPC_STORAGE子句>]
<LIST子分区项>::= SUBPARTITION <分区名> VALUES (DEFAULT|<<表达式>,{<表达式>}>) [<DMDPC_STORAGE子句>]
<SUBPARTITION 子句> ::= <RANGE子分区模板项>|<HASH子分区模板项>|<LIST子分区模板项>
<RANGE子分区模板项> ::= SUBPARTITION BY RANGE (<列名>{,<列名>})[SUBPARTITION TEMPLATE (<RANGE子分区项> {,<RANGE子分区项>})]
<HASH子分区模板项> ::= 
	SUBPARTITION BY HASH (<列名>{,<列名>})SUBPARTITION TEMPLATE SUBPARTIONS <分区数> [<STORAGE HASH 子句>]|
	SUBPARTITION BY HASH (<列名>{,<列名>})SUBPARTITION TEMPLATE (<HASH子分区项> {,<HASH子分区项>})
<LIST子分区模板项> ::= SUBPARTITION BY LIST (<列名>{,<列名>})[SUBPARTITION TEMPLATE (<LIST子分区项> {,<LIST子分区项>})]
<STORAGE HASH 子句>::= STORE IN (<DMDPC_存储位置> {,<DMDPC_存储位置>})
<DMDPC_STORAGE子句>::= STORAGE (ON <DMDPC_存储位置> )
<DMDPC_存储位置子句>::= STORAGE (<DMDPC_存储位置链表> | <DMDPC_存储选项链表>)
<DMDPC_存储位置链表>:: = ON <DMDPC_存储位置> {,<DMDPC_存储位置>}
<DMDPC_存储选项链表> ::= <DMDPC_存储选项>{,<DMDPC_存储选项>}
<DMDPC_存储选项> ::= <DMDPC_HASHMAP选项> | <DMDPC_存储位置链表选项>
<DMDPC_HASHMAP选项> ::= HASHPARTMAP ( <HASHPARTMAP定位方式> )
<HASHPARTMAP定位方式>::= 0|1|2
<DMDPC_存储位置链表选项> ::= ON (<DMDPC_存储位置> {,<DMDPC_存储位置>})
<DMDPC_存储位置>::= <表空间名>|<表空间组名>
其他语法请参见《DM8 SQL语言使用手册》3.6.1.4节说明
```

\<DMDPC_STORAGE 子句>用于指定子表的存储位置。

\<DMDPC_存储位置子句> 用于指定主表或子表的存储位置。分区组创建时，如果未指定子表的存储位置，子表会从父层主表处继承；如果主表、子表都未指定，系统会从集群可用的 BP 实例中随机挑选。如果\<DMDPC_存储位置子句>没有指定分区组中分区的存储表空间时，系统会在保证存储负载均衡的前提下优先考虑混合表空间。即当已有的混合表空间所在的 BP 不足以覆盖集群中大部分的 BP 实例时，系统会添加未被覆盖的 BP 实例中的普通表空间。除非确信分区组不会用于创建 HUGE 分区表，否则建议用户在分区组定义脚本中显式指定混合表空间的存储方式。

\<STORAGE HASH 子句>中指定的\<DMDPC_存储位置>数量少于分区个数时，可以按照从前到后的顺序，循环使用现有的\<DMDPC_存储位置>。

\<分区列类型>必须满足已有的分区类型约束，详见《DM8 SQL 语言使用手册》中对“分区列类型”的描述。

\<HASHPARTMAP 定位方式 > 用于指定哈希分区表的数据定位方式。取值 0 表示采用 DM 原有的数据定位方式；取值 1 表示采用新数据定位方式，此时可以添加分区，默认取值 1；取值 2 为兼顾数据分布均衡和允许添加分区的数据定位方式。当分区表根表为 RANGE 或 LIST 分区表时也可以指定 \<HASHPARTMAP 定位方式 >，用来影响下层 HASH 分区子表。

例 1 创建一个分区列类型是整型的范围分区组 PG1，指定每个分区的存储表空间。

```
create partition group PG1 partition by range(int)
(
partition p1 values less than(10) storage (on TS_2),
partition p2 values less than(40) storage (on TS_1),
partition p3 values less than(80) storage (on TS_2)) ;
```

例 2 创建分区列类型是 varchar(100)的 HASH 分区组，32 个子表存储位置均匀分布在 TS_1，TS_2 上。

```
create partition group PG2 partition by hash(varchar(100)) partitions 32 storage (on TS_1,TS_2);
```

例 3 使用\<STORAGE HASH 子句>创建一个 HASH 分区组，3 个子表存储位置分别分布在 TS_1、TS_2、TS_3 上。

```
create partition group PG3 partition by hash(varchar(1)) partitions 3 store in (TS_1,TS_2,TS_3);
```

例 4 创建一个二级分区组，两级分区方式都为范围分区。

```
create partition group PG4
partition by range(int)
subpartition by range(int) 
(
partition  p1 values less than(0) storage(on TS_1) (subpartition p11 values less than(1), subpartition p12 values less than(10)) ,
partition  p2 values less than(10) (subpartition p21 values less than(0) storage (on TS_2), subpartition p22 values less than(10)) ,
partition  p3 values less than(100) (subpartition p31 values less than(50) , subpartition p32 values less than(60)) 
) storage (on TS_1);
```

例 5 创建一个二级分区组，一级分区为 HASH，二级分区为 RANGE。所有叶子表的存储位置将从 TS_1，TS_2 中挑选。

```
create partition group PG5
partition by hash(int)
subpartition by range(int) 
(
partition  "part_1"   (subpartition p11 values less than(1), subpartition p12 values less than(10)) ,
partition  "part_2"   (subpartition p21 values less than(0) , subpartition p22 values less than(10)) ,
partition  "part_3"   (subpartition p31 values less than(50) , subpartition p32 values less than(60)) 
) storage (on TS_1,TS_2);
```

使用分区组 PG5 创建一个分区表 T_XQ64。

```
create table T_XQ64 (c int, c2 int, c3 int) using partition group PG5 by (c)
SUBPARTITION by (c3);
```

查看每个子表的存储位置。

```
SQL>select(select x.name from sysobjects x where x.id = y.pid) tname, groupid as ts_id, (select name from dpc_tablespace where ts_id = groupid) as ts_name from sysobjects y, sysindexes z where z.id = y.id and pid in (select id from sysobjects where name like 'T_XQ64%' and subtype$='UTAB' and name not like '%AUX') and subtype$='INDEX' and schid = (select id from sysobjects where type$='SCH' and name = 'SYSDBA') order by tname asc;
//查询结果如下：
行号     TNAME                TS_ID       TS_NAME
---------- ----------------- ----------- -------
1          T_XQ64              7           TS_1
2          T_XQ64_part_1      8           TS_2
3          T_XQ64_part_1_P11 7           TS_1
4          T_XQ64_part_1_P12 8           TS_2
5          T_XQ64_part_2      7           TS_1
6          T_XQ64_part_2_P21 7           TS_1
7          T_XQ64_part_2_P22 8           TS_2
8          T_XQ64_part_3      8           TS_2
9          T_XQ64_part_3_P31 7           TS_1
```

#### 8.6.2.3 查看分区组

查看分区组定义有两种方式：一是通过 PARTGROUPDEF()函数查看；二是通过 SYSOBJECTS 和 SYSTEXTS 相互配合查看。

一 通过 PARTGROUPDEF()函数查看。

PARTITION GROUP 作为一个模式对象，可以通过 PARTGROUPDEF 查看定义信息。

```
select PARTGROUPDEF('SYSDBA','PG2');
//查询结果如下：
行号 PARTGROUPDEF('SYSDBA','PG2')
----------------------------------------------------------------------------
1 CREATE PARTITION GROUP PG2 PARTITION BY HASH(VARCHAR (100)) PARTITIONS 32
STORAGE (ON TS_1,TS_2);
```

二 通过 SYSOBJECTS 和 SYSTEXTS 相互配合查看。

首先，通过查询 SYSOBJECTS 获取当前系统中的所有 PARTITION GROUP 信息。得到 PG1 的 ID 为 3283。

```
select * from sysobjects where subtype$='PGRP';
SQL> select * from sysobjects where subtype$='PGRP';
//查询结果如下：
行号 NAME ID SCHID TYPE$ SUBTYPE$ PID VERSION CRTDATE INFO1 INFO2 INFO3 INFO4
INFO5 INFO6 INFO7 INFO8 VALID
-------------------- ---------- ----- -------------------- ---------- -----
1 PG1 3283 150994945 SCHOBJ PGRP NULL 0 2021-05-07 13:54:41.000000
0 NULL NULL NULL NULL NULL NULL NULL Y
2 PG2 3284 150994945 SCHOBJ PGRP NULL 0 2021-05-07 13:54:49.000000
0 NULL NULL NULL NULL NULL NULL NULL Y
```

然后，查询 SYSTEXTS 系统表，得到 PG1 分区组定义。

```
SQL> select * from systexts where id =3283;
//查询结果如下：
行号 ID SEQNO TXT
----------------------------------------------------------------------------
1 3283 0 CREATE PARTITION GROUP PG1 PARTITION BY RANGE( INT )( PARTITION P1
VALUES LESS THAN(10) STORAGE( ON TS_2), PARTITION P2 VALUES LESS THAN(40)
STORAGE( ON TS_1), PARTITION P3 VALUES LESS THAN(80) STORAGE( ON TS_2))
```

用户在创建表对象时应该优先考虑借助分区组，选择最佳分区列和分区方式，将分区方式定义成分区组，这会为多表查询和事务处理带来性能提升。

#### 8.6.2.4 使用分区组创建分区表

使用分区组创建表时，分区列的个数和数据类型必须和分区组中定义保持完全一致。

语法如下：

```
CREATE TABLE <表名定义> (<列定义> {,<列定义>}[,<表级约束定义>{,<表级约束定义>}])<分区组子句> [<STORAGE子句>] [<ROW MOVEMENT子句>]
<表名定义> ::= [<模式名>.] <表名>
<分区组子句>::= USING PARTITION GROUP <分区组名> BY (<列名>{,<列名>})[SUBPARTITION BY (<列名>{,<列名>})]
<列定义>、<表级约束定义>和<STORAGE子句>::=参见《DM8 SQL语言使用手册》3.6.1.1节说明
<ROW MOVEMENT子句\>::=参见《DM8 SQL语言使用手册》3.6.1.4节说明
```

例 1 基于分区组 PG1 创建范围分区表。

```
create table DPC_T1(c int) using partition group PG1 by(c);
```

例 2 因为数据类型没有完全一致会在创建时报错。

```
create table DPC_T1_ERR(c bigint) using partition group PG1 by(c);
//查询结果报错：
[-3804]:列[C]类型与分区组要求的不一致
```

例 3 基于分区组 PG2 创建哈希分区。

```
create table DPC_T2(c varchar(100)) using partition group PG2 by(c);
```

例 4 基于分区组 PG5 创建二级分区。

```
create table DPC_T3 (c int, c2 int, c3 int) using partition group PG5 by (c)
SUBPARTITION by (c3);
```

例 5 使用分区 PG6 创建分区表，同时指定表空间。

```
create table DPC_T4(c varchar(1)) using partition group PG6 by(c) STORAGE (on
ts_01);
```

#### 8.6.2.5 删除分区组

语法如下：

```
DROP PARTITION GROUP <分区组名> [FORCE]
```

分区组被使用后无法直接删除，此时可指定 FORCE 选项强制删除分区组，基于该分区组创建的表对象不会被删除，但是表的 DDL 属性会发生变化，不再依附于分区组。

例 PG1 被表 DPC_T1 依赖，直接删除时失败。

```
drop partition group PG1;
//查询结果报错：
[-3805]:分区组被依赖，不能被删除.
```

查看表 DPC_T1 定义，发现 PG1 被表 DPC_T1 依赖，所以无法删除。

```
select tabledef('SYSDBA', 'DPC_T1');
//查询结果如下：
行号 TABLEDEF('SYSDBA','DPC_T1')
----------------------------------------------------------------------------
1 CREATE TABLE "SYSDBA"."DPC_T1"
("C" INT)
USING PARTITION GROUP "SYSDBA"."PG1" BY ("C");
```

强制删除分区组 PG1 后，再次查看表定义，表 DPC_T1 不再依赖 PG1。

```
SQL> drop partition group PG1 force;
SQL> select tabledef('SYSDBA','DPC_T1');
//查询结果如下：
行号 TABLEDEF('SYSDBA','DPC_T1')
----------------------------------------------------------------------------
1 CREATE TABLE "SYSDBA"."DPC_T1"
(
"C" INT)
PARTITION BY RANGE("C")
(
PARTITION "P1" VALUES LESS THAN(10) STORAGE(ON "TS_2", CLUSTERBTR) ,
PARTITION "P2" VALUES LESS THAN(40) STORAGE(ON "TS_1", CLUSTERBTR) ,
PARTITION "P3" VALUES LESS THAN(80) STORAGE(ON "TS_2", CLUSTERBTR)
) STORAGE(ON "TS_1", CLUSTERBTR) ;
```

#### 8.6.2.6 使用分区组的好处

分区组是一个逻辑概念，将相同的分区和分布选项定义为一个模板，然后基于这个模板创建分区表。它的引入能带来如下好处：

1.简化用户创建水平分区表的操作。

假设在一个应用中有多张表的分区方式一样，如果没有分区组，那么用户必须为每一张表反复指定相同的分区方式和存储表空间。

例 下面用为使用分区组和使用了分区组的两种情况进行对比，来说明分区组的更加简洁的操作性。

  * 未使用分区组的建表情况



```
create table M5_A(c1 int not null, c2 char(1000)) partition by range(c1) 
(
 partition p1 values less than(10) storage (on TS_1), 
 partition p2 values less than(40) storage (on TS_2),
 partition p3 values less than(80) storage (on TS_3)) ;
create table M5_B(c1 int not null, c2 char(1000)) partition by range(c1) 
(
 partition p1 values less than(10) storage (on TS_1), 
 partition p2 values less than(40) storage (on TS_2),
 partition p3 values less than(80) storage (on TS_3)) ;
```

  * 使用分区组的建表情况



但是创建分区组 PG1 后，创建分区表的时候，只要指定分区组即可。

```
create partition group PG1 partition by range(int) 
(
 partition p1 values less than(10) storage (on TS_1), 
 partition p2 values less than(40) storage (on TS_2),
 partition p3 values less than(80) storage (on TS_3)) ;
```

使用分区组 PG1 进行建表。大大简化了创建分区表的方式。

```
create table M5_A(c1 int not null, c2 char(1000)) using partition group PG1 by(c1);
create table M5_B(c1 int not null, c2 char(1000)) using partition group PG1 by(c1);
```

2.确保相同分区方式两张表的对应分区始终在同一 BP，减少跨机事务和连接时数据交换开销，提升事务处理的性能。

连接中一种常见的优化：分区智能连接（ Partition Wise Join ，简称为 PWJ），即当连接两侧不需要任何数据交换操作而可以直接在每个工作线程里进行连接。PWJ 优化的使用前提是两边分区方式一致且对应分区的 BP 位置也一致。借助于分区组，优化器可以直接判断出两张表连接时是否适用于 PWJ，否则优化器需要在比较完两张表的分区方式后，遍历表的每一个分区，逐一判断是否存储的 BP 一致。

## 8.7 数据迁移

DMDPC 进行节点扩容和缩容（增删节点）之后，为了能够充分利用系统内的节点资源，用户需要手动执行数据迁移，将数据在节点间进行重新均衡分布，以提高系统的资源利用率和整体性能。

DMDPC 提供库迁移、表迁移和表空间迁移三种方案。推荐用户使用表空间迁移。原因如下：

库级迁移是先将单个节点库停机，再将节点上的库拷贝到另外的节点上。库级迁移缺点是需要停机，对系统业务影响比较大。

表级迁移是指将单个表或者分区表的单个分区从一个表空间迁移另一个表空间。表级迁移缺点是粒度比较小，当存在大量表需要迁移时比较繁琐，且在表迁移期间不支持对表的读写操作，影响用户业务。

表空间迁移是将整个表空间从一个 RAFT 组迁移到另外一个 RAFT 组。表空间迁移分为联机和脱机两种方式。相比于库迁移和表迁移，表空间联机迁移效率更高，对系统正常运行影响更小。因此，表空间联机迁移是 DMDPC 系统用于实现数据均衡的最常用、最重要的技术手段。

### 8.7.1 表空间迁移

在 DM 数据库中，表空间由一个或者多个数据文件组成。DM 数据库中的所有对象在逻辑上都存放在表空间中，而物理上都存储在所属表空间的数据文件中。

DMDPC 支持在 BP 上创建用户表空间，BP 和表空间的关系为一对多的关系，即一个 BP 上可以创建多个表空间，但一个表空间只能创建在一个 BP 上。一个分区表的多个子表可以指定分布在不同的表空间上，从而将数据分布到多个 BP 上进行均衡存储。传统的单表只能指定一个表空间存储，因此无法分布到多个 BP 上。在一个 DMDPC 系统中，每个 BP 节点上必须至少创建一个表空间才会被用来存储用户数据，因此 BP 节点扩容后需要在其上创建表空间。

节点扩容或缩容后，整个系统中原有的数据均衡被打破。此时，我们只需要将分区表的部分分区所属的表空间迁移到新增 BP 节点上，将分区数据迁移到新增 BP 节点的目的，从而使系统数据再次分布均衡。

按照表空间为最小单位进行数据迁移的方法，和传统意义上的按照表对象的分布方式，根据表中每一行数据重新计算目标节点进行重分布的方式有着本质上的区别。表空间迁移可以同时对多个存储在该表空间上的表进行均衡。

表空间的迁移分为联机和脱机两种方式。联机方式不用停机即可操作，推荐用户使用联机方式迁移。

DMDPC 环境支持表空间迁移，但不允许将表空间向存在故障节点的 RAFT 组迁移。

#### 8.7.1.1 联机方式

表空间联机迁移为在线进行，不需要停机。利用修改表空间 SQL，一键式拷贝表空间物理文件，且迁移期间允许用户读写操作，并将期间写操作新产生的日志不断进行重演达到数据同步，平滑实现表空间的跨 BP 节点转移，达到数据的均衡分布。

利用修改表空间语法实现联机表空间移动。目前 DMDPC 仅支持部分表空间修改语法。

语法如下：

```
ALTER TABLESPACE <表空间名> [ONLINE｜OFFLINE|<增加数据文件子句>|<修改文件大小子句>|<修改文件自动扩展子句>|<表空间移动子句>]
<表空间移动子句> ::= MOVE TO <RAFT组名> [[NOT] READ ONLY]
其他语法请参考《DM8_SQL语言使用手册》中管理表空间章节
```

ONLINE｜OFFLINE 表示表空间的状态。ONLINE 为联机状态，ONLINE 时才允许用户访问该表空间中的数据；OFFLINE 为脱机状态，OFFLINE 时不允许访问该表空间中的数据。副本架构的 DMDPC 不支持 OFFLINE，非副本架构支持 OFFLINE。

READ ONLY 只读和 NOT READ ONLY 读写方式区别：只读方式在表空间迁移的过程中，用户只能对表空间包含的表进行读操作，不能进行写操作。读写方式在迁移的过程中，用户大部分时间，比如在文件拷贝的过程中仍然能对表空间进行写操作，只在最后切换节点的短时间内写操作会被阻塞。读写方式对迁移过程中新修改的数据，通过重演日志的方式完成修改，这样对系统的影响更小，对用户更友好。如果不指定读写的属性，默认为只读方式。

例 1 使用 READ ONLY 只读方式，将表空间 TS_01 迁移到 RAFT_2 上。

```
alter tablespace ts_01 move to raft_2;
alter tablespace ts_01 move to raft_2 read only;
```

例 2 使用 NOT READ ONLY 读写方式，将表空间 TS_01 迁移到 RAFT_2 上。

```
alter tablespace ts_01 move to raft_2 not read only;
```

#### 8.7.1.2 脱机方式

所有节点正常退出的情况下，可以直接手动拷贝表空间文件到目标 BP 上，然后通过辅助步骤修改字典、控制文件以及更新系统表。

步骤如下：

  1. 用户确保需要移动的表空间所在 BP 是正常退出的。
  2. 手动拷贝源表空间所有文件到目标 RAFT 的库目录中。
  3. 利用 dmctlcvt 工具，转换源库 dm.ctl->dm.txt。首先将 dm.txt 中的表空间项（TS_ID、TS_NAME）另存在他处备用；其次将 TS_ID、TS_NAME 从 dm.txt 中删除后保存；再次将删除后的 dm.txt 转换为新的 dm.ctl，并替换掉源库 dm.ctl。
  4. 利用 dmctlcvt 工具，转换目标库 dm.ctl->dm.txt 得到对应表空间的文本，添加步骤 3 中另存的表空间项（TS_ID、TS_NAME），再将 dm.txt 转换为 dm.ctl 并替换掉目标库 dm.ctl。
  5. 单独启动 MP，直连 MP 执行 SP_DPC_MOVE_TS_OFFLINE(TS_NAME, TO_RAFT_NAME)。
  6. 比较目标库和源库的联机日志 CUR LSN 大小，确保目标库联机日志 CUR LSN 更大。如果目标库 CUR LSN 大则跳过此步骤。否则将目标库的 CUR LSN 修改为源库 CUR LSN+1。
  7. 启动所有节点，验证迁移后数据是否正确。



例 表空间 TS1 从 RAFT_1 脱机方式迁移到 RAFT_2 的全过程。假设 RAFT_1 存在 BP1_A 节点上，RAFT_2 存在 BP2_A 节点上。

  1. 确保需要移动的表空间所在 BP 是正常退出的。
  2. 手动拷贝源库 RAFT_1 中表空间 TS1 的所有数据文件到目标库 RAFT_2 的库目录中。
  3. 使用 dmctlcvt 工具转换 dm.ctl 为文本文件 dm.txt。



```
dmctlcvt.exe type=1 src=D:\data\dmdpc_dem_new\dmdpc\BP1_A\DAMENG\dm.ctl dest=D:\data\dmdpc_dem_new\dmdpc\BP1_A\DAMENG\dm.txt
```

首先，将 dm.txt 中的表空间项（TS_ID、TS_NAME 等内容）（下面内容）另存在它处备用。其次，将 TS_ID、TS_NAME 从 dm.txt 中删除后保存。另外需要记录一下 next_ts_id=16，如果目标库 next_ts_id 小于 16，则修改目标库中的 next_ts_id 值为 16。

```
#===============================================
# table space name
ts_name=TS1
 # table space ID
ts_id=7
# table space status
ts_state=0
# table space cache
ts_cache=NORMAL
# DSC node number
ts_nth=0
# DSC optimized node number
ts_opt_node=0
# table space create time
ts_create_time=DATETIME '2022-2-22 14:26:36'
# table space modify time
ts_modify_time=DATETIME '2022-2-22 14:26:36'
# table space encrypt flag
ts_encrypt_flag=0
# table space copy num
ts_copy_num=0
# table space region size flag
ts_size_flag=0
# table space region huge size flag
ts_huge_size_flag=0

#-----------------------------------------------

# file path
fil_path=D:\data\dmdpc_dem_new\dmdpc\BP1_A\DAMENG\ts1.dbf
# mirror path
mirror_path=
# file id
fil_id=0
# whether the file is auto extend
autoextend=1
# file create time
fil_create_time=DATETIME '2022-2-22 14:26:36'
# file modify time
fil_modify_time=DATETIME '2022-2-22 14:26:36'
# the max size of file
fil_max_size=0
# next size of file
fil_next_size=0
```

再次，将删除后的 dm.txt 转换为新的 dm.ctl，并替换掉源库 dm.ctl。

```
dmctlcvt.exe type=2 src=D:\data\dmdpc_dem_new\dmdpc\BP1_A\DAMENG\dm.txt dest=D:\data\dmdpc_dem_new\dmdpc\BP1_A\\DAMENG\dm.ctl
```

  4. 将目标库 dm.ctl 转换为文本文件 dm.txt。



```
dmctlcvt.exe type=1 src=D:\data\dmdpc_dem_new\dmdpc\BP2_A\DAMENG\dm.ctl dest=D:\data\dmdpc_dem_new\dmdpc\BP2_A\DAMENG\dm.txt
```

首先，在目标库的 dm.txt 中添加步骤 3 中另存的表空间项（TS_ID、TS_NAME），注意需要修改 fil_path 路径为目标库路径，另外根据需要修改 next_ts_id 的值。

```
#===============================================
# table space name
ts_name=TS1
 # table space ID
ts_id=7
# table space status
ts_state=0
# table space cache
ts_cache=NORMAL
# DSC node number
ts_nth=0
# DSC optimized node number
ts_opt_node=0
# table space create time
ts_create_time=DATETIME '2022-2-22 14:26:36'
# table space modify time
ts_modify_time=DATETIME '2022-2-22 14:26:36'
# table space encrypt flag
ts_encrypt_flag=0
# table space copy num
ts_copy_num=0
# table space region size flag
ts_size_flag=0
# table space region huge size flag
ts_huge_size_flag=0

#-----------------------------------------------

# file path
fil_path=D:\data\dmdpc_dem_new\dmdpc\BP2_A\DAMENG\ts1.dbf
# mirror path
mirror_path=
# file id
fil_id=0
# whether the file is auto extend
autoextend=1
# file create time
fil_create_time=DATETIME '2022-2-22 14:26:36'
# file modify time
fil_modify_time=DATETIME '2022-2-22 14:26:36'
# the max size of file
fil_max_size=0
# next size of file
fil_next_size=0
```

其次，再将 dm.txt 转换为 dm.ctl 并替换掉目标库 dm.ctl。

```
dmctlcvt.exe type=2 src=D:\data\dmdpc_dem_new\dmdpc\BP2_A\DAMENG\dm.txt dest=D:\data\dmdpc_dem_new\dmdpc\BP2_A\DAMENG\dm.ctl
```

  5. 单独启动 MP，直连 MP 执行 SP_DPC_MOVE_TS_OFFLINE 过程。



```
SP_DPC_MOVE_TS_OFFLINE('TS1','RAFT_2');
```

  6. 比较目标库和源库的联机日志 CUR LSN 大小，确保目标库联机日志 CUR LSN 更大。



首先，用 dmmdf 查看源库的联机日志中的 CUR LSN(取 CLSN 和 C_LSN 中的最大值)，此处源库为 BP1_A。

```
dmmdf TYPE=2 FILE=D:\data\dmdpc_dem_new\dmdpc\BP1_A\DAMENG\DAMENG01.log
```

记录下该值，例如为 621563。

用 dmmdf 查看目标库的联机日志中的 CUR LSN(取 CLSN 和 C_LSN 中的最大值)，此处为 BP2。

```
dmmdf TYPE=2 FILE=D:\data\dmdpc_dem_new\dmdpc\BP2_A\DAMENG\DAMENG01.log
```

记录下该值，例如为 620000。可见，620000 小于 621563，因此需要将目标库的 CUR LSN 修改为大于源库的 CUR LSN。

温馨提示：如果目标库 BP2_A 是新初始化的库且未启动过，在步骤 12 前需要先以 OPEN 状态正常启动 BP2_A。

其次，以 MOUNT 方式启动目标库，修改目标库的 CUR LSN 大于源库的联机日志中的 CUR LSN。修改为源库的 CUR LSN+1。

```
SP_ADJUST_CUR_LSN(621564);
```

如果目标库 BP2_A 的 CUR LSN 本来就大于源库的 CUR LSN，则跳过这一步。

再次，再将目标库 ALTER 为 OPEN 模式。

```
alter database open;
```

最后，正常退出目标库 BP2_A、MP。

  7. 重新依次启动 MP、BP、SP，脱机方式迁移表空间过程结束。验证迁移后数据是否正确。



### 8.7.2 表迁移

表迁移分为两种情况：一是基于分区的迁移，专门用于分区子表；二是基于分区组的迁移，专门用于整表迁移。

#### 8.7.2.1 基于分区的迁移

基于分区的迁移方式将分区表中的一个子分区从一个表空间迁移到另一个表空间。如果使用基于分区的迁移方式，创建表时不允许使用分区组，并且分区迁移方式不支持整表迁移。

语法如下：

```
Alter TABLE [<模式名>.]<表名> MOVE PARTITION <分区名> TO <表空间名> [FAST]
```

\<表名>分区表的主表名。只有未使用分区组创建的主表才支持使用基于分区的迁移。

\<分区名>待迁移分区的名字。

\<表空间名>目标表空间名。必须是实际存在的表空间名。普通表可以迁移到普通表空间或混合表空间，HUGE 表只能迁移到混合表空间。

[FAST]是否使用快速方式迁移，缺省使用普通方式迁移。FAST 方式通过数据的转换和数据的封装直接对 B 树进行操作，省去了普通插入方式下各个操作符之间的跳转，提升了迁移的效率，但对于约束的检查等由用户保证，系统将不处理有约束冲突的数据。普通方式迁移选择查询插入方式迁移数据，可以保证数据的正确性和约束的有效性，效率比前者要低。

例 将 test22 表的 PAR5 子分区从表空间 TS_1 快速迁移到表空间 TS_2，然后再迁移到 TS_1 表空间。

```
//数据准备
create table test22(c1 varchar(100),c2 float,c3 double, c4 int, c5 bigint , c6 int)
partition by range(c4, c5, c6) (
PARTITION PAR1 values less than(10,10, 10) storage(on TS_1),
PARTITION PAR2 values equ or less than(10,10, 100) storage(on TS_2),
PARTITION PAR3 values less than(10,10,170) storage(on TS_1),
PARTITION PAR4 values less than(10,10, 330) storage(on TS_2),
partition par5 values less than(maxvalue,maxvalue,maxvalue) storage(on TS_1));
insert into test22 select level,level,level,level,level,level from dual connect by level < 1000;
commit;
//将PAR5子分区从表空间TS_1快速迁移到表空间TS_2
alter table test22 move partition PAR5 to TS_2 FAST;
//将PAR5子分区从表空间TS_2迁移到表空间TS_1
alter table test22 move partition PAR5 to TS_1;
```

#### 8.7.2.2 基于分区组的迁移

基于分区组的迁移方式是将整个分区表从一个分区组迁移到另外一个分区组。通过改变分区组的方式达到改变 RAFT 组的目的。基于分区组方式的迁移仅支持整表迁移。

语法如下：

```
Alter TABLE [<模式名>.]<表名> MOVE TO <pg名> [FAST]
```

\<表名>待迁移分区表的主表名。必须是基于分区组创建的分区表才支持使用基于分区组的迁移。

\<pg 名>目标分区组的名字。原表的分区组与目标\<pg 名>除了存储位置不同，其他（分区方式，分区个数等）都必须相同。

[FAST]是否使用快速方式迁移，缺省使用普通方式迁移。

例 将 test1 表从 PG1 快速迁移到 PG2 分区。

```
//数据准备
create partition group pg1 partition by hash(BIGINT) partitions 8 storage (on TS_1,TS_2);
create partition group pg2 partition by hash(BIGINT) partitions 8 storage (on TS_2,TS_1);
create table test1(c1 varchar(100),c2 float,c3 double, c4 int, c5 bigint , c6 int) using partition group pg1  by (c5);
insert into test1 select level,level,level,level,level,level from dual connect by level < 10000;
commit;
//将test1表从当前组（PG1）快速迁移到PG2组
alter table test1 move to pg2 fast;
//将test1表从当前组（PG2）迁移到PG1组
alter table test1 move to pg1;
```

### 8.7.3 库迁移

所有节点正常退出的情况下，可以直接手动拷贝整个数据库到目标 BP 上，然后通过辅助步骤修改字典、控制文件以及更新系统表。

因为停机会影响到正常的业务，所以不推荐用户使用此种方式。

## 8.8 备份与还原

### 8.8.1 概述

DMDPC 集群中，SP 因为不存储数据，不需要备份与还原。需要备份与还原的节点为 MP 和 BP 节点。如果 MP 或 BP 为多副本集群，则参与备份的只有主节点，参加还原的为所有 MP 和 BP 节点。

#### 8.8.1.1 完整的备份与还原流程

目前，DMDPC 仅支持单库、整个集群和归档的备份与还原。

针对不同的备份对象，一个完整的备份与还原过程包含的步骤不同。下面分别进行介绍。

##### 8.8.1.1.1 数据备份与还原

###### 8.8.1.1.1.1 单库

一个完整的单库的备份与还原过程包括：备份—还原。

● 备份

备份时在联机备份和脱机备份中二选一即可。

单库的联机备份专用于 MP 和 BP 节点。如果是单副本则直连 MP 或 BP 执行；如果是多副本只需直连主 MP 或主 BP 执行。

单库的脱机备份，可用于 MP 或 BP 节点。需要使用 DMRMAN 工具对某一个 BP 或 MP 节点单独执行。

表8.1 单库的备份操作

|**备份粒度**|**状态**|**备份（二选一）**|
|-------|-------|-------|
|单库（MP、BP）|联机|√|
|单库（MP、BP）|脱机|√|


● 还原

还原只支持脱机还原。既可使用联机备份集也可使用脱机备份集。需要使用 DMRMAN 工具对所有的 BP 或 MP 节点分别单独执行。为了保证数据一致性，多副本中的所有节点使用同一份备份集。

还原分为数据还原和数据恢复两步。数据恢复又细分为恢复一致性和更新 DB_MAGIC 两步。

表8.2 单库的还原操作

![表 8.2 单库的还原操作.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202304191629113KKCFM11WB6WLDGKE5)

###### 8.8.1.1.1.2 整个集群

一个完整的整个集群的备份与还原过程包括：备份—还原。

● 备份

备份时在联机备份和脱机备份中二选一即可。

整个集群的联机备份，只需连接一个 SP 执行联机备份语句，即可完成整个集群中的所有 MP 和 BP 节点的备份。整个集群的脱机备份，对于单副本集群，需要使用 DMRMAN 工具对所有 BP 和 MP 节点分别单独执行备份，对于多副本集群，仅需要备份每个 RAFT 组中的主节点。

表8.3 整个集群的备份操作

|**备份粒度**|**状态**|**备份（二选一）**|
|-------|-------|-------|
|整个集群|联机|√|
|整个集群|脱机|√|


● 还原

整个集群的还原只支持脱机方式。既可使用联机备份集也可使用脱机备份集。需要使用 DMRMAN 工具对所有 BP 和 MP 节点分别单独执行还原。为了保证数据一致性，多副本中的所有节点使用同一份备份集。

还原分为数据还原和数据恢复两步。数据恢复又细分为恢复一致性、更新 DPC_MAGIC、更新 DB_MAGIC 三步。

表8.4 整个集群的还原操作

![表 8.4 整个集群的还原操作.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202304191630567DS1MQ5JC8E4BATPWF)

##### 8.8.1.1.2 归档备份与还原

归档备份与还原过程包括：备份—还原。

归档备份时，用户可根据实际需要选择联机或脱机执行，二选一即可。归档还原只支持脱机还原。

#### 8.8.1.2 魔数介绍

为了能够唯一标识一个 DMDPC 集群以及 DMDPC 集群中同一个批次的备份集，特意为 DMDPC 增加了 DPC_MAGIC 和 BAK_MAGIC 两个魔数。具体含义说明如下：

  * **DPC_MAGIC**  
DMDPC 集群魔数，用于唯一标识一个 DMDPC 集群，同一个 DMDPC 集群内的 MP 和 BP 节点拥有相同的 DPC_MAGIC
值。DPC_MAGIC 在 MP 初始化时生成。BP 初始化完成后，首次启动时向 MP 获取 DPC_MAGIC 值并写入 BP 本地。

  * **BAK_MAGIC**  
备份集魔数，用于唯一标识 DMDPC 集群内同一批次的 MP 和 BP 的备份集。BAK_MAGIC 在对 DMDPC
节点执行脱机备份时由用户指定，并需要确保同一批次的备份集 BAK_MAGIC 相同，以便区分不同批次的备份集，避免备份集出现混用，脱机备份未指定时
BAK_MAGIC 为 0。而在连接 SP 执行集群联机备份时，BAK_MAGIC 由 MP 自动生成并自动同步给所有 BP 节点，无需用户指定。可通过
DMRMAN 工具的 SHOW BACKUPSET 命令查看备份集的 BAK_MAGIC。使用备份集进行数据还原和数据恢复时指定的 BAK_MAGIC
需要与备份集的 BAK_MAGIC 一致，若备份集中 BAK_MAGIC 为 0，数据还原和数据恢复时可以不指定 BAK_MAGIC。



### 8.8.2 脱机备份

目前，DMDPC 集群仅支持脱机库级备份与脱机归档备份。下面分别进行介绍。

#### 8.8.2.1 库级备份

脱机库级备份分为完全备份和增量备份两种。

##### 8.8.2.1.1 完全备份

通过 DMRMAN 工具对 DMDPC 集群执行脱机库级完全备份。

由于 MP 和 BP 节点上都存放有数据，因此需要分别对 MP 和 BP 执行备份操作并生成相应的备份集。对于单副本架构的 DPC 集群，如果存在多个 BP 节点，则需要备份 MP 节点和所有的 BP 节点。对于多副本架构的 DPC 集群，仅需要备份每个 RAFT 组中的主节点（主 MP、主 BP），从节点不支持备份。开始执行 DMDPC 集群的脱机备份时，一定要确保 MP 和所有 BP 节点已经退出，否则无法保证还原出来的 MP 和 BP 节点的数据一致性。

对于脱机备份，MP 和 BP 备份集的 BAK_MAGIC 由用户指定并确保唯一，对同一次备份操作，MP 和 BP 需要指定成相同的 BAK_MAGIC 值。建议用户指定 BAK_MAGIC，因为如果不指定，则备份集上的 BAK_MAGIC 为 0，还原时有可能会出现备份集混淆的情况。

使用 BAK_MAGIC 示例如下：

备份 MP：

```
RMAN>BACKUP DATABASE 'E:\dpc\mp\DAMENG\dm.ini' BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back' USE BAK_MAGIC 132256;
```

备份 BP：

```
RMAN>BACKUP DATABASE 'E:\dpc\bp\DAMENG\dm.ini' BACKUPSET 'E:\dpc\bp\DAMENG\bak\bp_back' USE BAK_MAGIC 132256;
```

对于非多副本架构的 DPC 集群备份（含 1 个 MP 节点，2 个 BP 节点，1 个 SP 节点）备份，示例如下：

备份 MP：

```
RMAN>BACKUP DATABASE 'E:\dpc\mp\DAMENG\dm.ini' BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back' USE BAK_MAGIC 132256;
```

备份 BP1：

```
RMAN>BACKUP DATABASE 'E:\dpc\bp1\DAMENG\dm.ini' BACKUPSET 'E:\dpc\bp1\DAMENG\bak\bp1_back' USE BAK_MAGIC 132256;
```

备份 BP2：

```
RMAN>BACKUP DATABASE 'E:\dpc\bp2\DAMENG\dm.ini' BACKUPSET 'E:\dpc\bp2\DAMENG\bak\bp2_back' USE BAK_MAGIC 132256;
```

对于多副本架构的 DPC 集群（1MP，3 个同属一个 RAFT 组的 BP 节点，1 个 SP 节点）备份。

示例如下（MP 多副本类似，此处不再举例）：

备份 MP：

```
RMAN>BACKUP DATABASE 'E:\dpc\mp\DAMENG\dm.ini' BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back' USE BAK_MAGIC 132256;
```

找到主库 BP1，备份主 BP1 即可：

```
RMAN>BACKUP DATABASE 'E:\dpc\bp1\DAMENG\dm.ini' BACKUPSET 'E:\dpc\bp1\DAMENG\bak\bp1_back' USE BAK_MAGIC 132256;
```

##### 8.8.2.1.2 增量备份

通过 DMRMAN 工具对 DMDPC 集群执行脱机增量备份。执行增量备份时同样也需要指定 BAK_MAGIC，需要分别对 MP 和 BP 执行备份操作并生成相应的备份集。除需额外指定 BAK_MAGIC 以外与普通单节点的增量备份语法一致。

在执行增量备份时为了确保 DMDPC 集群各节点数据一致，需要注意以下事项：

  1. 执行增量备份时，需要保证 DMDPC 集群内各节点均正常退出。
  2. 对 MP 和 BP 执行同一批次的增量备份操作时，要求 MP 和所有 BP 节点都有可用的基备份集，且这些基备份集是在同一批次的备份操作中产生的（基备份集的 BAK_MAGIC 相同）。
  3. 对 MP 和 BP 执行同一批次的增量备份操作时，需要指定相同的 BAK_MAGIC 值。
  4. 执行不同批次的增量备份操作时，需要指定不同的 BAK_MAGIC 值，避免出现备份集混用的情况，由用户确保 BAK_MAGIC 的唯一性。



一次 DMDPC 集群的增量备份操作示例如下：

第一步，准备基备份集。若基备份集已存在，可以跳过这一步。

MP：

```
RMAN>BACKUP DATABASE 'E:\dpc\mp\DAMENG\dm.ini' BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back' USE BAK_MAGIC 132256;
```

BP：

```
RMAN>BACKUP DATABASE 'E:\dpc\bp\DAMENG\dm.ini' BACKUPSET 'E:\dpc\bp\DAMENG\bak\bp_back' USE BAK_MAGIC 132256;
```

在对集群执行一次增量备份时，各节点使用的基备份集需要确保是在同一批次的备份操作中生成的，所有基备份集的 BAK_MAGIC 相同，以确保还原后各节点数据一致。

第二步，在所有节点均有可用的同一批次的基备份集的情况下，开始执行脱机增量备份。

执行增量备份时，同样需要指定 BAK_MAGIC，同一批次的增量备份操作中，MP 和所有 BP 节点必须指定相同的 BAK_MAGIC 值。

注意：本示例中使用的是指定基备份搜索目录（WITH BACKUPDIR）的方式进行增量备份，需要用户确保在这些目录下可以搜索到最近一个批次的所有节点的基备份集，如果是通过指定基备份集（BASE ON BACKUPSET）的方式执行增量备份，则需要用户确保各节点指定的基备份集是同一个批次产生的（即所有基备份集的 BAK_MAGIC 相同）。

MP：

```
RMAN>BACKUP DATABASE 'E:\dpc\mp\DAMENG\dm.ini' INCREMENT WITH BACKUPDIR 'E:\dpc\mp\DAMENG\bak\mp_back' BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back_increment' USE BAK_MAGIC 112233;
```

BP：

```
RMAN>BACKUP DATABASE 'E:\dpc\bp\DAMENG\dm.ini' INCREMENT WITH BACKUPDIR 'E:\dpc\bp\DAMENG\bak\bp_back' BACKUPSET 'E:\dpc\bp\DAMENG\bak\bp_back_increment' USE BAK_MAGIC 112233;
```

#### 8.8.2.2 归档备份

通过 DMRMAN 工具对 DMDPC 集群执行脱机归档备份与普通单机归档备份流程一致。

由于 MP 和 BP 节点上都存放有数据，因此需要分别对 MP 和 BP 执行备份操作并生成相应的备份集。对于单副本架构的 DMDPC 集群，如果存在多个 BP 节点，则需要备份 MP 节点和所有的 BP 节点。对于多副本架构的 DMDPC 集群，仅需要备份每个 RAFT 组中的主节点（主 MP、主 BP），从节点不支持备份。

脱机归档备份时，无需指定 BAK_MAGIC。

### 8.8.3 联机备份

目前，DMDPC 集群仅支持联机库级备份与联机归档备份。联机库级备份分为完全备份和增量备份两种，暂不支持 DDL_CLONE 备份。

DMDPC 的联机备份语法和普通单机没有差别，只是在执行流程上增加了 SP、MP 和 BP 之间的协同处理。

只要连接任意一个 SP 执行备份操作，即可完成对整个 DMDPC 系统的联机备份。直连 MP 备份、直连 BP 备份是特殊情况下使用，下文会详细介绍。

#### 8.8.3.1 直连 SP 备份

##### 8.8.3.1.1 整个集群备份

###### 8.8.3.1.1.1 完全备份

直接在 SP 端执行联机备份语句，即可完成 MP 和所有 BP 节点的备份，执行成功后，会在 MP 和所有 BP 节点本地生成各自的备份集，SP 本地不会有备份集产生。如果是 DMDPC 多副本环境，则只有主 MP 和主 BP 会参与备份并在本地生成备份集。

各备份集上的 BAK_MAGIC 值由 MP 自动生成并同步至各 BP 节点，不需要再由用户指定，每执行一次联机备份，MP 会分配一个不同的 BAK_MAGIC，用于标识不同批次的联机备份集，还原时必须使用同一批次产生的 MP 和 BP 备份集。

为了保证 MP 和各 BP 节点备份下来的数据处于事务一致状态，在备份流程中会暂停 MP 的提交请求，这个暂停动作理论上会很快完成，但为了避免影响上层业务，可以考虑在系统空闲时执行联机备份。

联机库级备份的注意事项：

  1. 联机备份时，若指定了备份集名称，则在 MP 和 BP 端生成备份集时会在备份集名称之后各自添加实例名后缀，避免 MP 和 BP 部署在同一台机器上，在同一个路径下生成备份集时发生名称冲突。
  2. 联机备份开始前如果有 MP 或 BP 节点故障，则不允许执行备份。
  3. 联机备份过程中如果有 MP 或 BP 节点故障，则备份失败。
  4. 支持直接连接 MP 和 BP 节点执行联机备份，但是不建议使用。因为 DMDPC 下各节点的 LSN 值不统一，无法保证各节点事务一致，通过这种方式可能引发数据不一致的风险。
  5. 联机备份过程中如果有增删 BP 节点的动作（非副本节点增删），则新增节点不会参与备份，若备份过程中节点被删除，则会导致备份失败。



例 连接 SP 执行库级联机备份。

```
SQL>BACKUP DATABASE;
```

###### 8.8.3.1.1.2 增量备份

DMDPC 的联机增量备份（可支持差异增量备份和累积增量备份），需要遵守和普通单机增量备份相同的规则要求。例如：MP 和 BP 各节点本地必须已经存在有可用的基备份集，具体规则可参考《DM8 备份与还原》手册，此处不再详细说明。

直接在 SP 端执行联机增量备份语句，即可完成 MP 和所有 BP 节点的增量备份，执行成功后，会在 MP 和所有 BP 节点本地生成各自的增量备份集，SP 本地不会有增量备份集产生。如果是 DPC 多副本环境，则只有主 MP 和主 BP 会参与备份并在本地生成增量备份集。

同样的，增量备份集上也有 BAK_MAGIC 标识，由 MP 自动生成并同步至各 BP 节点。

每执行一次增量备份，MP 会分配一个不同的 BAK_MAGIC，用于标识不同批次的增量备份集，还原时必须使用同一批次产生的 MP 和 BP 的增量备份集。

为了保证 MP 和各 BP 节点备份下来的数据处于事务一致状态，在备份流程中会暂停 MP 的提交请求，这个暂停动作理论上会很快完成，但为了避免影响上层业务，可以考虑在系统空闲时执行联机增量备份。

执行联机增量备份时，不要求所有节点一定拥有同一批次的基备份集，只要能够生成同一批次的增量备份集，并使用同一批次的增量备份集对各节点执行还原，就一定能够将各节点恢复到事务一致状态。

联机完全备份的注意事项，对联机增量备份也同样适用，此处不再重复说明，具体请参考 [8.8.3.1.1.1 完全备份](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-cluster-management-use.html#8.8.3.1.1.1%20%E5%AE%8C%E5%85%A8%E5%A4%87%E4%BB%BD)。

执行增量备份集指定基备份集路径后，会在指定的基备份集路径上拼接每个节点自己的实例名作为真正的基备份集路径搜索基备份集。如指定了基备份集路径为'/home/backupset/dm_bak'，对于实例名为 MP_INSTANCE 的 MP 节点，会在'home/backupset/dm_bak_MP_INSTANCE'下搜素自己的基备份集。因此执行增量备份时，需要确保执行增量备份的节点指定的基备份路径下对应的基备份集均存在。

例 连接 SP 执行库级联机增量备份。

```
SQL>BACKUP DATABASE INCREMENT WITH BACKUPDIR '/home/backupset/dm_bak' BACKUPSET '/home/backupset/db_increment_bak';
```

##### 8.8.3.1.2 归档备份

DMDPC 集群归档联机备份执行方式同库级一样，也是直接在 SP 端执行联机备份语句，即可完成 MP 和所有 BP 节点的备份。执行成功后，会在 MP 和所有 BP 节点本地生成各自的备份集，SP 本地不会有备份集产生。如果是 DMDPC 多副本环境，则只有主 MP 和主 BP 会参与备份并在本地生成备份集。 与库级备份不同的是，归档备份集中不包含备份集上的 BAK_MAGIC 值。

联机归档备份的注意事项：

  1. 联机备份时，若指定了备份集名称，则在 MP 和 BP 端生成备份集时会在备份集名称之后各自添加实例名后缀，避免 MP 和 BP 部署在同一台机器上，在同一个路径下生成备份集时发生名称冲突。
  2. 联机备份开始前如果有 MP 或 BP 节点故障，则不允许执行备份。
  3. 联机备份过程中如果有 MP 或 BP 节点故障，则备份失败。
  4. 不允许直接连接 MP 和 BP 节点执行联机备份（无法保证各节点事务一致）。
  5. 联机备份过程中如果有增删 BP 节点的动作（非副本节点增删），则新增节点不会参与备份，若备份过程中节点被删除，则会导致备份失败。



例  连接 SP 执行归档备份。

```
SQL>BACKUP ARCHIVELOG;
```

#### 8.8.3.2 直连 MP 执行单库备份

专门用于 MP 多副本集群中。当 MP 多副本集群中的某一个非主 MP 节点故障且无法重新启动时，连接 SP 执行集群备份还原的开销较大，此时可通过直连主 MP 执行备份还原的方式来恢复故障的 MP 节点。具体操作分为：一直连主 MP 进行备份，获取备份集；二使用 DMRMAN 工具和备份集对故障 MP 进行还原；三重启故障 MP，此时集群内主 MP 会自动对故障 MP 恢复到数据一致状态。

![图 8.2 直连主 MP 执行备份还原.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230419163221B12N5SPVJ399LQFMQR)

图8.2 直连主MP执行备份还原

需要注意的是，单副本环境下，直连 MP 执行联机备份，然后进行还原操作，重启之后，因为没有多副本集群中的主节点自动恢复故障节点的数据一致性的功能，所以无法保证集群数据一致性。因此，单副本集群环境建议使用连接 SP 执行备份，用同一批备份集还原集群，才可保证数据的一致性。

例  直连主 MP 执行备份操作。

```
SQL>BACKUP DATABASE;
```

直连主 MP 执行备份生成的备份集中没有 BAK_MAGIC 信息，因此用这个备份集进行数据还原和数据恢复时也无需指定 BAK_MAGIC。

若需要将一套 DMDPC 集群的 MP 生成的备份集还原到另外一套 DMDPC 集群的 MP 节点上，则需要保证新 MP 节点的所有初始化参数与老 MP 节点完全相同。同时还原恢复后启动后，新 MP 的注册信息已被还原，因此还需要修改新 MP 中注册信息。

例 将 MP_1 还原到 MP_2 上后，修改 MP_2 上的注册信息。

```
//重命名MP_1为MP_2
SQL>SP_RENAME_DPC_INSTANCE('MP_1','MP_2');
//更新MP_2端口信息
SQL>SP_MODIFY_DPC_INSTANCE('MP_2','192.168.1.68','192.168.1.68', 6004,5239);
```

#### 8.8.3.3 直连 BP 执行单库备份

专门用于 BP 多副本集群中。当 BP 多副本集群中的某一个非主 BP 节点故障时，连接 SP 执行集群备份还原的开销较大，也可通过直连主 BP 执行备份还原的方式恢复故障的 BP 节点。具体操作分为：一直连主 BP 进行备份，获取备份集；二使用 DMRMAN 工具和备份集对故障 BP 进行还原；三重启故障 BP 之后，主 BP 会自动对故障 BP 进行异步恢复。

需要注意的是，单副本环境下，直连 BP 模式节点执行联机备份，然后进行还原操作无法保证集群数据一致性。因此，单副本集群环境建议使用连接 SP 执行备份，用同一批备份集还原整个集群，才可保证数据的一致性。

下面对不同模式（BP 模式和 BS 模式）启动的 BP 节点备份分别进行介绍。

##### 8.8.3.3.1 直连 BP 模式节点

直连 BP 模式节点执行备份生成的备份集中没有 BAK_MAGIC 信息，因此用这个备份集进行数据还原和数据恢复时也无需指定 BAK_MAGIC。

例 直连 BP 模式节点执行备份操作。

```
SQL>BACKUP DATABASE;
```

若需要将生成的备份集还原到一个新的 BP 模式节点上，则需要保证新 BP 模式节点的所有初始化参数与老 BP 模式节点完全相同。同时还原后启动后，新 BP 模式节点的注册信息已被还原，因此还需要修改新 BP 模式节点中注册信息。

例 BP_1 和 BP_2 均以 BP 模式启动。将 BP_1 还原到 BP_2 上后，修改 BP_2 上的注册信息。操作登录 MP 执行。

```
//重命名BP_1为BP_2
SQL> SP_RENAME_DPC_INSTANCE('BP_1','BP_2');
//更新MP_2端口信息
SQL> SP_MODIFY_DPC_INSTANCE('BP_2','192.168.1.69','192.168.1.69', 6014,5249);
```

##### 8.8.3.3.2 直连 BS 模式节点

目前直连 BS 节点进行备份有两种登录方式：一是以非 LOCAL 方式登录；二是以 LOCAL 方式登录。下面分别介绍两种登录方式的备份方法：

###### 8.8.3.3.2.1 非 LOCAL 方式登录

非 LOCAL 方式登录 BS 节点执行备份时，BS 节点会以 SP 方式执行备份语句，执行备份语句时，相当于连接 SP 对整个 DMDPC 集群进行一次备份，对各个主节点生成一批备份集，此时会生成一个 BAK_MAGIC 值，这批备份集的 BAK_MAGIC 值相同，以便用户进行还原时使用同一批备份集还原集群，从而使整个集群恢复一致。当使用这批备份集还原节点时，数据还原和数据恢复时需要指定 BAK_MAGIC 进行还原，可以通过 show backupset 命令查看备份集 BAK_MAGIC 值。非 LOCAL 方式登录备份时，若以非 USE_AP=2 的方式备份，需要确保集群中各个主节点的 dmap 服务启动。

例 非 LOCAL 方式登录 BS 备份，等效于对 DMDPC 集群整体执行一次备份。

```
./disql SYSDBA/*****@192.168.100.168:5600
SQL>BACKUP DATABASE;
```

###### 8.8.3.3.2.2 LOCAL 方式登录

以 LOCAL 方式登录 BS 节点执行备份，会以 BP 的方式执行备份语句，等效于直连 BP 备份，仅会备份自身节点，这种情况下备份集中不会生成 BAK_MAGIC，默认为 0， 因此还原时无需指定 BAK_MAGIC。

例 LOCAL 方式登录 BS 备份，仅会备份自身 BS 节点。

```
./disql SYSDBA/*****@192.168.100.168:5600#{mpp_type=local}
SQL>BACKUP DATABASE;
```

需要注意的是，单副本环境下，直连 BS 执行 LOCAL 方式登录执行联机备份，然后进行还原操作无法保证集群数据一致性。因此，单副本集群环境建议使用连接 SP 执行备份，或者连接 BS 非 LOCAL 方式备份，然后用同一批备份集还原集群，才可保证数据的一致性。

### 8.8.4 还原

还原是备份的逆过程。DMRMAN 工具通过使用 MP 和 BP 的备份集，依次对目标库的 MP 和 BP 执行还原。

还原时，如果只还原 DMDPC 集群中的 MP 或者某个 BP 节点，则无法保证 MP 和 BP 节点的数据一致性，原则上是需要对整个 DMDPC 集群内的所有 MP 和 BP 节点全部执行还原。

如果存在 MP 或 BP 多副本，则必须要对所有副本节点全部执行还原。

#### 8.8.4.1 还原须知

本节说明对完全备份集和增量备份集均适用。

##### 8.8.4.1.1 BAK_MAGIC 用法

如果备份时未指定 BAK_MAGIC，数据还原、数据恢复时也不需要指定 BAK_MAGIC，但这种方式无法识别备份集混用的情况。

如果备份时指定了 BAK_MAGIC，数据还原、数据恢复时需要指定相同的 BAK_MAGIC，以确保使用同一组备份集进行还原，还原、恢复时会校验备份集中 BAK_MAGIC 与指定 BAK_MAGIC 是否一致，可以通过 DMRMAN 工具的 SHOW BACKUPSET 命令查看备份集的 BAK_MAGIC 字段值。

##### 8.8.4.1.2 DPC_MAGIC 用法

如果是对整个 DMDPC 集群进行还原，则还需要增加更新 DPC_MAGIC 这一步，以区分不同的 DMDPC 集群，DPC_MAGIC 也由用户指定并确保唯一，MP 和 BP 需要更新为相同的 DPC_MAGIC 值，更新 DPC_MAGIC 的动作必须要放在更新 DB_MAGIC 之前。

##### 8.8.4.1.3 恢复一致性

恢复一致性在单库还原和整个集群还原中用法略有不同，下面详细介绍。

###### 8.8.4.1.3.1 指定备份集恢复

n 单副本

对于单副本的 DMDPC 环境，无论是使用联机备份集还是脱机备份集，都可以将各节点恢复到备份结束时的位置，和普通单机的恢复一致性完全相同。

n 多副本

对于多副本架构的 DMDPC 环境，在常规恢复一致性时的基础上，建议指定 UNTIL END_LSN 选项，可保证所有节点数据恢复到一致性状态。

在多副本架构中在使用联机备份集恢复时可能存在以下几种情况：

  1. 各节点（主 MP 或主 BP）生成联机备份集时，各备份集备份结束时的 END_LSN 位置可以保证数据是一致的，但主 MP 或主 BP 的 C_LSN（已写入多数副本节点的 LSN）不一定推进到了 END_LSN 位置，备份集中记录的 C_LSN 有可能小于 END_LSN。
  2. 使用联机备份集恢复时，默认只恢复到 C_LSN 位置，此时不能保证各节点恢复出来的数据是一致的，因此新增了一个 UNTIL END_LSN 关键字，允许在 C_LSN 小于 END_LSN 的情况下，强制恢复到 END_LSN 位置。可通过 SHOW BACKUPSET 命令查看 C_LSN 和 END_LSN 信息。



对于多副本架构的 DMDPC 环境，在使用脱机备份集恢复时存在以下几种情况：

  1. 如果脱机备份集是在各节点正常退出的情况下生成的，UNTIL END_LSN 也可不指定，不指定 UNTIL END_LSN 也可以将各节点恢复到数据一致状态。
  2. 如果脱机备份集是在异常退出的情况下生成的，则必须使用 UNTIL END_LSN 方式才可以将各节点恢复到最新状态（包括所有副本节点），否则不能保证各节点的数据是一致的。



###### 8.8.4.1.3.2 指定归档恢复

和普通单节点相比，不论是单副本还是多副本的 DMDPC 集群恢复，均需要保证将所有 MP 和 BP 节点恢复到一个事务一致（数据一致）的状态，因此在指定归档恢复时，采用常规的恢复到最新还不行，还需要对 DMDPC 进行一些额外的操作。

DMDPC 归档恢复操作：

  1. 支持 UNTIL LSN 恢复方式，但是不建议用户使用这种方式。因为这种方式无法通过 LSN 判断出各节点是否处于一致状态，具体为 DMDPC 下各节点的 LSN 值不统一，通过 UNTIL LSN 的方式恢复无法保证集群恢复到数据一致状态，可能引发数据不一致的风险。
  2. 建议使用 UNTIL TIME 方式恢复，将各节点恢复到同一时间点，确保将各节点数据恢复到一致状态，此方式必须和 DPC_LOG_INTERVAL 参数配合使用，否则无法保证各节点的数据一致性。
  3. 如果不指定 UNTIL TIME，直接将各节点恢复到最新状态，则需要用户自己保证各节点恢复后的数据一致性。



指定 UNTIL TIME 方式恢复时，如果在日志中没有找到相匹配的时间点，则会一直恢复到最新，可通过日志中记录的恢复结束信息来确认各节点是否恢复到了相同的时间点。

#### 8.8.4.2 单库的还原

专门用于直连 MP 备份或直连 BP 备份之后，对 MP 或 BP 的还原。

单库还原具体包括数据还原和数据恢复两步。数据恢复又细分为恢复一致性和更新 DB_MAGIC 两步。

其中恢复一致性有两种方式：指定备份集恢复和指定归档恢复。

下面按照恢复一致性的两种方式进行分类，分别说明单库还原的操作步骤。

##### 8.8.4.2.1 指定备份集恢复

本节的恢复一致性操作采用指定备份集恢复方式。

示例如下：

  1. **使用完全备份集还原**



在准备好完全备份集的基础上，对 DMDPC 集群执行一次完全还原操作。

还原单副本 MP：

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc2\mp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back' USE BAK_MAGIC 132256;
//使用备份集恢复
RMAN>RECOVER DATABASE 'E:\dpc2\mp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back' USE BAK_MAGIC 132256;
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc2\mp\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

还原单副本 BP：

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc2\bp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\bp\DAMENG\bak\bp_back' USE BAK_MAGIC 132256;
//使用备份集恢复
RMAN>RECOVER DATABASE 'E:\dpc2\bp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\bp\DAMENG\bak\bp_back' USE BAK_MAGIC 132256 UNTIL END_LSN;
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc2\bp\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

  2. **使用增量备份集还原**



增量还原与增量备份相对应。执行增量还原时，指定的 BAK_MAGIC 为增量备份集的 BAK_MAGIC 而不是基备份集的 BAK_MAGIC。为了确保还原出来的 DMDPC 集群各节点数据一致，在一次 DMDPC 增量还原操作中，要求 MP 和所有 BP 节点指定相同的 BAK_MAGIC 值执行还原操作。

在准备好增量备份集的基础上，对 DMDPC 集群执行一次增量还原操作示例如下：

还原 MP：

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc\mp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back_increment' WITH BACKUPDIR 'E:\dpc\mp\DAMENG\bak\mp_back' USE BAK_MAGIC 112233;
//使用备份集恢复
RMAN>RECOVER DATABASE 'E:\dpc\mp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\mp\DAMENG\bak\mp_back_increment' USE BAK_MAGIC 112233;
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc\mp\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

还原 BP：

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc\bp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\bp\DAMENG\bak\bp_back_increment' WITH BACKUPDIR 'E:\dpc\bp\DAMENG\bak\bp_back' USE BAK_MAGIC 112233;
//使用备份集恢复
RMAN>RECOVER DATABASE 'E:\dpc\bp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\bp\DAMENG\bak\bp_back_increment' USE BAK_MAGIC 112233 UNTIL END_LSN;
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc\bp\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

##### 8.8.4.2.2 指定归档恢复

本节的恢复一致性操作采用指定归档恢复方式。

本节和在准备好归档的前提下，对 DMDPC 集群执行一次归档还原操作。

示例如下：

还原 MP：

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc\mp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\mp\DAMENG\bak\db_full_bak_for_recover_arch' USE BAK_MAGIC 112233;
//使用归档恢复
RMAN>RECOVER DATABASE 'E:\dpc\mp\DAMENG\dm.ini' WITH ARCHIVEDIR 'E:\dpc\mp\DAMENG\arch' UNTIL TIME '2021-10-10 10:56:40';
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc\mp\DAMENG\dm.ini' update DB_MAGIC;
```

还原 BP：

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc\bp\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc\bp\DAMENG\bak\db_full_bak_for_recover_arch' USE BAK_MAGIC 112233;
//使用归档恢复
RMAN>RECOVER DATABASE 'E:\dpc\bp\DAMENG\dm.ini' WITH ARCHIVEDIR 'E:\dpc\bp\DAMENG\arch' UNTIL TIME '2021-10-10 10:56:40';
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc\bp\DAMENG\dm.ini' update DB_MAGIC;
```

#### 8.8.4.3 整套集群的还原

DMDPC 整套集群的还原操作，是指将一套 DMDPC 集群的备份集还原到另一套 DMDPC 集群上，从而实现整套 DMDPC 的还原。

整个集群还原同样包括数据还原和数据恢复两步。数据恢复又细分为恢复一致性、更新 DPC_MAGIC、更新 DB_MAGIC 三步。更新 DPC_MAGIC 这一步是整套集群还原中特有的一步，为了标识所有节点均属于同一个集群。

其中恢复一致性有两种方式：指定备份集恢复和指定归档恢复。

目前整套 DMDPC 的还原与恢复集群主要有以下几种方式：

  1. 单副本 DMDPC 集群-->单副本 DMDPC 集群
  2. 单副本 DMDPC 集群-->多副本 DMDPC 集群
  3. 多副本 DMDPC 集群-->单副本 DMDPC 集群
  4. 多副本 DMDPC 集群-->多副本 DMDPC 集群



下面按照恢复一致性的两种方式进行分类，分别说明整个还原的操作步骤。

##### 8.8.4.3.1 指定备份集恢复

本节的恢复一致性操作采用指定备份集恢复方式。

###### 8.8.4.3.1.1 单副本集群还原到单副本集群

对于单副本集群之间的还原，需要确保备份时产生备份集的集群与目标需要确保两套 DMDPC 集群的配置是同构的，即 RAFT 组数是一致的，BP 节点数目等于还原的目标集群库的 BP 节点数目，从而确保还原后的集群数据是完整的。

另外，由于还原后的 DMDPC 集群中 MP 上的注册信息仍然是旧集群 MP 的注册信息，因此还需要更新还原后的 MP 上注册信息。

将一套 DMDPC 集群备份并还原到另外一套 DPC 集群(DPC 集群 1 到 DPC 集群 2)。

DPC 集群 1（MP: MP_1； BP: BP_11、BP_12； SP:SP1）

DPC 集群 2（MP: MP_2； BP: BP_21、BP_22； SP:SP2）

流程如下：

1）还原 MP_2、BP_21、BP_22。

为了保证还原后集群的数据一致，一定要确保还原使用的备份集是同一批次的备份集（可通过 DMRMAN 中 SHOW BACKUPSET 命令查看备份集上的 BAK_MAGIC 字段是否一致，以确认使用的备份集是否是同一批）。另外在执行 UPDATE DB_MAGIC 前，需要更新 DPC_MAGIC，使新集群和旧集群的 DPC_MAGIC 不同，以便通过 DPC_MAGIC 和旧集群进行区分，避免造成混淆。

2）正常退出 DPC 集群 2。

3）使用 MP_1 的备份集还原 MP_2。

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
//使用备份集恢复
RMAN>RECOVER DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
//更新DPC_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

使用 BP_11 的备份集还原 BP_21。

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\bp_11\DAMENG\bak\bp_11_back' USE BAK_MAGIC 132256;
//使用备份集恢复
RMAN>RECOVER DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\bp_11\DAMENG\bak\bp_11_back' USE BAK_MAGIC 132256;
//更新DPC_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN>RECOVER DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

使用 BP_12 的备份集还原 BP_22。

```
//数据还原
RMAN>RESTORE DATABASE 'E:\dpc2\bp_22\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\bp_12\DAMENG\bak\bp_12_back' USE BAK_MAGIC 132256;
//使用备份集恢复
RMAN>RECOVER DATABASE 'E:\dpc2\bp_22\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\bp_12\DAMENG\bak\bp_12_back' USE BAK_MAGIC 132256;
//更新DPC_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc2\bp_22\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
//更新DB_MAGIC
RMAN>RECOVER DATABASE 'E:\dpc2\bp_22\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

4）启动 MP。

通过 DIsql 连接 MP， 更新 MP 上注册信息。由于 BP11 及 BP12 的 RAFT_ID 已被表对象使用，因此无法在 MP 注册信息表中直接删除 BP11，BP12 实例。这里通过重命名和更新 IP 的操作来更新 MP 上的注册信息。

```
//重命名MP_1为MP_2
SQL>SP_RENAME_DPC_INSTANCE('MP_1', 'MP_2');
//更新MP_2端口信息
SQL>SP_MODIFY_DPC_INSTANCE('MP_2', '192.168.1.68', '192.168.1.68',  6004, 5239);
//重命名BP_11实例为BP_21，
SQL>SP_RENAME_DPC_INSTANCE('BP_11', 'BP_21');
//更新BP_21端口信息
SQL>SP_MODIFY_DPC_INSTANCE('BP_21', '192.168.1.68', '192.168.1.68', 6005, 5240);
//重命名BP_12为BP_22
SQL>SP_RENAME_DPC_INSTANCE('BP_12', 'BP_22');
//更新BP_22端口信息
SQL>SP_MODIFY_DPC_INSTANCE('BP_22', '192.168.1.68', '192.168.1.68', 6007, 5241);
//重命名SP1为SP2
SQL>SP_RENAME_DPC_INSTANCE('SP1', 'SP2');
//更新SP2注册信息
SQL>SP_MODIFY_DPC_INSTANCE('SP2', '192.168.1.68', '192.168.1.68', 6008, 5242);
```

5）启动 BP_21、 BP_22、 SP2。 至此 DPC 集群备份与还原全部完成。

可通过连接 SP2 查看表 DPC_INSTANCE 或建表查询等操作确认还原后集群是否正常。

###### 8.8.4.3.1.2 单副本集群还原到多副本集群

单副本集群还原到多副本集群，同样要求即 RAFT 组数是一致的，以确保还原后的集群数据是完整的。

另外，由于还原后的 DMDPC 集群中 MP 上的注册信息仍然是旧集群 MP 的注册信息，因此也需要更新还原后的 MP 上注册信息。

将一套单副本 DMDPC 集群环境还原到另一套多副本 DMDPC 集群环境（DPC 集群 1 到 DPC 集群 2）。

DPC 集群 1 包括 RAFT_MP1( MP_1)、RAFT_BP11( BP_1)、RAFT_BP12(BP_2 )和 RAFT_SP1(SP1)。

DPC 集群 2 包括 RAFT_MP2(MP_21、MP_22、MP_23)、RAFT_BP21(BP_11、 BP_12、BP_13)、RAFT_BP22(BP_21、BP_22、BP_23)和 RAFT_SP2(SP2)。

流程如下：

1）还原 MP_21、MP_22、MP_23。

语句如下：

```
//还原MP_21
RMAN>RESTORE DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN> RECOVER DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' UPDATE DB_MAGIC;
//还原MP_22
RMAN>RESTORE DATABASE 'E:\dpc2\mp_22\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_22\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_22\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_22\DAMENG\dm.ini' UPDATE DB_MAGIC;
//还原MP_23
RMAN>RESTORE DATABASE 'E:\dpc2\mp_23\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_23\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc1\mp_1\DAMENG\bak\mp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_23\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_23\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

2）使用 BP_1 的备份集还原 BP_11、BP_12、BP_13。

其中还原 BP11 的语句如下，还原 BP12，BP13 的语句与还原 BP11 的语句基本一致。（单副本还原到多副本不需要指定 MODE）

```
RMAN>RESTORE DATABASE 'E:\dpc2\bp_11\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\bp_1\DAMENG\bak\bp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\bp_11\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc1\bp_1\DAMENG\bak\bp_1_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\bp_11\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN>RECOVER DATABASE 'E:\dpc2\bp_11\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

3）使用 BP_2 的备份集还原 BP_21、BP_22、BP_23，这一步与使用 BP_1 的备份集还原 BP_11、BP_12、BP_13 的流程一致，这里不再赘述。

4）修改 MP_21 归档文件：删除 RAFT 归档，仅保留本地归档。使 MP_21 启动不会进入 MOUNT 状态，确保 MP_21 启动后可以正常修改注册信息。修改后 MP_21 的 dmarch.ini 文件如下：

```
[ARCHIVE_LOCAL1]
ARCH_TYPE			= LOCAL			#本地归档类型
ARCH_DEST			= e:\dpc_2\mp_21\DAMENG\arch	#本地归档文件路径
ARCH_FILE_SIZE	 	 = 128				#本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT	 = 0				#本地归档文件总大小，0表示无限制
```

5）启动 MP_21，使用 DIsql 连接 MP_21，修改 MP_21 中注册信息  。目前还不支持重命名 RAFT 组，因此仅修改 RAFT 组下节点信息。

```
//1.将实例MP_1重命名为MP_21
SQL>SP_RENAME_DPC_INSTANCE('MP_1','MP_21');
//2.修改MP_21的端口信息
SQL>SP_MODIFY_DPC_INSTANCE('MP_21','192.168.1.66','192.168.1.80','9457','8457');
//3.修改MP_21的状态信息,以便MP_21对应的RAFT组中可以加入MP_22,MP_23
SP_ALTER_DPC_INSTANCE('MP_21','STANDBY',4,1);
//4.将实例MP_22，MP_23加入MP_21所在的RAFT组（若忘记MP_21所在的RAFT组，可通过查询DPC_INSTACE表得知所在的RAFT组名）
SP_CREATE_DPC_INSTANCE(NULL,'MP_22','MP',6002,5238,'192.168.1.68', 'STANDBY',1,'MP instance');
SP_CREATE_DPC_INSTANCE(NULL,'MP_23','MP',6003,5239,'192.168.1.68', 'STANDBY',1,'MP instance');
//5.将实例BP_1重命名为BP_11
SQL>SP_RENAME_DPC_INSTANCE('BP_1','BP_11');
//6.修改端口信息
SQL>SP_MODIFY_DPC_INSTANCE('BP_11','192.168.1.68','192.168.1.68',7530,7430);
//7.修改BP_11模式状态信息，以便BP_11对应raft组中可以加入BP_12,BP_13
SQL>SP_ALTER_DPC_INSTANCE('BP_11','STANDBY',4,0);
//加入BP_12,BP_13
SQL>SP_CREATE_DPC_INSTANCE('RAFT_1','BP_12','BP',7531,7431,'192.168.1.68', 'standby',0,'BP instance');
SQL>SP_CREATE_DPC_INSTANCE('RAFT_1','BP_13','BP',7532,7432,'192.168.1.68', 'standby',0,'BP instance');
//8.将BP_2重命名为BP_21，并修改端口及模式状态信息，然后在BP_21所在的RAFT组中加入BP_22、BP_23
SQL>SP_RENAME_DPC_INSTANCE('BP_2','BP_21');
SQL>SP_MODIFY_DPC_INSTANCE('BP_21','192.168.1.68','192.168.1.68',7630,7830);
SQL>SP_ALTER_DPC_INSTANCE('BP_21','STANDBY',4,0);
SQL>SP_CREATE_DPC_INSTANCE('RAFT_2','BP_22','BP',7631,7831,'192.168.1.68', 'standby',0,'BP instance');
SQL>SP_CREATE_DPC_INSTANCE('RAFT_2','BP_23','BP',7632,7832,'192.168.1.68', 'standby',0,'BP instance');
//9.重命名SP并修改端口信息
SQL>SP_RENAME_DPC_INSTANCE('SP1','SP2');
SQL>SP_MODIFY_DPC_INSTANCE('SP2','192.168.1.68','192.168.1.68',7529,7429);
```

6）正常退出 MP_21，脱机备份 MP_21，还原到 MP_22、MP_23 上，使在 MP_21 上修改的注册信息同步到 MP_22、MP_23。

```
//备份MP_21
dmrman CTLSTMT="BACKUP DATABASE 'e:\dpc_2\mp_21\DAMENG\dm.ini' FULL TO BACKUP_01 BACKUPSET 'e:\dpc_2\mp_21\BACKUP_01'" USE_AP=2
//还原到MP_22
dmrman CTLSTMT="RESTORE DATABASE 'e:\dpc_2\mp_22\DAMENG\dm.ini' FROM BACKUPSET 'e:\dpc_2\mp_21\BACKUP_01'" USE_AP=2
dmrman CTLSTMT="RECOVER DATABASE 'e:\dpc_2\mp_22\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'e:\dpc_2\mp_21\BACKUP_01'" USE_AP=2
dmrman CTLSTMT="RECOVER DATABASE 'e:\dpc_2\mp_22\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
//还原到MP_23
dmrman CTLSTMT="RESTORE DATABASE 'e:\dpc_2\mp_23\DAMENG\dm.ini' FROM BACKUPSET 'e:\dpc_2\mp_21\BACKUP_01'" USE_AP=2
dmrman CTLSTMT="RECOVER DATABASE 'e:\dpc_2\mp_23\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'e:\dpc_2\mp_21\BACKUP_01'" USE_AP=2
dmrman CTLSTMT="RECOVER DATABASE 'e:\dpc_2\mp_23\DAMENG\dm.ini' UPDATE DB_MAGIC" USE_AP=2
```

7）重新为 MP_21 配置好 RAFT 归档，配置好后启动集群，单副本还原到多副本动作完成。

```
XMAL_HB_INTERVAL	     = 5				#节点通信检测间隔
RAFT_HB_INTERVAL	     = 150  			#选举心跳间隔
RAFT_VOTE_INTERVAL	     = 1500			#选举超时时间，三个库设置不同以尽快选出主库
[ARCHIVE_RAFT1] 
ARCH_TYPE				 = RAFT				#RAFT归档
ARCH_DEST			     = mp_22	              #归档目标实例名
[ARCHIVE_RAFT2] 
ARCH_TYPE				 = RAFT				#RAFT归档
ARCH_DEST				 = mp_23 			#归档目标实例名
[ARCHIVE_LOCAL1]
ARCH_TYPE			     = LOCAL			#本地归档类型
ARCH_DEST			     = e:\dpc_2\mp_21\DAMENG\arch	#本地归档文件路径
ARCH_FILE_SIZE	 	     = 128				#本地单个归档文件最大值,单位MB
ARCH_SPACE_LIMIT	     = 0				#本地归档文件总大小,0表示无限制
```

###### 8.8.4.3.1.3 多副本集群还原到单副本集群

多副本集群还原到单副本集群同样确保两套 DMDPC 集群的配置是同构的，即 RAFT 组数是一致的。

将一套多副本架构的 DMDPC 集群还原到另外一套单副本架构 DMDPC 集群。

流程如下：

DPC 集群 1 还原到 DPC 集群 2。

DPC 集群 1 包括 RAFT_MP1 ( MP_11、MP_12、 MP_13)、RAFT_BP11( BP_11、 BP_12、BP_13)、RAFT_BP12(BP_14、BP_15、BP_16 )和 RAFT_SP1(SP1)。

DPC 集群 2 包括 RAFT_MP2 ( MP_2)、RAFT_BP21( BP_21)、RAFT_BP22(BP_22 )和 RAFT_SP2(SP2)。

  1. 还原 MP、BP。



为了保证还原后集群的数据一致，一定要确保还原使用的备份集是同一批产生的备份集（可通过 DMRMAN 中 SHOW BACKUPSET 命令查看备份集上的 BAK_MAGIC 字段是否一致，以确认使用的备份集是否是同一批）。另外在执行 UPDATE DB_MAGIC 前，需要更新 DPC_MAGIC，以便和旧集群进行区分。

还原 SMP_2 时需要注意的是在 MP 执行 restore 阶段，注意指定 mode 为 normal 模式，以便启动后可以修改注册信息。

```
RMAN>RESTORE DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_11\DAMENG\bak\mp_11_back' USE BAK_MAGIC 132256 MODE 'NORMAL';
RMAN>RECOVER DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_11\DAMENG\bak\mp_11_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_2\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

还原 BP_21，BP_22 的语句与还原 MP_2 基本一致，使用 RAFT_BP11 组中节点的备份集还原 BP_21，使用 RAFT_BP12 组中节点备份集还原 BP_22。

此处需要指定 MODE 为 NORMAL。

  2. 启动修改 MP_2，修改 MP_2 上注册信息。



```
//1.将实例MP_11重命名为MP_2
SQL>SP_RENAME_DPC_INSTANCE('MP_11', 'MP_2');
//2.修改MP_2的端口信息
SQL>SP_MODIFY_DPC_INSTANCE('MP_2', '192.168.1.68', '192.168.1.68',  7630, 7830);
//3.删除MP_12、MP_13
SQL>SP_DROP_DPC_INSTANCE('MP_12');
SQL>SP_DROP_DPC_INSTANCE('MP_13');
//4.修改MP_2模式状态为NORMAL、OPEN、VALID
SQL>SP_ALTER_DPC_INSTANCE('MP_2', 'NORMAL', 4, 1);
//5.使用同样的方式将BP_11重命名为BP_21，删除BP_12,BP_13，修改BP_21模式状态
//6.使用同样的方式将BP_14重命名为BP_22，删除BP_15、BP_15，修改BP_22模式状态
//7.重命名SP1为SP2，修改SP2上端口信息
```

  3. 重启 MP_2、BP_21、BP_22、SP2，集群还原完成。



###### 8.8.4.3.1.4 多副本集群还原到多副本集群

多副本集群还原到多副本集群同样需要确保两套 DPC 集群的配置是同构的，即 RAFT 组数是一致的。

将一套多副本架构的 DPC 集群还原到另外一套多副本架构 DPC 集群流程如下：(DPC 集群 1 到 DPC 集群 2)。

DPC 集群 1 包括 RAFT_MP1 ( MP_11、 MP_12、 MP_13)、RAFT_BP11( BP_11、 BP_12、BP_13)、RAFT_BP12(BP_14、BP_15、BP_16 )和 RAFT_SP1(SP1)。

DPC 集群 2 包括 RAFT_MP2 (MP_21、MP_22、MP_23)、RAFT_BP21( BP_21、BP_22、BP_23)、RAFT_BP22(BP_24、BP_25、BP_26)和 RAFT_SP2(SP2)。

流程如下：

1） 还原 MP、BP。

为了保证还原后集群的数据一致，一定要确保还原使用的备份集是同一批产生的备份集（可通过 DMRMAN 中 SHOW BACKUPSET 命令查看备份集上的 BAK_MAGIC 字段是否一致，以确认使用的备份集是否是同一批）。另外在执行 UPDATE DB_MAGIC 前，需要更新 DPC_MAGIC，以便和旧集群进行区分。

还原 MP_21、MP_22、MP_23。

以还原 MP_21 为例：

```
RMAN>RESTORE DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\mp_11\DAMENG\bak\mp_11_back' USE BAK_MAGIC 132256 MODE 'NORMAL';
RMAN>RECOVER DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc1\mp_11\DAMENG\bak\mp_11_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN>RECOVER DATABASE 'E:\dpc2\mp_21\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

还原 MP_22、MP_23 的流程与 MP_21 基本一致，需要注意的是在 MP 执行 restore 阶段，注意指定 mode 为 normal 模式，以便启动后可以修改注册信息。

多副本还原到多副本，还原 BP_21，还原 BP 时无需指定 MODE 字段。

```
RMAN>RESTORE DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' FROM BACKUPSET 'E:\dpc1\bp_11\DAMENG\bak\bp_11_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' FOR STANDBY FROM BACKUPSET 'E:\dpc1\bp_11\DAMENG\bak\bp_11_back' USE BAK_MAGIC 132256;
RMAN>RECOVER DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' UPDATE DPC_MAGIC 123456;
RMAN>RECOVER DATABASE 'E:\dpc2\bp_21\DAMENG\dm.ini' UPDATE DB_MAGIC;
```

还原	BP_22、BP_23 的流程与 BP_21 基本一致。

  2. 修改 MP_21 的归档文件，删除 RAFT 归档，确保启动后是 OPEN 状态。
  3. 启动 MP_21，通过 DISQL 连接 MP_21，更新 MP_21 上注册信息。



这里以 MP_21 为例。MP_22、MP_23、BP_21、BP_22、BP_23、BP_24、BP_25、BP_26 和 SP2 的更新注册信息流程与 MP_21 基本一致，均为执行重命名和更新端口信息两步。

```
//将实例MP_11重命名为MP_21
SQL>SP_RENAME_DPC_INSTANCE('MP_11', 'MP_21');
//更新MP_21的端口信息
SQL>SP_MODIFY_DPC_INSTANCE('MP_21', '192.168.1.68', '192.168.1.68',  7630, 7830);
//下面分别继续执行其他节点的重命名和更新端口号
……
```

如果多副本 RAFT 内节点进行了改变（扩容或者缩容）。例如：集群 1 中 MP 为 MP1、MP2 和 MP3，集群 2 中 MP 为 MP21、MP22、MP23、MP4、MP5。那么新增或删除的节点请按照下面的情况进行处理。

首先，对于新增或者删除的节点注册信息。

● 对于新增的节点只需要使用 SP_CREATE_DPC_INSTANCE 按照实际配置注册进对应的 RAFT 组这一步即可，无需重命名和修改端口信息。

例 增加一个 MP4，只需要执行如下步骤：

```
SP_CREATE_DPC_INSTANCE(NULL, 'MP4', 'MP', 6203, 5439, '192.168.1.118', '', 'STANDBY', 0, 'MP instance');
```

● 对于需要删除的节点也仅需使用 SP_DROP_DPC_INSTANCE 删除对应节点的注册信息这一步即可。

其次，完成更新注册信息后，可在系统表 DPC_INSTANCE 中查询到。注册信息包括实例名和 IP：PORT 信息。注册信息和目标 DPC 环境配置完全一致表示注册成功。

  4. 备份 MP_21，还原到 MP_22、MP_23。使在 MP_21 上修改的注册信息同步到 MP_22、MP_23。
  5. 重新为 MP_21 配置好 RAFT 归档，归档文件与删除 RAFT 归档前的配置一致即可。
  6. 启动 MP（MP_21、MP_22、MP_23）、BP（BP_21、BP_22、BP_23、BP_24、BP_25、BP_26）和 SP（SP2）。至此，多副本集群还原结束。



##### 8.8.4.3.2 指定归档恢复

本节的还原内容和 [8.8.4.3.1 指定备份集恢复](https://eco.dameng.com/document/dm/zh-cn/pm/dpc-cluster-management-use.html#8.8.4.3.1%20%E6%8C%87%E5%AE%9A%E5%A4%87%E4%BB%BD%E9%9B%86%E6%81%A2%E5%A4%8D)中的还原内容基本一致。唯一不同的是恢复一致性这一步，采用的是指定归档恢复。

例 指定归档修复的方式完成恢复一致性。

使用归档恢复 MP。

```
RMAN>RECOVER DATABASE 'E:\dpc\mp\DAMENG\dm.ini' WITH ARCHIVEDIR 'E:\dpc\mp\DAMENG\arch' UNTIL TIME '2021-10-10 10:56:40';
```

使用归档恢复 BP。

```
RMAN>RECOVER DATABASE 'E:\dpc\bp\DAMENG\dm.ini' WITH ARCHIVEDIR 'E:\dpc\bp\DAMENG\arch' UNTIL TIME '2021-10-10 10:56:40';
```

#### 8.8.4.4 归档还原

归档还原就是将备份集中的归档日志文件重新拷贝到指定归档目录中。通过 DMRMAN 工具可实现对 DMDPC 集群实现归档还原。备份集可以是脱机归档备份集，也可以是联机归档备份集。

对 DMDPC 中节点归档还原方式与单节点的归档还原方式一致。

例 使用 DMRMAN 工具执行归档还原，下面两种方式二选一即可。

指定还原的目标归档日志目录：

```
RMAN> RESTORE ARCHIVE LOG FROM BACKUPSET '/home/dm_bak/arch_all_for_restore' TO ARCHIVEDIR'/opt/dmdbms/data/DAMENG_FOR_RESTORE/arch_dest' OVERWRITE 2;
```

指定还原目标库的 dm.ini 文件路径：

```
RMAN> RESTORE ARCHIVE LOG FROM BACKUPSET '/home/dm_bak/arch_all_for_restore' TO DATABASE '/opt/dmdbms/data/DAMENG_FOR_RESTORE/dm.ini' OVERWRITE 2;
```

## 8.9 管理表的读写属性

### 8.9.1 修改表的属性

DMDPC 支持将数据库表设置为 READ ONLY 或 READ WRITE 属性。READ ONLY 属性修改不支持分区表和 HUGE 表。

语法如下：

```
ALTER TABLE table_name {READ ONLY | READ WRITE};
```

READ ONLY 修改为只读，具备 READ ONLY 属性的表又称为只读表，不支持对只读表修改数据。READ WRITE 修改为可读可写。

将表 T（假定表名为 T）从 READ WRITE 修改为 READ ONLY 的同时，对于以 BS 模式启动的 DMDPC 集群，系统会在集群所有节点上隐式创建一个与该表同构的临时表 T$ROT（包括二级索引），并在随后的查询中用 T$ROT 替换表 T 的查询；以 BP 模式启动的集群则仅在用户连接的 SP 节点上创建同构临时表。将表从 READ ONLY 修改为 READ WRITE 的同时，系统会隐式删除同构的临时表（包括二级索引）。

但是在下面这种情况下，DMDPC 系统不会在查询中将表 T 替换为 T$ROT：如果影响列数据类型的服务器参数值（如参数 DECIMAL_FIX_STORAGE）在修改表 T 为只读时和创建表 T 时不同，那么修改表后服务器并不会将 T 和 T$ROT 进行关联，从而也不会将查询中的表 T 替换为 T$ROT。

### 8.9.2 使用只读表的好处

在分布式环境中，将数据库表的属性修改为只读表时，系统会自动在相关节点上创建一个同构临时表，并将只读表的数据装载进同构临时表。如此以来，只读表中的数据将以同构临时表的形式全部存放在 SP 上。

使用只读表好处有两点：一是 SP 将访问各个站点目标表的分布式访问变成对同构临时表的本地访问，减少通讯代价，提高查询效率；二是减少事务涉及的 BP，减少分布式事务提交时的交互。

```
//第一步，准备数据库表。
CREATE TABLE T1(C1 INT);
INSERT INTO T1 VALUES(1);
COMMIT;
//第二步，将数据库表T1修改为只读表（READ ONLY属性）。
ALTER TABLE T1 READ ONLY;
//第三步，查询数据库表。此时使用的查询对象为同构临时表，可提高查询效率。
SELECT * FROM T1;
//第四步，可以观察EXPLAIN所展示的表对象名称看出有无替换成临时表T1$ROT。
EXPLAIN SELECT * FROM T1;
```

## 8.10 灰度升级与引流

### 8.10.1 灰度升级

在 DMDPC 使用过程中，可以对 SP 进行灰度升级：在不停止集群服务的情况下，轮流更新升级 SP 的代码版本，升级后 SP 能正常对外服务。升级后可以执行引流，使得在 SP 上的会话达到负载均衡的效果。

#### 8.10.1.1 灰度升级介绍

灰度升级支持的内容介绍如下：

  1. 支持用系统函数 SP_SET_SP_UPGRADE()将 SP 标记为升级状态。不能对 BP、MP 标记升级。
  2. 支持用系统函数 SP_RESET_SP_UPGRADE()将升级中的 SP 恢复正常。不能对 BP、MP 执行恢复。
  3. 升级状态的 SP 不接受新连接，不允许被选为计算节点。
  4. 存在 SP 进行升级时，禁用在 SP 上执行的事件触发器，时间触发器以及调度。MP 上执行的触发器不进行禁用。
  5. 升级状态的 SP 的会话平滑释放。具体为在配置有服务名的前提下，事务提交或回滚时，客户端会主动断开并尝试切换到其他正常服务的 SP；未配置服务名时，会话仅断开，不切换。长事务或长空闲会话会在服务器检测超时后自动断开，超时时间由 INI 参数 DPC_GUP_SESS_TIMEOUT 进行配置。
  6. 升级状态的 SP 在所有会话结束后，SP 服务器自行退出。



SP 灰度升级过程中的注意事项：

  1. 升级状态中的 SP，对于已存在的 ESESSION 连接支持其它节点广播的系统函数或 V$视图查询请求，对于新连接则不支持。
  2. 升级状态中的 SP，支持 MP 在 DDL 期间广播的字典封锁及计划淘汰等请求。
  3. 长事务与空闲会话达到 DPC_GUP_SESS_TIMEOUT 配置的超时值，逐渐退出。
  4. 升级状态中 SP 的当前连接数目会话数目前无法查看。
  5. 服务器重启前，需要由用户先登录 MP 或可用的 SP 执行系统函数使升级节点恢复正常。



#### 8.10.1.2 灰度升级操作过程

灰度升级过程共分为六步。下面详细介绍：

第一步 配置服务名，可选择跳过该阶段。

第二步 用户对目标 SP 进行标记升级。

例  升级登录任意正常 SP，升级 SP1。

```
SP_SET_SP_UPGRADE('SP1');
```

**第三步 等待该节点退出后。**未配置服务名时，该阶段事务结束时，会话仅断开而不是切换。

第四步 更换执行码。

第五步 手动对该节点进行恢复。

例  登录任意正常状态 SP 或 MP，恢复 SP1。

```
SP_RESET_SP_UPGRADE('SP1');
```

第六步 最后重启节点。

例如重启 SP1。

### 8.10.2 引流

引流是为了解决 DMDPC 使用过程中（例如灰度升级导致的）SP 上会话数差距较大的问题而提出的一种负载均衡方案。

#### 8.10.2.1 引流介绍

当一个 SP 上会话数较多时，引流会在会话的事务执行提交或回滚操作后，对会话做一次切换，将会话连接到其他会话数低的 SP 节点上，从而达到整体的负载均衡效果。

引流支持的内容介绍如下：

  1. n 支持在任意 SP 上执行系统函数 SP_DPC_REBANLANCE_SESSION(1)，收集当前可见的所有 SP 的会话数，并产生所有节点各自的平衡方案。
  2. 支持在任意 SP 上执行系统函数 SP_DPC_REBANLANCE_SESSION(0)，收集当前可见的所有 SP 的会话数，并对所有 SP 节点上的平衡方案清空，清空时关闭引流。



SP 引流的注意事项。下面正在执行灰度升级的 SP1 为例进行说明：

  1. 在 SP1 上灰度升级时，SP1 无法执行引流；若已正在引流，引流也会暂停执行。
  2. 在 SP1 上灰度升级时，其他 SP  
执行引流时 SP1 对他们是不可见的，若其他已正在向 SP1 切换，此次切换跳过。

  3. 在进行中的引流任务未结束时，不能再次引流覆盖未完成的平衡方案，需要先清除，因此 SP_DPC_REBANLANCE_SESSION(1)不能连续执行。
  4. 引流的清除操作 SP_DPC_REBANLANCE_SESSION(0)可以连续执行。



#### 8.10.2.2 引流开启与关闭

引流过程共分为两步。下面详细介绍：

一 引流开启。

例 在任意 SP 上执行系统函数，收集当前可见的所有 SP 的会话数，并产生平衡方案，开始引流。

```
SP_DPC_REBANLANCE_SESSION(1);
```

引流开启后，当 SP 上会话较多时，每当会话的事务提交或回滚，会话均会自动进行一次切换，切换到其他相对空闲的 SP 站点上，直到负载均衡。例如：DMDPC 系统共三个 SP，SP1 有 15 个会话，SP2 有 0 个会话，SP3 有 0 个会话。开启引流之后，当 SP1 的第 1-5 个会话的事务结束，系统会将会话切换给 SP2，第 6-10 个会话的事务结束后，系统会将会话切换给 SP3，直到负载均衡。

二 引流关闭。

例 在任意 SP 上执行系统函数，收集当前可见的所有 SP 的会话数，并清除平衡方案，结束引流。

```
SP_DPC_REBANLANCE_SESSION(0);
```

## 8.11 多副本手动切换主备

目前多副本集群是通过自动选举的方式选出主备的，为了方便集群使用，同时提供了手动切换主库的方式。

备库在执行切换前，会对自身否具有切换条件进行检查，下面列出了不具备切换的情况：

  1. 不是多副本环境，不允许切换。
  2. 备库选举角色不为 FOLLOWER，不允许切换。
  3. 备库模式不为 STANDBY 或状态不为 OPEN，不允许切换。
  4. 不存在有效的主库，不允许切换。
  5. 备库自身归档无效，不允许切换。
  6. 系统前一次节点变更还未完成（比如正在执行增删节点，尚未完成），不允许切换。



手动切换的前提条件和可能遇到问题的解决办法：

  1. 所有操作是直连 MP 或 BP 节点执行切换。其中，对于 BS 主库节点需要 LOCAL 方式登录执行。
  2. 执行手动切换需要严格按照执行流程执行。
  3. 若执行 SP_RAFT_SUSPEND_THREAD 时失败，可由用户根据返回值决定是否重试。
  4. 若执行 SP_RAFT_SWITCHOVER 执行失败，可登录任意一个备库，执行 SP_RAFT_RESUME_THREAD 唤醒主库挂起的工作线程。若所有备库都故障，可以等到有活动备库再执行此操作，或者将主库重启。



```
SP_RAFT_RESUME_THREAD('BP_1'); //唤醒BP_1工作线程
```

手动切换流程如下:

1.Disql 登录主库（对于 BS 主库节点需要通过 LOCAL 方式登录），挂起主库工作线程。

```
SP_RAFT_SUSPEND_THREAD();
```

2.Disql 登录备库，执行 SP_RAFT_SWITCHOVER()，将备库切换为主库

```
SP_RAFT_SWITCHOVER();
```

## 8.12 SP 缓存表行数

提供 SP 可在本地缓存表行数的功能。开启本功能后，SP 每次执行 SQL 语句时，可先从本地缓存中获取表行数，本地没有再向 MP 请求表行数，可大大降低 MP 的性能瓶颈。关闭本功能，每次执行 SQL 语句时均需向 MP 请求表行数。

使用 INI 参数 STAT_CACHE_FLAG=1，开启 SP 在本地缓存表行数的功能。此参数值在各站点可以不一致。STAT_CACHE_FLAG=1 需和 STAT_CACHE_CAPACITY 搭配使用，由 STAT_CACHE_CAPACITY 设置 SP 缓存表行数的缓存容量。

如果需要清理本地缓存的表行数，可使用 SP_CLEAR_TAB_ROWCNT_CACHE(站点号,表 id)，清除所有或指定 SP/BS 节点上的全部或部分表行数缓存。

使用 V$TABLE_ROWCNT_CACHE 可查看各节点中的缓存情况，包含表 id，访问次数，表行数。

## 8.13 本地登录

本地登录（即登录时设置 mpp_type=local）是一种仅允许访问自身节点与 MP 节点的一种登录模式。只要自身节点存活且存在可用 MP，就允许本地登录。本地登录仅支持访问和修改本地数据，不支持访问和修改其他节点数据。

### 8.13.1 DDL 操作

本地模式登录 BP/MP 时，仅允许对自身节点进行库级备份这一种 DDL 操作。SP 不支持任何 DDL。以本地模式登录，执行备份，只会备份本节点。该情况下备份集中 BAK_MAGIC=0，数据还原和数据恢复时不需要指定 BAK_MAGIC 即还原成功。

例 本地登录直连 BP 备份本节点，备份集中 BAK_MAGIC=0，数据还原和数据恢复时不需要指定 BAK_MAGIC。

```
./disql SYSDBA/*****@192.168.100.121:5273#"{mpp_type=local}"

disql V8

SQL> BACKUP DATABASE;

操作已执行

RMAN> show backupset

'/ssd/test/BP3_D/DAMENG/bak/DB_DAMENG_FULL_20220707_171446_555778' ;

bak_magic:       0

RMAN> RESTORE DATABASE '/ssd/test/BP1_C/DAMENG/dm.ini' FROM BACKUPSET '/ssd/test/BP3_D/DAMENG/bak/DB_DAMENG_FULL_20220707_171446_555778' ;

RMAN> RECOVER DATABASE '/ssd/test/BP1_C/DAMENG/dm.ini' FROM BACKUPSET '/ssd/test/BP3_D/DAMENG/bak/DB_DAMENG_FULL_20220707_171446_555778';

RMAN> RECOVER DATABASE '/ssd/test/BP1_C/DAMENG/dm.ini' UPDATE DB_MAGIC;
```

### 8.13.2 DML 操作

本地登录支持 DML 操作访问或修改本地数据。不支持跨节点操作。例如：不支持数据更新操作中，旧值和新值位于不同的节点。

以本地模式登录 MP、BP 和 SP 主库时，允许执行的 DML 操作有增加、删除、修改、查询、提交、回滚。查询、插入、更新、删除操作均支持带有子查询。备库仅支持提交和回滚。

不同表对象的查询，插入，修改、删除在不同节点上的限制各不相同。下面分别介绍：

#### 8.13.2.1 用户表

本小节的用户表是指除了临时表、DUAL 表、V$ 动态视图、系统表之外的表。

MP、SP 不支持操作用户表。

BP 支持操作非分区表，但是表和表所属表空间均需位于本节点上。BP 支持操作分区表，但是仅支持操作本节点上的数据，例如：查询其他节点数据将返回空集。

#### 8.13.2.2 动态视图和系统表

以本地模式登录 MP、BP 或 SP，支持查询自身 V​视图，但视图中无法获得其他节点的V动态视图信息。

MP 支持查询系统表，BP 和 SP 不支持。

#### 8.13.2.3 临时表

MP、BP、SP 均支持对临时表 DML 操作（查询，插入，修改、删除、提交、回滚）。

### 8.13.3 系统过程或函数处理

系统过程或函数处理。在本地登录 MP、BP 或 SP 时：

系统过程或函数处理。在本地登录 MP、BP 或 SP 时：

  1. SP 上执行的函数：仅在 MP 与本 SP 节点执行的函数将被允许，否则将报错。
  2. 只直连 MP 执行的函数：支持重建系统视图函数 SP_CREATE_SYSTEM_VIEWS、通信参数 SP_SET_DPC_NET_CONF、以及动态增删节点等相关函数。具体支持的函数如下：



SP_CREATE_SYSTEM_VIEWS

SP_SET_DPC_NET_CONF

SP_CREATE_DPC_系列

SP_DROP_DPC_系列

SP_MODIFY_DPC_系列

SP_BP_GROUP_系列

  3. 可直连 MP 或 BP 执行的函数，在本地登录时，均允许执行。具体支持的函数如下：



SP_RAFT_SUSPEND_THREAD

SP_RAFT_SWITCHOVER

SP_RAFT_RESUME_THREAD

SP_ADD_RAFT_LEARNER

SP_ALTER_RAFT_NODE

SP_ADD_RAFT_NODE

SP_DELETE_RAFT_NODE

SP_REPLACE_RAFT_NODE

SP_DELETE_RAFT_LEARNER

CHECKPOINT

SP_SET_PARA_VALUE

## 8.14 容错域的使用

容错域可以添加 MP 或 BP，禁止添加 SP。RAFT 协议中的 4 种角色，以及影子节点也可以正常加入。同个容错域可以同时包含 MP、BP。容错域的 DOMAIN_ID 为主键，且为当前最小空闲 id；容错域的名称 name 唯一；DESCRIPTION 不唯一。

每个容错域内包含 0-N 个不同 RAFT 组内的节点，同一个 RAFT 组内的不同节点不能包含在同一个域内。因此每个容错域的容纳个数和 RAFT 个数有关：每个容错域能容纳 4096 个节点。在容错域新增实例、以及 MP 主实例启动时，会检查容错域是否有重复 raft 组。

不同容错域不能位于同一个物理区域。

例 1 新建容错域 FDOM_1。

```
SP_CREATE_FAULT_DOMAIN('FDOM_1', 'shanghai');
```

例 2 将 MP1，BP1 实例移入容错域，不论 MP1,BP1 现在属于哪个容错域。

```
SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'MP1');

SP_FAULT_DOMAIN_MV_INST('FDOM_1', 'BP1');
```

例 3 查看实例与容错域对应关系。

```
SELECT RAFT_ID, NAME, FAULT_DOMAIN from V$DPC_EDCT_INSTANCE;
```

例 4 将 BP1 移出容错域 FDOM_1，并使其不属于任何一个容错域。

```
SP_FAULT_DOMAIN_MV_INST(NULL, 'BP1');
```

例 5 删除容错域 FDOM_1。删除之前，须先移出 FDOM_1 内所有的实例。

```
SP_DROP_FAULT_DOMAIN('FDOM_1', 1);
```

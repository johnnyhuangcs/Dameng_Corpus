

# DM8 Linux 脚本使用手册

本文档主要介绍 Linux(Unix) 系统中 DM 数据库的各种服务脚本，以及脚本的使用。

## 1 服务脚本

在 Linux(Unix)系统中，很多程序的进程是以后台运行的方式启动的，进程以后台方式运行能够保证进程不因终端窗口的关闭而关闭，而且这些进程大多是常驻的，需要长期运行且不中断的。DM 中也有许多这样的程序，这些程序的稳定运行保证了 DM 数据库实例或 DM 集群的正常运行，这些程序就是 DM 服务程序。为了方便用户能够使 DM 服务程序以后台运行的方式启动，我们为 DM 服务程序提供了对应的服务脚本模板。

### 1.1 DM 服务脚本

DM 提供了 14 个服务脚本模板，分为两类：

第一类 位于 bin 目录下，不支持修改模板名称的脚本。分别为：DmAPService 服务、DmAuditMonitor 服务、DmJobMonitor 服务、DmInstanceMonitor 服务。

第二类 位于 bin/service_template 目录下，支持修改模板名称的脚本。分别为：DmServer 服务、DmWatchService 服务、DmMonitorService 服务、DmASMSvrService 服务、DmASMSvrmService 服务、DmCSSService 服务、DmCSSMonitorService 服务。

### 1.2 服务数量管理

不同的脚本服务可运行的数量各不相同。下面分别进行介绍：

第一类服务脚本模板对应的 DM 服务程序，一个 DM 系统只需要运行一个。

第二类服务脚本模板对应的 DM 服务程序，每一种进程均可在同一物理机器上运行一个或多个，同样一台物理机器也可以运行多种服务脚本。用户可以将第二类服务脚本模板拷贝到其他目录，并修改脚本名称。建议用户将服务脚本模板名称作为新服务脚本名称的前缀。

### 1.3 服务脚本参数修改

用户在使用这些服务脚本前，需要先手动修改服务脚本的参数。

以下将详细讲解各种服务脚本需要设置的参数。

  1. **DmAPService**



达梦数据库辅助插件服务。dmap 对应的服务脚本模板，不需要修改脚本参数。DmAPService 服务为单实例，即当前达梦数据库系统只可运行一个 DmAPService 服务。

表1.1 DmAPService服务参数

|**参数名称**|**参数说明**|
|----|----|
|DMAP_INI_PATH|服务脚本所需要的 ini 文件路径，即 dmap.ini 文件路径|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  2. **DmAuditMonitor**



达梦数据库实时审计监控服务。dmamon 对应的服务脚本模板，DmAuditMonitor 服务为单实例，即当前达梦数据库系统只可运行一个 DmAuditMonitor 服务。

表1.2 DmAuditMonitor服务参数

|**参数名称**|**参数说明**|
|----|----|
|INI_PATH|服务脚本所需要的 ini 文件路径，即 dmamon.ini 文件路径|
|DCR_INI_PATH|服务脚本所需要的 ini 文件路径，即 dmdcr.ini 文件路径|
|USER_ID|数据库连接字符串，格式为 username/password@servername:port|
|SSL_PATH|加密通讯(SSL)数据库的 SSL 文件的路径|
|SSL_PWD|加密通讯(SSL)数据库的 SSL 文件的密码|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  3. **DmJobMonitor**



达梦数据库实时作业监控。dmjmon 对应的服务脚本模板，DmJobMonitor 服务为单实例，即当前达梦数据库系统只可运行一个 DmJobMonitor 服务。

表1.3 DmJobMonitor服务参数

|**参数名称**|**参数说明**|
|----|----|
|USER_ID|数据库连接字符串，格式为 username/password@servername:port|
|SSL_PATH|加密通讯(SSL)数据库的 SSL 文件的路径|
|SSL_PWD|加密通讯(SSL)数据库的 SSL 文件的密码|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  4. **DmInstanceMonitor**



达梦数据库实例实时监控服务。dmimon 对应的服务脚本模板，除了 SAVE_PROC_OUT 参数，其他参数不需要修改。DmInstanceMonitor 服务为单实例，即当前达梦数据库系统只可运行一个 DmInstanceMonitor 服务。

表1.4 DmInstanceMonitor服务中可修改的参数

|**参数名称**|**参数说明**|
|----|----|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  5. **DmService**



达梦数据库实例服务。dmserver 对应的服务脚本模板，一台物理主机可以运行多个 dmserver 实例，同样一台物理主机也可以运行多个 dmserver 的服务脚本。用户可以将服务脚本模板拷贝到其他目录，并修改脚本名称。

> **注意**
>
> 建议用户将DmService作为新服务脚本的名称前缀。



表1.5 DmService服务参数

|**参数名称**|**参数说明**|
|----|----|
|INI_PATH|服务脚本所需要的 ini 文件路径，即 dm.ini 文件路径|
|DCR_INI_PATH|服务脚本所需要的 ini 文件路径，即 dmdcr.ini 文件路径|
|DPC_MODE|DPC 中的实例角色，参数为 BP、MP、SP|
|SIGNKEY_PASS|GmSSL signkey.pem 证书文件的口令密码|
|ENCKEY_PASS|GmSSL enckey.pem 证书文件的口令密码|
|START_MODE|服务启动模式，即 dmserver 启动模式，参数为 open 和 mount|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  6. **DmWatcherService**



达梦数据库数据守护服务(V4.0)。dmwatcher 对应的服务脚本模板。

表1.6 dmwatcher服务参数

|**参数名称**|**参数说明**|
|----|----|
|INI_PATH|服务脚本所需要的 ini 文件路径，即 dmwatcher.ini 文件路径|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  7. **DmMonitorService**



达梦数据库数据守护监视器服务(V4.0)。dmmonitor 对应的服务脚本模板。

表1.7 dmmonitor服务参数

|**参数名称**|**参数说明**|
|----|----|
|INI_PATH|服务脚本所需要的 ini 文件路径，即 dmmonitor.ini 文件路径|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  8. **DmASMSvrService**



达梦数据库集群同步服务。dmasmsvr 对应的服务脚本模板。

表1.8 dmasmsvr服务参数

|**参数名称**|**参数说明**|
|----|----|
|DCR_INI_PATH|服务脚本所需要的 ini 文件路径，即 dmdcr.ini 文件路径|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  9. **DmASMSvrmService**



达梦数据库集群同步服务，专门用于 DMASM 镜像环境。dmasmsvrm 对应的服务脚本模板。

表1.9 dmasmsvrm服务参数

|**参数名称**|**参数说明**|
|----|----|
|DCR_INI_PATH|服务脚本所需要的 ini 文件路径，即 dmdcr.ini 文件路径|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  10. **DmCSSService**



达梦数据库集群同步监控服务。dmcss 对应的服务脚本模板。

表1.10 dmcss服务参数

|**参数名称**|**参数说明**|
|----|----|
|DCR_INI_PATH|服务脚本所需要的 ini 文件路径，即 dmdcr.ini 文件路径|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


  11. **DmCSSMonitorService**



达梦数据库自动存储管理器服务。dmcssm 对应的服务脚本模板。

表1.11 dmcssm服务参数

|**参数名称**|**参数说明**|
|----|----|
|INI_PATH|服务脚本所需要的 ini 文件路径，即 dmcssm.ini 文件路径|
|IS_DISABLED|是否禁用服务脚本，是指为 true 则禁用此脚本|
|SAVE_PROC_OUT|是否在脚本服务日志文件中记录脚本服务运行过程中产生的日志。TRUE：是；FALSE：否。缺省为 FALSE|


### 1.4 服务使用命令

服务脚本支持的命令包括：

```
start|stop|status|condrestart|restart
```

DmServer 服务脚本和 DmASMSvrService 服务脚本除了支持上述命令外，还支持 stop_all 命令。在 DMDSC 环境下，使用 stop_all 命令可以停止集群中所有节点服务。

以下内容将以服务脚本 DmServiceDMSERVER 为示例。

  1. **start**



启动服务命令，启动命令如下：

```
./DmServiceDMSERVER start
```

启动服务成功，将打印 [ok] 字样。如果服务启动失败，将打印 [cancel]字样，并打印启动服务失败的详细信息。

  2. **stop**



停止服务命令，停止命令如下：

```
./DmServiceDMSERVER stop
```

停止服务成功，将打印 [ok] 字样。

> **注意**
>
>  **如果服务停止失败，服务脚本将循环去关闭服务进程，服务脚本不会主动强杀服务进程，此时需要用户手动停止服务进程。  
>  DMDSC环境下，必须依次停止各个节点服务，即仅当一个服务成功停止后才能停止下一个服务。**



  3. **stop_all**



停止 DMDSC 集群中所有节点服务命令，停止命令如下：

```
./DmServiceDMSERVER stop_all
```

停止服务成功，将打印 [ok] 字样。停止服务失败，将打印 [cancel] 字样，此时需要用户手动停止服务进程。

在 DMDSC 集群任一节点上执行 stop_all 命令均可停止集群中所有节点服务。

  4. **status**



查询服务状态，查询命令如下：

```
./DmServiceDMSERVER status
#打印信息如下
#服务DmServiceDMSERVER正在运行
DmServiceDMSERVER (pid pid_num) is running...
#服务DmServiceDMSERVER已停止
DmServiceDMSERVER is stopped
#服务DmServiceDMSERVER已停止，但PID文件还存在
DmServiceDMSERVER dead but pid file exists
```

  5. **restart**



重启服务命令，重启命令如下：

```
./DmServiceDMSERVER restart
#重启服务命令等同于
./DmServiceDMSERVER stop
./DmServiceDMSERVER start
```

  6. **condrestart**



condrestart 也属于重启命令，但增加了确认条件。如果服务正在运行，等同于 restart 命令。如果服务没有在运行，打印消息如下：

```
DmServiceDMSERVER service is not running.
```

## 1.5 脚本服务日志

以脚本启动的服务日志记录在/log 目录下的日志文件中。

根据不同的阶段，产生的日志分为两种情况：

一 各服务在启动和停止过程中产生的日志，均存储在/log 目录下的 dmsvc.log（Windows）或 dmsvc_sh.log（Linux）中。

二 各服务在运行过程中产生的日志，分别存储在/log 目录下各自对应的日志文件中。各脚本服务命名规则为：服务名.log。例如：DmAPService.log、DmServiceDMSERVER.log 等。为了防止日志过大，可通过 SAVE_PROC_OUT 脚本参数设置脚本服务运行过程中产生的日志是否记录在日志文件中。

## 2 注册与卸载服务

在现实使用环境中，如果 Linux(Unix)系统重启，用户希望在操作系统启动时能够自动启动某些应用程序的进程。为了达到这种功能就必须为这些应用程序编写服务脚本，并注册成操作系统服务。在以上的内容，已经讲解了 DM 所提供的服务脚本种类，参数修改和使用说明。以下内容将详细讲解把 DM 服务脚本注册成操作系统服务的方法。

### 2.1 注册服务

#### 2.1.1 使用脚本注册服务

DM 提供了将 DM 服务脚本注册成操作系统服务的脚本，同时也提供了卸载操作系统服务的脚本。注册和卸载脚本文件所在目录为安装目录的“/scripts/root”子目录下。

注册服务脚本为 dm_service_installer.sh，用户可以使用注册服务脚本将服务脚本注册成为操作系统服务，命令参数如下所示：

表2.1 注册服务参数

|**标志**|**参数**|**说明**|
|--|--|--|
|-t|服务类型|注册服务类型，支持一下服务类型：dmap、dmamon、dmserver、dmwatcher、dmmonitor、dmasmsvr、dmasmsvrm、dmcss、dmcssm|
|-p|服务名后缀|指定服务名后缀，生成的操作系统服务名为“服务脚本模板名称 + 服务名后缀”。此参数只针对 dmserver、dmwatcher、dmmonitor、dmasmsvr、dmasmsvrm、dmcss、dmcssm 服务脚本生效|
|-dm_ini|INI 文件路径|指定服务所需要的 dm.ini 文件路径|
|-watcher_ini|INI 文件路径|指定服务所需要的 dmwatcher.ini 文件路径|
|-monitor_ini|INI 文件路径|指定服务所需要的 dmmonitor.ini 文件路径|
|-dcr_ini|INI 文件路径|指定服务所需要的 dmdcr.ini 文件路径|
|-cssm_ini|INI 文件路径|指定服务所需要的 dmcssm.ini 文件路径|
|-signkey_pass|signkey.pem 密码|GmSSL  signkey.pem 证书文件的口令密码|
|-enckey_pass|enckey.pem 密码|GmSSL enckey.pem 证书文件的口令密码|
|-server|连接信息|指定服务器连接信息(IP:PORT)|
|-m|open 或 mount|指定数据库的启动模式 open 或 mount。此参数只针对 dmserver 服务类型生效，可选|
|-s|服务脚本文件路径|如果设置此参数则忽略除-y 外的其他所有参数。指定服务脚本全路径|
|-y|服务名|设置依赖服务，此选项只针对 systemd 服务环境下的 dmserver、dmasmsvr、dmasmsvrm 服务生效|
|-h|-|帮助|


使用方法：

操作之前，需要使用 root 系统用户登录或切换至 root 系统用户。

以下示例为创建数据库实例的系统服务，数据库 dm.ini 所在路径为/opt/dmdbms/data/DAMENG/dm.ini，系统服务后缀为 DMSERVER。

1. 通过指定服务类型注册服务

命令如下：

```
./dm_service_installer.sh -t dmserver -dm_ini /opt/dmdbms/data/DAMENG/dm.ini -p DMSERVER
```

注册脚本将注册达梦数据库实例服务 DmServiceDMSERVER。

2. 通过服务脚本文件注册服务

如果已存在修改完成的服务脚本模板。用户可通过此服务脚本文件注册操作系统随机启动服务。

命令如下：

```
./dm_service_installer.sh -s /opt/dmdbms/bin/DmServiceDMSERVER
```

#### 2.1.2 手动注册服务

在 Linux(Unix)系统中，init.d 和 systemd 是最常见的系统服务环境。下面以 dmserver（DmServcie）服务为例，将讲解在不同系统服务环境中手动注册服务的方法。

##### 2.1.2.1 init.d 环境

以下步骤将以 Red Hat Enterprise Linux 6 for x86-64 系统为例，由于不同操作系统系统命令不尽相同，具体步骤及操作请以本机系统为准，具体细节可向系统管理员咨询。

  1. 拷贝服务模板文件（DmService）到系统服务目录（/etc/rc.d/init.d），并将新文件命名为 DmServiceDMSERVER。



```
cp /opt/dmdbms/bin/service_template/DmService

/etc/rc.d/init.d/DmServiceDMSERVER

chmod 755 /etc/rc.d/init.d/DmServiceDMSERVER
```

  2. 修改服务脚本（/etc/rc.d/init.d/DmServiceDMSERVER）配置。



```
##修改DmServiceDMSERVER

INI_PATH=INI路径
```

其他参数请参考 [1.3 服务脚本参数修改](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_linux-script-manual.html#1.3%20%E6%9C%8D%E5%8A%A1%E8%84%9A%E6%9C%AC%E5%8F%82%E6%95%B0%E4%BF%AE%E6%94%B9)相关内容，并根据自身需求修改服务脚本配置。

  3. 创建启动服务的连接



```
ln -s /etc/rc.d/init.d/DmServiceDMSERVER /etc/rc.d/rc0.d/K02DmServiceDMSERVER

ln -s /etc/rc.d/init.d/DmServiceDMSERVER /etc/rc.d/rc1.d/K02DmServiceDMSERVER

ln -s /etc/rc.d/init.d/DmServiceDMSERVER /etc/rc.d/rc2.d/S98DmServiceDMSERVER

ln -s /etc/rc.d/init.d/DmServiceDMSERVER /etc/rc.d/rc3.d/S98DmServiceDMSERVER

ln -s /etc/rc.d/init.d/DmServiceDMSERVER /etc/rc.d/rc4.d/S98DmServiceDMSERVER

ln -s /etc/rc.d/init.d/DmServiceDMSERVER /etc/rc.d/rc5.d/S98DmServiceDMSERVER

ln -s /etc/rc.d/init.d/DmServiceDMSERVER /etc/rc.d/rc6.d/K02DmServiceDMSERVER
```

  4. 启动停止服务。



```
##停止服务

service DmServiceDMSERVER stop

##启动服务

service DmServiceDMSERVER start
```

##### 2.1.2.2 systemd 环境

以下步骤将以 Red Hat Enterprise Linux 7 for x86-64 系统为例，由于不同操作系统系统命令不尽相同，具体步骤及操作请以本机系统为准，具体细节可向系统管理员咨询。

1．拷贝服务模板文件（DmService）到目录（/opt/dmdbms/bin），并将新文件命名为 DmServiceDMSERVER。

```
cp /opt/dmdbms/bin/service_template/DmService

/opt/dmdbms/bin/DmServiceDMSERVER

chmod 755 /opt/dmdbms/bin/DmServiceDMSERVER
```

  2. 修改服务脚本（/opt/dmdbms/bin/DmServiceDMSERVER）配置。



```
##修改DmServiceDMSERVER

INI_PATH=INI路径
```

其他参数请参考 [1.3 服务脚本参数修改](https://eco.dameng.com/document/dm/zh-cn/pm/dm8_linux-script-manual.html#1.3%20%E6%9C%8D%E5%8A%A1%E8%84%9A%E6%9C%AC%E5%8F%82%E6%95%B0%E4%BF%AE%E6%94%B9)相关内容，并根据自身需求修改服务脚本配置。

  3. 创建系统服务配置文件（/usr/lib/systemd/system/DmServiceDMSERVER.service）。文件内容，如下所示：



```
[Unit]

Description=DmServiceDMSERVER

After=network.target remote-fs.target

[Service]

Type=forking

PIDFile=/opt/dmdbms/bin/pids/DmServiceDMSERVER.pid

ExecStart=/opt/dmdbms/bin/DmServiceDMSERVER start

ExecStop=/opt/dmdbms/bin/DmServiceDMSERVER stop

PrivateTmp=true

[Install]

WantedBy=multi-user.target
```

  4. 在使用服务前，需要启用服务。相关命令如下所示:



```
##启用服务

systemctl enable DmServiceDMSERVER

##停止服务

systemctl stop DmServiceDMSERVER

##启动服务

systemctl start DmServiceDMSERVER
```

### 2.2 卸载服务

服务卸载脚本文件为 dm_service_uninstaller.sh。用户可以使用服务卸载脚本删除已经注册成操作系统服务的 DM 服务脚本，参数如下表：

表2.2 删除服务参数

|**标志**|**参数**|**说明**|
|--|--|--|
|-n|服务名|指定服务名，删除指定的操作系统随机启动服务|
|-h|-|帮助|


使用方法：

操作之前，需要使用 root 系统用户登录或切换至 root 系统用户。

删除指定的操作系统服务。假设存在 DM 的操作系统随机启动服务 DmServiceDMSERVER，命令如下：

```
./dm_service_uninstaller.sh -n DmServiceDMSERVER
```

# 3 注意事项

## 3.1 systemctl

在 systemd 环境下，可使用 systemctl 命令进行系统服务的管理，但 systemctl 命令只能管理 systemd 环境下的系统服务。

详细用法说明如下：

  1. systemctl 只能停止由 systemctl 启动的系统服务及相关进程。
  2. systemctl 只能查询由 systemctl 启用/停止的系统服务的状态。
  3. systemctl 只能启用/禁用 systemd 环境下的系统服务。
  4. DMDSC 环境下，使用 systemctl 命令停止集群中的节点服务时，必须依次停止各个节点服务，即仅当一个服务成功停止后才能停止下一个服务。



其他情况可咨询系统管理员。

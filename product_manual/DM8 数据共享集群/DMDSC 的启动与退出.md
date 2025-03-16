

# DMDSC 的启动与退出

## 6.1 DMDSC 的启动

DMDSC 是基于共享存储的数据库集群系统，包含多个数据库实例，因此，与单节点的达梦数据库不同，DMDSC 集群需要在节点间进行同步、协调，才能正常地启动、关闭。启动 DMDSC 集群之前，必须先启动集群同步服务 DMCSS，如果使用了 DMASM 文件系统，则 DMASMSVR 服务也必须先启动。

如果 DMCSS 配置了 DMASMSVR/DMSERVER 自动拉起命令，可以先仅启动 DMCSS，然后启动 DMCSSM，在 DMCSSM 控制台执行命令"ep startup grp_asm"启动 DMASMSVR 集群，执行"ep startup grp_dsc"启动 DMSERVER 集群（其中 grp_asm / grp_dsc 为 DMASMSVR/DMSERVER 集群的组名）。

Linux 环境下，DMCSS/DMASMSVR/DMSERVER 可以配置成操作系统服务，每次开机自动启动，或者通过 Linux 命令"service XXX start/stop/restart"(XXX 为配置的服务名)完成服务的启动、关闭。服务脚本在达梦安装包里提供，可能还需要用户根据实际情况修改部分参数才能使用。关于服务脚本的详细介绍请参考《DM8_Linux 服务脚本使用手册》。

## 6.2 DMDSC 的退出

完整退出 DMDSC 集群的正确步骤为：依次退出 DMSERVER、DMASMSVR、DMCSS，未按照该顺序进行将导致节点异常退出。

### 6.2.1 使用 DMCSSM 监视器提供的命令退出集群

DMCSSM 提供了两种退出节点的命令：

```
ep stop group_name //退出指定组（所有节点）
ep halt group_name.ep_name //强制退出指定组的指定节点
```

使用 DMCSSM 监视器提供的命令退出集群时，可以先使用 show 命令查看 ASM/DB 组每个节点的自动拉起标记。

```
[CSS0] auto check = TRUE, global info:
[ASM0] auto restart = FALSE
[DSC02] auto restart = FALSE
[CSS1] auto check = TRUE, global info:
[ASM1] auto restart = FALSE
[DSC01] auto restart = FALSE
```

如果当前标记 auto restart 为 TRUE，则故障节点会尝试重新拉起，若此时直接进行退出会导致节点退出后再次被拉起，可以使用 set group_name auto restart off（其中 group_name 为指定的组名）关闭指定组的自动拉起功能。

使用 ep stop 命令退出 ASM 组时，需要保证 DB 组已经退出，否则会报错处理。该命令执行前，如果 CSS 对指定组的自动拉起功能是打开的，则会先通知 CSS 关闭对指定组的自动拉起功能，再通知指定组退出，避免命令执行成功后节点再次被自动拉起，可以省略 set group_name auto restart off 的步骤。执行 ep stop 命令，如果所有节点原本都是正常状态，那么所有节点同步正常退出；如果原先有节点处于故障退出状态，那么其余正常节点全部正常退出。

使用 ep halt 命令直接退出 ASM 组的节点时，DB 组的对应节点将一并异常退出。该命令不会关闭指定节点的自动拉起功能，需要先执行 set group_name auto restart off，再进行退出。

CSS 类型的组不允许执行 ep stop 和 ep halt 命令。

对于上述使用 ep stop 命令和 ep halt 命令进行退出的节点，用户可以分别通过以下方式查看 log 和节点的当前状态，判断所有节点是否同步正常退出，还是单节点故障退出。

可以在使用 ep stop 命令正常退出的节点对应的 log 中看到如下信息：

```
nsvr_shutdown->utl_stub_sys_close 100.
[for dem]SYSTEM SHUTDOWN SUCCESS.
DM Database Server shutdown successfully.
nsvr_notify_exit wakeup main thread to exit
```

在 DMCSSM 控制台使用 show [group_name]命令查看正常退出的节点状态，如下：

```
inst_status        vtd_status		is_ok		active   
SHUTDOWN       SHUTDOWN		OK			FALSE 
```

可以在使用 ep halt 命令强制退出的节点对应的 log 中可以看到如下信息：

```
[for dem]SYSTEM SHUTDOWN ABORT.
[for dem]SYSTEM SHUTDOWN ABORT.
VTD_CMD_SYS_HALT
VTD_CMD_SYS_HALT
```

在 DMCSSM 控制台使用 show [group_name]命令查看强制退出的节点状态，如下：

```
inst_status			vtd_status		is_ok		active    
OPEN				SYSHALT		ERROR		FALSE
```

### 6.2.2 使用控制台命令退出

DMASMSVR 和 DMSERVER 控制台执行 exit 命令，会通知所有其他节点一起退出；DMCSS 需要手动退出所有节点，没有同步功能。

DMDSC 环境 DIsql 登录实例执行 stop instance，实例会正常退出，不影响其他活动节点正常运行。、

### 6.2.3 通过 DEM 资源监控进行启停

使用 DEM 搭建的 DSC 集群可以通过进程或服务方式来进行启动或停止。下面以搭建 DSC 集群时注册服务的情况为例：

  1. 关闭节点的自动拉起



退出 DSC 集群前需要先关闭节点的自动拉起功能。可以在 DEM 的监控模块中选择数据库监控，选中需要修改的节点，点击编辑，在弹出窗口中修改数据库状态，关闭节点的自动拉起。

![图 6.1 DEM 监控模块下数据库监控选中节点并编辑.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202403221643355EP0OQCPJYE2DS5OFU)

图6.1 DEM监控模块下数据库监控选中节点并编辑

在修改数据库弹窗中取消勾选自动拉起选项，界面截图如下：

![图 6.2 DEM 修改数据库弹窗.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322164402Y5ATE0W9PXHQYHIMQW)

图6.2 DEM修改数据库弹窗

或者在启动集群节点时取消勾选自动拉起。步骤为：在监控模块选择数据库监控，选中需要调整的节点，选择“启停”，进入数据库启动与停止界面。

![图 6.3 DEM 监控模块下数据库监控中选择启停.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322164428GRPZCPRWBMNXLWX4X4)

图6.3 DEM监控模块下数据库监控中选择启停

在已选择数据库列表中，取消勾选自动拉起选项，点击确认即可关闭节点的自动拉起。

![图 6.4 取消勾选自动拉起.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322164453IIISSMTY1AO3D458QB)

图6.4 取消勾选“自动拉起”

  2. 以进程方式停止



在监控模块选择数据库监控，选中需要调整的集群或集群中的节点，选择“启停”，进入数据库启动与停止界面。

![图 6.5 DEM 监控模块下数据库监控中选择启停.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202403221645257QTSD97XPOJBK82942)

图6.5 DEM监控模块下数据库监控中选择启停

勾选需要退出的节点，选择以“进程”方式启动/停止后，点击停止按钮。

![图 6.6 以进程方式退出节点.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322164547ATL2UX1TH4IWZ8Z2QF)

图6.6 以进程方式退出节点

在已选择数据库列表弹框中预览已选数据库，然后点击“确定”开始退出节点。

![图 6.7 以进程方式退出节点执行完成.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322164610SXP05OBARX1M8G53ZL)

图6.7 以进程方式退出节点执行完成

  3. 以服务方式停止



在监控模块选择数据库监控，选中需要调整的集群或集群中的节点，选择“启停”，进入数据库启动与停止界面，截图可参考 2）以进程方式停止。

勾选需要退出的节点，选择以“服务”方式启动/停止后，点击停止按钮。

![图 6.8 以服务方式退出节点.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202403221646398HKOS9IBN1289PMPBT)

图6.8 以服务方式退出节点

在已选择数据库列表弹框中预览已选数据库，然后点击“确定”开始退出节点。

![图 6.9 以服务方式退出节点执行完成.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240322164700W9EC6FHENXR71JP7WY)

图6.9 以服务方式退出节点执行完成

需要注意的是，强杀停止可能导致数据库文件损坏；以进程/服务方式启动的节点，只能以相应的方式退出；正常退出集群应依次退出[DB]、[ASM]、[CSS]各组节点，直接退出[ASM]节点将导致对应的[DB]节点异常退出，直接退出[CSS]节点将导致对应的[ASM]、[DB]节点异常退出。

### 6.2.4 通过 systemctl 命令退出

使用 systemctl 命令启动节点，必须通过 systemctl 命令退出节点。

必须正常退出一个节点后再依次退出其余节点。

如果当前节点未完全退出就使用命令退出下一个节点，下一个节点将收到系统发出的 signal 15 异常退出，在 DMCSSM 控制台使用 show [group_name]命令查看节点状态。

```
inst_name      inst_status      vtd_status      is_ok       active  

DSC02         SHUTDOWN      OPEN           SYSHALT     FALSE   

DSC01          SHUTDOWN      STOP           ERROR      FALSE
```

1）启动命令

systemctl start \< 服务名 >

启动两节点 DSC。

```
systemctl start DmService_03_DSC01.service

systemctl start DmService_03_DSC02
```

两节点都启动成功后，在 DMCSSM 控制台使用 show [group_name]命令查看节点状态。

```
inst_name      inst_status      vtd_status      is_ok       active

DSC02         OPEN           WORKING       OK         TRUE  

DSC01          OPEN           WORKING       OK         TRUE 
```

2）节点顺序退出命令

systemctl stop \< 服务名 >

先退出 DSC01。

```
systemctl stop DmService_03_DSC01.service
```

DSC01 退出后，在 DMCSSM 控制台使用 show [group_name]命令查看节点状态。

```
inst_name      inst_status      vtd_status      is_ok       active

DSC02         OPEN           WORKING       OK         TRUE 

DSC01          SHUTDOWN      STOP           ERROR      FALSE
```

再退出 DSC02。

```
systemctl stop DmService_03_DSC02
```

DSC02 退出后，在 DMCSSM 控制台使用 show [group_name]命令查看节点状态。

```
inst_name      inst_status      vtd_status      is_ok       active  

DSC02         SHUTDOWN      SHUTDOWN      OK         FALSE   

DSC01          SHUTDOWN       STOP           ERROR      FALSE
```

### 6.2.5   通过 DMSERVER 脚本命令退出

使用 DMSERVER 脚本命令启动节点，必须通过 DMSERVER 脚本命令退出节点。

正常退出一个节点后再依次退出其余节点。

如果当前节点未完全退出就使用命令退出下一个节点，下一个节点将退出失败，仍然可以正常连接使用。

```
[root@test109 bin]# ./DmService_03_DSC02 stop

Stopping DmService_03_DES02:       [**FAILED**]

Kill the process(pid:67980) had timeout!
```

在 DMCSSM 控制台使用 show [group_name]命令查看节点状态，如下：

```
inst_name      inst_status      vtd_status      is_ok       active  

DSC02         OPEN           WORKING       OK         TRUE   

DSC01          SHUTDOWN      STOP           ERROR      FALSE
```

1) 启动命令

\<DMSERVER 脚本名 > start

先在 107 上执行启动命令。

```
[root@test107 bin]# ./ DmService_03_DSC01 start

Starting DmService_03_DSC01：connect dmasmtool（dmasmtoolm）sucessfully

上一次登录：2024年3月26日 14:05:35 星期二 CST

                                [OK]
```

再在 109 上执行启动命令。

```
[root@test109 bin]# ./ DmService_03_DSC02 start

Starting DmService_03_DSC02：connect dmasmtool（dmasmtoolm）sucessfully

上一次登录：2024年3月26日 14:05:53 星期二 CST

                                 [OK]
```

在 DMCSSM 控制台使用 show [group_name]命令查看节点状态。

```
inst_name      inst_status      vtd_status      is_ok       active

DSC02          OPEN             WORKING         OK          TRUE  

DSC01          OPEN             WORKING         OK          TRUE 
```

2) 节点顺序退出命令 stop

stop 命令退出单节点。

```
<DMSERVER脚本名> stop
```

先退出 DSC01。

```
[root@test107 bin]# ./DmService_03_DSC01 stop

Stopping DmService_03_DES01:       [OK]
```

DSC01 退出后，在 DMCSSM 控制台使用 show [group_name]命令查看节点状态。

```
inst_name      inst_status      vtd_status      is_ok       active  

DSC02          OPEN             WORKING         OK          TRUE   

DSC01          SHUTDOWN         STOP            ERROR       FALSE
```

再退出 DSC02。

```
[root@test109 bin]# ./DmService_03_DSC02 stop

Stopping DmService_03_DES02:       [OK]
```

DSC02 退出后，在 DMCSSM 控制台使用 show [group_name]命令查看节点状态。

```
inst_name      inst_status      vtd_status      is_ok       active  

DSC02          SHUTDOWN         SHUTDOWN        OK          FALSE   

DSC01          SHUTDOWN         STOP            ERROR       FALSE
```

3) 集群退出命令 stop_all

stop_all 命令退出集群所有节点，任一 DMSERVER 脚本都可以使用该命令，且只需要在其中一个节点上执行即可，不需要每个节点都执行。

```
<DMSERVER脚本名> stop_all
```

执行如下命令：

```
[root@test109 bin]# ./DmService_03_DSC02 stop_all

Stopping DmService_03_DES02:       [OK]
```

在 DMCSSM 控制台使用 show [group_name]命令查看节点状态，如下：

```
inst_name      inst_status      vtd_status       is_ok       active

DSC02          SHUTDOWN       SHUTDOWN           OK          FALSE

DSC01          SHUTDOWN       SHUTDOWN           OK          FALSE
```

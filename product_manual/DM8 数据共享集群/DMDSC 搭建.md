

# DMDSC 搭建

本章将举例说明 DMDSC 的搭建过程，并介绍搭建 DMDSC 的一些注意事项。

> **说明**
>
> 真实的生产环境中，建议至少配置两块共享磁盘，分别用来存放联机日志文件和数据文件。



在本章的磁盘准备中，介绍了 2 种共享存储磁盘固定方式：一种是使用 UDEV 工具，详见 [12.1.1 环境准备](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.1.1%20%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)或 [[12.2.1 环境准备](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.2.1%20%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.2.1%20%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)的磁盘准备中；一种是使用 multipath 软件，详见 [[12.2.1 环境准备](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.2.1%20%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.2.1%20%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)的磁盘准备中。因为两种方式效果一样，但 UDEV 方式搭建步骤更简单，所以推荐用户使用 UDEV 方式。

固定磁盘时建议将 DMDSC 系统中的共享存储磁盘固定到一个自行创建的独立目录下，因为 DMDSC 启动时会扫描磁盘目录下所有磁盘，使用独立目录可以避免扫描到目录中的其他无关磁盘文件而导致 DMDSC 启动失败。

## 12.1 基于 DMASM 的 DMDSC

本节用到的 DMASM 功能和 [10 DMASM 介绍](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-introduce.html)中一样。

一个典型的基于 DMASM 的 DMDSC 一般具备两个 DMDSC 节点。使用块设备的真实物理磁盘做共享存储，并使用 DMASM 来管理块设备。

磁盘数依据磁盘组数目不同而存在不同的最低磁盘数要求。本示例中 1 块 DCR 磁盘、1 块 VOTE 磁盘、1 个 DMDATA 磁盘组（含 1 块磁盘）和 1 个 DMLOG 磁盘组（含 1 块磁盘），共 4 块共享磁盘即可。

本示例以两节点为例搭建 DMDSC 环境。规划将 IP 为 107 的机器作为控制节点。

表12.1 集群规划



在 4 块共享磁盘中：2 块较小的磁盘（1G）用于创建 DCR、VOTE 磁盘；2 块较大的磁盘（2T）用于创建 ASM 磁盘组（数据磁盘组 DMDATA 和联机日志磁盘组 DMLOG）。

### 12.1.1   环境准备

从硬件、存储设备、操作系统、网络配置、用户准备、目录规划和磁盘准备这 7 个方面进行准备，下面分别介绍。

#### 12.1.1.1   硬件

IP 地址为 192.168.100.107 和 192.168.100.110 的两台相同配置的机器（380G 内存，2 块网卡）。

#### 12.1.1.2   存储设备

4 块由存储服务器提供的共享存储磁盘，存储服务器通过 SAN 交换机与两台机器相连提供存储服务。

#### 12.1.1.3   操作系统

eulerosv2r7.x86_64，使用 OS 默认的参数配置即可，同一套 DMDSC 环境要求其所在机器的操作系统相同。

#### 12.1.1.4   网络配置

eth0 网卡 192.168.102.x 为内网网段，eth1 网卡 192.168.100.x 为外网网段。

#### 12.1.1.5   用户准备

使用 root 用户创建用于搭建环境的用户和组，以下两机器都要做。

```
#新建组dmdba
groupadd dmdba
#在组dmdba（第一个）中创建一个用户dmdba（第二个）
useradd -g dmdba dmdba
#为用户dmdba设置密码
passwd dmdba
#用户输入密码，本例以******代替
******
```

#### 12.1.1.6   目录规划

两机器都要做。

使用 dmdba 用户创建用于 DSC 环境搭建的目录：/home/dmdba/dmdsc。

DM 执行码和工具存放于目录：/home/dmdba/dmdsc/bin。

配置文件存放于目录：107 机器/home/dmdba/dmdsc/data/DSC01 和 110 机器 /home/dmdba/dmdsc/data/DSC02。

#### 12.1.1.7   磁盘准备

两机器都要做。本节使用 UDEV 工具来固定磁盘。如果用户想使用 multipath 软件来固定磁盘，请参考 [12.2.1 环境准备](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.2.1%20%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)的磁盘准备。

  1. 通过 scsi_id 获取磁盘信息。



```
[root@test107 ~]# /usr/lib/udev/scsi_id -g -u /dev/sdgp
234074fa79680f431
[root@test110 ~]# /usr/lib/udev/scsi_id -g -u /dev/sdcf
234074fa79680f431
```

以上可以看出 107 的 /dev/sdgp 和 110 的 /dev/sdcf 对应的共享存储服务器上的同一块磁盘。同样取得其他 4 块磁盘的 scsi id 信息。

本步骤的目的是为了在下一步中将相同 scsi_id 的磁盘创建相同的盘符。例如，下一步骤中 RESULT=="234074fa79680f431"即为将 scsi_id 为 234074fa79680f431 的磁盘命名为 DCR 磁盘，并将 DCR 磁盘软链接到/dev_DSC2 目录下并命名为 DCR。

  2. 创建磁盘链接。



编写/etc/udev/rules.d/66-dmdevices.rules 配置信息，创建磁盘链接。书写时一个 KERNEL 为一行，不能换行。

```
## DCR磁盘配置,且在软链接之前创建文件夹 /dev_DSC2
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa79680f431",SYMLINK+="DCR", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name;mkdir -p /dev_DSC2; ln -s /dev/DCR /dev_DSC2/DCR'"
## VOTE 磁盘配置
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa746a006ac",SYMLINK+="VOTE", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/VOTE /dev_DSC2/VOTE'"
## DMDATA 磁盘配置
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa7f643c18b",SYMLINK+="DMDATA", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/DMDATA /dev_DSC2/DMDATA'"
## DMLOG 磁盘配置，且在搭建完成之后，将权限直接赋予 dmdba组的dmdba用户
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa795eb6057",SYMLINK+="DMLOG", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/DMLOG /dev_DSC2/DMLOG ; chown -R dmdba:dmdba /dev_DSC2
'"
```

从配置信息可以看出，通过 scsi id 找到磁盘后，在 /dev/下命名了磁盘信息，然后再做软链接到 /dev_DSC2 下。软链接的目录可以是/dev 下的独立目录，也可以是其他自行创建的独立目录，例如本例中的/dev_DSC2，目录路径必须以“/dev”开始，否则 DM 不会认为这是使用物理磁盘的真实环境。

之所以要软链接到独立目录，是因为 DMDSC 启动时会扫描目录下所有磁盘，如果目录中存在与当前 DMDSC 系统无关的磁盘，则 DMDSC 将启动失败。因此需要软链接到独立目录，保证该目录下仅包含当前 DMDSC 系统使用的共享存储磁盘，避免 DMDSC 启动失败。

以上配置在 107 和 110 机器完全相同，2 台机器均完成之后，进行下一步。

  3. 重启 systemd-udev-trigger 服务。



```
[root@test107 ~]# systemctl restart systemd-udev-trigger
[root@test110 ~]# systemctl restart systemd-udev-trigger
```

搭建之前磁盘权限信息如下：

```
[dmdba@test107 ~]# ls -lth /dev_DSC2/
总用量 0
lrwxrwxrwx 1 dmdba dmdba 32 10月 31 14:06 DMDATA -> /dev/DMDATA
lrwxrwxrwx 1 dmdba dmdba 32 10月 31 14:06 DMLOG -> /dev/DMLOG
lrwxrwxrwx 1 dmdba dmdba 27 10月 31 14:05 VOTE -> /dev/VOTE
lrwxrwxrwx 1 dmdba dmdba 27 10月 31 14:05 DCR -> /dev/DCR

[dmdba@test110 ~]# ls -lth /dev_DSC2/
总用量 0
lrwxrwxrwx 1 dmdba dmdba 32 10月 31 14:06 DMDATA -> /dev/DMDATA
lrwxrwxrwx 1 dmdba dmdba 32 10月 31 14:06 DMLOG -> /dev/DMLOG
lrwxrwxrwx 1 dmdba dmdba 27 10月 31 14:05 VOTE -> /dev/VOTE
lrwxrwxrwx 1 dmdba dmdba 27 10月 31 14:05 DCR -> /dev/DCR
```

### 12.1.2   搭建两节点 DMDSC

搭建的配置文件分别存放于 107 机器的/home/dmdba/dmdsc/data/DSC01 和 110 机器的/home/dmdba/dmdsc/data/DSC02 下。

  1. 准备配置文件 DMDCR_CFG.INI 文件，保存到 107 机器的/home/dmdba/dmdsc/data/DSC01 下。



```
DCR_N_GRP= 3
DCR_VTD_PATH=/dev_DSC2/VOTE
DCR_OGUID= 1071107589
[GRP]
  DCR_GRP_TYPE = CSS
  DCR_GRP_NAME = GRP_CSS
  DCR_GRP_N_EP = 2
  DCR_GRP_DSKCHK_CNT = 60

[GRP_CSS]
  DCR_EP_NAME = CSS0
  DCR_EP_HOST = 192.168.102.107
  DCR_EP_PORT = 9836

[GRP_CSS]
  DCR_EP_NAME = CSS1
  DCR_EP_HOST = 192.168.102.110
  DCR_EP_PORT = 9837

[GRP]
DCR_GRP_TYPE= ASM
DCR_GRP_NAME= GRP_ASM
DCR_GRP_N_EP= 2
DCR_GRP_DSKCHK_CNT= 60

[GRP_ASM]
DCR_EP_NAME= ASM0
DCR_EP_SHM_KEY= 64735
DCR_EP_SHM_SIZE= 512
DCR_EP_HOST= 192.168.102.107
DCR_EP_PORT= 5836
DCR_EP_ASM_LOAD_PATH= /dev_DSC2

[GRP_ASM]
DCR_EP_NAME= ASM1
DCR_EP_SHM_KEY= 54736
DCR_EP_SHM_SIZE= 512
DCR_EP_HOST= 192.168.102.110
DCR_EP_PORT= 5837
DCR_EP_ASM_LOAD_PATH= /dev_DSC2

[GRP]
DCR_GRP_TYPE= DB
DCR_GRP_NAME= GRP_DSC
DCR_GRP_N_EP= 2
DCR_GRP_DSKCHK_CNT= 60

[GRP_DSC]
DCR_EP_NAME= DSC01
DCR_EP_SEQNO= 0
DCR_EP_PORT= 6636

[GRP_DSC]
DCR_EP_NAME= DSC02
DCR_EP_SEQNO= 1
DCR_EP_PORT= 6637
```

  2. 在 107 机器使用 DMASMCMD 工具初始化所有磁盘。



```
create dcrdisk '/dev_DSC2/DCR' 'DCR'
create votedisk '/dev_DSC2/VOTE' 'VOTE'
create asmdisk '/dev_DSC2/DMDATA' 'DMDATA'
create asmdisk '/dev_DSC2/DMLOG'  'DMLOG'
init dcrdisk '/dev_DSC2/DCR' from '/home/dmdba/dmdsc/data/DSC01/dmdcr_cfg.ini' identified by '*****'
init votedisk  '/dev_DSC2/VOTE' from '/home/dmdba/dmdsc/data/DSC01/dmdcr_cfg.ini'
```

  3. 准备 DMASM 的 MAL 配置文件 DMASVRMAL.INI，保存到 107 的/home/dmdba/dmdsc/data/DSC01 下。更多关于 DMASVRMAL.INI 的介绍请参考 [9.4 MAL 系统配置文件](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-configuration-instructions.html#9.4%20MAL%20%E7%B3%BB%E7%BB%9F%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)。



```
[MAL_INST1]
MAL_INST_NAME= ASM0
MAL_HOST= 192.168.102.107
MAL_PORT= 4836
[MAL_INST2]
MAL_INST_NAME= ASM1
MAL_HOST= 192.168.102.110
MAL_PORT= 4837
```

如果 MAL_HOST 为 IPv6 地址，并且当前环境存在多块网卡，则需要用 % 号指定具体有效的网卡序号或网卡名称。假设当前机器上 IPv6 网卡名为 ens01，则 DMASVRMAL.INI 文件可以写为如下形式：

```
[MAL_INST1]
MAL_INST_NAME= ASM0
MAL_HOST= fe80::610e:9715:5ec6:4ea8%ens01
MAL_PORT= 4836
[MAL_INST2]
MAL_INST_NAME= ASM1
MAL_HOST= fe80::8261:5fff:fe1b:4620%ens01
MAL_PORT= 4837
```

  4. 为 DSC02 配置 DMASVRMAL.INI，和 DSC01 的 DMASVRMAL.INI 内容完全一样。保存到 110 的/home/dmdba/dmdsc/data/DSC02 下。如果 MAL_HOST 为 IPv6 地址，则将 DMASVRMAL.INI 复制到 DSC02 后，需要修改 MAL_HOST 中的网卡序号或网卡名称为当前环境下的网卡序号或网卡名称。假设当前机器上 IPv6 网卡名为 ens02，则 DMASVRMAL.INI 文件修改为如下形式：



```
[MAL_INST1]
MAL_INST_NAME= ASM0
MAL_HOST= fe80::610e:9715:5ec6:4ea8%ens02
MAL_PORT= 4836
[MAL_INST2]
MAL_INST_NAME= ASM1
MAL_HOST= fe80::8261:5fff:fe1b:4620%ens02
MAL_PORT= 4837
```

  5. 准备 DMDCR.INI 文件，保存到 107 的/home/dmdba/dmdsc/data/DSC01 下。



```
DMDCR_PATH                   = /dev_DSC2/DCR 
DMDCR_MAL_PATH               = /home/dmdba/dmdsc/data/DSC01/dmasvrmal.ini  
DMDCR_SEQNO                  = 0  
DMDCR_ASM_RESTART_INTERVAL   = 0  
DMDCR_ASM_STARTUP_CMD        = /home/dmdba/dmdsc/bin/dmasmsvr dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini  
DMDCR_DB_RESTART_INTERVAL    = 0  
DMDCR_DB_STARTUP_CMD         = /home/dmdba/dmdsc/bin/dmserver path=/home/dmdba/dmdsc/data/DSC01/DSC01_conf/dm.ini dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
DMDCR_LINK_CHECK_IP=192.168.1.88
```

设置了 DMDCR_LINK_CHECK_IP，须为 107 节点的 DMSERVER 和 DMASMSVR 赋予 ping 权限。

```
sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmserver
sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmasmsvr
```

  6. 为 DSC02 配置 DMDCR.INI，保存到 110 的/home/dmdba/dmdsc/data/DSC02 下。



```
DMDCR_PATH                   = /dev_DSC2/DCR 
DMDCR_MAL_PATH               = /home/dmdba/dmdsc/data/DSC02/dmasvrmal.ini  
DMDCR_SEQNO                  = 1   
DMDCR_ASM_RESTART_INTERVAL   = 0  
DMDCR_ASM_STARTUP_CMD        = /home/dmdba/dmdsc/bin/dmasmsvr dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini  
DMDCR_DB_RESTART_INTERVAL    = 0  
DMDCR_DB_STARTUP_CMD         = /home/dmdba/dmdsc/bin/dmserver path=/home/dmdba/dmdsc/data/DSC02/DSC02_conf/dm.ini dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini
DMDCR_LINK_CHECK_IP=192.168.1.88
```

设置了 DMDCR_LINK_CHECK_IP，须为 110 节点的 DMSERVER 和 DMASMSVR 赋予 ping 权限。

```
sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmserver
sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmasmsvr
```

  7. 启动 DMCSS、DMASM 服务程序。



主节点 107 启动 DMCSS：

```
./dmcss dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
```

另一节点 110 启动 DMCSS:

```
./dmcss dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini
```

主节点 107 启动 DMASMSVR：

```
./dmasmsvr dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
```

另一节点 110 启动 DMASMSVR:

```
./dmasmsvr dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini
```

  8. 使用 DMASMTOOL 工具创建 ASM 磁盘组，在 107 上登录创建：



```
./dmasmtool dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
```

创建一个 DATA 磁盘组和一个 LOG 磁盘组。

```
#创建DATA磁盘组
CREATE DISKGROUP DMDATA asmdisk '/dev_DSC2/DMDATA'
#创建LOG磁盘组
CREATE DISKGROUP DMLOG  asmdisk '/dev_DSC2/DMLOG'
```

  9. 在 107 机器上准备 DMINIT.INI 配置文件，保存到/home/dmdba/dmdsc/data/DSC01 目录下。



```
DB_NAME= dsc2
SYSTEM_PATH= +DMDATA/data
SYSTEM= +DMDATA/data/dsc2/system.dbf
SYSTEM_SIZE= 128
ROLL= +DMDATA/data/dsc2/roll.dbf
ROLL_SIZE= 128
MAIN= +DMDATA/data/dsc2/main.dbf
MAIN_SIZE= 128
CTL_PATH= +DMDATA/data/dsc2/dm.ctl
LOG_SIZE= 2048
DCR_PATH= /dev_DSC2/DCR
DCR_SEQNO= 0
AUTO_OVERWRITE= 2
PAGE_SIZE = 16
EXTENT_SIZE = 16

[DSC01]
CONFIG_PATH= /home/dmdba/dmdsc/data/DSC01/DSC01_conf
PORT_NUM = 6636
MAL_HOST= 192.168.102.107
MAL_PORT= 6536
LOG_PATH= +DMLOG/log/DSC01_log1.log
LOG_PATH= +DMLOG/log/DSC01_log2.log
[DSC02]
CONFIG_PATH= /home/dmdba/dmdsc/data/DSC02/DSC02_conf
PORT_NUM = 6637
MAL_HOST= 192.168.102.110
MAL_PORT= 6537
LOG_PATH= +DMLOG/log/DSC02_log1.log
LOG_PATH= +DMLOG/log/DSC02_log2.log
```

  10. 使用 DMINIT 初始化一个节点的数据库环境。



选择一个节点，启动 DMINIT 初始化数据库，这里以 107 为例。DMINIT 执行完成后，会在 config_path 目录（/home/dmdba/dmdsc/data/DSC01/DSC01_conf 和/home/dmdba/dmdsc/data/DSC02/DSC02_conf）下生成配置文件 DM.INI 和 DMMAL.INI。

```
./dminit control=/home/dmdba/dmdsc/data/DSC01/dminit.ini 
```

打印如下：

```
initdb V8
db version: 0x7000c
file dm.key not found, use default license!
License will expire on 2023-10-21
Normal of FAST
Normal of DEFAULT
Normal of RECYCLE
Normal of KEEP
Normal of ROLL
 log file path: +DMLOG/log/DSC01_log1.log
 log file path: +DMLOG/log/DSC01_log2.log
 log file path: +DMLOG/log/DSC02_log1.log
 log file path: +DMLOG/log/DSC02_log2.log
write to dir [+DMDATA/data/dsc2].
create dm database success. 2022-10-31 15:36:48
```

  11. 使用拷贝的方式配置另外一个节点的数据库环境。



将 107 上初始化库时产生的 DSC02 节点的配置文件（整个/home/dmdba/dmdsc/data/DSC02 文件夹）复制到 110 机器的/home/dmdba/dmdsc/data/DSC02/目录下。之后就可以启动数据库服务器了。

```
scp -r /home/dmdba/dmdsc/data/DSC02/* dmdba@192.168.100.110:/home/dmdba/dmdsc/data/DSC02/
```

需要注意的是，如果 DMMAL.INI 文件中的 MAL_HOST 为 IPv6 地址，则将配置文件复制到 110 机器上后，需要修改 MAL_HOST 中的网卡序号或网卡名称为当前环境下的网卡序号或网卡名称。

  12. 启动数据库服务器



分别启动两个节点的服务器。

```
107节点服务器启动：
cd /home/dmdba/dmdsc/bin
./dmserver dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini /home/dmdba/dmdsc/data/DSC01/DSC01_conf/dm.ini
110节点服务器启动：
cd /home/dmdba/dmdsc/bin
./dmserver dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini /home/dmdba/dmdsc/data/DSC02/DSC02_conf/dm.ini
```

  13. 配置并启动 DMCSSM 监视器。



现在我们搭建监视器，配置 DMCSSM.INI 文件。

DMCSSM 在任何机器上均可以启动，只要该台机器和 DMDSC 的真实机器网络是相通的，就可以监控 DMDSC 集群信息。

这里我们选择在 107 机器上搭建监视器。/home/dmdba/dmdsc/data 目录中 DMCSSM.INI 详细内容如下：

```
#和DMDCR_CFG.INI中的DCR_OGUID保持一致
CSSM_OGUID	=	1071107589 

#配置所有CSS的连接信息，
#与DMDCR_CFG.INI中CSS配置项的DCR_EP_HOST和DCR_EP_PORT保持一致
CSSM_CSS_IP = 192.168.102.107:9836
CSSM_CSS_IP = 192.168.102.110:9837

CSSM_LOG_PATH	=	/home/dmdba/dmdsc/data/cssm_log #监视器日志文件存放路径
CSSM_LOG_FILE_SIZE		=	32		#每个日志文件最大32M
CSSM_LOG_SPACE_LIMIT	=	0		#不限定日志文件总占用空间
```

创建 DMCSSM 的日志存放路径。

```
 mkdir /home/dmdba/dmdsc/data/cssm_log
```

启动 DMCSSM 集群监视器。

```
 ./dmcssm ini_path=/home/dmdba/dmdsc/data/dmcssm.ini
```

DMCSSM 启动之后，可使用 show 命令在 DMCSSM 监视器中查看集群状态信息。

```
show

monitor current time:2022-10-31 15:52:23, n_group:3
=================== group[name = GRP_CSS, seq = 0, type = CSS, Control Node = 0] ========================================

[CSS0] auto check = TRUE, global info:
[ASM0] auto restart = FALSE
[DSC01] auto restart = FALSE
[CSS1] auto check = TRUE, global info:
[ASM1] auto restart = FALSE
[DSC02] auto restart = FALSE

ep:	css_time               inst_name     seqno     port    mode         inst_status        vtd_status   is_ok        active       guid              ts          
	2022-10-31 15:52:22    CSS0          0         9836    Control Node OPEN               WORKING      OK           TRUE         376456396         376457755   
	2022-10-31 15:52:22    CSS1          1         9837    Normal Node  OPEN               WORKING      OK           TRUE         996354520         996355857   

=================== group[name = GRP_ASM, seq = 1, type = ASM, Control Node = 0] ========================================

n_ok_ep = 2
ok_ep_arr(index, seqno):
(0, 0)
(1, 1)

sta = OPEN, sub_sta = STARTUP
break ep = NULL
recover ep = NULL

crash process over flag is TRUE
ep:	css_time               inst_name     seqno     port    mode         inst_status        vtd_status   is_ok        active       guid              ts          
	2022-10-31 15:52:22    ASM0          0         5836    Control Node OPEN               WORKING      OK           TRUE         376477557         376478862   
	2022-10-31 15:52:22    ASM1          1         5837    Normal Node  OPEN               WORKING      OK           TRUE         996373364         996374653   

=================== group[name = GRP_DSC, seq = 2, type = DB, Control Node = 0] ========================================

n_ok_ep = 2
ok_ep_arr(index, seqno):
(0, 0)
(1, 1)

sta = OPEN, sub_sta = STARTUP
break ep = NULL
recover ep = NULL

crash process over flag is TRUE
ep:	css_time               inst_name     seqno     port    mode         inst_status        vtd_status   is_ok        active       guid              ts          
	2022-10-31 15:52:22    DSC01         0         6636    Control Node OPEN               WORKING      OK           TRUE         693225933         693226511   
	2022-10-31 15:52:22    DSC02         1         6637    Normal Node  OPEN               WORKING      OK           TRUE         1833938144        1833938716  
```

至此，基于 DMASM 的 DMDSC 已经搭建完成。

## 12.2 基于 DMASM 镜像的 DMDSC

本节用到的 DMASM 功能和 [11 DMASM 镜像介绍](https://eco.dameng.com/document/dm/zh-cn/pm/dmasm-image-introduction.html)中一样。

一个典型的基于 DMASM 镜像的 DMDSC 一般具备两个 DMDSC 节点。使用块设备的真实物理磁盘做共享存储，并使用 DMASM 镜像来管理块设备。磁盘数依据磁盘组数目和副本数不同而存在不同的最低磁盘数要求。例如：若全部为单副本则三个磁盘组（通常会有 SYSTEM、DATA、LOG 三个磁盘组）各只需要一块，共 3 块共享磁盘即可。

本实例以两节点三副本为例搭建镜像环境。规划将 IP 为 109 的机器作为控制节点。

表12.2 集群规划



本示例中使用 23 块共享磁盘，其中 5 块较小的磁盘（1G）用于创建 DCRV 系统磁盘组（SYSTEM），18 块较大的磁盘（2T）用于创建 ASM 磁盘组（数据磁盘组 DATA 和联机日志磁盘组 LOG）。

表12.3 集群磁盘规划



### 12.2.1   环境准备

从硬件、存储设备、操作系统、网络配置、用户准备、目录规划和磁盘准备这 7 个方面进行准备，下面分别介绍。

#### 12.2.1.1   硬件

IP 地址为 192.168.100.109 和 192.168.100.107 的两台相同配置的机器（380G 内存，2 块网卡）。

#### 12.2.1.2   存储设备

11 块由存储服务器提供的共享存储磁盘，存储服务器通过 SAN 交换机与两台机器相连提供存储服务。

#### 12.2.1.3   操作系统

eulerosv2r7.x86_64，使用 OS 默认的参数配置即可，同一套 DMDSC 环境要求其所在机器的操作系统相同。

#### 12.2.1.4   网络配置

eth0 网卡 192.168.102.x 为内网网段，eth1 网卡 192.168.100.x 为外网网段。

#### 12.2.1.5   用户准备

使用 root 用户创建用于搭建环境的用户和组，以下两机器都要做。

```
#新建组dmdba
groupadd dmdba
#在组dmdba（第一个）中创建一个用户dmdba（第二个）
useradd -g dmdba dmdba
#为用户dmdba设置密码
passwd dmdba
#用户输入密码，本例以******代替
******
```

#### 12.2.1.6   目录规划

两机器都要做。

使用 dmdba 用户创建用于镜像环境搭建的目录：/home/dmdba/dmdsc。

DM 执行码和工具存放于目录：/home/dmdba/dmdsc/bin。

配置文件存放于目录：109 机器/home/dmdba/dmdsc/data/DSC01 和 107 机器 /home/dmdba/dmdsc/data/DSC02。

#### 12.2.1.7   磁盘准备

两机器都要做。

本节提供两种磁盘固定方式：一是使用 UDEV 工具；二是使用 multipath 软件。用户采用其中一种即可。推荐用户使用第一种方式。

##### 12.2.1.7.1   UDEV 方式

  1. 通过 scsi_id 获取磁盘信息。



```
[root@test107 ~]# /usr/lib/udev/scsi_id -g -u /dev/sdgp
234074fa79680f431
[root@test110 ~]# /usr/lib/udev/scsi_id -g -u /dev/sdcf
234074fa79680f431
```

以上可以看出 107 的 /dev/sdgp 和 109 的 /dev/sdcf 对应的共享存储服务器上的同一块磁盘。同样取得其他 22 块磁盘的 scsi id 信息。

本步骤的目的是为了在下一步中将相同 scsi_id 的磁盘创建相同的盘符。例如，下一步骤中 RESULT=="234074fa79680f431"即为将 scsi_id 为 234074fa79680f431 的所有磁盘均命名为 DCRV1 磁盘，并将 DCRV1 磁盘软链接到 dev_DSC2/DCRV1 目录下。

  2. 创建磁盘链接。



编写/etc/udev/rules.d/66-dmdevices.rules 配置信息，创建磁盘链接。书写时一个 KERNEL 为一行，不能换行。

```
## DCRV1磁盘配置，且在软链接之前创建文件夹 /dev_DSC_HDD
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa79680f431",SYMLINK+="DCRV1", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name;mkdir -p /dev_DSC_HDD; ln -s /dev/DCRV1 /dev_DSC_HDD/DCRV1'"
## DCRV2磁盘配置
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa79680f432",SYMLINK+="DCRV2", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/DCRV2 /dev_DSC_HDD/DCRV2'"
## DCRV3磁盘配置
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa79680f433",SYMLINK+="DCRV3", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/DCRV3 /dev_DSC_HDD/DCRV3'"
## DCRV4磁盘配置
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa79680f434",SYMLINK+="DCRV4", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/DCRV4 /dev_DSC_HDD/DCRV4'"
## DCRV5磁盘配置
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa79680f435",SYMLINK+="DCRV5", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/DCRV5 /dev_DSC_HDD/DCRV5'"

## DMDATA 磁盘（MIR_HDD_1、MIR_HDD_2……MIR_HDD_9）配置
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa7f643c18b",SYMLINK+="MIR_HDD_1", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/MIR_HDD_1 /dev_DSC_HDD/MIR_HDD_1'"
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa7f643c18c",SYMLINK+="MIR_HDD_2", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/MIR_HDD_2 /dev_DSC_HDD/MIR_HDD_2'"
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa7f643c18d",SYMLINK+="MIR_HDD_3", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/MIR_HDD_3 /dev_DSC_HDD/MIR_HDD_3'"
……省略部分为使用同样的方法配置MIR_HDD_4……MIR_HDD_9
## DMLOG 磁盘配置（MIR_HDD_10、MIR_HDD_11……MIR_HDD_18），且在搭建完成之后，将权限直接赋予 dmdba组的dmdba用户
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa7f643c18e",SYMLINK+="DMLOG", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/MIR_HDD_4 /dev_DSC_HDD/ MIR_HDD_4 ; chown -R dmdba:dmdba /dev_DSC_HDD
'"
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa7f643c18f",SYMLINK+="MIR_HDD_5", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/MIR_HDD_5 /dev_DSC_HDD/MIR_HDD_5'"
KERNEL=="sd*",SUBSYSTEM=="block",PROGRAM=="/usr/lib/udev/scsi_id --whitelisted --replace-whitespace --device=/dev/$name",RESULT=="234074fa7f643c18g",SYMLINK+="MIR_HDD_6", OWNER="dmdba", GROUP="dmdba", MODE="0660", RUN+="/bin/sh -c 'chown dmdba:dmdba /dev/$name; ln -s /dev/MIR_HDD_6 /dev_DSC_HDD/MIR_HDD_6; chown -R dmdba:dmdba /dev_DSC_HDD'"
……省略部分为使用同样的方法配置MIR_HDD_13……MIR_HDD_18
```

从配置信息可以看出，通过 scsi id 找到磁盘后，在 /dev/下命名了磁盘信息，然后再做软链接到/dev_DSC_HDD 下。软链接的目录可以是/dev 下的独立目录，也可以是其他自行创建的独立目录，例如本例中的/dev_DSC_HDD，目录路径必须以“/dev”开始，否则 DM 不会认为这是使用物理磁盘的真实环境。

之所以要软链接到独立目录，是因为 DMDSC 启动时会扫描目录下所有磁盘，如果目录中存在与当前 DMDSC 系统无关的磁盘，则 DMDSC 将启动失败。因此需要软链接到独立目录，保证该目录下仅包含当前 DMDSC 系统使用的共享存储磁盘，避免 DMDSC 启动失败。

以上配置在 107 和 109 机器完全相同，2 台机器均完成之后，进行下一步。

  3. 重启 systemd-udev-trigger 服务。



```
[root@test107 ~]# systemctl restart systemd-udev-trigger
[root@test109 ~]# systemctl restart systemd-udev-trigger
```

搭建之前磁盘权限信息如下：

```
[dmdba@test107 ~]# ls -lth /dev_DSC_HDD/
总用量 0
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV1 -> /dev/DCRV1
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV2 -> /dev/DCRV2
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV3 -> /dev/DCRV3
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV4 -> /dev/DCRV4
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV5 -> /dev/DCRV5
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_1 -> /dev/MIR_HDD_1
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_2 -> /dev/MIR_HDD_2
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_3 -> /dev/MIR_HDD_3
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_4 -> /dev/MIR_HDD_4
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_5 -> /dev/MIR_HDD_5
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_6 -> /dev/MIR_HDD_6

[dmdba@test109 ~]# ls -lth /dev_DSC_HDD/
总用量 0
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV1 -> /dev/DCRV1
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV2 -> /dev/DCRV2
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV3 -> /dev/DCRV3
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV4 -> /dev/DCRV4
lrwxrwxrwx 1 dmdba dmdba 32 11月 7 14:06 DCRV5 -> /dev/DCRV5
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_1 -> /dev/MIR_HDD_1
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_2 -> /dev/MIR_HDD_2
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_3 -> /dev/MIR_HDD_3
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_4 -> /dev/MIR_HDD_4
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_5 -> /dev/MIR_HDD_5
lrwxrwxrwx 1 dmdba dmdba 27 11月 7 14:05 MIR_HDD_6 -> /dev/MIR_HDD_6
```

##### 12.2.1.7.2   multipath 方式

  1. 多路径软件准备：使用 multipath 做多路径。



mutipath 通常为 Linux 自带，如果没有安装配置，那么可以参照如下流程进行安装并配置。

```
yum install *multipath* -y
mpathconf --enable  # 生成/etc/multipath.conf
systemctl start multipathd
systemctl enable multipathd
```

mutipath 配置完成。

完成后新加到系统上的磁盘都会在/dev/mapper 中显示，可以通过命令快速查看磁盘信息：

```
fdisk -l | grep mapper
```

  2. 创建磁盘链接。



通过以上配置，共享存储服务器提供的磁盘被 mutipath 发现后会在/dev/mapper 下看到 mutipath 为其创建的链接。

```
[dmdba@test109 mapper]# ls
control       euleros-root  mpathad  mpathaf  mpathah  mpathaj  mpathap  mpathar  mpathat  mpathav  mpathax  mpathba  mpathbc  mpathbe  mpathbg  mpathbi  mpathbk  mpathbm  mpathbo  mpathbq  mpathbs  mpathh  mpathj  mpathl
euleros-home  euleros-swap  mpathae  mpathag  mpathai  mpathak  mpathaq  mpathas  mpathau  mpathaw  mpathay  mpathbb  mpathbd  mpathbf  mpathbh  mpathbj  mpathbl  mpathbn  mpathbp  mpathbr  mpathbt  mpathi  mpathk  mpathm
```

由于所有新加入的磁盘都会被放到该目录下，而 DMDSC 环境在启动时会读取配置目录中所有设备的头信息，因此为了避免多套环境间互相影响建议将其中所需要用到的磁盘单独再链接到一个新的目录，该目录可以是/dev 下的独立目录，也可以是其他自行创建的独立目录，目录路径必须以“/dev”开始，否则 DM 不会认为这是使用物理磁盘的真实环境。这里将磁盘链接到/dev_DSC_HDD 目录下并重命名。

这里使用 root 用户创建目录/dev_DSC_HDD，并赋权给 dmdba 用户，两机器都要做。dev_DSC_HDD 目录名称可以自取，以 dev 开头即可。

```
[root@test109 /]# mkdir /dev_DSC_HDD
[root@test109 /]# chown -R dmdba:dmdba dev_DSC_HDD
[root@test107 /]# mkdir /dev_DSC_HDD
[root@test107 /]# chown -R dmdba:dmdba dev_DSC_HDD
```

链接之前需要注意：因为同样的磁盘在两机器/dev/mapper 下名称可能不同，不同的磁盘在两机器/dev/mapper 下名称也可能相同。这会导致不同节点的 DMDSC 对同一名称的磁盘读取出不同的内容，简单通过两个磁盘名称一样无法判断是否为同一磁盘。因此需要通过命令/usr/lib/udev/scsi_id -g -u mpathx 读取磁盘通用唯一识别码（UUID），从而判断两机器上的链接分别对应哪块磁盘。例如下面这个例子可以判断 109 机器上的/dev/mapper/mpathba 与 107 机器上的/dev/mapper/mpathbb 为同一磁盘，因此在链接时应该将这两个磁盘更改为同一名称。

```
[dmdba@test109 mapper]# /usr/lib/udev/scsi_id -g -u mpathba
3600b34203c36824d1178d8057d7900d0
[dmdba@test107 mapper]# /usr/lib/udev/scsi_id -g -u mpathbb
3600b34203c36824d1178d8057d7900d0
#两机器上创建链接并更名为相同的名称
[dmdba@test109 dev_DSC_HDD]$ ln -s /dev/mapper/mpathba MIR_HDD_1
[dmdba@test107 dev_DSC_HDD]$ ln -s /dev/mapper/mpathba MIR_HDD_1
```

之后使用 root 用户依次将所有磁盘链接到/dev_DSC_HDD 下并更名为便于识别的名称。如下所示，DCRV 为 1G 的磁盘用于创建 DCRV 系统磁盘组，MIR_HDD_*为 2T 的磁盘用于创建 DATA 和 LOG 磁盘组。

109 机器上查看创建完成的链接如下：

```
[dmdba@test109 dev_DSC_HDD]$ ll
总用量 0
lrwxrwxrwx 1 dmdba dmdba 18 9月  20 10:48 dcrv1 -> /dev/mapper/mpathl
lrwxrwxrwx 1 dmdba dmdba 18 9月  20 10:48 dcrv2 -> /dev/mapper/mpathm
lrwxrwxrwx 1 dmdba dmdba 18 9月  20 10:48 dcrv3 -> /dev/mapper/mpathk
lrwxrwxrwx 1 dmdba dmdba 18 9月  20 10:48 dcrv4 -> /dev/mapper/mpathi
lrwxrwxrwx 1 dmdba dmdba 18 9月  20 10:48 dcrv5 -> /dev/mapper/mpathj
lrwxrwxrwx 1 dmdba dmdba 19 9月  20 10:58 MIR_HDD_1 -> /dev/mapper/mpathba
lrwxrwxrwx 1 dmdba dmdba 19 9月  20 10:48 MIR_HDD_2 -> /dev/mapper/mpathbb
lrwxrwxrwx 1 dmdba dmdba 19 9月  20 10:48 MIR_HDD_3 -> /dev/mapper/mpathbc
lrwxrwxrwx 1 dmdba dmdba 19 9月  20 10:48 MIR_HDD_4 -> /dev/mapper/mpathbd
lrwxrwxrwx 1 dmdba dmdba 19 9月  20 10:48 MIR_HDD_5 -> /dev/mapper/mpathbe
lrwxrwxrwx 1 dmdba dmdba 19 9月  20 10:48 MIR_HDD_6 -> /dev/mapper/mpathbf
```

107 机器上查看创建完成的链接如下：

```
[dmdba@test107 dev_DSC_HDD]$ ll
总用量 0
lrwxrwxrwx 1 dmdba dmdba 18 8月  30 08:47 dcrv1 -> /dev/mapper/mpathi
lrwxrwxrwx 1 dmdba dmdba 18 8月  30 08:47 dcrv2 -> /dev/mapper/mpathg
lrwxrwxrwx 1 dmdba dmdba 18 8月  30 08:48 dcrv3 -> /dev/mapper/mpathj
lrwxrwxrwx 1 dmdba dmdba 18 8月  30 08:48 dcrv4 -> /dev/mapper/mpathm
lrwxrwxrwx 1 dmdba dmdba 18 8月  30 08:48 dcrv5 -> /dev/mapper/mpathh
lrwxrwxrwx 1 dmdba dmdba 19 8月  29 17:45 MIR_HDD_1 -> /dev/mapper/mpathbb
lrwxrwxrwx 1 dmdba dmdba 19 8月  29 17:45 MIR_HDD_2 -> /dev/mapper/mpathbc
lrwxrwxrwx 1 dmdba dmdba 19 8月  29 17:45 MIR_HDD_3 -> /dev/mapper/mpathbd
lrwxrwxrwx 1 dmdba dmdba 19 8月  29 17:45 MIR_HDD_4 -> /dev/mapper/mpathbe
lrwxrwxrwx 1 dmdba dmdba 19 8月  29 17:45 MIR_HDD_5 -> /dev/mapper/mpathbf
lrwxrwxrwx 1 dmdba dmdba 19 8月  29 17:45 MIR_HDD_6 -> /dev/mapper/mpathbg
```

  3. 磁盘权限更改。



以上创建的链接虽然显示权限为 dmdba，但是链接对应的实际磁盘（通常为/dev/dm-*）权限还未更改。

首先，需要检查一下权限是否变动。如果系统重启，会导致权限变成 root，需要手动执行以下权限更新。使用 root 用户将权限赋给 dmdba：

```
[root@test109 dev_DSC_HDD]# chown dmdba:dmdba *
[root@test107 dev_DSC_HDD]# chown dmdba:dmdba *
```

然后，使用 ll 命令查看/dev/dm-*可以看到链接实际对应的磁盘权限应该已经变为 dmdba 用户了。以 109 上为例：

```
[dmdba@test109 dev_DSC_HDD]$ ll /dev/dm-*
brw-rw---- 1 dmdba      dmdba      253, 16 9月  19 14:53 /dev/dm-16
brw-rw---- 1 dmdba      dmdba      253, 18 9月  19 14:53 /dev/dm-18
brw-rw---- 1 dmdba      dmdba      253, 21 9月  20 09:00 /dev/dm-21
brw-rw---- 1 dmdba      dmdba      253, 22 9月  20 09:00 /dev/dm-22
brw-rw---- 1 dmdba      dmdba      253, 23 9月  20 09:00 /dev/dm-23
brw-rw---- 1 dmdba      dmdba      253, 24 9月  20 09:00 /dev/dm-24
brw-rw---- 1 dmdba      dmdba      253, 25 9月  20 09:00 /dev/dm-25
brw-rw---- 1 dmdba      dmdba      253, 26 9月  20 09:00 /dev/dm-26
brw-rw---- 1 dmdba      dmdba      253,  3 9月  19 14:53 /dev/dm-3
brw-rw---- 1 dmdba      dmdba      253,  4 9月  19 14:53 /dev/dm-4
brw-rw---- 1 dmdba      dmdba      253,  5 9月  19 14:53 /dev/dm-5
```

### 12.2.2   搭建两节点 DMDSC

搭建的配置文件分别存放于 109 机器的/home/dmdba/dmdsc/data/DSC01 和 107 机器的/home/dmdba/dmdsc/data/DSC02 下。

  1. 准备配置文件 DMDCR_CFG.INI 文件，保存到 109 机器的/home/dmdba/dmdsc/data/DSC01 下。与普通 DMDSC 环境相比，镜像环境中的 DCR_DISK_LOAD_PATH 不再指向具体的磁盘，而是 DCRV 磁盘所在的目录/dev_DSC_HDD。



```
DCR_N_GRP= 3
DCR_DISK_LOAD_PATH = /dev_DSC_HDD
DCR_OGUID= 509317566
[GRP]
  DCR_GRP_TYPE = CSS
  DCR_GRP_NAME = GRP_CSS
  DCR_GRP_N_EP = 2
  DCR_GRP_DSKCHK_CNT = 60

[GRP_CSS]
  DCR_EP_NAME = CSS0
  DCR_EP_HOST = 192.168.102.109
  DCR_EP_PORT = 7936

[GRP_CSS]
  DCR_EP_NAME = CSS1
  DCR_EP_HOST = 192.168.102.107
  DCR_EP_PORT = 7937

[GRP]
DCR_GRP_TYPE= ASM
DCR_GRP_NAME= GRP_ASM
DCR_GRP_N_EP= 2
DCR_GRP_DSKCHK_CNT= 60

[GRP_ASM]
DCR_EP_NAME= ASM0
DCR_EP_SHM_KEY= 54730
DCR_EP_SHM_SIZE= 512
DCR_EP_HOST= 192.168.102.109
DCR_EP_PORT= 7536

[GRP_ASM]
DCR_EP_NAME= ASM1
DCR_EP_SHM_KEY= 54731
DCR_EP_SHM_SIZE= 512
DCR_EP_HOST= 192.168.102.107
DCR_EP_PORT= 7537

[GRP]
DCR_GRP_TYPE= DB
DCR_GRP_NAME= GRP_DSC
DCR_GRP_N_EP= 2
DCR_GRP_DSKCHK_CNT= 60

[GRP_DSC]
DCR_EP_NAME= DSC01
DCR_EP_SEQNO= 0
DCR_EP_PORT= 7236

[GRP_DSC]
DCR_EP_NAME= DSC02
DCR_EP_SEQNO= 1
DCR_EP_PORT= 7237
```

  2. 在 109 机器使用 DMASMCMDM 工具初始化所有磁盘并创建 DCRV 系统磁盘组，DCRV 系统磁盘组中的磁盘数只能为 1，3，5 其中之一，这里以 5 块 DCRV 磁盘为例。



```
create dcrvdisk '/dev_DSC_HDD/DCRV1' 'dcrv1'
create dcrvdisk '/dev_DSC_HDD/DCRV2' 'dcrv2'
create dcrvdisk '/dev_DSC_HDD/DCRV3' 'dcrv3'
create dcrvdisk '/dev_DSC_HDD/DCRV4' 'dcrv4'
create dcrvdisk '/dev_DSC_HDD/DCRV5' 'dcrv5'
create asmdisk '/dev_DSC_HDD/MIR_HDD_1'  'data1'
create asmdisk '/dev_DSC_HDD/MIR_HDD_2'  'data2'
create asmdisk '/dev_DSC_HDD/MIR_HDD_3'  'data3'
create asmdisk '/dev_DSC_HDD/MIR_HDD_4'  'log1'
create asmdisk '/dev_DSC_HDD/MIR_HDD_5'  'log2'
create asmdisk '/dev_DSC_HDD/MIR_HDD_6'  'log3'
CREATE SYSTEM DISKGROUP  ASMDISK '/dev_DSC_HDD/dcrv1','/dev_DSC_HDD/dcrv2','/dev_DSC_HDD/dcrv3','/dev_DSC_HDD/dcrv4','/dev_DSC_HDD/dcrv5'  ATTRIBUTE CONFIG='/home/dmdba/dmdsc/data/DSC01/dmdcr_cfg.ini', passwd='aa123456'

```

可以启动 DMASMCMDM 工具，依次输入以上命令，或者将命令写入 asmcmd.txt 文件，执行 DMASMCMDM SCRIPT_FILE=asmcmd.txt，只需在一台机器执行即可。

用户没有指定脚本文件，则 DMASMCMDM 进入交互模式运行，逐条解析、运行命令；用户指定脚本文件（比如 asmcmd.txt），则以行为单位读取文件内容，并依次执行，执行完成以后，自动退出 DMASMCMDM 工具。脚本文件必须以“#asm script file”开头，否则认为是无效脚本文件；脚本中其它行以“#”表示注释；脚本文件大小不超过 1M。

  3. 准备 DMASM 的 MAL 配置文件 DMASVRMAL.INI，规则与普通的 DMDSC 环境一样，保存到 109 的/home/dmdba/dmdsc/data/DSC01 下。更多关于 DMASVRMAL.INI 的介绍请参考 [9.4 MAL 系统配置文件](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-configuration-instructions.html#9.4%20MAL%20%E7%B3%BB%E7%BB%9F%E9%85%8D%E7%BD%AE%E6%96%87%E4%BB%B6)。



```
[MAL_INST1]
MAL_INST_NAME= ASM0
MAL_HOST= 192.168.102.109
MAL_PORT= 7436
[MAL_INST2]
MAL_INST_NAME= ASM1
MAL_HOST= 192.168.102.107
MAL_PORT= 7437
```

  4. 为 DSC02 配置 DMASVRMAL.INI，和 DSC01 的 DMASVRMAL.INI 内容完全一样。保存到 107 的/home/dmdba/dmdsc/data/DSC02 下。需要注意的是，如果 MAL_HOST 为 IPv6 地址，则将 DMASVRMAL.INI 复制到 DSC02 后，需要修改 MAL_HOST 中的网卡序号或网卡名称为当前环境下的网卡序号或网卡名称，详细介绍请参考 [12.1.2 搭建两节点 DMDSC](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.1.2%20%E6%90%AD%E5%BB%BA%E4%B8%A4%E8%8A%82%E7%82%B9%20DMDSC)。
  5. 准备 DMDCR.INI 文件，保存到 109 的/home/dmdba/dmdsc/data/DSC01 下，同样注意 DMDCR_PATH 不再指向具体的磁盘而是目录/dev_DSC_HDD。



```
DMDCR_PATH= /dev_DSC_HDD
DMDCR_SEQNO= 0
DMDCR_MAL_PATH= /home/dmdba/dmdsc/data/DSC01/dmasvrmal.ini
DMDCR_ASM_RESTART_INTERVAL= 0
DMDCR_ASM_STARTUP_CMD= /home/dmdba/dmdsc/bin/dmasmsvrm dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
DMDCR_DB_RESTART_INTERVAL= 0
DMDCR_DB_STARTUP_CMD= /home/dmdba/dmdsc/bin/dmserver path=/home/dmdba/dmdsc/data/DSC01/DSC01_conf/dm.ini dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
DMDCR_IPC_CONTROL            = 2
DMDCR_LINK_CHECK_IP=192.168.1.88
```

设置了 DMDCR_LINK_CHECK_IP，须为 109 节点的 DMSERVER 和 DMASMSVRM 赋予 ping 权限。

```
sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmserver

sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmasmsvrm
```

  6. 为 DSC02 配置 DMDCR.INI，保存到 107 的/home/dmdba/dmdsc/data/DSC02 下。



```
DMDCR_PATH= /dev_DSC_HDD
DMDCR_SEQNO= 1
DMDCR_MAL_PATH= /home/dmdba/dmdsc/data/DSC02/dmasvrmal.ini
DMDCR_ASM_RESTART_INTERVAL= 0
DMDCR_ASM_STARTUP_CMD= /home/dmdba/dmdsc/bin/dmasmsvrm dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini
DMDCR_DB_RESTART_INTERVAL= 0
DMDCR_DB_STARTUP_CMD= /home/dmdba/dmdsc/bin/dmserver path=/home/dmdba/dmdsc/data/DSC02/DSC02_conf/dm.ini dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini
DMDCR_IPC_CONTROL            = 2
DMDCR_LINK_CHECK_IP=192.168.1.88
```

设置了 DMDCR_LINK_CHECK_IP，须为 107 节点的 DMSERVER 和 DMASMSVRM 赋予 ping 权限。

```
sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmserver
sudo setcap cap_net_raw,cap_net_admin=eip /home/dmdba/dmdsc/bin/dmasmsvrm
```

  7. 启动 DMCSS、DMASM 服务程序，在镜像环境中对应的执行码为 DMCSS，DMASMSVRM。



主节点 109 启动 DMCSS：

```
./dmcss dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
DMCSS V8
DMCSS IS READY
[2022-08-30 15:05:28:464] [CSS]: 设置EP CSS0[0]为控制节点
```

另一节点 107 启动 DMCSS：

```
./dmcss dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini
DMCSS V8
DMCSS IS READY
[2022-08-30 15:06:23:136] [CSS]: 设置EP CSS0[0]为控制节点
```

主节点 109 启动 DMASMSVRM：

```
./dmasmsvrm dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini

ASM SELF EPNO:0
DMASMSVRM V8
dmasmsvrm task worker thread startup
the ASM server is Ready.
check css cmd: START NOTIFY, cmd_seq: 24
check css cmd: EP START, cmd_seq: 25

ASM Control Node EPNO:0
check css cmd: EP OPEN, cmd_seq: 38
check css cmd: EP REAL OPEN, cmd_seq: 41
```

另一节点 107 启动 DMASMSVRM：

```
./dmasmsvrm dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini

ASM SELF EPNO:1
DMASMSVRM V8
dmasmsvrm task worker thread startup
the ASM server is Ready.
check css cmd: EP START, cmd_seq: 27

ASM Control Node EPNO:0
check css cmd: EP OPEN, cmd_seq: 39
check css cmd: EP REAL OPEN, cmd_seq: 42
```

  8. 使用 DMASMTOOLM 工具创建 ASM 磁盘组，选择一个节点启动 DMASMTOOLM 工具：



```
./dmasmtoolm dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
```

创建一个 DATA 磁盘组和一个 LOG 磁盘组，每个磁盘组包含 3 个副本。

```
#创建DATA磁盘组
ASM>CREATE DISKGROUP DMDATA HIGH REDUNDANCY FAILGROUP 'data1' asmdisk '/dev_DSC_HDD/MIR_HDD_1','/dev_DSC_HDD/MIR_HDD_2','/dev_DSC_HDD/MIR_HDD_3' FAILGROUP 'data2' asmdisk '/dev_DSC_HDD/MIR_HDD_4' ,'/dev_DSC_HDD/MIR_HDD_5','/dev_DSC_HDD/MIR_HDD_6'FAILGROUP 'data3' asmdisk '/dev_DSC_HDD/MIR_HDD_7','/dev_DSC_HDD/MIR_HDD_8','/dev_DSC_HDD/MIR_HDD_9' ATTRIBUTE AU_SIZE=32, REDO_SIZE=128
#创建LOG磁盘组
ASM>CREATE DISKGROUP DMLOG HIGH REDUNDANCY FAILGROUP 'data1' asmdisk '/dev_DSC_HDD/MIR_HDD_10','/dev_DSC_HDD/MIR_HDD_11','/dev_DSC_HDD/MIR_HDD_12' FAILGROUP 'data2' asmdisk '/dev_DSC_HDD/MIR_HDD_13','/dev_DSC_HDD/MIR_HDD_14' ,'/dev_DSC_HDD/MIR_HDD_15'FAILGROUP 'data3' asmdisk '/dev_DSC_HDD/MIR_HDD_16' ,'/dev_DSC_HDD/MIR_HDD_17','/dev_DSC_HDD/MIR_HDD_18'ATTRIBUTE AU_SIZE=32, REDO_SIZE=128
```

  9. 准备 DMINIT.INI 配置文件，保存到/home/dmdba/dmdsc/data/DSC01 目录下，这里创建 3 副本的数据和联机日志文件，且数据文件使用 32K 的条带化粒度，联机日志文件使用 64K 的条带化粒度。



```
DB_NAME= dameng
SYSTEM_PATH= +DMDATA/data
SYSTEM= +DMDATA/data/dameng/system.dbf
SYSTEM_SIZE= 128
ROLL= +DMDATA/data/dameng/roll.dbf
ROLL_SIZE= 128
MAIN= +DMDATA/data/dameng/main.dbf
MAIN_SIZE= 128
CTL_PATH= +DMDATA/data/dameng/dm.ctl
LOG_SIZE= 2048
DCR_PATH= /dev_DSC_HDD
DCR_SEQNO= 0
AUTO_OVERWRITE= 2
PAGE_SIZE = 32
EXTENT_SIZE = 16
DATA_MIRROR = 3
LOG_MIRROR = 3
DATA_STRIPING = 32
LOG_STRIPING = 64
[DSC01]
CONFIG_PATH= /home/dmdba/dmdsc/data/DSC01/DSC01_conf
PORT_NUM = 7236
MAL_HOST= 192.168.102.109
MAL_PORT= 7336
LOG_PATH= +DMLOG/log/DSC01_log1.log
LOG_PATH= +DMLOG/log/DSC01_log2.log
[DSC02]
CONFIG_PATH= /home/dmdba/dmdsc/data/DSC02/DSC02_conf
PORT_NUM = 7237
MAL_HOST= 192.168.102.107
MAL_PORT= 7337
LOG_PATH= +DMLOG/log/DSC02_log1.log
LOG_PATH= +DMLOG/log/DSC02_log2.log
```

  10. 使用 DMINIT 初始化一个节点的数据库环境。



选择一个节点，启动 DMINIT 初始化数据库，这里以 109 为例。DMINIT 执行完成后，会在 config_path 目录（/home/dmdba/dmdsc/data/DSC01/DSC01_conf 和/home/dmdba/dmdsc/data/DSC02/DSC02_conf）下生成配置文件 DM.INI 和 DMMAL.INI。

```
./dminit control=/home/dmdba/dmdsc/data/DSC01/dminit.ini
```

打印如下：

```
file dm.key not found, use default license!
License will expire on 2023-08-29
Normal of FAST
Normal of DEFAULT
Normal of RECYCLE
Normal of KEEP
Normal of ROLL
 log file path: +DMLOG/log/DSC01_log1.log
 log file path: +DMLOG/log/DSC01_log2.log
 log file path: +DMLOG/log/DSC02_log1.log
 log file path: +DMLOG/log/DSC02_log2.log
write to dir [+DMDATA/data/dameng].
create dm database success. 2022-08-30 15:52:39
initdb V8
db version: 0x7000c
```

  11. 使用拷贝的方式配置另外一个节点的数据库环境。



将 109 上初始化库时产生的 DSC02 节点的配置文件（整个/home/dmdba/dmdsc/data/DSC02 文件夹）复制到 107 机器的/home/dmdba/dmdsc/data/DSC02/目录下。之后就可以启动数据库服务器了。

```
scp -r /home/dmdba/dmdsc/data/DSC02/* dmdba@192.168.100.107:/home/dmdba/dmdsc/data/DSC02/
```

需要注意的是，如果 DMMAL.INI 文件中的 MAL_HOST 为 IPv6 地址，则将配置文件复制到 107 机器上后，需要修改 MAL_HOST 中的网卡序号或网卡名称为当前环境下的网卡序号或网卡名称。

  12. 启动数据库服务器。



分别启动两个节点的服务器。

```
109节点服务器启动：
/home/dmdba/dmdsc/bin/dmserver dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini /home/dmdba/dmdsc/data/DSC01/DSC01_conf/dm.ini
107节点服务器启动：
/home/dmdba/dmdsc/bin/dmserver dcr_ini=/home/dmdba/dmdsc/data/DSC02/dmdcr.ini /home/dmdba/dmdsc/data/DSC02/DSC02_conf/dm.ini
```

  13. 配置并启动 DMCSSM 监视器。



镜像的 DMCSSM 监视器搭建同普通的 DMDSC 集群相同，详情可以参考普通的 DMDSC DMCSSM 信息。

在/home/dmdba/dmdsc/data/路径下配置 DMCSSM.INI。

```
#和DMDCR_CFG.INI中的DCR_OGUID保持一致
CSSM_OGUID	=	509317566 

#配置所有CSS的连接信息，
#与DMDCR_CFG.INI中CSS配置项的DCR_EP_HOST和DCR_EP_PORT保持一致
CSSM_CSS_IP = 192.168.102.109:7936
CSSM_CSS_IP = 192.168.102.107:7937

CSSM_LOG_PATH	=	/home/dmdba/dmdsc/data/cssm_log #监视器日志文件存放路径，没有的话需要创建
CSSM_LOG_FILE_SIZE		=	32		#每个日志文件最大32M
CSSM_LOG_SPACE_LIMIT	=	0		#不限定日志文件总占用空间
```

创建 DMCSSM 的日志存放路径。

```
mkdir /home/dmdba/dmdsc/data/cssm_log
```

启动 DMCSSM 集群监视器。

```
./dmcssm ini_path=/home/dmdba/dmdsc/data/dmcssm.ini
```

DMCSSM 启动之后，可使用 show 命令在 DMCSSM 监视器中查看集群状态信息。

```
show
```

至此，基于 DMASM 镜像的 DMDSC 环境搭建完成。

## 12.3 检验是否搭建成功

DMDSC 搭建完成后，可通过下面三种方式检验是否搭建成功。下面的示例以 [12.2 基于 DMASM 镜像的 DMDSC](https://eco.dameng.com/document/dm/zh-cn/pm/dsc-build.html#12.2%20%E5%9F%BA%E4%BA%8E%20DMASM%20%E9%95%9C%E5%83%8F%E7%9A%84%20DMDSC) 为例进行操作。

一 使用 DMASMTOOLM 登录到 ASM 文件系统，通过 ls 命令查看磁盘组信息是否正确。

```
[dmdba@test109 bin]$ ./dmasmtoolm dcr_ini=/home/dmdba/dmdsc/data/DSC01/dmdcr.ini
ASM>ls
+
disk groups total [3]......
NO.1 	 name: DMDATA
NO.2 	 name: DMLOG
NO.3 	 name: SYS
```

二 通过 lsdsk 命令查看磁盘详细信息是否正确。

```
ASM>lsdsk
group DMDATA include 3 disks......
	 NO.1 disk : 
		 id: 0
		 name: DMASMd1
		 failgroup: data1
		 partner: 1 2
		 path: /dev_DSC_HDD/MIR_HDD_1
		 status: NORMAL
		 size: 71423AU
		 free_size: 71406AU
		 free_rate: 100.0%
		 create_time: 2022-09-20 13:56:34
		 modify_time: 2022-09-20 13:56:52
		 belong group: DMDATA
	 NO.2 disk : 
		……
group DMLOG include 3 disks......
	 NO.1 disk : 
		 id: 0
		 name: DMASMd4
		 failgroup: data1
		 partner: 1 2
		 path: /dev_DSC_HDD/MIR_HDD_4
		 status: NORMAL
		 size: 71423AU
		 free_size: 71406AU
		 free_rate: 100.0%
		 create_time: 2022-09-20 13:56:34
		 modify_time: 2022-09-20 13:57:03
		 belong group: DMLOG
	 NO.2 disk : 
		……
group SYS include 5 disks......
	 NO.1 disk : 
		 id: 0
		 name: DMASMdcrv1
		 failgroup: SYS_FGRP0
		 partner: 
		 path: /dev_DSC_HDD/dcrv1
		 status: NORMAL
		 size: 1024AU
		 free_size: 1006AU
		 free_rate: 100.0%
		 create_time: 2022-09-20 13:52:33
		 modify_time: 2022-09-20 13:52:33
		 belong group: SYS
	 NO.2 disk : 
		……
total 0 disks unused......
Used time: 5.106(ms).
```

三 通过 DIsql 工具登录服务器查看 V$ASMDISK 视图中的信息是否正确。

```
disql V8
SQL> conn sysdba@192.168.100.109:7236

服务器[192.168.100.109:7236]:处于普通打开状态
登录使用时间 : 10.755(ms)
SQL> SELECT GROUP_ID,DISK_ID,DISK_NAME,DISK_PATH,SIZE,FREE_MB,STATUS FROM V$ASMDISK;

行号       GROUP_ID    DISK_ID     DISK_NAME  DISK_PATH              SIZE        FREE_MB     STATUS
---------- ----------- ----------- ---------- ---------------------- ----------- 
1          0           0           DMASMd1    /dev_DSC_HDD/MIR_HDD_1 2285536     2284320     NORMAL
2          0           1           DMASMd2    /dev_DSC_HDD/MIR_HDD_2 2285536     2284288     NORMAL
3          0           2           DMASMd3    /dev_DSC_HDD/MIR_HDD_3 2285536     2284288     NORMAL
……
```



# dminit 高级主题

CONTROL 参数是 dminit 工具的高级功能，用于初始化数据库时指定初始化配置文件。初始化配置文件是一个保存了各数据文件路径和大小设置、所有 dminit 工具的命令行参数设置等信息的文本，名称由用户自己选取，例如：dminit.ini、abc.txt、dminit.ctl 等。各数据文件路径和大小设置相关参数详见表 5.1 和表 5.2；dminit 工具的命令行参数详见第 4 章。

dminit 工具使用 CONTROL 参数，就不能再指定其他参数，CONTROL 参数只能单独使用。

DM 既支持初始化单机数据库，又支持初始化 DSC 集群的数据库。操作非常简单，只要在使用 dminit 工具创建数据库的时候，使用 CONTROL 参数指定初始化配置文件即可。

例  初始化配置文件为 dminit.ini。

```
./dminit CONTROL=/home/data/dminit.ini
```

初始化配置文件（本章统一命名为 dminit.ini）内容如何书写，单机和 DSC 环境下略有不同，下面分别详细介绍。

## 5.1 初始化单机数据库

DM 支持初始化数据库到一个普通机器上或是一个共享存储上。本节以 DMASM 文件系统为例，初始化单机数据库。

涉及路径和文件大小的参数配置，详细请参考下表。

表5.1 初始配置文件参数介绍

|**参数**|**说明**|
|--|--|
|[node_instance]|本节点的实例名|
|system_path|初始数据库存放的相对路径。文件路径长度最大为 256 个字符。必选。 与 dminit 工具的参数 PATH 等价。选择了此参数，PATH 参数要省略|
|main|指定 MAIN.DBF 文件相对路径。 可选，若不指定使用 dminit 当前所在的工作目录； 若指定，则 main_size 不能为空|
|main_size|指定 MAIN.DBF 文件大小，取值范围为 32~65534，单位 MB。 如果使用裸设备，建议指定裸设备大小减一。 裸设备大小由用户保证，若超出实际大小可能会导致系统无法正常使用|
|system|指定 SYSTEM.DBF 文件相对路径。可选，若不指定使用 dminit 当前所在的工作目录； 若指定，则 system_size 不能为空|
|system_size|指定 SYSTEM.DBF 文件大小，取值范围为 32~65534，单位 MB。 如果使用裸设备，建议指定裸设备大小减一。 裸设备大小由用户保证，若超出实际大小可能会导致系统无法正常使用|
|roll|指定 ROLL.DBF 文件相对路径。可选，若不指定使用 dminit 当前所在的工作目录；若指定，则 roll_size 不能为空|
|roll_size|指定 ROLL.DBF 文件大小，取值范围为 32~65534，单位 MB。如果使用裸设备，建议指定为裸设备大小减一。 裸设备大小由用户保证，若超出实际大小可能会导致系统无法正常使用|
|huge_path|指定 MAIN 表空间的 HUGE 数据文件路径。可选，若不指定则默认在 SYSTEM_PATH/DB_NAME 路径下创建 HMAIN 目录，并将该目录路径作为 MAIN 表空间的 HUGE 数据文件路径|
|ctl_path|指定 dm.ctl 文件相对路径。可选，若不指定，则使用 dminit 当前所在的工作目录|
|ctl_size|指定 dm.ctl 文件大小|
|log_size|日志文件大小，若要使用裸设备必选，若为普通文件默认为 2048。取值范围为 64~2048，单位 MB|
|log_path|日志文件地址。可选，若不指定，则路径为 system_path，文件名为默认|
|auto_overwrite|文件存在时的处理方式，是否覆盖建库目录下所有同名文件。取值范围 0、1、2。 0：不覆盖，表示建库目录下如果没有同名文件，直接创建。如果遇到同名文件时，屏幕提示是否需要覆盖，由用户手动输入是与否（y/n，1/0），若选择不进行覆盖则初始化失败；1：部分覆盖，表示覆盖建库目录下所有同名文件；2：完全覆盖，表示先清理掉建库目录下所有文件再重新创建。缺省值为 0。可选|
|dcr_path|DCR 磁盘路径|
|dcr_seqno|连接 DMASM 节点节点号|
|temp_dbf_path|指定 TEMP.DBF 文件存放路径。可选，若不指定默认在 SYSTEM_PATH/DB_NAME 下；实例级参数，需要填写在[node_instance]之后|


例如，在 ASM 文件系统上创建数据库。参数 system_path、main、system、roll、ctl_path、log01、log02 都指定了 ASM 文件系统。以 + 开头的就是 ASM 文件系统的路径，例如 +DMDATA/data。单机配置文件（命名 dminit.ini）书写如下：

```
[DAMENG]

system_path = +DMDATA/data

main = +DMDATA/data/dsc/main.dbf

main_size = 2509

system = +DMDATA/data/dsc/system.dbf

system_size = 2509

roll = +DMDATA/data/dsc/roll.dbf

roll_size = 2509

ctl_path = +DMDATA/data/dsc/dm.ctl

log_size = 1024

log_path = +DMLOG/log/log01.log

log_path = +DMLOG/log/log02.log

auto_overwrite =2
```

## 5.2 初始化 DSC 集群的数据库

DM DSC 是一个单数据库、多实例的集群系统，数据库部署在共享存储上，供所有节点访问，具有高可用性、高性能、低成本等特性。DM 支持在 DSC 环境下创建数据库。

DSC 的初始化库配置文件 dminit.ini 中涉及到的参数，是在单机 dminit.ini 基础上，增加了 DSC 节点信息。同时，把单机 dminit.ini 中 node_instance 参数去掉，log_path 参数下放到每个 DSC 节点里。详细的节点信息请参考下表：

表5.2 DSC节点信息

|**参数**|**说明**|
|--|--|
|[dsc_instance]|本节点的实例名|
|config_path|配置文件存放路径|
|port_num|数据库实例端口号。必选|
|mal_host|mal 系统 IP。必选|
|mal_port|mal 系统端口号。必选|
|log_path|日志文件地址。可选，若不指定，则路径为 system_path，文件名为默认|


例如，初始化 DSC 集群的数据库。两节点的 DSC 集群是搭建在 ASM 文件系统上。DSC 环境初始化配置文件（命名 dminit.ini）书写如下：

```
db_name = dsc

system_path= +DMDATA/data

system= +DMDATA/data/dsc/system.dbf

system_size= 128

roll= +DMDATA/data/dsc/roll.dbf

roll_size= 128

main= +DMDATA/data/dsc/main.dbf

main_size= 128

ctl_path= +DMDATA/data/dsc/dm.ctl

ctl_size= 8

log_size= 256

dcr_path= /dev/raw/raw1			 ##dcr磁盘路径，目前不支持asm，只能是裸设备

dcr_seqno= 0

auto_overwrite= 1

[DSC0]			 				## dsc_instance跟dmdcr_cfg.ini中DB类型group中DCR_EP_NAME对应

config_path= /home/data/dsc0_config

port_num= 5236

mal_host= 10.0.2.101

mal_port= 9340

log_path= +DMLOG/log/dsc0_log01.log

log_path= +DMLOG/log/dsc0_log02.log

[DSC1]			 				## dsc_instance跟dmdcr_cfg.ini中DB类型group中DCR_EP_NAME对应

config_path= /home/data/dsc1_config

port_num= 5237

mal_host= 10.0.2.102

mal_port= 9341

log_path= +DMLOG/log/dsc1_log01.log

log_path= +DMLOG/log/dsc1_log02.log
```

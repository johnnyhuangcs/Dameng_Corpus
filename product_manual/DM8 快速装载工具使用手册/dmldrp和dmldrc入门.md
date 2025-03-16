

# dmldrp和dmldrc入门

dmldrp 和 dmldrc 只支持快速载入功能，不支持快速载出功能。

## 4.1 dmldrp

本章简单介绍如何启动轻量级快速装载工具服务器 dmldrp 及其支持的参数。通过阅读本章，读者可以了解 dmldrp 通过各参数能提供的各项功能。

### 4.1.1 启动 dmldrp

安装好 DM 数据库管理系统后，在安装目录“bin”子目录下可找到 dmldrp 执行文件。

启动操作系统的命令行窗口，进入 dmldrp 所在目录，可以准备启动 dmldrp 工具了。

dmldrp 的使用必须指定必要的参数，否则工具会报错“无效的参数个数”并退出。为 dmldrp 指定参数的格式为：

```
dmldrp keyword=value
```

例 启动一个端口号为 9898 的 dmldrp。

```
./dmldrp.exe port=9898
```

### 4.1.2 查看 dmldrp 参数

用户可以使用“dmldrp help”查看 dmldrp 版本信息和各参数的简单信息。

```
./dmldrp help
dmldrp V8
格式: ./dmldrp  KEYWORD=value
例程: ./dmldrp  
关键字       说明
---------------------------------------------------------------------
PORT        端口号(可选项, 默认为8336)
HELP        打印帮助信息
```

### 4.1.3dmldrp 参数简介

dmldrp 只有两个参数：端口号和 HELP。

●PORT

dmldrp 的端口号，用于 dmldrp 和 dmldrc 之间的通信连接。取值范围为 1024~65534。可选配置，不配置则使用默认的端口 8336。

● HELP

获取帮助信息。

## 4.2 dmldrc

本章简单介绍如何启动轻量级快速装载工具客户端 dmldrc 及其支持的参数。通过阅读本章，读者可以了解 dmldrc 通过各参数能提供的各项功能。

### 4.2.1 启动 dmldrc

安装好 DM 数据库管理系统后，在安装目录“bin”子目录下可找到 dmldrc 执行文件。

启动操作系统的命令行窗口，进入 dmldrc 所在目录，可以准备启动 dmldrc 工具了。

dmldrc 的使用必须指定必要的参数，否则工具会报错“无效的参数个数”并退出。为 dmldrc 指定参数的格式为：

```
dmldrc keyword=value [keyword=value ...]
```

例 dmldrc 连接一个端口号为 9898 的 dmldrp。

```
./dmldrc userid=SYSDBA/*****@localhost:5236 localhost:9898 control=\'/opt/data/test.ctl\'
```

### 4.2.2 查看 dmldrc 参数

dmldrc 使用较为灵活，参数较多，用户可以使用“dmldrc help”查看 dmldrc 版本信息和各参数的简单信息。

```
./dmldrc help
version: 05134284311-20240822-240257-10000
格式: ./dmldrc   KEYWORD=value

例程: ./dmldrc   SYSDBA/***** 192.168.0.1:8336

USERID 必须是命令行中的第一个参数
SERVER 必须是命令行中的第二个参数
字符串类型参数必须以引号封闭

关键字              说明（默认值）
--------------------------------------------------------------------------------
USERID              用户名/口令， 格式:{<username>[/<password>] | /}[@<connect_identifier>][<option>] [<os_auth>]
                    <connect_identifier> : [<svc_name> | host[:port] | <unixsocket_file>]
                    <option> : #{<extend_option>=<value>[,<extend_option>=<value>]...}
                               --此行外层{}是为了封装参数之用，书写时需要保留
                    <os_auth> : AS {SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO}
SERVER              dmldrp的IP地址和端口号。不需要指定参数名，直接指定host[:port]
CONTROL             控制文件，字符串类型
LOG                 日志文件，字符串类型 (fldr.log)
BADFILE             错误数据记录文件，字符串类型 (fldr.bad)
SKIP                初始忽略逻辑行数 (0)
LOAD                需要装载的行数 (ALL)
SET_IDENTITY        是否插入自增列 (FALSE)
SORTED              数据是否已按照聚集索引排序 (FALSE)
INDEX_OPTION        索引选项 (1)
                    1 不刷新二级索引，数据按照索引先排序，装载完后再
                    将排序的数据插入索引
                    2 不刷新二级索引，数据装载完成后重建所有二级索引
                    3 刷新二级索引, 数据装载的同时将数据插入二级索引
ERRORS              允许的最大数据错误数 (100)
CHARACTER_CODE      字符编码，字符串类型 (GBK, UTF-8, SINGLE_BYTE, EUC-KR)
MODE                装载方式，字符串类型 IN表示载入，OUT表示载出，
                    OUTORA表示载出ORACLE (IN)
CLIENT_LOB          大字段目录是否在本地 (FALSE)
LOB_DIRECTORY       大字段数据文件存放目录
LOB_FILE_NAME       大字段数据文件名称，仅导出有效 (dmfldr.lob)
BUFFER_NODE_SIZE    读入文件缓冲区的大小 (10MB),有效值范围1~2048
LOG_SIZE            日志信息缓冲区的大小 (1MB),有效值范围1~100
READ_ROWS           工作线程一次最大处理的行数 (100000)，最大支持2^26-10000
NULL_MODE           载入时NULL字符串是否处理为NULL
                    载出时空值是否处理为NULL字符串 (FALSE)
NULL_STR            载入时视为NULL值处理的字符串
SEND_NODE_NUMBER    运行时发送节点的个数 (20)，有效值范围16~65535
TASK_THREAD_NUMBER  处理用户数据的线程数目，默认与处理器核数量相同，有效值范围1~128
BLDR_NUM            服务器BLDR数目 (64),有效值范围1~1024
BDTA_SIZE           bdta的大小 (5000)，有效值范围100~10000
COMPRESS_FLAG       是否压缩bdta (FALSE)
MPP_CLIENT          MPP环境，是否本地分发 (TRUE)
SINGLE_FILE         MPP/DPC环境，是否只生成单个数据文件(FALSE)
LAN_MODE            MPP/DPC环境，是否以内网模式装载数据(FALSE)
UNREP_CHAR_MODE     非法字符处理选项(0),为0时表示跳过该数据行，为1时表示使用(*)替换错误字节
SILENT              是否静默方式装载数据(FALSE)
OCI_DIRECTORY       OCI动态库所在的目录
DATA                指定数据文件路径
ENABLE_CLASS_TYPE   允许用户导入CLASS类型数据 (FALSE)
FLUSH_FLAG          提交时是否立即刷盘 (FALSE)
SINGLE_HLDR_HP      是否使用单个HLDR装载HUGE水平分区表 (TRUE)
EP                  指定需要发送数据的站点序号列表，仅向MPP/DPC环境导入数据时有效
PARALLEL            是否开启并行装载(FALSE)
SQL                 使用自定义查询语句，仅导出模式有效
TABLE               导入/出表
ROW_SEPERATOR       行分隔符
FIELD_SEPERATOR     列分隔符
COMMIT_OPTION       提交选项(0), 0:每发送一批数据后提交, 1:发送完所有数据后提交
APPEND_OPTION       追加选项(0), 0: 追加方式, 1: 替代方式, 2: 插入方式
COLNAME_HEADING     是否在导出文件头中打印列名(FALSE)
IGNORE_AIMLESS_DATA 是否忽略无目标数据(FALSE)
LOB_AS_VARCHAR      是否将CLOB作为VARCHAR进行导入导出(FALSE)
LOB_AS_VARCHAR_SIZE 将CLOB作为VARCHAR进行导入导出时, lob数据最大大小(10MB)
LOG_LEVEL           记录错误数据信息级别(3), 0: 不记录 1: 只记录到log文件 2: 只记录到bad文件 3: 记录到log和bad文件
FLDR_INI            配置文件路径，字符串类型
RECONN              自动重连次数(0)
RECONN_TIME         自动重连等待时间(5), 单位(s), 有效值范围(1~10000)
PLOG                ldrp日志文件，字符串类型 (ldrp.log)
COMPATIBLE_MODE     兼容模式(0), 0: 不做兼容, 1: 兼容DB2, 2: 兼容MYSQL
PRIORITY_ENCLOSE    DB2模式下, enclose优先级最高
HELP                打印帮助信息
```

### 4.2.3dmldrc 参数简介

由于 dmldrp 和 dmldrc 只支持快速载入功能，因此 dmldrc 的 MODE 参数只支持指定为 IN，其余除 SERVER、RECONN 和 PLOG 参数外，均与 dmfldr 参数用法一致。本节只介绍 SERVER、RECONN 和 PLOG 参数，其它参数用法请参考 [2.3dmfldr 参数简介](https://eco.dameng.com/document/dm/zh-cn/pm/getting-started-dmfldr.html#2.3%20dmfldr%20%E5%8F%82%E6%95%B0%E7%AE%80%E4%BB%8B)。

● SERVER

用于指定 dmldrp 的 IP 地址和端口号。必选参数，且必须位于参数位置的第二个。

该参数不需要指定参数名 SERVER，直接指定 host[:port]。端口号缺省为 8336，本机 IP 可写做 localhost。

例 在 dmldrc 中指定 IP 和端口号分别为 192.168.1.64 和 9898 的 dmldrp。MODE 缺省为 IN。

```
./dmldrc userid=SYSDBA/*****@localhost:5236 192.168.1.64:9898 control=\'/opt/data/test.ctl\'
```

● RECONN

指定自动重连次数，范围为 0~4294967295。缺省为 0，表示不进行自动重连。

与 dmfldr 不同的是，dmldrc 不支持断点重连，dmldrc 重连成功后总是重新装载全部数据。重连失败时根据 COMMIT_OPTION 采取相应的处理策略：若 COMMIT_OPTION=0，则未提交数据全部回滚；若 COMMIT_OPTION=1，则所有数据全部回滚。由于 dmldrc 最终重连成功时会重新装载全部数据，因此当 COMMIT_OPTION=0 时，如果发生重连，可能会出现数据重复装载的情况。

● PLOG

用于指定 dmldrp 端生成的日志文件及其路径，字符串类型，缺省为 ldrp.log。为可选参数。

dmldrc 中的 LOG 参数为 dmldrc 端的日志配置路径，PLOG 为 dmldrp 端的日志配置路径；BADFILE 为 dmldrp 端的错误日志文件配置路径，dmldrc 端不记录错误日志文件。

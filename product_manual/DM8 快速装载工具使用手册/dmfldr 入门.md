

# dmfldr 入门

本章简单介绍如何启动 dmfldr 和 dmfldr 支持的参数简介。通过阅读本章，读者可以了解 dmfldr 通过各参数能提供的各项功能，不过要想熟练灵活地使用 dmfldr 还需要继续阅读下一章。

## 2.1 启动 dmfldr

安装好 DM 数据库管理系统后，在安装目录的“bin”子目录下可找到 dmfldr 执行文件。

启动操作系统的命令行窗口，进入“dmfldr”所在目录，可以准备启动 dmfldr 工具了。

dmfldr 的使用必须指定必要的参数，否则工具会报错“无效的参数个数”并退出。为 dmfldr 指定参数的格式为：

```
dmfldr keyword=value [keyword=value ...]
```

USERID 是启动 dmfldr 必须要指定的参数，且 USERID 必须是第一个参数。其它参数用户需根据实际情况选取。

例 1 使用 USERID 和 CONTROL 参数启动 dmfldr，完成载入。

```
./dmfldr USERID=SYSDBA/***** MODE=\'IN\' CONTROL=\'/opt/data/test.ctl\'
```

例 2 使用 USERID 和 CONTROL 参数启动 dmfldr，完成载出。

```
./dmfldr USERID=SYSDBA/***** MODE=\'OUT\' CONTROL=\'/opt/data/test.ctl\'
```

例 3 使用 USERID 、OUT、TABLE 和 DATA 参数启动 dmfldr。将 test 表中数据载出到 test.txt 文件中。

```
./dmfldr USERID=SYSDBA/***** @localhost:5236 MODE=\'OUT\'  TABLE=test   DATA=\'/opt/data/test.txt\'
```

例 4 使用 USERID、IN、TABLE 和 DATA 参数启动 dmfldr。将 test.txt 文件中数据载入到 test 表中。

```
./dmfldr USERID=SYSDBA/***** @localhost:5236 MODE=\'IN\'  TABLE=test   DATA=\'/opt/data/test.txt\'
```

除了在启动命令行中直接指定 dmfldr 参数值外，用户还可以通过 CONTROL 参数设置控制文件中的 OPTIONS 选项来指定 dmfldr 参数值，也可以在 dmfldr.ini 配置文件中指定 dmfldr 参数值。

> **注意**
>
> dmfldr参数值的优先选择顺序为：优先为命令行参数，其次为控制文件中指定的参数值，最后为dmfldr.ini配置文件中指定的参数值。  
> 有以下特殊情况：  
>  使用控制文件时，装载表和数据文件优先使用控制文件中的值；  
>  不使用控制文件时，装载表和数据文件优先使用命令行中的值。



## 2.2 查看 dmfldr 参数

dmfldr 使用较为灵活，参数较多，用户可以使用“dmfldr help”查看 dmfldr 版本信息和各参数的简单信息。

```
./dmfldr help
version: 05134284311-20240822-240257-10000
格式: ./dmfldr   KEYWORD=value

例程: ./dmfldr   SYSDBA/***** CONTROL='/opt/data/fldr.ctl'

USERID 必须是命令行中的第一个参数
字符串类型参数必须以引号封闭

关键字              说明（默认值）
--------------------------------------------------------------------------------
USERID              用户名/口令， 格式:{<username>[/<password>] | /}[@<connect_identifier>][<option>] [<os_auth>]
                    <connect_identifier> : [<svc_name> | host[:port] | <unixsocket_file>]
                    <option> : #{<extend_option>=<value>[,<extend_option>=<value>]...}
                               --此行外层{}是为了封装参数之用，书写时需要保留
                    <os_auth> : AS {SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO}
CONTROL             控制文件，字符串类型
LOG                 日志文件，字符串类型 (fldr.log)
BADFILE             错误数据记录文件，字符串类型 (fldr.bad)
SKIP                初始忽略逻辑行数 (0)
LOAD                需要装载的行数 (ALL)
ROWS                提交频次 (50000), DIRECT为FALSE有效
DIRECT              是否使用快速方式装载 (TRUE)
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
BLOB_TYPE           BLOB类型字段数据值的实际类型，字符串类型 (HEX_CHAR)
                    HEX表示值为十六进制，HEX_CHAR表示值为十六进制字符类型
                    仅在direct=FALSE有效
OCI_DIRECTORY       OCI动态库所在的目录
DATA                指定数据文件路径
ENABLE_CLASS_TYPE   允许用户导入CLASS类型数据 (FALSE)
FLUSH_FLAG          提交时是否立即刷盘 (FALSE)
IGNORE_BATCH_ERRORS 是否忽略错误数据继续导入 (FALSE)
SINGLE_HLDR_HP      是否使用单个HLDR装载HUGE水平分区表 (TRUE)
EP                  指定需要发送数据的站点序号列表，仅向MPP/DPC环境导入数据时有效
PARALLEL            是否开启并行装载(FALSE)
SQL                 使用自定义查询语句，仅导出模式有效
SQLFILE             自定义查询语句所在文件，仅导出模式有效
TABLE               导入/出表
ROW_SEPERATOR       行分隔符
FIELD_SEPERATOR     列分隔符
COMMIT_OPTION       提交选项(0), 0:每发送一批数据后提交, 1:发送完所有数据后提交
APPEND_OPTION       追加选项(0), 0: 追加方式, 1: 替代方式, 2: 插入方式
COLNAME_HEADING     是否在导出文件头中打印列名(FALSE)
IGNORE_AIMLESS_DATA 是否忽略无目标数据(FALSE)
LOB_AS_VARCHAR      是否将CLOB作为VARCHAR进行导入导出(FALSE)
LOB_AS_VARCHAR_SIZE 将CLOB作为VARCHAR进行导入导出时, lob数据最大大小(10MB)
LOG_LEVEL           记录错误数据信息级别(3), 0: 不记录 1: 只记录到log文件 2: 只记录到bad文件 3: 记录到log和bad文件 4: 错误仅输出到屏幕
FLDR_INI            配置文件路径，字符串类型
RECONN              自动重连次数(0)
RECONN_TIME         自动重连等待时间(5), 单位(s), 有效值范围(1~10000)
WIDTH               设置列数据宽度
SEDF                被替换的字符列表
SEDT                用于替换的字符列表
ESCAPE              转义符
EXPORT_MODE         MPP/DPC环境, 使用sql语句时的导出模式(0), 0: 普通模式 1: 快速模式, LOCAL方式连接
BAD_FILE_MODE       错误数据文件模式(0), 0: 普通模式 1: 纯数据模式
COMPATIBLE_MODE     兼容模式(0), 0: 不做兼容 1: 兼容DB2 2: 兼容MYSQL
PRIORITY_ENCLOSE    DB2模式下, enclose优先级最高
HELP                打印帮助信息
```

## 2.3 dmfldr 参数简介

dmfldr 的众多参数中，USERID 是必选参数，且位置必须是第一位。其余参数均为可选参数，需要时指定，指定时也无顺序要求。

  * **USERID**



USERID 用于指定数据库的连接信息。必选参数，且必须位于参数位置的第一个。

语法如下：

```
{{<username>[/<password>]} | /}[@<connect_identifier>][] [<os_auth>] <connect_identifier> ::=<svc_name> | {<host>[:<port>]} | <unixsocket_file>
<option>::= #{ <extend_option>=<value>{,<extend_option>=<value>} } //此行外层{}为了封装参数之用，书写时需要保留
<os_auth>::= AS {SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO}
```

{{*\<USERNAME>[/\<password>]} | /}：\<username>[/\<*password>]为用户名和密码。普通登录方式时用户名必写。/表示采用操作系统身份验证方式登录，此时无需指定用户名和密码，即使指定也会被忽略。

\<svc_name>：服务名。

*\<host>[:\<*port>]：服务器 IP 地址和端口号。缺省情况下默认为本地服务器和端口号 LOCALHOST:5236。当服务器为本机时，SERVER:PORT 可直接写 LOCALHOST。当连接其他服务器时，SERVER:PORT 需写上 IP 地址和 PORTNUM，例如：192.168.0.248:8888。

\<unixsocket_file>：专门用于在 LINUX 系统中，当服务器与客户端之间使用 UNIXSOCKET（UNIX DOMAIN SOCKET - IPC）协议通信时，指定客户端连接的 socket 文件路径。

例 使用 USERID 启动 dmfldr。

```
./dmfldr SYSDBA/*****@/home/test/foo.sock##{inet_type=UNIXSOCKET} control=\'/home/test/dmfldr_test.ctrl\'
```

*\<option> 为扩展选项，用法为 \<extend_option>=\<*value>。所有 value 值不能包含空格，不能包含特殊的符号，如引号等。书写扩展选项时需要用引号#"{ }"进行封装，例如：#"{inet_type=tcp,mpp_type=local}"。

表2.1 extend_option扩展选项

|**extend_option**|**value**|
|-------------|-------------|
|mpp_type|MPP 登录属性，此属性的设置对非 MPP 系统没有影响。取值 GLOBAL 和 LOCAL，默认为 GLOBAL。GLOBAL 表示 MPP 环境下建立的会话为全局会话，对数据库的导入导出操作在所有节点进行；LOCAL 表示 MPP 环境下建立的会话为本地会话，对数据库的导入导出操作只在本地节点进行|
|inet_type|网络通信协议类型。取值 TCP/UDP/IPC/UNIXSOCKET/RDMA，缺省为 TCP。使用 UDP 协议时，服务器需要设置 INI 参数 ENABLE_UDP 非 0；使用 IPC 协议时，会忽略 IP 地址和端口号，直接连接本机上 INI 参数 ENABLE_IPC=1 的服务器，一台机器上只能有一个服务器设置 ENABLE_IPC=1；使用 UNIXSOCKET 协议时，无需指定 IP 地址和端口号，但服务器需要配置 INI 参数 UNIX_SOCKET_PATHNAME 指定 socket 文件路径，客户端连接服务器时也需要指定 socket 文件路径，二者必须一致，仅 LINUX 环境下支持 UNIXSOCKET；使用 RDMA 协议时，需要安装并配置 RDMA 网卡，连接数据库时需要指定 RDMA 网卡配置的 IP 地址以及服务器的端口号|
|ssl_path|通信加密的 SSL 数字证书路径，缺省为不使用加密。数字证书路径由用户自己创建，将相应的证书需放入该文件夹中。其中服务器证书必须与 dmserver  目录同级，客户端目录可以任意设置|
|ssl_pwd|通信加密的 SSL 数字证书密码。和 ssl_path 一起使用|


例 一个包含扩展选项的、完整的例子。

```
./dmfldr SYSDBA/*****@192.168.1.64:5236##"{mpp_type=local,inet_type=tcp}" control=\'/home/test/dmfldr_test.ctrl\' log=\'/home/test/log/fldr.log\'
```

SSLPATH@SSLPWD：通信加密的 SSL 数字证书路径和密码，缺省为不使用加密。数字证书路径由用户自己创建，将相应的证书需放入该文件夹中。其中服务器证书必须与 dmserver 目录同级，客户端目录可以任意设置。

AS \<SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO>：操作系统身份验证。用户可以通过将操作系统用户加入到操作系统的 dmdba|dmsso|dmauditor 用户组来使用操作系统用户登录数据库，分别对应数据库的 SYSDBA|SYSSSO|SYSAUDITOR 用户。还可以通过将操作系统用户加入到操作系统的 dmusers 用户组来使用操作系统用户登录数据库，对应数据库的同名用户。AUTO 表示按顺序自动匹配数据库用户类型。操作系统身份验证无需输入用户名和密码，若输入用户名和密码将会被忽略。关于操作系统身份验证的更多详细介绍请参考手册《DM8 安全管理》。

  * **CONTROL**



控制文件的路径，字符串类型。

控制文件用于指定数据文件的路径和数据格式。

在数据载入时，dmfldr 根据控制文件指定的格式来解析数据文件；导出数据时，dmfldr 也会根据控制文件指定的列分隔符、行分隔符等生成数据文件。控制文件中还可以指定其他的一些 dmfldr 参数值。缺省列分隔符是 |，行分隔符是回车。对控制文件格式的详细介绍见下一章。

此参数为可选参数。

  * **LOG**



dmfldr 的日志文件路径，字符串类型。默认日志文件名为 fldr.log。日志文件记录了 dmfldr 运行过程中的工作信息、错误信息以及统计信息。

此参数为可选参数。

  * **NULL_STR**



指定数据文件中 NULL 值的表示字符串，字符串类型，默认忽略此参数。

若设置了 NULL_STR，则此参数值将成为数据文件中 NULL 值的唯一表示方式。NULL_STR 区分字符串大小写，并且长度不允许超过 128 个字节。

此参数为可选参数。

  * **BADFILE**



记录错误数据的文件路径，字符串类型。默认的错误文件名为 fldr.bad。文件记录的信息为数据文件中存在格式错误的行数据以及转换出错的行数据。

用户也可以通过设置控制文件中的 OPTIONS 选项来指定错误数据文件的路径，同时也可以在控制文件的 LOAD 节点中指定错误数据文件的路径。BADFILE 选项的最终值的优先选择顺序为命令行中指定的参数值，控制文件的 LOAD 节点选项，控制文件的 OPTIONS 选项，dmfldr.ini 配置文件中指定的参数值，用户可以同时对四种设置方式中的一个或多个设置，但最终的值只取一个。

此参数为可选参数，作用于 MODE 为 IN 的情况下，当 MODE 为 OUT 时无效。

  * **SKIP**



跳过数据文件起始的逻辑行数，整型数值。默认的跳过起始行数为 0 行。如果用户指定了多个文件，且起始文件中的行数不足 SKIP 所指定的行数，则 dmfldr 工具会扫描下一个文件直至累加的行数等于 SKIP 所设置的行数或者所有文件都已扫描结束。

在 MPP 和分布计算集群 DPC 环境下，由于数据分散在各个节点，每个节点返回数据的速度也不一样，无法确定 SKIP 的数据，因此 SKIP 参数在 MPP 和分布计算集群 DPC 环境下无效。若在 MPP 环境下使用本地连接，则 SKIP 参数仍有效。

  * **LOAD**



装载的最大行数，整型数值。默认的最大装载行数为数据文件中的所有行数。LOAD 指定的值不包括 SKIP 指定的跳过的行数。

在 MPP 和分布计算集群 DPC 环境下，由于数据分散在各个节点，每个节点返回数据的速度也不一样，无法确定 LOAD 的数据，因此 LOAD 参数在 MPP 和分布计算集群 DPC 环境下无效。若在 MPP 环境下使用本地连接，则 LOAD 参数仍有效。

  * **ROWS**



每次提交的行数，整型数值。默认的提交行数为 50000 行。提交行数的值表示提交到服务器的行数，并不一定代表按照数据文件中的数据顺序的行数。用户可以根据实际情况调整每次提交的行数，以达到性能的最佳点。

此参数为可选参数，作用于 MODE 为 IN 且 DIRECT 为 FALSE 的情况下，其他情况下无效。

  * **DIRECT**



数据装载方式，布尔值。缺省为 TRUE。DIRECT 指定了装载的方式，当为 TRUE 时，dmfldr 选择快速的载入模式，通过数据的转换和数据的封装直接对 B 树进行操作，省去了普通插入方式下各个操作符之间的跳转，提升了装载的效率，但对于约束的检查等由用户保证，dmfldr 将不处理有约束冲突的数据。当为 FALSE 时，dmfldr 选择普通的插入方式装载数据，可以保证数据的正确性和约束的有效性，效率比前者要低。

当 DIRECT 为 FALSE 时，dmfldr 每次将 1000 行数据作为一批进行装载（但实际行数可能有所变化，因为系统会根据数据大小自动调整）。 因此在 DIRECT 为 FALSE 的场景下， 一行数据错误将引发一批数据装载失败。

解析控制文件中的同义词时，若 DIRECT 为 TURE，在指定模式下没有匹配到同义词时会再尝试匹配公共同义词；DIRECT 为 FALSE 时仅根据指定的模式匹配模式下的同义词或不指定模式时匹配公共同义词。

此参数为可选参数，作用于 MODE 为 IN 的情况下，当 MODE 为 OUT 时无效。

  * **SET_IDENTITY**



设置自增列选项，布尔值。缺省为 FALSE。如果指定 SET_IDENTITY 选项值为 TRUE，则 dmfldr 将把从数据文件中读取的自增列值作为目标值插入数据库表中，用户应当保证每一行的自增列的值符合自增列的规则。如果 SET_IDENTITY 选项值设置为 FALSE，则 dmfldr 将忽略数据文件中对应自增列的值，服务器将自动生成自增列的值插入每一行的对应列。

此参数为可选参数，仅在 MODE 为 IN、DIRECT 为 TRUE 且非 DMDPC 环境下有效，DMDPC 环境下暂不支持自增列装载。

  * **SORTED**



数据是否已经按照聚集索引排序，布尔值。缺省为 FALSE。如果设置为 TRUE，则用户必须保证数据已按照聚集索引排序完成，并且如果表中存在数据，需要插入的数据索引值要比表中数据的索引值大，服务器在做插入操作时顺序进行插入。如果设置为 FALSE，则服务器对于每条记录进行定位插入。

此参数为可选参数，作用于 MODE 为 IN 且 DIRECT 为 TRUE 的情况下，对于其他情况此参数无效。

  * **INDEX_OPTION**



索引的设置选项，整型数值。缺省为 1。INDEX_OPTION 的可选项有 1、2 和 3。

1 代表服务器装载数据时先不刷新二级索引，而是将新数据按照索引预先排序，在装载完成后，再将排好序的数据插入索引。

2 代表服务器在快速装载过程中不刷新二级索引数据，只在装载完成时重建所有二级索引。

3 代表服务器使用追加模式来进行二级索引的插入，在数据装载的过程中，同时进行二级索引的插入。若未禁用全局索引，则在非 DPC 环境下，全局索引可通过设置 INDEX_OPTION 为 3 进行插入。

此参数为可选参数，作用于 MODE 为 IN 且 DIRECT 为 TRUE 的情况下，对于其他情况此参数无效。

  * **ERRORS**



最大的容错个数，整型数值。取值范围为 0~4294967295，缺省为 100。当 dmfldr 在装载过程中出现错误的个数超过了 ERRORS 所设置的数目，则 dmfldr 将当前时间点已成功装载且未提交的所有正确数据提交到服务器，随后结束装载。如果载入过程中不允许出现错误则可以设置 ERRORS 为 0，如果允许所有的错误出现，则可以设置 ERRORS 为一个非常大的数。ERRORS 所统计的错误包含在数据转换和数据插入过程中所产生的数据错误。

此参数为可选参数，作用于 MODE 为 IN 的情况下，当 MODE 为 OUT 时无效。

  * **CHARACTER_CODE**



数据文件中数据的编码格式，字符串类型。默认与当前机器编码格式一致。CHARACTER_CODE 的可选项有 GBK、GB18030、UTF-8、SINGLE_BYTE 和 EUC-KR 五种：GBK 和 GB18030 对应中文编码；UTF-8 对应 UTF-8 国际编码；SINGLE_BYTE 对应单字节字符编码；EUC-KR 对应韩文字符集。

在 MODE 为 IN 的情况下，指定编码格式为 SINGLE_BYTE 时，dmfldr 将不做字符完整性检查，按单字节顺序读取每个字符。指定编码格式为 GBK、GB18030 或 UTF-8 时，dmfldr 将对每一个字符做字符的完整性检查，确保数据的正确性。

用户在使用 dmfldr 时可以根据不同的数据文件调整编码的格式确保装载的正确性，同时如果可以确保数据文件中的数据为单字节字符，则指定 SINGLE_BYTE 的载入效率将优于指定其他字符集的情况。

当 MODE 为非 IN 时，不支持 SINGLE_BYTE。

  * **MODE**



dmfldr 的装载模式，字符串类型。缺省值为 IN。MODE 的可选项有 IN、OUT、OUTORA 和 LOCAL_FILE 四种。

IN 模式指从数据文件中将数据装载入数据库，这种模式下控制文件的格式对应为数据文件中现有数据的格式；OUT 模式指从数据库中将数据导出到数据文件，这种模式下控制文件所指定的格式为数据存放在数据文件中的格式。需要说明的是在 OUT 模式下，如果指定了多个数据文件，则 dmfldr 最终只会将数据写入第一个数据文件。

OUT 模式下的控制文件与 IN 模式下的控制文件格式相同，用户可以通过使用同一个文件进行载入和导出数据。

OUTORA 模式表示导出 ORACLE 表的数据，此模式下暂不支持带有大字段表的导出。

LOCAL_FLIE 表示数据导入时，不向服务器发送数据，而是将数据写入成本地文件。通常用于多站点场景下，将数据按站点分类重新组织数据。

  * **CLIENT_LOB**



指明 LOB_DIRECTORY 表示的目录是否是客户端本地目录，布尔类型，缺省值为 FALSE。

CLIENT_LOB 仅在 MODE 为 IN 且 DIRECT 为 TRUE 时有效，指明在载入大字段对象数据时，LOB_DIRECTORY 参数指定的目录是否是客户端本地目录。若为 TRUE，表示目录为客户端本地目录，且此时待载入的大字段长度不得超过 2G，否则无法成功载入大字段；若为 FALSE，表示目录为 DM 服务器主库所在机器上的目录。

  * **LOB_DIRECTORY**



指明 dmfldr 使用的大字段数据存放的目录，字符串类型。

当 MODE 为 OUT 时，dmfldr 生成大字段对应的数据文件，文件名由 LOB_FILE_NAME 指定，并存放于 LOB_DIRECTORY 指定的目录，如果未指定 LOB_DIRECTORY 则存放于指定的导出数据文件同一目录。对于 MODE 为 OUT 的情况，此参数为可选参数。

当 MODE 为 IN 且 DIRECT 为 TRUE 时，此时数据载入若涉及到大字段对象，需要用户指定大字段数据文件。若 CLIENT_LOB 为 TRUE，LOB_DIRECTORY 应指定大字段数据文件所在的客户端本地目录；若 CLIENT_LOB 为 FALSE，此时此参数为必选参数，用户必须先把大字段数据文件传送到 DM 服务器主库所在的机器上，然后使用 LOB_DIRECTORY 指明大字段数据文件位于服务器主库所在机器上的目录；载入时 dmfldr 工具会从客户端本地读取控制文件和指定的数据文件。

当 MODE 为 IN 且 DIRECT 为 FALSE 时，此参数无效。

  * **LOB_FILE_NAME**



指明 dmfldr 导出大字段数据的文件名，字符串类型，缺省为 dmfldr.lob。

当 MODE 为 OUT 时，dmfldr 生成大字段对应的数据文件名由 LOB_FILE_NAME 指定，若未指定默认为 dmfldr.lob，文件存放于 LOB_DIRECTORY 指定的目录。

此参数为可选参数，作用于 MODE 为 OUT 的情况下，当 MODE 为 IN 时无效。

  * **BLOB_TYPE**



指定 BLOB 数据值的实际类型，字符串类型。可选项为 HEX 表示值为十六进制，HEX_CHAR 表示值为十六进制字符，默认为 HEX_CHAR。

此参数为可选参数，只在 DIRECT 参数为 FALSE 的情况下有效。

> **注意**
>
> 不支持使用包含BLOB列的表生成的导出数据文件在导入时指定BLOB_TYPE为HEX_CHAR。



  * **BUFFER_NODE_SIZE**



指定读取文件缓冲区页大小，整数类型，单位为 MB，取值范围为 1~2048，缺省为 10。

BUFFER_NODE_SIZE 的值越大，缓冲区的页越大，每次读取的数据就越多，每次发送到服务器的数据也就越多，效率越高。但其大小受 dmfldr 客户端内存大小限制。

  * **LOG_SIZE**



指定日志系统中缓冲区结点的大小，整数类型，单位为 MB，取值范围为 1~100，缺省为 1。

在装载过程中产生的错误数据和错误描述信息，将首先写入到日志系统的缓冲区结点中，缓冲区写满后，将缓冲区中的错误数据刷盘到错误数据文件中，并将缓冲区中的错误描述信息刷盘到日志文件中。若错误数据或错误描述信息的大小超过缓冲区结点的大小，则系统直接将该错误数据或错误描述信息刷盘到错误数据文件或日志文件中。

  * **READ_ROWS**



指定读取缓冲区每次读取的最大行数，整数类型，取值范围为 0~(2^26-10000)，缺省为 100000。

在某些情况下，1MB 的 BUFFER_NODE_SIZE 读入的数据行数很大，而后续操作处理不了这么大的行数，此时可以用 READ_ROWS 来限制处理的行数。dmfldr 取 READ_ROWS 和 BUFFER_NODE_SIZE 中较小的值作为一次处理的行数。

  * **NULL_MODE**



指定载入和导出数据时对 NULL 字符串和空值的处理方式，布尔类型，缺省为 FALSE。

若 NULL_MODE 为 TRUE，数据载入时将 NULL 字符串处理为空值，数据导出时则空值处理为 NULL 字符串；若设置为 FALSE，数据载入时将 NULL 字符串处理为字符串，数据导出时将空值处理为空串。

  * **SEND_NODE_NUMBER**



指定 dmfldr 在数据载入时发送节点的个数，整数类型，取值范围为 16~65535，默认有 20 个数据节点。

此参数为可选参数，只在 MODE 为 IN 的情况下有效。

  * **TASK_THREAD_NUMBER**



指定 dmfldr 在数据载入时处理用户数据的线程数目，整数类型，取值范围为 1~128。默认情况下，dmfldr 将该参数值设为系统 CPU 的个数，但无论设定值是多少，dmfldr 至少会创建 2 个任务线程。在多核 CPU 环境下，增大 TASK_THREAD_NUMBER 值可以提升 dmfldr 装载性能。

> **注意**
>
>
> 在导出模式下，当TASK_THREAD_NUMBER设置为大于16而小于128时，dmfldr不会报错，但会将TASK_THREAD_NUMBER自动置为16。



  * **BLDR_NUM**



水平分区表装载时，指定服务器 BLDR 的最大个数，整数类型，取值范围为 1～1024，缺省为 64。

服务器的 BLDR 保存水平分区子表相关信息，BLDR_NUM 的设置也就指定了服务器能同时载入的水平分区子表的个数。

此参数为可选参数，作用于 MODE 为 IN 的情况下，当 MODE 为 OUT 时无效。

  * **BDTA_SIZE**



BDTA（Batch DaTA）的大小，整数类型，取值范围为 100~10000，缺省为 5000。

BDTA 代表 DM 数据库批量数据处理机制中一个批量，在内存、CPU 允许的条件下，增大 BDTA_SIZE 能加快装载速度；在网络是装载性能瓶颈时，增大 BDTA_SIZE 影响不大。

此参数为可选参数，作用于 MODE 为 IN 的情况下，当 MODE 为 OUT 时无效。

  * **COMPRESS_FLAG**



指定是否压缩 BDTA，布尔类型，缺省为 FALSE。

压缩 BDTA 能节省网络带宽，但同时也会加重 CPU 的负担，用户应根据具体应用情况考虑是否制定压缩。

  * **MPP_CLIENT**



指定当服务器环境为 MPP 环境时 dmfldr 的数据装载模式，布尔类型，缺省为 TRUE。

当服务器环境为 MPP 环境时，若 MPP_CLIENT 为 TRUE，为客户端分发模式，数据在 dmfldr 客户端分发好后直接往指定站点发送数据；若 MPP_CLIENT 为 FALSE，为本地分发模式，dmfldr 客户端将数据全部发往连接的 MPP EP 站点。

MPP 环境下要配置 dmmal.ini 文件中的 MAL_INST_HOST 和 MAL_INST_PORT 参数。

此参数只有当服务器环境为 MPP 环境时才有效。

  * **SINGLE_FILE**



指定当服务器环境为 MPP/DPC 环境时，dmfldr 的导出数据文件是否为单个文件，布尔类型，缺省为 FALSE。

此参数只在 MPP/DPC 环境下且 MODE 为 OUT 时有效。参数值为 TRUE 表示仅生成一个数据导出文件，MPP/DPC 各个站点的数据将导出到同一个数据文件；值为 FALSE 表示可以生成多个数据文件，每一个 MPP/DPC 站点都有专门的数据文件接收该站点的数据。

  * **LAN_MODE**



指定当服务器环境为 MPP/DPC 环境时，dmfldr 导入/导出数据是否使用局域网，布尔类型，缺省为 FALSE。

此参数只在 MPP/DPC 环境下有效。值为 TRUE 表示使用局域网，此时服务器的 MAL 系统必须配置了局域网 IP，否则 dmfldr 依然采用服务器对外服务的外网 IP；值为 FALSE 表示不使用局域网。

  * **UNREP_CHAR_MODE**



指定是否将不完整的字符用“*”替换。1 是，0 否，缺省为 0。

设置为 1 时，dmfldr 装载过程中遇到包含不完整字符的数据时，将用"*"替换不完整的字符；为 0 时，该行数据可能会被丢弃。

  * **SILENT**



指定是否以静默方式进行数据装载，布尔类型，缺省 FALSE。

当设置为 TRUE 时，dmfldr 装载过程中将忽略反馈式的消息，例如装载进度提示信息、错误信息等，但仍然会在装载的开始和结束阶段打印一些静态/统计信息。静默方式只作于客户端的屏幕打印，dmfldr 日志依然会完整地记录装载过程中的详细信息。

  * **OCI_DIRECTORY**



指定 ORACLE OCI 动态库所在的目录，字符串类型。

该参数与 MODE 参数配合使用，当 MODE 指定为 OUTORA 时，使用 OCI_DIRECTORY 指定 OCI 的动态库来构建导出环境。

  * **DATA**



指定数据文件路径，字符串类型。

一般情况下数据文件路径在控制文件中指定。如果控制文件缺省或控制文件中数据文件路径指定为‘*’，那么会使用命令行或 dmfldr.ini 配置文件中 DATA 参数指定的数据文件。优先使用命令行中指定的 DATA 参数指定的数据文件。

  * **ENABLE_CLASS_TYPE**



指定是否以 BLOB 方式载入或导出 CLASS 类型列数据，布尔类型，默认为 FALSE。仅在 DIRECT=TRUE 时有效，若 DIRECT=FALSE，不支持导入 CLASS 类型数据。

CLASS 类型是 DM 数据库服务器内部实现的数据类型，实际以 BLOB 类型存储。如果通过交互式工具或 DM 提供的接口等正常途径创建的 CLASS 类型数据，内部会转换成 BLOB 存储。而通过 dmfldr 直接导 CLASS 数据，没有进行转换，有可能出现载入的大对象数据无法转换成对应的 CLASS 类型。因此，当将 ENAME_CLASS_TYPE 设为 TRUE 时，用户要保证对应的 BLOB 数据能正确转换成对应的 CLASS 类型。

  * **FLUSH_FLAG**



指定提交数据时服务器的处理方式，布尔类型，缺省为 FALSE。

该参数用于 dmfldr 向服务器发送 commit 请求时，是否要求服务器提供可靠的服务响应，若设置为 TRUE，则服务器会等数据写入磁盘后才将响应结果返回给 dmfldr，此种方式会降低数据装载的效率，但提供可靠的服务，不存在数据丢失的情况；若为 FALSE，则服务器在确认数据正确无误后便将响应结果返回，随后再写入磁盘，此种方式装载效率高，但若遇到掉电、机器故障、服务器崩溃等灾难性情况下，有可能会导致数据来不及写入磁盘而导致数据丢失。

此参数为可选参数，作用于 MODE 为 IN 的情况下，当 MODE 为 OUT 时无效。

  * **SINGLE_HLDR_HP**



是否使用单个 HLDR 装载 HUGE 水平分区表主表。取值 TRUE 或 FALSE，缺省值为 FALSE。TRUE 表示使用单个 HLDR 装载 HUGE 水平分区表主表。FALSE 表示装载涉及到的每个叶子子表都各使用一个 HLDR。

HLDR 是服务器端装载 HUGE 表时用到的处理装置。SINGLE_HLDR_HP 的设置相当于指定了服务器装载 HUGE 水平分区主表时可以使用资源的模式。

TRUE 的方式更节约空间，FALSE 的方式装载速度更快，用户需要根据自己得需要权衡哪种方式更适合自己。

  * **IGNORE_BATCH_ERRORS**



指定用户在数据装载过程中遇到数据错误时，是否忽略错误继续装载，还是报错返回，布尔类型，缺省为 FALSE。

该参数只在 DIRECT 为 FALSE 时有效。在普通数据装载方式下，以绑定插入的方式向服务器发送数据，服务器检查 IGNORE_BATCH_ERRORS 参数，发现为 TRUE 时，将处理所有接受到的数据，如果有不合要求的数据，服务器保存该数据的错误信息，直到当前批次所有数据处理完毕后，再将执行的结果返回 dmfldr，dmfldr 根据服务器返回的错误信息向错误数据文件写入错误数据。当 IGNORE_BATCH_ERRORS 参数值为 FALSE 时，服务器一旦遇到不合要求的数据，则终止当前批次数据的装载，向 dmfldr 返回错误信息，dmfldr 则直接废弃当前批次的所有数据。

此参数为可选参数，作用于 MODE 为 IN 且 DIRECT 为 FALSE 的情况下，当 MODE 为 OUT 时无效。

  * **EP**



指定 dmfldr 只能向指定的 MPP/DPC 站点发送数据，对于其他站点的数据直接丢弃。EP 站点序号用整型数字表示，各站点间用逗号分隔，整个 EP 参数值用括号括起来。这是 EP 参数的一个常用用法：EP=(1,3,4)，表示向站点号为 1、3、4 的站点发送数据。

该参数仅在 MPP/DPC 环境有效，dmfldr 只能向 EP 指定的站点上的表上锁并发送数据，其他站点的表操作与其无关。需要注意的是，指定 EP 方式对于随机分布表无效，并且 dmfldr 当前连接站点的数据发送不能由其他 dmfldr 客户端发送，换言之，客户端 A 连接 EP1，客户端 B 连接 EP2，那么 EP1 的表数据只能有 A 来发送，不能由 B 发送，否则会导致锁等待或锁超时错误。

此参数为可选参数，在 MODE 为 IN 且 direct 为 TRUE 的情况下有效。

  * **PARALLEL**



dmfldr 向服务器导入数据时，是否开启并行模式。

开启并行参数后，可以运行多个用户同时向同一张表装载数据，此模式可以充分利用客户端的数据处理能力，服务器接收到各个客户端发送过来的数据后，再进行统一的数据处理和刷盘。若存在以下情况，并行参数不可开启：

  1. 装载表存在二级索引；
  2. 装载表存在自定义的聚集索引；
  3. 装载表开启了逻辑日志或者高级日志；
  4. 装载表为列存储表（HUGE 表）；
  5. 装载表为间隔分区表。



此参数为可选参数，在 MODE 为 IN 且 direct 为 TRUE 的情况下有效。

  * **SQL**



用户指定 SQL 查询语句，用于导出自定义查询结果数据，仅导出模式有效。

自定义 SQL 查询语句会将 SQL 语句发送到服务器进行分析执行，若发现 SQL 语句不是查询语句，dmfldr 将报错返回。SQL 语句可以实现用户的个性化数据导出需要，可以选择需要导出的列，需要过滤的结果。

此参数为可选参数，在 MODE 为 OUT 的情况下有效。

SQL 查询语句需要使用双引号括起来。

例 在导出过程中使用 SQL 查询语句。

```
./dmfldr USERID=SYSDBA/*****@192.168.100.121:5254 MODE=\'OUT\' SQL="select * from test where c1='b';" data=\'/home/test/yy/test.txt\'
```

  * **SQLFILE**



用户指定 SQL 查询语句所在文件路径，用于导出自定义查询结果数据，仅导出模式有效。

当 SQL 查询语句过长时，用户可以将 SQL 查询语句写入文件中，然后使用 SQLFILE 参数指定 SQL 查询语句所在文件路径。

此参数为可选参数，在 MODE 为 OUT 的情况下有效。

例 SQL 查询语句所在文件路径为/home/test/yy/test.sql，文件内容如下：

```
select * from test where c1='b';
```

使用 SQLFILE 参数指定 SQL 查询语句所在文件路径进行数据导出。

```
./dmfldr USERID=SYSDBA/*****@192.168.100.121:5254 MODE=\'OUT\' SQLFILE=\'/home/test/yy/test.sql\' data=\'/home/test/yy/test.txt\'
```

• TABLE

指定导入或导出表的表名，可以包含模式名。可选参数。

若导出时指定 SQL 或 SQLFILE 参数，则该选项失效。

• ROW_SEPERATOR

指定行分隔符，分隔符额外指定 X 表示十六进制的分隔符，长度应小于 128。可选参数。

• FIELD_SEPERATOR

指定列分隔符，分隔符额外指定 X 表示十六进制的分隔符，长度应小于 255。可选参数。

例 使用 FIELD_SEPERATOR 参数指定分隔符。

```
./dmfldr USERID=SYSDBA/*****@192.168.100.165:9999  FIELD_SEPERATOR=\'|\' ROW_SEPERATOR=X \'0A\' DATA= \'/opt/data/D1.TXT\' TABLE="sysdba".\"tyjm\" mode=\'in\' SINGLE_FILE=true
```

• COMMIT_OPTION

提交选项。 取值：0 表示每发送一批数据进行提交； 1 表示数据全部装载完毕后进行提交。默认为 0。可选参数，导入时有效。

• APPEND_OPTION

dmfldr 的追加模式。取值：0 表示 APPEND 追加方式； 1 表示 REPLACE 替代方式； 2 表示 INSERT 插入方式。缺省为 0。

当 dmfldr 处于数据装载模式时： APPEND 追加方式，在表中追加新记录；REPLACE 替代方式，先清空表再插入新记录；INSERT 插入方式，向空表插入新记录（如果不是空表则会报错无效的装载模式）。

当 dmfldr 处于导出数据模式时：APPEND 追加方式，若数据文件已存在则以追加的方式写入数据，否则直接创建新文件。REPLACE 替代方式，若数据文件已存在则先删除文件再重新创建新文件，否则直接创建新文件；INSERT 插入方式, 直接创建新文件，若数据文件已存在则报错创建文件失败，否则直接创建新文件成功。

如果导出数据含有大字段数据，且未指定 LOB_AS_VARCHAR=TRUE，则新创建的数据文件会包含普通数据文件（由 DATA 指定名称）和大字段数据文件（由 LOB_FILE_NAME 指定名称，缺省为 dmfldr.lob）；如果不包含大字段数据，则导出文件只有普通数据文件。

• COLNAME_HEADING

是否在导出文件头中打印列名。取值 TRUE 或 FALSE，缺省值为 FALSE。TRUE 表示在导出文件头中打印列名，列名之间使用列分隔符进行分隔，列名与数据之间使用行分隔符进行分隔。可选参数，导出时有效。

• IGNORE_AIMLESS_DATA

是否忽略无目标数据。取值 TRUE 或 FALSE，缺省值为 FALSE。TRUE 表示在导入数据时忽略无目标数据，即未找到对应分区的分区表数据，该错误不影响其他数据的导入，相关错误个数不会计入 ERRORS，并且不会显示相关错误信息，导入结束后用户可通过提交总数、读取逻辑记录总数、拒绝逻辑记录总数以及跳过逻辑记录总数的差值来获取忽略的无目标数据的总数。可选参数，仅在 DIRECT=TRUE 时，导入的情况下有效。

• LOB_AS_VARCHAR

是否将 CLOB 作为 VARCHAR 进行导入导出。取值 TRUE 或 FALSE，缺省值为 FALSE。TRUE 表示进行数据导入导出时将 CLOB 类型数据作为 VARCHAR 类型数据处理，此时 CLOB 类型数据内容为实际数据内容。可选参数。

• LOB_AS_VARCHAR_SIZE

指定将 CLOB 作为 VARCHAR 进行导入导出时，支持的 LOB 数据的最大大小，整数类型，单位为 MB，范围为 1~20，缺省为 10。

可选参数，仅在 LOB_AS_VARCHAR 为 TRUE 时有效。

• LOG_LEVEL

指定记录错误数据信息的级别，可取值 0、1、2、3、4，缺省为 3。

0：不记录错误信息和错误数据；

1：仅记录错误信息；

2：仅记录错误数据；

3：记录错误信息和错误数据；

4：仅将错误输出到屏幕，不记录到文件中。

其中，错误信息指错误原因等信息，记录在日志文件（.lob 文件）中；错误数据指因为格式或数据本身错误而导致导入导出失败的数据，记录在错误文件（.bad 文件）中。

可选参数。

• FLDR_INI

指定 dmfldr.ini 配置文件路径，字符串类型。用户可在 dmfldr.ini 文件中指定除 USERID、CONTROL、HELP 以外的所有 dmfldr 参数值。

可选参数，未指定该参数时，默认在当前目录下查找 dmfldr.ini 配置文件。

dmfldr.ini 配置文件的语法如下所示：

```
OPTIONS(
<id>=<value>
……
)
```

文件中每个参数值对之间使用空格或者换行分隔。

dmfldr.ini 配置文件中指定 SQL 参数时，对于 SQL 语句中出现的单引号需要使用单引号括起来。

例 展示一个完整的 dmfldr.ini 文件。

```
OPTIONS(
MODE='OUT'
SQL='SELECT * FROM TEST WHERE C1=''B'';'
LOG='/home/test/yy/test_log.log'
DATA='/home/test/yy/test.txt'
)
```

• RECONN

指定自动重连次数，范围为 0~4294967295。缺省为 0，表示不进行自动重连。

在服务器未启动时，该参数在 COMMIT_OPTION=1 时可以正常生效，当 COMMIT_OPTION=0 时，该参数可能不生效。

在数据导入过程中，如果出现可恢复的环境错误（如主备切换、故障重启等）或网络错误（如网络超时、连接断开重连等）等问题，dmfldr 根据 RECONN 参数进行指定次数的自动重连处理，若自动重连成功，则 dmfldr 继续执行数据导入任务，若重连失败，则 dmfldr 根据 COMMIT_OPTION 采取相应的处理策略：若 COMMIT_OPTION=0，则未提交数据全部回滚；若 COMMIT_OPTION=1，则所有数据全部回滚。

此参数为可选参数，仅在 MODE 为 IN 的情况下有效。

• RECONN_TIME

指定自动重连等待时间，单位为 S，取值范围为 1~10000，缺省为 5。

当数据导入过程中出现可恢复的环境错误或网络错误等问题时，若 RECONN 不为 0，则 dmfldr 每间隔 RECONN_TIME 时间进行下一次自动重连。

此参数为可选参数，仅在 MODE 为 IN、RECONN 不为 0 的情况下有效。

• WIDTH

指定导出列的数据宽度，单位为字节，取值范围为 1~65535。不足指定宽度时填充空格，超出指定宽度时进行截断。未指定该参数时，导出列按实际长度导出。

不同列的指定宽度之间使用“:”进行分隔。若 WIDTH 指定的列个数少于实际导出列个数，则最后几列视为未设置 WIDTH，其导出列按实际数据长度导出。

此参数为可选参数，仅在 MODE 为 OUT 的情况下有效。

例 指定导出数据第一列的宽度为 12 字节，第二列的宽度为 30 字节。

```
./dmfldr USERID=SYSDBA/*****@192.168.100.121:5254 MODE=\'OUT\' SQL="select * from test;" DATA=\'/home/test/yy/test.txt\' WIDTH=12:30
```

• SEDF

指定被替换的字符列表，以单个字符为单位进行替换，“0x”开头表示十六进制格式，系统自动将其转换为 ASCII 字符。

此参数为可选参数，与参数 SEDT 一同使用。

• SEDT

指定用于替换的字符列表，以单个字符为单位进行替换，“0x”开头表示十六进制格式，系统自动将其转换为 ASCII 字符。

将参数 SEDF 指定的字符列表逐个替换为 SEDT 指定的字符列表，参数 SEDF 和 SEDT 指定的字符个数必须相同。

此参数为可选参数，与参数 SEDF 一同使用。

例 导出数据时，将数据中的“b”和空格分别替换为“z”和“x”。

```
./dmfldr USERID=SYSDBA/*****@192.168.100.121:5254 MODE=\'OUT\' SQL="select * from test;" DATA=\'/home/test/yy/test.txt\' SEDT=\'zx\' SEDF=\'b0x20\'
```

• ESCAPE

指定转义符，“0x”开头表示十六进制格式，系统自动将其转换为 ASCII 字符。仅支持指定一个转义符。

导出数据时，对于指定转义符会输出两个相邻的指定转义符；导入数据时，若存在两个相邻的指定转义符，则仅输入一个指定转义符。

此参数为可选参数。

例 指定转义符为“*”。

```
./dmfldr USERID=SYSDBA/*****@192.168.100.121:5254 MODE=\'OUT\' SQL="select * from test;" DATA=\'/home/test/yy/test.txt\' ESCAPE=\'*\'
```

对于表中的数据“t*1”，将被转换为“t**1”。导入数据时，通过指定 ESCAPE 为“*”，将数据“t**1”转换为“t*1”。

  * **PRIORITY_ENCLOSE**



DB2 兼容模式下封闭符优先级是否高于行分隔符，布尔值，缺省为 FALSE。需要开启 DB2 兼容模式后才生效，生效后，数据文件中的封闭符优先级高于行分隔符。

此参数为可选参数，仅当 COMPATIBLE_MODE=1 时生效。

  * **EXPORT_MODE**



MPP/DPC 导出模式，可取值 0、1，缺省为 0。在使用 SQL 参数进行数据导出时，0：使用普通模式，数据通过一个 GLOBAL 连接获取；1：使用快速模式，数据通过多个 LOCAL 连接从各个节点获取。

快速模式不支持 SQL 结果集包含多表数据，不支持 SQL 为非表的查询语句；

快速模式因为是通过多个 LOCAL 连接从各节点获取数据，因此当涉及跨节点数据的连接等查询语句时，数据可能缺失，此类情况不建议使用。

此参数为可选参数。

  * **BAD_FILE_MODE**



错误数据文件 BADFILE 的模式。取值 0 或 1，缺省为 0。0: 普通模式，错误数据文件中包含文件头、数据头和数据等；1: 纯数据模式，错误数据文件中只包含数据内容。

当 BAD_FILE_MODE=1 时，默认 bad 文件名为“数据文件名.bad”。对于多表装载，还会为 bad 文件增加编号，编号顺序为控制文件中该数据文件对应表格的顺序。

此参数为可选参数。

  * **COMPATIBLE_MODE**



兼容模式，可取值 0、1、2，缺省为 0。0：不做兼容；1：兼容 DB2；2：兼容 MYSQL。

兼容 DB2 时支持功能：1.字符串列封闭符默认为"，全局封闭符只针对字符串列生效；2.大字段导入导出文件的数据文件格式更换为：filename.nnn.mmm/，其中 filename 是大字段文件名，nnn 是文件偏移（取值范围 0~BIGINT），mmm 是大字段长度（取值范围 0~BIGINT）；3.支持 PRIORITY_ENCLOSE 参数；4.数据没有封闭，则被认为是空值，存在封闭符但没有数据，则认为是空串。

兼容 MYSQL 时，支持 MYSQL 中对 NULL 值、空串以及转义符的处理。

此参数为可选参数。

  * **HELP**



获取帮助信息。

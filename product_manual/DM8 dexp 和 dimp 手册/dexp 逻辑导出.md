

# dexp 逻辑导出

dexp 工具可以对本地或者远程数据库进行数据库级、用户级、模式级和表级的逻辑备份。备份的内容非常灵活，可以选择是否备份索引、数据行和权限，是否忽略各种约束（外键约束、非空约束、唯一约束等），在备份前还可以选择生成日志文件，记录备份的过程以供查看。

dexp 工具名称有两种写法 dexp 和 dexpdp，两者区别在于：dexp 导出的文件必须存放在客户端，dexpdp 导出的文件必须存放在服务器端。

## 2.1 使用 dexp 工具

dexp 工具需要从命令行启动。在 cmd 命令行工具中找到 dexp 所在安装目录 /dmdbms/bin，输入 dexp 和参数后回车。参数在下一节详细介绍。

语法如下：

```
dexp PARAMETER=<value> { PARAMETER=<value> }
或
dexpdp  PARAMETER=<value> { PARAMETER=<value> }
```

PARAMETER：dexp 参数。参数 USERID 必须置于首位，此外的多个参数之间排列顺序无影响，参数之间使用空格间隔。

```
<value>：参数取值。
```

例 将用户名为 SYSDBA，IP 地址为 192.168.0.248，端口号为 8888 的数据库采用 FULL 方式完全导出。/user/data 为数字证书路径。导出文件名为 db_str.dmp，导出的日志文件名为 db_str.log，导出文件的路径为/mnt/dexp/data。

```
./dexp USERID=SYSDBA/*****@192.168.0.248:8888##/user/data FILE=db_str.dmp
DIRECTORY=/mnt/dexp/data LOG=db_str.log FULL=Y
```

## 2.2 dexp 参数一览表

本节提供 dexp 的参数一览表，供用户快速参考。

表2.1 dexp参数一览表



> **说明**
>
> 每个参数含义后面的括号内为(N)则表示该参数缺省为否，为(Y)表示缺省为是；“()”内的值表示的是参数的缺省值。



## 2.3 dexp 参数详解

本节详细介绍 dexp 的各个参数。

### 2.3.1 特殊参数

特殊参数中介绍了必选参数和最常用的可选参数。

#### 2.3.1.1 USERID

USERID 用于指定数据库的连接信息。

若配置文件 dm_svc.conf 中配置了导入导出默认连接串，或者使用 CONFIG_FILE 参数指定了局部配置文件，并且局部配置文件中配置了导入导出默认连接串，则可以不指定 USERID 参数；否则必须指定 USERID 参数。

语法如下：

```
{{<username>[/<password>]} | /}[@<connect_identifier>][<option>] [<os_auth>] <connect_identifier> ::=<svc_name> | {<host>[:<port>]} | <unixsocket_file>
<option>::= #{ <extend_option>=<value>{,<extend_option>=<value>} }  //此行外层{}为了封装参数之用，需要出现在语法中
<os_auth>::= AS {SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO}
```

`{{`<username>[/<password>]`} | /}`：`<username>[/<password>]` 为用户名和密码。普通登录方式时用户名必写。/表示采用操作系统身份验证方式登录或利用 wallet 文件登录。采用操作系统身份验证方式登录时无需指定用户名和密码，即使指定也会被忽略。利用 wallet 文件登录时，dm_svc.conf 文件中的配置项 WALLET_LOCATION 必须非空，客户端会通过用户输入的服务名以及 WALLET_LOCATION 配置项指定的 wallet 文件路径自动获取 wallet 文件中服务名所对应的用户名和密码，因此用户无需输入用户名和密码，若用户输入了用户名和密码，则优先使用用户输入的用户名和密码登录数据库，关于利用 wallet 文件登录数据库的更多详细介绍请参考手册《DM8 安全管理》。

`<svc_name>`：服务名。服务名在 dm_svc.conf 中配置。dm_svc.conf 的配置请参考《DM8 系统管理员手册》。

`<host>[:<port>]`：服务器 IP 地址和端口号。缺省情况下默认为本地服务器和端口号 LOCALHOST:5236。当服务器为本机时，SERVER:PORT 可直接写 LOCALHOST。当连接其他服务器时，SERVER:PORT 需写上 IP 地址和 PORTNUM，例如：192.168.0.248:8888。

`<unixsocket_file>`：专门用于在 LINUX 系统中，当服务器与客户端之间使用 UNIXSOCKET（UNIX DOMAIN SOCKET - IPC）协议通信时，指定客户端连接的 socket 文件路径。例如：

```
./dexp SYSDBA/*****@/home/test/foo.sock#{inet_type=UNIXSOCKET} file=/home/dexp/dexp_test.dmp log=/home/log/
```

`<option>`： 为扩展选项，用法为 <extend_option>=`<value>`。所有 value 值不能包含空格，不能包含特殊的符号，如引号等。书写扩展选项时需要用引号#"{ }"进行封装，例如：`#"{inet_type=tcp,mpp_type=local}"`。

现支持的扩展选项如下：

|**extend_option**|**value**|
|-------------|-------------|
|mpp_type|MPP 登录属性，此属性的设置对非 MPP 系统没有影响。取值 GLOBAL 和 LOCAL，默认为 GLOBAL。GLOBAL 表示 MPP 环境下建立的会话为全局会话，对数据库的导入导出操作在所有节点进行；LOCAL 表示 MPP 环境下建立的会话为本地会话，对数据库的导入导出操作只在本地节点进行|
|inet_type|网络通信协议类型。取值 TCP/UDP/IPC/UNIXSOCKET/RDMA，缺省为 TCP。使用 UDP 协议时，服务器需要设置 INI 参数 ENABLE_UDP 非 0；使用 IPC 协议时，会忽略 IP 地址和端口号，直接连接本机上 INI 参数 ENABLE_IPC=1 的服务器，一台机器上只能有一个服务器设置 ENABLE_IPC=1；使用 UNIXSOCKET 协议时，无需指定 IP 地址和端口号，但服务器需要配置 INI 参数 UNIX_SOCKET_PATHNAME 指定 socket 文件路径，客户端连接服务器时也需要指定 socket 文件路径，二者必须一致，仅 LINUX 环境下支持 UNIXSOCKET；使用 RDMA 协议时，需要安装并配置 RDMA 网卡，连接数据库时需要指定 RDMA 网卡配置的 IP 地址以及服务器的端口号|
|ssl_path|通信加密的 SSL 数字证书路径，缺省为不使用加密。数字证书路径由用户自己创建，将相应的证书需放入该文件夹中。其中服务器证书必须与 dmserver 目录同级，客户端目录可以任意设置。和 ssl_pwd 一起使用。各用户只能使用自己的 SSL 数字证书，例如 SYSDBA 账户只能使用\bin\CLIENT_SSL\SYSDBA 下的证书和密码。例如：./dexp SYSDBA/*****@192.168.1.64:5236#"{ssl_path= /home/dmdbms/bin/client_ssl/SYSDBA,ssl_pwd=12345}" file=/home/dexp_test.dmp log=/home/log/|
|ssl_pwd|通信加密的 SSL 数字证书密码。和 ssl_path 一起使用。缺省为不加密|


例  一个包含扩展选项的例子。

```
./dexp SYSDBA/*****@192.168.1.64:5236#"{mpp_type=local,inet_type=tcp}" file=/home/dexp_test.dmp log=/home/log/
```

`SSLPATH@SSLPWD`：通信加密的 SSL 数字证书路径和密码，缺省为不使用加密。数字证书路径由用户自己创建，将相应的证书需放入该文件夹中。其中服务器证书必须与 dmserver 目录同级，客户端目录可以任意设置。

`AS <SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO>`：操作系统身份验证。用户可以通过将操作系统用户加入到操作系统的 dmdba|dmsso|dmauditor 用户组来使用操作系统用户登录数据库，分别对应数据库的 SYSDBA|SYSSSO|SYSAUDITOR 用户。还可以通过将操作系统用户加入到操作系统的 dmusers 用户组来使用操作系统用户登录数据库，对应数据库的同名用户。AUTO 表示按顺序自动匹配数据库用户类型。操作系统身份验证无需输入用户名和密码，若输入用户名和密码将会被忽略。关于操作系统身份验证的更多详细介绍请参考手册《DM8 安全管理》。

> **注意**
>
> password比较特殊，因为password中会用到各种特殊字符。特殊字符在操作系统中需要被特殊处理。不同操作系统，书写方式不同。



\<password> 中特殊字符的书写规范：

1. 不同的操作系统

1) WINDOWS 系统

  * 关键字符，dexp 和 dimp 要求对连接串的特殊字符需要使用双引号括起来"aaaa/aaaa"，操作系统的要求需要再在最外加双引号和转义"""aaaa/aaaa"""。例如：用户名为 user01，密码为 aaaa/aaaa，那么连接串要写成：dexp user01/"""aaaa/aaaa"""
  * 空格，需要使用双引号括起来作为一个整体（这是操作系统的要求）。例如：用户名为 user01，密码为 aaaa aaaa ，那么连接串要写成：dexp user01/"aaaa aaaa"
  * 双引号，dexp 和 dimp 要求对双引号需要使用双引号括起来，同时双引号需要转义"aaaa""aaaa"；操作系统要求再对双引号转义和最外层加双引号"""aaaa""""aaaa"""。例如：用户名为 user01，密码为 aaaa"aaaa ，那么连接串要写成：dexp user01/"""aaaa""""aaaa"""。



2) LINUX 系统

LINUX 环境下，密码中的特殊字符处理过程既要考虑操作系统的要求，又要考虑 dexp 和 dimp 的要求。

首先，操作系统的要求。

bash 的引号设计为：在单引号中，所有的特殊字符都失去其特殊含义；在双引号中，特殊字符包括：美元符($)、反引号(\`)、转义符()、感叹号(!）。

如果密码中没有单引号的，应该都只有外面加单引号就可以解决了；如果密码只有单引号，那么可以将单引号用双引号括起来；如果既有单引号又有美元符($)、反引号(\`)、转义符(\)、感叹号(!)四个特殊字符，那么在特殊字符之前全部加\转义就好了。例如：

'aaaa\aaaa' 传给 dexp 和 dimp 为 aaaa\aaaa。

"aaaa'aaaa" 传给 dexp 和 dimp 为 aaaa'aaaa。

"aaa'\$aaaa" 传给 dexp 和 dimp 为 aaa'$aaaa。

其次，在操作系统要求的基础上，增加 dexp 和 dimp 对关键字和双引号的要求。

  * 关键字符，dexp 和 dimp 要求对连接串的特殊字符需要使用双引号括起来。例如：密码为 aaaa\aaaa，使用双引号括起来"aaaa\aaaa"，因为此密码中不含有单引号，根据操作系统的要求直接在最外面加单引号。例如：用户名为 user01，密码为 aaaa/aaaa，那么连接串要写成：./dexp user01/'"aaaa/aaaa"'。
  * 双引号，dexp 和 dimp 要求对双引号需要使用双引号括起来，同时双引号需要转义。例如:密码为 aaa"\aaaa，那么根据 dexp 和 dimp 的要求加双引号同时转义为"aaa""\aaaa"，因为没有单引号，根据操作系统的要求直接加单引号。例如：用户名为 user01，密码为 aaa"\aaaa，那么连接串要写成：./dexp user01/'"aaa""\aaaa"'。
  * 单引号，根据操作系统的要求，只能将单引号放入双引号中。例如：用户名为 user01，密码为 aaaa'aaaa，那么连接串要写成：./dexp user01/"aaaa'aaaa"。
  * 单引号 + 操作系统下的特殊字符，根据操作系统的要求，因为单引号只能放在双引号内，同时双引号中还有一些特殊字符不能被识别需要加反斜杠转义。例如: 用户名为 user01，密码为 aaa'$aaaa，使用双引号括起来，同时对$ 加反斜杠转义。那么连接串要写成：./dexp user01/"aaa'\$aaaa"。
  * 单引号 + 双引号，根据操作系统的要求，单引号需要放在双引号中，在双引号中表示双引号则使用反斜杠转义双引号。例如: 用户名为 user01，密码为 aaa"'aaaa，根据 dexp 和 dimp 的要求双引号作为特殊字符，需要使用双引号再括起来，同时使用双引号对双引号转义"aaa""'aaaa"；同时考虑操作系统的要求，因为含有单引号，只能将整个密码放入双引号中，同时对双引号使用反斜杠转义，那么连接串要写成：./dexp user01/"\"aaa\"\"'aaaa\""。



2.如何转义双引号

  1. dexp 和 dimp 要求使用双引号对双引号内的双引号转义。
  2. WINDOWS 命令行，使用双引号或者反斜杠对双引号内的双引号转义。
  3. LINUX 命令行，使用反斜杠对双引号内的双引号转义。



#### 2.3.1.2 FILE

FILE 用于明确指定导出的文件（可以包含路径），可以包含多个文件，用逗号分隔，其中文件名不能包含特殊符号（\、/、:、*、？、"、\<、>、|）。可选参数，如果不设置 FILE 参数（来明确指定导出文件名称），那么 dexp 默认导出文件名称为 dexp.dmp，dexpdp 默认导出文件名称为 dexpdp.dmp。长度不超过 128 个字符，绝对路径不超过 256 个字符。

语法如下：

```
FILE=<文件>
```

例 1 设置 FILE=db_str.dmp，导出文件名为 db_str.dmp。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log FULL=Y
DIRECTORY=/mnt/data/dexp
```

例 2 FILE 指定的文件也可以包含路径，设置 FILE=/mnt/db_str.dmp。

```
./dexp USERID=SYSDBA/***** FILE=/mnt/db_str.dmp LOG=db_str.log FULL=Y
```

> **注意**
>
> 如果FILE指定的文件包含生成路径，则忽略DIRECTORY中的路径；  
> 如果FILE没有指定路径时，使用DIRECTORY中指定的路径；  
> 如果FILE和DIRECTORY都没有指定路径，则使用系统当前路径。



文件也可以在生成过程中自动扩展成多个。首先，文件名需要包含通配符 %U，用于作为自动扩充文件的文件名模板。%U 表示为 2 个字符宽度的数字，由系统自动生成，起始为 01。其次，使用 FILESIZE 参数来指定文件的大小。

例 3 设置 FILE=db_str%U.dmp，导出文件名为 db_str%U.dmp。同时使用 %U 对文件进行扩展，使用 FIZESIZE=128m 指定文件大小。

```
./dexp USERID=SYSDBA/***** FILE=db_str%U.dmp LOG=db_str.log FULL=Y
DIRECTORY=/mnt/data/dexpFILESIZE=128m
```

#### 2.3.1.3 LOG

LOG 用于明确指定导出过程产生的日志文件名称（可以包含路径），可以包含多个文件，用逗号分隔，其中文件名不能包含特殊符号（\、/、:、*、？、"、\<、>、|）。可选参数。如果不设置 LOG 参数（来明确指定日志文件名称），那么 dexp 默认导出日志文件名称为 dexp.log，dexpdp 默认导出日志文件名称为 dexpdp.log。

语法如下：

```
LOG=<文件名>
```

例 1 指定日志文件名为 db_str.log。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log FULL=Y
DIRECTORY=/mnt/data/dexp
```

文件也可以在生成过程中自动扩展成多个。首先，文件名需要包含通配符 %U，用于作为自动扩充文件的文件名模板。%U 表示为 2 个字符宽度的数字，由系统自动生成，起始为 01。其次，使用 FILESIZE 参数来指定文件的大小。

例 2 指定日志文件名为 db_str%U.log。同时使用 %U 对文件进行扩展，使用 FIZESIZE=128m 指定文件大小。

```
./dexp USERID=SYSDBA/***** FILE=db_str%U.dmp LOG=db_str%U.log FULL=Y 
DIRECTORY=/mnt/data/dexp FILESIZE=128m
```

> **注意**
>
> 如果LOG指定的文件包含生成路径，则忽略DIRECTORY中的路径；  
> 如果LOG没有指定路径时，使用DIRECTORY中指定的路径；  
> 如果LOG和DIRECTORY都没有指定路径，则使用系统当前路径。



导出日志记录了导出的整个过程信息和导出对象的统计信息，供用户查看。

LOG 日志格式介绍如下：

  1. 如果导出级别为 TABLES，那么日志信息包含一张或多张表的索引、权限、数据行数。
  2. 如果导出级别为 SCHEMAS，那么日志信息只包含一个或多个模式中的对象统计信息，每个模式下的内容具体有：



  * 导出 SEQUENCE、VIEW、COMMENT、PROCEDURE、SYNONYM、DBLINK、TRIGGER、PACKAGE、OBJECT、CLASS_BODY 和 DOMAIN 等对象的个数和名称，以及对象的权限。
  * 依次导出表的索引、权限、数据行数。



  3. 如果导出级别为 OWNER，那么日志内容包含一个或多个用户所拥有的所有模式中的对象统计信息，每个模式下的内容和导出级别为 SCHEMAS 时一样。
  4. 如果导出级别为 FULL，那么日志内容除了包含所有模式中的对象信息，还包括系统包和系统视图。



和 LOG 有关的参数还有 NOLOGFILE、NOLOG 和 LOG_WRITE。NOLOGFILE=Y 用于设置不使用日志文件。NOLOG=Y 用于设置不在屏幕上显示日志信息。LOG_WRITE=Y 用于设置日志的实时打印（到日志文件）功能。

#### 2.3.1.4 DIRECTORY

DIRECTORY 用于指定导出文件及日志文件生成的路径。长度不超过 256 个字符。

使用 dexp 工具时，DIRECTORY 为可选参数，缺省导出到 dexp 所在路径。

使用 dexpdp 工具时，DIRECTORY 为必选参数，并且指定的路径必须是通过 CREATE DIRECTORY 定义的目录名。

语法如下：

```
DIRECTORY=<path>
```

\<path>：导出文件和日志文件生成的路径。

如果 FILE 和 LOG 参数指定的文件包含生成路径，则 FILE 和 LOG 参数中指定的路径将替代 DIRECTORY 所指定的路径；如果 FILE 和 LOG 参数指定的文件未包含路径信息，则文件将被生成到 DIRECTORY 指定的目录下；如果都没有指定路径，程序将根据当前的运行环境来设置相应的导出路径，一般为当前路径。

例 1 没有指定路径，使用当前路径。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log FULL=Y
```

例 2 指定路径/mnt/data/dexp。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log FULL=Y
DIRECTORY=/mnt/data/dexp
```

> **注意**
>
> FILE和LOG中指定的路径，优先级高于DIRECTORY中指定的路径。  
>  当FILE和LOG不指定路径时，才使用DIRECTORY指定的路径。  
> 如果都没有指定路径，则使用系统当前路径。



例 3  使用 dexpdp 工具，先在服务器端创建名为 GYFDIR 的目录，再使用该目录进行导出。

```
//先连接上远程的数据库服务器192.168.1.60:5236
disql.exe SYSDBA/*****@192.168.1.60:5236
//在远程服务器上创建目录
CREATE OR REPLACE DIRECTORY  "GYFDIR"  AS 'E:\test\path';
//使用dexpdp导出文件
dexpdp.exe USERID=SYSDBA/*****@192.168.1.60:5236 FILE=dexpDP.dmp LOG=dexpDP.log FULL=Y DIRECTORY=GYFDIR
```

#### 2.3.1.5 四种级别的导出方式

针对数据库对象，有 FULL、OWNER、SCHEMAS、TABLES 四种导出方式可供选择。一次导出只能指定一种方式。可选参数，缺省为 SCHEMAS。

需要注意的是，无论哪种级别的导出方式，当涉及索引导出时，位图连接索引和虚索引不会被导出。

##### 2.3.1.5.1 FULL

FULL 方式导出数据库的所有对象。

语法如下：

```
FULL=Y/N
```

设置 FULL=Y 时指定以 FULL 方式导出数据库的所有对象；设置 FULL=N 相当于没有设置 FULL 参数，缺省为 N。

例 设置 FULL=Y，导出数据库的所有对象，导出数据库文件和日志文件放在路径/mnt/data/dexp 下。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log
FULL=YDIRECTORY=/mnt/data/dexp
```

##### 2.3.1.5.2 OWNER

OWNER 方式导出一个或多个用户拥有的所有对象。

语法如下：

```
OWNER=<用户名>{,<用户名>}
```

例 设置 OWNER=USER01，导出用户 USER01 所拥有的对象全部导出。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log OWNER=USER01
DIRECTORY=/mnt/data/dexp
```

##### 2.3.1.5.3 SCHEMAS

SCHEMAS 方式的导出一个或多个模式下的所有对象。

语法如下：

```
SCHEMAS=<模式名>{,<模式名>}
```

例 设置 SCHEMAS=USER01，导出模式 USER01 模式下的所有对象。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log
SCHEMAS=USER01DIRECTORY=/mnt/data/dexp
```

> **注意**
>
> 一般情况下，OWNER与SCHEMAS导入导出是相同的。  
>  但是用户可以包含多个模式，在这种情况下SCHEMAS的导入导出是OWNER导入导出的一个子集。



##### 2.3.1.5.4 TABLES

TABLES 方式导出和导入一个或多个指定的表或表分区。导出所有数据行、约束、索引等信息。

语法如下：

```
TABLES=[模式名.]表名[:表分区名] [, ...]
```

例 设置 TABLES=table1,table2，导出 table1,table2 两张表的所有数据和信息。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log TABLES=table1,table2
DIRECTORY=/mnt/data/dexp
```

和 TABLES 导出有关的参数还有 QUERY、EXCLUDE 和 INCLUDE，都是用来设置过滤条件的。

##### 2.3.1.5.5 OWNER、SCHEMAS、TABLES 的书写规定

对于输入的用户名、模式名和表名，工具都会自动转换为大写。例如：输入的命令 TABLES=t1，那么实际处理的表是 T1。

对于小写的用户名、模式名和表名，为了不转换为大写，需要转义。如果用户名、模式名和表名中含有特殊字符（双引号、单引号、空格、逗号等），则也需要进行转义。具体的转义规则请参考 [2.3.1.1 USERID](https://eco.dameng.com/document/dm/zh-cn/pm/dexp-logical-export.html#2.3.1.1%20USERID) 对于<password> 中特殊字符书写规范的介绍。

### 2.3.2 普通参数

普通参数中介绍了具有一些专门用途的可选参数。

#### 2.3.2.1 FUZZY_MATCH

FUZZY_MATCH 用于指定 TABLES 选项是否开启模糊匹配。可选参数，缺省为 N。

语法如下：

```
FUZZY_MATCH=N/Y
```

N：TABLES 选项不强制开启模糊匹配，根据所指定的表名来自动识别是否进行模糊匹配，缺省值；

Y：TABLES 选项强制开启模糊匹配，指定的表名与数据库中的表名采用 LIKE 模糊查询匹配。

例  导出 OTHER 模式下以"R"开头的表。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log TABLES=OTHER.R%
FUZZY_MATCH=Y
```

或

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log TABLES=OTHER.R% FUZZY_MATCH=N
```

#### 2.3.2.2 QUERY

QUERY 用于指定过滤条件来对表数据进行导出。可选参数。

语法如下：

```
QUERY="<QUERY子句> {, <QUERY子句>}"
<QUERY子句>::= [[schema.]table:]<where_condition>
```

\<where_condition>：过滤条件。更详细了解请参考《DM8_SQL 语言使用手册》的 \<WHERE 子句 >。

> **注意**
>
> 1.QUERY中指定的表对象需要在数据库对象中存在，否则报错；  
>  2.QUERY中对于同一个表对象不能重复指定过滤条件；  
>
> 3.指定了表对象的过滤条件为特定过滤条件，未指定表对象的过滤条件为普通过滤条件，特定过滤条件的优先级高于普通过滤条件。如果QUERY中有普通过滤条件，某一表对象有特定过滤条件，对该表只执行特定过滤条件



例 1 导出 OTHER.READER 表中满足"WHERE AGE=19"条件的数据。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log TABLES=OTHER.READER QUERY="WHERE AGE=19"
```

例 2 导出 SYSDBA 模式数据，但是其中表 TEST 只导出定义，不导出数据。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log SCHEMAS=SYSDBA QUERY="TEST:\"WHERE 1=2\""
```

#### 2.3.2.3 PARALLEL

PARALLEL 用于指定导出的过程中所使用的线程数目。可选参数。如果 CPU 核心数为 N 的话，那一般来说 PARALLEL 为 N 或者 N+1 最合适。

语法如下：

```
PARALLEL=<num>
```

\<num>：线程数。整数类型，取值范围：1~100，缺省为 16。

例 设置 PARALLEL=2 时则线程数为 2。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log FULL=Y
DIRECTORY=/mnt/data/dexp PARALLEL=2
```

#### 2.3.2.4 TABLE_PARALLEL

TABLE_PARALLEL 用于指定导出每张表所使用的线程数。可选参数。如果 CPU 核数为 N 的话，那一般来说 TABLE_PARALLEL 为 N 或者 N+1 最合适。在 MPP 模式下会转换成单线程。

语法如下：

```
TABLE_PARALLEL=<num>
```

\<num>：线程数。整数类型，取值范围 1~50，缺省为 8。

例 设置 TABLE_PARALLEL=3 时则线程数为 3。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log FULL=Y
DIRECTORY=/mnt/data/dexp TABLE_PARALLEL=3
```

#### 2.3.2.5 TABLE_POOL

TABLE_POOL 用于设置导出过程中存储表数据的缓冲区个数。可选参数。

语法如下：

```
TABLE_POOL=<num>
```

\\<num>：存储表数据的缓冲区个数。整数类型，取值范围：1~20，缺省为 8。

> **注意**
>
> TABLE_PARALLEL
> 固定的情况下，导出时间随着TABLE_POOL个数的增加而减少。当TABLE_POOL稍大于TABLE_PARALLEL时结果为最优。



例 设置 TABLE_POOL=3 时。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log FULL=Y
DIRECTORY=/mnt/data/dexp TABLE_POOL=3
```

#### 2.3.2.6 EXCLUDE

EXCLUDE 用来批量设置导出时忽略的对象。可选参数。

语法如下：

```
EXCLUDE=[(]<对象种类名>{,<对象种类名>}[)]
或
EXCLUDE=TYPE: name1,name2
或
EXCLUDE=TYPE:cond{,TYPE:cond}
```

\< 对象种类名 >：包括 CONSTRAINTS、INDEXES、ROWS、TRIGGERS、GRANTS 、VIEWS、PROCEDURE、PACKAGE、SEQUENCE、TABLES。例如：EXCLUDE=(CONSTRAINTS,INDEXES)。

TYPE：包括 SCHEMAS、TABLES、VIEWS、PROCEDURE、PACKAGE、SEQUENCE。

  * 当 TYPE 为 SCHEMAS 时，忽略指定的模式，使用 SCHEMAS:INFO 格式，如果使用表级、模式级导出方式导出，则使用 SCHEMAS:INFO 格式的 EXCLUDE 无效。例如：EXCLUDE=SCHEMAS:SCH1,SCH2。
  * 当 TYPE 为 TABLES 时，忽略指定的表，使用 TABLES:INFO 格式。例如：EXCLUDE= TABLES:table1,table2。
  * 当 TYPE 为 VIEWS 时，忽略指定的视图，使用 VIEWS:INFO 格式。例如：EXCLUDE= VIEWS:view1,view2。
  * 当 TYPE 为 PROCEDURE 时，忽略指定的存储过程和存储函数。
  * 当 TYPE 为 PACKAGE 时，忽略指定的包。
  * 当 TYPE 为 SEQUENCE 时，忽略指定的序列。



cond：过滤条件，包括 IN、LIKE、NOT IN、NOT LIKE、BETWEEN AND、>、\<、=、>=、 \<=、\<>、!=。

> **注意**
>
> 如果导出方式为TABLES表级或SCHEMAS模式级导出时，EXCLUDE=SCHEMAS:\<模式名>{,\<模式名>}排除模式不起作用。



例 1  设置 EXCLUDE=(CONSTRAINTS)，将当前用户下的 table1、table2 的对象信息导出时不导出约束。

```
./dexp SYSDBA/***** FILE=/mnt/data/db_str.dmp LOG=db_str.log TABLES=table1,table2 EXCLUDE=\(CONSTRAINTS\)
```

例 2 设置 EXCLUDE="TABLES:"LIKE 'TEMP%'",TABLES:"IN ('TEST1')""，不导出以“TEMP”开头的表，并且不导出 TEST1 表。

```
./dexp SYSDBA/***** FILE=/mnt/data/db_str.dmp LOG=db_str.log  EXCLUDE="TABLES:\"LIKE 'TEMP%'\",TABLES:\"IN ('TEST1')\""
```

#### 2.3.2.7 INCLUDE

INCLUDE 用来批量设置导出时包含的对象。可选参数。

语法如下：

```
INCLUDE=[(]<对象种类名>{,<对象种类名>}[)]
或
INCLUDE=TYPE: name1,name2
或
INCLUDE=TYPE:cond{,TYPE:cond}
```

\< 对象种类名 >：包括 CONSTRAINTS、INDEXES、ROWS、TRIGGERS、GRANTS 、VIEWS、PROCEDURE、PACKAGE、SEQUENCE、TABLES。例如：INCLUDE=(CONSTRAINTS,INDEXES)。

TYPE：包括 SCHEMAS、TABLES、VIEWS、PROCEDURE、PACKAGE、SEQUENCE。

  * 当 TYPE 为 SCHEMAS 时，只导出指定的模式，使用 SCHEMAS:INFO 格式，如果使用表级、模式级导出方式导出，则使用 SCHEMAS:INFO 格式的 INCLUDE 无效。例如：INCLUDE=SCHEMAS:SCH1,SCH2。
  * 当 TYPE 为 TABLES 时，只导出指定的表，使用 TABLES:INFO 格式。
  * 当 TYPE 为 VIEWS 时，只导出指定的视图，使用 VIEWS:INFO 格式。例如：INCLUDE= VIEWS:view1,view2。
  * 当 TYPE 为 PROCEDURE 时，只导出指定的存储过程和存储函数。
  * 当 TYPE 为 PACKAGE 时，只导出指定的包。
  * 当 TYPE 为 SEQUENCE 时，只导出指定的序列。



cond：过滤条件，包括 IN、LIKE、NOT IN、NOT LIKE、BETWEEN AND、>、\<、=、>=、 \<=、\<>、!=。

例 1  设置 INCLUDE=(CONSTRAINTS,INDEXES)，指明将当前用户下的 table1 的对象约束和索引信息导出。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp LOG=db_str.log TABLES=table1
DIRECTORY=/mnt/data/dexp INCLUDE=\(CONSTRAINTS,INDEXES\)
```

例 2 设置 INCLUDE="TABLES:"LIKE 'TEMP%'""，只导出以“TEMP”开头的表。

```
./dexp SYSDBA/***** FILE=/mnt/data/db_str.dmp LOG=db_str.log  INCLUDE="TABLES:\"LIKE 'TEMP%'\""
```

#### 2.3.2.8 CONSTRAINTS、TABLESPACE、GRANTS、INDEXES、TRIGGERS、ROWS

这些参数用来指定某类对象是否被导出。类别分别为约束、表空间、权限、索引、触发器和数据。

语法如下：

```
CONSTRAINTS = Y/N
TABLESPACE = N/Y
GRANTS = Y/N
INDEXES = Y/N
TRIGGERS = Y/N
ROWS = Y/N
```

CONSTRAINTS：设置是否导出约束，可选参数。取值范围：Y/N。Y：导出约束，缺省值；N：不导出约束。

TABLESPACE：设置是否导出表空间或表空间组，及导出的对象定义中是否包含表空间或表空间组的存储选项，可选参数。导出表空间定义时只导出 ONLINE 状态表空间。取值范围：N/Y。N：不包含表空间或表空间组，且导出的对象定义中不包含表空间或表空间组的存储选项，缺省值；Y：包含表空间或表空间组，且导出的对象定义中包含表空间或表空间组的存储选项。

GRANTS：设置是否导出权限，可选参数。取值范围：Y/N。Y：导出权限，缺省值；N：不导出权限。

INDEXES：设置是否导出索引，可选参数。取值范围：Y/N。Y：导出索引，缺省值；N：不导出索引。

TRIGGERS：设置是否导出触发器，可选参数。取值范围：Y/N。Y：导出触发器，缺省值；N：不导出触发器。

ROWS：设置是否导出数据，可选参数。取值范围：Y/N。Y：导出数据，缺省值；N：不导出数据。

例  设置 ROWS=N 不导出表中的数据。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp NOLOGFILE=Y FULL=Y
DIRECTORY=/mnt/data/dexp ROWS=N
```

> **注意**
>
>
> **此处，对象（CONSTRAINTS、GRANTS、INDEXES、TRIGGERS、ROWS）分别单独设置为N和EXCLUDE批量设置功能一样，设置一个即可。  
>
> 对象（CONSTRAINTS、GRANTS、INDEXES、TRIGGERS、ROWS）分别单独设置为Y和INCLUDE批量设置功能略有区别，设置INCLUDE时只导出指定了的对象；单独设置CONSTRAINTS、GRANTS、INDEXES、TRIGGERS、ROWS仅用于控制是否导出该对象。  
>  如果单独设置和批量设置同时出现时，那么以最后出现的那个为准。 **



#### 2.3.2.9 NOLOGFILE

NOLOGFILE 用于设置是否使用日志文件。可选参数。

语法如下：

```
NOLOGFILE=Y/N
```

Y：不使用。

N：使用，缺省值。

> **建议**
>
> 一旦设置NOLOGFILE=Y不使用日志文件。此时即使存在LOG参数，LOG参数也为无效。



例 设置 NOLOGFILE=Y，不使用日志文件。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp NOLOGFILE=Y FULL=Y
DIRECTORY=/mnt/data/dexp
```

#### 2.3.2.10 NOLOG

NOLOG 设置屏幕上是否显示日志信息。可选参数。

语法如下：

```
NOLOG=Y/N
```

Y：不显示。

N：显示，缺省值。

例 设置 NOLOG=Y 屏幕不显示日志信息。

```
./dexp USERID=SYSDBA/***** FILE=db_str.dmp NOLOG=Y FULL=Y
DIRECTORY=/mnt/data/dexp
```

#### 2.3.2.11 LOG_WRITE

LOG_WRITE 用于日志信息实时写入日志文件。可选参数。

语法如下：

```
LOG_WRITE=Y/N
```

Y：一边将日志内容打印到屏幕上，一边将日志内容写入日志文件。

N：日志内容先在屏幕上全部打印完毕再写入日志文件，缺省值。

#### 2.3.2.12 DUMMY

DUMMY 用于设置交互信息处理。可选参数，缺省使用 P 打印交互信息。在使用 dexpdp 的时候，此参数无需设置，不提供交互信息，全部按 YES 处理。

语法如下：

```
DUMMY=P/Y/N
```

P：打印交互信息，缺省方式。当导出文件已存在的时候，提供是否覆盖交互界面。

Y：不打印交互信息，所有交互都按 YES 处理。

N：不打印交互信息，所有交互都按 NO 处理。

#### 2.3.2.13 PARFILE

PARFILE 用于将常用的参数设置保存到文件中，然后使用参数文件 PARFILE 进行导出、导入操作。可选参数。长度不超过 256 个字符。在使用 dexpdp 的时候，该文件须放在服务器端。

语法如下：

```
PARFILE=<path>
```

：PARFILE 文件的绝对路径。

例 设置 PARFILE=/mnt/data/dexp/para.txt。

```
./dexp USERID=SYSDBA/***** PARFILE=/mnt/data/dexp/para.txt
```

其中，参数文件/mnt/data/dexp/para.txt 的内容如下：

```
FILE=db_str.dmp

LOG=db_str.log

TABLES=table1

DIRECTORY=/mnt/data/dexp
```

#### 2.3.2.14 FEEDBACK

FEEDBACK 用来指明在导出数据的过程中每间隔多少行打印一次进度信息。可选参数。

当参数缺省时，默认为 0，只打印导出表的总行数。

语法如下：

```
FEEDBACK=<num>
```

\<num>：表间隔的行数。正整数。缺省为 0，只打印导出表的总行数。

例  将整个数据库导出到文件 dexp.dmp 中，并且在导出的过程中每隔 100 行打印一次进度信息。

```
./dexp SYSDBA/***** FULL=Y FILE=dexp.dmp DIRECTORY=/mnt/data/dexp FEEDBACK=100
```

#### 2.3.2.15 COMPRESS

COMPRESS 指定是否压缩导出文件。可选参数。缺省为不压缩。

语法如下：

```
COMPRESS=Y/N
```

Y：压缩。

N：不压缩，缺省值。

#### 2.3.2.16 COMPRESS_LEVEL

COMPRESS_LEVEL 指定导出数据压缩等级。仅当 COMPRESS=Y 时有效。

语法如下：

```
COMPRESS_LEVEL=<num>
```

\<num>：压缩等级，整数类型，取值范围 0~9，缺省为 1。数值越大，压缩等级越高，压缩比例越高，0 表示不压缩。

例 设置压缩等级为 2。

```
./dexp USERID=SYSDBA/***** COMPRESS=Y COMPRESS_LEVEL=2
```

#### 2.3.2.17 ENCRYPT、ENCRYPT_PASSWORD、ENCRYPT_NAME

这三个参数用来设置导出文件加密。可选参数。

语法如下：

```
ENCRYPT=Y/N
ENCRYPT_PASSWORD=<加密密钥>
ENCRYPT_NAME=<加密算法>
```

ENCRYPT=Y/N：设置导出文件加密。N：不开启导出文件加密，缺省值；Y：开启导出文件加密。如果设置 ENCRYPT=Y，那么默认 COMPRESS=Y，先压缩后加密。

ENCRYPT_PASSWORD=\<加密密钥>：设置加密密钥。支持最大长度为 255。

ENCRYPT_NAME=\<加密算法>：设置加密算法。缺省为 RC4。

> **注意**
>
> 加密算法： DES_ECB 、DES_CBC 、DES_CFB、DES_OFB、DESEDE_ECB、 DESEDE_CBC
> 、DESEDE_CFB、DESEDE_OFB 、AES128_ECB 、 AES128_CBC 、AES128_CFB 、AES128_OFB
> 、AES192_ECB 、 AES192_CBC 、AES192_CFB 、AES192_OFB 、AES256_ECB 、 AES256_CBC
> 、AES256_CFB 、AES256_OFB 、RC4。



例如，设置导出文件加密，密钥 abcdefg，加密算法为 DES_CBC。

```
./dexp SYSDBA/***** DIRECTORY=/mnt/data/dexp FILE=dk15.dmp LOG=db_str15.log

ENCRYPT=Y ENCRYPT_PASSWORD=abcdefg ENCRYPT_NAME=DES_CBC FULL=Y
```

> **注意**
>
>
> **ENCRYPT、ENCRYPT_PASSWORD、ENCRYPT_NAME经常一起使用。如果设置了ENCRYPT_PASSWORD或ENCRYPT_NAME，那么默认ENCRYPT=Y，此时设置ENCRYPT=N无效。**



#### 2.3.2.18 FILESIZE

FILESIZE 用于指定单个导出文件（FILE）大小或日志文件（LOG）大小的上限。可以按字节[B]、K[B]、M[B]、G[B]的方式指定大小。可选参数。

使用 FILESIZE 参数时，FILE（或 LOG）必须使用 %U 对名称进行自动扩展，否则报错。如果不指定 FILE（或 LOG），那么即使设置了 FILESIZE 也不起作用。FILE 和 LOG 同时使用 %U 时，FILESIZE 参数只需指定一次。

语法如下：

```
FILESIZE=<num>
```

：单个导出文件上限。整数类型，取值范围：128M~系统最大值。

例  以 FULL 方式导出实例，每个导出文件大小为 128m，文件名以 data01.dmp 自动扩展，存储位置为/mnt/data/dexp 目录。

```
./dexp USERID=SYSDBA/***** DIRECTORY=/mnt/data/dexp FILE=data%u.dmp
LOG=data%u.log FILESIZE=128m FULL=Y
```

> **注意**
>
>
> dexp执行过程中可能遇到单个文件过大的问题。解决办法是通过FILESIZE参数来设置单个导出文件上限，导出文件名称按照FILE参数中描述的使用%u来自动扩展。这样在按照FILESIZE大小导出到第一个文件之后，会自动生成下一个导出文件。



#### 2.3.2.19 FILENUM

FILENUM 用于指定一个模板导出文件的个数。范围为 1~99，缺省为 99。可选参数。FILENUM 需要和 FILESIZE 搭配使用，FILENUM*FILESIZE 要大于等于导出的文件总大小，否则会报错空间不足。

#### 2.3.2.20 DROP

DROP 用于设置导出后是否删除原表。可选参数。

语法如下：

```
DROP=Y/N
```

Y：导出后删除原表，但不级联删除。

N：导出后不删除原表，缺省值。

#### 2.3.2.21 DESCRIBE

DESCRIBE 用于设置导出数据文件的描述信息，记录在数据文件中。可选参数。

语法如下：

```
DESCRIBE=’字符串’
```

字符串：设置导出数据文件的描述信息，记录在数据文件中。缺省时，导出的数据文件中没有描述信息。

#### 2.3.2.22  CTRL_INFO

CTRL_INFO 控制信息。可选参数，默认为 0。

语法如下：

```
CTRL_INFO=[n]
```

n 取值为整型数值。

1：控制分区表约束在表定义导出。

0：分区表约束分开导出。

#### 2.3.2.23  FLASHBACK_SCN

FLASHBACK_SCN 用于指定导出表数据的闪回 LSN。可选参数。由于 dexp 闪回导出是通过闪回查询方式实现，闪回查询功能存在的限制在 dexp 同样存在，详情见《DM8_SQL 语言使用手册》中第 17 章。

语法如下：

```
FLASHBACK_SCN=LSN
```

LSN：可以通过查询动态视图 V$RLOG 或 V$LSN_TIME 来确定。

#### 2.3.2.24  FLASHBACK_TIME

FLASHBACK_TIME 用于指定导出表数据的闪回时间。可选参数。由于 dexp 闪回导出是通过闪回查询方式实现，闪回查询功能存在的限制在 dexp 同样存在，详情见《DM8_SQL 语言使用手册》中第 17 章。

语法如下：

```
FLASHBACK_TIME="time_exp"
```

time_exp：时间字符串。

例 使用 FLASHBACK_TIME 指定'2023/2/22 13:10:10'为导出数据的闪回时间。

```
./dexp USERID=SYSDBA/***** FLASHBACK_TIME="'2023/2/22 13:10:10'"
```

#### 2.3.2.25  COL_DEFAULT_SEPARATE

COL_DEFAULT_SEPARATE 用于设置是否单独导出列默认值。可选参数。

语法如下：

```
COL_DEFAULT_SEPARATE=Y/N
```

Y：单独导出列默认值，缺省值。

N：列默认值和表定义一起导出。

#### 2.3.2.26 WITH_UR

WITH_UR 用于设置导出数据是否允许脏读。可选参数。

语法如下：

```
WITH_UR=N/Y
```

N：导出表数据不允许脏读，缺省值。

Y：导出表数据允许脏读。

#### 2.3.2.27 SIMPLE_LOG

SIMPLE_LOG 用于设置导出日志是否只打印简要日志，简要日志只打印导出对象个数和导出表数据行数。可选参数。

语法如下：

```
SIMPLE_LOG=N/Y
```

N：导出日志不使用简要日志，缺省值。

Y：导出日志使用简要日志。

#### 2.3.2.28 CONFIG_FILE

CONFIG_FILE 用于指定局部配置文件路径，局部配置文件中可以配置导入导出默认连接串和默认密码。可选参数。长度不超过 256 个字符。

语法如下：

```
CONFIG_FILE=<path>
```

\<path>：局部配置文件路径。

例 设置 CONFIG_FILE=/mnt/data/dexp/local_config.txt。

```
./dexp CONFIG_FILE=/mnt/data/dexp/local_config.txt FILE=test.dmp
```

其中，局部配置文件 local_config.txt 中的内容如下：

```
DUMP_CONN_STR=(SYSDBA/*****@LOCALHOST:5236)

DUMP_PASSWORD=(*****)
```

局部配置文件目前仅支持以下两个配置项：

DUMP_CONN_STR：指定导入导出默认连接串。没有指定 USERID 连接串时，使用该连接串。

DUMP_PASSWORD：指定导入导出默认连接密码。USERID 缺省密码时，使用该密码。

全局配置文件 dm_svc.conf 中也支持配置 DUMP_CONN_STR 和 DUMP_PASSWORD，局部配置文件的优先级高于全局配置文件 dm_svc.conf。

#### 2.3.2.29 FILE_VERSION

FILE_VERSION 用来指定导出的 dmp 文件的逻辑版本，有效范围为 9~25。可选参数。

语法如下：

```
FILE_VERSION = <num>
```

\<num>：指定导出的 dmp 文件的逻辑版本，为 9~25 之间的正整数。

例 将整个数据库导出至逻辑版本为 15 的文件 dexp.dmp 中。

```
./dexp SYSDBA/***** FULL=Y FILE=dexp.dmp DIRECTORY=/mnt/data/dexp FILE_VERSION=15
```

#### 2.3.2.30 HELP

输入 dexp HELP 即可查看帮助信息。

语法如下：

```
HELP
```

例  使用 HELP 查看帮助信息。

```
./dexp HELP
```

HELP 信息会显示 dexp 版本信息以及所有参数的大致信息，供用户快速参考。

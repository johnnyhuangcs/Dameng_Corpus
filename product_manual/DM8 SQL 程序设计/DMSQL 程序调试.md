

# DMSQL 程序调试

DM 提供了 DMSQL 程序调试功能。可调试直接执行的 DMSQL 程序或非 DDL 的 SQL 语句，以便开发人员定位 DMSQL 程序中存在的错误。

DM 提供了两种调试 DMSQL 程序的工具：dmdbg 工具和 MANAGER 工具。

## 8.1 使用 dmdbg 工具调试 DMSQL 程序

要使用 dmdbg 调试工具，首先要调用系统过程 SP_INIT_DBG_SYS(1)创建调试所需要的包。

### 8.1.1 启动 dmdbg 工具

为了使用 dmdbg，必须首先要启动 dmdbg。dmdbg 工具可以广泛应用于各种操作系统，如 Windows、Linux 等。

启动之后，当出现 “DBG>”符号时，用户就可以进行调试 DMSQL 程序的交互操作了。

#### 8.1.1.1 从安装目录中启动

在 Windows 环境中，安装 DM8 数据库管理系统后，在安装目录的“bin”子目录下可找到 dmdbg 执行程序。例如，DM 数据库的安装目录为 D:\dmdbms，则 dmdbg 位于 D:\dmdbms\bin\dmdbg.exe。双击启动。

启动之后，使用 LOGIN 命令登录到目标数据库。

#### 8.1.1.2 从操作系统命令行启动

从操作系统命令行启动 dmdbg 适用于任何操作系统平台。有两种启动方式：一是只启动 dmdbg，但不登录数据库；二是启动 dmdbg 的同时登录数据库。

  * 方式一 只启动 dmdbg，但不登录数据库



例 在命令行工具中找到 dmdbg 所在安装目录 D:\dmdbms\bin，输入 dmdbg 后回车。

```
D:\dmdbms\bin>DMDBG

dmdbg V8
```

启动之后，使用 LOGIN 命令登录到目标数据库。

  * 方式二 启动 dmdbg 的同时登录数据库



例 在命令行工具中找到 dmdbg 所在安装目录，输入 dmdbg 和参数后回车。

```
./dmdbg userid=SYSDBA/*****@192.168.100.122:8888##"{inet_type=tcp}"

dmdbg V8
```

### 8.1.2 dmdbg 状态

dmdbg 在整个运行过程中可以处于初始状态(S)、待执行(W)、执行(R)、调试(D)、执行结束(O)等不同的状态：

  * 初始状态(S)：工具启动完成后，尚未设置调试语句；
  * 待执行状态(W)：设置调试语句后，等待用户执行；
  * 执行状态(R)：开始执行后，未中断而运行的过程；
  * 调试状态(D)：执行到断点或强制中断后进入交互模式；
  * 执行结束(O)：执行完当前设置的调试语句，并返回结果。



dmdbg 在不同的状态下可以执行不同的操作，如表 8.1 所示，其中备注表示不同的命令分别在哪几种状态下可以使用。

### 8.1.3 dmdbg 参数一览表

表8.1 dmdbg工具的调试命令

|**参数**|**说明**|**状态**|
|--|--|--|
|USERID|数据库的连接信息|-|
|SQL|设置 SQL 语句，SQL 语句为非 DDL 语句或语句块|S/W/O|
|B|设置断点，格式：B [方法名:]\< 行号 >|W/D/O|
|C|继续执行 SQL|D|
|CONTINUE|继续执行 SQL|D|
|D|取消断点，格式：D \< 断点序号 >|W/D/O|
|DELETE|取消断点，格式：DELETE \< 断点序号 >|W/D/O|
|INFO B|显示断点|W/D/O|
|R|执行语句|W/O|
|RUN|执行语句|W/O|
|N|单步执行|D|
|NEXT|单步执行|D|
|S|执行进入|D|
|F|执行跳出|D|
|FINISH|执行跳出|D|
|L|显示脚本，格式：L [方法名]|D|
|P|打印变量，格式：P \< 变量名 >|D|
|CTRL+C|中断执行|R|
|LOGIN|登录 DMDBG|S/W/D/O|
|KILL|结束执行|W/D/O|
|Q|退出 DMDBG|S/W/D/O|
|QUIT|退出 DMDBG|S/W/D/O|
|BT|显示堆栈|D|
|BTREE|显示堆栈|D|
|UP|上移堆栈|D|
|DOWN|下移堆栈|D|
|EXPLAIN[行号]|显示 SQL 计划，格式：EXPLAIN [行号]|D|
|EXPLAIN\<游标名 >|显示游标计划，格式：EXPLAIN \< 游标名 >|D|
|HELP|打印帮助信息|-|


### 8.1.4 dmdbg 参数详解

#### 8.1.4.1 登录参数

##### 8.1.4.1.1 USERID

USERID 用于从操作系统命令行启动 dmdbg 的同时登录数据库之用。USERID 用于指定数据库的连接信息。

语法如下：

```
dmdbg USERID=<userid>
<userid>::={{<username>[/<password>]} | /}[@<connect_identifier>][<option>] [<os_auth>] <connect_identifier> ::=<svc_name> 
								| {<host>[:<port>]} 
								| <unixsocket_file>
<option>::= #{ <exetend_option>=<value>{,<extend_option>=<value>} }  //此行外层{}是为了封装参数之用，书写时需要保留
<os_auth>::= AS {SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO}
```

{{\<username>[/\<password>]} | /}：\<username>[/\<password>]为用户名和密码。普通登录方式时用户名必写。/表示采用操作系统身份验证方式登录或利用 wallet 文件登录。采用操作系统身份验证方式登录时无需指定用户名和密码，即使指定也会被忽略。利用 wallet 文件登录时，dm_svc.conf 文件中的配置项 WALLET_LOCATION 必须非空，客户端会通过用户输入的服务名以及 WALLET_LOCATION 配置项指定的 wallet 文件路径自动获取 wallet 文件中服务名所对应的用户名和密码，因此用户无需输入用户名和密码，若用户输入了用户名和密码，则优先使用用户输入的用户名和密码登录数据库，关于利用 wallet 文件登录数据库的更多详细介绍请参考手册《DM8 安全管理》。

\<svc_name>：服务名。服务名在 dm_svc.conf 中配置。dm_svc.conf 的配置请参考《DM8 系统管理员手册》。

\<host>[:\<port>]：服务器 IP 地址和端口号。缺省情况下默认为本地服务器和端口号 LOCALHOST:5236。当服务器为本机时，SERVER:PORT 可直接写 LOCALHOST。当连接其他服务器时，SERVER:PORT 需写上 IP 地址和 PORTNUM，例如：192.168.0.248:8888。

\<unixsocket_file>：专门用于在 LINUX 系统中，当服务器与客户端之间使用 UNIXSOCKET（UNIX DOMAIN SOCKET - IPC）协议通信时，指定客户端连接的 socket 文件路径。例如：

```
./dmdbg SYSDBA/*****@/home/test/foo.sock#{inet_type=UNIXSOCKET}
```

\<option>：扩展选项，用法为 \<exetend_option>=\<value>。所有 value 值不能包含空格，不能包含特殊的符号，如引号等。书写扩展选项时需要用引号#"{}"进行封装，例如：#"{INET_TYPE=tcp,mpp_type=local}"。

现支持的扩展选项如下：

|**extend_option**|**value**|
|-------------|-------------|
|mpp_type|MPP 登录属性，此属性的设置对非 MPP 系统没有影响。取值 GLOBAL 和 LOCAL，为 GLOBAL。GLOBAL 表示 MPP 环境下建立的会话为全局会话，对数据库的导入导出操作在所有节点进行；LOCAL 表示 MPP 环境下建立的会话为本地会话，对数据库的导入导出操作只在本地节点进行|
|inet_type|网络通信协议类型。取值 TCP/UDP/IPC/UNIXSOCKET/RDMA，缺省为 TCP。使用 UDP 协议时，服务器需要设置 INI 参数 ENABLE_UDP 非 0；使用 IPC 协议时，会忽略 IP 地址和端口号，直接连接本机上 INI 参数 ENABLE_IPC=1 的服务器，一台机器上只能有一个服务器设置 ENABLE_IPC=1；使用 UNIXSOCKET 协议时，无需指定 IP 地址和端口号，但服务器需要配置 INI 参数 UNIX_SOCKET_PATHNAME 指定 socket 文件路径，客户端连接服务器时也需要指定 socket 文件路径，二者必须一致，仅 LINUX 环境下支持 UNIXSOCKET；使用 RDMA 协议时，需要安装并配置 RDMA 网卡，连接数据库时需要指定 RDMA 网卡配置的 IP 地址以及服务器的端口号|
|ssl_path|通信加密的 SSL 数字证书路径，缺省为不使用加密。数字证书路径由用户自己创建，将相应的证书需放入该文件夹中。其中服务器证书必须与 dmserver 目录同级，客户端目录可以任意设置。和 ssl_pwd 一起使用。各用户只能使用自己的 SSL 数字证书，例如 SYSDBA 账户只能使用\bin\CLIENT_SSL\SYSDBA 下的证书和密码，如果证书没有密码可以用缺省或任意数字代替。例如：./dmdbg SYSDBA/*****@192.168.1.64:5236#"{ssl_path= /home/dmdbms/bin/client_ssl/SYSDBA,ssl_pwd=12345}" file=/home/dexp_test.dmp log=/home/log/|
|ssl_pwd|通信加密的 SSL 数字证书密码。和 ssl_path 一起使用。缺省为不加密|


例如，一个包含扩展选项的例子。

```
./dmdbg SYSDBA/*****@192.168.1.64:5236##"{mpp_type=local,inet_type=tcp}"
```

AS \<SYSDBA|SYSSSO|SYSAUDITOR|USERS|AUTO>：操作系统身份验证。用户可以通过将操作系统用户加入到操作系统的 dmdba|dmsso|dmauditor 用户组来使用操作系统用户登录数据库，分别对应数据库的 SYSDBA|SYSSSO|SYSAUDITOR 用户。还可以通过将操作系统用户加入到操作系统的 dmusers 用户组来使用操作系统用户登录数据库，对应数据库的同名用户。AUTO 表示按顺序自动匹配数据库用户类型。操作系统身份验证无需输入用户名和密码，若输入用户名和密码将会被忽略。操作系统身份验证的其他说明可参考《DM8 安全管理》2.3.1 节。

##### 8.1.4.1.2 LOGIN

LOGIN 命令用于启动 dmdbg 之后，在 dmdbg 界面中使用。使用 LOGIN 命令可登录目标数据库。

进入 dmdbg 命令行工具窗口。在命令行输入 LOGIN 命令进行登录，会提示用户输入服务名/IP 地址:端口号、用户名、密码、端口号、ssl 路径和 ssl 密码。如下所示：

```
DBG> login

server:

user name:

password:

ssl path:

ssl password:

login time used:7.741(ms)

服务器[LOCALHOST:5236]:处于普通打开状态
```

server：数据库服务名或 IP 地址:端口号。IP 地址缺省为 LOCALHOST，表示本地服务器。端口号缺省为 5236。server 缺省值为 LOCALHOST:5236；

user name 和 password：用户名和密码，密码不回显；

ssl path 和 ssl password：ssl 路径和 ssl 密码，用于服务器通信加密，不加密的用户不用设置，缺省为不设置。

#### 8.1.4.2 调试命令参数

调试命令参数用于进入 dmdbg 界面之后输入使用。

##### 8.1.4.2.1 设置语句

命令 SQL 供用户设置需要执行的 SQL 语句，其命令格式如下：

```
SQL <SQL语句> ['/']
```

SQL 语句是非 DDL 语句或语句块。如果 SQL 是单条语句，则以分号“;”结尾；如果是语句块，则语句块后必须以单独一行的斜杠符“/”结束。在调试中行数从 SQL 语句实际起始行开始计数。

在实际执行前，可以多次调用 SQL 命令设置待调试的语句，而 dmdbg 只是记录最后一次设置的语句。

> **注意**
>
> 如果需要调试自定义的存储函数，需要和过程调用方式一样，使用call调用。



下面我们用一个具体的示例来说明如何使用 SQL 命令设置语句，并将使用这个例子贯穿后续的调试命令介绍。

首先，对象和数据初始化，在 DM 中使用下面的脚本创建表和存储过程：

```
CREATE TABLE STUDENT (ID INT, NAME VARCHAR(20));	//创建游标类型对应的表
INSERT INTO STUDENT VALUES(10010,'ZHANGSAN');
INSERT INTO STUDENT VALUES(10011,'LISI');

CREATE OR REPLACE PROCEDURE P0 AS				    //创建需要调试的存储过程
BEGIN
PRINT 'HELLO';
PRINT 'WORLD';
END;
/

CREATE CLASS MYCYCLE                                //定义类对象
AS
ID INT;
C1 INT;
FUNCTION MYCYCLE(ID INT , C1 INT) RETURN MYCYCLE;
END;
/

CREATE OR REPLACE CLASS BODY MYCYCLE                //定义类体
AS
FUNCTION MYCYCLE(ID INT, C1 INT) RETURN MYCYCLE
AS
BEGIN
	THIS.ID = ID;
	THIS.C1 = C1;
	RETURN THIS;
END;
END;
/

CREATE OR REPLACE PROCEDURE P AS				   //创建需要调试的存储过程
TYPE T_REC IS RECORD(ID INT, NAME VARCHAR(20));    //定义记录类型
TYPE ARR is ARRAY INT[4];					       //定义数组类型
R1 T_REC;							               //声明记录类型
C1 CURSOR;							               //声明游标类型
B ARR;								               //声明数组类型
O MYCYCLE;							               //声明对象类型
I1 INT;							                   //声明整型
V1 VARCHAR(20);						               //声明字符串类型
BEGIN
R1.ID = 10012;
R1.NAME = 'WANGWU';
OPEN C1 FOR SELECT * FROM STUDENT;
LOOP
FETCH C1 INTO I1,V1;
EXIT WHEN C1%NOTFOUND;
END LOOP;
FOR I IN 1..4 LOOP  
B[I] := I * 10;
PRINT B[I];
END LOOP;
O = NEW MYCYCLE(1, 2);
CALL P0;
END;
/
```

然后，按前面介绍的方法登录 dmdbg，使用 SQL 命令设置调试语句，如下：

```
DBG>SQL CALL P;
```

##### 8.1.4.2.2 设置断点

使用命令 B 设置调试断点，可以为指定的语句块设置执行中断位置。调试执行到断点设置位置后，将自动中断当前执行。

设置断点命令有两种不同格式：

```
B <方法名>

或

B [<方法名>:]<行号>
```

如果省略方法名，则为当前方法或语句块设置行号断点。包中的方法可以设置断点，但不能指定行号；如果断点设置在包上，则可以指定行号，但可以设置断点部分只能是该包体的初始化代码。

行号从 1 开始计数。对于方法来说，其开始的定义声明部分语句也包含在行号计数中。

对于断点的行号设置必须遵守以下原则：

  * 定义声明部分不能设置断点；
  * 如果一条语句跨多行，则断点应设置在语句最后一行；
  * 如果多条语句在同一行，则执行中断在其中第一条语句执行前；
  * 如果设置断点行号不能中断，则自动将其下移到第一个可以中断的行；
  * 可以在调试过程中动态添加断点，如果在同一位置重复设置断点，则重复断点被忽略。



继续前面的例子，设置断点：

```
DBG>B 3					            //没有方法名，则在最顶层设置断点
No line 3 in method "@dbg_main".	//由于顶层只有一行，无法设置断点，报错返回
DBG>B P:3					        //不能设置断点，则下移到第一条可以中断的行
Breakpoint 1 at SYSDBA.P, line 11@{R1.ID = 10012;}
DBG>B P:12					        //直接在指定行设置断点
Breakpoint 2 at SYSDBA.P, line 12@{R1.NAME = 'WANGWU';}
```

##### 8.1.4.2.3 显示断点

显示断点命令为：

```
INFO B
```

该命令显示所有已设置的断点。显示的每个断点包含以下信息：

  * 序号：这是一个自增值，从 1 开始，设置断点时自动为其分配；
  * 方法名：如果为语句块，则方法名为空；
  * 行号。



继续前面的例子，显示断点：

```
DBG> INFO B

Num     Schema  Package  Method          What

1       SYSDBA  NULL    P       line 11@{R1.ID = 10012;}

2       SYSDBA  NULL    P       line 12@{R1.NAME = 'WANGWU';}
```

##### 8.1.4.2.4 取消断点

使用命令 D 或 DELETE 取消断点，格式如下：

```
D <断点序号>

或

DELETE <断点序号>
```

断点序号可以通过 INFO B 命令获取，删除后断点的编号不会重用。取消断点命令可以在工具运行的任何时间调用。

继续前面的例子，取消断点：

```
DBG>D 1			//删除断点，成功
DBG>D 3			//删除不存在的断点，则报错
No breakpoint number 3.
DBG>INFO B		//显示断点信息
Num     Schema  Package Method  What
2       SYSDBA  NULL    P       line 12@{R1.NAME = 'WANGWU';}
```

##### 8.1.4.2.5 执行语句

执行语句命令格式为：

```
R | RUN
```

命令 R 或 RUN 执行设置的 SQL 语句。此命令无参数，执行 R 命令前必须已经设置过调试语句，且调试过程中命令 R 或 RUN 不允许重复使用。

如果设置了断点，则执行到断点指定的位置时中断，转入调试状态，并显示当前执行所在行的语句；否则，执行完成并显示结果。

继续前面的例子，执行语句：

```
DBG>R
Breakpoint 2, SYSDBA.P line: 12@{R1.NAME = 'WANGWU';}
DBG>R						//调试过程中不可以再次运行
The program is already running.
DBG>B P:23					//调试过程中可以设置断点
Breakpoint 3 at SYSDBA.P, line 23@{CALL P0;}
```

##### 8.1.4.2.6 显示脚本

使用命令 L 可以在 dmdbg 工具中显示指定方法的脚本。命令格式如下：

```
L <方法名>
```

方法名可以包含模式、包等前缀。在调试状态下，可以省略方法名，则显示当前正在运行的方法。此命令可以帮助用户准确设置断点位置。

L 命令每次显示 5 行代码，再次执行 L 命令时（不再需要带方法名），从已显示的下一行开始显示。

> **注意**
>
> 命令L不能显示包的单个方法脚本。



继续前面的例子，显示脚本：

```
DBG>L SYSDBA.P
1       CREATE OR REPLACE PROCEDURE P AS	//创建需要调试的存储过程
2       TYPE T_REC IS RECORD(ID INT, NAME VARCHAR(20)); //定义记录类型
3       TYPE ARR is ARRAY INT[4];		//定义数组类型
4       R1 T_REC;			//声明记录类型
5       C1 CURSOR;			//声明游标类型
DBG>L			//注意，第二次以后显示脚本不要带方法名，否则从第一行显示
6       B ARR;			//声明数组类型
7       O MYCYCLE;		//声明对象类型
8       I1 INT;			//声明整型
9       V1 VARCHAR(20);	//声明字符串类型
10      BEGIN
```

##### 8.1.4.2.7 中断执行

中断执行命令为：

```
CTRL+C
```

在正常执行过程中，当监听到用户输入 CTRL+C，则 dmdbg 强制中断当前执行，转入调试状态。该命令对于调试死循环错误非常有效。

##### 8.1.4.2.8 继续执行

继续执行命令为：

```
C | CONTINUE
```

在调试状态下，可以使用命令 C 或 CONTINUE 继续 SQL 语句的执行，直到运行到断点或执行完成。

继续前面的例子，继续执行：

```
DBG>C

10

20

30

40

Breakpoint 3, SYSDBA.P line: 23@{CALL P0;}
```

##### 8.1.4.2.9 执行进入

执行进入命令为：

```
S
```

如果当前中断语句包含方法调用，则执行进入命令 S 可以将方法调用展开，执行到其语句块的第一条语句中断。如果当前行没有方法调用，则命令 S 与单步执行命令 N 等效。

继续前面的例子，执行进入：

```
DBG>S					        //执行进入存储过程P0
SYSDBA.P0 line: 3       @{PRINT 'HELLO';}
```

> **注意**
>
> 对于确定性函数，会直接进行结果重用，在重复调用时将不能执行进入调试。



##### 8.1.4.2.10 单步执行

单步执行命令为：

```
N | NEXT
```

在调试状态下，单步执行命令 N 或 NEXT 可以执行到当前层次下一个可中断的行。如果当前行包含多条语句，则所有该行语句都被执行。

继续前面的例子，单步执行：

```
DBG>N					        //单步执行存储过程P0
SYSDBA.P0 line: 4       @{PRINT 'WORLD';}
```

##### 8.1.4.2.11 执行跳出

执行跳出命令为：

```
F

或

FINISH
```

该命令可以执行当前函数的余下操作，如果剩余行有断点则执行到断点中断，否则返回到上层调用后中断。如果当前已经是最上层，则与单步执行命令 N 等效。

继续前面的例子，执行跳出：

```
DBG>F					//跳出存储过程P0
WORLD
SYSDBA.P line: 23       @{CALL P0;}
```

##### 8.1.4.2.12 打印变量

打印变量命令的格式为：

```
P <变量名>
```

命令 P 可以打印指定的变量值。SQL 类型中，除了大字段数据，其它类型都可以打印。DMSQL 数据类型往往为复杂数据类型，其显示格式如下：

  * 结构类型



在大括号内显示结构所有成员的值，各成员值用“,”分隔。

  * 数组类型



如果是数组，则可以与结构类型一样，依次显示数组所有成员的值，也可以只显示其中某一下标的值。

  * 对象类型



可以打印对象中所有的静态成员，或者打印某一个静态成员，函数可以不打印。

  * 游标类型



游标也是对象类型，其成员如表 8.2 所示：

表8.2 游标对象成员

|**名称**|**数据类型**|**说明**|
|----|----|----|
|ROWCOUNT|BIGINT|当前游标查询获取的总行数。如果游标未打开或查询失败时该值无效|
|FOUND|INT|最近一次 FETCH 操作是否取得数据，如果取得则为 1，否则为 0。如果游标未打开或查询失败时该值无效|
|ISOPEN|INT|游标是否打开。打开为 1，否则为 0|
|ROWCOUNT2|BIGINT|游标打开时确定的查询的结果集总行数，如果游标未打开或查询失败时该值无效|


命令 P 只能打印当前方法所在栈帧中的变量，如果要打印其它层次方法栈帧的变量，首先需要移动到对应栈帧。打印变量的信息以 $ 开始，从 1 开始计数，每打印一个变量，自动增加 1。

继续之前的例子，重新开始执行，并打印变量：

```
DBG>C						    //结束之前的执行
0 rows affected
DBG>R						    //开始执行
Breakpoint 2, SYSDBA.P line: 12@{R1.NAME = 'WANGWU';}
DBG> P R1.ID					//打印记录类型中单个字段
$1 = 10012
DBG> N						    //单步执行后，R1所有字段都被赋值
SYSDBA.P line: 13       @{OPEN C1 FOR SELECT * FROM STUDENT;}
DBG>P R1					    //打印记录类型R1
$2 = {ID=10012, NAME=WANGWU}
DBG>C						    //继续运行，遇断点则中断
10
20
30
40
Breakpoint 3, SYSDBA.P line: 23@{CALL P0;}
DBG>P C1					    //打印游标的所有成员值
$3 = {ROWCOUNT=2, FOUND=0,ISOPEN=1, ROWCOUNT2=2}
DBG>P B					        //打印数组
$4 = {10, 20, 30, 40}
DBG>P B[3]					    //数组下标以1开始
$5 = 30
DBG> P O.C1					    //打印对象类型中的成员
$6 = 2
DBG> P O					    //打印对象类型
$7 = {ID=1, C1=2}
```

##### 8.1.4.2.13 显示堆栈

显示堆栈命令为：

```
BT | BTREE
```

命令 BT 或 BTREE 可以显示当前执行的堆栈状态。首先显示当前方法执行到的行数，然后从下至上显示各层方法名称及行号和调用参数值，输出参数不打印。

继续前面的例子，显示堆栈：

```
DBG>BT
#0      SYSDBA.P()      line: 23@{CALL P0;}	     //#0表示最下层
#1      @dbg_main       line: 1@{CALL P;}         //表示栈帧只有两层
```

##### 8.1.4.2.14 下移栈帧

下移栈帧命令为：

```
DOWN
```

命令 DOWN 可以将当前调试栈帧下移一层。下移后，显示当前层执行到的语句行数。如果当前已经是堆栈最下层，则此命令无效。

继续前面的例子，下移栈帧：

```
DBG>DOWN                        //存储过程已到最下层，无法下移
Bottom (innermost) frame selected; you cannot go down.
```

##### 8.1.4.2.15 上移栈帧

上移栈帧命令为：

```
UP
```

命令 UP 可以将当前调试栈帧上移一层。上移后，显示当前层执行到的语句行数。如果当前已经是堆栈最上层，则此命令无效。

继续前面的例子，上移栈帧：

```
DBG>UP 
#1      @dbg_main       line: 1@{CALL P;}
DBG>UP							//已经到最上层，无法上移
Initial frame selected; you cannot go up.
DBG> C							//执行结束，并显示结果
HELLO
WORLD
0 rows affected
```

##### 8.1.4.2.16 结束执行

结束执行命令为：

```
KILL
```

在调试过程中，用户可以使用命令 KILL 结束当前正在调试的 SQL 语句，并重新设置待调试的 SQL 语句。

KILL 命令结束执行与 R 命令执行结束的处理是不同的：

  * KILL 之后，必须重新设置 SQL 语句，且断点信息被清空；
  * R 返回结果结束执行之后，如果没有重新设置 SQL 语句，则再次运行的仍是之前设置的 SQL 语句，断点信息可以重用；如果重新设置了 SQL 语句，则断点信息亦被清空。



##### 8.1.4.2.17 退出工具

退出 dmdbg 工具的命令为：

```
Q或QUIT
```

如果不希望继续调试，可以输入命令 Q 或 QUIT。DMDBG 工具将中断连接，退出执行。

##### 8.1.4.2.18 显示游标执行计划

显示游标执行计划的命令为：

```
EXPLAIN <cursor_name>
```

cursor_name 为游标名。游标名支持三种格式：游标变量名、包名.游标名、模式名.包名.游标名。

例如，下面展示显示多种游标的执行计划。

首先，创建系统包和各种游标。

```
SP_INIT_DBG_SYS(1);	         //创建系统包

create or replace package dbgcsrpkg01 as
cursor csr01 for select 1;   //静态游标
cursor csr02;                //动态游标
procedure dbgcsrpro01(c1 int);
end;
/

create or replace package body dbgcsrpkg01 as
procedure dbgcsrpro01(c1 int)
as
begin
  print('因为静态游标在编译时就能确定，所以只要包头创建好了，就可以显示这个包中静态游标的执行计划了');
  open csr02 for select * from dual;
  print('因为动态游标不指定其关联的查询语句，在执行部分OPEN游标后，才能显示动态游标执行计划');
  close csr02;
  print('close后，不能显示动态游标执行计划');
 end;
 end;
 /
```

其次，在 DBG 中进行调试。

```
 ----------------------------在dbg中调试-----------------------
DBG> sql call dbgcsrpkg01.dbgcsrpro01(1);
DBG> b 1
Breakpoint 1 at @dbg_main, line: 1@{call dbgcsrpkg01.dbgcsrpro01(1);}

DBG> r
Breakpoint 1,  line: 1@{call dbgcsrpkg01.dbgcsrpro01(1);}

DBG> s
SYSDBA.DBGCSRPKG01 line: 5      @{  print('因为静态游标在编译时就能确定，所以只要包头创建好了，就可以显示这个包中静态游标的执行计划了');}

DBG> p dbgcsrpkg01.csr01
$1 = {ROWCOUNT=0, FOUND=0, ISOPEN=0, ROWCOUNT2=NULL}

DBG> explain dbgcsrpkg01.csr01

1   #NSET2: [1, 1, 1]
2     #PRJT2: [1, 1, 1]; exp_num(1), is_atom(FALSE)
3       #CSCN2: [1, 1, 1]; SYSINDEXSYSDUAL(SYSDUAL);btr_scan(1)

DBG> explain dbgcsrpkg01.csr02
No symbol " dbgcsrpkg01.csr02" in current context.

DBG> n
因为静态游标在编译时就能确定，所以只要包头创建好了，就可以显示这个包中静态游标的执行计划了
SYSDBA.DBGCSRPKG01 line: 6      @{  open csr02 for select * from dual;}

DBG> n
SYSDBA.DBGCSRPKG01 line: 7      @{  print('因为动态游标不指定其关联的查询语句，在执行部分OPEN游标后，才能显示动态游标执行计划');}

DBG> n
因为动态游标不指定其关联的查询语句，在执行部分OPEN游标后，才能显示动态游标执行计划

SYSDBA.DBGCSRPKG01 line: 8      @{  close csr02;}

DBG> explain csr02

1   #NSET2: [1, 1, 1]
2     #PRJT2: [1, 1, 1]; exp_num(1), is_atom(FALSE)
3       #CSCN2: [1, 1, 1]; SYSINDEXSYSDUAL2(SYSDUAL2);btr_scan(1)


DBG> n
SYSDBA.DBGCSRPKG01 line: 9      @{  print('close后，不能显示动态游标执行计划');}

DBG> explain csr02
No symbol " csr02" in current context.
```

##### 8.1.4.2.19 显示 SQL 执行计划

显示 SQL 语句执行计划的命令为：

```
EXPLAIN <行号>
```

##### 8.1.4.2.20 打印帮助信息

打印 HELP 帮助信息：

```
HELP
```

## 8.2 使用图形化客户端工具 Manager 调试 DMSQL 程序

DM 图形化客户端管理工具 Manager 也提供调试 DMSQL 程序的功能。在 WINDOWS 操作系统下的 DM8 数据库系统，可以通过选择“开始”-“程序”-“达梦数据库”-“客户端”，然后选择 DM 管理工具。或者在安装目录的“bin”子目录下可找到 manager.exe。

运行管理工具 Manager，其主界面如下图所示。

![DM 管理工具主界面](https://download.dameng.com/eco/docs/asset/pm/sql-programming/DM管理工具主界面.png)

图8.1 DM管理工具主界面

![调试](https://download.dameng.com/eco/docs/asset/pm/sql-programming/调试.png)

![DMSQL 程序调试工具登录界面](https://download.dameng.com/eco/docs/asset/pm/sql-programming/DMSQL程序调试工具登录界面.jpg)

图8.2 DMSQL程序调试工具登录界面

![对象选择](https://download.dameng.com/eco/docs/asset/pm/sql-programming/对象选择.png)

![SQL 调试界面](https://download.dameng.com/eco/docs/asset/pm/sql-programming/SQL调试界面.png)

图8.3 SQL调试界面

![开始](https://download.dameng.com/eco/docs/asset/pm/sql-programming/开始.png)

![开始](https://download.dameng.com/eco/docs/asset/pm/sql-programming/开始.png)

![SQL 调试的调试状态](https://download.dameng.com/eco/docs/asset/pm/sql-programming/SQL调试的调试状态.png)

图8.4 SQL调试的调试状态

![停止](https://download.dameng.com/eco/docs/asset/pm/sql-programming/停止.png)

![按钮](https://download.dameng.com/eco/docs/asset/pm/sql-programming/按钮.png)

![进入](https://download.dameng.com/eco/docs/asset/pm/sql-programming/进入.png)

![SQL 调试](https://download.dameng.com/eco/docs/asset/pm/sql-programming/SQL调试.png)

图8.5 SQL调试



# DM 性能监视工具

## 一、概述

DM 性能监视工具是系统管理员用来监视服务器的活动和性能情况，并对系统参数进行调整的客户端工具，它允许系统管理员在本机或远程监视服务器的运行状况。DM 性能监视工具包含如下基本功能：

  * 统计分析
  * 性能监视
  * 调优向导
  * 预警警告



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402191008288FH5B8MYCQ5MW70Q28)

如上图中，右侧为 DM 性能监视工具主界面：显示数据库内存使用情况、CPU 使用情况、线程和会话信息。

左侧为性能导航模块，展示性能监视工具监控和管理的对象的层次结构。性能导航主要包括统计分析、性能监视、调优向导、预警配置。

## 二、统计分析

统计分析视图监视系统资源，数据库可用性，健康状态，性能等。显示系统自启动以来各项资源的使用历史情况以及平均使用率。包括内存和 hash 缓存的使用情况，逻辑、物理读写情况，事务总次数以及提交、回滚、死锁次数、会话分配和空闲的次数以及检查点的次数，sql 的执行情况、系统 CPU 使用率以及线程切换情况。

统计分析界面如下图所示:

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240219101013354BB5VMD485IOUI6S)

## 三、性能监视

性能监视功能根据服务器运行情况，监视系统和数据库各参数运行状态，包括：线程监视、会话监视、事务监视、SQL 监视、SQL 日志文件分析、dm.ini 参数、资源监控、存储监视、系统监视等等。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723160844JWL8T486JKEGAICFLV)

### 3.1 线程监视

通过线程监控可以看到数据库系统线程的执行情况，展示的是系统正在运行的所有线程，包括：线程 ID、线程名、线程开始时间、运行状态、说明等等。还包括：正在等待的线程信息、等待事件的历史信息、命令的历史信息、检查点的历史信息、线程历史信息等。

达梦数据库是一个单进程多线程系统和多进程系统相比，线程的切换代价更小，线程间的数据共享也更容易， 线程监视视图显示系统正在运行的线程。

线程监视界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407231611456458H1XN6CXTN9Z98D)

线程监视以列表的形式显示线程的详细信息，每一条记录对应于一个线程。大部分线程的数量是不变的。 有几个例外，os_io_thread 的数量可以在 ini 文件中配置，sess4_scan_thread 也会随着连接数的增加或者减少而动态变化，通常每 64 个连接对应一个 sess4_scan_thread。 对于工作线程( ntsk_worker_thread ), 系统缺省的工作线程数量为 4 个。系统会按照用户连接数和工作负载的变化，在预先设定的范围内，动态地创建或者销毁工作线程。

### 3.2 会话监视

会话监视视图显示会话的详细信息，比如创建时间，客户类型，用户，模式，IP 地址，执行的 SQL 语句等。 通过对会话的监视可以分析是否存在频繁的连接占用系统资源等。

会话界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723161329XGLBQ9DZYD4BBN66JO)

会话监视以列表的形式显示所有连接的会话的详细信息，左边表格一行记录对应一个会话，点击选中一条记录会在右边详细信息面板中显示该会话的详细信息。

### 3.3 事务监视

事务监视视图显示所有系统中所有事务以及相关信息。

事务监视界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723161508ZROQJP2CCP4WGDWYKQ)

事务监视以列表的形式显示事务的详细信息，每一条记录对应于一个事务。

### 3.4 SQL 监视

SQL 监视视图展示 SQL 执行的历史记录信息，方便用户经常使用的记录进行保存。DBA 和程序员都很关心 SQL 语句的执行效率，希望能找到系统执行慢的 SQL 语句，并进行必要的优化。

SQL 监视界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723161637G7KPNEFTGPDSTUKN1I)

SQL 监视以列表的形式显示 SQL 语句的显示信息，上面表格一行记录对应一次执行的 SQL 信息，点击选中一条记录会在下面 SQL 语句面板中显示栈帧中的 SQL 语句以及其执行计划。

### 3.5 SQL 日志文件分析

分析 DM 服务器跟踪 SQL 日志文件。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723161749NOTOUU0ZCBD0UJ6QTH)

在该页面，用户输入 SQL 日志文件的路径，指定需要分析多少毫秒以上的 SQL 语句，点击分析按钮将在表格中显示分析的结果，选择分析结果中的某一条记录，则可查看该条历史记录执行的 SQL 语句以及执行计划等详细信息。

### 3.6 dm.ini 参数

DM.INI 化参数，为 DM 数据库启动所必须的配置信息，通过这些参数的设置，可以设置 DM 数据库服务器的各种功能和性能选项。

DM.INI 参数界面如下图所示:

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723161929CMCKH3VZTFOT5ZDUE2)

DM.INI 参数分为静态、动态，系统级、会话级、手动几种。其中静态指修改后重启服务器才能生效；动态指修改后即时生效；手动表示服务器运行过程中不可修改。动态参数又分为会话级和系统级两个级别，会话级参数在服务器运行过程中被修改时，之前创建的会话不受影响，只有新创建的会话使用新的参数值；系统级参数的修改则会影响所有的会话。

### 3.7 资源监控

资源监控主要监控数据库系统运行情况，主要包括：内存池、缓冲池、日志包、SQL 缓存、字典缓存等等。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723162127GS4IFAYPZ9Q7N9Y3YV)

#### 3.7.1 内存池

在内存池监视页面，显示内存池的具体信息，包括内存池的申请与分配情况.

内存池界面如下图所示:

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407231622367Z5A968HYYACHU410O)

#### 3.7.2 缓冲池

缓冲池的使用，可以减少 IO，提高系统速度。系统通过缓冲池，来读写磁盘上数据，严格禁止饶过缓存池，系统直接访问数据文件。 系统启动时，按照初始化文件中设定的参数，向 OS 申请片连续的内存，分别存放数据页和相对应的控制机构。缓冲池视图显示缓冲池的使用情况。

缓冲池界面如下图所示:

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723162348NR58Q9TJLM7L4PXKCV)

系统缓冲池分为 3 类 DEFAULT，KEEP，RECYCLE。系统默认的存放在 DEFAULT 缓冲池中，对于 KEEP 缓冲池，即可以选择长久保存的，对于 RECYCLE 缓冲池，即其空间根据需要回收。

#### 3.7.3 日志包

日志包信息视图显示当前实例日志系统中日志包使用的统计信息以及日志系统中当前日志包的使用情况。

日志包信息界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723162913HZ7OGJ68FYV4EAZVF7)

日志包信息视图显示当前日志系统中日志包的使用情况，包括包的长度、最大 LSN、最小 LSN 等。并且还可以显示当前实例日志系统中等待刷盘的链表上的日志包信息。

#### 3.7.4 SQL 缓存

一条 SQL 语句从用户提交执行到返回结果，可以分为以下几个阶段：

  1. 对 SQL 语句进行词法、语法以及语义分析。
  2. 对 SQl 语句进行优化处理，并生成执行计划。
  3. 执行 SQL 语句的执行计划。
  4. 返回结果集。



在这个过程中，执行计划的好坏在一定程度上影响了 I/O 的次数，因此，对 SQL 语句进行优化并产生高效的执行计划可以提高效率。 当前主要有两种方法来提高 SQL 语句的执行效率：

  1. 对 SQL 语句进行不断的优化。
  2. 对 SQL 语句进行重用。



DM 数据库管理系统除了对 SQL 语句进行不断的优化之外，还对 SQL 的计划和结果集进行重用，使得在要执行的 SQL 语句已经执行过，或者有相似的 SQl 语句已经执行过的情况下，重用执行计划甚至是重用结果集，从而提高效率。为了对执行计划和结果集进行重用，达梦设计了三种与 SQL 有关的池：SQL 语句池，计划池（ PLAN ），结果集池。这三种池分别保存 SQL 语句信息，SQL 语句对应的计划信息，以及执行之后得到的结果集信息。 SQL 存放在一个缓冲区中。当进行一个 SQL 语句查询时：

  1. 在 PLN 池中查找是否有可以重用的计划。
  2. 首先在 SQL 池中查找是否有相同的 SQL。



如果有，则重用计划；如果没有，则将 SQL 添加到 SQL 池中。

  3. 否则，对 SQL 语句进行分析生成查询计划，并插入到计划池中。
  4. 查询结果集池，如果结果集在池中，并且结果集有效，则直接返回结果，否则，重新生成结果集，并将结果集放入结果集池中。



在 dm 中存在一个 SQL_POOL 池，这个池中存放 SQL 语句、计划、结果集等的信息。每一个 SQL 语句池、计划池、结果池中的项都将各自的信息添加到 SQL_POOL 池的链表中。在缓存监视视图中可以查看到该 SQL_POOL 池的信息。

缓存监视界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723163224N5I5LJ7PFSRXUYV8ZI)

缓存监视以列表的形式显示缓存的详细信息，上面列表显示缓存池的统计信息，下面列表显示缓存的内容，每一条记录对应于一条缓存信息。

#### 3.7.5 字典缓存

字典缓存视图显示数据库字典缓存信息和字典缓存对象信息。

字典缓存界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723163433C0P2RIRZJWPC5XD4HN)

### 3.8 存储监视

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724092137NMUKDUQXYCBZ9HKVSU)

#### 3.8.1 表空间

表空间是数据库的逻辑划分，一个表空间只能属于一个数据库。所有的数据库对象都存放在指定的表空间中。在达梦中默认的存在 SYSTEM，ROLL，MAIN 和 TEMP 表空间。 表空间视图显示系统中所有表空间信息以及每个表空间对应的数据文件信息。

表空间界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407240929556W8YKIGSZPZJWFE0FD)

表空间视图分页签显示表空间和大表空间，在表空间页，上面列表中显示系统中所有表空间的信息，点击某一个表空间则在下面的表空间上的数据文件面板中显示组成该表空间的数据文件信息。 在大表空间页，显示系统中所有的大表空间的信息。

#### 3.8.2 日志文件

系统通过记录日志文件来记录数据的修改操作，日志文件视图可以查看系统的日志信息。

日志文件界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724093150LL0COD7YT81NXQYRYB)

日志文件视图在上面的列表中显示日志的总体信息，包括系统当前日志事务号 LSN 的情况、归档日志情况、检查点的执行情况等。在下面的日志文件面板中显示日志文件的具体信息。

#### 3.8.3 数据库

数据库视图显示数据库信息和实例版本信息。

数据库界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724093333PWG8EHRSZFSP6RBBO0)

数据库视图在上面面板中显示数据库的具体信息，在下面显示实例信息。

### 3.9 系统监视

#### 3.9.1 运行时错误

在数据库的操作中，通常会出现一些异常情况。这些异常可以分为两种，一种是系统异常，用户没有捕获，由 vm_raise_runtime_error 产生。 另一种是用户异常，用户捕获错误，并抛出自定义异常，由 nthrow_exec 产生。运行时错误视图显示系统所遇到的异常情况。

运行时错误界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407240938563TEFL0CKKOAR1OY7K8)

运行时错误视图显示系统出现的错误的详细信息。

#### 3.9.2 死锁

在使用数据库的过程中都有可能遇到死锁的情况，多数情况下，可以认为如果一个资源被锁定， 它总会在以后某个时间被释放。而死锁发生在多个进程访问同一数据库时，其中每个进程拥有的锁都是其他进程所需的， 由此造成每个进程都无法继续下去。死锁视图显示死锁发生的时间，以及产生死锁的 SQL 语句，产生死锁的会话和事务等。

 死锁界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724095054TWKSLUAX81318KGPWS)

死锁视图显示系统出现死锁的详细情况。从而有利于帮助解除系统死锁状态。

#### 3.9.3 大量数据返回结果集

查询大量数据的操作一般是比较耗时的，记录这些查询大量数据结果集的操作有利于解决系统性能问题。 大量数据返回结果集视图显示大量数据返回结果集的历史信息。

大量数据返回结果集界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407240952154L8X00M0MWBKZB576W)

#### 3.9.4 数据库链接

数据库链接视图显示系统配置的数据库链接信息。

数据库链接界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724095311AUZ2RKSF0RT0JP8DDC)

数据库链接视图显示系统配置的数据库链接信息。包括链接的名称、登录名、主机名、链接状态、链接类型等。

#### 3.9.5 数据库事件

数据库事件视图显示数据库重要事件和行为信息。

数据库事件界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024072409542617FH1U5FV1X74YIYSS)

数据库事件视图显示数据库重要事件和行为信息。包括数据库事件或行为发生的时间、执行的语句以及执行该事件或行为的用户。

#### 3.9.6 备库恢复进度

备库恢复进度。

备库恢复进度界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724095541CSZJ53IU9IIC0IYAB8)

#### 3.9.7 备库的 KEEP_BUF 信息

备库的 KEEP_BUF 信息视图显示数据库重要事件和行为信息。

备库的 KEEP_BUF 信息界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724095634X98DXKOXN08UGA3TK5)

#### 3.9.8 备库重做日志时的系统信息

备库重做日志时的系统信息视图显示数据库重要事件和行为信息。

备库重做日志时的系统信息界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724095733OOJ0I0YAV45FA9XF05)

备库重做日志时的系统信息视图显示备库重做日志时的一些系统信息。

#### 3.9.9 备库重做任务的日志信息

备库重做任务的日志信息视图显示数据库重要事件和行为信息。

备库重做任务的日志信息界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724095827DT624KDZU3T6YLX4VQ)

备库重做任务的日志信息视图显示备库所有重做任务的日志信息。

#### 3.9.10 ASM 文件系统

ASM 文件系统视图显示 ASM 文件系统信息。

ASM 文件系统界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724095945E0TPMV773D4B6EY7DC)

#### 3.9.11 DCR 配置

DCR 配置视图显示 DCR 配置的相关信息。

DCR 配置界面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724100043J3HG7AX2UQ53S16N7Q)

DCR 配置视图显示 DCR 配置的相关信息。包括 DCR 配置的全局配置信息以及配置的组和节点信息。

## 四、调优向导

调优向导根据系统环境和运行状态，调整数据库服务器各参数，使系统性能达到比较优化的状态，包括内存配置，线程配置，数据库物理\逻辑存储维护，性能瓶颈分析，索引优化以及索引重组等。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241003265O9W0KY5YF54K109YX)

### 4.1 内存配置向导

内存配置向导对话框用来分析配置内存缓存区大小。

  1. 内存配置向导对话框-系统信息



该页面用于显示操作系统的信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724100443OWDSOTM6UDP9KQANYB)

 在该页面显示操作系统的 CPU、内存、磁盘等信息，给调整缓存大小作为参考。

  2. 内存配置向导对话框-缓存信息



该页面用于显示缓存信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724100517QKSOAWVYGA98RX4CSN)

在该页面中显示各缓存区大小和命中率。如果命中率低于 90%，则可以考虑是否调整缓存区大小。如果需要调整，点击调整列的按钮即可打开调整参数对话框，对相应的参数进行调整。

### 4.2 线程配置向导

线程配置向导对话框用于分析配置线程参数。

  1. 线程配置向导对话框-系统信息



该页面用于显示操作系统的信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724100643OAP2YR0QA4IK3CHXCV)

在该页面显示操作系统的 CPU、内存、磁盘等信息，给调整线程参数作为参考。

  2. 线程配置向导对话框-线程



该页面用于显示线程参数的信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724100753IV0R7V1RKHW2DZTFFF)

### 4.3 数据库物理存储维护向导

数据库物理存储维护向导对话框用于分析数据库的物理存储单元使用情况，给出调整建议。

  1. 数据库物理存储维护向导对话框-数据库信息



数据库信息页显示数据库的详细信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024072410091690HXXUBL4G8QQCW8FN)

在该页面中显示数据库各参数信息，以备系统管理员了解当前数据库信息，作为物理存储维护的参考信息。

  2. 数据库物理存储维护向导对话框-数据库物理存储分析



数据库物理存储分析页面通过对数据库数据文件进行分析，显示数据库是否存在利用率超过 70% 的数据文件，如果存在则列出利用率超过 70% 的数据文件。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241010102V3Q75HZR7CUECGUQS)

在该页面中通过分析显示是否存在利用率超过 70% 的数据文件，显示分析结果以及数据库其他相关信息，以备系统管理员对数据库物理存储进行分析。

  3. 数据库物理存储维护向导对话框-数据库物理存储维护



数据库物理存储维护页面用于系统管理员管理和维护数据库数据文件信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724101144ZZ3B6P8QSHU030OYM2)

在该页面中，显示每个数据文件的使用情况，如果文件利用率超过 70%， 系统管理员可以双击修改文件大小列中的值来增大数据文件大小，或者手动增加数据文件。

### 4.4 数据库逻辑存储维护向导

数据库逻辑存储维护向导对话框用于分析数据库的逻辑存储单元使用情况，给出调整建议。

  1. 数据库逻辑存储维护向导对话框-数据库信息



数据库信息页显示数据库的详细信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024072410165016KMXOSFHIQTKBAEPD)

在该页面中显示数据库各参数信息，以备系统管理员了解当前数据库信息，作为逻辑存储维护的参考信息。

  2. 数据库逻辑存储维护向导对话框-数据库逻辑存储分析



数据库逻辑存储分析页面显示数据库的逻辑存储单元的参数信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724101722K1FGGRBYRZTZRUVZ7M)

在该页面中显示通过显示数据库逻辑结构，簇大小和页大小，以备系统管理员对数据库逻辑存储进行分析。

  3. 数据库逻辑存储维护向导对话框-数据库逻辑存储维护



数据库逻辑存储维护页面给出调整建议并说明调整各参数会带来的影响。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724101823MA1V99HHNAZ845PR3C)

### 4.5 性能瓶颈分析与调优向导

性能瓶颈分析与调优向导对话框用于分析系统内存、IO、网络、存储等性能参数，给出分析报告。

  1. 性能瓶颈分析与调优向导对话框-内存分析



该页面用于分析和判断是否存在内存瓶颈。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724101918EG38I34ZC8NMALTEAD)

在该页面中给出系统内存和数据库使用内存信息，以作为是否存在内存瓶颈的参考。

  2. 性能瓶颈分析与调优向导对话框-IO 分析



该页面用于分析和判断是否存在 IO 瓶颈。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724102006UWWK9EGNYO4UZRXW4Y)

在该页面中给出物理读、写，逻辑读的次数以及写日志的大小（单位：字节），以作为判断是否存在 IO 瓶颈的参考。

  3. 性能瓶颈分析与调优向导对话框-网络分析



该页面用于分析和判断是否存在网络瓶颈。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724102037BS24EOQRZ7NVLR0PIU)

在该页面中给出接收和发送的字节数，以作为判断是否存在网络瓶颈的参考。

  4. 性能瓶颈分析与调优向导对话框-存储分析



该页面用于分析和判断是否存在存储瓶颈。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241021141QCHUIVZYKQQ7PHU5P)

在该页面中给出数据文件和日志文件的使用情况，以作为判断是否存在存储瓶颈的参考。

  5. 性能瓶颈分析与调优向导对话框-分析报告



该页面用于给出对前面内存，IO，网络，存储等参数的分析结果。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241021549JY7CGK08WIG67H50T)

### 4.6 索引优化向导

索引优化向导对话框用于对某一条查询语句或者某一个查询脚本文件进行分析，如果查询脚本中存在比较耗时的操作，则给出对相应表创建索引的建议。

  1. 索引优化向导对话框-工作负荷



该页面选择输入要进行索引优化的工作负荷。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724102404BEURS2I2O4ELRDSZ1R)

  2. 索引优化向导对话框-优化建议



该页面用于给出优化建议。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241026349IHBL12LE084VURALH)

通过对索引的分析，列出需要创建索引的表，以及需要创建索引的列，并列出该表上已经存在的索引。

### 4.7 索引重组向导

索引重组对话框用于对索引的物理页面进行分析，如果其页面不是连续的，存在很多折回页面，则可以进行对该索引进行重组操作，以提高系统性能。

  1. 索引重组对话框-选择索引



该页面用于选择要进行重组分析的索引。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724102729R8CSHY8H5CPD3YFY77)

模式名：索引所属模式的名称。

表名：索引所属表的名称。

  2. 索引重组对话框-索引段信息



该页面用于分析和重组所选择的索引。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241028193GMM1077B23CMAXYS4)

分析：

点击该按钮将对选择的索引段信息进行分析，以图形的形式显示索引的物理页面和逻辑页面的关系， 如果图形中存在很多的折线，则需要对该索引进行重组操作。

重组：

对所选择的索引进行重组操作。

## 五、预警配置

预警配置页面可以配置监控的关键指标和警告发送方式，以监测所配置的关键指标是否达到预警值。如果达到预警值则通过所配置的警告发送方式把警告信息发送给数据库管理员。

预警配置页面如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724102957AWIXWZBGO2ONXDFHMH)

预警配置页面上面为关键指标配置和警告配置，下面为消息区，显示开启性能采集后，警告消息的发送情况。

  1. 关键指标配置



该页面用于添加关键性能指标，并对这些指标进行配置管理。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240724103031NABUTPO2K5BPHB6RPF)

采样间隔(秒)：设置每隔多少时间对关键性能指标进行一次采集，判断是否超出上、下限制。

停止时间：性能采集的停止时间。

关键性能指标：列出关键性能指标的名称、类别、上限、下限等。

添加：向列表中添加一项性能指标。

删除：从列表中删除一项性能指标。

保存：保存所配置性能指标，只有保存后才可以生效。

  2. 警告配置



该页面用于配置发送警告信息的方式。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241034556STHD3AO4R2IC90OC6)

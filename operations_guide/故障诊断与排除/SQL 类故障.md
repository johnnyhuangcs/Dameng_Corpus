

# SQL 类故障

## 一、前言

### 1.1 简介

SQL 类故障，即数据库实例通过接口发送 SQL 语句与客户端之间进行交互时，在执行语句的时候出现的异常情况，本文将从以下三个方面来介绍出现 SQL 类故障后该如何排查与处理：

  * SQL 长时间等待问题。
  * SQL 执行过程中数据库进程异常。
  * SQL 结果集异常问题。



### 1.2 术语

  * core 文件：程序异常时操作系统保留的完整进程的内存镜像文件。
  * Halt 错误：即停机，程序结束。
  * segfault 错误：即段错误，指访问的内存超出了系统所给这个程序的内存空间。
  * Pagefault 错误：即页错误，指当 cpu 执行进程的某个页面时，发现要访问的页(虚拟地址的页)没有在物理内存中，而导致的中断。
  * OOM：即内存溢出（Out of Memory），指需要的内存空间大于系统分配的内存空间，oom 的后果就是程序 crash。



### 1.3 适用范围

本文中所涉及内容适用于 DM7 及 DM8 版本数据库产品。

## 二、SQL 长时间等待问题

数据库若出现 SQL 长时间等待的情况，可以从以下几个方向进行排查：

  * 执行过程中某些对象或资源发生等待，导致执行语句长时间等待不能返回。
  * 所执行语句本身存在性能问题。
  * 其他异常情况。



### 2.1 对象或资源发生等待问题

如果存在长时间没有返回结果的语句，首先通过 V$SESSIONS 确认语句处于活动状态，语句如下：

```
SELECT * FROM v$sessions WHERE state='ACTIVE'
AND dbms_lob.substr(sf_get_session_sql(sess_id)) LIKE '%语句片段%'
```

明确事务等待导致语句没有正常执行结束，语句如下：

```
SELECT * FROM v$trxwait WHERE id = 上述语句得到结果的 TRX_ID
```

  1. V$TRXWAIT 查询得到结果的 WAIT_FOR_ID 字段标记的事务即为当前语句正在等待的事务。通过 \`SELECT * FROM v$sessions WHERE trx_id = 查询到的 WAIT_FOR_ID;\` 可以得到当前事务所在等待会话的一些信息，一般来说，因为某些会话或者客户端忘记进行提交或者回滚操作，后续一直空闲，导致其他的事务由于跟该事务存在一些事务上的依赖关系发生等待。针对这种情况，需要在确认安全的情况下针对这个阻塞源头的会话进行操作，可以通过关闭客户端、关闭会话、发送提交或者回滚命令等方式结束等待的会话。
  2. 当 V$TRXWAIT 查询不到结果时，可以通过查询 \`SELECT * FROM v$lock WHERE blocked = 1\`; 来进行确认，在查询结果中可以对相关锁对象的持有事务来查询来确认阻塞来源。



> **注意**
>
> 一般情况下 DDL 导致的等待会有一个显示的等待时间，由 dm.ini 配置文件中的参数 DDL_WAIT_TIME 来进行控制，默认为
> 10，也就是说如果等待 10 秒钟后，阻塞源头的 DDL
> 还没有执行完释放资源，会抛出锁超时错误。可以适当调大等待时间再次运行查看是否还会抛出锁超时错误。



### 2.2 性能问题

首先确认 sql 是否存在事务等待，如果不存在事务等待需要确认 sql 本身是否存在性能问题。

确认语句处于活动状态，语句如下：

```
SELECT * FROM v$sessions WHERE state='ACTIVE'
AND dbms_lob.substr(sf_get_session_sql(sess_id)) LIKE '%语句片段%'
```

查询等待事务：

```
SELECT * FROM v$trxwait WHERE id = 上述语句得到结果的 TRX_ID
```

若查询不到结果，说明该语句没有发生事务性等待。如果发现语句活动，且没有事务性等待，则大概率是该语句自身执行存在效率问题，需要对执行计划进行调整。具体方式可以参考 [性能诊断与优化章节 sql 优化](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html#%E5%9B%9B%E3%80%81SQL%20%E4%BC%98%E5%8C%96)。

### 2.3 其他异常情况问题

若对象或资源未发生等待，且 SQL 语句的执行性能正常，则可参考以下排查思路：

  1. **临时表空间大小限制** 。在 dm.ini 配置文件中对 TEMP 表空间大小进行了限制，查询中存在 /SORT/HASH JOIN/HAGR 等操作使用临时表空间，在临时表空间没有被其他会话释放时发生等待。
  2. **刷新 REDO 日志语句出现等待** 。主备、读写分离等环境运行过程中，由于备机自身或者配置相关的原因（备机 IO 出现异常、主备网络异常、数据延迟达到配置的主备最大延迟）等，导致主机上运行一些需要刷 REDO 日志的语句时发生等待。
  3. **相关表出现大批量修改操作** 。 大表发生大批量删除数据后，由于这些数据都只是被标记删除，在一定事件后会由回收站进行统一的清理操作，清理过程中需要对数据页进行修改，而涉及的数据页又非常多，导致的执行速度缓慢。



## 三、SQL 执行过程中数据库进程异常问题

SQL 执行过程中数据库进程异常一般分为以下几类：

  * 运行过程中数据库发生 Halt
  * 运行过程中数据库段错误 Segfault
  * 运行过程中发生 PAGE FAULT
  * 运行过程中发生线程污染 TAINED
  * 运行过程中发生 OOM 错误



对于 Segfault，PAGE FAULT，TAINED 与 OOM 错误，在数据库运行日志中一般不会发现明显的信息，这些错误可以通过查询操作系统日志，一般是 `/var/log/messages*`，然后搜索异常前的 dmserver 的进程号或者搜索 DM 相关的信息，根据查询到的不同的信息类型进行不同的处理。

### 3.1 Halt 错误

对于 Halt 类型错误，可以通过查看数据库的运行日志，搜索 ”Halt“ 关键字，如果发现存在 Halt 字样的日志内容，会在 Halt 之前有具体的 Halt 原因说明，这是数据自己保证数据安全的一种方式，当检测到一些严重异常时，采取自杀（自行停止）的方式来保全数据，防止产生更严重的问题。

### 3.2 Segfault 错误

对于 Segfault 类型错误，可以通过查看操作系统日志，需要说明的是，Segfault 类型错误一般是由 数据库 Halt 时通过主动进行除 0 操作引发的异常时，如果不是 Halt，则可能是由于某些语句引起。可以参考以下步骤进行处理：

  * 通过 dmrdc 工具对异常生成的 core 文件进行信息读取，读取后会得到一个数据文件，输出文件的格式为[线程号] SQL 语句内容。
  * 通过 gdb 工具打开异常时生成的 core 文件进行检查。
  * 通过 \`info thread\` 命令，找到出现异常的线程，线程对应的 LWP 后跟的数字即为异常线程号。
  * 通过 GDB 找到的异常线程号去 dmrdc 的输出文件中进行搜索，得到的语句就是导致异常的语句，如果语句可以稳定重现，可以将 gdb 该 core 文件的 bt 输出内容、相关语句发送到相关技术服务人员进行确认。



### 3.3 Page fault 错误

对于 Page fault 类型错误，可以通过查看操作系统日志，一般 PAGE FUALT 后会跟有具体的错误码，即 `code: xx`，在 LINUX 内核中定义了这些报错对应的内容。

如果 code 是 0，一般是由于数据库运行中没有办法申请到需要使用的内存导致，这种时候需要考虑在 dm.ini 中修改相关配置，调整数据库的内存使用量。其他几种 code 或者是 code 的组合（比如 `6 = 4 + 2` 表示存在两种错误），基本是发生内存读写越界引起。

### 3.4 Tained 错误

对于 Tained 类型错误，可以通过查看操作系统日志，如果操作系统日志中出现了 DM 相关线程 tained 相关的信息，一般是由于第三方软件（主要是杀毒软件或者安全软件），对 DM 的相关线程进行污染，导致数据库的一些线程被异常中止，不同类型的线程被中止可能导致不同的结果，而且是不可预期的，这种情况下需要及时调整系统环境，让相关软件尽量不影响数据库进程。

### 3.5 OOM 错误

这种也是比较常见的错误，OOM 作为操作系统本身对于自己的一种保护机制，对占用大量内存的进程，如果满足一定条件，就会被操作系统中止，腾出空余内存，如果频繁发生数据库进程被 OOM kill，则需要调整数据库内存相关参数和操作系统内存相关参数等，防止数据库进程被频繁 OOM。

## 四、SQL 结果集异常问题

首先需要找到结果集异常的 SQL 语句，可以通过 LOGCOMMIT 日志、V$SQL_HISTORY 视图等来查找。

一般情况下，结果集异常是由于优化器对查询语句的错误改写或者优化导致，在提取 SQL 时尽量保证提取到的语句和实际执行的语句严格一致，要求绑定参数相同、数据库参数一致，这样可以在进行验证时的语句执行计划和出错时一致。

在计划一致的情况下，如果可以重现查询结果集异常的情况，可以尝试对异常的查询语句进行裁剪，也可以参照当前计划，有目的修改通过 SQL 语句摘除计划中的某些操作符，一步步得到结果集出错的最精简语句。

还可以通过修改参数 /HINT/ ，改写语句等方式，调整问题操作符为等价的其他操作符，查看是否可以规避问题，如果不能通过等价修改的方式绕过该问题，可以及时整理重现用例将问题反馈给有关部门。

另外一些特殊情况的结果集异常可能是因为某些信息没有及时更新或者错误使用导致，包括但不限于：

  * 查询涉及到全文索引没有更新。
  * 查询涉及到的物化视图没有更新。
  * 错误的使用确定性函数标记了不确定函数。
  * 非一致读备机查询数据存在延迟。



## 五、案例

### 5.1 长时间等待示例

#### 5.1.1 执行 SQL 长时间无返回数据

【问题描述】

某系统数据库中对一张表执行相关 update 的 SQL 语句，运行等待 8 分钟，SQL 未执行结束。SQL 语句示例如下：

```
update uf_srcbtz_dt1 set 
htydqsr = a.htydqsr, 
htydqsdz = a.htydshdz, 
htydqsdw = a.qsdw, 
kdsjr = a.sjr, 
kdsjdz = a.sjshdz, 
kdsjdw = a.qsdw, 
sjqsr = a.sjqsr, 
sjqsdz = a.sjdz, 
sjqsdw = a.qsdw
from uf_srcbtz_dt1 b, uf_fhqsqr a, uf_srcbtz c
 where b.sqdbh = a.cpsqdbh and a.cpckdbh = b.CPJCDH and  a.HTBH = c.HTMC and c.ID = b.MAINID and c.htmc is not null
  and b.sjqsdz is null;
```

【故障排查及原因分析】

在 SQL 语句语法无异常情况下，推测相关表上了表锁，造成了相关表执行 update 的 SQL 等待，所以执行不过去。可通过如下思路进行排查。

首先查询数据库中的表锁情况。

```
SELECT t2.name,t1.TABLE_ID,t1.LTYPE,t1.BLOCKED,t1.LMODE,t1.TRX_ID,t1.addr,t1.ROW_IDX,t2.SCHID FROM V$LOCK t1,sysobjects t2 where t1.table_id=t2.id;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221122154732KXF82TIARBHZCPOYGA)

查询结果如上图，初步查看，可以看到和 update 里执行相关的两张表存在表锁，继续对这两张相关表监控查询，发现这两张表锁一直长时间驻留。

```
SELECT t2.name,t1.TABLE_ID,t1.LTYPE,t1.BLOCKED,t1.LMODE,t1.TRX_ID,t1.addr,t1.ROW_IDX,t2.SCHID FROM V$LOCK t1,sysobjects t2 where t1.table_id=t2.id and t2.name in('UF_SRCBTZ_DT1','UF_SRCBTZ');
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221122155031C1L9K3JX5CFL7M6Z79)

查询发现表 UF_SRCBTZ_DT1，UF_SRCBTZ 相关 IX 锁一直存在。再根据相关 TRX_ID 查询对应的会话 session 里，执行 SQL 情况。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202211221549284DHG9WJ4DO4PONWFTG)

由以上分析可知，存在一个连接会话里面执行相关表的 SQL 语句，且未提交或者回滚。经确认该会话是之前操作且无关业务的 SQL，使用 `SP_CLOSE_SESSION` 将该会话关闭。再执行之前的 update 相关 SQL，即可正常执行完毕。

```
call sp_close_session('140240215016760'); 
```

#### 5.1.2 执行创建分区 SQL 报错锁超时

【问题描述】

主备集群环境下，执行相关分区创建 SQL 失败，应用日志中报错：锁超时。

【故障排查及原因分析】

在创建表的分区时，同时存在对该表查询的 SQL 语句，导致分区创建失败。

【解决方案及建议】

  1. 通过 DM 管理工具（或命令行工具 disql ）使用管理员用户 SYSDBA 连接数据库集群主节点，执行如下语句查询会话对应的 IP 和客户端：



```
SELECT * FROM V$SESSIONS WHERE STATE='ACTIVE';
```

  2. 手动停止 DM 管理工具（或命令行工具 disql ）上的查询语句和会话窗口。
  3. 使用 \`SP_CLOSE_SESSION\` 将会话关闭。



（1）通过 DM 管理工具（或命令行工具 disql ）使用管理员用户 SYSDBA 连接数据库集群主节点，执行如下语句关闭相应查询：

```
SP_CLOSE_SESSION(xxx);      ---xxx 表示查询出的 session_id
```

（2）执行完成后，通过如下语句查询确认是否已经不存在对应查询语句：

```
SELECT * FROM V$SESSIONS WHERE STATE='ACTIVE';
```

若会话清理成功则返回一行结果，如下所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221122155346K6ZD3993M1EROSDFI8)

`SP_CLOSE_SESSION` 是轻量级命令，仅对数据库发送关闭会话的指令，不会强制对数据库线程进行操作。

  4. 重新执行相关分区创建的 SQL。



#### 5.1.3 整体业务 SQL 执行速度慢且主机 CPU 使用率高

【问题描述】

系统整体执行业务 SQL 较为缓慢，执行 `top -p 数据库服务进程 ` 查看数据库服务进程的 CPU 使用率，发现数据库主机 CPU 使用率高。

【解决方案】

  1. 确认数据库状态。



```
##确认数据库进程正常。
ps –ef|grep dmserver
```

通过以下语句查询数据库状态：

```
select instance_name,status$ from v$instance;
```

  2. 检查数据库当前会话，是否有运行中的异常 SQL。



如果有大量执行效率低的 SQL，确认是否可以限流、强制终止。如果可以强制终止，执行下一步。

（1）查询所有会话。

```
select
        sysdate                    ,
        SF_GET_SESSION_SQL(SESS_ID), --获取完整 SQL
        sess_id                    ,
        sess_seq                   ,
        sql_text                   ,
        state                      ,
        seq_no                     ,
        user_name                  ,
        trx_id                     ,
        create_time                ,
        clnt_ip
from
        v$sessions
where
        state='ACTIVE';
```

（2）查询执行效率低的 SQL。

```
SELECT
        *
FROM
        (
                SELECT
                        sess_id                                         ,
                        sql_text                                        ,
                        datediff (ss, last_recv_time, SYSDATE) Y_EXETIME,
                        SF_GET_SESSION_SQL (SESS_ID) fullsql            ,
                        clnt_ip
                FROM
                        V$SESSIONS
                WHERE
                        STATE = 'ACTIVE'
        )
WHERE
        Y_EXETIME >= 2; --执行时间超 2s，可以自定义该时间
```

  3. 终止异常会话。在数据库里面执行 SQL 语句 \`sp_close_session(sess_id);\`
  4. 分析 SQL 执行计划，看是否有优化空间，进行 SQL 优化。
  5. 观察数据库活动会话是否正常。



#### 5.1.4 部分业务 SQL 执行缓慢且 CPU 占用正常持续变化

【问题描述】

数据库部分业务 SQL 执行缓慢，但执行 `top -p 数据库服务进程`，发现 CPU 占用正常并持续变化。

【解决方案】

  1. 检查存储读写性能，检查网络传输速度。可以参考以下命令：



```
dd if=/dev/zero of=/home/test bs=8k count=1k oflag=dsync   (本地服务器速度一般为 25M/秒以上,磁盘阵列 10M/S 以上)
```

  2. 检查数据库当前会话，是否有运行中的异常 SQL。查询所有会话：



```
select
        sysdate                    ,
        SF_GET_SESSION_SQL(SESS_ID), --获取完整 sql
        sess_id                    ,
        sess_seq                   ,
        sql_text                   ,
        state                      ,
        seq_no                     ,
        user_name                  ,
        trx_id                     ,
        create_time                ,
        clnt_ip
from
        v$sessions
where
        state='ACTIVE';
```

  3. 检查数据库执行 SQL 缓存信息，是否有异常 SQL。
  4. 查看数据库 topsql，是否有异常 SQL。



```
Select top 60 datediff(ss,last_send_time,sysdate) TIME,sess_id,run_status,sql_text,AUTO_CMT,user_name,appname,CLNT_IP,trx_id  from  v$sessions  order by TIME desc;
```

  5. 检查数据库 SQL 日志，通过关键字在 SQL 日志中查找是否有异常 SQL。
  6. 收集最近一天的 SQL 日志，分析 SQL 执行计划，看是否有优化空间，进行 SQL 优化。
  7. 观察数据库活动会话是否正常。



#### 5.1.5 统计信息不准确或缺失导致的慢 SQL

【问题描述】

分区表或是大表，某一些简单的 SQL 原本运行较快，但随着表数据量的不断增长，会在某个时刻变得非常缓慢甚至运行不出结果。

【问题分析】

此问题是因为随着表数据量的不断增长，表的统计信息不准确或表没有统计信息，导致优化器生成的执行计划改变，从而引发 SQL 运行很慢，且占用系统资源。

【解决方案】

正确的收集表的[统计信息](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)，及时的更新表的[统计信息](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)，让优化器选择最佳执行计划，以最少的代价（成本）查询出表中的数据。[统计信息](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)的收集和更新参考：[统计信息](https://eco.dameng.com/document/dm/zh-cn/ops/performance-optimization.html#%E4%BA%94%E3%80%81%E7%BB%9F%E8%AE%A1%E4%BF%A1%E6%81%AF)。

收集完统计信息后，有时也需要用 SP_CLEAR_PLAN_CACHE(cache_item) 清理对应的 SQL 执行计划缓存，才能生效。

#### 5.1.6 高并发场景下 SQL 执行缓慢

【问题描述】

某一类简单的 SQL 语句在高并发场景下执行速度显著下降。

【问题分析】

此问题需要分析此条 SQL 的执行计划，一般是由于此 SQL 回表太多引起的，当高并发上来时，回表会消耗主机大量资源（sys），引起 SQL 执行缓慢，造成系统瓶颈，情况复杂时会导致系统处于瘫痪状态，占用别的 SQL 系统资源。

【解决方案】

消除 SQL 中的回表，常用的方法有：

  1. 创建适当的索引（包括组合索引），select 查询选出的结果集中不要出现无关的字段，尽量使用索引中的字段，减少执行计划中的回表操作符。
  2. 在对应的表上创建适当的聚集索引（对于新版本的达梦 8 适用），减少执行计划中的回表操作符。



### 5.2 结果集异常示例

#### 5.2.1 MySQL 迁移到 DM 的存储过程运算结果与原库不一致

【问题描述】

MySQL 中执行 `SELECT SIGN(65^4)^1` ，运算结果为 0；而 DM 数据库中执行结果为 11。

【问题分析】

此问题是由于 MySQL 和 DM 数据库运算优先级不一致导致。

DM 数据库运算优先级：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221122160413Y3H6IHWS3WMD24B5VI)

MySQL 数据库运算优先级：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221122160416C4XRAG0OFZHITOPC1I)

因此 MySQL 中该运算 `SELECT SIGN(65^4)^1` ，实际执行顺序为：`select (sign(65^4)^1)`。

DM 中该运算 `SELECT SIGN(65^4)^1` ，实际执行顺序为：`select sign(65^4)^1`。

【解决方案】

在达梦的过程中实现与 MySQL 一样的顺序，对异运算加括号干预：`select (sign(65^4)^1)`。

## 六、参考

若以上内容无法解决您的问题，可以在[达梦技术社区](https://eco.dameng.com/community/question/)提问交流。

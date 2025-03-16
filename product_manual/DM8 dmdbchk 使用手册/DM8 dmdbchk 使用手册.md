

# DM8 dmdbchk 使用手册

本文档主要介绍如何使用 DM 的用于检查数据文件完整性的命令行工具 dmdbchk，该工具所提供的功能、及其参数的使用方法。

## 1 功能简介

  dmdbchk 是 DM 提供的用于检查数据库完整性、正确性的命令行工具。在服务器正常关闭后的脱机情况下，用户可以使用 dmdbchk 对数据库进行校验，包括校验 DM 数据库内部的物理存储结构是否正常，对象信息是否合法等。除此之外，dmdbchk 工具还可以检测并删除操作系统中残留的信号量和共享内存，避免达到操作系统上限后数据库服务无法启动。在服务器启动的联机情况下，用户可以使用 dmdbchk 对数据库的单个数据文件进行校验，需设置参数 FILE_PATH 才可以在联机情况下校验。

检验的内容具体包括三方面：

  1. 数据文件合法性检验。



数据文件校验只校验数据文件大小。如果数据库文件实际大小大于或等于文件头中记录的大小，则合法。

  2. 索引正确性校验。



索引检验包括：普通表 B 树索引校验、LIST 表扁平 B 树索引校验、列存储表索引校验。校验内容具体包括：B 树的层次、层次关系，每层的内部页、叶子页以及页之间的前后链接关系，每个页的页头信息（如页类型等)等。

  3. 对象 ID 合法性校验。



对象 ID 校验包括数据库内的所有对象的 ID 检验。对象包括：索引、表、约束、存储过程、模式、同义词、用户等。如果从系统表中查出的对象 ID 小于库的 ID 预留页中记录的该类型对象的下一分配 ID，则合法。因为库的 ID 预留页中记录的是各类型对象下一个新分配对象将使用的 ID，因此若系统表中的对象 ID 大于或等于该 ID，说明库已损坏。

> **注意**
>
> dmdbchk并不能检查出用户实际数据的正确性，如果数据文件被手动人为修改，正好修改到的是用户数据部分，是检查不出来的。



  4. 残留信号量和共享内存的检测及删除



一般 Linux 下默认可创建的信号量个数为 128 个，共享内存为 4096 个。DM 数据库服务器正常退出的情况下，会将创建的信号量或共享内存删除，但如果服务器异常退出，这些信号量及共享内存会一直留在操作系统中直到通过手工方式删除。如果残留的信号量或共享内存个数达到操作系统上限，服务器再次启动时，会因为创建信号量或共享内存失败而无法启动成功，因此在本工具中提供对 Linux 残留信号量和共享内存的检测及删除命令。

校验完毕后，dmdbchk 会在当前目录下（dmdbchk 所在目录）生成一个名为 dbchk_err.txt 的检查报告，供用户查看。此外会在 dbchk_err.txt 所在目录下生成 dbchk_err_pages.txt 损坏页检测报告，仅包含页损坏统计信息，供用户对照损坏页信息进行修复。

该工具位于安装目录的 /dmdbms/bin 目录下。

## 2 使用 dmdbchk

dmdbchk 工具需要从命令行启动。找到 dmdbchk 所在安装目录 /dmdbms/bin，输入 dmdbchk 和参数后回车。参数在下一节详细介绍。

> **注意**
>
>
> **dmdbchk工具不同的参数用法也不同，如果是校验数据库的参数，则要求必须是经过初始化，且正常关闭的数据库，才能被dmdbchk工具校验，否则dmdbchk会报错退出。
> 例如，正常关闭/home/test/dmdbms目录下数据库后，使用dmdbchk对数据库进行校验。**



例如，正常关闭 /home/test/dmdbms 目录下数据库后，使用 dmdbchk 对数据库进行校验。

```
./dmdbchk PATH=/home/test/dmdbms/dm.ini
```

如果 dm.ini 在 dmdbchk 当前目录，也可以写成这样：

```
./dmdbchk PATH=dm.ini
```

## 3 查看 dmdbchk 参数

dmdbchk 使用简单灵活。用户可使用“dmdbchk HELP”快速查看 dmdbchk 版本信息以及参数用法。

```
./dmdbchk help
dmdbchk V8

version: 03134284311-20240822-240257-10000
格式: ./dmdbchk KEYWORD=value

例程: ./dmdbchk path=/opt/dmdbms/bin/dm.ini

关键字         说明
------------------------------------------------------------------------------
PATH           dm.ini绝对路径或者当前目录的dm.ini
DCR_INI        dmdcr.ini的路径
HELP           打印帮助信息
START_INDEXID  最小检查索引号
END_INDEXID    最大检查索引号
CHECK_SEMA     检查当前系统信号量使用情况（只适用于LINUX，1：只做检查 2：检查并删除残留信号量）
CHECK_SHM      检查当前系统共享内存使用情况（只适用于LINUX，1：只做检查 2：检查并删除残留共享内存）
CHECK_REC      检查数据库中B树叶子节点的记录（0：不做检查 1：检查 2：同时检查BLOB字段 默认为0，仅用于B树索引检测，在FILE_PATH参数下不起作用）
PARALLEL       用于指定检测的过程中所使用的线程数目(1-100, 默认8)
FILE_PATH      单独检测指定的dbf文件的路径地址
TABLESPACEID   单独检测指定的表空间号
FEEDBACK       是否显示检测百分比进度（0：不显示 1：显示 2：只显示简略信息 默认为0）
OUTPUT_FIL     用于指定生成的检测报告的路径（默认为当前目录下的dbchk_err.txt）
```

## 4 dmdbchk 报告解读

dmdbchk 校验的过程对用户不可见，校验的结果以报告的形式呈现给用户。

dmdbchk 校验数据库的报告内容分为七部分：一 dmdbchk 版本信息；二开始标志；三数据文件校验结果；四索引校验结果；五对象 ID 校验结果；六结束标志；七错误总数。

![dmdbchk 报告内容](https://download.dameng.com/eco/docs/asset/pm/dmdbchk-manual/dmdbchk报告内容.jpg)

图4.1 dmdbchk 报告内容

数据文件校验结果、索引校验结果、对象 ID 校验结果三部分内容伸缩性较大，内容多少由数据库大小决定。对于数据文件校验结果和索引校验结果，如果校验成功，直接打印出具体校验的对象；如果校验失败，则打印出具体错误信息。对于对象 ID 校验结果，如果校验成功，不打印出具体校验的对象；如果校验失败，则打印出具体错误信息。

dmdbchk 检测信号量或共享内存的报告则比较简单，分为三部分：一 dmdbchk 版本信息；二系统中所有信号量或共享内存的检测结果；三各项结果总数归类。

## 5 应用实例

例 1  初始化一个数据库，并创建 DM 的示例库 BOOKSHOP，正常关闭后，使用 dmdbchk 工具对该库的数据文件进行校验。

```
./dmdbchk PATH=/home/test/dmdbms/dm.ini START_INDEXID=33554433 
END_INDEXID=33555531
```

校验后的报告存在 dmdbchk 工具所在的目录里，名称为 dbchk_err.txt。报告内容如下：

```
/**一dmdbchk版本信息**/
[2015-12-31 16:57:29] dmdbchk V7.1.5.42-Build(2015.12.30-64335trunc)
/**二开始标志**/
[2015-12-31 16:57:30] DM DB CHECK START......
/**三数据文件校验结果**/
[2015-12-31 16:57:30] --------check dbf file size start---------
[2015-12-31 16:57:30] FILE=(ts_id=0, fil_id=0, path=D:\xx\DAMENG\SYSTEM.DBF)
[2015-12-31 16:57:30] FILE=(ts_id=1, fil_id=0, path=D:\xx\DAMENG\ROLL.DBF)
[2015-12-31 16:57:30] FILE=(ts_id=4, fil_id=0, path=D:\xx\DAMENG\MAIN.DBF)
[2015-12-31 16:57:30] FILE=(ts_id=5, fil_id=0, path=D:\xx\DAMENG\BOOKSHOP.DBF)
[2015-12-31 16:57:30] --------check dbf file size end-----------
/**四索引校验结果**/
[2015-12-31 16:57:30] --------check indexes start---------------
[2015-12-31 16:57:30] INDEX=(id=33554433, name=SYSINDEXCOLUMNS, table_name=SYSCOLUMNS)
[2015-12-31 16:57:30] INDEX=(id=33554434, name=SYSINDEXINDEXES, table_name=SYSINDEXES)
[2015-12-31 16:57:30] INDEX=(id=33554440, name=SYSINDEXTUSERS, table_name=SYSUSER$)
[2015-12-31 16:57:30] INDEX=(id=33554442, name=SYSINDEXSYSGRANTS, table_name=SYSGRANTS)
[2015-12-31 16:57:30] INDEX=(id=33554452, name=SYSINDEXCONSTRAINTS, table_name=SYSCONS)
[2015-12-31 16:57:30] INDEX=(id=33554458, name=SYSINDEXSYSAUDIT, table_name=SYSAUDIT)
[2015-12-31 16:57:30] INDEX=(id=33554459, name=SYSINDEXSYSAUDITSQLSEQ, table_name=SYSAUDITSQLSEQ)
[2015-12-31 16:57:30] INDEX=(id=33554464, name=SYSINDEXCONTEXTINDEXES, table_name=SYSCONTEXTINDEXES)
......
省略一部分索引校验结果......
......
[2015-12-31 16:57:30] INDEX=(id=33555528, name=INDEX33555528, table_name=EMPTAB)
[2015-12-31 16:57:30] INDEX=(id=33555529, name=INDEX33555529, table_name=EMPTAB)
[2015-12-31 16:57:30] INDEX=(id=33555530, name=INDEX33555530, table_name=SALGRADE)
[2015-12-31 16:57:30] INDEX=(id=33555531, name=INDEX33555531, table_name=COMPANYHOLIDAYS)
[2015-12-31 16:57:30] --------check indexes end-----------------
/**五对象ID校验结果**/
[2015-12-31 16:57:30] --------check iid start-------------------
[2015-12-31 16:57:30] check cons id ...
[2015-12-31 16:57:30] check index id ...
[2015-12-31 16:57:30] check table id ...
[2015-12-31 16:57:30] check proc id ...
[2015-12-31 16:57:30] check schema id ...
[2015-12-31 16:57:30] check synonym id ...
[2015-12-31 16:57:30] check user id ...
[2015-12-31 16:57:30] --------check iid end---------------------
/**六结束标志**/
[2015-12-31 16:57:30] DM DB CHECK END......
/**七错误总数**/
[2015-12-31 16:57:30] error count is 0
```

例 2 例如，检测某台 linux 机器上信号量的使用情况。

```
./dmdbchk CHECK_SEMA=1
```

检测后的报告存在 dmdbchk 工具所在的目录里，名称为 dbchk_err.txt。报告内容如下：

```
/**一dmdbchk版本信息**/
[2018-11-05 14:54:44] dmdbchk V8.0.0.105-Build(2018.10.31-98635-debug)ENT
/**二所有信号量检测结果**/
[2018-11-05 14:54:44] get semid 40992768(key:0xcc020ab4) current value:0, cur_time:1541400884, last op time:1540894468, need delete it!
[2018-11-05 14:54:44] get semid 41025537(key:0xcd020ab4) current value:0, cur_time:1541400884, last op time:1540894468, need delete it!
[2018-11-05 14:54:44] get semid 53116930(key:0xcd02111d) current value:0, cur_time:1541400884, last op time:1541318198, need delete it!
/**三总数归类**/
[2018-11-05 14:54:44] check os semaphore finished:
[2018-11-05 14:54:44] total_cnt:3, active_cnt:0, check_err_cnt:0, need_del:3, real_del:0, del_err_cnt:0!
```

例 3 例如，删除某台 linux 机器上的残留信号量。

```
./dmdbchk CHECK_SEMA=2
```

检测后的报告存在 dmdbchk 工具所在的目录里，名称为 dbchk_err.txt。报告内容如下：

```
/**一dmdbchk版本信息**/
[2018-11-05 14:54:47] dmdbchk V8.0.0.105-Build(2018.10.31-98635-debug)ENT
/**二所有信号量检测结果**/
[2018-11-05 14:54:47] get semid 40992768(key:0xcc020ab4) current value:0, cur_time:1541400887, otime:1540894468, delete it success!
[2018-11-05 14:54:47] get semid 41025537(key:0xcd020ab4) current value:0, cur_time:1541400887, otime:1540894468, delete it success!
[2018-11-05 14:54:47] get semid 53116930(key:0xcd02111d) current value:0, cur_time:1541400887, otime:1541318198, delete it success!
/**三总数归类**/
[2018-11-05 14:54:47] check os semaphore finished:
[2018-11-05 14:54:47] total_cnt:3, active_cnt:0, check_err_cnt:0, need_del:3, real_del:3, del_err_cnt:0!
```

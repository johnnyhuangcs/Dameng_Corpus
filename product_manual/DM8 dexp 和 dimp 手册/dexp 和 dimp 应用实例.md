

# dexp 和 dimp 应用实例

# 4 dexp 和 dimp 应用实例

## 4.1 一个完整示例

下面列出一个完整的导出/导入示例以供参考。

  1. **环境准备**



导出库：环境为 linux，服务器为 192.168.0.248，用户名为 SYSDBA。导出的是 DM 数据库系统安装时自带的名为 BOOKSHOP 的示例库，端口号 5236。

导入库：环境为 linux，服务器为 192.168.0.248，用户名为 SYSDBA。准备一个空数据库作为导入库，端口号为 8888。

  2. **dexp 导出**



导出数据库的所有对象（FULL=Y），导出文件为 dexp01.dmp，导出日志为 dexp01.log，导出文件和日志文件都存放在/emc_2/data/dexp 目录中。

```
./dexp SYSDBA/*****\@192.168.0.248:5236 FILE=dexp01.dmp LOG=dexp01.log DIRECTORY=/emc_2/data/dexp FULL=Y
```

  3. **dimp 导入**



导入 SYSDBA、OTHER、PERSON 模式中的数据（SCHEMAS=SYSDBA,OTHER,PERSON），导入文件就是上一步导出的文件 dexp01.dmp，导入日志 dimp02.log 放入/emc_2/data/dimp 目录中。

```
./dimp SYSDBA/*****\@192.168.0.248:8888 FILE=/emc_2/data/dexp/dexp01.dmp
LOG=dimp02.log DIRECTORY=/emc_2/data/dimp SCHEMAS=SYSDBA,OTHER,PERSON
```

  4. **查看 LOG 日志**



如果想了解详细的导出、导入内容统计信息，请查看 LOG 日志 dexp01.log 和 dimp02.log。

## 4.2 使用关联参数示例

使用加密相关参数 ENCRYPT、ENCRYPT_PASSWORD、ENCRYPT_NAME 进行导出导入。

  1. **环境准备**



导出库：环境为 linux，服务器为 192.168.0.248，用户名为 SYSDBA。导出库是 DM 数据库系统安装时自带的名为 BOOKSHOP 的示例库，端口号 5236。

导入库：环境为 linux，服务器为 192.168.0.248，用户名为 SYSDBA。准备一个空数据库作为导入库，端口号为 8889。

  2. **dexp 导出**



导出数据库的所有对象（FULL=Y），导出文件为 dexp03.dmp，导出日志为 dexp03.log，导出文件和日志文件都存放在/emc_2/data/dexp 目录中。

使用加密参数 ENCRYPT、ENCRYPT_PASSWORD、ENCRYPT_NAME，对导出库进行加密。

```
./dexp SYSDBA/*****\@192.168.0.248:5236 FILE=dexp03.dmp LOG=dexp03.log
DIRECTORY=/emc_2/data/dexp FULL=Y ENCRYPT=Y ENCRYPT_PASSWORD=damengren
ENCRYPT_NAME=DES_CBC
```

  3. **dimp 导入**



导入整个数据库（FULL=Y），导入文件就是上一步导出的文件 dexp03.dmp，导入日志 dimp04.log 放入/emc_2/data/dimp 目录中。

在导入的过程中，要提供 ENCRYPT_PASSWORD、ENCRYPT_NAME 两个参数，这两个参数值必须和导出时一样。

```
./dimp SYSDBA/*****\@192.168.0.248:8889 FILE=/emc_2/data/dexp/dexp03.dmp
LOG=dimp04.log DIRECTORY=/emc_2/data/dimp FULL=Y ENCRYPT_PASSWORD=damengren
ENCRYPT_NAME=DES_CBC
```

  4. **查看 LOG 日志**



如果想了解详细的导出、导入内容统计信息，请查看 LOG 日志 dexp03.log 和 dimp04.log。

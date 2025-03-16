

# DBMS_SYSTEM包

使用 DBMS_SYSTEM 包，可以在 INI 参数 COMPATIBLE_MODE=2 的前提下，通过执行 DBMS_SYSTEM.KSDWRT 包过程实现分别往数据库告警日志 ELOG 和 trace 文件写入信息。需要注意的是，只有在 INI 参数 TRC_LOG 为开启状态，且 INI 参数 TRC_LOG_MODULE 取值包含 12 时，向 TRACE 文件写入信息才能写入成功。

## 51.1 相关方法

  1. KSDWRT



向数据库告警日志 ELOG 和 trace 文件写入用户指定信息。

语法如下：

```
PROCEDURE KSDWRT(DEST_ID INT, WRT_STR VARCHAR2);
```

参数详解

  * DEST_ID 输入参数，指定信息写入目的地。

目的地参数有三个选项：

    1. DEST_ID=1，向 trace 文件写入 WRT_STR；
    2. DEST_ID=2，向 ELOG 文件写入 WRT_STR；
    3. DEST_ID=3，同时向 trace 文件与 ELOG 文件写入 WRT_STR。

若输入的目的地参数不合法或为空，则报错。

  * WRT_STR 输入参数，向日志写入的具体内容。

若日志写入内容为空串，则向 LOG 文件中写入空白；若写入内容过长，则进行截断处理，每 1000 个字节进行一次截断，并在被截断串末尾添加省略号"..."。



## 51.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包。请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_SYSTEM');
```

以指定内容为例使用 DBMS_SYSTEM 包。

例 1 INI 参数 TRC_LOG 开启，且 INI 参数 TRC_LOG_MODULE 取值包含 12 时，设置目的参数为 1，向 trace 文件写入信息。

```
CALL DBMS_SYSTEM.KSDWRT(1,'TEST_TRACE_FILE');
```

进入对应路径查看 trace 文件信息 dmtrc_DMSERVER.log，已记录“TEST_TRACE_FILE”和执行时间相关信息，结果如下：

```
2023-05-10 15:11:01.333 [INF] P2911112:T2908348 TEST_TRACE_FILE [IFUN_SYS2.C:28804]
```

例 2 设置目的参数为 2，向告警 elog 文件写入信息。

```
CALL DBMS_SYSTEM.KSDWRT(2,'TEST_ELOG_FILE');
```

进入对应路径查看 elog 文件信息 dm_DMSERVER_202305.log，已记录“TEST_ELOG_FILE”和执行时间相关信息，结果如下：

```
2023-05-10 15:11:12.358 [INFO] database P0002911112 T0000000000002908348  TEST_ELOG_FILE
```

例 3 INI 参数 TRC_LOG 开启，且 INI 参数 TRC_LOG_MODULE 取值包含 12 时，设置目的参数为 3，向告警 elog 文件与 trace 文件同时写入信息。

```
CALL DBMS_SYSTEM.KSDWRT(3,'TEST_TRACE_AND_ELOG_FILE');
```

进入对应路径查看 elog 文件与 trace 文件，已记录“TEST_TRACE_AND_ELOG_FILE”和执行时间相关信息，结果如下：

```
TRACE文件：
2023-05-10 15:11:27.891 [INF] P2911112:T2908348 TEST_TRACE_AND_ELOG_FILE [IFUN_SYS2.C:28804]
ELOG文件：
2023-05-10 15:11:27.890 [INFO] database P0002911112 T0000000000002908348  TEST_TRACE_AND_ELOG_FILE
```

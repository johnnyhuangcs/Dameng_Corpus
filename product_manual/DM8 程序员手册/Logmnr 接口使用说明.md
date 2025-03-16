

# Logmnr 接口使用说明

Logmnr 包是达梦数据库的日志分析工具，达梦提供了 JNI 接口和 C 接口，供应用程序直接调用。

在使用 Logmnr 包中的接口之前，需要先开启两个日志相关参数：一是开启归档（设置 INI 参数 ARCH_INI 为 1）；二是开启在日志中记录逻辑操作的功能（设置 INI 参数 RLOG_APPEND_LOGIC 为 1、2 或者 3）。

## 9.1 JNI 接口

logmnr.jar 包是达梦数据库的日志分析工具的 JNI 接口，供应用程序直接调用。

一般调用过程如下：

  1. 初始化 LOGMNR 环境；
  2. 创建一个分析日志的连接；
  3. 添加需要分析的日志文件；
  4. 启动日志文件分析；
  5. 获取完成分析的日志数据；
  6. 终止日志文件分析；
  7. 关闭当前连接；
  8. 销毁 LOGMNR 环境。



### 9.1.1 接口说明

logmnr.jar 包括两个类：LogmnrDll 和 LogmnrRecord。logmnr.jar 位于 DM 安装目录的 jar 文件夹下，例如：D:\dmdbms\jar\logmnr.jar。LogmnrDll 中包含了所有功能接口；LogmnrRecord 是日志分析结果数据的返回值对应的对象类型。

#### 9.1.1.1 初始化环境

函数原型

```
int LogmnrDll.initLogmnr();
```

功能说明

初始化 LOGMNR 环境。

参数说明

无。

返回值

0：初始化成功；

\< 0 的值和异常：初始化失败。

#### 9.1.1.2 创建连接

函数原型

```
long LogmnrDll.createConnect(String hostName, int port, String userName, String password);
```

功能说明

创建一个分析日志的连接。

参数说明

hostName：ip 或主库名。

port：端口号。

userName：用户名。

password：密码。

返回值

创建的数据库连接标识 ID。

#### 9.1.1.3 添加日志文件

函数原型

```
int LogmnrDll.addLogFile(long connId, String logFileName, int option)
```

功能说明

添加需要分析的归档日志文件。

参数说明

connId：数据库连接标识 ID。

logFileName：需要分析的归档日志文件名（绝对路径）。

option：可选配置参数。包括以下值：

1：结束当前日志挖掘（隐式调用 endLogmnr，以前添加的归档日志文件将被清除），并增加新的归档日志文件。
2：删除日志文件。
3：增加的归档日志文件名。

返回值

0：添加成功；

\< 0 的值和异常：添加失败。

注意事项

如果当前已经 startLogmnr，则 addLogFile 将报错，如果需要添加新的日志文件，需要先调用 endLogmnr 结束当前日志分析后再添加文件。

#### 9.1.1.4 移除日志文件

函数原型

```
int LogmnrDll.removeLogFile(long connId, String logFileName);
```

功能说明

移除指定的归档日志文件。

参数说明

connId：数据库连接标识 ID。

logFileName：需要移除档日志文件名（绝对路径）。

返回值

0：移除成功；

\< 0 的值和异常：移除失败。

注意事项

同 addLogFile 一样，如果当前已经 startLogmnr，则 removeLogFile 将报错，如果需要移除日志文件，需要先调用 endLogmnr 结束当前日志分析后再移除文件。

#### 9.1.1.5 启动日志分析

函数原型

```
int LogmnrDll.startLogmnr(long connId, long trxid, String startTime, String endTime);
```

功能说明

启动当前会话的归档日志文件分析，对添加的归档日志文件进行日志分析。

参数说明

connId：数据库连接标识 ID。

trxid：分析归档日志的事务 id 号，默认为-1，表示不区分事务号。

startTime：分析归档日志的起始时间，默认 1988/1/1。

endTime：分析归档日志的结束时间，默认 2110/12/31。

返回值

0：启动成功；

\< 0 的值和异常：启动失败。

#### 9.1.1.6 获取数据

函数原型

```
LogmnrRecord[]LogmnrDll.getData(long connId, int rownum);
```

功能说明

获取日志文件分析结果数据。

参数说明

connId：数据库连接标识 ID。

rownum：获取行数。

返回值

获取的 logmnr 记录，为 LogmnrRecord 对象数组。

##### 9.1.1.6.1 LogmnrRecord 对象说明

LogmnrRecord 对象支持的成员变量如下表所示。获取和设置这些变量可以使用相应的“get+ 成员变量”和“set+ 成员变量”方法。

|**成员变量**|**类型**|**说明**|
|----|----|----|
|scn|long|当前记录的 LSN|
|startScn|long|当前事务的起始 LSN|
|commitScn|long|当前事务的截止 LSN|
|timestamp|String|当前记录的创建时间|
|startTimestamp|String|当前事务的起始时间|
|commitTimestamp|String|当前事务的截止时间|
|xid|String|当前记录的事务 ID 号|
|operation|String|操作类型包括 start、insert、update、delete、commit、rollback 等语句|
|operationCode|String|操作类型。插入操作值为 1，更新操作值为 3，删除操作值为 2，事务起始语句值为 6，提交操作值为 7，回滚操作值为 36|
|rollBack|int|当前记录是否被回滚；1：是，0：否|
|segOwner|String|执行这条语句的用户名|
|tableName|String|操作的表名|
|rowId|String|对应记录的行号|
|rbasqn|int|对应的归档日志文件号|
|rbablk|int|RBASQN 所指日志文件的块号从 0 开始|
|rbabyte|int|RBABLK 所指块号的块内偏移|
|dataObj|int|对象 ID 号|
|dataObjv|int|对象版本号|
|sqlRedo|String|当前记录对应的 sql 语句|
|rsId|Stirng|记录集 ID|
|ssn|int|连续 sql 标志。如果 sql 长度超过单个 sql_redo 字段能存储的长度，则 sql 会被截断成多个 sql 片段在结果集中“连续”返回|
|csf|int|与 SSN 配合。最后一个片段的 csf 值为 0，其余片段的值均为 1。没因超长发生截断的 sql 该字段值均为 0|
|status|int|日志状态。缺省为 0|


#### 9.1.1.7 终止日志分析

函数原型

```
int LogmnrDll.endLogmnr(long connId, int option);
```

功能说明

终止当前会话的归档日志文件分析。

参数说明

connId：数据库连接标识 ID。

options：可选模式如下：

0：清除当前会话的归档日志文件分析环境，再次分析时需要重新 add_logfile。

1：保留当前会话的归档日志文件分析环境，不需要重新 add_logfile。

返回值

0：终止成功；

\< 0 的值和异常：终止失败。

#### 9.1.1.8 关闭连接

函数原型

```
int LogmnrDll.closeConnect(long connId);
```

功能说明

关闭当前连接，清理字典缓存等。

参数说明

connId：数据库连接标识 ID。

返回值

0：关闭成功；

\< 0 的值和异常：关闭失败。

#### 9.1.1.9 销毁环境

函数原型

```
boolean LogmnrDll.deinitLogmnr();
```

功能说明

销毁 LOGMNR 环境。

参数说明

无。

返回值

0：添加销毁；

\< 0 的值和异常：销毁失败。

#### 9.1.1.10 设置属性

函数原型

```
int LogmnrDll.setAttr(long connId, int attr, int attr_value)
```

功能说明

设置属性。

有如下属性可以配置：

LogmnrDll.LOGMNR_ATTR_PARALLEL_NUM：并行线程数，取值范围(2，16)，缺省为 2。该参数设置解析日志的线程个数，当系统内存充足且希望提高日志分析效率时，可以考虑增大该参数以优化性能。

LogmnrDll.LOGMNR_ATTR_BUFFER_NUM：任务缓存节点数，取值范围(8，1024)，缺省为 8。该参数设置从日志文件读取数据时缓冲区个数，当系统内存充足且希望提高文件读取效率时，可以考虑增大该参数优化性能。

LogmnrDll.LOGMNR_ATTR_CONTENT_NUM：结果缓存节点数，取值范围(256， 2048)，缺省为 256。该参数设置结果缓冲区的个数，当系统内存充足且希望提高挖掘结果输出效率时，可以考虑增大该参数以优化性能。

LOGMNR_ATTR_TRX_END：是否查找事务结束记录，缺省为 1，取值范围 0 或 1。该参数为 1 时，若当前语句未解析出对应事务的结束记录，则会等待直到全部日志文件解析完成或者达到最大等待时间。若期望挖掘日志中的挖掘结果包含结束记录（影响属性 ROLL_BACK、COMMIT_SCN、COMMIT_TIMESTAMP、CSCN），则应开启，否则可以考虑关闭以提高性能。

LOGMNR_ATTR_TRX_WAIT_TIME：查找事务结束记录的最大等待时间，单位为秒(s)，缺省为 60s，取值范围（0，600）。设置成 0 表示死等，只有 LOGMNR_ATTR_TRX_END 为 1 时才有效。若实际操作中单个事务包含多条语句，且设置的单个归档文件较小，则该事务对应的语句可能跨日志文件，需要解析多个文件才能获取事务结束记录，此时应增大该参数或将其设置为 0 来保证所有日志文件均被解析。

参数说明

connId：数据库连接标识 ID。

attr：属性名。

attr_value：属性值。

返回值

0：添加成功；

\< 0 的值和异常：添加失败。

### 9.1.2 编程实例

下面用一个例子来说明 dmlogmnr 接口的使用方法。

数据库安装在本机上，端口号为 5236，用户名为 SYSDBA，初始化时设置密码为 sysDBA*00。

开启两个日志相关参数：一是开启归档（设置 INI 参数 ARCH_INI 为 1）；二是开启在日志中记录逻辑操作的功能（设置 INI 参数 RLOG_APPEND_LOGIC 为 1、2 或者 3）。

要分析的日志为 D:\dmdata\arch\ARCHIVE_LOCAL1_20160704082303068.log。分析的结果放在 D:\dmdata\result.txt 中。其中 logmnr.jar 包放在 DM 数据库安装目录的 jar 文件夹下。

在项目中创建类名为 dbmslob，代码实现如下：

```
import java.io.PrintStream;
import com.dameng.logmnr.LogmnrDll;
import com.dameng.logmnr.LogmnrRecord;
public class dbmslob {
	public static void main(String[] args) {
 	try
		{
			LogmnrDll.initLogmnr();	
			long connid = LogmnrDll.createConnect("localhost", 5236, "SYSDBA",	"sysDBA*00");			LogmnrDll.addLogFile(connid,"D:\\dmdata\\arch\\ARCHIVE_LOCAL1_20160704082303068.log",3);			 	LogmnrDll.startLogmnr(connid, -1, null, null);
			LogmnrRecord[] arr = LogmnrDll.getData(connid, 100);
			PrintStream ps = new PrintStream("D:\\dmdata\\result.txt");
	          System.setOut(ps);
	          System.out.println("日志分析结果打印：");
	          for (int i =  0; i < arr.length; i++) {
				System.out.println("-----------------------------" + i + "-----------------------------" + "\n");
				System.out.println("xid:" + arr[i].getXid() + "\n");
				System.out.println("operation:" + arr[i].getOperation() + "\n");
				System.out.println("sqlRedo:" + arr[i].getSqlRedo() + "\n");
				System.out.println("#############################" + i + "#############################" + "\n");
				System.out.println("scn:" + arr[i].getScn() + "\n");
				System.out.println("startScn:" + arr[i].getStartScn() + "\n");
				System.out.println("commitScn:" + arr[i].getCommitScn() + "\n");
				System.out.println("timestamp:" + arr[i].getTimestamp() + "\n");
				System.out.println("startTimestamp:" + arr[i].getStartTimestamp() + "\n");
				System.out.println("commitTimestamp:" + arr[i].getCommitTimestamp() + "\n");
				System.out.println("operationCode:" + arr[i].getOperationCode() + "\n");
				System.out.println("rollBack:" + arr[i].getRollBack() + "\n");
				System.out.println("segOwner:" + arr[i].getSegOwner() + "\n");
				System.out.println("tableName:" + arr[i].getTableName() + "\n");
				System.out.println("rowId:" + arr[i].getRowId() + "\n");
				System.out.println("rbasqn:" + arr[i].getRbasqn() + "\n");
				System.out.println("rbablk:" + arr[i].getRbablk() + "\n");
				System.out.println("rbabyte:" + arr[i].getRbabyte() + "\n");
				System.out.println("dataObj:" + arr[i].getDataObj() + "\n");
				System.out.println("dataObjv:" + arr[i].getDataObjv() + "\n");
				System.out.println("//dataObjd:" + arr[i].getDataObjd() + "\n");
				System.out.println("rsId:" + arr[i].getRsId() + "\n");
				System.out.println("ssn:" + arr[i].getSsn() + "\n");
				System.out.println("csf:" + arr[i].getCsf() + "\n");
				System.out.println("status:" + arr[i].getStatus() + "\n");
				System.out.println("#############################" + i + "#############################" + "\n");
	      }  
	    	System.out.println("结果打印完毕");
		ps.flush();
	     ps.close();		    
		LogmnrDll.endLogmnr(connid, 1);
		LogmnrDll.deinitLogmnr();
		}catch (Exception e) {
			e.printStackTrace();
		}
	}
}
```

分析的结果在 D:\dmdata\result.txt 中查看。

## 9.2 C 接口

dmlogmnr_client.dll 是达梦数据库的日志分析工具的 C 接口，供应用程序直接调用。dmlogmnr_client C 接口依赖的动态链接库在 DM 安装目录\bin 目录下，依赖的静态库 dmlogmnr_client.lib 在 DM 安装目录\include 目录下，所需的头文件 logmnr_client.h 也在 DM 安装目录\include 目录下。

应用程序员在调用接口时应注意接口参数为句柄指针时，应传入正确的结构指针，否则可能造成异常。

### 9.2.1 接口说明

#### 9.2.1.1 初始化环境

函数原型

```
dmcode_t

logmnr_client_init();
```

参数说明

无。

功能说明

初始化 LOGMNR 环境。说明：初始化互斥量等。

返回值

0：成功；

\<0：错误。

#### 9.2.1.2 创建连接

函数原型

```
dmcode_t
logmnr_client_create_connect( 
	schar*			ip,
	usint			  port,
	schar*			uname,
	schar*			pwd,
	void**			conn
);
```

功能说明

创建一个分析日志的连接。

参数说明

ip：    输入参数，服务器 IP；

port：  输入参数，端口号；

uname： 输入参数，用户名；

pwd：   输入参数，登录密码；

conn：  输出参数，连接句柄。

说明

执行接口的用户至少需要授予 CREATE SESSION 权限和 V$DM_INI 表的 SELECT 权限。

返回值

0：成功；

\<0：错误。

#### 9.2.1.3 增加日志文件

函数原型

```
dmcode_t
logmnr_client_add_logfile( 
	void*		conn,
	schar* 	logfilename,
	ulint		options
);
```

功能说明

增加需要分析的归档日志文件。

参数说明

conn：       连接句柄；

logfilename：需要分析的归档日志文件名（绝对路径）；

options：    可选配置参数包括：

1：结束当前日志挖掘 （隐式调用 endLogmnr，以前添加的归档日志文件将被清除），并增加新的归档日志文件。

2：删除日志文件。

3：增加的归档日志文件名。

说明：一旦 startLogmnr，addLogFile 可以继续添加文件，那么新添加的文件必须比之前添加的所有文件的创建时间都要晚。

返回值

0：成功；

\<0：错误。

#### 9.2.1.4 删除日志文件

函数原型

```
dmcode_t
logmnr_client_remove_logfile( 
	void*		  conn,
	schar*		logfilename
);
```

功能说明

移除某个需要分析的归档日志文件。

参数说明

conn：       连接句柄；

logfilename：待移除的归档日志文件名（绝对路径）。

说明：只有 startLogmnr 之前才能进行 remove 操作，否则会报错返回。

返回值

0：成功

\<0：错误

#### 9.2.1.5 启动日志分析

函数原型

```
dmcode_t
logmnr_client_start( 
	void*		  conn,
	lint64		trxid,
	schar*		starttime,
	schar*		endtime
);
```

功能说明

启动当前会话的归档日志文件分析，对 ADD_LOGFILE 添加的归档日志文件进行日志分析。

参数说明

conn：连接句柄。

trxid：分析归档日志的事务 id 号，缺省为-1,表示不区分事务号。

starttime：分析归档日志的起始时间，缺省为 1988/1/1。

endtime：  分析归档日志的结束时间，缺省为 2110/12/31。

返回值

0：成功；

\<0：错误。

#### 9.2.1.6 获取信息

函数原型

```
dmcode_t
logmnr_client_get_data(
	void*									conn,
	lint									row_num,
	logmnr_content_t***		data,
	lint*			  					real_num
);
```

功能说明

获取数据。

参数说明

conn：    输入参数，连接句柄；

rownum：  输入参数，获取行数；

data：    输出参数，返回数据；

real_num：输出参数，实际获取行数。

返回值

获取的 logmnr 记录。

#### 9.2.1.7 终止日志分析

函数原型

```
dmcode_t
logmnr_client_end(
	void*		conn,
	ulint		options 
);
```

功能说明

终止当前会话的归档日志文件分析。

参数说明

conn：连接句柄；

options：可选模式如下：

0：清除当前会话的归档日志文件分析环境，再次分析时需要重新 add_logfile。

1：保留当前会话的归档日志文件分析环境，不需要重新 add_logfile

返回值

0：成功；

\<0：错误。

#### 9.2.1.8 结束连接

函数原型

```
dmcode_t
logmnr_client_close_connect(
	void*		conn
);
```

参数说明

conn：连接句柄。

功能说明

关闭当前连接。说明：会清理字典缓存等。

返回值

0：成功；

\<0：错误。

#### 9.2.1.9 销毁环境

函数原型

```
dmcode_t

logmnr_client_deinit();
```

功能说明

销毁 LOGMNR 环境。

参数说明

无。

返回值

0：成功；

\<0：错误。

#### 9.2.1.10 设置日志分析属性

函数原型

```
dmcode_t
logmnr_client_set_attr( 
    void*           conn,
    ulint           attr,
    void*           val,
    ulint           val_len
);
```

参数说明

conn：输入参数，连接句柄；

attr：输入参数，属性名称；

val：输入参数，属性 attr 的值；

val_len：输入参数，属性值 val 的长度。

功能说明

设置日志分析属性。

有如下属性可以设置：

LOGMNR_ATTR_PARALLEL_NUM：并行线程数，取值范围(2，16)；

LOGMNR_ATTR_BUFFER_NUM：任务缓存节点数，取值范围(8，1024)；

LOGMNR_ATTR_CONTENT_NUM：结果缓存节点数，取值范围(256，2048)；

LOGMNR_ATTR_TRX_END：是否查找事务结束记录，缺省为 1，取值范围 0 或 1；

LOGMNR_ATTR_TRX_WAIT_TIME：查找事务结束记录的最大等待时间，单位为 s，缺省为 60s，取值范围（0，600），设置成 0 表示死等，只有 LOGMNR_ATTR_TRX_END 为 1 时才有效;

LOGMNR_ATTR_OPTIONS:分析日志选项，与 DBMS_LOGMNR 系统包中 start_logmnr 的 options 保持一致，参考《DM8 系统包使用手册》的 DBMS_LOGMNR 包章节;

LOGMNR_ATTR_CHECK_TIME:分析日志时是否检查日志生成时间与表创建时间的先后关系，若日志生成时间早于表创建时间，则认为该条日志对应的表已删除，不使用在线字典的信息。id 不重用的情况下不建议开启。需在 LOGMNR_ATTR_OPTIONS 中设置使用在线字典才能生效。缺省为 0，取值范围 0 或 1；

返回值

0：成功；

\<0：错误。

#### 9.2.1.11 获取日志分析属性

函数原型

```
dmcode_t
logmnr_client_get_attr( 
    void*           conn,
    ulint           attr,
    void*           buf,
	  ulint			      buf_len,
    ulint*          val_len
);
```

参数说明

conn：输入参数，连接句柄；

attr：输入参数，属性名称；

buf：输出参数，属性 attr 的值；

buf_len：输入参数，属性值缓存 buf 的长度；

val_len：输出参数，属性值的实际长度(\<=buf_len)。

功能说明

获取日志分析属性。

有如下属性可以获取：

LOGMNR_ATTR_PARALLEL_NUM ：并行线程数，取值范围(2，16)；

LOGMNR_ATTR_BUFFER_NUM：任务缓存节点数，取值范围(8，1024)；

LOGMNR_ATTR_CONTENT_NUM：结果缓存节点数，取值范围(256，2048)；

LOGMNR_ATTR_CHAR_CODE：本地编码（只读）；

LOGMNR_ATTR_TRX_END：是否查找事务结束记录，缺省为 1，取值范围 0 或 1；

LOGMNR_ATTR_TRX_WAIT_TIME：查找事务结束记录的最大等待时间，单位为 s，缺省为 60s，取值范围（0，600），设置成 0 表示死等，只有 LOGMNR_ATTR_TRX_END 为 1 时才有效;

LOGMNR_ATTR_OPTIONS：分析日志选项，与 DBMS_LOGMNR 系统包中 start_logmnr 的 options 保持一致，参考《DM8 系统包使用手册》的 DBMS_LOGMNR 包章节;

LOGMNR_ATTR_CHECK_TIME：分析日志时是否检查日志生成时间与表创建时间的先后关系，若日志生成时间早于表创建时间，则认为该条日志对应的表已删除，不使用在线字典的信息。id 不重用的情况下不建议开启。需在 LOGMNR_ATTR_OPTIONS 中设置使用在线字典才能生效。缺省为 0，取值范围 0 或 1。

返回值

0：成功；

\<0：错误。

### 9.2.2 基本示例

数据库安装在本机上，端口号为 5236，用户名为 SYSDBA，初始化时设置密码为 sysDBA*00。

开启两个日志相关参数：一是开启归档（设置 INI 参数 ARCH_INI 为 1）；二是开启在日志中记录逻辑操作的功能（设置 INI 参数 RLOG_APPEND_LOGIC 为 1、2 或者 3）。

```
#include <stdio.h>
#include <stdlib.h>
#include "logmnr_client.h"
void
main(int argc, schar* argv[])
{
	lint				rt;
	void*				conn;
	logmnr_content_t**	logmnr_contents;
	lint				real_num;
	ulint				char_code;

	rt = logmnr_client_init();
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_init error");
		exit(-1);
	}

	rt = logmnr_client_create_connect("LOCALHOST", 5236, "SYSDBA", "sysDBA*00", &conn);
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_create_connect error");
		exit(-1);
	}

	//需要添加的文件名
	rt = logmnr_client_add_logfile(conn, "d:\\dmdata\\arch\\ARCHIVE_LOCAL1_20140725091059625.log", LOGMNR_ADDFILE);
   
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_add_logfile error");
		exit(-1);
	}

	rt = logmnr_client_set_attr(conn, LOGMNR_ATTR_PARALLEL_NUM, (void*)2, 0);
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_set_attr error");
		exit(-1);
	}

	rt = logmnr_client_start(conn, -1, NULL, NULL);
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_start error");
		exit(-1);
	}

	rt = logmnr_client_get_attr(conn, LOGMNR_ATTR_CHAR_CODE, &char_code, 4, 0);
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_set_attr error");
		exit(-1);
	}

	while(1)
	{
		rt = logmnr_client_get_data(conn, 6, &logmnr_contents, &real_num);
		if (rt < 0)
		{
			fprintf(stderr, "logmnr_client_get_data error");
			exit(-1);
		}
		if (real_num == 0)
			break;
	}

	rt = logmnr_client_end(conn, 0);
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_end error");
		exit(-1);
	}

	rt = logmnr_client_close_connect(conn);
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_close_connect error");
		exit(-1);
	}

	rt = logmnr_client_deinit();
	if (rt < 0)
	{
		fprintf(stderr, "logmnr_client_deinit error");
		exit(-1);
	}
} 
```

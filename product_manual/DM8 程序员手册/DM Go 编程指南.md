

# DM Go 编程指南

## 11.1 Go DM 数据库驱动介绍

Go 语言标准库 database/sql（[https://golang.google.cn/pkg/database/sql/](https://golang.google.cn/pkg/database/sql/)）提供了一系列数据库操作的标准接口，DM 数据库基于 GO1.13 版本通过实现 database/sql 包的接口，向开发人员提供 DM 数据库操作的 Go 语言接口。

## 11.2 环境准备

  1. **安装 dm 驱动包**



将提供的 dm 驱动包放在 GOPATH 的 src 目录下。

  2. **安装依赖包**



```
go get golang.org/x/text

go get github.com/golang/snappy
```

## 11.3 连接串属性说明

连接串基本格式：

```
dm://user:password@host:port[?propName1=propValue1][&propName2=propValue2]…
dm://user:password@GroupName[?propName1=propValue1][&propName2=propValue2]…
dm://user:password@GroupName?GroupName=(host1:port1,host2:port2,…)[&propName1=propValue1]…
dm://user:password@host:port?dialName=anyName[&propName1=propValue1]…
```

propName 表示连接串属性名称，详见表 11.1。在连接串基本格式中，第四种连接串格式使用了自定义连接代替驱动中默认的 TCP 连接，具体使用方法可参考连接串属性 dialName 的说明。其余参数介绍请参考 [10.4 连接串可配置属性](https://eco.dameng.com/document/dm/zh-cn/pm/nodejs-rogramming-guide.html#10.4%20%E8%BF%9E%E6%8E%A5%E4%B8%B2%E5%8F%AF%E9%85%8D%E7%BD%AE%E5%B1%9E%E6%80%A7)。

连接串中可以设置的属性及其说明见下表。

表 11.1 Go 连接串属性

|**属性名称**|**说明**|
|----|----|
|socketTimeout|套接字超时时间，缺省为 0|
|escapeProcess|是否进行语法转义处理，缺省为 false；取值范围（true，false）|
|autoCommit|是否自动提交，缺省为 true；取值范围（true，false）|
|maxRows|结果集行数限制，若超过上限，则对结果集进行截断，取值范围 0~2147483647，缺省为 0，表示无限制|
|rowPrefetch|预取行数，缺省为 10|
|lobMode|Lob 模式，缺省为 1；1： 分批缓存到本地，2： 一次将大字段数据缓存到本地|
|ignoreCase|是否忽略大小写，缺省为 true，取值范围（true，false）|
|alwayseAllowCommit|在自动提交开关打开时，调用 rollback()接口是否不报错，缺省为 true，取值范围（true，false）|
|batchType|批处理类型，缺省为 1，1：进行批量绑定，2：不进行批量绑定|
|appName|客户端应用程序名称|
|sessionTimeout|会话超时时间，缺省为 0|
|sslCertPath|指定 ssl 加密证书文件的路径|
|sslKeyPath|指定 ssl 加密密钥文件的路径|
|mppLocal|是否 MPP 本地连接，缺省为 false；取值访问（true，false）|
|rwSeparate|是否使用读写分离系统，缺省为 false；取值访问（false：不使用，true：使用）|
|rwPercent|分发到主库的事务占主备库总事务的百分比，取值范围 0~100，缺省为 25|
|isBdtaRS|是否使用列模式结果集，缺省为 false；取值范围（true，false）|
|doSwitch|连接发生异常或一些特殊场景下的连接处理策略。取值范围 0~2；0：关闭连接；1：当连接发生异常时自动切换到其他库，无论切换成功还是失败都会报错，用于通知上层应用进行事务执行失败时的相关处理；2：配合 epSelector=1 使用，如果服务名列表前面的节点恢复了，将当前连接切换到前面的节点上；缺省为 0|
|continueBatchOnError|批量执行出错时是否继续执行；缺省为 false；取值范围（true，false）|
|connectTimeout|连接数据库超时时间；单位 ms，取值范围 0~2147483647，0 表示无限制；缺省为 5000|
|columnNameUpperCase|列名转换为大写字母，缺省为 false；取值范围（true，false）|
|rwIgnoreSql|读写分离是否忽略 sql 类型，并按比例分发到主/备库，缺省为 false，取值范围（true，false）|
|compatibleMode|兼容其他数据库, 属性值为数据库名称（例如：Oracle）, 支持兼容 Oracle 和 Mysql|
|dbAliveCheckFreq|检测数据库是否存活的频率，单位 ms，缺省为 0；0：不检测，1：检测|
|logDir|日志等其他一些驱动过程文件生成目录，缺省值是当前工作目录|
|logLevel|生成日志的级别，日志按从低到高依次如下（off：不记录；error：只记录错误日志；warn：记录警告信息；sql：记录 sql 执行信息；info：记录全部执行信息；all：记录全部），缺省为 off，高级别同时记录低级别的信息|
|logFlushFreq|日志刷盘频率，单位 s，缺省为 10|
|logBufferSize|日志缓冲区大小，缺省为 32768|
|logFlusherQueueSize|日志刷盘线程中等待刷盘的日志缓冲区队列大小，缺省为 100|
|statEnable|是否启用状态监控，缺省为 false；取值范围（true，false）|
|statFlushFreq|状态监控统计信息刷盘频率，单位 s；缺省为 3|
|statSlowSqlCount|日志打印慢 sql top 行数，缺省为 100；取值范围 0~1000|
|statHighFreqSqlCount|日志打印高频 sql top 行数，缺省为 100；取值范围 0~1000|
|statSqlMaxCount|状态监控可以统计不同 sql 的个数，缺省为 100000；取值范围 0~100000|
|statSqlRemoveMode|执行的不同 sql 个数超过 statSqlMaxCount 时使用的淘汰方式，取值范围（latest/eldest）；latest 淘汰最近执行的 sql，eldest 淘汰最老的 sql，缺省为 latest|
|statDir|状态监控信息以文本文件形式输出的目录，无缺省值，若不指定则监控信息不会以文本文件形式输出|
|schema|指定用户登录后的当前模式，缺省为用户的默认模式|
|cipherPath|第三方加密算法动态链接库位置|
|compress|是否压缩消息。取值范围 0~2；0：不压缩；1：完全压缩；2：优化的压缩；缺省为 0|
|compressId|压缩算法。0：ZLIB 压缩；1：SNAPPY 压缩；缺省为 0|
|svcConfPath|自定义客户端配置文件(dm_svc.conf)的完整路径|
|batchAllowMaxErrors|continueBatchOnError 开启时，最大可容错行数，超过则停止执行，但不报错，取值范围 0~65535，缺省为 0，表示无限制|
|batchNotOnCall|存储过程是否不批量执行，默认 false，取值（true，false）|
|cluster|当 doSwitch=2，epSelector=1 时，用于检测 DSC 集群节点故障恢复是否成功，默认空，取值（DSC）|
|columnNameCase|列名大小写显示策略，默认空，取值（upper，lower）|
|epSelector|服务名连接策略。0：依次选取列表中的不同节点建立连接，使得所有连接均匀地分布在各个节点上；1：选择列表中最前面的节点建立连接，只有当前节点无法建立连接时才会选择下一个节点进行连接；默认 0|
|loginDscCtrl|服务名连接数据库时是否只选择 dsc control 节点的库，默认 false，取值（true，false）|
|osName|操作系统名称，默认空|
|addressRemap|地址重定向，格式：(IP_1:PORT_1,IP_2:PORT_2)，如：addressRemap=(192.168.0.1:5236,localhost:5237)，如果连接 192.168.0.1:5236，则实际连接到 localhost:5237。可以配置多个地址重定向，例如：addressRemap=(localhost:5236,localhost:5237)(localhost:5238,localhost:5239)，缺省为空|
|userRemap|用户名重定向，格式：(USER1,USER2)，如：userRemap=(USER1,USER2)，如果使用 USER1 用户连接，则实际连接用户为 USER2。可以配置多个重定向，例如：userRemap=(USER1,USER2)(USER3,USER4)，缺省为空|
|switchInterval|服务名连接数据库时，若遍历了服务名中所有库列表都未找到符合条件的库成功建立连接，等待一定时间再继续下一次遍历；单位 ms，取值范围 0~2147483647，缺省为 1000|
|switchTimes|服务名连接数据库时，若未找到符合条件的库成功建立连接，将尝试遍历服务名中库列表的次数，取值范围 0~2147483647，缺省 1|
|dialName|指定自定义连接代替驱动中默认的 TCP 连接，本参数需要配合 dm.RegisterDial/dm.RegisterDialContext 函数使用，下面演示简单的应用：  import  (  “net”  “sql”  “dm”  )  dm.RegisterDial(“myDial”,  func (addr string) (net.Conn, error) {  //此处按照实际需求建立想要的连接，并返回标准 net.Conn 对象  // 后续相关数据库操作的消息包都将通过该对象传输到目的端    return net.Dial(“tcp”, addr)  })  dsn  := “dm://user:pwd@ip:port?dialName=myDial”  db,  err := sql.Open(“dm”, dsn)|


## 11.4 相关方法

### 11.4.1 DB

DB 是一个数据库（操作）句柄，代表一个具有零到多个底层连接的连接池。

DB 的相关方法介绍如下：

  1. Open



函数原型

```
func Open(driverName, dataSourceName string) (*DB, error)
```

功能说明

打开数据库。根据指定的驱动名与数据源打开数据库，此时还未真正建立数据库连接，还应调用返回值的 Ping 方法。

参数说明

driverName：驱动名，此处应为”dm”；

dataSourceName：数据源，参考 12.3 连接串属性说明。

返回值

*DB：数据库(操作)句柄；

error：错误信息，若执行成功，则值为 nil。

示例：

```
// 引用Go标准库sql和dm驱动包，后面的示例不再赘述
import (
	"database/sql"
	_ "dm"
)
db, err := sql.Open("dm", "dm://SYSDBA:sysDBA*00@localhost:5236?autoCommit=true")
```

2.Ping

函数原型

```
func (db *DB) Ping() error
```

功能说明

检查连接。检查与数据库连接是仍有效，如果需要会创建连接。

参数说明

无。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := db.Ping()
```

3.Close

函数原型

```
func (db *DB) Close() error
```

功能说明

关闭连接。关闭数据库连接，同时释放任何打开的资源。

参数说明

无。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := db.Close()
```

4.SetMaxOpenConns

函数原型

```
func (db *DB) SetMaxOpenConns(n int)
```

功能说明

设置最大连接数。若 n\<=0，表示不限制。

参数说明

n：最大连接数。

返回值

无。

示例：

```
db. SetMaxOpenConns(10)
```

5.SetMaxIdleConns

函数原型

```
func (db *DB) SetMaxIdleConns(n int)
```

功能说明

设置最大闲置连接数。若 n\<=0，表示不保留闲置连接。

参数说明

n：最大闲置连接数。

返回值

无。

示例：

```
db. SetMaxIdleConns(3)
```

6.Exec

函数原型

```
func (db *DB) Exec(query string, args …interface{}) (Result, error)
```

功能说明

执行 sql 语句。可执行查询、插入、删除、更新等操作。

参数说明

query：要执行的 sql 语句；

args：sql 语句中的占位参数。

返回值

result：对已执行的 sql 命令的总结；

error：错误信息，若执行成功，则值为 nil。

示例：

```
_, err := db. Exec("delete from TEST where id=?", 10)
```

7.Query

函数原型

```
func (db *DB) Query(query string, args …interface{}) (*Rows, error)
```

功能说明

执行查询，返回多行。一般用于执行 select 命令。

参数说明

query：要执行的 sql 语句；

args：sql 语句中的占位参数。

返回值

*Rows：查询结果集句柄；

error：错误信息，若执行成功，则值为 nil。

示例：

```
rows, err := db.Query("select * from TEST")
```

8.QueryRow

函数原型

```
func (db *DB) QueryRow(query string, args …interface{}) *Row
```

功能说明

执行查询，返回一行。总是返回非 nil 值，直到返回值的 Scan 方法被调用时，才会返回被延迟的错误。

参数说明

query：要执行的 sql 语句；

args：sql 语句中的占位参数。

返回值

*Row：单行查询结果句柄。

示例：

```
var username string
err := db.QueryRow("select name from TEST where id=?", 10).Scan(&username)
```

9.Prepare

函数原型

```
func (db *DB) Prepare(query string) (*Stmt, error)
```

功能说明

准备语句。创建一个准备好的状态用于之后的查询和命令。返回值可以同时执行多个查询和命令。

参数说明

query：要准备的 sql 语句。

返回值

*Stmt：语句句柄；

error：错误信息，若执行成功，则值为 nil。

示例：

```
stmt, err := db.Prepare("select * from TEST")
```

10.Begin

函数原型

```
func (db *DB) Begin() (*Tx, error)
```

功能说明

开始事务。隔离水平由数据库驱动决定。

参数说明

无。

返回值

*Tx：事务句柄；

error：错误信息，若执行成功，则值为 nil。

示例：

```
tx, err := db.Begin()
```

### 11.4.2 Row

QueryRow 方法返回 Row，代表单行查询结果。

ROW 相关方法如下：

  1. Scan



函数原型

```
func (r *Row) Scan(dest …interface{}) error
```

功能说明

导出查询结果。将查询结果行的各列分别保存进 dest 参数指定的值中。

参数说明

dest：输出值句柄。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
var username string
err := row.Scan(&username)
```

### 11.4.3 Rows

Query 方法返回 Rows，代表查询结果集。游标指向结果集的第零行，使用 Next 方法遍历各行结果。

Rows 相关方法如下：

  1. Columns



函数原型

```
func (rs *Rows) Columns() ([]string, error)
```

功能说明

返回列名。若 Rows 已关闭，返回错误。

参数说明

无。

返回值

[]string：列名数组；

error：错误信息，若执行成功，则值为 nil。

示例：

```
cols, err := rows.Columns()
```

2.Scan

函数原型

```
func (rs *Rows) Scan(dest …interface{}) error
```

功能说明

导出查询结果。将当前行的各列分别保存进 dest 参数指定的值中。

参数说明

dest：输出值句柄。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
var username string
err := rows.Scan(&username)
```

3.Next

函数原型

```
func (rs *Rows) Next() bool
```

功能说明

将结果集的游标指向下一行。若成功，返回 true；若出现错误或没有下一行，返回 false。

参数说明

无。

返回值

bool：游标是否成功移动到下一行。

示例：

```
success := rows.Next()
```

4.Close

函数原型

```
func (rs *Rows) Close() error
```

功能说明

关闭结果集。

参数说明

无。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := rows.Close()
```

5.Err

函数原型

```
func (rs *Rows) Err() error
```

功能说明

返回迭代时可能的错误。需在显式或隐式调用 Close 方法后调用。

参数说明

无。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := rows.Err()
```

### 11.4.4 Stmt

经过 Prepare 方法准备好的语句。

Stmt 相关方法如下：

  1. Exec



函数原型

```
func (s *Stmt) Exec(args …interface{}) (Result, error)
```

功能说明

执行语句。参数 args 表示语句中的占位参数。

参数说明

args：sql 语句中的占位参数。

返回值

Result：对已执行的 sql 命令的总结；

error：错误信息，若执行成功，则值为 nil。

示例：

```
_, err := stmt.Exec()
```

2.Query

函数原型

```
func (s *Stmt) Query(args …interface{}) (*Rows, error)
```

功能说明

执行查询，返回多行。

参数说明

args：sql 语句中的占位参数。

返回值

*Rows：查询结果集句柄；

error：错误信息，若执行成功，则值为 nil。

示例：

```
rows, err := stmt.Query("select * from TEST")
```

3.QueryRow

函数原型

```
func (s *Stmt) QueryRow(args …interface{}) *Row
```

功能说明

执行查询，返回一行。

参数说明

args：sql 语句中的占位参数。

返回值

*Row：查询结果行句柄。

示例：

```
var username string
err := stmt.QueryRow(10).Scan(&username)
```

4.Close

函数原型

```
func (s *Stmt) Close() error
```

功能说明

关闭准备好的语句。

参数说明

无。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := stmt.Close()
```

### 11.4.5 Tx

Begin 方法返回一个进行中的数据库事务。一次事务必须以对 Commit 或 Rollback 的调用结束。

Tx 相关方法如下：

  1. Exec



函数原型

```
func (tx *Tx) Exec(query string, args …interface{}) (Result, error)
```

功能说明

执行语句。参数 args 表示 query 中的占位参数。

参数说明

query：要执行的 sql 语句；

args：sql 语句中的占位参数。

返回值

result：对已执行的 sql 命令的总结；

error：错误信息，若执行成功，则值为 nil。

示例：

```
_, err := tx.Exec("delete from TEST where id=?", 10)
```

2.Query

函数原型

```
func (tx *Tx) Query(query string, args …interface{}) (*Rows, error)
```

功能说明

执行查询，返回多行。

参数说明

query：要执行的 sql 语句；

args：sql 语句中的占位参数。

返回值

*Rows：查询结果集句柄；

error：错误信息，若执行成功，则值为 nil。

示例：

```
rows, err := stmt.Query("select * from TEST")
```

3.QueryRow

函数原型

```
func (tx *Tx) QueryRow(query string, args …interface{}) *Row
```

功能说明

执行查询，返回一行。总是返回非 nil 值，直到返回值的 Scan 方法被调用时，才会返回被延迟的错误。

参数说明

query：要执行的 sql 语句；

args：sql 语句中的占位参数。

返回值

*Row：查询结果行句柄。

示例：

```
var username string
err := tx.QueryRow("select name from TEST where id=?", 10).Scan(&username)
```

4.Prepare

函数原型

```
func (tx *Tx) Prepare(query string) (*Stmt, error)
```

功能说明

准备语句。创建一个准备好的语句，返回的 Stmt 在事务提交或回滚后不能再使用。

参数说明

query：要准备的 sql 语句。

返回值

*Stmt：准备好的语句句柄；

error：错误信息，若执行成功，则值为 nil。

示例：

```
stmt, err := tx.Prepare("select * from TEST")
```

5.Stmt

函数原型

```
func (tx *Tx) Stmt(stmt *Stmt) *Stmt
```

功能说明

使用已存在的 Stmt 生成当前事务专用的 Stmt。

参数说明

stmt：已存在的语句句柄。

返回值

*Stmt：当前事务专用的语句句柄。

示例：

```
dbStmt := db.Prepare("select * from TEST")
……
tx, err := db.Begin()
……
txStmt := tx.Stmt(dbStmt)
```

6.Commit

函数原型

```
func (tx *Tx) Commit() error
```

功能说明

提交事务。

参数说明

无。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := tx.Commit()
```

7.Rollback

函数原型

```
func (tx *Tx) Rollback() error
```

功能说明

回滚事务。

参数说明

无。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := tx.Rollback()
```

## 11.5 扩展对象

类似于 database/sql 中的 NullString，本节所有的自定义扩展对象都含有一个名为 Valid 的字段，用于标识该对象在数据库中的值是否为 NULL。如果 Valid 为 false，则表示该对象在数据库中为 NULL；反之不为 NULL。

Valid 示例：

```
var clob dm.DmClob

err := db.QueryRow(“SELECT content FROM foo”).Scan(&clob)

...

if clob.Valid {

// clob不为NULL的处理

} else {

// clob为NULL的处理

}
```

### 11.5.1 DmBlob

达梦数据库字节大字段

DmBlob 的相关方法介绍如下：

  1. NewBlob



函数原型

```
func NewBlob(value []byte) *DmBlob
```

功能说明

构造 DmBlob 对象。

参数说明

value：DmBlob 对象存储的字节数组。

返回值

*DB：DmBlob 对象指针；

示例：

```
blob, err := dm.NewBlob([]byte{0x00, 0x01, 0x02})
```

2.Read

函数原型

```
func (blob *DmBlob) Read(dest []byte) (int, error)
```

功能说明

读取 DmBlob 对象存储的字节数组。

参数说明

dest：读取的字节存放到 dest 中。

返回值

int：实际读取字节数；

error：错误信息，若执行成功，则值为 nil。

示例：

```
b := make([]byte, 100)

n, err := blob.Read(b)
```

3.ReadAt

函数原型

```
func (blob *DmBlob) ReadAt(pos int, dest []byte) (int, error)
```

功能说明

从指定偏移读取 DmBlob 对象存储的字节数组。

参数说明

pos：偏移，范围为 1~Blob 对象长度；

dest：读取的字节存放到 dest 中。

返回值

int：实际读取字节数；

error：错误信息，若执行成功，则值为 nil。

示例：

```
b := make([]byte, 100)

n, err := blob.ReadAt(1,b)
```

4.Write

函数原型

```
func (blob *DmBlob) Write(pos int, src []byte) (int, error)
```

功能说明

从指定偏移写字节数组到 DmBlob 对象。

参数说明

pos：偏移，范围为 1~Blob 对象长度；

src：要写入的字节数组。

返回值

int：实际写入字节数；

error：错误信息，若执行成功，则值为 nil。

示例：

```
b := []byte{0x00, 0x01, 0x02}

n, err := blob.Write(1,b)
```

5.GetLength

函数原型

```
func (blob *DmBlob) GetLength() (int64, error)
```

功能说明

获取 DmBlob 对象长度。

参数说明

返回值

int64：DmBlob 对象长度；

error：错误信息，若执行成功，则值为 nil。

示例：

```
n, err := blob.GetLength()
```

6.Truncate

函数原型

```
func (blob *DmBlob) Truncate(length int64) error
```

功能说明

从后往前删除 DmBlob 对象直到剩余 length 个字节。

参数说明

length：指定所剩的字节数。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := blob.Truncate(0)
```

### 11.5.2 DmClob

达梦数据库字符大字段

DmClob 的相关方法介绍如下：

  1. NewClob



函数原型

```
func NewClob(value string) *DmClob
```

功能说明

构造 DmClob 对象。

参数说明

value：DmClob 对象存储字符串。

返回值

*DB：DmClob 对象指针。

示例：

```
clob, err := dm.NewClob("hello world")
```

2.ReadString

函数原型

```
func (clob *DmClob) ReadString(pos int, length int) (string, error)
```

功能说明

读取 DmClob 对象存储的字符串。

参数说明

pos：偏移，范围为 1~Clob 对象长度；

length：读取的字符串长度。

返回值

string：实际读取字符串；

error：错误信息，若执行成功，则值为 nil。

示例：

```
s, err := clob.ReadString(1, 10)
```

3.WriteString

函数原型

```
func (clob *DmClob) WriteString(pos int, s string) (int, error)
```

功能说明

写入字符串到 DmClob 对象中。

参数说明

pos：偏移，范围为 1~Clob 对象长度；

s：写入的字符串。

返回值

int：实际写入字符串长度；

error：错误信息，若执行成功，则值为 nil。

示例：

```
n, err := clob.WriteString(1, "hello world")
```

4.GetLength

函数原型

```
func (clob *DmClob) GetLength() (int64, error)
```

功能说明

获取 DmClob 对象长度。

参数说明

无。

返回值

int64：DmClob 对象长度；

error：错误信息，若执行成功，则值为 nil。

示例：

```
n, err := clob.GetLength()
```

5.Truncate

函数原型

```
func (clob *DmClob) Truncate(length int64) error
```

功能说明

从后往前删除 DmClob 对象直到剩余 length 个字符。

参数****说明

length：指定所剩的字符串长度。

返回值

error：错误信息，若执行成功，则值为 nil。

示例：

```
err := clob.Truncate(0)
```

### 11.5.3 DmTimestamp

达梦数据库日期时间对象

DmTimestamp 的相关方法介绍如下：

  1. NewDmTimestampFromString



函数原型

```
func NewDmTimestampFromString(str string) (*DmTimestamp, error)
```

功能说明

由日期时间字符串构造 DmTimestamp 对象。

参数说明

str：时间日期字符串。

返回值

*DmTimestamp：DmTimestamp 对象指针；

error：错误信息，若执行成功，则值为 nil。

示例：

```
dmTimestamp, err := dm.NewDmTimestampFromString("2020-01-01 11:22:33")
```

2.NewDmTimestampFromTime

函数原型

```
func NewDmTimestampFromTime(time time.Time) *DmTimestamp
```

功能说明

由 go 原生数据类型 time.Time 构造 DmTimestamp 对象。

参数说明

time：go 原生数据类型 time.Time。

返回值

*DmTimestamp：DmTimestamp 对象指针。

示例：

```
dmTimestamp, err := dm.NewDmTimestampFromTime(time.Now())
```

3.ToTime

函数原型

```
func (dmTimestamp *DmTimestamp) ToTime() time.Time
```

功能说明

将 DmTimestamp 对象转换为 go 原生数据类型 time.Time。

参数说明

无。

返回值

time.Time：go 原生数据类型 time.Time。

示例：

```
t := dmTimestamp.ToTime()
```

### 11.5.4 DmDecimal

达梦数据库大数字对象

DmDecimal 的相关方法介绍如下：

  1. NewDecimalFromBigInt



函数原型

```
func NewDecimalFromBigInt(bigInt *big.Int) (*DmDecimal, error)
```

功能说明

由 big.Int 对象构造 DmDecimal 对象。

参数说明

bigInt：big.Int 对象。

返回值

*DmDecimal：DmDecimal 对象指针；

error：错误信息，若执行成功，则值为 nil。

示例：

```
dmDecimal, err := dm.NewDecimalFromBigInt(big.NewInt(1234567890))
```

2.NewDecimalFromBigFloat

函数原型

```
func NewDecimalFromBigFloat(bigFloat *big.Float) (*DmDecimal, error)
```

功能说明

由 big.Float 对象构造 DmDecimal 对象。

参数说明

bigFloat：big.Float 对象。

返回值

*DmDecimal：DmDecimal 对象指针。

示例：

```
dmDecimal, err := dm.NewDecimalFromBigFloat(big.NewFloat(12345.67890))
```

3.ToBigInt

函数原型

```
func (decimal DmDecimal) ToBigInt() *big.Int
```

功能说明

将 DmDecimal 对象转换为 big.Int 对象。

参数说明

无。

返回值

*big.Int：big.Int 对象指针。

示例：

```
bigInt := decimal.ToBigInt()
```

4.ToBigFloat

函数原型

```
func (decimal DmDecimal) ToBigFloat() *big.Float
```

功能说明

将 DmDecimal 对象转换为 big.Float 对象。

参数说明

无。

返回值

*big.Float：big.Float 对象指针。

示例：

```
bigFloat := decimal.ToBigFloat()
```

### 11.5.5 DmIntervalYM

达梦数据库年月间隔对象。

DmIntervalYM 的相关方法介绍如下：

  1. NewDmIntervalYMByString



函数原型

```
func NewDmIntervalYMByString(str string) (*DmIntervalYM, error)
```

功能说明

由年月间隔字符串构造 DmIntervalYM 对象。

参数说明

str：年月间隔字符串。

返回值

*DmIntervalYM：DmIntervalYM 对象指针；

error：错误信息，若执行成功，则值为 nil。

示例：

```
dmIntervalYM, err := dm.NewDmIntervalYMByString("INTERVAL '10-11' YEAR TO
MONTH")
```

### 11.5.6 DmIntervalDT

达梦数据库日时间间隔对象。

DmIntervalDT 的相关方法介绍如下：

  1. NewDmIntervalDTByString



函数原型

```
func NewDmIntervalDTByString(str string) (*DmIntervalDT, error)
```

功能说明

由日时间间隔字符串构造 DmIntervalDT 对象。

参数说明

str：日时间间隔字符串。

返回值

*DmIntervalDT：DmIntervalDT 对象指针；

error：错误信息，若执行成功，则值为 nil。

示例：

```
dmIntervalDT, err := dm.NewDmIntervalDTByString("INTERVAL '102:03:04.5678' DAY TO SECOND")
```

### 11.5.7 DmArray

达梦数据库数组对象。

DmArray 的相关方法介绍如下：

  1. NewDmArray



函数原型

```
func NewDmArray(typeName string, elements []interface{}) *DmArray
```

功能说明

构造 DmArray 对象。

参数说明

typeName：数组元素的类型；

elements：数组元素数组。

返回值

*DmArray：DmArray 对象指针。

示例：

```
dmArray := dm.NewDmArray("INT", []interface{1,2,3})
```

2.GetBaseTypeName

函数原型

```
func (dmArray *DmArray) GetBaseTypeName() (string, error)
```

功能说明

获取 DmArray 对象的基本类型名。

参数说明

无。

返回值

string：基本类型名；

error：错误信息，若执行成功，则值为 nil。

示例：

```
typeName, err := dmArray.GetBaseTypeName(
```

)

3.GetObjArray

函数原型

```
func (dmArray *DmArray) GetObjArray(index int64, count int) (interface{},
error)
```

功能说明

获取 DmArray 对象存储的 Object 数组。

参数说明

index：数组开始索引；

count：数组个数。

返回值

interface{}：Object 数组；

error：错误信息，若执行成功，则值为 nil。

4.GetIntArray

函数原型

```
func (dmArray *DmArray) GetIntArray(index int64, count int) ([]int, error)
```

功能说明

获取 DmArray 对象存储的 int 数组。

参数说明

index：数组开始索引；

count：数组个数。

返回值

[]int：int 数组；

error：错误信息，若执行成功，则值为 nil。

5.GetInt16Array

函数原型

```
func (dmArray *DmArray) GetInt16Array(index int64, count int) ([]int16, error)
```

功能说明

获取 DmArray 对象存储的 int16 数组。

参数说明

index：数组开始索引；

count：数组个数。

返回值

[]int16：int16 数组；

error：错误信息，若执行成功，则值为 nil。

6.GetInt64Array

函数原型

```
func (dmArray *DmArray) GetInt64Array(index int64, count int) ([]int64, error)
```

功能说明

获取 DmArray 对象存储的 int64 数组。

参数说明

index：数组开始索引；

count：数组个数。

返回值

[]int64：int64 数组；

error：错误信息，若执行成功，则值为 nil。

7.GetFloatArray

函数原型

```
func (dmArray *DmArray) GetFloatArray(index int64, count int) ([]float32,
error)
```

功能说明

获取 DmArray 对象存储的 float32 数组。

参数说明

index：数组开始索引；

count：数组个数。

返回值

[]float32：float32 数组；

error：错误信息，若执行成功，则值为 nil。

8.GetDoubleArray

函数原型

```
func (dmArray *DmArray) GetDoubleArray(index int64, count int) ([]float64, error)
```

功能说明

获取 DmArray 对象存储的 float64 数组。

参数说明

index：数组开始索引；

count：数组个数。

返回值

[]float64：float64 数组；

error：错误信息，若执行成功，则值为 nil。

## 11.6 批量执行

Go 语言标准库 database/sql 没有提供批量执行的接口，DM 数据库驱动通过执行参数的类型判断是否批量执行。下面用一个例子展示如何批量执行：

```
// 方便起见，忽略了所有错误返回
db, _ := sql.Open("dm", "dm://SYSDBA:sysDBA*00@localhost:5236")
db.Exec("DROP TABLE IF EXISTS TEST")
db.Exec("CREATE TABLE TEST(C1 INT,C2 NUMBER,C3 VARCHAR)")
stmt, _ := db.Prepare("INSERT INTO test VALUES (?,?,?)")
// 批量数据必须为interface二维Slice，第一维是要插入的行数据，第二维是每一行的列数据
var batchArg = [][]interface{}{{1,1.1,"first"},{2,2.2,"second"},{3,3.3,"third"}}
// Exec执行时，除最后一个参数外，其他参数必须为nil
stmt.Exec(nil, nil, batchArg)
```

除了 func (s *Stmt) Exec 外，如下方法都可以批量执行：

func (db *DB) Exec；

func (db *DB) ExecContext；

func (c *Conn) ExecContext；

func (s *Stmt) Exec；

func (s *Stmt) ExecContext；

func (tx *Tx) Exec；

func (tx *Tx) ExecContext。

## 11.7 事务及隔离级说明

在 Go 中，有两种方式开启事务：

```
db, err := sql.Open(...)
// 第一种，调用DB.Begin或DB.BeginTx方法
tx1, err := db.Begin(); // 使用默认的隔离级read committed和非只读配置
tx2, err := db.BeginTx(ctx, txOptions); // 使用指定的隔离级和是否只读配置
// 第二种，调用Conn.BeginTx方法
conn, err := db.Conn(ctx)
tx3, err := conn.BeginTx(ctx, txOptions); //使用指定的隔离级和是否只读配置
```

txOptions 对应的结构为 database/sql 包中下述内容：

```
type TxOptions struct {
Isolation IsolationLevel
ReadOny   bool
}
```

IsolationLevel 有以下取值：

sql.LevelDefault；

sql.LevelReadUnCommitted；

sql.LevelReadCommitted；

sql.LevelWriteCommitted；

sql.LevelRepeatableRead；

sql.LevelSnapshot；

sql.LevelSerializable；

sql.LevelLinearizable。

达梦 Go 驱动包仅支持部分的隔离级，对应关系如下：

sql.LevelDefault 和 sql.LevelReadCommitted 对应达梦的 READ COMMITTED。

sql.LevelReadUncommitted 对应达梦的 READ UNCOMMITTED。

sql.LevelSerializable 对应达梦的 SERIALIZABLE。

sql.LevelRepeatableRead 在兼容 mysql 模式下对应达梦的 READ COMMITTED，其他模式下报错。

除上以外的所有 IsolationLevel 都将报错。

## 11.8 ORM 方言包

目前 GO 语言主流的 ORM 框架为 GORM。GORM 又分为 V1：github.com/jinzhu/gorm 和 V2：gorm.io/gorm 两个版本。两个版本的 GORM 互相不兼容，为此达梦数据库提供了 2 套方言包分别适配。

方言包是位于达梦安装目录/drivers/go 目录下的 gorm_v1_dialect.zip 和 gorm_v2_dialect.zip。解压后用户需自行将其添加到包依赖路径中，默认包名为 dm。DM ORM 方言包需要依赖 dm 驱动包。如果方言包中引用 DM 驱动包的路径不是“dm”，那么需要在方言包的 dialect_dm.go/dm.go 文件中重新调整引用 DM 驱动包的实际路径。

由于 GORM V1 自身对标识符是否加双引号策略不一，所以使用 GORM V1 时，仅推荐使用 INI 参数 CASE_SENSITIVE=N 的数据库。

## 11.9 基本示例

例 1 下面用一个具体的编程实例来展示利用 Go 驱动程序进行数据库操作。

```
//该例程实现插入数据，修改数据，删除数据，数据查询等基本操作。
package main
// 引入相关包
import (
	"database/sql"
	"dm"
	"fmt"
	"io/ioutil"
	"time"
)

var db *sql.DB
var err error

func main() {
	driverName := "dm"
	dataSourceName := "dm://SYSDBA:sysDBA*00@localhost:5236"

	if db, err = connect(driverName, dataSourceName); err != nil {
		fmt.Println(err)
		return
	}
	if err = insertTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = updateTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = queryTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = deleteTable(); err != nil {
		fmt.Println(err)
		return
	}
	if err = disconnect(); err != nil {
		fmt.Println(err)
		return
	}
}

//创建数据库连接 
func connect(driverName string, dataSourceName string) (*sql.DB, error) {
	var db *sql.DB
	var err error
	if db, err = sql.Open(driverName, dataSourceName); err != nil {
		return nil, err
	}
	if err = db.Ping(); err != nil {
		return nil, err
	}
	fmt.Printf("connect to \"%s\" succeed.\n", dataSourceName)
	return db, nil
}

//往产品信息表插入数据
func insertTable() error {
	var inFileName = "c:\\三国演义.jpg"
	var sql = `INSERT INTO production.product(name,author,publisher,publishtime, 
				product_subcategoryid,productno,satetystocklevel,originalprice,nowprice,discount, 
                description,photo,type,papertotal,wordtotal,sellstarttime,sellendtime) 
                VALUES(:1,:2,:3,:4,:5,:6,:7,:8,:9,:10,:11,:12,:13,:14,:15,:16,:17);`
	data, err := ioutil.ReadFile(inFileName)
	if err != nil {
		return err
	}
	t1, _ := time.Parse("2006-Jan-02", "2005-Apr-01")
	t2, _ := time.Parse("2006-Jan-02", "2006-Mar-20")
	t3, _ := time.Parse("2006-Jan-02", "1900-Jan-01")
	_, err = db.Exec(sql, "三国演义", "罗贯中", "中华书局", t1, 4, "9787101046121", 10, 19.0000, 15.2000, 8.0,
		"《三国演义》是中国第一部长篇章回体小说，中国小说由短篇发展至长篇的原因与说书有关。",
		data, "25", 943, 93000, t2, t3)
	if err != nil {
		return err
	}
	fmt.Println("insertTable succeed")
	return nil
}

//修改产品信息表数据
func updateTable() error {
	var sql = "UPDATE production.product SET name = :name WHERE productid = 11;"
	if _, err := db.Exec(sql, "三国演义（上）"); err != nil {
		return err
	}
	fmt.Println("updateTable succeed")
	return nil
}

//查询产品信息表
func queryTable() error {
	var productid int
	var name string
	var author string
	var description dm.DmClob
	var photo dm.DmBlob
	var sql = "SELECT productid,name,author,description,photo FROM production.product WHERE productid=11"
	rows, err := db.Query(sql)
	if err != nil {
		return err
	}
	defer rows.Close()

	fmt.Println("queryTable results:")
	for rows.Next() {
		if err = rows.Scan(&productid, &name, &author, &description, &photo); err != nil {
			return err
		}
		blobLen, _ := photo.GetLength()
		fmt.Printf("%v	%v	%v	%v	%v\n", productid, name, author, description, blobLen)
	}
	return nil
}

//删除产品信息表数据 
func deleteTable() error {
	var sql = "DELETE FROM production.product WHERE productid = 11;"
	if _, err := db.Exec(sql); err != nil {
		return err
	}
	fmt.Println("deleteTable succeed")
	return nil
}

//关闭数据库连接
func disconnect() error {
	if err := db.Close(); err != nil {
		fmt.Printf("db close failed: %s.\n", err)
		return err
	}
	fmt.Println("disconnect succeed")
	return nil
}
```

例 2 展示 GORM V1 版本方言包用法。

```
package main
import (
	_ "dm" // DM驱动包路径
	"github.com/jinzhu/gorm"
)
type Product struct {
	gorm.Model
	Code string
	Price uint
}
func main() {
	db, err := gorm.Open("dm", "dm://SYSDBA:sysDBA*00@192.168.100.165:8989")
	if err != nil {
		panic("failed to connect database")
	}
	defer db.Close()

	// Migrate the schema
	db.AutoMigrate(&Product{})

	// 创建
	db.Create(&Product{Code: "L1212", Price: 1000})

	// 读取
	var product Product
	db.First(&product, 1) // 查询id为1的product
	db.First(&product, "code = ?", "L1212") // 查询code为l1212的product

	// 更新 - 更新product的price为2000
	db.Model(&product).Update("Price", 2000)

	// 删除 - 删除product
	db.Delete(&product)
}
```

例 3 展示 GORM V2 版本方言包用法。

```
package main
import (
	"dm" // DM驱动包路径
	"gorm.io/gorm"
)

type Product struct {
	gorm.Model
	Code  string
	Price uint
}

func main() {
	db, err := gorm.Open(dm.Open("dm://SYSDBA:sysDBA*00@192.168.100.165:8989"), &gorm.Config{})
	if err != nil {
		panic("failed to connect database")
	}

	// 迁移 schema
	db.AutoMigrate(&Product{})

	// Create
	db.Create(&Product{Code: "D42", Price: 100})

	// Read
	var product Product
	db.First(&product, 1) // 根据整型主键查找
	db.First(&product, "code = ?", "D42") // 查找 code 字段值为 D42 的记录

	// Update - 将 product 的 price 更新为 200
	db.Model(&product).Update("Price", 200)
	// Update - 更新多个字段
	db.Model(&product).Updates(Product{Price: 200, Code: "F42"}) // 仅更新非零值字段
	db.Model(&product).Updates(map[string]interface{}{"Price": 200, "Code": "F42"})

	// Delete - 删除 product
	db.Delete(&product, 1)
}
```



# DM R2DBC编程指南

## 14.1 DM R2DBC 介绍

因为使用 JDBC 来操作关系型数据库有一定局限性，JDBC 是一个完全阻塞的 API，虽然使用线程池来弥补阻塞行为，但是效果有限。

基于上述原因，SPRING 官方提出了 R2DBC。R2DBC（Reactive Relational Database Connectivity）是一项 API 规范，它声明了一个反应式 API，该方法将由数据库厂商实现来访问其关系数据库。反应式是指对突如其来的事件做出反应，使操作完成或数据可用，而不是直接阻塞。反应式 API 可以通过非阻塞的方式，提升线程的可用性，提升系统的吞吐量，且响应及时。

DM R2DBC 是使用 Java 语言编写，在 JDBC 的基础上进行的改进。它符合 SPRING 制定的异步标准。

该规范还包含以下突出的功能：

  1. 集成 BLOB 和 CLOB 类型。DmBlob 和 DmClob 对 R2DBC LOB 类型的再次封装。
  2. 可扩展的事务定义。通过 Connection 对象中的 releaseSavepoint()、rollbackTranscation、 rollbackTransactionToSavepoint()进行设置。
  3. 普通语句和参数化语句（Prepared Statement）。全部由 DmStatement 实现。
  4. 支持存储过程/服务器侧 IN 和 OUT 参数绑定的执行函数。
  5. 批量操作。通过 DmBatch 中的 add()函数进行设置。
  6. 可分类的操作异常。通过 JDBC 抛出的异常，转换为 R2DBC 相对应的异常信息。
  7. 新的数据库链接 URL 方案。通过 R2DBC 官网规定 URL 串进行数据库连接。



## 14.2 DM R2DBC 编程步骤

利用 R2DBC 驱动程序进行编程的一般步骤为：

  1. 建立连接



获取 io.r2dbc.spi.Connection 对象。利用 ConnectionFactory 工厂来获取与数据库的连接。其中，io.r2dbc.spi.Connection 是 DM8 的父类接口，DmConnection 是对这个接口的具体实现。

  2. 创建 Statement 对象



创建 io.r2dbc.spi.Statement 对象。这点不同于 JDBC：JDBC 中分为 Statement 和 PreparedStatement 对象；而 R2DBC 中只有 Statement 对象，只不过 R2DBC 中的 Statement 对象包含了 JDBC 中的 Statement 和 PreparedStatement 对象。

  3. 数据操作



数据操作主要分为两个方面，一个是数据库的增加、删除和修改操作；另一个是查询操作，执行完查询之后，会得到一个 io.r2dbc.spi.Result 对象，可以操控该对象获取数据库中的每行数据（Row）和元数据（RowMetadata）。

  4. 释放资源



在操作完成之后，用户需要释放系统资源，主要是关闭连接。当然，这些动作也可以由 JDBC 驱动程序自动执行，但由于 Java 语言的特点，这个过程会比较慢，需要等到 Java 进行垃圾回收时进行，容易出现意想不到的问题。

## 14.3 DM R2DBC 特性

DM R2DBC 驱动程序依据 r2dbc-spi-1.0.0.RELEASE 版本实现。

实现了 1.0.0 发行规范所要求的下列接口和实体类：

```
io.r2dbc.spi.Assert
io.r2dbc.spi.Batch
io.r2dbc.spi.Blob
io.r2dbc.spi.Clob
io.r2dbc.spi.Closeable
io.r2dbc.spi.ColumnMetadata
io.r2dbc.spi.Connection
io.r2dbc.spi.ConnectionFactories
io.r2dbc.spi.ConnectionFactory
io.r2dbc.spi.ConnectionFactoryMetadata
io.r2dbc.spi.ConnectionFactoryOptions
io.r2dbc.spi.ConnectionFactoryProvider
io.r2dbc.spi.ConnectionMetadata
io.r2dbc.spi.ConnectionUrlParser
io.r2dbc.spi.ConstantPool
io.r2dbc.spi.DefaultLob
io.r2dbc.spi.IsolationLevel
io.r2dbc.spi.Lifecycle
io.r2dbc.spi.NonNullApi
io.r2dbc.spi.NoSuchOptionException
io.r2dbc.spi.Nullability
io.r2dbc.spi.Nullable
io.r2dbc.spi.Option
io.r2dbc.spi.OutParameterMetadata
io.r2dbc.spi.OutParameters
io.r2dbc.spi.OutParametersMetadata
io.r2dbc.spi.package-info
io.r2dbc.spi.Parameter
io.r2dbc.spi.Parameters
io.r2dbc.spi.R2dbcBadGrammarException
io.r2dbc.spi.R2dbcDataIntegrityViolationException
io.r2dbc.spi.R2dbcException
io.r2dbc.spi.R2dbcNonTransientException
io.r2dbc.spi.R2dbcNonTransientResourceException
io.r2dbc.spi.R2dbcPermissionDeniedException
io.r2dbc.spi.R2dbcRollbackException
io.r2dbc.spi.R2dbcTimeoutException
io.r2dbc.spi.R2dbcTransientException
io.r2dbc.spi.R2dbcTransientResourceException
io.r2dbc.spi.R2dbcType
io.r2dbc.spi.Readable
io.r2dbc.spi.ReadableMetadata
io.r2dbc.spi.Result
io.r2dbc.spi.Row
io.r2dbc.spi.RowMetadata
io.r2dbc.spi.Statement
io.r2dbc.spi.TransactionDefinition
io.r2dbc.spi.Type
io.r2dbc.spi.ValidationDepth
io.r2dbc.spi.Wrapped
```

## 14.4 DM R2DBC 扩展

为了满足 DM 的实际需求，对 R2DBC 功能进行了一定扩展。扩展了 DM 特有类型和读写分离集群下的错误信息。扩展内容和 JDBC 完全一样，具体请参考 4.4 DM JDBC 扩展。

## 14.5 DM R2DBC 提供的接口和对象

### 14.5.1 建立 R2DBC 连接

创建 R2DBC 连接分为两步：首先创建连接工厂，然后连接工厂使用 create()方法获取连接。

#### 14.5.1.1 创建连接工厂对象

创建连接工厂分为三种：一通过 JDBC 数据源创建；二通过 Properties 创建；三通过 R2DBC URL 创建。用户可以根据实际情况，选择其中一种即可。

##### 14.5.1.1.1 通过 JDBC 数据源创建

具体 DMdbDataSource 配置请参考 4.5.2 创建 JDBC 数据源。

例 下面是使用 DmdbDataSource 获取 R2DBC 连接工厂。

```
public static ConnectionFactory getConnectionFactory() throws SQLException {
  DmdbDataSource dataSource = new DmdbDataSource();
  dataSource.setURL("jdbc:dm://127.0.0.1");
  dataSource.setUser("SYSDBA");
  dataSource.setPassword("sysDBA*00");
  dataSource.setPort(5236);
  dataSource.setLoginTimeout(3000);

  ConnectionFactoryOptions options = ConnectionFactoryOptions.builder()
      .option(DmConnectionFactoryProvider.DATASOURCE, dataSource)
      .option(DmConnectionFactoryProvider.CONVERTS, new DefaultConverts())
      .build();
  return ConnectionFactories.find(options);
}
```

##### 14.5.1.1.2 通过 Properties 创建

具体 Properties 配置请参考 4.5.4 DM 扩展连接属性的使用。

例 下面是使用 Properties 对象获取 R2DBC 连接工厂。

```
public static ConnectionFactory getFactoryByProperties() throws SQLException {
  Properties properties = new Properties();
  ConnectionFactoryOptions options = builder()
      //原JDBC Properties方法，用以设置相关参数
      .option(DmConnectionFactoryProvider.PROPERTIES, properties)
      //原JDBC连接串相关参数后缀，必须以?开头
      .option(DmConnectionFactoryProvider.POSTFIX, "?encode=utf-8")
      .option(HOST, "127.0.0.1")
      .option(PORT, 5236)
      .option(USER, "SYSDBA")
      .option(PASSWORD, "sysDBA*00")
      .build();
  return ConnectionFactories.find(options);
}
```

##### 14.5.1.1.3 通过 R2DBC URL 创建

R2DBC URL 只支持在用户名和密码中使用英文字母和数字的组合，不支持参数的绑定。

URL 格式为“r2dbc:\< 数据库名称 >://\< 用户名 >:\< 密码 >@\<IP>:\<Port>”。

例 下面是使用 R2DBC URL 获取 R2DBC 连接工厂

```
public static ConnectionFactory getFactoryByUrl() {
    String url = "r2dbc:dm://SYSDBA:sysDBA*00@127.0.0.1:5236";
  return ConnectionFactories.get(url);
}
```

#### 14.5.1.2 获取连接

最终，连接工厂通过统一的 create()方法获取连接。

```
Flux.usingWhen(
   connectionFactory.create(),
      connection -> Flux.collect(null),
      Connection::close)
      .doOnNext(System.out::println)
      .blockLast(Duration.ofSeconds(10));
```

### 14.5.2 Statement 接口

Statement 接口是 R2DBC 的标准接口，定义了 DmStatement 对象中的相关函数，DmStatement 对象为 DM8 的具体实现。

R2DBC Statement 对象用于将 SQL 语句发送到数据库服务器。在 R2DBC 中有且仅有 Statement 一种语句对象，其底层会自动判断相关语句对数据库进行操作。

例 Statement 接口底层使用 DmStatement 对象。

```
public void createStmt() {
  ConnectionFactory factory = getFactoryByUrl();
  Connection conn = Mono.from(factory.create()).block(Duration.ofSeconds(60L));
  Statement statement = conn.createStatement("select * from test1 where id = 1");
}
```

### 14.5.3 DmStatement 对象

DmStatement 对象是 R2DBC Statement 接口的具体实现。

DmStatement(java.sql.Connection connection, String sql, Converts converts):初始化 DmStatement。

DmStatement（java.sql.Connection connection, String sql, Converts converts,   List\<String> parameterNames）: 初始化 DmStatement。

execute():执行 Statement 句柄。

bind(int index, Object value): 根据游标位置，对 SQL 语句绑定参数。

bind(String name, Object value):根据名称，对 SQL 语句绑定参数。

bindNull(String name, Class\<?> type):对 SQL 语句绑定特定类型的空参。

createResult（PreparedStatement stmt， ResultSet resultSet, int[] affectedRows）： 创建 R2DBC 结果集。

createResultProcess(CallableStatement stmt)：创建 R2DBC 的存储过程结果。

ReturnGeneratedValues（String...columns）：对插入 SQL，获取特定列结果数据。

### 14.5.4 DmConnection 对象

DmConnection 对象表示一个 DM 数据库打开的连接。

公共属性

beginTransaction():开始一个新的事务。

beginTransaction(TransactionDefinition definition):根据输入参数 definition 指定的隔离等级，开始一个新的事务。

close()：关闭连接对象，清理占用的资源。

commitTransaction()：提交当前事务。

createBatch():创建一个批量对象，此对象用来构建批量的 SQL 请求。

createSavepoint(String name): 创建一个基于当前 Statement 的保存点，并以 name 命名。

createStatement(String sql)：创建一个 Statement 语句，包含了请求的 SQL。

getMetadata()：返回当前数据库连接的元数据。

getTransactionIsolationLevel():获取当前数据库连接的隔离等级。

isAutoCommit()：返回当前数据库连接的事务是否为自动提交模式。

releaseSavePoint(String name):释放当前事务中该名称（name）的保存点。

rollbackTransaction():回滚当前事务。

rollbackTransactionToSavepoint(String name):回滚到当前事务的特定名称的保存点。

setAutoCommit(boolean autoCommit):根据布尔值设置当前数据库连接是否为自动提交事务。

setLockWaitTimeOut(Duration timeout)：配置要使用当前连接执行语句的锁获取超时。

setStatementTimeout(Duration timeout):配置要使用当前连接执行的 Statment 语句最大等待时间

setTransactionIsolationLevel(IsolationLevel isolationLevel):配置当前数据库连接的隔离等级。

validate(Validation depth):达梦数据库暂不支持。

### 14.5.5 DmBatch 对象

DmBatch 是 R2DBC 批量操作 SQL 语句的对象。不支持在 SQL 语句中使用动态绑定参数。

add(String sql):添加一条 sql 语句至当前 Batch 对象。

execute():执行 Batch 对象中所有的 Statement 句柄并返回所有结果。

例 批量绑定 SQL 语句，一条一条绑定。

```
public void createBatch() {
  ConnectionFactory factory = getFactoryByUrl();
  Connection conn = Mono.from(factory.create()).block(Duration.ofSeconds(60L));
 
  Publisher<? extends Result> execute = conn.createBatch()
      .add("select 1 from dual")
      .add("select 2 from dual")
      .add("select 3 from dual")
      .execute();
}
```

### 14.5.6 Blob 对象

Blob 是存储二进制大文件的对象。

discard():在未订阅流内容时，释放 Blob 持有的任何资源。

from(Publisher*\<*ByteBuffer> p):把有发布者类包装好的 ByteBuffer 对象转变为 Blob 对象。

stream():把 Blob 对象重新转变为由发布者类包装好的 ByteBuffer 对象。

### 14.5.7 Clob 对象

Clob 是存储 Char 类型大文件的对象。

discard():在未订阅流内容时，释放 Clob 持有的任何资源。

from(Publisher\<? extends CharSequence> p):把有发布者类包装好的 CharSequence 对象转变为 Clob 对象。

stream():把 Clob 对象重新转变为由发布者类包装好的 CharSequence 对象。

### 14.5.8 DmColumnMetaData 对象

继承 ReadableMetadata 的接口对象。表示查询返回的结果列的元数据。除 ReadableMetadata 之外的所有方法的实现。R2DBC 框架根据下述 get 函数，可以自动将数据库类型映射到 Java 类型，例如数据库中 Varchar 类型会转变为 Java 中的 String 类型。

getJavaType(): 获取该字段在 Java 中对应的类型名称（包含包名，类名）。

getName(): 获取该字段在数据库中的名称。

getNativeTypeMetadata(): 获取该字段在数据库中的元数据类型名称。

getNullability(): 获取该字段是否支持 Null 值。

getPreision(): 获取该字段的精确度。

getScale(): 获取该字段的刻度。

getType(): 获取该字段在 Java 中的类型名称（仅显示类名）。

### 14.5.9 DmConnectionFactory 对象

create():创建 R2DBC 对于数据库的连接对象（Connection）。

getMetadata():返回有关此 ConnectionFactory 适用产品的元数据信息。

### 14.5.10 DmConnectionFactoryMetadata 对象

getName():返回 ConnectionFactory 能够连接的产品名称。

### 14.5.11 DmConnectionFactoryProvider 对象

create（ConnectionFactoryOption connectionFactoryOptions）:通过上层解析的 Option 参数（Option 的使用请参考 [14.5.1.1 创建连接工厂](https://eco.dameng.com/document/dm/zh-cn/pm/r2dbc-rogramming-guide.html#14.5.1.1%20%E5%88%9B%E5%BB%BA%E8%BF%9E%E6%8E%A5%E5%B7%A5%E5%8E%82%E5%AF%B9%E8%B1%A1)，作用为传递数据库连接相关参数，例如用户名，密码，端口等等），创建一个新的连接工厂。

getDriver(): 返回使用的 Driver 名称。

Supports(ConnectionFactoryOptions connectionFactoryOptions)：判断是否支持构建连接工厂的参数。

### 14.5.12 DmConnectionMetadata 对象

getDatabaseProductName():获取数据库产品名称。

getDatabaseVersion():获取数据库产品的版本号。

### 14.5.13 DmR2dbcType 对象

继承 Type 类型，扩展时间 Interval 类型。

getJavaType():获取 Java 类型 Class。

getName()：获取类型的名称。

### 14.5.14 DmResult 对象

DmResult 对象是用来获取数据库返回的结果。

#### 14.5.14.1 DmResult

DmResult 用于接收数据库返回的数据并以 Java 的形式展现，无需手动初始化，R2DBC 会自动对其进行填充。

DmResult(Flux\<DmRow> rows, Mono\<RowMetadata> rowMetadata, int[]affectedRows) ：初始化 DmResult 对象。

#### 14.5.14.2 filter

filter(Predicate\<Result.Segment> filter):根据过滤条件，过滤结果集。

#### 14.5.14.3 flatMap

段映射是指把数据库读取的默认类型转换为别的类型。例如：Varchar 默认对应 Java 的 String 类型，于是通过 flatMap 把 String 类型再转换为 Char[]数组类型）。

flatMap(Function\<Result.Segment,? extends Publisher\<?Extends T>> mappingFunction):获取作为数据库查询结果的结果段映射。

#### 14.5.14.4 getRowsUpdated

getRowsUpdated():获取数据库更新的行数。

#### 14.5.14.5 map

map 的作用和 flatMap 类似（参考 14.5.14.3），但 map 转换后不会被 publisher(发布者类型)包裹。

map(BiFunction\<Row, RowMetadata, ? extends T> mappingFunction): 获取结果集中行数据结果的映射。

map（Function\<? super Readable, ? extends T> mappingFunction）:获取结果集中行数据/存储过程 Out 数据的映射。

### 14.5.15 DmRow 对象

实现了 Row、Result、RowSegment 接口。

DmRow(RowMetadata rowMetadata, Map\<Integer, Object> values, Converts converts)：初始化 DmRow 对象。

row(): 返回当前 DmRow 对象。

getMetadata(): 获取当前 Row 对象的元数据对象。

get(int index):根据游标获取当前行数据中特定位置的数据。

getConverts():获取当前 Row 对象的类型映射对象。

getRowMetadata():获取当前 Row 对象的元数据对象。

setRowMetadata(RowMetadata rowMetadata): 设置 Row 对象的元数据对象。

getValues():以 Map 形式获取 Row 对象的结果集。

setValues（Map\<Integer, Object> values）:以 Map 形式设置 Row 对象的结果集。

get(int index, Class\<T> type):以特定类型获取 Row 结果集中特定游标位置的数据。

get（String name):以名称获取 Row 结果集中的数据。

get(String name, Class\<T> type):以特定类型获取 Row 结果集中特定列名称的数据。

### 14.5.16 DmRowMetadata 对象

用于接收数据库 Result 中每行数据的元数据信息。

Of（Result result, Converts converts）：获取新的 DmRowMetadata 对象。

DmRowMetadata（List*\<*ColumnMetadata> columnMetaDatas）：根据列元数据信息，初 始化 DmRowMetadata 对象。

getColumnMetadata(int index)： 获取特定游标位置的列元数据信息。

getColumnMetadata(String name): 获取特定名称的列元数据信息。

contains(String columnName): 判断是否存在该名称的列数据。

getColumnMetadatas(): 获取列元数据信息列表。

## 14.6 基本示例

准备工作：相关 JAR 包在 DM8 数据库安装目录\source\drivers\r2dbc 中，其中包含 dm-r2dbc-1.0.0.jar、lib 文件夹和 readme.txt。

编程时须在项目中导入 dm-r2dbc-1.0.0.jar 和 lib 中相关依赖。

下面用一个具体的实例来展示如何利用 DM R2DBC 驱动程序进行编程并操作数据库。

该示例中使用到的表结构如下：

```
CREATE TABLE TEST (C INT);
CREATE TABLE TESTBLOB(C1 BLOB);
CREATE TABLE TEST_CALL200(C1 VARCHAR, C2 VARCHAR, C3 VARCHAR);
CREATE TABLE BUG_INFO(ID VARCHAR);
```

具体实例如下：

```
package io.r2dbc.jdbc;

import dm.jdbc.driver.DmdbArray;
import dm.jdbc.driver.DmdbDataSource;
import dm.jdbc.driver.DmdbRowId;
import dm.r2dbc.DmConnectionFactoryProvider;
import dm.r2dbc.convert.DefaultConverts;
import dm.r2dbc.utils.R2dbcUtils;
import dm.r2dbc.utils.StrUtil;
import io.r2dbc.spi.Blob;
import io.r2dbc.spi.Clob;
import io.r2dbc.spi.Connection;
import io.r2dbc.spi.Statement;
import io.r2dbc.spi.*;
import org.reactivestreams.Publisher;
import reactor.core.publisher.Flux;
import reactor.core.publisher.Mono;

import java.nio.ByteBuffer;
import java.sql.*;
import java.time.Duration;
import java.util.Properties;

import static io.r2dbc.spi.ConnectionFactoryOptions.*;

public class Main
{
    public static void main(String[] args) throws SQLException
    {
        // query方法
        query("select c from test");
        // rollback方法
        rollback();
        // savePoint方法
        savePoint("hello world");
        // procedure方法
        procedure();
        // batch 方法
        batch();
    }
    public static void query1(String sql)
    {
        if (StrUtil.isEmpty(sql))
        {
            throw new NullPointerException("sql cannot be blank!");
        }
        ConnectionFactory connectionFactory = getFactoryByUrl();
        Flux.usingWhen(connectionFactory.create(),
                connection -> Flux.from(
                        connection.createStatement(sql).bind(0, 100).bind(1, 101).bind(2, 102).execute())
                        .flatMap(result -> result.map(((row, rowMetadata) -> {
                            return row.get(0) + " " + rowMetadata.getColumnMetadata(0).getJavaType();
                        }))),
                Connection::close).doOnNext(System.out::println).doOnError(Throwable::printStackTrace)
                .blockLast(Duration.ofSeconds(60));
    }
    public static void procedure()
    {
        ConnectionFactory connectionFactory = getFactoryByProperties();
        Connection connection = Mono.from(connectionFactory.create()).doOnError(Throwable::printStackTrace)
                .block(Duration.ofSeconds(60L));
        Statement stmt1 = connection
                .createStatement("create or replace procedure pro_testCall(bb in out blob)" + " as" + " begin"
                        + " insert into SYSDBA.testBlob values(bb);"
                        + " select c1 into bb from SYSDBA.testBlob limit 1,1;" + " end;");
        Statement stmt2 = connection.createStatement("{call pro_testCall(:bb)}");

        ByteBuffer by = ByteBuffer.wrap(new byte[] {1, 2, 3, 4, 5});
        Flux.from(stmt1.execute()).thenMany(Flux
                .from(stmt2.bind("bb", Parameters.inOut(R2dbcType.BLOB, Blob.from(Mono.just(by)))).execute())
                .flatMap(result -> result.map(((row, rowMetadata) -> {
                    byte[] bytes = R2dbcUtils.blobToByteArray(row.get(0, Blob.class));
                    for (byte b : bytes)
                    {
                        System.out.println(b);
                    }
                    return "";
                }))).doOnNext(System.out::println)).thenMany(Flux.from(connection.close()))
                .blockLast(Duration.ofSeconds(60L));
    }
    public static void savePoint(String sql) throws SQLException
    {
        if (StrUtil.isEmpty(sql))
        {
            throw new NullPointerException("sql cannot be blank!");
        }
        ConnectionFactory connectionFactory = getFactoryByDataSource();
        Connection connection = Mono.from(connectionFactory.create()).block(Duration.ofSeconds(60L));
        Mono.from(connection.setAutoCommit(false)).block(Duration.ofSeconds(60L));
        Statement stmt1 = connection
                .createStatement("UPDATE SYSDBA.TEST_CALL200 set c1= 'fw1-1' where c1 = 'fwu-1'");
        Statement stmt2 = connection
                .createStatement("UPDATE SYSDBA.TEST_CALL200 set c2= '2022-9-20' where c1 = 'fw1-1'");
        Mono.from(stmt1.execute()).then(Mono.from(connection.createSavepoint("sp1")))
                .then(Mono.from(stmt2.execute()))
                .then(Mono.from(connection.rollbackTransactionToSavepoint("sp1")))
                .then(Mono.from(connection.commitTransaction())).then(Mono.from(connection.close()))
                .block(Duration.ofSeconds(60L));
    }
    public static void rollback() throws SQLException
    {
        ConnectionFactory connectionFactory = getFactoryByDataSource();
        Connection connection = Mono.from(connectionFactory.create()).block(Duration.ofSeconds(60L));
      Mono.from(connection.setAutoCommit(false)).block(Duration.ofSeconds(60L));
        Statement statement = connection
                .createStatement("UPDATE SYSDBA.TEST_CALL200 set c1= 'fw1-3' where c1 = 'fwu-1'");
        Flux.from(statement.execute()).flatMap(result -> Flux.from(result.getRowsUpdated()))
                .map(Math::toIntExact).collectList().block(Duration.ofSeconds(60L));
        Publisher<Void> rollbackPb = connection.rollbackTransaction();
        Mono.from(rollbackPb).block(Duration.ofSeconds(60L));
        Mono.from(connection.close()).block(Duration.ofSeconds(60L));
    }
    public static void batch()
    {
        ConnectionFactory connectionFactory = getFactoryByUrl();
        Flux.usingWhen(connectionFactory.create(),
                connection -> Flux
                        .from(connection.createBatch().add("SELECT * FROM SYSDBA.BUG_INFO WHERE ID = 520000")
                                .add("SELECT * FROM SYSDBA.BUG_INFO WHERE ID = 520001")
                                .add("SELECT * FROM SYSDBA.TEST_CALL200 LIMIT 1")
                                .add("UPDATE SYSDBA.TEST_CALL200 SET C3 = 'LOLO' WHERE C1 = 'fwu-7'")
                                .add("SELECT C2 FROM SYSDBA.TEST_CALL200 WHERE C1 = 'fwu-7'").execute())
                        .flatMap(result -> result.map(((row, rowMetadata) -> {
                            return row.get(0);
                        }))),
                Connection::close).doOnNext(System.out::println).doOnError(Throwable::printStackTrace)
                .blockLast(Duration.ofSeconds(60));
    }
    public static void query(String sql)
    {
        if (StrUtil.isEmpty(sql))
        {
            throw new NullPointerException("sql cannot be blank!");
        }
        ConnectionFactory connectionFactory = getFactoryByUrl();
        Flux.usingWhen(connectionFactory.create(), connection -> Flux.from(connection.createStatement(sql)
.execute()).flatMap(result -> result.map(((row, rowMetadata) -> {
                    return row.get(0);
                }))), Connection::close).doOnNext(System.out::println).doOnError(Throwable::printStackTrace)
                .blockLast(Duration.ofSeconds(60));
    }
    public static ConnectionFactory getFactoryByProperties()
    {
        Properties properties = new Properties();
        ConnectionFactoryOptions options = ConnectionFactoryOptions.builder()
                .option(DmConnectionFactoryProvider.PROPERTIES, properties).option(HOST, "127.0.0.1")
                .option(PORT, 5236).option(USER, "SYSDBA").option(PASSWORD, "sysDBA*00").build();
        return ConnectionFactories.get(options);
    }
    public static ConnectionFactory getFactoryByDataSource() throws SQLException
    {
        DmdbDataSource dataSource = new DmdbDataSource();
        dataSource.setURL("jdbc:dm://127.0.0.1");
        dataSource.setUser("SYSDBA");
        dataSource.setPassword("sysDBA*00");
        dataSource.setPort(5236);
        dataSource.setLoginTimeout(3000);
        ConnectionFactoryOptions options = ConnectionFactoryOptions.builder()
                .option(DmConnectionFactoryProvider.DATASOURCE, dataSource)
                .option(DmConnectionFactoryProvider.CONVERTS, new DefaultConverts()).build();
        return ConnectionFactories.get(options);
    }
    public static ConnectionFactory getFactoryByUrl()
    {
        String url = "r2dbc:dm://SYSDBA:sysDBA*00@127.0.0.1:5236";
        return ConnectionFactories.get(url);
    }
}
```

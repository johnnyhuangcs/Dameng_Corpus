

# MyCat 框架

## 一、前言

MyCat 是一个开源的、面向企业应用开发的大数据库集群；一个支持事务、ACID 、可以替代 MySQL 的加强版数据库；一个新颖的数据库中间件产品。MyCat 天生与 MySQL 完美结合，支持前端作为 MySQL 通用代理，后端 JDBC 方式支持 Oracle 等数据库。此处我们将 MyCat 与 DM 数据库进行适配使用，利用 MyCat 对 DM 数据库进行分库分表。

## 二、数据库连接

### 2.1 准备工作

本次 window 环境下使用的 MyCat 版本为 [Mycat-server-1.6.5-DEV-20161231120132-win.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240419153845P5L3R2FP32ZSIMAKF5) ，linux 环境下使用 MyCat 版本为 [Mycat-server-1.6.5-DEV-20161231120132-linux.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202404191539021EP1C8W21L4I50IU13) 。

MySQL 连接器版本为 [mysql-connector-j-8.2.0.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240419153909ZV57A61FORVZFW4M24) 。

MyCat 安装包官方下载地址为 [http://www.mycat.org.cn/](http://www.mycat.org.cn/) 。

达梦数据库安装请参考[数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.2 DM 数据库连接 MyCat 配置

  1. 达梦数据库端准备。



使用 MyCat 分库分表功能，先在数据库端建立两个实例 5236 ， 5237 ，并分别在实例中创建好用户，分别在不同用户对应的模式下创建表结构完全一致的表。语句如下所示：

```
/*在 5236 ， 5237 实例中分别以 SYSDBA/***** 用户分别登录达梦数据库。
在实例 5236 中创建用户 mydb1 ，此时会自动生成与用户名同名的模式，并在 mydb1 模式下创建表。*/
create user "mydb1" identified by "a123456789";
grant VTI to mydb1; 
create table mydb1.PRODUCT_CATEGORY( ID INT PRIMARY KEY, NAME VARCHAR(50) NOT NULL) ;
/*在实例5237中创建用户mydb2 ，此时会自动生成与用户名同名的模式，并在mydb2模式下创建表。*/
create user "mydb2" identified by "a123456789";
grant VTI to mydb2; 
create table mydb2.PRODUCT_CATEGORY( ID INT PRIMARY KEY, NAME VARCHAR(50) NOT NULL) ;
```

创建完成后如下图所示：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419154517QD95NT7XVDFCS2IHRD)

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419154524FKW7T55R56XILBRK7U)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps5.jpg)

  2. MyCat 配置：



（1）将 DM 数据库的 JDBC 驱动包（在’\DM 安装目录\drivers\jdbc\’中）复制到 MyCat 安装路径下 lib 文件夹内。

（2）配置 mycat/conf/server.xml 配置文件。

主要是配置 MyCat 服务启动后，对外提供数据库访问的用户信息，如下图所示：

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404191546314US5BJ15SJ5CZY4NU6)

```
    <user name="root"><!--此处配置为连接 MyCat 的用户名密码-->
        <property name="password">******</property>
        <property name="schemas">PRODUCTION</property>
        <!-- 表级 DML 权限设置 -->
        <!-- 
        <privileges check="false">
            <schema name="TESTDB" dml="0110" >
                <table name="tb01" dml="0000"></table>
                <table name="tb02" dml="1111"></table>
            </schema>
        </privileges>
            -->
    </user>
    <!-- 定于 MyCat 用户，只读：user/user -->
    <user name="user">
        <property name="password">******</property>
        <property name="schemas">PRODUCTION</property>
        <property name="readOnly">true</property>
    </user>
```

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps6.jpg)

（3）配置 /mycat/conf/schema.xml 配置文件。

```
<?xml version="1.0"?>
<!DOCTYPE mycat:schema SYSTEM "schema.dtd">
<mycat:schema xmlns:mycat="http://io.mycat/">
    <!-- 定义一个 MyCat 的模式，此处定义了一个逻辑数据库名称 PRODUCTION -->
    <!-- “checkSQLschema”：描述的是当前的连接是否需要检测数据库的模式 -->
    <!-- “sqlMaxLimit”：表示返回的最大的数据量的行数 -->
    <!-- “dataNode="dn1,dn2"”：该操作使用的数据节点是 dn1 的逻辑名称,这里 dn1 dn2 分别对应刚刚数据库里的两个模式 -->
    <!--rule="mod-long"  这里指的是数据库分库分表的柜子,可以在 rule.xml 文件里配置规则,此处 mod-long 指的是,交替插入 dn1 dn2 等各个节点-->
    <schema name="PRODUCTION" checkSQLschema="true" sqlMaxLimit="100" >
        <table name="PRODUCT_CATEGORY" primaryKey="id" autoIncrement="true" dataNode="dn1,dn2" rule="mod-long"/>
    </schema>
    <!-- 定义个数据的操作节点，以后这个节点会进行一些库表分离使用 -->
    <!-- “dataHost="localhost1"”：定义数据节点的逻辑名称 -->
    <!-- “database="mydb1"”：定义数据节点要使用的数据库名称,针对达梦数据库来说,对应的是模式名。 -->
    <dataNode name="dn1" dataHost="localhost1" database="mydb1" />
    <dataNode name="dn2" dataHost="localhost2" database="mydb2" />
    <!-- 定义数据节点，包括各种逻辑项的配置 -->
    <dataHost name="localhost1" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="oracle" dbDriver="jdbc" switchType="1"  slaveThreshold="100">
        <!-- 配置真实数据库与 MyCat 的心跳 -->
        <heartbeat>select user()</heartbeat>
        <!-- 配置真实的 MySQL 的连接信息 这里由于我们借用 Oracle 连接通道,通过达梦配置对 Oracle 数据库的兼容参数-->
        <writeHost host="hostM1" url="jdbc:dm://localhost:5236?comOra=true" user="MYDB1" password="a123456789"></writeHost>
    </dataHost>
    <dataHost name="localhost2" maxCon="1000" minCon="10" balance="0" writeType="0" dbType="oracle" dbDriver="jdbc" switchType="1"  slaveThreshold="100">
        <!-- 配置真实 MySQL 与 MyCat 的心跳 -->
        <heartbeat>select user()</heartbeat>
        <!-- 配置真实的 MySQL 的连接信息 -->
        <writeHost host="hostM1" url="jdbc:dm://localhost:5237?comOra=true" user="MYDB2" password="a123456789"></writeHost>
    </dataHost>
</mycat:schema>
```

  3. 启动 MyCat 中间件服务。



配置完成后，双击 /mycat/bin/startup_nowrap.bat 即可启动 MyCat 中间件服务。

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404191547471W5LMXC0KZZ1GU22VK)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps7.jpg)

  4. 使用 JDBC 连接 MyCat 中间件服务操作数据库。



因为 MyCat 完全适配 MySQL ，所以无论是什么数据库，都要使用 mysql-connect-java 的 JDBC 驱动包，可从 [mysql-connector-java 包](https://dev.mysql.com/downloads/connector/j/5.1.html) 下载指定 jar 包。

新建项目 JAVA_Mycat，并导入项目所需 jar 包，如下图所示：

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404191550044FF74B0S4IKLOB1WBR)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps8.jpg)

> **注意**
>
> 复制 jar 包到 lib 文件夹后，要选择所有 jar 包，鼠标右键，Build path-->Add to Build path 。



## 三、开发示例

### 3.1 基础操作示例

  1. 编写 mycat_Demo.java 文件。



```
package dameng.mycat;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
public class mycat_Demo {
    // 定义 DM JDBC 驱动串
    static String jdbcString = "com.mysql.cj.jdbc.Driver";
    // 定义 DM URL 连接串
    static String urlString = "jdbc:mysql://localhost:8066/PRODUCTION";
    // 定义连接用户名
    static String userName = "root";
    // 定义连接用户口令
    static String password = "******";
    // 定义连接对象
    static Connection conn = null;
    // 定义 SQL 语句执行对象
    static Statement state = null;
    // 定义结果集对象
    static ResultSet rs = null;
    public static void main(String[] args) {
        try {
            //1.加载 JDBC 驱动程序
            System.out.println("Loading JDBC Driver...");
            Class.forName(jdbcString);
            //2.连接 DM 数据库
            System.out.println("Connecting to DM Server...");
            conn = DriverManager.getConnection(urlString, userName, password);
            //3.通过连接对象创建 java.sql.Statement 对象
            state = conn.createStatement();
//--------------------------------------------------------------------------------------------------
            //基础操作:此处操作对应的数据库,为示例库中的 PRODUCTION 模式中的 PRODUCT_CATEGORY 表
            //增加
                //定义增加的 SQL--这里由于此表中的结构为主键,自增,只需插入 name 列的值
                String sql_insert = "insert into PRODUCT_CATEGORY (id,name)values(1,'小说'),"+
                                    "(2,'文学'),(3,'计算机'),(4,'英语'),(5,'管理'),(6,'少儿'),(7,'金融')";
                //执行添加的 SQL 语句
                state.executeUpdate(sql_insert);
            //删除
                //定义删除的 SQL 语句
                String sql_delete = "delete from PRODUCT_CATEGORY where name = '厨艺'";
                //执行删除的 SQL 语句
                state.execute(sql_delete);
            //修改
                String sql_update = "update PRODUCT_CATEGORY set name = '国学' where name = '文学'";
                state.executeUpdate(sql_update);
            //查询表中数据
                //定义查询 SQL
                String sql_selectAll = "select * from PRODUCT_CATEGORY";
                //执行查询的 SQL 语句
                rs = state.executeQuery(sql_selectAll);
                displayResultSet(rs);
//---------------------------------------------------------------------------------------------------
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                //关闭资源
                rs.close();
                state.close();
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
    //显示结果集
    public static void displayResultSet(ResultSet rs) throws SQLException{
        while (rs.next()) {
            int i=1;
            Object id = rs.getObject(i++);
            Object name = rs.getObject(i++);
            System.out.println(id+"  "+name);
        }
    }
}
```

  2. 运行结果截图。



运行前数据库表数据截图如下所示：

![1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240422110947CTTE4ZG6VG96YSNACW)

![2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404221110287IP6UDNDIGKNI1NCZR)

运行后控制台截图如下所示：

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419155239L28UTO54049RJ8YNEX)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps11.jpg)

运行后数据库表截图，可以看到数据分别插入两张表中，如下图所示：

![微信截图_20240422111801.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240422111811P63O7MLRWJQLWMAUE1)

![微信截图_20240422111719.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240422111728ZKMK6YNSCOI447HQIS)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps12.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps13.jpg)

### 3.2 基础绑定参数示例

  1. 编写 mycat_prepareStatement.java 类。



```
package dameng.mycat;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
public class mycat_prepareStatement{
    // 定义 DM JDBC 驱动串
    static String jdbcString = "com.mysql.cj.jdbc.Driver";
    // 定义 DM URL 连接串
    static String urlString = "jdbc:mysql://localhost:8066/PRODUCTION";
    // 定义连接用户名
    static String userName = "root";
    // 定义连接用户口令
    static String password = "******";
    // 定义连接对象
    static Connection conn = null;
    // 定义 SQL 语句执行对象
    static PreparedStatement pstate = null;
    public static void main(String[] args) {
        try {
            //1.加载 JDBC 驱动程序
            System.out.println("Loading JDBC Driver...");
            Class.forName(jdbcString);
            //2.连接 DM 数据库
            System.out.println("Connecting to DM Server...");
            conn = DriverManager.getConnection(urlString, userName, password);
//----------------------------------------------------------------------------
            //绑定操作：此处操作对应的数据库，为示例库中的 PRODUCTION 模式中的
            //PRODUCT_CATEGORY 表
            //根据指定 id 修改 PRODUCT_CATEGORY 表中的 name 的值
            //这里 name 和 id 不确认用?代替
            String sql_updateNameById = "update PRODUCT_CATEGORY "+
            "set name = ? where ID  = ? ";
            //3.通过连接对象和修改语句的模板，创建 java.sql.PreparedStatement 对象
            pstate = conn.prepareStatement(sql_updateNameById);
            //4.绑定?对应的参数：理论上有多少个?就要绑定多少个值;
            //把 id 为 3 所对应的 name 值修改为 "魔幻"
            pstate.setString(1, "魔幻");
            pstate.setInt(2, 3);
            //5. 执行 SQL 语句
            pstate.executeUpdate();
//----------------------------------------------------------------------------
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } finally {
            try {
                pstate.close();
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            }
        }
    }
}
```

  2. 运行结果截图。



运行后数据库表数据如下图所示：

![微信截图_20240422111936.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240422111945PFAXTOC04PZK3QNE6O)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps14.jpg)

## 四、 常见问题

### 4.1 MyCat 启动报错 1

【问题描述】：报错信息如下图：

![图片 12.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419155543WKTU9YPKJYPUD2GJ7C)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps15.jpg)

【解决方法】：该报错是没有修改 rule.xml 文件导致的，修改 rule.xml 中的内对应的数字即可，数字为 schema.xml 文件中配置的 dataNode 数量。

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps16.jpg)

### 4.2 MyCat 启动报错 2

【问题描述】：报错信息如下图：

![图片 13.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404191557517AJRLYQZ4QEUA3IJSP)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps17.jpg)

【解决方法】：编码格式不一致，删除 xml 文件中文内容，或者转换为操作系统适合的编码。

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps18.jpg)

### 4.3 MyCat 启动报错 3

【问题描述】：报错信息如下图：

![图片 14.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419155816ROO5CC47Y35J2U887M)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps19.jpg)

【解决方法】：

Linux 环境下：

需要在 /mycat/bin/startup_nowrap.sh 调大 MaxDirectMemorySize 大小。

window 环境下：

需要在 /mycat/bin/startup_nowrap.bat 文件调大 MaxDirectMemorySize 大小，下图所示此参数已从 1G 调大为 2G。

![图片 15.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404191558517H9U8VQK77QSF8W4DD)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps20.jpg)

### 4.4 代码运行报错 1

【问题描述】：报错信息如下图：

![图片 16.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419155921PMBGYPJHI7XIROCJAS)

详细报错信息：

```
Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary .

java.sql.SQLNonTransientConnectionException: CLIENT_PLUGIN_AUTH is required .
```

【解决方法】：com.mysql.jdbc.Driver 改为 com.mysql.cj.jdbc.Driver  。

![图片 17.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024041915593668XKWT4D6F9O9EH7BT)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps22.jpg)

### 4.5 代码运行报错 2

【问题描述】：报错信息如下图：

![图片 18.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419160322H7PQ8MWAPT3GHPGHHC)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps23.jpg)

【解决方法】：

此现象是 mysql  版本和所连接的 mysql 版本不一致导致的。

如果 mysql-connector-java 用的 6.0 以上的，数据库驱动是：com.mysql.cj.jdbc.Driver 。

如果 mysql-connector-java 用的 6.0 以下的，数据库驱动是：com.mysql.jdbc.Driver 。

### 4.6 代码运行时报无效的模式名

【问题描述】：报错截图如下：

![图片 19.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419160355NZV0KY4QOFSCBHU0RH)

【问题分析】：代码里面有模式名时，运行时 sql 时会直接将带模式名的 sql 传入数据库。

![图片 20.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419160431EKVXZW7PSQKLUR0EHL)

![图片 21.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240419160439BJ5NM0CUGB1QOUBQIT)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps25.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps26.jpg)

在 schema.xml 文件中，checkSQLschema 参数说明如下：

当值设置为 true 时，会把模式名删除后执行 sql，例如上图中的 SQL 语句会变为如下 SQL：

```
insert into PRODUCT_CATEGORY (id,name)values(1,'小说'),"+ "(2,'文学'),(3,'计算机'),(4,'英语'),(5,'管理'),(6,'少儿'),(7,'金融');
```

如果语句所带的模式名不是通过 schema 标签指定 name，这种情况下例如有 SQL：`select * from db1.travelrecord;` 那么 MyCat 并不会删除 db1 这个模式名，会直接传到 DM 连接用户下执行，这种情况会报错无效的模式名，所以在写 SQL 语句时建议不带模式名。

当值设置为 false 时，不会删除模式名，会按照原 sql 执行。

【问题处理】：如果想运行 sql 时删除模式名，需要在 schema.xml 文件中指定 name 为当前 sql 语句中的模式名，并且 checkSQLschema 为 true 。

如果想运行 sql 时带有模式名，则在 schema.xml 文件中不指定 name 或配置 checkSQLschema 为 false 。

### 4.7 其他问题

其他更多 MyCat 相关的常见问题请参考：

[https://eco.dameng.com/document/dm/zh-cn/faq/faq-java.html](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java.html) 。

## 五、参考

1．示例代码下载：参考下面文件 [JAVA_MYCAT.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240419160704IWGUL5L7BKZ64W1WLV) 。

2．以上文档内容参考过程中遇到任何问题，可到[达梦技术社区](https://eco.dameng.com/community/question/)提问交流。

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20412\wps27.png)



# JDBC 接口

## 一、前言

JDBC (Java Database Connectivity)  是 Java 应用程序与数据库的接口规范，旨在让各数据库开发商为 Java 程序员提供标准的数据库应用程序编程接口 (API) 。JDBC 定义了一个跨数据库、跨平台的通用 SQL 数据库 API。

DM JDBC 数据库驱动程序是一个能够支持基本 SQL 功能的通用应用程序编程接口，支持一般的 SQL 数据库访问。通过 JDBC 驱动程序，用户可以在应用程序中实现对 DM 数据库的连接与访问，JDBC 驱动程序的主要功能包括：

  * 建立与 DM 数据库的连接。
  * 转接发送 SQL 语句到数据库。
  * 处理并返回语句执行结果。



由于 DM JDBC 驱动遵照 JDBC 标准规范设计与开发，因此 DM ODBC 接口提供的函数与标准 JDBC 一致。JDBC 接口函数较多，下表仅列出了 DM JDBC 主要接口和函数：

|**主要类或接口**|**类或接口说明**|**主要函数**|**函数说明**|
|------|------|------|------|
|java.sql.DriverManager|用于管理驱动程序、并可与数据库建立连接。其类中的方法均为静态方法。|getConnection|创建连接|
|setLoginTimerout|设置登录超时时间|
|registerDriver|注册驱动|
|deregisterDriver|卸载驱动|
|java.sql.Connection|数据库连接类，作用是管理执行数据库的连接，可用于提交和回滚事务、创建Statement对象等操作。|createStatement|创建一个 Statement 对象|
|setAutoCommit|设置自动提交|
|close|关闭数据库连接|
|commit|提交事务|
|rollback|回滚事务|
|java.sql.Statement|用于在连接上运行SQL 语句，并可访问结果。|execute|运行 SQL 语句|
|executeQuery|执行一条返回 ResultSet 的 SQL 语句|
|executeUpdate|执行 INSERT、UPDATE、DELETE 或一条没有返回数据集的 SQL 语句|
|getResultSet|用于得到当前 ResultSet的结果|
|java.sql.ResultSet|结果集对象，主要用于查询结果访问|absolute|将结果集的记录指针移动到指定行|
|next|将结果集的记录指针定位到下一行|
|last|将结果集的记录指针定位到最后一行|
|close|释放 ResultSet 对象|
|java.sql.DatabaseMetaData|用于获取数据库元数据信息的类，如模式信息、表信息、表权限信息、表列信息、存储过程信息等|getTables|得到指定参数的表信息|
|getColumns|得到指定表的列信息|
|getPrimaryKeys|得到指定表的主键信息|
|getTypeInfo|得到当前数据库的数据类型信息|
|getExportedKeys|得到制定表的外键信息|
|java.sql.ResultSetMetaData|用于获取结果集元数据信息的类，如结果集的列数、列的名称、列的数据类型、列大小等信息|getColumnCount|得到数据集中的列数|
|getColumnName|得到数据集中指定的列名|
|getColumnLabel|得到数据集中指定的标签|
|getColumnType|得到数据集中指定的数据类型|


## 二、数据库连接

### 2.1 准备 DM-JDBC 驱动包

老版本目录结构 ，DM JDBC 驱动 jar 包在 DM 安装目录 /dmdbms/drivers/jdbc，下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221226141847UWTCR7SKVNE62XH0DV)

达梦 24 年第三季度版及之后版本，DM JDBC 驱动 jar 包在 DM 安装目录 /dmdbms/drivers/jdbc，新加入 DmJdbcDriver11.jar ，如下图所示：

![微信截图_20241021155247.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241021155313JWE0CUAMR5XJ5WOCO4)

> **注意**
>
> 达梦 24 年第三季度版之前的 jdbc 的 jar 包名为 DmJdbcDriver16.jar 代表适配 jdk 1.6，
> DmJdbcDriver17.jar 代表适配 jdk 1.7 ， DmJdbcDriver18.jar代表适配jdk 1.8, 达梦 24
> 年第三季度版现加入 jdk11的版本，并对原有jar进行改名，新的jar名称为DmJdbcDriver6.jar代表适配jdk 1.6，
> DmJdbcDriver7.jar代表适配jdk 1.7， DmJdbcDriver8.jar代表适配jdk 1.8，
> DmJdbcDriver11.jar 代表适配jdk 11 。



### 2.2 数据库连接示例

  1. DM 数据库扩展连接串的使用



DM JDBC 数据库连接驱动具体位置是 dm.jdbc.driver.DmDriver。

  2. 连接串的书写格式有以下两种：



（1）host、port 不作为连接属性，此时只需输入值即可：

```
jdbc:dm [: //host][:port][?propName1=propValue1][& propName2=propValue2]...
```

> **注意**
>
> 若 host 不设置，则默认为‘localhost’。  
> 若 port 不设置，则默认为‘5236’。
>
> 若 host 不设置，则 port 一定不能设。
>
> 若 user、password 没有单独作为参数传入，则必须在连接属性中传入。
>
> 若 host 为 ipv6 地址，则应包含在[]中。



示例如下：

```
jdbc:dm://192.168.0.96:5236?resultSetType=1003
```

（2）host、port 作为连接属性，此时必须按照下表中说明进行设置，且属性名称大小写敏感。

|**属性名称**|**说明**|**是否必须设置**|
|------|------|------|
|“host”|主库地址，包括 IP、localhost 或者配置文件中主库地址，列表对应的变量名，如 dm_svc.conf 中的“o2000”|否|
|“port”|端口号，服务器登录端口号|否|


连接串格式格式如下：

```
jdbc:dm:// [?propName1=propValue1] [ & propName2=propValue2] [&…]…
```

> **注意**
>
> host、port 设置与否，以及在属性串中的位置没有限制。若 user、password 没有单独作为参数传入，则必须在连接属性中传入。



示例如下：

```
jdbc:dm:// ?host=192.168.0.96&port=5236
```

> **建议**
>
> 更多连接串属性的使用请参考《DM 程序员手册》- DM 扩展连接属性的使用，手册位于数据库安装路径/dmdbms/doc 文件夹下。



  3. 示例代码



```
package java_jdbc;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
public class jdbc_conn {
    static Connection con = null;
    static String cname = "dm.jdbc.driver.DmDriver";
    static String url = "jdbc:dm://localhost:5236";
    static String userid = "SYSDBA";
    static String pwd = "*****";
    public static void main(String[] args) {
        try {
            Class.forName(cname);
            con = DriverManager.getConnection(url, userid, pwd);
            con.setAutoCommit(true);
            System.out.println("[SUCCESS]conn database");
        } catch (Exception e) {
            System.out.println("[FAIL]conn database：" + e.getMessage());
        }
    }
    public void disConn(Connection con) throws SQLException {
        if (con != null) {
            con.close();
        }
    }
}
```

运行结果截图：

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241223120614R79G2X3UUVZ0VRMA12)

## 三、开发示例

### 3.1 基础操作示例

  1. 基础增删改查示例代码。



```
package java_jdbc;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.ResultSet;
import java.sql.SQLException;
import java.sql.Statement;
public class jdbc_insert {
// 定义 DM JDBC 驱动串
static String jdbcString = "dm.jdbc.driver.DmDriver";
// 定义 DM URL 连接串
static String urlString = "jdbc:dm://localhost:5236";
// 定义连接用户名
static String userName = "SYSDBA";
// 定义连接用户口令
static String password = "*****";
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
//-----------------------------------------------------------------------------
        //基础操作：此处对应的操作代码为示例库中 PRODUCTION 模式中的
        //PRODUCT_CATEGORY 表
        //增加
            //定义增加的 SQL 这里由于此表中的结构为主键，自增，只需插入 name 列的值
            String sql_insert = "insert into PRODUCTION.PRODUCT_CATEGORY"+
            "(name)values('厨艺')";
            //执行添加的 SQL 语句
            state.execute(sql_insert);
        //删除
            //定义删除的 SQL 语句
            String sql_delete = "delete from PRODUCTION.PRODUCT_CATEGORY "+
            "where name = '厨艺'";
            //执行删除的 SQL 语句
            state.execute(sql_delete);
        //修改
            String sql_update = "update PRODUCTION.PRODUCT_CATEGORY set "+
            "name = '国学' where name = '文学'";
        //查询表中数据
            //定义查询 SQL
            String sql_selectAll = "select * from PRODUCTION.PRODUCT_CATEGORY";
            //执行查询的 SQL 语句
            rs = state.executeQuery(sql_selectAll);
            displayResultSet(rs);
//----------------------------------------------------------------------------
                state.executeUpdate(sql_update);
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

  2. 运行示例截图。运行前数据库数据如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221226170404AOI5AW8A2YUF2H2HZR)

运行后控制台输出结果如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214151551WMVIDEUWTZK1MTA8CK)

运行后数据库数据如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212261705277VQJJYEYC50VNSYV02)

### 3.2 绑定变量示例

  1. 绑定参数示例代码。



```
package java_jdbc;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.SQLException;
public class jdbc_prepareStatement{
    // 定义 DM JDBC 驱动串
    static String jdbcString = "dm.jdbc.driver.DmDriver";
    // 定义 DM URL 连接串
    static String urlString = "jdbc:dm://localhost:5236";
    // 定义连接用户名
    static String userName = "SYSDBA";
    // 定义连接用户口令
    static String password = "*****";
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
//------------------------------------------------------------------------------------------------------------------------------
            //绑定操作：此处操作对应的数据库，为示例库中的 PRODUCTION 模式中的
            //PRODUCT_CATEGORY 表
            //根据指定 id 修改 PRODUCT_CATEGORY 表中的 name 的值
            //这里 name 和 id 不确认用?代替
            String sql_updateNameById = "update PRODUCTION.PRODUCT_CATEGORY "+
            "set name = ? where PRODUCT_CATEGORYID = ? ";
            //3.通过连接对象和修改语句的模板，创建 java.sql.PreparedStatement 对象
            pstate = conn.prepareStatement(sql_updateNameById);
            //4.绑定?对应的参数：理论上有多少个?就要绑定多少个值;
            //把 id 为 3 所对应的 name 值修改为 "魔幻"
            pstate.setString(1, "魔幻");
            pstate.setInt(2, 3);
            //5. 执行 SQL 语句
            pstate.executeUpdate();
//------------------------------------------------------------------------------------------------------------------------------
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

  2. 运行示例截图。运行后控制台输出如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212261707003XSBXHQTFDS3R5KGEE)

![](file:///C:\Users\yboo\AppData\Local\Temp\ksohtml75992\wps1.jpg)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221226170819D748TIZVM35OQMZ52E)

### 3.3 大字段操作示例

  1. 创建需要操作的含大字段类型的数据表。



```
CREATE TABLE "PRODUCTION"."BIG_DATA"
(
"ID" INT IDENTITY(1, 1) NOT NULL,
"PHOTO" IMAGE,
"DESCRIBE" BLOB,
"TXT" CLOB,
NOT CLUSTER PRIMARY KEY("ID")) STORAGE(ON "BOOKSHOP", CLUSTERBTR) ;
```

  2. 在 D 盘根目录下，创建 DM8 特点 .jpg、达梦产品简介 .txt 两个文件，作为大字段存储，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212231037381QM3TL20H9JQY4LUNE)

  3. 插入大字段关键代码。



```
//1.插入大字段信息:  
    package java_jdbc;
    import java.io.BufferedInputStream;
    import java.io.BufferedReader;
    import java.io.File;
    import java.io.FileInputStream;
    import java.io.FileNotFoundException;
    import java.io.InputStream;
    import java.io.InputStreamReader;
    import java.io.UnsupportedEncodingException;
    import java.sql.Connection;
    import java.sql.DriverManager;
    import java.sql.PreparedStatement;
    import java.sql.SQLException;
    public class jdbc_operate_bigData{
        // 定义 DM JDBC 驱动串
        static String jdbcString = "dm.jdbc.driver.DmDriver";
        // 定义 DM URL 连接串
        static String urlString = "jdbc:dm://localhost:5236";
        // 定义连接用户名
        static String userName = "SYSDBA";
        // 定义连接用户口令
        static String password = "*****";
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
//------------------------------------------------------------------------------------------------------------------------
                //1.插入大字段信息:  
                String sql_insert = "INSERT INTO production.BIG_DATA (\"photo\","
                    + "\"describe\",\"txt\")VALUES(?,?,?);";
                pstate = conn.prepareStatement(sql_insert);
                //加载图片为输入流
                String filePath = "D:\\DM8特点.jpg";
                File file = new File(filePath);
                String filePath2 = "D:\\达梦产品简介.txt";
                File file2 = new File(filePath2);
                InputStream in = new BufferedInputStream(new FileInputStream(file));
                InputStream in2 = new BufferedInputStream(new FileInputStream(file));
                BufferedReader reader = new BufferedReader(
                    new InputStreamReader(new FileInputStream(file2),"UTF-8"));
                //1.绑定 stream 流信息到第一个?
                pstate.setBinaryStream(1, in);
                //2.绑定 Inputstream 对象到第二个?这里
                pstate.setBlob(2, in2);
                //3.绑定 Reader 对象到第三个?
                pstate.setClob(3, reader);
                pstate.executeUpdate();
    //------------------------------------------------------------------------------------------------------------------------
            } catch (ClassNotFoundException e) {
                e.printStackTrace();
            } catch (SQLException e) {
                e.printStackTrace();
            } catch (FileNotFoundException e) {
                e.printStackTrace();
            } catch (UnsupportedEncodingException e) {
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

  4. 插入数据后，数据库数据如下所示：



  * stream 以字节输入流的形式，插入 Image 类型的字段，数据库保存的是图片信息。
  * Blob 以字节输入流的形式，插入 Blob 类型的字段，数据库保存的是图片信息。
  * Clob 以字符输入流的形式，插入 Clob 类型的字段，数据库表中保存的是文本信息。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212141520439GDZOO18YV6MGYAMOA)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214152053SFP4DOMHNCE7OAG2IR)

  5. 查询大字段关键代码。



```
package java_jdbc;
import java.io.BufferedReader;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.UnsupportedEncodingException;
import java.sql.Blob;
import java.sql.Clob;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;
public class jdbc_operate_bigData2{
    // 定义 DM JDBC 驱动串
    static String jdbcString = "dm.jdbc.driver.DmDriver";
    // 定义 DM URL 连接串
    static String urlString = "jdbc:dm://localhost:5236";
    // 定义连接用户名
    static String userName = "SYSDBA";
    // 定义连接用户口令
    static String password = "*****";
    // 定义连接对象
    static Connection conn = null;
    // 定义 SQL 语句执行对象
    static PreparedStatement pstate = null;
    // 定义保存结果集的对象
    static ResultSet rs = null;
    // 定义输入流对象
    static InputStream in = null;
    // 定义输出流对象
    static FileOutputStream fos = null;
    // 定义输出流对象
    static FileOutputStream fos2 = null;
    // 定义高效字符流对象
    static BufferedReader reader = null;
    public static void main(String[] args) {
        try {
            //1.加载 JDBC 驱动程序
            System.out.println("Loading JDBC Driver...");
            Class.forName(jdbcString);
            //2.连接 DM 数据库
            System.out.println("Connecting to DM Server...");
            conn = DriverManager.getConnection(urlString, userName, password);
//-----------------------------------------------------------------------------------------------
            //3.查询大字段信息 SQL 语句
            String sql_insert = "SELECT * FROM production.BIG_DATA ;";
            pstate = conn.prepareStatement(sql_insert);
            //4.创建 ResultSet 对象保存查询结果集
            rs = pstate.executeQuery();
            //5.解析结果集
            while(rs.next()) {
                //获取第一列 id 信息
                int id = rs.getInt("id");
                //获取第二列 photo 图片信息，并把该图片直接写入到 D:/id_DM8特点.jpg;
                in = rs.getBinaryStream("photo");
                fos = new FileOutputStream("D:/"+id+"_DM8特点.jpg");
                int num = 0;
                //每次从输入流中读取一个字节数据，如果没读到最后指针向下继续循环
                while((num=in.read())!=-1) {
                    //将每次读取的字节数据，写入到输出流中
                    fos.write(num);
                }
                //获取第三列的 Blob 大字段信息
                //Blob 对象处理的是字节型大字段信息例如图片、视频文件等
                Blob blob = rs.getBlob("describe");
                in = blob.getBinaryStream();
                fos2 = new FileOutputStream("D:/"+id+"_Blob_DM8特点.jpg");
                //每次从输入流中读取一个字节数据，如果没读到最后指针向下继续循环
                while((num=in.read())!=-1) {
                    //将每次读取的字节数据，写入到输出流中
                    fos2.write(num);
                }
                //获取第四列的 Clob 大字段信息
                //Clob 大字段处理的是字符型大字段信息，文本等数据
                Clob clob = rs.getClob("txt");
                reader = new BufferedReader(clob.getCharacterStream());
                String str = null;
                while((str=reader.readLine())!=null) {
                    //将每次读取的字节数据
                    System.out.println(str.toString());
                }
            }
//-----------------------------------------------------------------------------------------
        } catch (ClassNotFoundException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } finally {
            try {
                //关闭资源
                fos.close();
                in.close();
                rs.close();
                pstate.close();
                conn.close();
            } catch (SQLException e) {
                e.printStackTrace();
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }
}
```

  6. 运行示例截图。运行前如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212261716317MSMW7KG9GGRZQS243)

运行后控制台输出 Clob 里保存的大字段文本信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212261717040OWRLU0RMNSLOZ229D)

运行后读取处理 Image 大字段和 Blob 大字段后的信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221226171828NSZHU92ND1CEFUV462)

## 四、常见问题

1. JDBC 中使用 setNull 方法给 varchar 类型绑定空值报错：字符串转换出错

【问题描述】：通过如下语句给 varchar 类型绑定空值报错：字符转换出错。

```
String sql = "select * from sysobjects where name=?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setNull(1, 0);
stmt.execute();
conn.commit();
```

【问题解决】：出现该报错是由于达梦默认 null 和空串不等价，解决办法如下：

  * 修改 dm.ini，令 COMPATIBLE_MODE = 2；
  * 重启数据库服务后生效



2. jdbc 程序中如何获取存储过程的元数据信息

【问题描述】：利用如下程序获取存储过程的元数据信息，执行完后获取的元数据信息为空，jdbc 程序代码片段如下：

```
DatabaseMetaData dbMetaData = conn.getMetaData();
  ResultSet rs = dbMetaData.getProcedureColumns("PERSONPACKAGE", dbMetaData.getUserName(), "ADDPERSON", null);
  ResultSet rs = dbMetaData.getProcedureColumns("PERSONPACKAGE", dbMetaData.getUserName(), "ADDPERSON", null);
   System.out.println("Get getProcedureColumns：");
    while (rs.next()) {
     System.out.println("PROCEDURE_NAME："+rs.getString("PROCEDURE_NAME"));
    System.out.println("COLUMN_NAME："+rs.getString("COLUMN_NAME"));
             }
```

【问题解决】：需要 JDBC 打开兼容 Oracle 模式，在 jdbc 的 url 串中添加 compatibleMode=oracle。
例如：jdbc:dm//localhost:5236?compatibleMode=oracle

3. 报错 JDK 包和驱动不匹配 driver dm.jdbc.driver.DmDriver is not sequoia compliant

【问题描述】：连接数据库时报错：driver dm.jdbc.driver.DmDriver is not sequoia compliant

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228105844G9Z86ASJ60867D3V94)

【问题解决】：更换 JDK 包就可以了。连接池的连接问题：连接要使用 DM 安装数据库后自带的 JDK 包。

4. JDBC 驱动连接数据库报错：failed to initialize ssl

【问题描述】：由于未配置 SSL 环境而使用 SSL 登录。

【问题解决】：可以关闭 SSL 参数。使用 sysdba 登录执行以下语句：

```
SP_SET_PARA_VALUE(2,'ENABLE_ENCRYPT',0);
```

然后重启达梦数据库服务即可。

## 五、参考

  1. 示例代码下载：[JAVA_JDBC.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221229154330IB7GXUU59PCX3MRR9W) 。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。



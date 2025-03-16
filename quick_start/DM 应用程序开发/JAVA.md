

# JAVA

## 一、前言

JDBC (Java Database Connectivity) 是 Java 应用程序与数据库的接口规范，旨在让各数据库开发商为 Java 程序员提供标准的数据库应用程序编程接口 (API) 。JDBC 定义了一个跨数据库、跨平台的通用 SQL 数据库 API。

DM JDBC 驱动是一套能够支持基本 SQL 功能的通用应用程序编程接口，支持一般的 SQL 访问 DM 数据库。

## 二、环境配置

数据库安装请参考 [数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87) 。

JAVA 环境配置请参考: [JAVA 开发环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/develop-environment-prepare-java) 。

## 三、获取驱动方式

使用 java 连接达梦，需要使用达梦 jdbc 驱动，达梦 jdbc 驱动位于达梦数据库安装目录的 \drivers\jdbc 目录下，将其路径添加到项目工程下，或者通过 maven ，gradle 等方式进行导入， Maven Repository 上 DmJdbcDriver 下载地址为:

[https://mvnrepository.com/artifact/com.dameng/DmJdbcDriver18](https://mvnrepository.com/artifact/com.dameng/DmJdbcDriver18，)

在此可找到合适版本进行下载。

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240703163335NLSCT6X5DXPL63RI1A)

> **注意**
>
> 使用 jdbc 驱动时，需要使用和服务器端 DM 数据库相近的版本，相隔时间太久的版本，可能出现部分功能无法适配的情况。达梦的 JDBC
> 驱动目前有三个版本： DmJdbcDriver16.jar 、 DmJdbcDriver17.jar 和 DmJdbcDriver18.jar
> ，分别对应 jdk1.6 、 jdk1.7 和 jdk1.8 及以上版本。



## 四、工程添加驱动

普通项目可通过此方式进行添加驱动，点击文件--> 项目结构--> 库，添加 DmJdbc  驱动。

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202412231153221YOLNUVTLNBE4JMO5T)

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240703163757K7VQYVC90HASXGM1VO)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml26148\wps3.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml26148\wps4.jpg)

## 五、测试连接示例

```
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.SQLException;
public class jdbc_conn {
   // 定义连接
   static Connection con = null;
   // 定义 DM JDBC 驱动串 
   static String cname = "dm.jdbc.driver.DmDriver";
   // 定义 DM URL 连接串 
   static String url = "jdbc:dm://localhost:5236";
   // 定义连接用户名 
   static String userid = "SYSDBA";
   // 定义连接用户口令 
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

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240703163846TXOJH1SMP456XWPUKU)



# QODBC 编译

## 一、前言

本章节详细介绍 QT QODBC 数据库编程连接，QT 为数据库编程提供了一个类似 JDBC 的统一操作模型，它的底层是具体数据库或数据库接口的插件，由它们负责完成真正的数据库操作。可进入达梦云适配中心下载试用下载 QT 接口源码。

QT 自带 QODBC Driver，在 Windows 平台上通过系统提供的 ODBC Driver 可以访问支持 ODBC 的数据库，如 Ms Access、SQL Server 等（ Windows XP 自带 Access 和 SQL Server 的 ODBC Driver）。开发者通过 QODBC 接口连接 DM 数据库。

## 二、开发环境准备

### 2.1 达梦数据库版本

|**名称**|**版本**|
|--|--|
|DM 数据库|DM 8.0 及以上版本|
|QT|5.12.1|


### 2.2 达梦数据库安装

请参考[数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.3 QT 开发环境搭建

下载链接：[https://download.qt.io/archive/qt/](https://download.qt.io/archive/qt/)

### 2.4 注册 ODBC 数据源

请参考 [通过 ODBC 连接数据库](https://eco.dameng.com/document/dm/zh-cn/start/C_C++_development.html#%E9%80%9A%E8%BF%87%20ODBC%20%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93) 章节。

### 2.5 编译 libqsqlodbc.so

  1. 首先进入 qt 源码目录中修改 qsqldriverbase.pri 文件，将 include 部分修改为下图所示内容。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911165855TI94YPFPK0YNE0LNZL)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911165823Z8L7NBRYPI5W4TZVSI)

  2. 修改 qt 源码目录中的 odbc.pro 文件，注释 \`QMAKE_USE += odbc\` 。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911170554CRAYICQ09X84CEIOV3)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911170330W6MU8RJNQQ02AN2PNI)

  3. 在/data/Qt5.12.1/5.12.1/gcc_64/bin 下执行。INCLUDEPATH 和 LIBS 的查找方式如下：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911171126U03WZ2KFKUGIEWF3PV)

```
./qmake "INCLUDEPATH+=/usr/local/include" "LIBS+=-L/usr/local/lib -lodbc" /data/Qt5.12.1/5.12.1/Src/qtbase/src/plugins/sqldrivers/odbc/odbc.pro
```

会在该目录下生成 Makefile 文件，执行 make 即可生成 libqsqlodbc.so，将此动态库复制到 qt 目录下的 sqldrivers 目录下即可正常使用 qodbc 接口对达梦进行操作。

## 三、数据库连接

### 3.1 数据库连接示例

QODBC 通过 ODBC 接口登录登出示例程序 qt_conn.c 如下：

```
#include <QCoreApplication>
#include <QtSql>
#include <QDebug>
#include <QSqlDatabase>

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    QSqlDatabase db = QSqlDatabase::addDatabase("QODBC");
    db.setHostName("192.168.104.21");
    db.setPort(51236);
    db.setDatabaseName("DM8");
    db.setUserName("SYSDBA");
    db.setPassword("*****");
    if (db.open())
        qDebug() << "connect ok!";
    else
    {
        qDebug() << "connect fail! " << db.lastError().text().toLatin1();
    }

    return a.exec();
}
```

2、运行示例截图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911172726LY3VJ11QO17IZ18NA7)

## 四、开发示例

### 4.1 基础操作示例

  1. QODBC 通过 ODBC 接口增、删、改、查四个基本操作，示例程序 qt_dml.c 如下：



```
#include <QCoreApplication>
#include <QtSql>
#include <QDebug>
#include <QTextCodec>

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    QSqlDatabase db = QSqlDatabase::addDatabase("QODBC");
    db.setHostName("192.168.104.21");
    db.setPort(51236);
    db.setDatabaseName("DM8");
    db.setUserName("SYSDBA");
    db.setPassword("*****");
    if (db.open())
        qDebug() << "connect ok!";
    else
    {
        qDebug() << "connect fail! " << db.lastError().text().toLatin1();
    }

    QSqlQuery query;

    //清空表，初始化测试环境
    QString strsql = "delete from PRODUCTION.PRODUCT_CATEGORY";
    query.exec(strsql);


    //插入数据
    strsql = "insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values('语文'), ('数学'), ('英语'), ('体育')";
    if (query.exec(strsql))
    {
        qDebug() << "insert ok!";
    }
    else
    {
        qDebug() << "insert fail! " << query.lastError().text();
        getchar();
        exit(-1);
    }

    //删除数据
    strsql = "delete from PRODUCTION.PRODUCT_CATEGORY where name='数学'";
    if (query.exec(strsql))
    {
        qDebug() << "delete ok!";
    }
    else
    {
        qDebug() << "delete fail! " << query.lastError().text();
        getchar();
        exit(-1);
    }

    //更新数据
    strsql = "update PRODUCTION.PRODUCT_CATEGORY set name = '英语—新课标' where name='英语';";
    if (query.exec(strsql))
    {
        qDebug() << "update ok!";
    }
    else
    {
        qDebug() << "update fail! " << query.lastError().text();
        getchar();
        exit(-1);
    }

    //查询数据
    strsql = "select name from PRODUCTION.PRODUCT_CATEGORY";
    if (query.exec(strsql))
    {
        qDebug() << "select ok!";
    }
    else
    {
        qDebug() << "select fail! " << query.lastError().text();
        getchar();
        exit(-1);
    }
    while (query.next())
    {
         qDebug() << query.value(0).toString().toStdString().c_str();
    }
    query.clear();

    return a.exec();
}
```

  2. 运行示例截图：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911164356TRWPYK3IX7AMV4GHJF)

### 4.2 绑定变量示例

  1. QODBC 通过 ODBC 接口绑定变量示例程序 qt_bind.c 如下：



```
#include <QCoreApplication>
#include <QtSql>
#include <QDebug>
#include <QTextCodec>

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    QSqlDatabase db = QSqlDatabase::addDatabase("QODBC");
    db.setHostName("192.168.104.21");
    db.setPort(51236);
    db.setDatabaseName("DM8");
    db.setUserName("SYSDBA");
    db.setPassword("*****");
    if (db.open())
        qDebug() << "connect ok!";
    else
    {
        qDebug() << "connect fail! " << db.lastError().text().toLatin1();
    }

    QSqlQuery query;

    //清空表，初始化测试环境
    QString strsql = "delete from PRODUCTION.PRODUCT_CATEGORY";
    query.exec(strsql);


    //绑定参数方式插入数据
    strsql = "insert into PRODUCTION.PRODUCT_CATEGORY(name) values(?)";
    query.prepare(strsql);
    query.bindValue(0, "物理");
    if (query.exec())
    {
        qDebug() << "insert ok!";
    }
    else
    {
        qDebug() << "insert fail! " << query.lastError().text();
        getchar();
        exit(-1);
    }

    //查询数据
    strsql = "select name from PRODUCTION.PRODUCT_CATEGORY";
    if (query.exec(strsql))
    {
        qDebug() << "select ok!";
    }
    else
    {
        qDebug() << "select fail! " << query.lastError().text();
        getchar();
        exit(-1);
    }
    while (query.next())
    {
         qDebug() << query.value(0).toString().toStdString().c_str();
    }
    query.clear();

    return a.exec();
}
```

  2. 运行示例截图：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911164307TYHC383QRZVQXCIDBA)

### 4.3 大字段操作示例

  1. QODBC 通过 ODBC 接口大字段操作包括大字段的插入，查询等。示例程序 qt_lob.c 如下：



```
#include <QCoreApplication>
#include <QtSql>
#include <QDebug>
#include <QTextCodec>

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);

    QSqlDatabase db = QSqlDatabase::addDatabase("QODBC");
    db.setHostName("192.168.104.21");
    db.setPort(51236);
    db.setDatabaseName("DM8");
    db.setUserName("SYSDBA");
    db.setPassword("*****");
    if (db.open())
        qDebug() << "connect ok!";
    else
    {
        qDebug() << "connect fail! " << db.lastError().text().toLatin1();
    }

    QSqlQuery query;

    //清理测试环境
    QString strsql = "drop table if exists PRODUCTION.BIG_DATA";
    query.exec(strsql);
    strsql = "create table PRODUCTION.BIG_DATA(c1 int, c2 blob)";
    query.exec(strsql);

    // 读取文件数据，写入 lob 列
    //选择要写入的文件
    QString f = "../qt_lob/file/DM8_SQL.pdf";

    //转换为字节数组
    QByteArray mapData;
    QFile file(f);
    file.open(QIODevice::ReadOnly);
    mapData = file.readAll();
    file.close();

    //插入数据
    query.prepare("INSERT INTO PRODUCTION.BIG_DATA values(?, ?)");
    query.bindValue(0, 1);
    query.bindValue(1, mapData);
    if (!query.exec()) {
        qDebug() << query.lastError();
    } else {
        qDebug() << "insert OK!";
    }

    //读取 lob 列数据，写入到文件中
    QSqlQuery readquery;

    QByteArray databa;
    if (readquery.exec("select c2 from PRODUCTION.BIG_DATA")) {
        QSqlRecord myrecord = readquery.record();

        if (readquery.next()) {
            databa = readquery.value(myrecord.indexOf("C2")).toByteArray();
        }
    }

    QFile mybfile("../qt_lob/file/DM81_SQL.pdf");
    mybfile.open(QIODevice::WriteOnly);
    mybfile.write(databa);
    mybfile.close();
    qDebug() << "select OK!";


    return a.exec();
}
```

  2. 运行示例截图：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230911164156DGLK15OHHKJT101KIK)

## 五、常见问题

  1. 首先尽可能使用源码安装的完整版 qt，否则会缺失基础的 libqsqlodbc.so，如果没有安装源码的情况下使用系统自带的 qt，要注意直接下载的源码 src 版本也要和现有 qt 版本一致，否则会出现头文件缺失等问题，该问题是目录结构不匹配导致；
    
        -isystem /usr/include/aarch64-linux-gnu/qt5/QtSql -isystem /usr/include/aarch64-liunx-gnu/qt5/QtCore -I.moc/debug
    ql_dm.cpp:59:10: fatal error: qsqldriver_p.h； 没有那个文件或目录
             #include\<qsqldriver_p.h>
    

  2. 保证 odbc 服务名能够正常进行 isql 测试，否则根据 isql -v dsn 来判断具体原因，大部分都是因为环境变量配置错误导致 libdodbc.so 动态库依赖缺失，arm 平台编译安装 unixodbc 注意加上--build=arm-linux；

  3. 避免系统中安装多版本 qt，会导致环境混乱，libqsqlodbc.so 或者 libqsqldm.so 动态库放入 qt 的 sqldrivers 目录中还是无法识别为可用驱动，driver not loaded,可通过临时 export QT_DEBUG_PLUGINS=1 该窗口下运行程序会打印出具体 qt 的插件 debug 信息，从中看是否从正确的目录搜索到了对应动态库，另外对应动态库是否被识别为 plugins，否则需要去考虑 qt 环境方面问题；



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212141127113OCVD77FJHKHNPWU3S)

  4. pro 文件中需要加上 qt+=sql 否则无法识别驱动；
  5. 尽量使用 root 用户打开 qtcreator，避免其他用户环境变量问题导致无法识别一些依赖组件；
  6. qt 中 kit 套件、数据库、odbc 位数需要保持一致。



## 六、参考

  1. 示例代码下载：[qt_qodbc_code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221222115824I5STRB7IRKF4W6L8NA)。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。




# QDM 编译

## 一、前言

QT 通过 QT SQL 模块提供了对 SQL 数据库的支持，QT SQL 模块接口是独立于数据库的，它可以通过数据库驱动插件和不同的数据库接口进行通信，达梦数据库提供了 QDM 对 QT SQL 模块进行支持。本文主要介绍 QDM 的编译，并提供了相应的例子对数据库进行操作演示。

## 二、开发环境准备

### 2.1 版本信息

|**名称**|**版本**|
|--|--|
|DM 数据库|DM 8.0 及以上版本|
|QT|5.12.1|


### 2.2 达梦数据库安装

请参考[数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.3 编译环境准备

QT 环境官方下载地址：[https://www.qt.io/download-open-source](https://www.qt.io/download-open-source) 。

  1. 编译 QDM 需要引用 QT src 中的相关文件，所以编译前我们需要先安装 QT，并在安装时选择 Source 组件。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214144512C3GJKU0PM3978AP1PQ)

安装完成后在安装目录下应包含如下两个目录：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214144532GZ5M0W1N92Y9KH08ND)

  2. G++，GCC 版本最好为 5.4 及以上，否则编译过程中可能会出现下面此类报错：



```
g++: 错误：unrecognized command line option '-Wdate-time'
```

此次编译环境版本为：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214144611HWMM9VYG4DN2PE2V4Q)

  3. 请前往[达梦云适配中心](https://eco.dameng.com/download/)下载试用下载 QT QDM 接口源码。



### 2.4 编译 QDM 动态库

本机 QT 安装目录为 /opt/Qt5.12.1，因为每个人的安装目录都不一致，所以定义 QTDIR=/opt/Qt5.12.1/5.12.1。

  1. 上传 QDM 源码到对应目录。



（1）上传 qt_src\plugins_sqldrivers\dm_56 到 QTDIR/Src/qtbase/src/plugins/sqldrivers，并改名为 dm；

（2）上传 qt_src\sql_drivers\dm 到 QTDIR/Src/qtbase/src/sql/drivers 目录，没有此目录可以自行创建。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214144842TY5T299VFOHKG5LKG2)

  2. 生成 Makefile。进入 $QTDIR/Src/qtbase/src/plugins/sqldrivers/dm 目录执行：



```
qmake "INCLUDEPATH+=/home/dmdba/dm/dmdbms/include" "LIBS+=/home/dmdba/dm/dmdbms/include/libdmdpi.a -ldl"  "DEFINES+=DM64" "CONFIG+=debug_and_release" dm.pro
```

其中的 INCLUDEPATH 和 LIBS 变量目录需要根据本机环境进行更改。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221229142041C2H70X9935D3WF9NTB)

  3. 编译。执行 make debug 命令，编译完成后会生成 libqsqldm.so 文件，生成目录由 Makefile.Debug 的 DESTDIR 变量决定。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214145028M7NDO60DSAE7DUCWNQ)

  4. 拷贝 libqsqldm.so 文件到对应目录。QT 的数据库插件文件一般放在 $QTDIR/gcc_64/plugins/sqldrivers/ 目录，因此编译完成后需要拷贝文件到此目录，否则 QT 程序会无法加载。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214145103LMJQYV5J2XOJ4O5VRQ)

## 三、 数据库连接

上面我们已经完成了 QDM 的编译，下面我们就通过一个简单的例子来检查插件是否能够正常使用。

### 3.1 新建工程

使用 qtcreator 工具新建一个 Qt Console AppLication 工程 qdmtest：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212141451583ADPTX9Q2A1EW2WCYD)

### 3.2 编写代码

因为需要访问 QT SQL 模块，所以需要在 qdmtest.pro 文件中添加：

```
QT += sql
INCLUDEPATH += /opt/Qt5.12.1/5.12.1/gcc_64/include/QtSql
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214145237T0H8I52KAVM0C4XURG)

main.cpp 编译代码进行登录数据库并查询数据库版本，注意此处指定 driver 为 QDM：

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241223123913K13SRP2OGUD70XAY48)

### 3.3 编译运行

选中项目右键进行构建编译，也可以在终端通过命令进行编译，编译成功后即可运行。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2022121414532704M5FZ2QU1K4GOPPF9)

## 四、开发示例

### 4.1 基础操作示例

具体代码示例参考：[QODBC 编译](https://eco.dameng.com/document/dm/zh-cn/app-dev/c_c++_qodbc.html)。

代码段仅需要做如下修改即可执行：

```
原 QODBC 接口：QSqlDatabase db = QSqlDatabase::addDatabase("QODBC");
QDM 接口：QSqlDatabase db = QSqlDatabase::addDatabase("QDM");
```

## 五、常见问题

1. QT SQL 类头文件未找到

【问题解决】：需要在 .pro 文件添加 `INCLUDEPATH += $QTDIR/gcc_64/include/QtSql`。

2. 对 'QSqlDatabase::defaultConnection' 未定义的引用

【问题解决】：这是因为编译时未连接 QT SQL 动态库导致，在 .pro 文件添加 `QT += sql` 解决。

3. 编译报错： “cannot find –lGL”

【问题解决】：动态库路径不对导致，QT 默认查找 /usr/lib 目录下文件，可以通过 locate 定位，然后 ln -s 创建软连接解决。

4. QSqlDatabase: QDM driver not loaded

【问题解决】：未加载到 libqsqldm.so 文件，需要拷贝此文件到正确目录：

```
cp libqsqldm.so $QTDIR/gcc_64/plugins/sqldrivers
```

5. 提示缺少文件：No such file or directory

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221229142130OMXQGINYDKOXM1O2WM)

```
[root@localhost dm]# /data/QT 5.12.1/5.12.1/gcc_64/bin/qmake "INCLUDEPATH+=/home/dmdba/dmdbms/include " " LIBS +=/home/dmdba/dmdbms/include/libdmdpi.a  -ldl " "DEFINES+=DM64" "CONFIG+=debug_and_release" dm.pro
Cannot read /data/QT 5.12.1/5.12.1/Src/qtbase/src/plugins/sqldrivers/qtsqldrivers-config.pri : No such file or directory 
[root@localhost dm]# 
```

【问题解决】：修改如下文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221229142159KFBS3UK5SD0LVBOLVC)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221229142213BK3UKL9HE9ASCFZ65J)

## 六、参考

  1. 示例代码下载：[qt_qdm_code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221222115846AVT00UAJDUDB2XL36G)。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。





# C&C++ 语言

## 前言

本章节主要介绍 C/C++ 应用开发常见问题，为用户提供 C/C++ 应用开发常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [动态链接库文件不存在](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E5%8A%A8%E6%80%81%E9%93%BE%E6%8E%A5%E5%BA%93%E6%96%87%E4%BB%B6%E4%B8%8D%E5%AD%98%E5%9C%A8)
  * [ODBC 连接失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E8%BF%9E%E6%8E%A5%E5%A4%B1%E8%B4%A5)
  * [Qt 包缺失](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#Qt%20%E5%8C%85%E7%BC%BA%E5%A4%B1)
  * [QT SQL 类头文件未找到](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#QT%20SQL%20%E7%B1%BB%E5%A4%B4%E6%96%87%E4%BB%B6%E6%9C%AA%E6%89%BE%E5%88%B0)
  * [对 ​'QSqlDatabase::defaultConnection​' 未定义的引用](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E5%AF%B9%20'QSqlDatabase::defaultConnection'%20%E6%9C%AA%E5%AE%9A%E4%B9%89%E7%9A%84%E5%BC%95%E7%94%A8)
  * [编译报错 cannot find –lGL](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E7%BC%96%E8%AF%91%E6%8A%A5%E9%94%99%20cannot%20find%20%E2%80%93lGL)
  * [QSqlDatabase: QDM driver not loaded](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#QSqlDatabase:%20QDM%20driver%20not%20loaded)
  * [提示缺少如下文件](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E6%8F%90%E7%A4%BA%E7%BC%BA%E5%B0%91%E5%A6%82%E4%B8%8B%E6%96%87%E4%BB%B6)
  * [ODBC 如何指定 schema](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E5%A6%82%E4%BD%95%E6%8C%87%E5%AE%9A%20schema)
  * [使用 ODBC 连接时候，报错返回 segmentation fault (core dumped)](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E4%BD%BF%E7%94%A8%20ODBC%20%E8%BF%9E%E6%8E%A5%E6%97%B6%E5%80%99%EF%BC%8C%E6%8A%A5%E9%94%99%E8%BF%94%E5%9B%9E%20segmentation%20fault%20\(core%20dumped\))
  * [odbc_for_dm 怎么支持 utf-8 ?](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#odbc_for_dm%20%E6%80%8E%E4%B9%88%E6%94%AF%E6%8C%81%20utf-8%20?)
  * [debian11 使用 ODBC 报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#debian11%20%E4%BD%BF%E7%94%A8%20ODBC%20%E6%8A%A5%E9%94%99)
  * [连接 ODBC 必须要安装数据库吗？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E8%BF%9E%E6%8E%A5%20ODBC%20%E5%BF%85%E9%A1%BB%E8%A6%81%E5%AE%89%E8%A3%85%E6%95%B0%E6%8D%AE%E5%BA%93%E5%90%97%EF%BC%9F)
  * [Access 迁移到 DM，报错 can not find driver: sun.jdbc.odbc.JdbcOdbcDriver ](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#Access%20%E8%BF%81%E7%A7%BB%E5%88%B0%20DM%EF%BC%8C%E6%8A%A5%E9%94%99%20can%20not%20find%20driver:%20sun.jdbc.odbc.JdbcOdbcDriver%C2%A0)
  * [DCI 接口关于 LOB 字段示例](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#DCI%20%E6%8E%A5%E5%8F%A3%E5%85%B3%E4%BA%8E%20LOB%20%E5%AD%97%E6%AE%B5%E7%A4%BA%E4%BE%8B)
  * [未发现数据源名称并且未指定默认驱动程序](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E6%9C%AA%E5%8F%91%E7%8E%B0%E6%95%B0%E6%8D%AE%E6%BA%90%E5%90%8D%E7%A7%B0%E5%B9%B6%E4%B8%94%E6%9C%AA%E6%8C%87%E5%AE%9A%E9%BB%98%E8%AE%A4%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F)
  * [DSN 中，驱动程序和应用程序之间的体系结构不匹配](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#DSN%20%E4%B8%AD%EF%BC%8C%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F%E5%92%8C%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E4%B9%8B%E9%97%B4%E7%9A%84%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84%E4%B8%8D%E5%8C%B9%E9%85%8D)
  * [ODBC 连接 DM 数据库，报错：加密模块加载失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E8%BF%9E%E6%8E%A5%20DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%EF%BC%8C%E6%8A%A5%E9%94%99%EF%BC%9A%E5%8A%A0%E5%AF%86%E6%A8%A1%E5%9D%97%E5%8A%A0%E8%BD%BD%E5%A4%B1%E8%B4%A5)
  * [communication error](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#communication%20error)
  * [通过指定字符集的字符串获取字符集的 ID](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E9%80%9A%E8%BF%87%E6%8C%87%E5%AE%9A%E5%AD%97%E7%AC%A6%E9%9B%86%E7%9A%84%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%8E%B7%E5%8F%96%E5%AD%97%E7%AC%A6%E9%9B%86%E7%9A%84%20ID)
  * [ODBC 连接 DM 数据库报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E8%BF%9E%E6%8E%A5%20DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99)
  * [ODBC 连接 DM8 时报错： 无效的配置值（Invalid config value）](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E8%BF%9E%E6%8E%A5%20DM8%20%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%20%E6%97%A0%E6%95%88%E7%9A%84%E9%85%8D%E7%BD%AE%E5%80%BC%EF%BC%88Invalid%20config%20value%EF%BC%89)
  * [ODBC 连接 DM8 报错 Data source name not found and no default driver specified](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E8%BF%9E%E6%8E%A5%20DM8%20%E6%8A%A5%E9%94%99%20Data%20source%20name%20not%20found%20and%20no%20default%20driver%20specified)
  * [ODBC 连接 DM8 数据库有中文数据插入时报错：[ISQL]ERROR: Could not SQLPrepare](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E8%BF%9E%E6%8E%A5%20DM8%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9C%89%E4%B8%AD%E6%96%87%E6%95%B0%E6%8D%AE%E6%8F%92%E5%85%A5%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A\[ISQL\]ERROR:%20Could%20not%20SQLPrepare)
  * [DmBulkCopoy 提示 [The faseloading dll not loading]](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#DmBulkCopoy%20%E6%8F%90%E7%A4%BA%20\[The%20faseloading%20dll%20not%20loading\])
  * [Visual Studio 连接达梦数据库有没有专门的 DLL](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#Visual%20Studio%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9C%89%E6%B2%A1%E6%9C%89%E4%B8%93%E9%97%A8%E7%9A%84%20DLL)
  * [语句句柄个数超上限或系统内存不足](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E8%AF%AD%E5%8F%A5%E5%8F%A5%E6%9F%84%E4%B8%AA%E6%95%B0%E8%B6%85%E4%B8%8A%E9%99%90%E6%88%96%E7%B3%BB%E7%BB%9F%E5%86%85%E5%AD%98%E4%B8%8D%E8%B6%B3)
  * [DM 是否能连接最新的 NetCore3.1 和 net5](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#DM%20%E6%98%AF%E5%90%A6%E8%83%BD%E8%BF%9E%E6%8E%A5%E6%9C%80%E6%96%B0%E7%9A%84%20NetCore3.1%20%E5%92%8C%20net5)
  * [dpi_login 登陆数据库失败，获取错误信息描述为 70089 “加密模块加载失败”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#dpi_login%20%E7%99%BB%E9%99%86%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A4%B1%E8%B4%A5%EF%BC%8C%E8%8E%B7%E5%8F%96%E9%94%99%E8%AF%AF%E4%BF%A1%E6%81%AF%E6%8F%8F%E8%BF%B0%E4%B8%BA%2070089%20%E2%80%9C%E5%8A%A0%E5%AF%86%E6%A8%A1%E5%9D%97%E5%8A%A0%E8%BD%BD%E5%A4%B1%E8%B4%A5%E2%80%9D)
  * [应用报错 -30014](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E5%BA%94%E7%94%A8%E6%8A%A5%E9%94%99%20-30014)
  * [用 occi 接口操作数据库，执行 getstring 方法时，开发工具崩溃](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E7%94%A8%20occi%20%E6%8E%A5%E5%8F%A3%E6%93%8D%E4%BD%9C%E6%95%B0%E6%8D%AE%E5%BA%93%EF%BC%8C%E6%89%A7%E8%A1%8C%20getstring%20%E6%96%B9%E6%B3%95%E6%97%B6%EF%BC%8C%E5%BC%80%E5%8F%91%E5%B7%A5%E5%85%B7%E5%B4%A9%E6%BA%83)
  * [proc*c 调用 fetch 异常](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#proc*c%20%E8%B0%83%E7%94%A8%20fetch%20%E5%BC%82%E5%B8%B8)
  * [windows VS 下编译运行 dpi 程序](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#windows%20VS%20%E4%B8%8B%E7%BC%96%E8%AF%91%E8%BF%90%E8%A1%8C%20dpi%20%E7%A8%8B%E5%BA%8F)
  * [sudo -u 指定其它用户后通过 ODBC 连接提示找不到动态库文件](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#sudo%20-u%20%E6%8C%87%E5%AE%9A%E5%85%B6%E5%AE%83%E7%94%A8%E6%88%B7%E5%90%8E%E9%80%9A%E8%BF%87%20ODBC%20%E8%BF%9E%E6%8E%A5%E6%8F%90%E7%A4%BA%E6%89%BE%E4%B8%8D%E5%88%B0%E5%8A%A8%E6%80%81%E5%BA%93%E6%96%87%E4%BB%B6)
  * [配置 ODBC 数据源执行 isql DM8 报错：数据库 DM8 不存在](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E9%85%8D%E7%BD%AE%20ODBC%20%E6%95%B0%E6%8D%AE%E6%BA%90%E6%89%A7%E8%A1%8C%20isql%20DM8%20%E6%8A%A5%E9%94%99%EF%BC%9A%E6%95%B0%E6%8D%AE%E5%BA%93%20DM8%20%E4%B8%8D%E5%AD%98%E5%9C%A8)
  * [配置 ODBC 数据源执行 isql 报错：当前系统下没有 libreadline.so.8 库文件](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E9%85%8D%E7%BD%AE%20ODBC%20%E6%95%B0%E6%8D%AE%E6%BA%90%E6%89%A7%E8%A1%8C%20isql%20%E6%8A%A5%E9%94%99%EF%BC%9A%E5%BD%93%E5%89%8D%E7%B3%BB%E7%BB%9F%E4%B8%8B%E6%B2%A1%E6%9C%89%20libreadline.so.8%20%E5%BA%93%E6%96%87%E4%BB%B6)
  * [配置 ODBC 数据源执行 isql 报错：找不到 libodbc.so 文件和生成环境句柄失败](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E9%85%8D%E7%BD%AE%20ODBC%20%E6%95%B0%E6%8D%AE%E6%BA%90%E6%89%A7%E8%A1%8C%20isql%20%E6%8A%A5%E9%94%99%EF%BC%9A%E6%89%BE%E4%B8%8D%E5%88%B0%20libodbc.so%20%E6%96%87%E4%BB%B6%E5%92%8C%E7%94%9F%E6%88%90%E7%8E%AF%E5%A2%83%E5%8F%A5%E6%9F%84%E5%A4%B1%E8%B4%A5)
  * [qt 工具中运行连接数据库程序报错：不能打开 libdodbc.so , 文件 QODBC3 没找到，无法连接](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#qt%20%E5%B7%A5%E5%85%B7%E4%B8%AD%E8%BF%90%E8%A1%8C%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E7%A8%8B%E5%BA%8F%E6%8A%A5%E9%94%99%EF%BC%9A%E4%B8%8D%E8%83%BD%E6%89%93%E5%BC%80%20libdodbc.so%20,%20%E6%96%87%E4%BB%B6%20QODBC3%20%E6%B2%A1%E6%89%BE%E5%88%B0%EF%BC%8C%E6%97%A0%E6%B3%95%E8%BF%9E%E6%8E%A5)
  * [通过 gdb 命令找 sql 参数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E9%80%9A%E8%BF%87%20gdb%20%E5%91%BD%E4%BB%A4%E6%89%BE%20sql%20%E5%8F%82%E6%95%B0)
  * [使用 gbd 获取堆栈里的完整 sql](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E4%BD%BF%E7%94%A8%20gbd%20%E8%8E%B7%E5%8F%96%E5%A0%86%E6%A0%88%E9%87%8C%E7%9A%84%E5%AE%8C%E6%95%B4%20sql)
  * [QT-ODBC 无法连接数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#QT-ODBC%20%E6%97%A0%E6%B3%95%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [ODBC 连接达梦数据库时如何添加连接串的自定义参数](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E6%97%B6%E5%A6%82%E4%BD%95%E6%B7%BB%E5%8A%A0%E8%BF%9E%E6%8E%A5%E4%B8%B2%E7%9A%84%E8%87%AA%E5%AE%9A%E4%B9%89%E5%8F%82%E6%95%B0)
  * [ODBC 程序进行日期查询时日期毫秒值尾数 0 值被截断](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E7%A8%8B%E5%BA%8F%E8%BF%9B%E8%A1%8C%E6%97%A5%E6%9C%9F%E6%9F%A5%E8%AF%A2%E6%97%B6%E6%97%A5%E6%9C%9F%E6%AF%AB%E7%A7%92%E5%80%BC%E5%B0%BE%E6%95%B0%200%20%E5%80%BC%E8%A2%AB%E6%88%AA%E6%96%AD)
  * [ODBC 接口开发报错缺失 sql.h 和 sqltypes.h 头文件](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E6%8E%A5%E5%8F%A3%E5%BC%80%E5%8F%91%E6%8A%A5%E9%94%99%E7%BC%BA%E5%A4%B1%20sql.h%20%E5%92%8C%20sqltypes.h%20%E5%A4%B4%E6%96%87%E4%BB%B6)
  * [Windows server 操作系统上 odbc 测试连接报错：​"加密模块加载失败​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#Windows%20server%20%E6%93%8D%E4%BD%9C%E7%B3%BB%E7%BB%9F%E4%B8%8A%20odbc%20%E6%B5%8B%E8%AF%95%E8%BF%9E%E6%8E%A5%E6%8A%A5%E9%94%99%EF%BC%9A%22%E5%8A%A0%E5%AF%86%E6%A8%A1%E5%9D%97%E5%8A%A0%E8%BD%BD%E5%A4%B1%E8%B4%A5%22)
  * [如何查看应用系统使用的 ODBC/OCI/DPI 的驱动版本号](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E5%A6%82%E4%BD%95%E6%9F%A5%E7%9C%8B%E5%BA%94%E7%94%A8%E7%B3%BB%E7%BB%9F%E4%BD%BF%E7%94%A8%E7%9A%84%20ODBC/OCI/DPI%20%E7%9A%84%E9%A9%B1%E5%8A%A8%E7%89%88%E6%9C%AC%E5%8F%B7)
  * [C 编程直接调用 ctl 文件方式导入数据示例](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#C%20%E7%BC%96%E7%A8%8B%E7%9B%B4%E6%8E%A5%E8%B0%83%E7%94%A8%20ctl%20%E6%96%87%E4%BB%B6%E6%96%B9%E5%BC%8F%E5%AF%BC%E5%85%A5%E6%95%B0%E6%8D%AE%E7%A4%BA%E4%BE%8B)
  * [proc 中将 varchar2 类型字段 into 到变量中时会补空格到最大长度要怎么处理？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#proc%20%E4%B8%AD%E5%B0%86%20varchar2%20%E7%B1%BB%E5%9E%8B%E5%AD%97%E6%AE%B5%20into%20%E5%88%B0%E5%8F%98%E9%87%8F%E4%B8%AD%E6%97%B6%E4%BC%9A%E8%A1%A5%E7%A9%BA%E6%A0%BC%E5%88%B0%E6%9C%80%E5%A4%A7%E9%95%BF%E5%BA%A6%E8%A6%81%E6%80%8E%E4%B9%88%E5%A4%84%E7%90%86%EF%BC%9F)
  * [ODBC 应用程序执行 SQLConnect 函数时报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#ODBC%20%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E6%89%A7%E8%A1%8C%20SQLConnect%20%E5%87%BD%E6%95%B0%E6%97%B6%E6%8A%A5%E9%94%99)
  * [找不到 libodbcinst.so 动态库文件](https://eco.dameng.com/document/dm/zh-cn/faq/faq-c-new.html#%E6%89%BE%E4%B8%8D%E5%88%B0%20libodbcinst.so%20%E5%8A%A8%E6%80%81%E5%BA%93%E6%96%87%E4%BB%B6)



## 正文

### 动态链接库文件不存在

【问题描述】

odbcinstall.ini 配置文件编写错误，无法找到文件。

报错信息如下图：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405281106550DBOVAA1LMXYRPZOCX)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps1.jpg)

【问题解决】

修改 odbcinstall.ini 配置文件。

### ODBC 连接失败

【问题描述】

报错信息如下图：

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405281107110JQ74NW995YH76ILPA)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps2.jpg)

【问题解决】

odbc.ini 文件中服务名配置错误，导致无法找到对应服务名，修改 odbc.ini 文件中服务名。

### Qt 包缺失

【问题描述】

报错信息如下图：

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052811091783XKONJN045U05FKZE)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps3.jpg)

【问题解决】

1．首先尽可能使用源码安装的完整版 qt ，否则会缺失基础的 libqsqlodbc.so ，如果没有安装源码的情况下使用系统自带的 qt ，要注意直接下载的源码 src 版本也要和现有 qt 版本一致，否则会出现头文件缺失等问题，该问题是目录结构不匹配导致；

2．保证 odbc 服务名能够正常进行 isql 测试，否则根据 isql -v dsn 来判断具体原因，大部分都是因为环境变量配置错误导致 libdodbc.so 动态库依赖缺失，arm 平台编译安装 unixodbc 注意加上 --build=arm-linux ；

3．避免系统中安装多版本 qt ，否则会导致环境混乱，libqsqlodbc.so 或者 libqsqldm.so 动态库放入 qt 的 sqldrivers 目录中还是无法识别为可用驱动，报错 driver not loaded ，可通过临时使用命令 export QT_DEBUG_PLUGINS=1 ，该窗口下运行程序会打印出具体 qt 的插件 debug 信息，从中看是否从正确的目录搜索到了对应动态库，另外对应动态库是否被识别为 plugins ，否则需要去考虑 qt 环境方面问题；

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528110947YQ4L7N1YW0Y2XT0YE1)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps4.png)

4．pro 文件中需要加上 qt+=sql 否则无法识别驱动；

5．尽量使用 root 用户打开 qtcreator ，避免其他用户环境变量问题导致无法识别一些依赖组件；

6．qt 中 kit 套件、数据库、odbc 位数需要保持一致。

### QT SQL 类头文件未找到

【问题描述】

QT SQL 类头文件未找到。

【问题解决】

需要在 .pro 文件添加 INCLUDEPATH += $QTDIR/gcc_64/include/QtSql 。

### 对 'QSqlDatabase::defaultConnection' 未定义的引用

【问题描述】

对 'QSqlDatabase::defaultConnection' 未定义的引用。

【问题解决】

因为编译时未连接 QT SQL 动态库导致，在 .pro 文件添加 QT+=sql 解决。

### 编译报错 cannot find –lGL

【问题描述】

编译报错 cannot find –lGL 。

【问题解决】

动态库路径不对导致，QT 默认查找 /usr/lib 目录下文件，可以通过 locate 定位，然后 ln -s 创建软连接解决。

### QSqlDatabase: QDM driver not loaded

【问题描述】

```
QSqlDatabase: QDM driver not loaded
```

【问题解决】

未加载到 libqsqldm.so 文件，需要拷贝此文件到正确目录：

```
cp libqsqldm.so $QTDIR/gcc_64/plugins/sqldrivers
```

### 提示缺少如下文件

【问题描述】

提示缺少文件，报错如下：

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528111314I0WIHLD97JGPV1V2S2)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps5.jpg)

【问题解决】

修改如下文件：

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052811132344ANF0B1N8M58FPV38)

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528111329H4E3D5B2TYJQWAUYUA)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps6.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps7.jpg)

### ODBC 如何指定 schema

【问题描述】

使用 ODBC 连接数据库，如何在 odbc.ini 中指定 schema 。

【问题解决】

如果是用户下默认模式，可以指定用户，在 odbc.ini 里面配置，如果不是默认模式，需要把模式写在 sql 中。

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528111417QLK8AECBNJC5IT0O0B)

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528111429Q9PJZ5N31U003ZM9NJ)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps8.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps9.jpg)

### 使用 ODBC 连接时候，报错返回 segmentation fault (core dumped)

【问题描述】

使用 odbc 连接时候，报错返回 segmentation fault (core dumped) 。

【问题解决】

  1. 检查下安装的达梦数据库版本与操作系统是否匹配。
  2. ldd /dm8/drivers/odbc/libdodbc.so 检查是否缺少依赖文件。



### odbc_for_dm 怎么支持 utf-8 ?

【问题描述】

在 Debian11 上安装好之后用 manager 创建 schema 等这些都没问题，但是系统是基于 odbc 的，请问 odbc_for_dm 怎么支持。

【问题解决】

dminit 实例时设置 charset 为 1(utf-8) 并且 export LANG=zh_CN.UTF-8 。

### debian11 使用 ODBC 报错

【问题描述】

出现下图报错：

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528111740SPD440GE9HMRNOSI2U)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps10.jpg)

【问题解决】

下载 odbc 压缩包后，yum install unixODBC-devel ，具体参考 [https://eco.dameng.com/document/dm/zh-cn/app-dev/c_c++_odbc.html](https://eco.dameng.com/document/dm/zh-cn/app-dev/c_c++_odbc.html) 。

### 连接 ODBC 必须要安装数据库吗？

【问题描述】

在麒麟 v10 上面安装了一个 DM8 数据库，配置 odbc 后用 isql 可以直接连接，然后将 bin 目录拷贝到一个统信系统上配置 odbc 使用 isql 命令后报错，创建 SOCKET 失败！如果需要通过 ODBC 连接数据库，是否必须要安装完整的达梦数据库包？

【问题解决】

拷贝 bin 目录到系统并加入到环境变量，再 ldd libdodbc.so 查询一下依赖包情况，如果出现 not found ，把相应的包拷贝到 driver 目录即可。

### Access 迁移到 DM，报错 can not find driver: sun.jdbc.odbc.JdbcOdbcDriver 

【问题描述】

如何从 Access 迁移到 DM，在进行第一步的时候报了 can not find driver: sun.jdbc.odbc.JdbcOdbcDriver 。

【问题解决】

使用 JDK8+JDBC 连接数据库提示 sun.jdbc.odbc.JdbcOdbcDriver 找不到的错误，原因是 jdk8 已经调整了 ODBC 的连接库，所以在 rt.jar 里面示找不到该类，解决办法是使用 jdk7 替换 jdk8 。

具体步骤：

  1. 下载 JDK7 或 JRE7 。



转到 JRE\lib 文件夹并找到 rt.jar 。

将其解压缩（如果您安装了 WinRAR 或 7zip ），或者您可以将其重命名为 rt.zip 并将其解压缩。

复制 sun\jdbc 和 sun\security\action 文件夹，保留文件夹结构，即你应该有如下的文件夹结构：

Sun --> Security --> Action

Sun --> JDBC

  2. 打开一个 CMD 窗口。转到 Sun 文件夹的父文件夹。运行命令：jar -cvf jdbc.jar sun 。



上述命令将创建一个名为 jdbc.jar 的文件。

将 JDBC.jar 复制到您的 JDK8 或 JRE8\lib 文件夹。如果这不起作用，请尝试使用该 lib\ext 文件夹。

将 jdbcodbc.dll 从 JRE7 安装的 JRE\bin 复制到 JRE8 安装的 JRE\bin 。

  3. 重新启动您的 JVM 。



### DCI 接口关于 LOB 字段示例

【问题解决】

LOB 方法：

OCILobGetLength

用途：返回大字段的长度，按字节计算。

格式如下：

```
sword OCILobGetLength ( OCISvcCtx *svchp, OCIError*errhp, OCILobLocator *locp, ub4*lenp );
```

OCILobRead

用途：读取某个大字段中指定长度的内容。

格式如下：

```
 sword OCILobRead ( OCISvcCtx *svchp,
    OCIError             *errhp,  
    OCILobLocator   *locp,  
    ub4                     *amtp,  
    ub4                     offset,  
    dvoid                  *bufp,  
    ub4                     bufl,  
    dvoid                  *ctxp,  
    OCICallbackLobRead (cbfp)  
           ( dvoid               *ctxp,
            CONST dvoid   *bufp,  
            ub4                   len,  
            ub1                   piece )  
    ub2                    csid,  
    ub1                    csfrm );
```

### 未发现数据源名称并且未指定默认驱动程序

【问题描述】

```
[IM002][unixODBC][Driver Manager]Data source name not found, and no default driver specified.
```

【问题解决】

第一种原因：odbc.ini 配置文件本身有问题，比如名称有空格。（ odbc.ini 和 odbcinst.ini 中每行开头禁止留有空格）

第二种原因：配置文件路径不对，请参考 [Linux 平台配置 ODBC 连接达梦数据库](https://eco.dameng.com/docs/zh-cn/article/article-003.html) 。

/etc 下的 odbc.ini 文件，要是 linux 格式的文档。

linux 上部署 ODBC 需要有 DM 数据库的驱动，要装一个 DM 数据库。

参考技术链接：[ODBC 接口](https://eco.dameng.com/document/dm/zh-cn/app-dev/c_c++_odbc) 。

### DSN 中，驱动程序和应用程序之间的体系结构不匹配

【问题描述】

报错：[Microsoft][ODBC 驱动程序管理器] 在指定的 DSN 中，驱动程序和应用程序之间的体系结构不匹配，或者 ODBC 数据源管理程序里没找到 DM 驱动。

【问题解决】

Win64 位系统有两个 ODBC 数据源管理器，分别为 64 位和 32 位。

64 位 ODBC 数据源管理器路径是 C:\WINDOWS\system32\odbcad32.exe 。

32 位 ODBC 数据源管理器路径是 C:\WINDOWS\SysWOW64\odbcad32.exe 。

Win64 位系统控制面板默认打开的 ODBC 数据源管理器是 64 位，在 Win64 系统安装 DM 版本后通过控制面板打开的 ODBC 数据源管理器发现找不到 ODBC 驱动，一般是由于安装的 DM 版本为 32 位，所以 64 位 ODBC 数据源管理器看不到，需要启动 32 位数据源管理器才可看到 ODBC 驱动。

64 位应用程序只能调用 64 位 ODBC 驱动；

32 位应用程序只能调用 32 位 ODBC 驱动；

64 位应用程序调用 32 位 ODBC 驱动或 32 位应用程序调用 64 位 ODBC 驱动，都会提示以下错误：

```
Error: [Microsoft][ODBC 驱动程序管理器] 在指定的 DSN 中，驱动程序和应用程序之间的体系结构不匹配。
```

### ODBC 连接 DM 数据库，报错：加密模块加载失败

【问题分析】

该问题是由于连接数据库时，调用 OpenSSL 加密动态库失败，针对该种报错，解决办法如下：

Windows 环境下

确认达梦安装目录下是否有 libeay32.dll ，并且确认环境变量是否包含达梦安装目录，也可以将对应的动态库放置到 c:\windows\system32 目录下。

Linux 环境下

ODBC 接口调用依赖 libcrypt.so ，程序执行时找不到 libcrypt.so 就会报错：加密动态库失败。
首先，检查 bin 目录下是否存在 libcrypt.so 。
其次，确保 LD_LIBRARY_PATH 环境变量下包含达梦的安装目录 bin 目录。如：

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/dmdba/dmdbms/bin
```

上述环境变量设置可以添加到对应用户的环境变量配置文件中。

【问题解决】

检查 bin 目录下存在 libcrypt.so ，以及设置环境变量 LD_LIBRARY_PATH 指向 dmdbms/bin 目录。

### communication error

【问题描述】

DM 数据库，MAX_SESSION=65000，在程序中没有设置 timeout 超时限制，C# 在插入数据库时会提示这个错误 communication error，循环插入前三万条可以，三万后就提示这个错误。

【问题解决】

检查 MAX_SESSION_STATEMENT 参数，检查每次插入是否新开了一个句柄，通过 select MAX(N_USED_STMT) from v$sessions；进行查看。

### 通过指定字符集的字符串获取字符集的 ID

【问题描述】

在进行对 DM 数据库编程时，使用 DM 的 DCI 用于兼容 Oracle 数据库的代码，使用 Oracle OCI 接口进行编程的时候，可以通过 OCINlsCharSetNameToId 方法获取指定字符集的 id ，在 DM 的 DCI 中未找到这个方法，在 DM 程序员手册也没有找到相关的 API 的说明。

【问题解决】

因为 DM 没有 NlsCharSetName 这个方法。DM 可以使用 SQL 语句 select UNICODE(); 来获取指定字符集 id 。如果有兼容性需求，可通过对应销售或技术提需求。

### ODBC 连接 DM 数据库报错

【问题描述】

ODBC 连接 DM 报错：can't open lib'/opt/dmdbms/bin/libdodbc.so':file not found 或者找不到 .so 文件 error：could not SQLConnect

【问题解决】

ldd /opt/dmdbms/bin/libdodbc.so 检查 libdodbc.so 依赖的文件是否有问题。

依赖的 so 文件存在，但 ldd 仍提示找不到，一般是环境变量 LD_LIBRARY_PATH 设置问题。

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528113304AMXO5T1NRARYVRM5RE)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps11.jpg)

解决方式：

  1. 切换成数据库用户 dmdba 设置环境变量。



```
vim ~/.bash_profile
```

  2. 添加环境变量。



```
export DM_HOME="/home/dmdba/dmdbms"
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/dmdba/dmdbms/bin"
```

  3. 使环境变量生效。



```
[dmdba@localhost ~]$ source ~/.bash_profile
```

  4. 重新连接，成功执行。



### ODBC 连接 DM8 时报错： 无效的配置值（Invalid config value）

【问题描述】

ODBC 连接 DM8，执行程序报错 Failed to connect to database: 无效的配置值（Invalid config value）。

报错信息如下图：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240628130100ERZVZA54TFEUA9QY3N)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps12.jpg)

【问题分析】

该问题可能由以下几种情况导致：

  1. 配置用户名或密码无效;
  2. 配置的数据库连接字符串的格式不正确；
  3. 防火墙或网络问题；
  4. 缺少必需的 ODBC 驱动程序；
  5. 缺少环境变量。



【问题解决】

配置的用户名或密码无效：请确保指定的用户名和密码正确，如果使用的是 Windows 身份验证，则需要使用 Windows 域用户账户和密码；

配置的数据库连接字符串的格式不正确：请检查使用的连接字符串是否正确。例如，如果使用的是 DSN ，则请确保 DSN 的名称正确，并且在 DSN 的属性中指定了正确的数据库名称、用户名和密码；

防火墙或网络问题：检查防火墙是否关闭，网络是否通畅；

缺少必需的 ODBC 驱动程序：如果正在使用 ODBC 驱动程序连接数据库，则需要确保安装了正确的 ODBC 驱动程序。如果缺少所需的驱动程序，则连接尝试可能会失败，并显示 “Invalid config value” 错误。

缺少环境变量：为 ODBC 启动用户配置 LD_LIBRARY_PATH 环境添加 ODBC lib 目录（具体路径请按照实际情况配置）：

```
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/usr/local/unixODBC-arm/lib/
```

注意:代码中 SQLConnect 函数的 host 属性不能写 ip，要写配置的 dsn 。

### ODBC 连接 DM8 报错 Data source name not found and no default driver specified

【问题描述】

ODBC 连接 DM8，执行程序时报错 ：Data source name not found and no default driver specified

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps13.jpg)

【问题分析】

该报错可能由两种情况导致：

本地机器存在多套 odbc 驱动版本，识别到错误的或者没有识别到达梦 odbc 驱动的路径；

odbcinst.ini 与 odbc.ini 文件配置有误。

【问题解决】

检查 odbcinst.ini 与 odbc.ini 配置文件，确保配置无误，如果确认配置文件存在问题，修改后重新测试连接；

如果 odbcinst.ini 与 odbc.ini 配置文件正确还是无法连接，可以调整 /usr/bin 目录下 isql 引用，调整命令:

```
cd /usr/local/unixODBC-2.3.0/exe --（odbc的路径）
cp -r isql /usr/bin/
```

通过账号和密码方式登录检查：

```
isql DM8 SYSDBA SYSDB
```

### ODBC 连接 DM8 数据库有中文数据插入时报错：[ISQL]ERROR: Could not SQLPrepare

【问题描述】

ODBC 连接 DM8 数据库进行 insert 操作，当插入数据中包含中文时报错：[ISQL]ERROR: Could not SQLPrepare

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps14.jpg)

【问题分析】

该报错可能由以下两种情况导致：

使用的 odbc 版本驱动与数据库版本不匹配；

用户的字符集与数据库编码不同导致乱码报错。

【问题解决】

更换与数据库版本相匹配的 odbc 驱动；

修改用户字符集与数据库编码一致，用户默认编码为 gbk 。

例如：当数据库字符集为 utf-8 时通过以下命令修改用户的字符集为 utf-8 。

```
export LANG=zh_CN.UTF-8
```

### DmBulkCopoy 提示 [The faseloading dll not loading]

【问题描述】

DmBulk 提示 [The faseloading dll not loading] ，然后在 readme.txt 里面得知要引用 dmfldr_dll.dll ，但是找不到这个 dll 。

【问题解决】

Linux 中把 DM 数据库的路径加到环境变量里面去。

Windows 的环境就把环境变量 path 加上 DM 数据库的路径。

Visual Studio 连接达梦数据库：

（1）Linux 环境下 odbc 对应外部库文件 libdodbc.so 。

（2）Windows 环境下 odbc 对应外部库文件 DODBC.DLL 。

### Visual Studio 连接达梦数据库有没有专门的 DLL

【问题解决】

没有专门的，Linux 是 libdodbc.so ;  Windows 是 DODBC.DLL 。

### 语句句柄个数超上限或系统内存不足

【问题描述】

ODBC 访问达梦数据库，执行 SQL 语句报错：语句句柄个数超上限或系统内存不足。

【问题解决】

SQL 执行完成、断开连接前一定要释放语句句柄。“语句句柄个数超上限或系统内存不足”报错原因大部分是应用程序在断开连接前没有释放语句句柄导致。

### DM 是否能连接最新的 NetCore3.1 和 net5

【问题解决】

DM8 可以连接最新的 NetCore3.1 和 net5 ，从[达梦云适配中心](https://eco.dameng.com/download/?_blank)下载最新版本数据库。

### dpi_login 登陆数据库失败，获取错误信息描述为 70089 “加密模块加载失败”

【问题解决】

这个是因为拷贝的动态库缺少第三方依赖导致的，建议将 dmdbms/bin 目录也加到环境变量中即可。

### 应用报错 -30014

【问题描述】

现象表现是在调用测试程序时发生如下报错：

![图片 12.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528152153Y4ULDKSJ737ZHEH8TD)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps15.jpg)

【问题分析】

在 DPC 绑定参数时，由于送入的参数长度等信息为 INT 类型，DPC 绑定函数需要 INT64 类型参数，发生强制转换时，INT 的高四位填充偶发出现不是 0 的情况，导致送入参数值不正确，绑定失败执行报错。

【问题解决】

在进行编译时，需要添加 DM64 选项，否则会有风险，引发上述问题。详情请见文档《DM8-PROC》（位于数据库安装目录下的 doc）的 1.3.3 部分。

![图片 13.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528152220EEJV2H8EDFMFT0W2PQ)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps16.jpg)

清理环境之后重新进行编译，添加 DM64 选项，即可避免上述错误；

### 用 occi 接口操作数据库，执行 getstring 方法时，开发工具崩溃

【问题描述】

使用 vs2019 或者 2013 开发工具，利用 occi 接口操作数据库，执行 getstring 方法时，开发工具崩溃。

【问题解决】

需要根据 vs 开发工具版本申请对应的 occi 版本，请自行下载对应的 occi 驱动包。

### proc*c 调用 fetch 异常

【问题描述】

代码如下：

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
include <strings.h>
#include <memory.h>
#include <stdarg.h
#include <sqlca.h>
#include <sqlda.h>

EXEC SQL INCLUDE SQLDA;

int db_connect(char *login)
{
    EXEC SQL BEGIN DECLARE SECTION;
    char  *logname = login;
    EXEC SQL END DECLARE SECTION;
    int  i = 0;
    printf("login: %s\n", logname);
    EXEC SQL CONNECT :logname;
    if (sqlca.sqlcode != 0) {
        printf("%s\n", sqlca.sqlerrm.sqlerrmc);
        return(-1);
    }
    return(0);
}

char col1[100][30], col2[100][30];

void fetchdata() {
    EXEC SQL FETCH DYN_SQL_C1 INTO :col1, :col2;
    printf("fetch %d\n", sqlca.sqlcode);
}

void main(int argc, char **argv) {
    if (argc != 2)
        exit(1);

    if (db_connect(argv[1]) < 0)
        exit(1);

    char psql[1024];
    strcpy(psql, "SELECT ROWNUM,to_char(sysdate,'yyyymmdd') optime FROM dual CONNECT BY ROWNUM<=30");
    EXEC SQL PREPARE DYN_SQL_01 FROM :psql;
    printf("prepare %d\n", sqlca.sqlcode);
    EXEC SQL DECLARE DYN_SQL_C1 CURSOR FOR DYN_SQL_01;
    printf("declare %d\n", sqlca.sqlcode);
    EXEC SQL OPEN DYN_SQL_C1;
    printf("open %d\n", sqlca.sqlcode);
    fetchdata();
    int ret_count = sqlca.sqlerrd[2];
    int i;
    for (i = 0; i < ret_count; i++) {
        printf("%d=%s,%s=\n", i, col1[i], col2[i]);
    }
    exit(0);
}
```

编译 pc 文件：

```
./dpc_new MODE=ORACLE file=a.pc
gcc -I/home/dmdba/dmdbms/include -g -L/home/dmdba/dmdbms/bin -ldmdpc -o a.out a.c
```

执行：

```
./a.out "SYSDBA/SYSDBA@192.168.104.72:5236"
此时报错：Segmentation fault
```

【问题分析】

应用程序编译缺少 DM64 宏，调用 64 位 dpc 动态库引发异常。

如果在 64 位机器上编译，需要加上 DM64 编译选项。由于 dpc_new 在编译时需要使用 DPItypes.h ，而在 DPItypes.h 中定义了 slength 和 ulength 类型，如下所示：

```
#ifdef DM64typedef sdint8 slength;
typedef udint8 ulength;
#define SLENGTH_MAX SDINT8_MAX
#define ULENGTH_MAX UDINT8_MAX
#elsetypedef sdint4 slength;
typedef udint4 ulength;
#define SLENGTH_MAX SDINT4_MAX
#define ULENGTH_MAX UDINT4_MAX#endif
```

因此，如果 64 位环境下编译应用时没加 DM64 宏，会导致 DPI 接口调用异常。

【问题解决】

gcc 编译时加上 DM64 参数：

```
gcc -I/home/dmdba/dmdbms/include -g -L/home/dmdba/dmdbms/bin -ldmdpc -o a.out a.c -D DM64
```

### windows VS 下编译运行 dpi 程序

【问题解决】

以下演示 windows 64 位系统，用 VS 创建 c++ 项目工程，添加源文件，编译运行 dpi 程序。

程序在编译的过程中需要用到 DM 的头文件 DPI.h 、DPIext.h 、DPItypes.h ，在链接阶段需要用到 dmdpi.lib 这个库文件，在执行阶段需要用到动态库 dmdpi.dll 以及 dmcalc.dll 、dmcomm.dll 、dmcyt.dll 、dmclientlex.dll 、dmcvt.dll 、dmelog.dll 、dmmem.dll 、dmmsg.dll 、dmos.dll 、dmutl.dll 。这几个动态库在安装 DM 时，已经被放到安装目录下。

  1. 添加头文件



选择属性——c/c++——附加包含目录，加入头文件路径。

![图片 14.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405281530546LN3CHLROWPACB7ARY)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps17.jpg)

  2. 头文件右键添加现有项，添加三个头文件。



![图片 15.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528153105OANRCQPETGFDR1TCXV)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps18.jpg)

  3. 添加链接文件



选择属性——链接器——常规——附加库目录，加入 dmdpi.lib 的路径。

![图片 16.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528153133FG5TFMJ7HBQCFI40FK)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps19.jpg)

  4. 资源文件右键添加现有项，添加 dmdpi.lib 。



![图片 17.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528153146V6PQWW3T9TA44EATAI)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps20.jpg)

  5. 添加动态库文件



生成解决方案后，将动态库文件都复制到生成的项目目录下：

![图片 18.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528153159BEP7CCOKY8H6O630U5)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps21.jpg)

  6. 添加编译参数 DM64



64 位平台需要添加编译参数 DM64 ，属性——c/c++——预处理器，添加 DM64 。

![图片 19.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528153210M7KRHUH7AW0D6CF67H)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps22.jpg)

### sudo -u 指定其它用户后通过 ODBC 连接提示找不到动态库文件

【问题描述】

使用 sudo -u 指定其它用户后通过 ODBC 连接提示找不到动态库文件。

报错信息如下：

```
[01000][unixODBC][Driver Manager]Can't open lib '/hom/dmdba/dmdbms/libdodbc.so' : file not found
```

【问题解决】

该问题是由于通过 sudo 切换用户执行命令时，无法通过 bashrc 等设置环境变量，当前 shell 的环境变量也无法继承导致，需要修改 /etc/sudoers ，使切换用户后能保持环境变量。修改方法如下：
编辑配置文件 /etc/sudoers 并添加配置项：

```
Defaults env_keep += "LD_LIBRARY_PATH"
```

### 配置 ODBC 数据源执行 isql DM8 报错：数据库 DM8 不存在

【问题描述】

执行 isql 报错：数据库 DM8 不存在。

```
kylinakylin:~/桌面$isql DM8
isql: FATAL:数据库“DM8"不存在
```

【问题分析】

出现上述报错：”数据库 DM8 不存在“ 的原因可能有如下几点：

数据库未安装或未启动数据库服务；

odbc.ini 配置文件信息有误或未配置 odbc.ini 和 odbcinst.ini 文件。

存在其他数据库占用了 isql。

【问题解决】

  1. 通过 ps -ef|grep dmserver 或者通过 DM 服务查看器图形化工具进行查看，查看数据库服务是否正常运行。
  2. 检查 odbc.ini 和 odbcinst.ini 配置文件是否正确。
  3. 检查是否存在其他数据库占用了 isql 。检查配置文件及环境变量，是否存在与其它数据库相关的环境变量及配置。如果存在则通过下面的方法解决。
  4. 检查全局变量 /etc/profile 文件下关于其它数据库的环境变量，将其删除或注释掉，或者把所有关其它数据库的 isql 全部删掉；
  5. 把系统中涉及 odbc 所有的环境变量都删掉。



> **注意**
>
> 通过新建用户不能解决环境变量的问题，实质上如果有其它数据库的环境变量配置在全局中，会对每个用户产生影响。



### 配置 ODBC 数据源执行 isql 报错：当前系统下没有 libreadline.so.8 库文件

【问题描述】

执行 isql 时报错 libreadline.so.8 找不到，具体报错信息：”isql: error while loading shared libraries: libreadline.so.8: cannot open shared object file: NO such ftle or directory“ 。

【问题分析】

当前操作系统可能不存在 libreadline.so.8 文件，或者存在其他版本的 libreadline.so.x 文件，如：libreadline.so.6 等，可能导致出现该报错。

【问题解决】

如果是 x86 架构进入到 /lib/x86_64-linux-gnu/ 目录下查看当前系统下有哪些 libreadline 文件版本，arm 架构则进入 /lib/aarch64-linux-gnu/ 目录下查看。

如果没有 libreadline.so.8 文件可以通过软链接生成。执行该命令 ln -s libreadline.so.6.3 libreadline.so.8 生成 libreadline.so.8 文件。

### 配置 ODBC 数据源执行 isql 报错：找不到 libodbc.so 文件和生成环境句柄失败

【问题描述】

执行 isql，报错：”[01000][ unixODBC][Driver ManagerJCan't open lib '/home/DaMeng/ dmdbms/bin/libodbc.so' :fitle not found“ ;
或者报错：”[unixODBC][Driver Manager]Driver's SQLAllocHandle on SQL_HANDLE_HENV failed“ 。

【问题分析】

安装 UNIXODBC 后，系统下会存在 libodbc.so 文件和 libdodbc.so 文件，由于配置了错误的文件 libodbc.so 导致报错：“出现找不到驱动文件 libodbc.so” ，使用错误的驱动文件会导致出现生成环境句柄失败的报错。

【问题解决】

查看 odbcinst.ini 配置文件中 libdodbc.so 是否被误写成 libodbc.so ，修改正确重新执行 isql ，可连接成功。

### qt 工具中运行连接数据库程序报错：不能打开 libdodbc.so , 文件 QODBC3 没找到，无法连接

【问题描述】

qt 工具中运行连接数据库程序报错：

```
“[unixODBC][Driver ManagerJ can't open lib ‘/home/DaMeng/dmdbms/bin/libdodbc.so’:file not found QODBC3:Unable to connect” 。
```

【问题分析】

qt 工具没有配置环境变量 LD_LIBRARY_PATH；或服务器上 ODBC 无法正常访问数据库或 ODBC 没有配置。

【问题解决】

检查 odbc.ini 和 odbcinst.ini 是否配置，在操作系统上通过 isql 命令是否能正常访问数据库。

在 QT 工具中检查是否配置了 LD_LIBRARY_PATH 环境变量，如果没有配置 LD_LIBRARY_PATH 环境变量，则需要配置 LD_LIBRARY_PATH 环境变量。配置方法如下：

  1. 进入 project 项目



![图片 20.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052816135206BK75SVVXHT2S3ML4)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps23.jpg)

  2. 点击 details



![图片 21.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528161402DCTWYOCB12PZ4YG9AC)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps24.jpg)

  3. 添加环境变量



![图片 22.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240528161420PD1AI3BHJBU9I52AYY)

> **注意**
>
> 如果是服务器上配置 ODBC 报错找不到驱动文件 libdodbc.so，则需要利用 ldd
> /home/DaMeng/dmdbms/bin/libdodbc.so 检查该驱动包是否缺少相关依赖文件，可以通过设置LD_LIBRARY_PATH
> 环境变量进行解决。



使用 QT 工具访问 DM 数据库时，检查服务器上是否安装了 UNIXODBC ,通过 odbcinst -j 进行查看，如果没有安装可以通过下载源码包的方式进行编译安装；

根据应用需求，选择对应用户进行配置 ODBC 数据源。

检查配置文件 odbc.ini 和 odbcinst.ini 参数值是否正确，特别是驱动文件 libdodbc.so 是否正确，odbcinst.ini 文件中 [DM8 ODBC DRIVER] 值是否与 odbc.ini 文件中 DRIVER = DM8 ODBC DRIVER 值一致，如果操作系统没有配置 localhost(/etc/hosts) ，odbc.ini 文件中 SERVER 值尽量写成具体 IP，例如：SERVER = 192.168.104.21；

检查服务器上环境变量 LD_LIBRARY_PATH 是否设置，例如：LD_LIBRARY_PATH=/opt/dmdbms/bin ；

检查服务器上是否存在其他数据库，如果存在，需要注释或者删除对应环境变量设置；

通过 QT 工具进行访问数据库时，需要配置环境变量 LD_LIBRARY_PATH 。

QT 工具中，编写的连接数据库代码串 db.setDatabaseName 值要与 odbc.ini 文件中 [DM8] 值一致，例如：db.setDatabaseName("DM8") 。

### 通过 gdb 命令找 sql 参数

【问题解决】

执行 bt 命令。

执行 f * 进入 vm_run 之后有 "vm" 字样的任一行。

执行 p vm->stmt->bind_param->params[0] --参数的序号从 0 开始。

当 sql_pl_type = 2 ,则用	(nstr_t) 	加地址查看。

例如：

执行 bt 命令。

```
(gdb) bt
#0  assert_fun (env=<optimized out>) at /home/dmops/build/svns/1666183702277/mgr/nsvr.c:3872
#1  0x0000000001aa6f83 in sigterm_handler (signum=11) at /home/dmops/build/svns/1666183702277/mgr/nsvr.c:3921
#2  <signal handler called>
#3  bifun_aav_s_no_null_opt (vm=0x9d62f0f8, tmp_col_no=4, bifun_real=<optimized out>) at /home/dmops/build/svns/1666183702277/pub/bifun.c:7887
#4  0x0000000000e22be6 in binv_internal_exec (vm=0x9d62f0f8, tmp_col_no=4, bifun_id=<optimized out>) at /home/dmops/build/svns/1666183702277/bop/binv.c:53
#5  0x000000000159db2f in vm_nexp_run_low (vm=<optimized out>, cmds=<optimized out>, cmd_len=<optimized out>, ign_cancel=1) at /home/dmops/build/svns/1666183702277/op/vm.c:5693
#6  0x0000000001536cfb in prjt2_exec_process_bdta (prjt2_vm=0x9d912be8) at /home/dmops/build/svns/1666183702277/op/prjt2.c:712
#7  0x000000000153743c in prjt2_exec_after_fetch (prjt2_vm=0x9d912be8) at /home/dmops/build/svns/1666183702277/op/prjt2.c:968
#8  0x00000000015375c0 in prjt2_exec (prjt2_vm=0x9d912be8) at /home/dmops/build/svns/1666183702277/op/prjt2.c:1011
#9  0x000000000159f79d in vm_run_low (vm=0x9d62f0f8) at /home/dmops/build/svns/1666183702277/op/vm.c:5177
#10 0x000000000159fbb1 in vm_run (vm=<optimized out>) at /home/dmops/build/svns/1666183702277/op/vm.c:5252
#11 0x00000000015a0d83 in vm_run_pln_low (env=0x7ff618c71c20, stmt=0x9d6300f8, pln=0x9a970860, ret_ident_flag=<optimized out>, n_ret_col=<optimized out>, ret_col_ident=<optimized out>, err_desc=0x0) at /home/dmops/build/svns/1666183702277/op/vm.c:9325
#12 0x00000000015a0f58 in vm_run_pln (env=<optimized out>, stmt=<optimized out>, pln=<optimized out>, ret_ident_flag=<optimized out>, n_ret_col=<optimized out>, ret_col_ident=<optimized out>) at /home/dmops/build/svns/1666183702277/op/vm.c:9407
#13 0x0000000001adc258 in ntsk_process_exec_low (env=0x7ff618c71c20, stmt=0x9d6300f8, pln=0x9a970860, ret_ident_flag=0 '\000', n_ret_col=0, ret_col_ident=0x0, dlck_reprepare=0x7ff618c713bc) at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:10684
#14 0x0000000001ae4b14 in ntsk_process_exec (env=0x7ff618c71c20, sess=0x9d60c0f8, msg_in=0x9beaa7e8 "\006") at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:13172
#15 0x0000000001af42e6 in ntsk_process_cop (env=0x7ff618c71c20, task=<optimized out>) at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:17681
#16 0x00000000019a8d29 in uthr_db_main_for_sess (sess2=0x9d60c0f8) at /home/dmops/build/svns/1666183702277/knl/uthr.c:1143
#17 0x00007ff631beddd5 in start_thread () from /usr/lib64/libpthread.so.0
#18 0x00007ff631109ead in clone () from /usr/lib64/libc.so.6
```

f * 命令进入 vm_run 之后有 "vm" 字样的任一行，如第 6 行栈帧。

```
f 6
```

执行 p vm->stmt->bind_param->params[0] 命令开始打印参数，参数的序号从 0 开始。

```
(gdb) p vm->stmt->bind_param->params[0]    -- 参数的序号从0开始

$3 = {ntype = {sql_pl_type = 2, len = 32768, prec = 0, org_len = 0, desc = 0x0, p_rec = 0x0, p_args = 0x0}, lit_flag = 0 '\000', io_flag = 0 '\000', org_pl_type = 2, 
    seqno = 0, p_name = 0x0, data = 0x9eba1a28, blob_info = 0x0}
```

当 sql_pl_type = 2 ，则用*(nstr_t*) 加地址方式查看。例如打印第 1 个参数，地址是 data = 0x9eba1a28。

```
(gdb) p *(nstr_t*)0x9eba1a28
$4 = {null_flag = 1, alen = 3, n_blank = 0, local_space = "TXT", '\000' <repeats 44 times>, 
    data = 0x9eba1a34 "TXT", mem_p = 0x0}     -- data=地址 "参数"，这行的 TXT 就是 SQL 的参数
```

打印第二个参数，剩余参数以此类推。

```
(gdb) p vm->stmt->bind_param->params[1]
$5 = {ntype = {sql_pl_type = 2, len = 32768, prec = 0, org_len = 0, desc = 0x0, p_rec = 0x0, p_args = 0x0}, lit_flag = 0 '\000', io_flag = 0 '\000', org_pl_type = 2, seqno = 1, p_name = 0x0, data = 0x9eba1a78, blob_info = 0x0}

-- 打印第2个参数，地址是 data = 0x9eba1a78

(gdb) p *(nstr_t*)0x9eba1a78
$6 = {null_flag = 1, alen = 3, n_blank = 0, local_space = "431", '\000' <repeats 44 times>, data = 0x9eba1a84 "431", mem_p = 0x0}
```

### 使用 gbd 获取堆栈里的完整 sql

【问题解决】

由于使用 rdc 扫描堆栈文件时间比较久，直接使用 gdb 获取完整 sql 块。

步骤如下：

  1. 执行 bt 命令。

  2. f * 进入对应行数的栈帧，选择这两个方法 ntsk_process_exec_low 、ntsk_process_exec 对应行数的栈帧。

  3. 通过以下命令调整设置，否则 sql 太长无法显示完整。
    
        (gdb) set print element 0 
    

  4. 获取完整 sql 。这里提供 3 中方法可以获取到完整的 SQL 。



  * 从 sess 里获取完整 sql 。
    
        (gdb) p sess ->sqls
    

  * 从 stmt 里获取完整 sql 。
    
        (gdb) p stmt ->sql_str
    

  * 从 pln 里获取完整 sql 。
    
        (gdb) p pln->sqlstr
    



例如：

  1. 执行 bt 命令。



```
(gdb)bt
#0  assert_fun (env=<optimized out>) at /home/dmops/build/svns/1666183702277/mgr/nsvr.c:3872
#1  0x0000000001aa6f83 in sigterm_handler (signum=11) at /home/dmops/build/svns/1666183702277/mgr/nsvr.c:3921
#2  <signal handler called>
#3  bifun_aav_s_no_null_opt (vm=0x9ecb00f8, tmp_col_no=4, bifun_real=<optimized out>) at /home/dmops/build/svns/1666183702277/pub/bifun.c:7887
#4  0x0000000000e22be6 in binv_internal_exec (vm=0x9ecb00f8, tmp_col_no=4, bifun_id=<optimized out>) at /home/dmops/build/svns/1666183702277/bop/binv.c:53
#5  0x000000000159db2f in vm_nexp_run_low (vm=<optimized out>, cmds=<optimized out>, cmd_len=<optimized out>, ign_cancel=1)
    at /home/dmops/build/svns/1666183702277/op/vm.c:5693
#6  0x0000000001536cfb in prjt2_exec_process_bdta (prjt2_vm=0x9d5c2be8) at /home/dmops/build/svns/1666183702277/op/prjt2.c:712
#7  0x000000000153743c in prjt2_exec_after_fetch (prjt2_vm=0x9d5c2be8) at /home/dmops/build/svns/1666183702277/op/prjt2.c:968
#8  0x00000000015375c0 in prjt2_exec (prjt2_vm=0x9d5c2be8) at /home/dmops/build/svns/1666183702277/op/prjt2.c:1011
#9  0x000000000159f79d in vm_run_low (vm=0x9ecb00f8) at /home/dmops/build/svns/1666183702277/op/vm.c:5177
#10 0x000000000159fbb1 in vm_run (vm=<optimized out>) at /home/dmops/build/svns/1666183702277/op/vm.c:5252
#11 0x00000000015a0d83 in vm_run_pln_low (env=0x7f19b23fec20, stmt=0x9ecaf8f8, pln=0x9be00860, ret_ident_flag=<optimized out>, n_ret_col=<optimized out>, 
    ret_col_ident=<optimized out>, err_desc=0x0) at /home/dmops/build/svns/1666183702277/op/vm.c:9325
#12 0x00000000015a0f58 in vm_run_pln (env=<optimized out>, stmt=<optimized out>, pln=<optimized out>, ret_ident_flag=<optimized out>, n_ret_col=<optimized out>, 
    ret_col_ident=<optimized out>) at /home/dmops/build/svns/1666183702277/op/vm.c:9407
#13 0x0000000001adc258 in ntsk_process_exec_low (env=0x7f19b23fec20, stmt=0x9ecaf8f8, pln=0x9be00860, ret_ident_flag=0 '\000', n_ret_col=0, ret_col_ident=0x0, 
    dlck_reprepare=0x7f19b23fe3bc) at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:10684
#14 0x0000000001ae4b14 in ntsk_process_exec (env=0x7f19b23fec20, sess=0x9ec8a0f8, msg_in=0x9d5287e8 "\002") at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:13172
#15 0x0000000001af42e6 in ntsk_process_cop (env=0x7f19b23fec20, task=<optimized out>) at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:17681
#16 0x00000000019a8d29 in uthr_db_main_for_sess (sess2=0x9ec8a0f8) at /home/dmops/build/svns/1666183702277/knl/uthr.c:1143
#17 0x00007f19cb37add5 in start_thread () from /usr/lib64/libpthread.so.0
#18 0x00007f19ca896ead in clone () from /usr/lib64/libc.so.6
```

  2. 选择这两个方法 ntsk_process_exec_low 、ntsk_process_exec 其中一个对应行数的栈帧即可，一个为 13 一个为 14。
    
        #13 0x0000000001adc258 in ntsk_process_exec_low (env=0x7f19b23fec20, stmt=0x9ecaf8f8, pln=0x9be00860, ret_ident_flag=0 '\000', n_ret_col=0, ret_col_ident=0x0, 
        dlck_reprepare=0x7f19b23fe3bc) at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:10684
    #14 0x0000000001ae4b14 in ntsk_process_exec (env=0x7f19b23fec20, sess=0x9ec8a0f8, msg_in=0x9d5287e8 "\002") at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:13172
    

  3. 需要调整设置，否则 sql 太长无法显示完整。



```
(gdb) set print element 0 
```

  4. 获取完整 sql。



选择对应行数的栈帧为 14 的。

```
(gdb) f 14
#14 0x0000000001ae4b14 in ntsk_process_exec (env=0x7f19b23fec20, sess=0x9ec8a0f8, msg_in=0x9d5287e8 "\002") at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:13172
13172	in /home/dmops/build/svns/1666183702277/mgr/ntsk.c
```

  * 从 sess 里获取完整 sql。



```
(gdb) p sess ->sqls 
$8 = (schar *) 0x9ec8a2c4 "SELECT NVL(SUM(D1),0)||'' AS D1,\nNVL(SUM(D3),0)||'' AS D2,\nNVL(SUM(D2),0)||'' AS D3\nFROM (\nSELECT CASE WHEN C1 >= SYSDATE - 10 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D1,\nCASE WHEN C1 >= SYSDATE - 20 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D2,\nCASE WHEN C1 >= SYSDATE - 30 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D3\nFROM T1  where c2= ? and c3=? AND C4>=?\nGROUP BY T1.ID,C1\n)"
```

选择对应行数的栈帧为 13 的。

```
(gdb) f 13
#13 0x0000000001adc258 in ntsk_process_exec_low (env=0x7f19b23fec20, stmt=0x9ecaf8f8, pln=0x9be00860, ret_ident_flag=0 '\000', n_ret_col=0, ret_col_ident=0x0, 
    dlck_reprepare=0x7f19b23fe3bc) at /home/dmops/build/svns/1666183702277/mgr/ntsk.c:10684
10684	in /home/dmops/build/svns/1666183702277/mgr/ntsk.c
```

  * 从 stmt 里获取完整 sql



```
(gdb) p stmt ->sql_str
$9 = (schar *) 0x9ecafd10 "SELECT NVL(SUM(D1),0)||'' AS D1,\nNVL(SUM(D3),0)||'' AS D2,\nNVL(SUM(D2),0)||'' AS D3\nFROM (\nSELECT CASE WHEN C1 >= SYSDATE - 10 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D1,\nCASE WHEN C1 >= SYSDATE - 20 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D2,\nCASE WHEN C1 >= SYSDATE - 30 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D3\nFROM T1  where c2= ? and c3=? AND C4>=?\nGROUP BY T1.ID,C1\n)"
```

  * 从 pln 里获取完整 sql。



```
(gdb) p pln->sqlstr
$10 = (schar *) 0x9bd6e450 "SELECT NVL(SUM(D1),0)||'' AS D1,\nNVL(SUM(D3),0)||'' AS D2,\nNVL(SUM(D2),0)||'' AS D3\nFROM (\nSELECT CASE WHEN C1 >= SYSDATE - 10 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D1,\nCASE WHEN C1 >= SYSDATE - 20 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D2,\nCASE WHEN C1 >= SYSDATE - 30 AND C1 < SYSDATE THEN COUNT(1)||'' ELSE 0||'' END AS D3\nFROM T1  where c2= ? and c3=? AND C4>=?\nGROUP BY T1.ID,C1\n)"
```

### QT-ODBC 无法连接数据库

【问题描述】

配置 QT-ODBC 后执行连接数据库程序时报错，报错信息如下：

```
QSqlDatabase: QODBC driver not loaded
QSqlDatabase: available drivers: QSQLITE QODBC QODBC3 QPSQL QPSQL7
connect fail! "Driver not loaded Driver not loaded"
```

【问题分析】

在 QT 安装路径的 sqldrivers 目录下执行如下命令检查发现缺少 libodbc.so.2 库文件。

```
[root@dsc1 ~]# ldd libqsqlodbc.so.2
```

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps26.jpg)

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052909320097S0ME3I457I2WIKWJ)

触发此报错的原因为：缺少 libodbc.so.2 库文件。

【问题解决】

通过以下命令查询 libodbc.so.2 库文件位置。

```
[root@dsc1 ~]# find / -name libodbc.so.2
```

通过以下命令进行软连接指定 libodbc.so.2 库文件。

```
[root@dsc1 ~]# ln -s /usr/local/lib/libodbc.so.2 /opt/Qt5.12.8/5.12.8/gcc_64/lib
```

### ODBC 连接达梦数据库时如何添加连接串的自定义参数

【问题解决】

在 ODBC 中连接到数据源的常用方法有 SQLConnect 函数与 SQLDriverConnect 函数两种，具体方法如下：

使用 SQLConnect 函数连接达梦数据库。

步骤一：C 代码中：

```
SQLConnect(hdbc, "DM8", SQL_NTS, "SYSDBA", SQL_NTS, "SYSDBA", SQL_NTS);
```

步骤二：在 odbc 的 odbc.ini 文件中添加参数 LINK_ATTR=COMPATIBLE_MODE=ORACLE ；

使用 SQLDriverConnect 函数连接达梦数据库直接跟参数即可。

```
SQLCHAR szConnStrOut[256];
SQLSMALLINT cbConnStrOut;
sret = SQLDriverConnect(hdbc, NULL, (SQLCHAR *)("SERVER=127.0.0.1;DSN=DM8;DRIVER=DM8 ODBC DRIVER;UID=SYSDBA;PWD=SYSDBA;TCP_PORT=5236;COMPATIBLE_MODE=ORACLE"), SQL_NTS, szConnStrOut, 256,&cbConnStrOut, SQL_DRIVER_NOPROMPT);
```

### ODBC 程序进行日期查询时日期毫秒值尾数 0 值被截断

【问题描述】

ODBC 程序进行日期查询，查询出来的日期当毫秒值尾数为 0 时，0 值被截断。

```
SELECT left(CURRENT_TIMESTAMP(6), 26) from dual
```

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps27.jpg)

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529093552X71Y1YVP21MK62BOK4)

【问题分析】

代码底层 CURRENT_TIMESTAMP(6) 返回的不是 varchar 类型，所以 datetime 转换为 varchar 存在问题，dmtime to char 处理时如果未指定 prec 则会截断尾部的 0 。

【问题解决】

以下提供两种解决方案，任选其一即可：

方法一：改写语句，手动指定类型

```
select to_char(current_timestamp,'YYYY-MM-DD HH24:MI:SS.FF') from dual
```

方法二：开启数据库参数

```
DATETIME_FMT_MODE               = 1
NLS_TIMESTAMP_FORMAT            = YYYY-MM-DD HH24:MI:SS.FF
```

### ODBC 接口开发报错缺失 sql.h 和 sqltypes.h 头文件

【问题描述】

ODBC 接口开发在 makefile 编译时和连接数据库时报错：缺失 sql.h 和 sqltypes.h 头文件。

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405290939215NR3XSYA2OCTR0MJNG)

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405290940108O7BLZAKWRP80OSZ41)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps28.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps29.jpg)

【问题分析】

该报错是由于缺少 unixODBC-devel 库导致，unixODBC-deve 是一种用于开发 ODBC 应用程序的软件包，它包含了 ODBC 开发所需的各种头文件、库文件和开发工具。这个软件包通常用于在 Linux 或 Unix 系统中编写 ODBC 应用程序，其中也包含了 sql.h 文件，是 ODBC 开发所需的头文件之一。

【问题解决】

  1. 安装 unixODBC-devel 库。



下载与 ODBC 版本相同的 unixODBC-devel 软件包。

下载参考地址：[https://developer.aliyun.com/packageSearch?word=unixodbc](https://developer.aliyun.com/packageSearch?word=unixodbc) 。

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405290945134FL9AQFK6FOS79VS4B)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps30.jpg)

  2. 安装 unixODBC-devel 。



```
rpm -ivh unixODBC-devel-2.3.1-14.el7.x86_64.rpm
```

### Windows server 操作系统上 odbc 测试连接报错："加密模块加载失败"

【问题描述】

在 Windows server 操作系统上，部署好达梦数据库软件后，进行 odbc 数据源的创建，在测试连接阶段报错：“加密模块加载失败”。

【问题解决】

可以尝试通过将 dmdbms/bin 目录下的 libeay32.dll 和 ssleay32.dll 文件拷贝至 C:\Windows\System32 目录下来解决该问题。

### 如何查看应用系统使用的 ODBC/OCI/DPI 的驱动版本号

【问题解决】

使用 ODBC 驱动连接到数据库后,通过查看视图 v$sessions 获取驱动版本号。

```
select CLNT_VER from v$sessions;
```

当无法连接数据库时可通过查看 libdmdpi.so 文件来获取驱动版本号。

在 ODBC 驱动同级目录下有个 libdmdpi.so 文件，执行如下命令可以打印出驱动版本号：

```
strings libdmdpi.so | grep SP_SET_SESSION_R；
```

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529095151A34BJACZ5QUZ2VGE54)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps31.jpg)

### C 编程直接调用 ctl 文件方式导入数据示例

【问题解决】

  1. 创建表 T1 。



```
create table t1(id int,name varchar(20));
```

  2. 加载数据文件内容。



```
Vim /opt/dmdbms/t1.txt

1|test
2|name
```

  3. 编写 C 脚本。



```
vim demo.c

#include "fldr.h"
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int main(int argc, char *argv[])
{
   fhinstance      instance;
   FLDR_RETURN     ret;
   char            server[20] = "127.0.0.1";
   char            user[20] = "SYSDBA";
   char            pwd[20] = "SYSDBA";
   char            table[20] = "T1";
   int             i;

   ret = fldr_alloc(&instance);
   ret = fldr_initialize(instance, 3, NULL, (fchar *) server,(fchar *) user,(fchar *) pwd,(fchar *) table);
   ret = fldr_exec_ctl_low(instance, (fchar *)"LOAD DATA\r\nINFILE '/opt/dmdbms/t1.txt'\r\nINTO TABLE SYSDBA.T1\r\nFIELDS '|'\r\n(ID,NAME)",5,(fsint8 *) &i);

  printf("%d",ret);
}
```

4.编译 C 脚本。

```
gcc -o demo demo.c -I/opt/dmdbms/include   -L/opt/dmdbms/bin    -ldmfldr -DDM64
```

5.执行程序。

```
./demo
```

### proc 中将 varchar2 类型字段 into 到变量中时会补空格到最大长度要怎么处理？

【问题描述】

pro*c 的语法中使用 select 语句将 varchar2 类型字段 into 到变量中时，变量的值会后面补空格到最大长度，怎么处理？

例如：数据库的字段是 varchar2(15)，实际数据库表里面的存的长度是 10，v_a 这个变量定义的是 15，最终 select into 给这个变量的时候，这个变量的值会后面补空格到 15 位长度。

【问题分析】

C++ 里面定义的是：char v_password_b [16] = { 0 } ;

数据库里面定义的是：varchar2(15) 。

这是因为 DM 的 dpc_new 工具解析时默认使用的是 char 接收会默认补空格。

【问题解决】

编译 c 代码时 dpc_new 工具新增参数 CHAR_MAP=STRING。这样会转为 string 处理就可以避免这个问题了。

### ODBC 应用程序执行 SQLConnect 函数时报错

【问题描述】

ODBC 应用程序执行 SQLConnect 函数时报错：“SQL_INVALID_HANDLE = -2 checkError function was called with an invalid handle!!” 。

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052910155733HTBZQXOWPSEBONQP)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml16320\wps32.jpg)

【问题分析】

该问题可能是由于以下原因导致：

DM 驱动与操作系统不匹配或者 DM 驱动过旧与数据库版本不匹配。

运行环境下 DM 库版本过多导致依赖冲突。

odbc.ini 中配置的 DM 驱动与实际依赖的不一致。

【问题解决】

可以尝试使用如下方法解决：

（1）使用 export 临时修改变量进行测试，例如：export LD_LIBRARY_PATH=/opt/dmdbms_20230316/bin:$LD_LIBRARY_PATH（注：/opt/dmdbms_20230316/bin 为数据库安装路径）。

（2）确保使用的 DM 驱动与操作系统版本一致（不可混用，例如：麒麟 v10 的驱动包在 rh7 下使用，x86 与 arm 也不通用）。

（3）在代码中使用 SQLGetDiagRec 函数打印出具体的错误代码，再进一步分析。

### 找不到 libodbcinst.so 动态库文件

【问题描述】

配置 C+ODBC 方式连接达梦数据库时报错：“找不到 libodbcinst.so 动态库文件”。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240808111729V2J87IBSERRLX2ZVAQ)

【问题解决】

可以通过配置 LD_LIBRARY_PATH 环境添加 ODBC lib 目录来尝试解决。

```
[root@localhost ~]# vim /etc/profile
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/dmdba/dmdbms/bin:/usr/local/lib/"
[root@localhost ~]# source /etc/profile
```

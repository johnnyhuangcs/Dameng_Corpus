

# JAVA 语言

## 前言

本章节主要介绍 JAVA 应用开发常见问题，为用户提供 JAVA 应用开发常见问题的分析和解决思路。除此之外，用户还可前往达梦技术社区参与更多问题讨论。

## 目录

  * [JDBC 程序获取存储过程元数据信息为空](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E7%A8%8B%E5%BA%8F%E8%8E%B7%E5%8F%96%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E5%85%83%E6%95%B0%E6%8D%AE%E4%BF%A1%E6%81%AF%E4%B8%BA%E7%A9%BA)
  * [JDBC 驱动连接数据库报错：failed to initialize ssl](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E9%A9%B1%E5%8A%A8%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99%EF%BC%9Afailed%20to%20initialize%20ssl)
  * [JDBC 如何通过服务名连接读写分离集群](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E5%A6%82%E4%BD%95%E9%80%9A%E8%BF%87%E6%9C%8D%E5%8A%A1%E5%90%8D%E8%BF%9E%E6%8E%A5%E8%AF%BB%E5%86%99%E5%88%86%E7%A6%BB%E9%9B%86%E7%BE%A4)
  * [JDBC 驱动连接数据库 Error: for input String 8](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E9%A9%B1%E5%8A%A8%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%20Error:%20for%20input%20String%208)
  * [JDBC 连接 DM 数据库时，是否可以使用 dm8 的驱动连接 dm7 的数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E8%BF%9E%E6%8E%A5%20DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%97%B6%EF%BC%8C%E6%98%AF%E5%90%A6%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8%20dm8%20%E7%9A%84%E9%A9%B1%E5%8A%A8%E8%BF%9E%E6%8E%A5%20dm7%20%E7%9A%84%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [JDBC 连接数据库查询的时间与客户端连接查询的时间相差 8 个小时](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E6%9F%A5%E8%AF%A2%E7%9A%84%E6%97%B6%E9%97%B4%E4%B8%8E%E5%AE%A2%E6%88%B7%E7%AB%AF%E8%BF%9E%E6%8E%A5%E6%9F%A5%E8%AF%A2%E7%9A%84%E6%97%B6%E9%97%B4%E7%9B%B8%E5%B7%AE%208%20%E4%B8%AA%E5%B0%8F%E6%97%B6)
  * [JDBC 程序中如何获取存储过程的元数据信息](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E7%A8%8B%E5%BA%8F%E4%B8%AD%E5%A6%82%E4%BD%95%E8%8E%B7%E5%8F%96%E5%AD%98%E5%82%A8%E8%BF%87%E7%A8%8B%E7%9A%84%E5%85%83%E6%95%B0%E6%8D%AE%E4%BF%A1%E6%81%AF)
  * [JDBC XA 框架下应用报错：​"事务分支中发生资源管理器错误​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20XA%20%E6%A1%86%E6%9E%B6%E4%B8%8B%E5%BA%94%E7%94%A8%E6%8A%A5%E9%94%99%EF%BC%9A%22%E4%BA%8B%E5%8A%A1%E5%88%86%E6%94%AF%E4%B8%AD%E5%8F%91%E7%94%9F%E8%B5%84%E6%BA%90%E7%AE%A1%E7%90%86%E5%99%A8%E9%94%99%E8%AF%AF%22)
  * [使用 JDBC 连接如何配置字符集](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E4%BD%BF%E7%94%A8%20JDBC%20%E8%BF%9E%E6%8E%A5%E5%A6%82%E4%BD%95%E9%85%8D%E7%BD%AE%E5%AD%97%E7%AC%A6%E9%9B%86)
  * [应用 JDBC 连接数据库失败问题/连接超时](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%BA%94%E7%94%A8%20JDBC%20%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A4%B1%E8%B4%A5%E9%97%AE%E9%A2%98/%E8%BF%9E%E6%8E%A5%E8%B6%85%E6%97%B6)
  * [JDBC 执行 explain 语句，resultset 是空的，用了 explain for，resultset 还是空的](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDBC%20%E6%89%A7%E8%A1%8C%20explain%20%E8%AF%AD%E5%8F%A5%EF%BC%8Cresultset%20%E6%98%AF%E7%A9%BA%E7%9A%84%EF%BC%8C%E7%94%A8%E4%BA%86%20explain%C2%A0for%EF%BC%8Cresultset%20%E8%BF%98%E6%98%AF%E7%A9%BA%E7%9A%84)
  * [使用 DM8 的 JDBC 驱动连接 DM7 数据库，报错：“网络通讯“ 错误和 “拒绝连接”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E4%BD%BF%E7%94%A8%20DM8%20%E7%9A%84%20JDBC%20%E9%A9%B1%E5%8A%A8%E8%BF%9E%E6%8E%A5%20DM7%20%E6%95%B0%E6%8D%AE%E5%BA%93%EF%BC%8C%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E7%BD%91%E7%BB%9C%E9%80%9A%E8%AE%AF%E2%80%9C%20%E9%94%99%E8%AF%AF%E5%92%8C%20%E2%80%9C%E6%8B%92%E7%BB%9D%E8%BF%9E%E6%8E%A5%E2%80%9D)
  * [报错：driver dm.jdbc.driver.DmDriver is not sequoia compliant](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E6%8A%A5%E9%94%99%EF%BC%9Adriver%20dm.jdbc.driver.DmDriver%20is%20not%20sequoia%20compliant)
  * [插入大字段数据报错 Caused by: dm.jdbc.driver.DMException: 数据大小已超过可支持范围](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E6%8F%92%E5%85%A5%E5%A4%A7%E5%AD%97%E6%AE%B5%E6%95%B0%E6%8D%AE%E6%8A%A5%E9%94%99%20Caused%20by:%20dm.jdbc.driver.DMException:%20%E6%95%B0%E6%8D%AE%E5%A4%A7%E5%B0%8F%E5%B7%B2%E8%B6%85%E8%BF%87%E5%8F%AF%E6%94%AF%E6%8C%81%E8%8C%83%E5%9B%B4)
  * [buffer index out of range](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#buffer%20index%20out%20of%20range)
  * [Communication error ​"dm network timeout executor​" Set network timeout error 。](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Communication%20error%20%22dm%20network%20timeout%20executor%22%20Set%20network%20timeout%20error%20%E3%80%82)
  * [Java 的连接配置方式](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Java%20%E7%9A%84%E8%BF%9E%E6%8E%A5%E9%85%8D%E7%BD%AE%E6%96%B9%E5%BC%8F)
  * [Java 更新 clob 或 blob 字段，提示无效的十六进制数字](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Java%20%E6%9B%B4%E6%96%B0%20clob%20%E6%88%96%20blob%20%E5%AD%97%E6%AE%B5%EF%BC%8C%E6%8F%90%E7%A4%BA%E6%97%A0%E6%95%88%E7%9A%84%E5%8D%81%E5%85%AD%E8%BF%9B%E5%88%B6%E6%95%B0%E5%AD%97)
  * [Java 程序指定访问模式](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Java%20%E7%A8%8B%E5%BA%8F%E6%8C%87%E5%AE%9A%E8%AE%BF%E9%97%AE%E6%A8%A1%E5%BC%8F)
  * [Java 程序使用 SSL 认证连接数据库报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Java%20%E7%A8%8B%E5%BA%8F%E4%BD%BF%E7%94%A8%20SSL%20%E8%AE%A4%E8%AF%81%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99)
  * [Java 应用报错：For input string: ​"0.000000​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Java%20%E5%BA%94%E7%94%A8%E6%8A%A5%E9%94%99%EF%BC%9AFor%20input%20string:%20%220.000000%22)
  * [Java 程序连接读写分离集群报错：“服务器模式不匹配”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Java%20%E7%A8%8B%E5%BA%8F%E8%BF%9E%E6%8E%A5%E8%AF%BB%E5%86%99%E5%88%86%E7%A6%BB%E9%9B%86%E7%BE%A4%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9C%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%A8%A1%E5%BC%8F%E4%B8%8D%E5%8C%B9%E9%85%8D%E2%80%9D)
  * [使用 getTypeInfo() 方法时报错：​"无效的列名 AUTO_INCREMENT​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E4%BD%BF%E7%94%A8%20getTypeInfo\(\)%20%E6%96%B9%E6%B3%95%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%22%E6%97%A0%E6%95%88%E7%9A%84%E5%88%97%E5%90%8D%20AUTO_INCREMENT%22)
  * [Druid 连接池连接达梦数据库报错：“dbType not support”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Druid%20%E8%BF%9E%E6%8E%A5%E6%B1%A0%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9CdbType%20not%20support%E2%80%9D)
  * [DM 如何支持 C3P0 连接池？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#DM%20%E5%A6%82%E4%BD%95%E6%94%AF%E6%8C%81%20C3P0%20%E8%BF%9E%E6%8E%A5%E6%B1%A0%EF%BC%9F)
  * [Druid 报错：unkow jdbc driver](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Druid%20%E6%8A%A5%E9%94%99%EF%BC%9Aunkow%20jdbc%20driver)
  * [Druid 报错：dbType not support](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Druid%20%E6%8A%A5%E9%94%99%EF%BC%9AdbType%20not%20support)
  * [如何配置 DruidDataSource 数据源连接 DM 数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%A6%82%E4%BD%95%E9%85%8D%E7%BD%AE%20DruidDataSource%20%E6%95%B0%E6%8D%AE%E6%BA%90%E8%BF%9E%E6%8E%A5%20DM%20%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [德鲁伊（druids）连接池相关配置推荐](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%BE%B7%E9%B2%81%E4%BC%8A%EF%BC%88druids%EF%BC%89%E8%BF%9E%E6%8E%A5%E6%B1%A0%E7%9B%B8%E5%85%B3%E9%85%8D%E7%BD%AE%E6%8E%A8%E8%8D%90)
  * [Druid 连接达梦报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Druid%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%8A%A5%E9%94%99)
  * [Springboot 使用 druid 连接池时报错：testWhileIdle is true, validationQuery not set](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Springboot%20%E4%BD%BF%E7%94%A8%20druid%20%E8%BF%9E%E6%8E%A5%E6%B1%A0%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9AtestWhileIdle%20is%20true,%20validationQuery%20not%20set)
  * [在进行连接池测试时，报错 “java.lang.ClassNotFoundException”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%9C%A8%E8%BF%9B%E8%A1%8C%E8%BF%9E%E6%8E%A5%E6%B1%A0%E6%B5%8B%E8%AF%95%E6%97%B6%EF%BC%8C%E6%8A%A5%E9%94%99%20%E2%80%9Cjava.lang.ClassNotFoundException%E2%80%9D)
  * [Java springboot 引入数据库的 jar 包，数据源监听到 SQL 已经执行，但没有返回值](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Java%20springboot%20%E5%BC%95%E5%85%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%20jar%20%E5%8C%85%EF%BC%8C%E6%95%B0%E6%8D%AE%E6%BA%90%E7%9B%91%E5%90%AC%E5%88%B0%20SQL%20%E5%B7%B2%E7%BB%8F%E6%89%A7%E8%A1%8C%EF%BC%8C%E4%BD%86%E6%B2%A1%E6%9C%89%E8%BF%94%E5%9B%9E%E5%80%BC)
  * [springBoot jpa 使用达梦数据库和驱动后程序报错:​"Failed to bind properties under...](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#springBoot%20jpa%20%E4%BD%BF%E7%94%A8%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E5%92%8C%E9%A9%B1%E5%8A%A8%E5%90%8E%E7%A8%8B%E5%BA%8F%E6%8A%A5%E9%94%99:%22Failed%20to%20bind%20properties%20under...)
  * [activiti 工作流是否支持达梦数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#activiti%20%E5%B7%A5%E4%BD%9C%E6%B5%81%E6%98%AF%E5%90%A6%E6%94%AF%E6%8C%81%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [activiti 连接 DM 数据库报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#activiti%20%E8%BF%9E%E6%8E%A5%20DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99)
  * [activiti 工作流因大小写敏感问题导致工作流引擎报错引发异常](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#activiti%20%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%9B%A0%E5%A4%A7%E5%B0%8F%E5%86%99%E6%95%8F%E6%84%9F%E9%97%AE%E9%A2%98%E5%AF%BC%E8%87%B4%E5%B7%A5%E4%BD%9C%E6%B5%81%E5%BC%95%E6%93%8E%E6%8A%A5%E9%94%99%E5%BC%95%E5%8F%91%E5%BC%82%E5%B8%B8)
  * [报错：Caused by: org.activiti.engine.](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E6%8A%A5%E9%94%99%EF%BC%9ACaused%C2%A0by:%C2%A0org.activiti.engine.)
  * [hibernate 连接数据库的方言写法](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#hibernate%20%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E7%9A%84%E6%96%B9%E8%A8%80%E5%86%99%E6%B3%95)
  * [用 hibernate 执行 sql，参数为 null 的时候报错 “无效的十六进制数据”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E7%94%A8%20hibernate%20%E6%89%A7%E8%A1%8C%20sql%EF%BC%8C%E5%8F%82%E6%95%B0%E4%B8%BA%20null%20%E7%9A%84%E6%97%B6%E5%80%99%E6%8A%A5%E9%94%99%20%E2%80%9C%E6%97%A0%E6%95%88%E7%9A%84%E5%8D%81%E5%85%AD%E8%BF%9B%E5%88%B6%E6%95%B0%E6%8D%AE%E2%80%9D)
  * [hibernate 配置主键默认、序列插入报错：违反非空约束](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#hibernate%20%E9%85%8D%E7%BD%AE%E4%B8%BB%E9%94%AE%E9%BB%98%E8%AE%A4%E3%80%81%E5%BA%8F%E5%88%97%E6%8F%92%E5%85%A5%E6%8A%A5%E9%94%99%EF%BC%9A%E8%BF%9D%E5%8F%8D%E9%9D%9E%E7%A9%BA%E7%BA%A6%E6%9D%9F)
  * [DM Hibernate 方言包兼容 Oracle 和 MySQL 的主键生成策略](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#DM%20Hibernate%20%E6%96%B9%E8%A8%80%E5%8C%85%E5%85%BC%E5%AE%B9%20Oracle%20%E5%92%8C%20MySQL%20%E7%9A%84%E4%B8%BB%E9%94%AE%E7%94%9F%E6%88%90%E7%AD%96%E7%95%A5)
  * [hibernate 框架运行时报错：​"Could not find setter for LOCATION on class com.xx.po.MeetingRoom​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#hibernate%20%E6%A1%86%E6%9E%B6%E8%BF%90%E8%A1%8C%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%22Could%20not%20find%20setter%20for%20LOCATION%20on%20class%20com.xx.po.MeetingRoom%22)
  * [应用连接数据库后启动报错：”hibernate.dialect​' not set“](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%BA%94%E7%94%A8%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E5%90%8E%E5%90%AF%E5%8A%A8%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9Dhibernate.dialect'%20not%20set%E2%80%9C)
  * [Error parsing XML: /hibernate.cfg.xml(1) 文档类型声明包含或指向的标记声明格式有误](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Error%20parsing%20XML:%20/hibernate.cfg.xml\(1\)%20%E6%96%87%E6%A1%A3%E7%B1%BB%E5%9E%8B%E5%A3%B0%E6%98%8E%E5%8C%85%E5%90%AB%E6%88%96%E6%8C%87%E5%90%91%E7%9A%84%E6%A0%87%E8%AE%B0%E5%A3%B0%E6%98%8E%E6%A0%BC%E5%BC%8F%E6%9C%89%E8%AF%AF)
  * [JavaWeb 框架如何连接访问 DM 数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JavaWeb%20%E6%A1%86%E6%9E%B6%E5%A6%82%E4%BD%95%E8%BF%9E%E6%8E%A5%E8%AE%BF%E9%97%AE%20DM%20%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [希望在 mybatis 下对象不用添加双引号](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%B8%8C%E6%9C%9B%E5%9C%A8%20mybatis%20%E4%B8%8B%E5%AF%B9%E8%B1%A1%E4%B8%8D%E7%94%A8%E6%B7%BB%E5%8A%A0%E5%8F%8C%E5%BC%95%E5%8F%B7)
  * [使用 mybatis 框架批量插入并自动获取自增主键 ID 时获取不到主键 ID 值](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E4%BD%BF%E7%94%A8%20mybatis%20%E6%A1%86%E6%9E%B6%E6%89%B9%E9%87%8F%E6%8F%92%E5%85%A5%E5%B9%B6%E8%87%AA%E5%8A%A8%E8%8E%B7%E5%8F%96%E8%87%AA%E5%A2%9E%E4%B8%BB%E9%94%AE%20ID%20%E6%97%B6%E8%8E%B7%E5%8F%96%E4%B8%8D%E5%88%B0%E4%B8%BB%E9%94%AE%20ID%20%E5%80%BC)
  * [mybatis 框架和 DM8 框架整合时报错：dm.jdbc.driver.DMException](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#mybatis%20%E6%A1%86%E6%9E%B6%E5%92%8C%20DM8%20%E6%A1%86%E6%9E%B6%E6%95%B4%E5%90%88%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9Adm.jdbc.driver.DMException)
  * [mybatis-plus 整合达梦时，自动生成的 sql 语句报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#mybatis-plus%20%E6%95%B4%E5%90%88%E8%BE%BE%E6%A2%A6%E6%97%B6%EF%BC%8C%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90%E7%9A%84%20sql%20%E8%AF%AD%E5%8F%A5%E6%8A%A5%E9%94%99)
  * [Spring 框架中应用修改数据时报错：试图在只读事务中修改数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#Spring%20%E6%A1%86%E6%9E%B6%E4%B8%AD%E5%BA%94%E7%94%A8%E4%BF%AE%E6%94%B9%E6%95%B0%E6%8D%AE%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%E8%AF%95%E5%9B%BE%E5%9C%A8%E5%8F%AA%E8%AF%BB%E4%BA%8B%E5%8A%A1%E4%B8%AD%E4%BF%AE%E6%94%B9%E6%95%B0%E6%8D%AE)
  * [配置 MYCAT 中间件连接达梦数据库报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E9%85%8D%E7%BD%AE%20MYCAT%20%E4%B8%AD%E9%97%B4%E4%BB%B6%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99)
  * [MyCat 启动报错 1](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#MyCat%20%E5%90%AF%E5%8A%A8%E6%8A%A5%E9%94%99%201)
  * [MyCat 启动报错 2](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#MyCat%20%E5%90%AF%E5%8A%A8%E6%8A%A5%E9%94%99%202)
  * [MyCat 启动报错 3](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#MyCat%20%E5%90%AF%E5%8A%A8%E6%8A%A5%E9%94%99%203)
  * [代码运行报错 1](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E4%BB%A3%E7%A0%81%E8%BF%90%E8%A1%8C%E6%8A%A5%E9%94%99%201)
  * [代码运行报错 2](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E4%BB%A3%E7%A0%81%E8%BF%90%E8%A1%8C%E6%8A%A5%E9%94%99%202)
  * [代码运行报错无效模式名](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E4%BB%A3%E7%A0%81%E8%BF%90%E8%A1%8C%E6%8A%A5%E9%94%99%E6%97%A0%E6%95%88%E6%A8%A1%E5%BC%8F%E5%90%8D)
  * [IDEA 配置 DM 的数据库方言](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#IDEA%20%E9%85%8D%E7%BD%AE%20DM%20%E7%9A%84%E6%95%B0%E6%8D%AE%E5%BA%93%E6%96%B9%E8%A8%80)
  * [DM 数据库支持的驱动](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#DM%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%94%AF%E6%8C%81%E7%9A%84%E9%A9%B1%E5%8A%A8)
  * [JDK 包和驱动不匹配](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#JDK%20%E5%8C%85%E5%92%8C%E9%A9%B1%E5%8A%A8%E4%B8%8D%E5%8C%B9%E9%85%8D)
  * [通过 dm_svc.conf 连接主备集群](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E9%80%9A%E8%BF%87%20dm_svc.conf%20%E8%BF%9E%E6%8E%A5%E4%B8%BB%E5%A4%87%E9%9B%86%E7%BE%A4)
  * [tpc-c 标准测试工具](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#tpc-c%20%E6%A0%87%E5%87%86%E6%B5%8B%E8%AF%95%E5%B7%A5%E5%85%B7)
  * [报错 ”未经授权的用户“ 的原因有哪些？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E6%8A%A5%E9%94%99%20%E2%80%9D%E6%9C%AA%E7%BB%8F%E6%8E%88%E6%9D%83%E7%9A%84%E7%94%A8%E6%88%B7%E2%80%9C%20%E7%9A%84%E5%8E%9F%E5%9B%A0%E6%9C%89%E5%93%AA%E4%BA%9B%EF%BC%9F)
  * [连接尚未建立或者已经关闭](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E8%BF%9E%E6%8E%A5%E5%B0%9A%E6%9C%AA%E5%BB%BA%E7%AB%8B%E6%88%96%E8%80%85%E5%B7%B2%E7%BB%8F%E5%85%B3%E9%97%AD)
  * [语句句柄个数超上限 (1024) 或系统内存不足](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E8%AF%AD%E5%8F%A5%E5%8F%A5%E6%9F%84%E4%B8%AA%E6%95%B0%E8%B6%85%E4%B8%8A%E9%99%90%20\(1024\)%20%E6%88%96%E7%B3%BB%E7%BB%9F%E5%86%85%E5%AD%98%E4%B8%8D%E8%B6%B3)
  * [将查询结果赋值给变量](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%B0%86%E6%9F%A5%E8%AF%A2%E7%BB%93%E6%9E%9C%E8%B5%8B%E5%80%BC%E7%BB%99%E5%8F%98%E9%87%8F)
  * [存进去的数据与读取出来的数据不同](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%AD%98%E8%BF%9B%E5%8E%BB%E7%9A%84%E6%95%B0%E6%8D%AE%E4%B8%8E%E8%AF%BB%E5%8F%96%E5%87%BA%E6%9D%A5%E7%9A%84%E6%95%B0%E6%8D%AE%E4%B8%8D%E5%90%8C)
  * [如何回收空闲会话](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%A6%82%E4%BD%95%E5%9B%9E%E6%94%B6%E7%A9%BA%E9%97%B2%E4%BC%9A%E8%AF%9D)
  * [达梦数据库如何防止 SQL 注入？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E5%A6%82%E4%BD%95%E9%98%B2%E6%AD%A2%20SQL%20%E6%B3%A8%E5%85%A5%EF%BC%9F)
  * [开启 ssl 认证后连接数据库报错：网络通讯异常](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%BC%80%E5%90%AF%20ssl%20%E8%AE%A4%E8%AF%81%E5%90%8E%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99%EF%BC%9A%E7%BD%91%E7%BB%9C%E9%80%9A%E8%AE%AF%E5%BC%82%E5%B8%B8)
  * [定时任务中适配 quartz blob 字段不支持的处理方法](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%AE%9A%E6%97%B6%E4%BB%BB%E5%8A%A1%E4%B8%AD%E9%80%82%E9%85%8D%20quartz%20blob%20%E5%AD%97%E6%AE%B5%E4%B8%8D%E6%94%AF%E6%8C%81%E7%9A%84%E5%A4%84%E7%90%86%E6%96%B9%E6%B3%95)
  * [应用 SQL 语句中 select 中别名为小写字母时，如何在查询结果中自动转换为大写字母](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%BA%94%E7%94%A8%20SQL%20%E8%AF%AD%E5%8F%A5%E4%B8%AD%20select%20%E4%B8%AD%E5%88%AB%E5%90%8D%E4%B8%BA%E5%B0%8F%E5%86%99%E5%AD%97%E6%AF%8D%E6%97%B6%EF%BC%8C%E5%A6%82%E4%BD%95%E5%9C%A8%E6%9F%A5%E8%AF%A2%E7%BB%93%E6%9E%9C%E4%B8%AD%E8%87%AA%E5%8A%A8%E8%BD%AC%E6%8D%A2%E4%B8%BA%E5%A4%A7%E5%86%99%E5%AD%97%E6%AF%8D)
  * [执行 SQL 语句，驱动返回层报错：变量空间溢出](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E6%89%A7%E8%A1%8C%20SQL%20%E8%AF%AD%E5%8F%A5%EF%BC%8C%E9%A9%B1%E5%8A%A8%E8%BF%94%E5%9B%9E%E5%B1%82%E6%8A%A5%E9%94%99%EF%BC%9A%E5%8F%98%E9%87%8F%E7%A9%BA%E9%97%B4%E6%BA%A2%E5%87%BA)
  * [应用连接 DM7 数据库报错：​"Error：JVMCFRE003 bad major version​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%BA%94%E7%94%A8%E8%BF%9E%E6%8E%A5%20DM7%20%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99%EF%BC%9A%22Error%EF%BC%9AJVMCFRE003%20bad%20major%20version%22)
  * [dm_svc.conf 文件是否可以使用普通用户定义并配置自定义路径](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#dm_svc.conf%20%E6%96%87%E4%BB%B6%E6%98%AF%E5%90%A6%E5%8F%AF%E4%BB%A5%E4%BD%BF%E7%94%A8%E6%99%AE%E9%80%9A%E7%94%A8%E6%88%B7%E5%AE%9A%E4%B9%89%E5%B9%B6%E9%85%8D%E7%BD%AE%E8%87%AA%E5%AE%9A%E4%B9%89%E8%B7%AF%E5%BE%84)
  * [执行环境堆栈空间不足](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E6%89%A7%E8%A1%8C%E7%8E%AF%E5%A2%83%E5%A0%86%E6%A0%88%E7%A9%BA%E9%97%B4%E4%B8%8D%E8%B6%B3)
  * [如何让返回的结果集中列的大小写和 SQL 中写的大小写保持一致？](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%A6%82%E4%BD%95%E8%AE%A9%E8%BF%94%E5%9B%9E%E7%9A%84%E7%BB%93%E6%9E%9C%E9%9B%86%E4%B8%AD%E5%88%97%E7%9A%84%E5%A4%A7%E5%B0%8F%E5%86%99%E5%92%8C%20SQL%20%E4%B8%AD%E5%86%99%E7%9A%84%E5%A4%A7%E5%B0%8F%E5%86%99%E4%BF%9D%E6%8C%81%E4%B8%80%E8%87%B4%EF%BC%9F)
  * [应用程序执行 SQL 报错：不支持该数据类型](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E6%89%A7%E8%A1%8C%20SQL%20%E6%8A%A5%E9%94%99%EF%BC%9A%E4%B8%8D%E6%94%AF%E6%8C%81%E8%AF%A5%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)
  * [字符串转换出错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#%E5%AD%97%E7%AC%A6%E4%B8%B2%E8%BD%AC%E6%8D%A2%E5%87%BA%E9%94%99)
  * [MySQL 迁移到 DM 后 getObject 接口返回值不一致](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java-new.html#MySQL%20%E8%BF%81%E7%A7%BB%E5%88%B0%20DM%20%E5%90%8E%20getObject%20%E6%8E%A5%E5%8F%A3%E8%BF%94%E5%9B%9E%E5%80%BC%E4%B8%8D%E4%B8%80%E8%87%B4)



## 正文

### JDBC 程序获取存储过程元数据信息为空

【问题描述】

利用如下程序获取存储过程的元数据信息，执行完后获取的元数据信息为空，jdbc 程序代码片段如下：

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

【问题解决】

需要 JDBC 打开兼容 Oracle 模式，在 jdbc 的 url 串中添加 compatibleMode=oracle，例如：

```
jdbc:dm//localhost:5236?compatibleMode=oracle
```

### JDBC 驱动连接数据库报错：failed to initialize ssl

【问题描述】

JDBC 驱动连接数据库报错：failed to initialize ssl 。

【问题解决】

该问题由于未配置 SSL 环境而使用 SSL 登录导致，应当关闭 SSL 参数。

  1. 使用 SYSDBA 登录执行以下语句： 
    
        SP_SET_PARA_VALUE(2,'ENABLE_ENCRYPT',0);
    

  2. 重启达梦数据库服务。



### JDBC 如何通过服务名连接读写分离集群

【问题解决】

dm_svc.conf 文件位置：

Linux 环境将 dm_svc.cof 文件放在应用服务器 /etc 目录下；Windows 32 位环境将 dm_svc.cof 文件放在应用服务器 system32 目录下；Windows 64 位环境将 dm_svc.cof 文件放在应用服务器 system32 和 syswow64 目录下。
应用连接串配置：

```
<DRIVER>dm.jdbc.driver.DmDriver</DRIVER>
<URL>jdbc:dm://GRP_RW1?rwSeparate=1&rwPercent=0</URL>
```

> **注意**
>
> dm_svc.conf 文件不是在主机或备机上配置，而是在应用所在的机器上配置。 应用所在的机器上没有要求必须安装数据库，可以自己新建一个
> dm_svc.conf 文件来使用。



### JDBC 驱动连接数据库 Error: for input String 8

【问题解决】

需使用对应版本的 JDBC 驱动，可以在数据库安装目录的 drivers/jdbc 目录下找到对应的驱动包 DmJdbcDriver18.jar 。如有其他问题，请在[社区](https://eco.dameng.com/community/question?_blank)内咨询。

### JDBC 连接 DM 数据库时，是否可以使用 dm8 的驱动连接 dm7 的数据库

【问题解决】

可以，但是最好使用 DM8 的驱动去连 DM8 。

### JDBC 连接数据库查询的时间与客户端连接查询的时间相差 8 个小时

【问题解决】

检查数据库服务器 / 应用服务器 / JDBC 连接串属性 / dm_svc.conf 中的时区设置。若确认都设置正确，建议申请一线技术支持。

### JDBC 程序中如何获取存储过程的元数据信息

【问题描述】

利用如下程序获取存储过程的元数据信息，执行完后获取的元数据信息为空，jdbc 程序代码片段如下：

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

【问题解决】

需要 JDBC 打开兼容 Oracle 模式，在 jdbc 的 url 串中添加 compatibleMode=oracle 。例如：

```
jdbc:dm//localhost:5236?compatibleMode=oracle 。
```

### JDBC XA 框架下应用报错："事务分支中发生资源管理器错误"

【问题描述】

在 JDBC XA 框架下高并发应用报错："事务分支中发生资源管理器错误"。

![图片 15.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604141708XQKYM8M4NAQENS0CZL)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps8.jpg)

【问题分析】

XA 框架为二次提交模式，在准备阶段有一个超时时间，如果超过了阈值则会话会报错回滚，高并发下响应时间变长可能会出现此问题。

【问题解决】

调整参数 XA_TRX_IDLE_TIME 和 XA_TRX_LIMIT 至一个合适值，建议 XA_TRX_IDLE_TIME=600 , XA_TRX_LIMIT=10000 。

参数解释：

  1. XA_TRX_IDLE_TIME：允许未 PRE-COMMITTED 的 XA 事务活动的时间，单位秒，取值范围 3036000，默认值为 60 。
  2. XA_TRX_LIMIT：XA 事务数量上限，取值范围 102465535，默认 1024 。



### 使用 JDBC 连接如何配置字符集

【问题解决】

JDBC 加 characterEncoding=XX（字符集）或者在 dm_svc.conf 中配置。

DM 安装时生成一个配置文件 dm_svc.conf ，不同的平台所在目录有所不同。

32 位的 DM 安装在 Win32 操作平台下，此文件位于 %SystemRoot%\system32 目录；

64 位的 DM 安装在 Win64 操作平台下，此文件位于 %SystemRoot%\system32 目录；

32 位的 DM 安装在 Win64 操作平台下，此文件位于 %SystemRoot%\SysWOW64 目录；

在 Linux 平台下，此文件位于 /etc 目录。

dm_svc.conf 文件中包含 DM 各接口及客户端需要配置的一些参数，具体的配置项如下：

|**配置项**|**缺省值**|**简述**|
|---|---|---|
|CHAR_CODE|操作系统编码格式|客户端使用的编码格式，会影响帮助信息和错误提示信息，要与客户端使用的编码格式一致。支持的选项为：PG_UTF8（表示 UTF8 编码）；PG_GBK/PG_GB18030（两者都表示 GBK 编码）；PG_BIG5（表示 BIG5 编码）；PG_ISO_8859_9（表示 ISO88599 编码）；PG_EUC_JP（表示 EUC_JP 编码）；PG_EUC_KR（表示 EUC_KR 编码）；PG_KOI8R（表示 KOI8R 编码）；PG_ISO_8859_1（表示 ISO_8859_1 编码）。可以不指定，若不指定，系统会读取操作系统信息获得编码信息，建议有需要才指定。|


### 应用 JDBC 连接数据库失败问题/连接超时

【问题解决】

首先验证 disql 连接性，其次排查参数 MAX_SESSIONS 的设置，MAX_SESSIONS 参数在安装目录的 dm.ini 文件里，如果都没问题在 jdbc 连接串里设置 socketTimeout 套接字配置超时时间。如果还是不行，尝试更换 jar 包。

### JDBC 执行 explain 语句，resultset 是空的，用了 explain for，resultset 还是空的

【问题解决】

用 rowset 来执行 explain for 语句。

### 使用 DM8 的 JDBC 驱动连接 DM7 数据库，报错：“网络通讯“ 错误和 “拒绝连接”

【问题描述】

应用使用 DM8 的 JDBC 驱动连接 DM7 数据库，但是报达梦 jdbc 驱动 “网络通信” 相关错误和 “拒绝连接” 错误，无法连接数据库。问题描述如下

```
WARN [main] (c.d.dms.metadata.database.JdbcDataSource):72 -数据源连接失败: 网络通信的异常: 拒绝连接 (Connection refused)
com.dameng.dms.framework.exception.SystemException: 网络通信错误异常: 拒绝连接 (Connection refused)
Caused by: dm.jdbc.driver.DMException: 网络通信的异常
```

【问题解决】

按以下步骤进行排查：

  1. 确认本地数据库服务启动，本地 disql 可以登录，从应用服务器可以登录；
  2. 确认数据库版本和替换对应版本的 JDBC 驱动版本测试；
  3. 测试调试应用，是否属于应用问题。



例如：可能是应用设置了防火墙，未打开需要的端口（8087）等原因。解决办法是打开防火墙相应的端口：

```
systemctl status firewalld.service
firewall-cmd --list-all
firewall-cmd --zone=public --add-port=8087/tcp --permanent
firewall-cmd --list-all
firewall-cmd --reload
firewall-cmd --list-all
```

### 报错：driver dm.jdbc.driver.DmDriver is not sequoia compliant

【问题描述】

JDK 包和驱动不匹配 driver dm.jdbc.driver.DmDriver is not sequoia compliant ，详细信息如下:

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052915171950OU6W11TRC2KZR7UQ)

【问题解决】

更换 JDK 包即可，使用 DM 安装数据库后自带的 JDK 包。

### 插入大字段数据报错 Caused by: dm.jdbc.driver.DMException: 数据大小已超过可支持范围

【问题描述】

排查发现数据库中存储图片的数据类型为 VARBINARY 。

【问题解决】

数据类型修改为 IMAGE/BLOB 后解决。

### buffer index out of range

【问题描述】

批量插入数据的时候插一条没问题，插入两条及以上的时候提示 ”buffer index out of range“ 。

【问题解决】

可按照如下步骤进行排查：

  1. 可能是 JDBC 驱动不匹配，建议使用较新的驱动。
  2. 换驱动后还有问题，可以尝试将 dm.ini 修改 BATCH_PARAM_OPT = 1 ，然后重启一下数据库服务。
  3. 如果还有问题，修改 JDBC 的 URL，开启 JDBC 的 log 日志辅助分析。可参考：[达梦数据库 JDBC 如何开启 log 日志](https://eco.dameng.com/docs/zh-cn/article/article-011.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93-JDBC-%E5%A6%82%E4%BD%95%E5%BC%80%E5%90%AF-log-%E6%97%A5%E5%BF%97)



### Communication error "dm network timeout executor" Set network timeout error 。

【问题解决】

具体请参考 [Communication 通信错误问题分析](https://eco.dameng.com/docs/zh-cn/article/article-011.html#Communication-%E9%80%9A%E4%BF%A1%E9%94%99%E8%AF%AF%E9%97%AE%E9%A2%98%E5%88%86%E6%9E%90) 。

### Java 的连接配置方式

【问题解决】

  1. 连接单机数据库配置如下：



![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530112720X8OHJIU3ABWU87YKIT)

  2. 连接集群配置



在应用程序所在服务器上配置 dm_svc.conf 文件，文件中配置服务名，通过服务名连接集群可实现故障自动重连，参考如下配置，服务名为 dw_svc 。

```
dw_svc=(192.168.100.136:32141,192.168.100.137:32142)
LOGIN_MODE=(1)
SWITCH_TIME=(3)
SWITCH_INTERVAL=(1000)
RW_SEPARATE=(1)
RW_PERCENT=(25)
CHAR_CODE=(PG_UTF8)
```

Windows 环境下：dm_svc.conf 文件位于 %SystemRoot%\system32 目录下。

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530112752XB18BP6PY0L2OJ8X83)

Linux 环境下：dm_svc.conf 文件位于 /etc 目录下。

![图片 12.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530113345RM0F20HKFGWKIUMSHW)

JDBC URL 连接串写法如下：

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240603111707DRG2GMIKUPROLX02PL)

其他环境：在部分云环境下配置达梦的数据库连接时，由于云环境无法配置 /etc/dm_svc.conf 文件，针对集群的连接就无法配置服务名了。此时，可利用 jdbc 连接串中指定服务名的属性连接 DM 数据库，使用方式如下：

```
jdbc:dm://test?test=(192.168.137.101:5236，192.168.137.102:5236) & loginMode=1
```

### Java 更新 clob 或 blob 字段，提示无效的十六进制数字

【问题解决】

关于大字段的更新，请参考《DM 程序员操作手册》的 JDBC 相关章节，手册位于数据库安装路径的 doc 文件夹下。

### Java 程序指定访问模式

【问题描述】

数据库一个用户下存在多个模式，JAVA 程序访问登录的数据库用户下非默认模式下的对象时，不在 SQL 中添加"模式名.对象名" 的方式来访问数据的方法。

【问题解决】

使用较新版本的达梦 JDBC 驱动（比如 2021 年 4 月之后的驱动），可以在 JDBC 连接串中指定 schema 属性，配置 schema=模式名，来指定用户登录后的当前模式。默认不配置 schema 属性表示使用默认模式。

举例说明：

数据库用户 TEST 下有两个模式 TEST 和 TEST1，其中 TEST 为默认模式。JDBC 中指定 TEST 登录后的当前模式为 TEST1，配置如下：

```
String name="dm.jdbc.driver.DmDriver";
String url="jdbc:dm://192.168.15.35:5236?schema=TEST1";     //使用schema指定当前模式名
String user="TEST";
String password="123456789";
```

这样使用 TEST 用户登录后，就可以不需要在表名前加上模式名 TEST1。

### Java 程序使用 SSL 认证连接数据库报错

【问题描述】

```
javax.net.ssl.SSLException: Received fatal alert: protocol_version
```

【问题分析】

可能是 DM JDBC 驱动包版本不对或者比较低。

【问题解决】

更换对应达梦数据库版本的 JDBC 驱动包。

### Java 应用报错：For input string: "0.000000"

【问题分析】

由于 decimal(22,6) 数据类型输出的数据为 "0.000000" 格式，但应用程序使用整型类型进行接收，导致出现报错：For input string: "0.000000"。decimal 数据类型说明如下表：

|**类型**|**语法**|**说明**|
|--|--|--|
|精确数值数据类型|DECIMAL(精度 , 标度)|精度是一个无符号整数，定义了总的数字数，精度范围是 1 至 38，标度定义了小数点右边的数字位数。一个数的标度不应大于其精度，缺省精度为 38，标度无限定。|


【问题解决】

可以选择在程序中重新定义数据类型解决或者选择在数据库中修改数据类型。

方法一：程序中重新定义数据类型。

正常的展示浮点类型方法。

```
Integer.parseInt('0.000000')
```

数据库中存储为浮点，但是应需求需展示为整点的。

```
int i = new BigDecimal('0.000000').intValue();
```

方法二：数据库中修改数据类型。

数据库中数据类型将 decimal(22,6) 调整为 decimal(22,0) 或者修改类型为 int 类型。

### Java 程序连接读写分离集群报错：“服务器模式不匹配”

【问题分析】

此类错误往往因为 dm_svc.conf 配置出错或者当前会话达到用户最大连接数，可以从这两个方面检查分析。

【问题解决】

  1. 检查 dm_svc.conf 中是否配置了不存在的参数，或者其他参数配置有误，集群相关参数是否配置成全局，如 LOGIN_MODE 参数。dm_svc.conf 正确配置如下：



```
##全局服务配置
TIME_ZONE=(480)
LANGUAGE=(cn)

##局部配置
[DMRW]
LOGIN_MODE=(1)
RW_SEPARATE=(1)
RW_PERCENT=(25)
SWITCH_TIME=(3000)
SWITCH_INTERVAL=(200)
```

  2. 检查应用连接池配置是否释放连接和长连接设置值。
  3. 检查用户的最大的连接数。



```
select
b.username as "达梦数据库用户名",
a.CONN_IDLE_TIME as "会话最大空闲时间",
a.failed_num as "失败次数限制" ,
a.lock_time as "锁定时间(min)",
A.LOCKED_STATUS,
A.SESS_PER_USER as "用户最大连接数",
A.LIFE_TIME,
A.REUSE_TIME,
A.REUSE_MAX,
A.LASTEST_LOCKED
from sysusers a right join all_users b on
a.id=b.user_id;
```

  5. 检查数据库的最大连接数。



```
select * from v$dm_ini where para_name ='MAX_SESSION';
```

  6. 查看当前活动会话数，对比当前会话是否达到最大连接数。



```
Select count(1) from v$sessions where state='ACTIVE';
```

dm_svc.conf 部分参数说明：

|**参数**|**缺省值**|**描述**|
|---|---|---|
|TIME_ZONE|操作系统当前时区|指明客户端的默认时区，设置范围为：-779~840M，如 60 对应+1:00 时区。|
|LANGUAGE|操作系统语言|当前数据库服务器使用的语言，会影响帮助信息错误和提示信息。支持的选项为：CN（表示中文）和 EN（表示英文）。可以不指定，若不指定，系统会读取操作系统信息获得语言信息，建议有需要才指定。|
|LOGIN_MODE|4|指定优先登录的服务器模式。0：优先连接 PRIMARY 模式的库，NORMAL 模式次之，最后选择 STANTBY 模式；1：只连接主库；2：只连接备库；3：优先连接 STANDBY 模式的库，PRIMARY 模式次之，最后选择 NORMAL 模式；4：优先连接 NORMAL 模式的库，PRIMARY 模式次之，最后选择 STANDBY 模式|
|RW_SEPARATE|0|是否启用读写分离。0：不启用；1：启用；2：启用，备库由客户端进行选择，且只会选择服务名中配置的节点。|
|RW_PERCENT|25|读写分离分发比例，有效值范围 0~100。|
|SWITCH_TIMES|1|以服务名连接数据库时，若未找到符合条件的库成功建立连接，将尝试遍历服务名中库列表的次数。有效值范围：1~9223372036854775807。|
|SWITCH_INTERVAL|200|在服务器之间切换的时间间隔，单位为毫秒，有效值范围：1~9223372036854775807。|


此外在全局服务中还可以配置服务名，用于数据库登录。例如：在全局服务配置中配置服务名为 DMRW 的服务

```
DMRW=(10.10.20.3:5236,110.10.20.4:5236,10.10.20.5.103:5236)
```

名词解释：

服务名：用于连接的服务名，参数值格式为：服务名=(IP[:PORT],IP[:PORT],......)。IP 为数据库所在的 IP 地址，如果是 IPV6 地址，为了区分端口，需要用[]封闭 IP 地址。PORT 为数据库使用的 TCP 连接端口，可选配置，不配置则使用默认的端口。

> **注意**
>
> 若配置服务名时没有指定端口，后续在工具或接口的命令中使用服务名时，在服务名后增加的―:XXXX‖端口号会被忽略，这样指定的端口号是无效的。



更多详细参数说明请参考数据库安装目录 doc 目录下的《DM8 系统管理员手册》。

### 使用 getTypeInfo() 方法时报错："无效的列名 AUTO_INCREMENT"

【问题描述】

Java 语言应用使用 getTypeInfo() 方法时报错，驱动抛出异常 “无效的列名 AUTO_INCREMENT”。

【问题解决】

如果遇到该报错可以通过在 dm_svc.conf 文件中配置 EP_SELECTOR=(1) 和 AUTO_RECONNECT=(1) 参数来解决该报错。

### Druid 连接池连接达梦数据库报错：“dbType not support”

【问题描述】

使用 druid 连接池连接达梦报错：“dbtype not support”。

![图片 16.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604144019QCOEF9VTRZF4MPHP8M)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps9.jpg)

【问题分析】

Druid 使用的 wallFilter 中的防火墙监控不支持 DM 数据库。

【问题解决】

去掉 spring.datasource.filters 的 wall 配置，也就是去掉 .propertity 配置文件中 filters 配置项中的 wall 即可。

### DM 如何支持 C3P0 连接池？

【问题解决】

参考下面的 SpringBoot 集成 MyBatis 与 C3P0 连接池例子：

  1. 添加 JDBC 驱动



![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530100921HG1ICAVSVJJVVWCEDI)

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530101840AB08J5QUL3FI7QC6UU)

  2. 通过 application.properties 配置文件配置数据源



![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530102218ORRB068MBACGB3LFM4)

  3. 创建 DataSourceConfig 类



![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530102331EQ5PZYI1QMPS2EN189)

  4. 测试代码—实体类



![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530104957PQ73Y2TORIZIYOLWI2)

  5. XML 映射文件



![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530105010U5YLH76FVV7O6DQVXG)

  6. Controller



![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530105640BKUCGK4RUBP99YHJN7)

  7. 执行结果



![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530105707ZH57WBDKQGIV9RDGRD)

### Druid 报错：unkow jdbc driver

【问题描述】

```
16:16:50,872 ERROR main pool.DruidDataSource:658 - dataSource init error
java.sql.SQLException: unkow jdbc driver : jdbc:dm://localhost:5236
at com.alibaba.druid.util.JdbcUtils.getDriverClassName(JdbcUtils.java:416)
```

【问题解决】

因为 Druid 1.0.11 及以前版本，不能省略 driverClassName 参数否则出现 unkow jdbc driver 异常，请升级到最新版，如 Druid 1.1.20（推荐）或 配置 `driverClassName=dm.jdbc.driver.DmDriver` 。

### Druid 报错：dbType not support

【问题描述】

```
16:01:02,007 ERROR main pool.DruidDataSource:983 - {dataSource-1} init error
java.lang.IllegalStateException: dbType not support : dm, url jdbc:dm://localhost:5236
at com.alibaba.druid.wall.WallFilter.init(WallFilter.java:167)
```

配置了 wall Filter（防火墙过滤），报异常：dbType not support 。

【问题解决】

去掉 wall 配置，在对应的配置文件里把 fitter 的 wall 删掉。

### 如何配置 DruidDataSource 数据源连接 DM 数据库

【问题解决】

连接池配置支持 DruidDataSource 数据源的连接，参考通用配置：

```
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
<property name="url" value="${jdbc_url}" />
<property name="username" value="${jdbc_user}" />
<property name="password" value="${jdbc_password}" />
<property name="filters" value="stat" />
<property name="maxActive" value="20" />
<property name="initialSize" value="1" />
<property name="maxWait" value="60000" />
<property name="minIdle" value="1" />
<property name="timeBetweenEvictionRunsMillis" value="60000" />
<property name="minEvictableIdleTimeMillis" value="300000" />
<property name="testWhileIdle" value="true" />
<property name="testOnBorrow" value="false" />
<property name="testOnReturn" value="false" />
<property name="poolPreparedStatements" value="true" />
<property name="maxOpenPreparedStatements" value="20" />
<property name="asyncInit" value="true" /></bean>
```

配置说明：

name： 配置这个属性的意义在于，如果存在多个数据源，监控的时候可以通过名字来区分开来。如果没有指明 name ，则会自动生成一个名字，格式是：“DataSource-” + System.identityHashCode(this) 。另外配置此属性至少在 1.0.5 版本中是不起作用的，强行设置 name 会出错。

url：连接数据库的连接串，不同数据库配置不一样，DM 数据库可参考如下配置：

```
jdbc.driver=dm.jdbc.driver.DmDriver（达梦数据库驱动）
jdbc.url=jdbc:dm://192.168.10.1:5236（数据库 IP：端口号）
jdbc.username=SZ_TYSL（用户名）
jdbc.password=123456789（用户密码）
```

driverClassName： 默认可根据 URL 自动识别。这一项可配可不配，如果不配置 druid 会根据 URL 自动识别 dbType，然后选择相应的 driverClassName。

initialSize： 初始化时建立物理连接的个数，默认值为 0，初始化发生在显式调用 init 方法，或者第一次 getConnection 时。

maxActive：最大连接池数量，默认值为 8。

maxIdle： 已经不再使用，配置了也没效果，可忽略。该参数是 Druid 为了方便 DBCP 用户迁移而增加的，maxIdle 是一个混乱的概念。连接池只应该有 maxPoolSize 和 minPoolSize，druid 只保留了 maxActive 和 minIdle，分别相当于 maxPoolSize 和 minPoolSize。

minIdle： 最小连接池数量。

maxWait： 获取连接时最大等待时间，单位毫秒。配置了 maxWait 之后，缺省启用公平锁，并发效率会有所下降，如果需要可以通过配置 useUnfairLock 属性为 true 来使用非公平锁。

### 德鲁伊（druids）连接池相关配置推荐

【问题解决】

```
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource"init-method="init" destroy-method="close">
<property name="driverClassName" value="${jdbc.driver}" />
<property name="url" value="${jdbc.url}" />
<property name="username" value="${jdbc.username}" />
<property name="password" value="${jdbc.password}" />
<property name="filters" value="stat,log4j" />
</bean>
```

### Druid 连接达梦报错

【问题描述】

```
com.alibaba.druid.pool.GetConnectionTimeoutException: wait millis 10003, active 326, runningSqlCount 254 
```

【问题解决】

升级 Druid 到最新版本，修改数据库配置文件 dm.ini 文件里面的 MAX_SESSIONS 参数，值适当调大并重启数据库。

### Springboot 使用 druid 连接池时报错：testWhileIdle is true, validationQuery not set

【问题描述】

报错信息如下图：

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604095812W643YPOAYJBRIYUYWV)

【问题解决】

引起该报错的主要原因是没有设置 SQL 语句测试连接数据库的可用性造成的。在配置文件添加如下配置可以解决该问题：

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604101105TPR70LP6WAQTA74JQM)

### 在进行连接池测试时，报错 “java.lang.ClassNotFoundException”

【问题解决】

排查是否出现以下情况：

  1. DM 的 JDBC 驱动没有放置在正确的路径下。
  2. DM 的 JDBC 驱动的类名写错了。



### Java springboot 引入数据库的 jar 包，数据源监听到 SQL 已经执行，但没有返回值

【问题解决】

注意在执行 SQL 语句后，要执行 commit; 进行提交。举例：

```
select "id","u_acount","u_password","u_kind","u_status" from "TEST"."table_user" limit 1;  
commit; 
//需要在 sql 里面提交
```

### springBoot jpa 使用达梦数据库和驱动后程序报错:"Failed to bind properties under...

【问题描述】

springBoot jpa 使用达梦数据库和驱动后程序报错：“Failed to bind properties under 'spring.jpa.database' to org.springframework.orm.jpa.vendor.Database”。

【问题分析】

orm 框架中源码中不包含识别 DM 的逻辑，需进行源码框架调整。

![图片 12.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604113622H6SP1WR5OFL3EDWSBI)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps4.jpg)

【问题解决】

  1. 使用 druid1.2.7 的连接池，该版本有对 DM 兼容。



![图片 13.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604113655GJG5T526F8IR49SXXT)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps5.png)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps6.jpg)

  2. 修改框架源码 org.springframework.orm.jpa.vendor.Database 添加 DM 。



```
package org.springframework.orm.jpa.vendor;
public enum Database {
DEFAULT,
DB2,
DERBY,
H2,
HANA,
HSQL,
INFORMIX,
MYSQL,
ORACLE,
DM,
POSTGRESQL,
SQL_SERVER,
SYBASE;
private Database() {
}
}
```

### activiti 工作流是否支持达梦数据库

【问题解决】

支持，可以参考 [如何让 Activiti 支持 DM7](https://eco.dameng.com/community/article/2510375cab5d1072f39569d379db3d56) 。

### activiti 连接 DM 数据库报错

【问题描述】

```
Unknown database: DM DBMS；Error getting default schema
```

【问题解决】

activiti 支持连接 DM 数据库，我们需要解决的是如何配置的问题，只需要修改以下两个配置文件：

  1. jdbc 的配置：更换驱动、类名、连接串等，下面是配置方式：



![图片 13.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024053015091243TQMEGJEYCP7K8RC8)

  2. activiti 自己的配置（要找到 activiti 的配置文件），强制指定数据库为 Oracle（默认无该项配置）:



![图片 14.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530150927IKG514WRDXCUIXNYXF)

这里的意思是：数据类型设置为 Oracle（在长期使用过程中，没有任何问题，DM 数据库的 Oracle 兼容性做的非常好，很多相关框架，都可以使用类似配置方式）。

如果是使用了方言包，还需要注意修改方言包的类名，以及方言包的引用，配置方式如下：

```
datasource.driver=dm.jdbc.driver.DmDriver
datasource.url=jdbc:dm://localhost:5236
datasource.username=test
datasource.password=testcsdc1
hibernate.dialect=org.hibernate.dialect.DmDialect
```

Java 作为一个最通用的平台框架，其数据库无关性做的比较好，所有和 Java 相关的配置方式，最核心的就是下面这张图：

![图片 15.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405301542064NKU5B5PBVWOTM7MLM)

### activiti 工作流因大小写敏感问题导致工作流引擎报错引发异常

【问题描述】

activiti 工作流 5.23 在适配达梦数据库时，如果 DM 初始化实例时设置参数大小写敏感，在通过工作流引擎写入 act_ru_variable 表的 NAME_ 字段值时，该字段以大写值 SYS_ORG_CODE 插入表中，但在工作流引擎以该字段作为查询条件进行结果集的查询过滤时，以小写值 sys_org_code 进行结果集过滤查询，这样会导致查询不到对应结果集而导致工作流引擎报错引发异常。

【问题解决】

为解决该问题，需要手动控制工作流引擎在写入该字段时必须以小写值的形式写入，这样在查询的时候以小写的形式查询过滤，便可获得正确的结果集，从而避免写入数据库中的值和查询过滤条件值大小写形式不一致的情况发生。

在 ActivitiController.java 类指定位置增加以下两行代码，把 NAME_ 字段的值替换为小写的形式，通过手动限定 taskVariables 里面指定变量的值为小写，在执行 `this.taskService.complete(taskid,taskVariables); ` 值写入时，把 NAME_ 字段的值以小写的形式写入到数据库中。如下图所示：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604093823L1GMRFKJQUQIO99PWS)

修改后，重新验证工作流程是否可以正常流转，发现工作流引擎写入 NAME_ 字段的值为小写，整个工作流程可以正常的流转起来。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406040941250APJUAZO5XEZZGJZDY)

### 报错：Caused by: org.activiti.engine.

【问题描述】

```
ActivitiException: couldn't deduct database type from database product name 'DM DBMS' 。
```

【问题解决】

请参考：[activiti 连接 DM 数据库报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-java.html#activiti-%E8%BF%9E%E6%8E%A5-DM-%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99) 。

在驱动 url 里面添加扩展连接属性 comOra=true , 例如：

```
jdbc:dm://localhost:5236?comOra=true
```

### hibernate 连接数据库的方言写法

【问题解决】

Hibernate.cfg.xml 配置要求：

驱动名称

```
<property name="connection.driver_class">dm.jdbc.driver.DmDriver</property>
```

方言包名称

```
<property name="dialect">org.hibernate.dialect.DmDialect</property>
```

举例：[达梦数据库通过 hibernate 连接的基本程序配置](https://eco.dameng.com/docs/zh-cn/article/article-011.html#%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E9%80%9A%E8%BF%87-hibernate-%E8%BF%9E%E6%8E%A5%E7%9A%84%E5%9F%BA%E6%9C%AC%E7%A8%8B%E5%BA%8F%E9%85%8D%E7%BD%AE) 。

### 用 hibernate 执行 sql，参数为 null 的时候报错 “无效的十六进制数据”

【问题解决】

请从达梦云适配中心[下载试用](https://eco.dameng.com/download/?_blank)下载最新的达梦数据库，使用最新的驱动。

### hibernate 配置主键默认、序列插入报错：违反非空约束

【问题解决】

修改 hibernate 配置以及表主键属性为 identity，可以参考：[hibernate 使用序列作为主键在国产达梦数据库中的配置方法](https://eco.dameng.com/docs/zh-cn/article/article-011.html#hibernate-%E4%BD%BF%E7%94%A8%E5%BA%8F%E5%88%97%E4%BD%9C%E4%B8%BA%E4%B8%BB%E9%94%AE%E5%9C%A8%E5%9B%BD%E4%BA%A7%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E4%B8%AD%E7%9A%84%E9%85%8D%E7%BD%AE%E6%96%B9%E6%B3%95) 。

### DM Hibernate 方言包兼容 Oracle 和 MySQL 的主键生成策略

【问题解决】

参考链接：[Hibernate 主键生成策略](https://eco.dameng.com/community/article/62d5559355f60597dc38a397e0fe857b) 。

### hibernate 框架运行时报错："Could not find setter for LOCATION on class com.xx.po.MeetingRoom"

【问题描述】

Java 的 hibernate 框架运行时报错如下:

```
“org.hibernate.PropertyNotFoundException: Could not find setter for LOCATION on class com.xx.po.MeetingRoom”
```

【问题分析】

  1. 顶层堆栈在 org.hibernate.property.ChainedPropertyAccessor.getSetter，说明已经查询到数据，在进行数据赋值时报错。
  2. hibernate 框架中数据赋值是通过类的 setter 方法即动态代理方式赋值，需确认该映射类中是否有该属性的 set、get 方法。
  3. 可能是 JDBC 驱动 hibernate 的方言包与 hibernate 版本不匹配导致。



【解决方案】

可以通过以下方法尝试解决：

  1. 检查 jdbc 驱动 hibernate 的方言包与 hibernate 版本是否匹配。
  2. 确认该实体类的报错字段是否是数据库关键字，可以在 jdbc 后面加上关键字过滤或者换个其他字段名。



### 应用连接数据库后启动报错：”hibernate.dialect' not set“

【问题描述】

应用连接数据库启动报如下错误：

```
Caused by: org.hibernate.HibernateException: Access to DialectResolutionInfo cannot be null when 'hibernate.dialect' not set
at org.hibernate.engine.jdbc.dialect.internal.DialectFactoryImpl.determineDialect(DialectFactoryImpl.java:100)
at org.hibernate.engine.jdbc.dialect.internal.DialectFactoryImpl.buildDialect(DialectFactoryImpl.java:54)
```

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps1.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps2.jpg)

【问题分析】

分析报错日志发现数据库连接失败、方言包未引用成功，该报错是由于未配置达梦的方言包导致。

【问题解决】

添加与当前使用的 hibernata 版本一致的方言包。DM 数据库的 hibernata 方言包保存在数据库安装目录下的 drivers/jdbc/dialect 目录下。

### Error parsing XML: /hibernate.cfg.xml(1) 文档类型声明包含或指向的标记声明格式有误

【问题描述】

http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd 在此 URL 下，获得是一个显示 DTD 的简单 HTML 页面，没有获取 DTD 文件本身。

【问题解决】

将该 DTD 保存到本地硬盘，并修改 URL 解决。

### JavaWeb 框架如何连接访问 DM 数据库

【问题解决】

通用的写法如下：

```
// 定义 DM JDBC 驱动串
String jdbcString = "dm.jdbc.driver.DmDriver";
// 定义 DM URL 连接串
String urlString = "jdbc:dm://localhost:5236";
// 定义连接用户名
String userName = "SYSDBA";
// 定义连接用户口令
String password = "SYSDBA";
```

不同的 JavaWeb 框架根据不同连接池修改相应的连接字符。例如 C3P0 连接池连接达梦，配置信息如下：

```
<property name="jdbcUrl" value=" dm.jdbc.driver.DmDriver" />
<property name="driverClass" value=" jdbc:dm://192.168.1.1:5236" />
<property name="user" value="SYSDBA" />
<property name="password" value="SYSDBA" />
```

达梦数据库 Java 连接接口以及示例在《程序员手册》中有介绍，JDBC 相关框架: ssh , ssm , spring boot , spring cloud 。

连接池：连接达梦和 Oracle 基本类似，只有 driver 名称和 url 不一样，走 JDBC 的数据库包括 sqlserver 和 MySQL 配置上都比较相似，只有 driver 和 url 存在差异。

### 希望在 mybatis 下对象不用添加双引号

【问题描述】

mybatis 下对象需要双引号。

【问题解决】

方法一：设置大小写不敏感，此方法会影响查询结果集，因为其对结果集匹配也不区分大小写，需要根据业务自行评估是否可行。

方法二：在 mysql 数据库中，对象名默认是小写，达梦对象名默认是大写，在用达梦 DTS 工具做数据迁移的时候，去掉 “保持对象名大小写” 的勾选，使对象名自动转换成大写，在 mybatis 查询的时候，即不需要加双引号强调小写。

### 使用 mybatis 框架批量插入并自动获取自增主键 ID 时获取不到主键 ID 值

【问题描述】

使用 mybatis 框架对接达梦数据库批量插入并自动获取自增主键 ID 时获取不到主键 ID 值。

【问题解决】

  1. 确保 mybatis 版本在 3.3.1 以上。
  2. batchInsert 方法上不能加 @param()，且该方法只能一个参数、 返回值为 Integer、long 或 void，不能是 List 。
  3. 如果自增主键数据库字段和实体类属性不一致，需要写成 useGeneratedKeys=“true” keyColumn=“主键字段名” keyProperty=“实体类名”。



### mybatis 框架和 DM8 框架整合时报错：dm.jdbc.driver.DMException

【问题描述】

mybatis 框架和 DM8 框架整合时报错：dm.jdbc.driver.DMException 。截图如下：

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406041023305PTW1GZPDZC9L8XLVY)

【问题分析】

开发人员在 mybatis 框架的 xml 文件的  select、update、detele 标签中编写 sql 语句时使用 ”--“ 注释 sql 语句，导致 jdbc 驱动执行 sql 会报序列号无效。

【问题解决】

删掉无效的注释语句即可。

### mybatis-plus 整合达梦时，自动生成的 sql 语句报错

【问题描述】

mybatis-plus 整合达梦时，应用系统项目的 sql 语句中包含数据库关键字，导致 mybatis-plus 自动生成的 sql 语句会报错，如下图所示：

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604103127WCJZ73BXOQVSOZ057H)

【问题分析】

mybatis-plus 生成的 sql 语句字段是小写的，同时还包含数据库关键字。框架会默认加上（\`）符号，该符号在达梦数据库中无法被识别，需要更换为 （"）。

【问题解决】

方法一：在实体类中的字段添加 TableField 注解：

```
@TableField（““type””)
private int type;
```

方法二：修改实体类的字段名称避免使用数据库关键字。

### Spring 框架中应用修改数据时报错：试图在只读事务中修改数据

【问题描述】

在同一套 spring 框架的应用从 oracle 迁移到达梦单机库，应用修改数据时报错：nested exception is dm.jdbc.driver.DMException: 试图在只读事务中修改数据。

【问题分析】

由于 Spring 框架层在对应的数据修改业务上设置了 setReadOnly(true)，而达梦与 oracle 在接口层对设置 setReadOnly(true) 的表现并不相同，DM8 JDBC 会直接将该会话设置为只读会话，而 Oracle 10g 以后的 JDBC 驱动会直接忽略该设置，继续允许执行修改操作的 sql，导致出现报错。

【问题解决】

使用 DM8 JDBC 驱动在连接串中设置 compatibleMode=oracle 可以兼容 oracle ，会忽略 setReadOnly(true) 设置，允许会话读写操作。

> **注意**
>
> compatibleMode=oracle 该参数与 dm.ini 中的 compatible_mode=2 并不相同，dm.ini 中的
> compatible_mode 为服务端的兼容参数，并不能解决该问题，需要注意区分。



### 配置 MYCAT 中间件连接达梦数据库报错

【问题描述】

Java 版本为 1.8.0_391，mycat 版本为 1.6，配置 MYCAT 中间件连接达梦数据库，mycat 启动报如下错误：

![图片 19.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604150314T95GNE3HIVBX8ROJXD)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps14.jpg)

【问题分析】

此问题是由于该 VM 不支持参数 AggressiveOpts 导致。

【问题解决】

将 startup_nowrap.sh 文件中的参数 -XX:+AggressiveOpts 去除，再运行可成功启动。

### MyCat 启动报错 1

【问题描述】

报错内容如下图：

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405291537515JJCH4ADSTWP4H59NI)

【问题解决】

该报错是没有修改 rule.xml 文件导致的，修改 rule.xml 中的内对应的数字即可，数字为 schema.xml 文件中配置的 dataNode 数量。

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529154041KLWV3D8QJIO49X2SWI)

### MyCat 启动报错 2

【问题描述】

报错内容如下图：

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529154146EGAD2SQIPZK3L601PQ)

【问题解决】

编码格式不一致，删除 xml 文件中文内容，或者转换为操作系统适合的编码。

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529154714ND441UPE7T7DWFPVRQ)

### MyCat 启动报错 3

【问题描述】

报错内容如下图：

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529164613BIH83X18JBB3QUBY2V)

【问题解决】

Linux 环境下，需要在 /mycat/bin/startup_nowrap.sh 中调大 MaxDirectMemorySize 大小。

windows 环境下，需要在 /mycat/bin/startup_nowrap.bat 中调大 MaxDirectMemorySize 大小，如下图所示，此参数从 1G 调大为 2G。

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052916462827MNSGH0UIJZZYV6HS)

### 代码运行报错 1

【问题描述】

报错内容如下图：

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529171144832EVGBMC2WLKZV2R7)

详细描述：

```
Loading class `com.mysql.jdbc.Driver'. This is deprecated. The new driver class is `com.mysql.cj.jdbc.Driver'. The driver is automatically registered via the SPI and manual loading of the driver class is generally unnecessary.
java.sql.SQLNonTransientConnectionException: CLIENT_PLUGIN_AUTH is required
```

【问题解决】

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529171201JMMAC8P7933FW2V24P)

com.mysql.jdbc.Driver 改为 com.mysql.cj.jdbc.Driver 。

### 代码运行报错 2

【问题描述】

报错内容如下图：

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529172338A8N174X0F1F93QTGG7)

【问题解决】

此现象是 mysql 版本和所连接的 mysql 版本不一致导致的。

如果 mysql-connector-java 用的 6.0 以上的，数据库驱动是：com.mysql.cj.jdbc.Driver 。

如果 mysql-connector-java 用的 6.0 以下的，数据库驱动是：com.mysql.jdbc.Driver 。

### 代码运行报错无效模式名

【问题描述】

报错截图如下：

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405291724207TWSU0UETMZYIOEI0U)

【问题分析】

代码里面有模式名时，运行 sql 时直接带模式名的 sql 传入数据库。

![图片 13.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405291725077FVO0QZQM9A82BAKIO)

![图片 14.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240529172514NN4GBLZHZPN1KK1HM7)

schema.xml 文件中 checkSQLschema 参数当值设置为 true 时，会把模式名删除后执行 sql 。例如，上面的语句会被自动删除模式名 PRODUCTION 变成：

```
insert into PRODUCT_CATEGORY (id,name)
values(1,'小说'),"+ "(2,'文学'),(3,'计算机'),(4,'英语'),(5,'管理'),(6,'少儿'),(7,'金融')
```

如果 sql 语句所带的模式名与配置文件中 schema 指定的模式名不一致，例如下面语句中的 db1 模式：

```
select * from db1.travelrecord; 
```

那么 MyCat 并不会删除 db1 这个字段。如果没有定义该模式的话，运行程序会报错。

当 checkSQLschema 设置为 false 时，不会删除模式名，会按照原 sql 执行。

【问题解决】

在 schema.xml 中配置 checkSQLschema 为 true 时，sql 语句不要带模式名；当 checkSQLschema 设置为 false 或不指定 name 时，sql 语句可以带模式名。

### IDEA 配置 DM 的数据库方言

【问题解决】

根据自己环境，找到合适的 DM 的方言包，可在数据库安装目录的 drivers 文件夹下获取，将方言包拷贝至项目指定路径下。

点击 File-settings-Languages&Frameworks-SQL Dialects ，如下图所示：

![图片 19.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530164530JYH0W553Y33S673ZH9)

点击【添加】按钮，如下图所示：

![图片 20.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405301645480LXX7VSHB8S124ITC6)

添加刚才复制的方言包，点击【ok】。

![图片 21.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530164600D5DLPI9JG9O2FU0VQG)

点击【apply】及【ok】完成方言包的添加。

![图片 22.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530164615283ZDXHM44TQ729FAI)

在 console 中执行以下语句，结果如下：

![图片 23.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240530164627YIVELBX7KPDIVKZ4IB)

### DM 数据库支持的驱动

【问题解决】

可以在 DM 数据库安装目录的 drivers 文件夹中查找相应的驱动版本进行使用。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240531150103K0ZN4TQM4WJ4OTZVN9)

DM 数据库提供了多种数据库访问接口，相应开发环境的配置，以及一些开发用例，详细内容可参考《DM 程序员手册》（手册位于数据库安装路径 doc 文件夹下） 如果没找到原生的驱动，比如 Go/Peal ，可以用 ODBC 驱动来替代。

### JDK 包和驱动不匹配

【问题描述】

错误提示：driver dm.jdbc.driver.DmDriver is not sequoia compliant ，如下图：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240531112941JOQAP5XUCL6U41IIUR)

【问题解决】

更换驱动包就可以了，要使用 DM 安装数据库后自带的驱动包。可在数据库安装目录的 drivers\jdbc 目录下获取驱动，分为 DmJdbcDriver16、DmJdbcDriver17，DmJdbcDriver18 分别对应 Jdk1.6、Jdk1.7、 Jdk1.8 环境。

### 通过 dm_svc.conf 连接主备集群

【问题解决】

dm_svc.conf 配置可参考下面的例子：

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240531113029Q5GBPN8RKR7NQ0AP0D)

JDBC 连接请查看《DM 程序员手册》（手册位于数据库安装路径 doc 文件夹下），如下图所示：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240531145252WOCAD2RW9T9LC3NWWR)

> **注意**
>
> 读写分离集群，MPP 集群都可以用这样的方法。



### tpc-c 标准测试工具

【问题解决】

数据库厂商不提供测试工具，建议使用第三方测试工具。举例最常用的 BenchMarkSql 工具：

DM JDBC 驱动包放到 BenchMarkSql 的 lib 文件夹下，在工具 run 文件夹下复制一个 props 文件命名为 props.dm 并且进行编辑添加 DM 的连接串，举例如下：

```
driver=dm.jdbc.driver.DmDriver
conn=jdbc:dm://localhost:5236
user=benchmarksql
password=123456789
```

### 报错 ”未经授权的用户“ 的原因有哪些？

【问题解决】

使用 druid 做连接池，在连接 DM 数据库时提示 ”未经授权的用户“ 有两种原因：

原因一：在 DM 管理工具中，此用户的资源限制中 ”会话持续期“ 进行了限制，把此限制取消即可。

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240603111600RATDJWN8WS3QFCTFRD)

### 连接尚未建立或者已经关闭

【问题描述】

使用的 DmJdbcDriver17 和 DmJdbcDriver18 程序启动后，提示连接尚未建立或者已经关闭。

【问题解决】

可按照如下步骤进行排查；

  1. 修改 dm.ini 里面的 max_sessions 参数。
  2. 检查是否用户设置了最大空闲时间参数，导致超时断开。
  3. 检查数据库服务器到应用服务器的网络波动，从应用服务器 ping 数据库服务器 -t，网络波动也会导致此报错。
  4. 检查连接池设置，是否设置了断开后自动连接。



若以上都没问题建议开启 JDBC 日志，出现报错后将生成的日志转发给达梦运维人员分析。

### 语句句柄个数超上限 (1024) 或系统内存不足

【问题描述】

报错内容： java.sql.SQLException: 语句句柄个数超上限 (1024) 或系统内存不足。

【问题分析】

新的需求建立时不申请新的 session ，而是不停地申请语句句柄，超过了每个 session 可用语句句柄数的上限。

【问题解决】

治本之策：修改应用程序，每个语句句柄用完后，将句柄关闭。

治标之法：修改 dm.ini 参数，提高 session 可用语句句柄的上限，比如：max_session_statement 由原先 100 改为 1000。

### 将查询结果赋值给变量

【问题描述】

`select @aa=xh from XXX ` 像这种将查询结果赋值给变量 @aa 的情况，在达梦数据库中应该怎样写？

【问题解决】

select 列名 into 变量名 from 表名 where 条件；

### 存进去的数据与读取出来的数据不同

【问题描述】

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240603143144EU7TDI1TDELIHZ60NZ)

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240603143211VPUH9GPV0U537BAZXZ)

【问题解决】

字段类型不同，请考虑使用 text 或者 clob 数据类型。此问题是由于应用代码导致的问题，请检查相关的插入与查询的代码。

### 如何回收空闲会话

【问题描述】

连接一直报错：reached the max session limit，调整 MAX_SESSIONS 参数无效，如何回收空闲会话。

【问题解决】

依据不同的编程接口，调用相应的关闭会话的函数。

服务器上关闭会话的函数是 `sp_close_session(sess_id) `。原则上禁止开发人员直接在服务器端关闭会话，请开发人员在使用完会话后自觉关闭会话。若有需要在服务器端关闭会话请数据库管理员 DBA 操作。

### 达梦数据库如何防止 SQL 注入？

【问题解决】

推荐应用人员在写 SQL 语句时，使用 SQL 语句参数化绑定。

### 开启 ssl 认证后连接数据库报错：网络通讯异常

【问题描述】

开启 ssl 认证后，应用无法连接数据库，提示网络通讯不正常报错，用 DM 管理工具可以正常连接，将应用的 jdk 更换为达梦自带 jdk 也可以正常连接。

【问题解决】

该问题是由于应用所使用的 jdk 中 jdk\jre\lib\security\java.security 配置文件差异导致，一般较高版本的 jdk 可能存在该问题。

存在该问题的情况下，可以查看 java.security 配置文件，删除 TLSv1 即可。

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240603162846RZDCNZMJ8HDTN2O69P)

### 定时任务中适配 quartz blob 字段不支持的处理方法

【问题描述】

```
org.quartz.JobPersistenceException: Couldn't store job: Driver's Blob representation is of an unsupported type: dm.jdbc.driver.DmdbBlob [See nested exception: java.sql.SQLException: Driver's Blob representation is of an unsupported type: dm.jdbc.driver.DmdbBlob]
```

【问题解决】

需要将 quartz.properties 配置文件的

```
org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.oracle.OracleDelegate
```

改为

```
org.quartz.jobStore.driverDelegateClass = org.quartz.impl.jdbcjobstore.StdJDBCDelegate
```

> **注意**
>
> org.quartz.jobStore.driverDelegateClass 中的 driver
> 代表了解不同数据库系统的特定“方言”。可能的选择包括：
> org.quartz.impl.jdbcjobstore.StdJDBCDelegate（用于完全符合 JDBC 的驱动程序）；
> org.quartz.impl.jdbcjobstore.MSSQLDelegate（对于 Microsoft SQL Server 和
> Sybase）； org.quartz.impl.jdbcjobstore.WebLogicDelegate（对于 WebLogic 驱动程序）；
> org.quartz.impl.jdbcjobstore.oracle.WebLogicOracleDelegate（对于 Weblogic 中使用的
> Oracle 驱动程序）；
> org.quartz.impl.jdbcjobstore.oracle.weblogic.WebLogicOracleDelegate（对于在
> Weblogic 中使用的 Oracle 驱动程序）； org.quartz.impl.jdbcjobstore.PostgreSQLDelegate
> ； org.quartz.impl.jdbcjobstore.oracle.OracleDelegate ；
> org.quartz.impl.jdbcjobstore.CloudscapeDelegate ；
> org.quartz.impl.jdbcjobstore.DB2v6Delegate ；
> org.quartz.impl.jdbcjobstore.DB2v7Delegate ；
> org.quartz.impl.jdbcjobstore.DB2v8Delegate ；
> org.quartz.impl.jdbcjobstore.HSQLDBDelegate ；
> org.quartz.impl.jdbcjobstore.PointbaseDelegate ；
> org.quartz.impl.jdbcjobstore.SybaseDelegate



### 应用 SQL 语句中 select 中别名为小写字母时，如何在查询结果中自动转换为大写字母

【问题描述】

应用 sql 语句中 select 中别名为小写字母时，查询结果别名为小写字母，并非和 oracle 一样自动转换为大写字母。如下图所示，DM 数据库中 dict_code 字段查询结果别名为小写，在 Oracle 中查询结果自动转换为大写。

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406041015086SDZX06CFBJGISVBPC)

【问题解决】

增加 jdbc 连接参数 `columnNameUpperCase=true` 可实现返回结果为大写字母的需求。columnNameUpperCase 参数含义：列名转换为大写字母，取值 1/0 或 true/false，默认 false。

### 执行 SQL 语句，驱动返回层报错：变量空间溢出

【问题描述】

当一条 SQL 涉及批量绑定参数个数较多时，JDBC 驱动层返回报错，“变量空间溢出”。且 SQL 日志里接收到对应的 sql ，没有解析而是直接 rollback 。报错截图如下：

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604103422MS1GKQVSEORC0AVARZ)

【问题解决】

可适当调整数据库 dm.ini 里 VM_STACK_SIZE 参数。默认为 256，单位为 KB 。例如当批量绑定参数个数达到 30000 时，可将 VM_STACK_SIZE 调整为 5120。

> **注意**
>
> VM_STACK_SIZE：系统执行时虚拟机堆栈大小，单位为
> KB，堆栈的空间是从操作系统中申请的，有效值范围（64~256*1024）。该参数为静态参数，修改后重启数据库生效。



### 应用连接 DM7 数据库报错："Error：JVMCFRE003 bad major version"

【问题描述】

在部署应用时，查看应用服务器的 Java 版本为 1.7，使用的 dm 驱动包为 DmJdbcDriver17.jar，但是应用无法连接到 DM 数据库，报错：“对数据源 DM7 执行的测试连接操作由于以下异常 java.sql.SQLException:java.lang.UnsupportedClassVersionError：JVMCFRE003 bad major version;class=dm/jdbc:/driver/DmdbConnectionPoolDataSource,offset=6 而失败”。

【问题解决】

此问题可能是由于服务器中存在多个版本的 Java ，应用服务器使用到了其他版本的 Java 导致。

使用 `ps -ef|grep java` 命令查看应用使用的 Java 路径及版本，更换 Java 版本为 1.7 后，即可连接成功。

### dm_svc.conf 文件是否可以使用普通用户定义并配置自定义路径

【问题解决】

可以，普通用户（非 root 用户）也可以创建 dm_svc.conf 文件和配置自定义路径。使用时添加 dmsvcconf 参数指定路径即可。

例如：当自定义路径在 /opt/dmsvc.conf 时，则用 `jdbc:dsc1://?dmsvcconf=/opt/dm_svc.conf` 连接数据库。

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps3.jpg)

### 执行环境堆栈空间不足

【问题描述】

管理系统业务报错：

```
Error updating database. Cause: dm.jdbc.driver.DMException: 执行环境堆栈空间不足。
```

【问题分析】

分析应用日志发现引起该错误信息的为一条 insert 语句，Insert 语句示例如下：

```
insert into pm_project_plan_revised (?,?......) values (?,?......), (?,?......), (?,?......), (?,?......)......;
```

其中字段值有 35 个，values 值的集合数超过 1300 个，每一个 values 里面的值都是通过传参获取。该 insert 语句不是一种规范的写法，大量的传参值和大量的 values 值集，必然导致系统资源消耗过高和执行时间变长。

【问题解决】

可通过以下两种方法解决：

方法一：调整 VM_STACK_SIZE 为 1024 或 2048，暂时避免该问题。

VM_STACK_SIZE 参数说明：系统执行时虚拟机堆栈大小，以 KB 为单位，堆栈的空间是从操作系统中申请的，有效值范围（64~256*1024），该参数是静态参数，默认值是 256，需要重启数据库生效。

方法二：从业务方面调整，在业务导入数据过程中，对导入的数据量进行限制，避免这种不合理的数据导入方式。

> **注意**
>
> 如果不对 values 值集进行限制，即使 VM_STACK_SIZE 设置为 2048 或更大值，values
> 值集达到一定值后，还是会报错，同时会导致系统资源消耗过高。



![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps7.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps11.jpg)

### 如何让返回的结果集中列的大小写和 SQL 中写的大小写保持一致？

【问题描述】

如何让返回的结果集的列大小写和 SQL 中写的大小写保持一致？比如列名本身为 name，但是 SQL 为 SELECT Name from table，要求结果集列名显示为 Name。

【问题解决】

可以在 url 连接串中配置参数 genKeyNameCase=0，即可保持用户输入列名大小写不变，如下：

```
jdbc:dm://ip:port_num?genKeyNameCase=0
```

### 应用程序执行 SQL 报错：不支持该数据类型

【问题描述】

应用程序执行 SQL 报错 “不支持该数据类型”，管理工具可以正常执行该 SQL。

![图片 17.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024060414594817A9KTJKTH21KZVOXR)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps12.jpg)

【问题分析】

此问题是应用程序代码直接使用原生的 jdbcTemplate 使用 hashmap 作为参数。

【问题解决】

直接使用原生的 jdbcTemplate 时不能使用 hashmap 作为参数,，应使用一个扩展类 NamedParameterJdbcTemplate，如下：

![图片 18.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240604150111PPIPQ2YP4AUMRX9T2D)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml19464\wps13.jpg)

### 字符串转换出错

【问题描述】

JDBC 中使用 setNull 方法给 varchar 类型绑定空值报错：字符串转换出错。语句如下所示：

```
String sql = "select * from sysobjects where name=?";
PreparedStatement stmt = conn.prepareStatement(sql);
stmt.setNull(1, 0);
stmt.execute();
conn.commit();
```

【问题解决】

出现该报错是由于达梦默认 null 和空串不等价，解决方法：

修改 dm.ini，令 COMPATIBLE_MODE = 2，重启数据库后生效。

### MySQL 迁移到 DM 后 getObject 接口返回值不一致

【问题描述】

使用 dts 将如下表迁移到达梦后 tinyint(1) 映射为 tinyint，通过 jdbc 中 getObject 接口获取该列数据时，mysql 返回 boolean 类型值，达梦返回 0 、1 数字。

```
/*mysql*/create table SimplyTest(id int,name varchar(20),create_time timestamp,flag tinyint(1),other longtext)
/*dm*/create table SimplyTest(id int,name varchar(20),create_time timestamp,flag tinyint,other longtext)
```

【问题解决】

将达梦端对应列定义改为 bit 类型 ，则使用 getObject 接口时返回值为 boolean 类型值。

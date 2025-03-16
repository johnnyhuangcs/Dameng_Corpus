

# .NET 语言

## 一、前言

本章节主要介绍 .NET 应用开发常见问题，为用户提供 .NET 应用开发常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 二、目录

  * [DmException: 试图在只读事务中修改数据](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#DmException:%20%E8%AF%95%E5%9B%BE%E5%9C%A8%E5%8F%AA%E8%AF%BB%E4%BA%8B%E5%8A%A1%E4%B8%AD%E4%BF%AE%E6%94%B9%E6%95%B0%E6%8D%AE)
  * [.NET 程序中使用 DmBulkCopy 对象执行时报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#.NET%20%E7%A8%8B%E5%BA%8F%E4%B8%AD%E4%BD%BF%E7%94%A8%20DmBulkCopy%20%E5%AF%B9%E8%B1%A1%E6%89%A7%E8%A1%8C%E6%97%B6%E6%8A%A5%E9%94%99)
  * [.NET 程序服务名连接达梦集群，备机异常时，连接异常变慢](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#.NET%20%E7%A8%8B%E5%BA%8F%E6%9C%8D%E5%8A%A1%E5%90%8D%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E9%9B%86%E7%BE%A4%EF%BC%8C%E5%A4%87%E6%9C%BA%E5%BC%82%E5%B8%B8%E6%97%B6%EF%BC%8C%E8%BF%9E%E6%8E%A5%E5%BC%82%E5%B8%B8%E5%8F%98%E6%85%A2)
  * [在指定的 DSN 中，驱动程序和应用程序之间的体系结构不匹配](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#%E5%9C%A8%E6%8C%87%E5%AE%9A%E7%9A%84%20DSN%20%E4%B8%AD%EF%BC%8C%E9%A9%B1%E5%8A%A8%E7%A8%8B%E5%BA%8F%E5%92%8C%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F%E4%B9%8B%E9%97%B4%E7%9A%84%E4%BD%93%E7%B3%BB%E7%BB%93%E6%9E%84%E4%B8%8D%E5%8C%B9%E9%85%8D)
  * [报错 “加密模块加载失败”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#%E6%8A%A5%E9%94%99%20%E2%80%9C%E5%8A%A0%E5%AF%86%E6%A8%A1%E5%9D%97%E5%8A%A0%E8%BD%BD%E5%A4%B1%E8%B4%A5%E2%80%9D)
  * [调用 get_ProviderFactory 方法后，返回 null](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#%E8%B0%83%E7%94%A8%20get_ProviderFactory%20%E6%96%B9%E6%B3%95%E5%90%8E%EF%BC%8C%E8%BF%94%E5%9B%9E%20null)
  * [This SQL map does not contain a MappedStatement named xxx](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#This%20SQL%20map%20does%20not%20contain%20a%20MappedStatement%20named%20xxx)
  * [Bad value for ai_flags 或者是 [(UNKNOW, UNKNOW)]Resource temporarily unavailable 或者是网络通讯异常](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#Bad%20value%20for%20ai_flags%20%E6%88%96%E8%80%85%E6%98%AF%20\[\(UNKNOW,%20UNKNOW\)\]Resource%20temporarily%20unavailable%20%E6%88%96%E8%80%85%E6%98%AF%E7%BD%91%E7%BB%9C%E9%80%9A%E8%AE%AF%E5%BC%82%E5%B8%B8)
  * [The provider is not in ​'providers.config​' or is not enabled.](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#The%20provider%20is%20not%20in%20'providers.config'%20or%20is%20not%20enabled.)
  * [command.Parameters.Add 方法无法实现参数跟名称绑定](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#command.Parameters.Add%20%E6%96%B9%E6%B3%95%E6%97%A0%E6%B3%95%E5%AE%9E%E7%8E%B0%E5%8F%82%E6%95%B0%E8%B7%9F%E5%90%8D%E7%A7%B0%E7%BB%91%E5%AE%9A)
  * [.net4/5 连接数据库出现 [(UNKNOW, UNKNOW)] 无效的客户端版本错误](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#.net4/5%20%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E5%87%BA%E7%8E%B0%20\[\(UNKNOW,%20UNKNOW\)\]%20%E6%97%A0%E6%95%88%E7%9A%84%E5%AE%A2%E6%88%B7%E7%AB%AF%E7%89%88%E6%9C%AC%E9%94%99%E8%AF%AF)
  * [.NET 程序中使用达梦数据库 @ 符号不能传参](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#.NET%20%E7%A8%8B%E5%BA%8F%E4%B8%AD%E4%BD%BF%E7%94%A8%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%20@%20%E7%AC%A6%E5%8F%B7%E4%B8%8D%E8%83%BD%E4%BC%A0%E5%8F%82)
  * [使用 DmProvider 驱动连接达梦数据库报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#%E4%BD%BF%E7%94%A8%20DmProvider%20%E9%A9%B1%E5%8A%A8%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99)
  * [如何 DmProvider 驱动的 nupkg 包导入项目中](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#%E5%A6%82%E4%BD%95%20DmProvider%20%E9%A9%B1%E5%8A%A8%E7%9A%84%20nupkg%20%E5%8C%85%E5%AF%BC%E5%85%A5%E9%A1%B9%E7%9B%AE%E4%B8%AD)
  * [.NET 使用 dapper 框架查询报错：”Error parsing column 0”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-net-new.html#.NET%20%E4%BD%BF%E7%94%A8%20dapper%20%E6%A1%86%E6%9E%B6%E6%9F%A5%E8%AF%A2%E6%8A%A5%E9%94%99%EF%BC%9A%E2%80%9DError%20parsing%20column%200%E2%80%9D)



## 三、正文

### DmException: 试图在只读事务中修改数据

【问题描述】

执行 sql 语句时报试图在只读事务中修改数据。

【问题解决】

达梦数据库的事务隔离级分为四级，默认是“读提交隔离级”。可以检查一下是否设置了隔离级别为“只读事务”。更多关于达梦数据库的事务隔离级，请参考数据库安装目录的 DOC 目录下的《系统管理员手册》第 19 章节——管理事务。

### .NET 程序中使用 DmBulkCopy 对象执行时报错

【问题描述】

.NET 程序中使用 DmBulkCopy 对象执行时报错：

```
Unhandled exception. Dm.DmException (0x80004005): The fastloading dll not loading!
    at Dm.DmFldr.Initilize(DmConnProperty props, String desttable, DmBulkCopyOptions op, Int32 rows, DataTable table)
    at Dm.DmBulkCopy.WriteToServer(DataTable table)
```

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps1.png)

【问题分析】

DmBulkCopy 对象用于快速批量装载数据，实现 IDisposable 接口，该功能依赖 DM 安装目录 bin 下的 dmfldr_dll.dll 等动态链接库。

【问题解决】

在 Path 环境变量里面加上达梦的 bin 目录：

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521151526MSXFVY9OD7O6HT6XS0)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps2.png)

### .NET 程序服务名连接达梦集群，备机异常时，连接异常变慢

【问题描述】

.NET 程序服务名连接达梦集群，备机异常时，连接异常变慢。

【问题解决】

在 dm_svc.conf 中配置连接超时参数 connnectTimeout，可配置为 connnectTimeout=(3)，如未配置该参数，可直接新增。

### 在指定的 DSN 中，驱动程序和应用程序之间的体系结构不匹配

【问题描述】

DSN 里面创建并使用数据源时，出现报错：在指定的 DSN 中，驱动程序和应用程序之间的体系结构不匹配。

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521151633KHMYIAJDNQKM97A5OZ)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps3.jpg)

【问题解决】

达梦和应用的位数不匹配，检查现场的应用是 32 位还是 64 位，安装的 DM 是 32 位还是 64 位，如果无法确认版本，可以分别对 32 位和 64 位的 odbc 数据源管理程序添加对应的 driver 进行测试。

### 报错 “加密模块加载失败”

【问题描述】

libeay32.dll 这个加密动态库找不到。

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521151643WOHOSK78NIN83P19WH)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps4.jpg)

【问题解决】

在系统环境变量中添加 DM 数据库的 bin 目录，或者将动态库以及依赖文件拷贝到程序目录下面和 system32（可以拷贝 bin 目录下所有动态库文件）。

### 调用 get_ProviderFactory 方法后，返回 null

【问题描述】

System.Data.Entity.Core.ProviderIncompatibleException:

对类型 Dm.DmConnection 的存储区提供程序实例调用 get_ProviderFactory 方法后，返回 null，存储区提供程序可能没有正确运行。

【问题解决】

DmProvider.dll 用的不是最新版本，替换为新版本即可，下载最新的 DM 数据库，在 drivers\dotNet\DmProvider 目录下获取。

### This SQL map does not contain a MappedStatement named xxx

【问题描述】

IBatisNet.DataMapper.Exceptions.DataMapperException: This SQL map does not contain a MappedStatement namd abc.List

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521151748DA4OWDRAYU4VFC7EP5)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps5.jpg)

【问题解决】

检查配置文件中的命名空间是否有误，sql 的配置文件有个命名空间的参数 useStatementNamespaces，此参数默认是 true，就是说使用了 Satement 命名空间，所以应用的时候必须加上命名空间，运行方法时需要带上命名空间名称 abc，例如：

`Mapper().QueryForList("abc.List", null)`，而不是 `Mapper().QueryForList("List", null)`。

### Bad value for ai_flags 或者是 [(UNKNOW, UNKNOW)]Resource temporarily unavailable 或者是网络通讯异常

【问题描述】

出现 Bad value for ai_flags 等报错。

【问题解决】

一般都是并发下连接超时，最新驱动修改连接参数，添加 connPooling=true; connectionTimeout=3600000; connPoolSize=1000。

### The provider is not in 'providers.config' or is not enabled.

【问题描述】

加载驱动出现 The provider is not in 'providers.config' or is not enabled。

【问题解决】

在 Providers.config 文件中将对应的 provider 的 enabled 选项改成 true。

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521151905E2WH0WCWQG8EWC2CZV)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps6.jpg)

### command.Parameters.Add 方法无法实现参数跟名称绑定

【问题描述】

使用 .net 框架调用 command.Parameters.Add 方法，把参数放到一个 LIST 里面，每次执行 ExecuteNonQuery 方法都会从 LIST 按顺序取值，而不能根据参数名称绑定。

【问题解决】

此问题需要升级驱动版本解决，请联系对应项目服务人员获取相应版本。

### .net4/5 连接数据库出现 [(UNKNOW, UNKNOW)] 无效的客户端版本错误

【问题描述】

将旧版本 DmProvider.dll 文件复制到新项目目录 bin 下然后执行报错。

【问题解决】

此问题出现原因为老版本客户端连接新版本服务器导致版本不匹配，修改 dm.ini 参数 IFUN_DATETIME_MODE=0，后重启数据库即可。

### .NET 程序中使用达梦数据库 @ 符号不能传参

【问题描述】

.NET 程序中使用达梦数据库 @ 符号报参数错误。

【问题解决】

修改 dm.ini 配置文件中 MS_PARSE_PERMIT=2，重启服务生效即可以使用 @ 符号传参。

参数解释：

|**参数名**|**默认值**|**属性**|**说明**|
|---|---|---|---|
|MS_PARSE_PERMIT|0|静态|是否支持 MS SQLSERVER 的语法。0：不支持；1：支持；2：在 MS_PARSE_PERMIT =1 的基础上，兼容 MS SQLSERVER 的查询项中支持”标识符=列名”或“@ 变量名=列名“用法。备注：当 COMPATIBLE_MODE=3 时，MS_PARSE_PERMIT 的实际值为 1|


### 使用 DmProvider 驱动连接达梦数据库报错

【问题描述】

.NET 使用 DmProvider 驱动连接达梦数据库报错：“System.TypeInitializationException:“The type initializer for 'Dm.DmConnProperty' threw an exception.”

报错信息如下图所示：

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405211522153R1K0CNQ5RKJYA05K6)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps7.jpg)

【问题分析】

DmProvider 可以在 .NET 框架和 NETCore 框架下使用，NETCore 框架下需要用户安装 System.Text.Encoding.CodePages 包或者直接以 NUGET 包的形式安装 DmProvider 可以自动依赖的 System.Text.Encoding.CodePages 包，由于这里采用了直接引用的方式所以报错。

【问题解决】

使用 NuGet 包管理器安装 System.Text.Encoding.CodePages 即可。

### 如何 DmProvider 驱动的 nupkg 包导入项目中

【问题解决】

点击项目 -> 管理 NuGet 程序包 。

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521152340WMX8ZL9ZK0GAB32AF7)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps8.jpg)

点击设置 -> 输入路径名 -> 输入包名（包含后缀）-> 更新 -> 确定。

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521152354449QSOHWKW4Q4E6K3E)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps9.jpg)

选择包源，就可以看到 DmProvider，点击右侧安装即可。

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024052115240449YO8MZM99FUEEF94Z)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml12552\wps10.jpg)

### .NET 使用 dapper 框架查询报错：”Error parsing column 0”

【问题描述】

.NET 使用 dapper 框架查询报错：“Error parsing column 0 (F_COMPANYID=77146e11-17bb-11ed-9d74-8c8caae51613 - Object)”

【问题分析】

此报错一般是数据类型转换问题，程序中报错的属性定义为 String 类型，数据库字段为 varchar(36) 且为 guid 类型值。

url 属性 archar36ToGuid 表示是否将数据库 varchar(36) 列类型返回 Guid 类型。TRUE 表示返回 Guid 类型；FALSE 表示返回 string 类型；缺省为 TRUE。

【问题解决】

方法一：将应用程序中报错的属性设置为 Guid 类型。

方法二：在 URL 中添加 archar36ToGuid=false，程序中报错的属性保持 String 类型。

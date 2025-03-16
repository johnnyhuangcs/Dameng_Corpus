

# .Net Data Provider

## 一、前言

.NET Data Provider 是 .NET Framework 编程环境下的数据库用户访问数据库的编程接口，用于连接到数据库、执行命令和检索结果。在数据源和代码之间创建了一个最小层，以便在不以功能为代价的前提下提高性能。

## 二、环境准备

### 2.1 开发环境准备

|**名称**|**版本**|
|--|--|
|DM 数据库|DM 8.0 及以上版本|
|Visual Studio|2019|


### 2.2 达梦数据库安装

请参考[数据库环境准备](https://eco.dameng.com/document/dm/zh-cn/app-dev/#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.3 创建项目

  1. 打开 visual studio 软件，创建一个新项目。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227161256Z7U9V1OA0I8YFII1EG)

  2. 配置项目名。



![图片 17.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240402102910A71Y9MKMLWWGULLFCC)

![图片 18.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240402103043LBS7JLFAOPABECS4IF)

  3. 添加一个项，右键【解决方案】->【添加】->【新建项】。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213163411Y2MG9THX3DDXEF5614)

  4. 添加一个新的 visual C# 类后如图所示。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213163438CSCENLFUFMZ6TAW15N)

### 2.4 添加驱动

  1. 首先在项目中引用 DmProvider.dll，DmProvider.dll 在达梦数据库安装目录下的 \`..\drivers\dotNet\DmProvider \` 文件夹下可以找到。添加方法：右键引用->【添加引用】。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213163518TKM7BNXU08BIDRQ5Q7)

  2. 从浏览中找到 DmProvider.dll，并添加。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213163531MXUO4937VDUKG16JZ3)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213163543V06E0436RRMO62HWXQ)

  3. 添加驱动后使用，添加 using Dm。
  4. 右键【引用】->【程序集】-> 添加 System、System.Data。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213163608YFJSAF78AH7Z8T0HFW)

在代码中引用，如图所示：

```
using System;
using System.Data;
```

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202412231242587FDT82RJW7I3C59B1O)

## 三、数据库连接

  1. DM provider 接口登录登出示例程序 provider_conn.cs 如下：



```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Data;
using Dm;

namespace provider_conn
{
    class conn
    {
        static void Main(string[] args)
        {
            try
            {
                // 连接测试
                DmConnection conn = new DmConnection();
                conn.ConnectionString = "Server=192.168.104.21:51236; UserId=SYSDBA; PWD=*****;";
                conn.Open();
                Console.WriteLine("dmprovider: connect success!");
                conn.Close();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    }
}
```

  2. 执行结果如下：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227162310YVKD322HXRYUHTRV9J)

> **注意**
>
> 需要选择正确的启动对象才能启动成功，启动对象名应为想要启动的文件的文件名，如下图启动的文件则为 provider_dml.cs。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213163826UIXO04JRG16KXL3YXO)

## 四、开发示例

### 4.1 基础操作示例

  1. DM provider 接口增、删、改、查四个基本操作，示例程序 provider_dml.cs 如下：



```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Dm;

namespace provider_dml
{
    class dml
    {
        static void Main(string[] args)
        {
            try
            {
                DmConnection conn = new DmConnection();
                conn.ConnectionString = "Server=192.168.104.21:51236; UserId=SYSDBA; PWD=*****;";
                conn.Open();

                //清理测试环境
                DmCommand cmd = new DmCommand();
                cmd.Connection = conn;
                cmd.CommandText = "delete from PRODUCTION.PRODUCT_CATEGORY";
                cmd.ExecuteNonQuery();

                //插入数据
                cmd.CommandText = "insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values('语文'), ('数学'), ('英语'), ('体育')";
                cmd.ExecuteNonQuery();
                Console.WriteLine("dmprovider: insert success!");

                //删除数据
                cmd.CommandText = "delete from PRODUCTION.PRODUCT_CATEGORY where name='数学'";
                cmd.ExecuteNonQuery();
                Console.WriteLine("dmprovider: delete success!");

                //更新数据
                cmd.CommandText = "update PRODUCTION.PRODUCT_CATEGORY set name = '英语-新课标' where name='英语'";
                cmd.ExecuteNonQuery();
                Console.WriteLine("dmprovider: update success!");

                //查询数据
                cmd.CommandText = "select name from PRODUCTION.PRODUCT_CATEGORY";
                DmDataReader reader =(DmDataReader)cmd.ExecuteReader();
                while (reader.Read())
                {
                    string name = reader.GetString(0);
                    Console.WriteLine("name：" + name);
                }
                reader.Close();
                Console.WriteLine("dmprovider: select success!");

                conn.Close();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    }
}
```

  2. 执行结果如下：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227162526ULEPJ60S44BE7F5TD9)

### 4.2 绑定变量示例

  1. DM provider 接口绑定变量示例程序 provider_bind.cs 如下：



```
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Dm;

namespace provider_bind
{
    class bind
    {
        static void Main(string[] args)
        {
            try
            {
                DmConnection conn = new DmConnection();
                conn.ConnectionString = "Server=192.168.104.21:51236; UserId=SYSDBA; PWD=*****;";
                conn.Open();

                //清理测试环境
                DmCommand cmd = new DmCommand();
                cmd.Connection = conn;
                cmd.CommandText = "delete from PRODUCTION.PRODUCT_CATEGORY";
                cmd.ExecuteNonQuery();

                //插入数据
                cmd.CommandText = "insert into PRODUCTION.PRODUCT_CATEGORY(name) values(:s_name)";
                var para1 = new DmParameter(":s_name", DmDbType.VarChar, 50);
                cmd.Parameters.Add(para1);
                para1.Value = "物理";
                cmd.ExecuteNonQuery();
                Console.WriteLine("dmprovider: insert with bind success!");

                //查询数据
                cmd.Parameters.Clear();
                cmd.CommandText = "select name from PRODUCTION.PRODUCT_CATEGORY";
                DmDataReader reader = (DmDataReader)cmd.ExecuteReader();
                while (reader.Read())
                {
                    string name = reader.GetString(0);
                    Console.WriteLine("name：" + name);
                }
                reader.Close();

                conn.Close();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    }
}
```

  2. 执行结果如下：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227164305S9VLLQ23W7OLUFWJKV)

### 4.3 大字段操作示例

  1. DM provider 接口大字段操作包括大字段的插入，查询等。示例程序 provider_lob.cs 如下：



```
using System;
using System.Collections.Generic;
using System.IO;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using Dm;

namespace provider_lob
{
    class lob
    {
        static void Main(string[] args)
        {
            try
            {
                DmConnection conn = new DmConnection();
                conn.ConnectionString = "Server=192.168.104.21:51236; UserId=SYSDBA; PWD=*****;";
                conn.Open();

                //清理测试环境
                DmCommand cmd = new DmCommand();
                cmd.Connection = conn;
                cmd.CommandText = "drop table if exists PRODUCTION.BIG_DATA";
                cmd.ExecuteNonQuery();
                cmd.CommandText = "create table PRODUCTION.BIG_DATA(c1 int, c2 blob)";
                cmd.ExecuteNonQuery();

                // 读取文件数据，写入 lob 列
                FileInfo fi = new FileInfo(@"..\..\file\DM8_SQL.pdf");
                FileStream fs = fi.OpenRead();
                int nBytes = (int)fs.Length;
                byte[] dataArray = new byte[nBytes];
                fs.Read(dataArray, 0, nBytes);
                fs.Close();

                cmd.CommandText = "insert into PRODUCTION.BIG_DATA values(1, :blob)";
                DmParameter param1 = new DmParameter(":blob", DmDbType.Binary);
                cmd.Parameters.Add(param1);
                param1.Value = dataArray;
                cmd.ExecuteNonQuery();
                Console.WriteLine("dmprovider: write to col of lob success!");

                //读取 lob 列数据，写入到文件中
                DmBlob buffer;
                cmd.Parameters.Clear();
                cmd.CommandText = "select c2 from PRODUCTION.BIG_DATA";
                DmDataReader reader = (DmDataReader)cmd.ExecuteReader();
                reader.Read();
                buffer = reader.GetBlob(0);
                int length = buffer.Length();

                FileStream fs1 = new FileStream(@"..\..\file\DM8_SQL1.pdf", FileMode.Create);
                fs1.Write(buffer.GetBytes(0, length), 0, length);
                fs1.Flush();
                fs1.Close();
                Console.WriteLine("dmprovider: get data from col of lob success!");

                reader.Close();


                conn.Close();
            }
            catch (Exception ex)
            {
                Console.WriteLine(ex.Message);
            }
        }
    }
}
```

  2. 执行结果如下：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227164727YLNZM5HVIF8YANQJLG)

## 五、常见问题

1. DmException：试图在只读事务中修改数据

达梦数据库的事务隔离级分为四级，默认是“读提交隔离级”。可以检查一下是否设置隔离级别为“只读事务”。

2. NET 程序中使用 DmBulkCopy 对象执行时报错

【问题描述】：.NET 程序中使用 DmBulkCopy 对象执行时报错：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213164356S18E3J30BUUIAJ9CA2)

```
##具体报错信息如下：
Unhandled exception. Dm.DmException (0x80004005): The fastloading dll not loading!
at Dm.DmFldr.Initilize(DmConnProperty props, String desttable, DmBulkCopyOptions op, Int32 rows, DataTable table)
at Dm.DmBulkCopy.WriteToServer(DataTable table)
```

【问题分析】：DmBulkCopy 对象用于快速批量装载数据。实现 IDisposable 接口。该功能依赖 DM 安装目录 \bin 下的 dmfldr_dll.dll 等动态链接库，需要拷贝到应用程序的执行目录。

驱动文档里的说明：

DmProvider 文件夹中是完整的 DmProvider 驱动文件。使用 DmProvider 的 DmBulkCopy 对象，需要引用 dmfldr_dll.dll 以及此 dll 依赖的其他库。

添加 dmfldr_dll.dll 引用时会报错，这种方法无效。

【问题解决】：在 Path 环境变量里面加上达梦的 bin 目录：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213164634YEASC9XYB5HDIWW3ID)

3. .net 程序服务名连接达梦集群，备机异常时，连接异常变慢

在 dm_svc.conf 中配置连接超时参数 connnectTimeout，可配置为 connnectTimeout=(3)。

4. 在指定的 DSN 中，驱动程序和应用程序之间的体系结构不匹配

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213164803CJ9EQT4HFR2BTXERRM)

达梦和应用的位数不匹配，检查应用是 32 位还是 64 位，安装的 dm 是 32 位还是 64 位。

5. 报错“加密模块加载失败”

【问题分析】：libeay32.dll 这个加密动态库找不到。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213164959FEUDY8W65MDT71TPFC)

【问题解决】：在系统环境变量中配置达梦的 bin 目录，或者把 dm 的安装目录环境变量设置到最前面或者把这个动态库以及依赖文件拷贝到程序目录下面或者 system32（可以拷贝 bin 目录下所有动态库文件）

6. 调用“get_ProviderFactory”方法后，返回 null

【问题描述】：System.Data.Entity.Core.ProviderIncompatibleException：

对类型 “Dm.DmConnection” 的存储区提供程序实例调用“get_ProviderFactory”方法后，返回 null。存储区提供程序可能没有正确运行。

【问题解决】：DmProvider.dll 用的不是最新版本。修改为新版本即可。

7. This SQL map does not contain a MappedStatement named xxx…

【问题解决】：检查配置文件中的命名空间是否有误，例如：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213170114WSWGCO5PJZIHNV8JS0)

8. The provider is not in 'providers.config' or is not enabled.

【问题解决】：在 Providers.config 文件中将对应的 provider 的 enabled 选项改成 true

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221213170150SY1LT233IVLI3PQHUC)

## 六、参考

  1. 示例代码下载：[.Net_Code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202212231109106FUA4BNIVS6XY8CWPU)。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。



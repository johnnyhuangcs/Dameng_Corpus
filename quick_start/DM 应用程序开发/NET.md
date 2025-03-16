

# .NET

## 一、获取 DmProvider 驱动

可访问达梦云适配中心[下载试用](https://eco.dameng.com/download/?_blank)，下载 DM8 数据库试用版，在数据库安装路径 drivers 目录下，找到对应驱动程序，请参考 [DM 数据库安装](https://eco.dameng.com/document-preview/dm/zh-cn/start/install-dm-windows-prepare)。

## 二、通过 DmProvider 连接数据库

使用 DmProvider 时需要注册 .NET 驱动，例如通过 DbProviderFactories 类调用 DmProvider 创建连接，NHibernate 及 EFDmProvider 的使用，都需要注册 .net 驱动。

### 2.1 注册 DmProvider

使用 NETFX 注册 DmProvider 驱动

我的电脑路径在 C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.8.1 Tools

执行以下命令

```
gacutil.exe  /if  C:\Users\Administrator\source\repos\EFDMDem\EFDMDem\bin\Debug\DmProvider.dll
```

记得用管理员启动

如果出现以下则是没有权限

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240206101059TQ2TED6I8SP1SV0XAD)

使用管理员使用此命令，出现下图说明添加成功

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240206101107CSPBOY1I7MI1CSUPHU)

### 2.2 读取达梦 dll 公钥

此步骤可以不做，达梦此驱动公钥都是 7a2d44aa446c6d01，没有跟随版本变化

我的 NETFX 路径位于 C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.8.1 Tools

执行以下命令读取公钥

```
SN -T C:\Users\Administrator\source\repos\EFDMDem\EFDMDem\bin\Debug\DmProvider.dll
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240206101543JN6P6QEH4B1VJ3M4GJ)

### 2.3 修改 machine.config

C:\Windows\Microsoft.NET\Framework 下找到使用的版本里面修改 machine.config 配置

我的电脑路径为 C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config

在配置文件 machine.config 中添加以下内容：

```
<DbProviderFactories>
<add description="DM .Net Framework Data Provider" invariant="Dm" name="DM Data Provider" type="Dm.DmClientFactory,  DmProvider,  Version=1.1.0.0,  Culture=neutral,PublicKeyToken=7a2d44aa446c6d01"/>   
</DbProviderFactories>
```

> **注意**
>
>
> 记得在里面搜索DbProviderFactorie，里面默认使用了DbProviderFactorie标签，配置文件只允许有一个DbProviderFactorie标签，找到标签位置，进行修改。



例如通过 DbProviderFactories 类调用 DmProvider 创建连接使用 .NET 驱动的情况，`using System.Data.Common`，如下所示：

```
using System.Data.Common;
using System;

namespace EFDMDem
{
    class ProcDemo
    {
        public static void Main()
        {
            DbProviderFactory factory = DbProviderFactories.GetFactory("Dm");
            DbConnection sconn = factory.CreateConnection();
            sconn.ConnectionString = "Server=localhost; UserId=SYSDBA; PWD=*****";
            sconn.Open();
            DbCommand scmd = factory.CreateCommand();
            scmd.Connection = sconn;
            try
            {
                scmd.CommandText = "CREATE TABLE TEST(ID INT)";
                scmd.ExecuteNonQuery();
            }
            catch (Exception)
            { }
        }
    }
}
```

登陆达梦管理工具进行查看，发现表已经添加成功

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240206100712SR4FRN7L2YJIVGO9GS)

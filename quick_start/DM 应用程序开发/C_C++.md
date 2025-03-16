

# C/C++

本章主要介绍在 C/C++ 开发的时候，如何快速连接 DM 数据库。

## 一、获取 ODBC 驱动

可访问达梦云适配中心[下载试用](/download/?_blank)，下载 DM8 数据库试用版，在数据库安装路径 drivers 目录下，找到对应驱动程序，请参考 [DM 数据库安装](../start/install-dm-windows-prepare)。

## 二、通过 ODBC 连接数据库

### 2.1 Windows 环境创建 ODBC 数据源

在客户使用 ODBC 方法访问 DM 数据库服务器之前，必须先对自己的应用程序所用的 ODBC 数据源进行配置。本小节将介绍如何安装和配置 ODBC 数据源。

在 Windows 上使用 ODBC 数据源管理程序，配置 ODBC 数据源的步骤如下：

  1. 在控制面板上访问 ODBC 构件，显示 ODBC 数据源管理器对话框，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240202140608ZRLKVRO428PPEGGWFH)

ODBC 数据源管理器对话框包含的标签如下所示：

```
用户 DSN：添加、删除或配置本机上的数据源，它们只可由当前用户使用。
系统 DSN：添加、删除或配置本机上的数据源，它们可由任何用户使用。
文件 DSN：添加、删除或配置在分离文件中的数据源。这些文件可以被安装了同样数据库驱动器的用户共享。
驱动程序：列出了安装在客户机上的数据库驱动器。
跟踪：用于测试你的数据库应用程序。它跟踪客户机和数据库服务器之间的 ODBC API 的调用。
连接池：允许不同的应用程序自动复用多个连接。这有助于限制和数据库服务器的通信过载。
关于：显示主要 ODBC 组件的版本。
```

  2. 设置和配置一个系统 DSN，请单击系统 DSN 标签，单击添加按钮增加一个新的 DSN，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240202140659TVRXGHTVAX3UHXT29E)

  3. 选择 DM ODBC 3.0 驱动程序即 \`DM ODBC DRIVER\`，单击【完成】按钮，显示 DM ODBC 3.0 数据源配置对话框，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240202140729MHOB7M6DJN32KT8CQY)

  4. 输入数据源的名称、描述，并选择你想要连接的数据库服务器的名字使用的端口号、验证登录用户 ID 真伪的方式。如果使用 DMServer 验证方式则需要输入登录数据源的 ID 以及密码等信息，选择系统提示信息的语种，以及选择是否使用 DMServer 的增强选项。
  5. 单击测试按钮测试配置的数据源是否正确，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240202140750X469IZNZSF1IDDU9T4)

  6. 单击确定按钮保存新的系统数据源，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240202140858LANUEIKKFIEIE5YM6I)

  7. 单击确定按钮关闭 ODBC 数据源管理器对话框。



### 2.2 Linux 环境创建 ODBC 数据源

DMODBC 在 Linux 操作系统依赖于 UnixODBC 库。

此链接为 2.3.12 版本安装包，[https://www.unixodbc.org/unixODBC-2.3.12.tar.gz](https://www.unixodbc.org/unixODBC-2.3.12.tar.gz) 。

如果需要下载其他版本 ,点击链接进入官网[ https://www.unixodbc.org/](https://www.unixodbc.org/) ，选择合适版本进行下载。

  1. 下载好后，进行解压，使用命令进行配置



```
./configure --enable -gui=no
```

  2. 配置好后进行编译



```
make && make install
```

  3. 编译后查找配置文件位置



```
odbcinst -j
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240202171242YAYY6Q34U0XRV81793)

> **注意**
>
> 如果出现so文件缺失的情况，大部分可以通过 yum install unixODBC-devel 方式进行解决，如果还有部分缺失，需要自行下载



  4. 编辑 \`/etc/odbcinst.ini\` ，如下所示：



```
[DM8 ODBC DRIVER]
Description = ODBC DRIVER FOR DM8
Driver = /home/dmdba/dmdbms/bin/libdodbc.so
```

  5. 编辑 \`/etc/odbc.ini\` ，如下所示：



```
[dm8]
Description = DM ODBC DSN
Driver = DM8 ODBC DRIVER
SERVER = localhost
UID = SYSDBA
PWD = *****
TCP_PORT = 5236
```

> **注意**
>
> odbc.ini 中的 Driver 内容一定要与 odbcinst.ini 中的 DM 驱动定义的节点名称相同。 odbc.ini 中的 SERVER
> 可以输入数据库服务器的 IP 。



  6. 测试连接，出现以下内容说明连接成功



```
isql  dm8
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402021732527L6X6W9RT5NJ9GJIV6)

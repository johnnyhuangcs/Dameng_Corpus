

# Python

本章主要介绍在 Python 开发的时候，如何使用 Python 快速连接达梦数据库。

## 一、dmPython 简介

dmPython 是 DM 提供的依据 Python DB API version 2.0 中 API 使用规定而开发的数据库访问接口。

使用 Python 连接达梦数据库时需要安装 dmPython。安装完 DM 数据库软件后，在安装路径下的 drivers 目录下，可以找到 dmPython 的驱动源码，由于提供的是源码，需要自己编译安装，下面分别介绍如何在 Windows 和 Linux 环境下编译安装 dmPython。

## 二、Windows 环境编译安装 dmPython

### 2.1 安装 DM 数据库软件并设置 DM_HOME 环境变量

dmPython 源码依赖 DM 安装目录中提供的 include 头文件，编译安装前需要检查是否安装 DM 数据库软件，并设置 DM_HOME 环境变量。

可访问达梦云适配中心[下载试用](/download/?_blank)，下载 DM8 数据库试用版并安装，请参考 [DM 数据库安装](../start/dm-install-windows.html)。

设置 DM_HOME 环境变量

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/DM_HOME环境变量.png)

### 2.2 安装编译工具 Microsoft Visual C++ Build Tools

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/编译工具.png)

### 2.3 编译安装 dmPython

安装完 DM 数据库软件后，在安装路径下的 drivers 目录下，找到 dmPython 的驱动源码。

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/驱动源码.png)

进入到 dmPython 驱动源码目录

```
cd D:\dmdbms\drivers\python\dmPython
```

编译安装 dmPython

```
python setup.py install
```

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/install1.png)

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/install2.png)

编译安装结束后使用 pip list 命令查看是否安装成功

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/pip-list.png)

### 2.4 查看搜索路径并将 dpi 目录文件拷贝到搜索路径下

dmPython 通过调用 DM DPI 接口完成 Python 模块扩展。在其使用过程中，除 Python 标准库以外，还需要 DPI 的运行环境。

进入 python 解释器查看搜索路径

```
python
import sys
sys.path
```

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/查看搜索路径.png)

C:\Users\Admin\AppData\Local\Programs\Python\Python39\lib\site-packages\dmpython-2.3-py3.9-win-amd64.egg

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/拷贝.png)

### 2.5 编写测试代码

```
import dmPython
conn=dmPython.connect(user='SYSDBA',password='*****',server= '192.168.201.118',port=5236)
cursor = conn.cursor()
cursor.execute('select username from dba_users')
values = cursor.fetchall()
print(values)
cursor.close()
conn.close()
```

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/连接成功.png)

输出数据库中的用户名则表示连接数据库成功。

## 三、Linux 环境编译安装 dmPython

### 3.1 安装 DM 数据库软件和设置 DM_HOME 环境变量

dmPython 源码依赖 DM 安装目录中提供的 include 头文件，编译安装前需要检查是否安装 DM 数据库软件，并设置 DM_HOME 环境变量。

可访问达梦云适配中心[下载试用](/download/?_blank)，下载 DM8 数据库试用版并安装，请参考 [DM 数据库安装](../start/dm-install-linux.html)。

设置 DM_HOME 环境变量：

```
vi /root/.bash_profile
export DM_HOME=/home/dmdba/dmdbms
source /root/.bash_profile
```

### 3.2 安装编译工具 gcc

```
yum install gcc
```

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/安装.png)

### 3.3 确认当前使用的 Python 版本是自己编译安装的还是系统自带或通过 yum 源方式安装

如果是自己编译安装的 Python 则不需要安装 python3-devel。

如果是系统自带或通过 yum 源方式安装的 Python，需要安装 python3-devel 依赖。

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/依赖.png)

### 3.4 编译并安装 dmPython

安装完 DM 数据库软件后，在安装路径下的 drivers 目录下，找到 dmPython 的驱动源码。

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/查看.png)

进入到 dmPython 驱动源码目录

```
cd /home/dmdba/dmdbms/drivers/python/dmPython
```

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/目录.png)

编译安装 dmPython

```
python3 setup.py install
```

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/编译安装.png)

编译安装结束后使用 pip3 list 命令查看是否安装成功

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/是否安装成功.png)

### 3.5 设置 LD_LIBRARY_PATH 环境变量

dmPython 通过调用 DM DPI 接口完成 Python 模块扩展。在其使用过程中，除 Python 标准库以外，还需要 DPI 的运行环境。

```
vi /root/.bash_profile
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/dmdba/dmdbms/drivers/dpi/
source /root/.bash_profile
```

### 3.6 编写的测试代码

```
import dmPython
conn=dmPython.connect(user='SYSDBA',password='*****',server= '192.168.201.118',port=5236)
cursor = conn.cursor()
cursor.execute('select username from dba_users')
values = cursor.fetchall()
print(values)
cursor.close()
conn.close()
```

![DM_HOME 环境变量](https://download.dameng.com/eco/docs/asset/start/成功.png)

输出数据库中的用户名则表示连接数据库成功。

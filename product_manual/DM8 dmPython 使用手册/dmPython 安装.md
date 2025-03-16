

# dmPython 安装

## 2.1 安装

dmPython 可以运行在任何安装了 Python 的平台上。用户需先安装 Python，再安装 dmPython。

### 2.1.1 安装 Python

Python 软件请用户自行前往 Python 官网下载。安装任何一个版本 Python 均可。如果 Python 版本大于等于 3.12，请务必确保 Python 同时安装了 setuptools 库。

成功安装 Python 之后，可查看 Python 版本号：

```
C:\Users\dameng>python --version

Python 3.7.4
```

### 2.1.2 安装 dmPython

dmPython 目前提供离线和在线两种安装方式。选择其中一种安装方式即可。

> **注意**
>
>
> **使用第三方加密算法，dpi默认是从DM_HOME/bin/external_crypto_libs或者dm_svc.conf配置文件中CIPHER_PATH参数指定的路径加载第三方依赖库，用户需要确保第三方依赖库存在。**



#### 2.1.2.1 离线安装

第一步，安装达梦数据库。

具体的安装步骤可参考《DM8 安装手册》。本章以 Windows 安装为例，安装目录为 D:\dmdbms。

第二步，配置环境变量。

dmPython 的运行需要使用 DPI 动态库。因此用户需提前配置好环境变量，使用环境变量指定 DPI 的位置。

Linux 环境下，用户须手动将 DPI 所在目录（即 DM 安装目录中的 BIN 目录）加入到 LD_LIBRARY_PATH 环境变量中。

Windows 环境下，当 Python 版本小于等于 3.7 时，需要将 DPI 所在 BIN 目录加入环境变量 PATH，BIN 目录的上级目录 DMDBMS 加入到环境变量 DM_HOME 中。当 Python 版本大于 3.7 时，系统会自动配置好环境变量，无需用户配置。

成功配置之后，可查看环境变量。下面以 Windows 为例。

```
//查询环境变量DM_HOME

C:\Users\dameng>echo %DM_HOME%

d:\dmdbms

//查询环境变量PATH

C:\Users\dameng>echo %PATH%

…… d:\dmdbms\bin……
```

第三步，安装 dmPython。

用户既可以使用 DM 软件中的源码安装 dmPython，也可使用安装文件安装 dmPython。选择其中一种即可。

> **注意**
>
>
> **使用第三方加密算法，dpi默认是从DM_HOME/bin/external_crypto_libs或者dm_svc.conf配置文件中CIPHER_PATH参数指定的路径加载第三方依赖库，用户需要确保第三方依赖库存在。**



方法一 使用源码安装。

dmPython 源码位于达梦安装目录 dmdbms\drivers\python\dmPython 中，安装程序为 setup.py。

进入到 setup.py 所在的源码目录，执行命令：python setup.py install。可以在 Windows 或 Linux 操作系统下安装 dmPython 。

```
D:\dmdbms\drivers\python\dmPython>python setup.py install
```

在 Windows 操作系统上，如果 Python 版本大于等于 3.6，编译并安装 dmPython 时，可能会因为 vc++ 版本不够报错，可通过安装 vs2015 或更高版本的 vs 等途径满足高版本 Python 对 vc++14 的需求。

方法二 使用安装文件安装。

需要先生成安装文件再进行安装。

可以使用 Python 生成 whl 文件再使用进行安装，操作如下：

```
//进入到setup.py所在的源码目录（D:\dmdbms\drivers\python\dmPython>），执行以下命令：

python setup.py bdist_wheel

//如果遇到报错invalid command 'bdist_wheel'，需更新打包所需工具版本，去pypi官网下载合适版本的wheel包，或者使用pip在线更新，执行命令如下：

pip install wheel

pip install --upgrade setuptools

//生成whl文件后，在当前目录的dist文件夹下可以找到生成的whl文件。进入dist目录，执行如下安装命令：

pip install dmPython-2.4.5.whl
```

在 Windows 操作系统下生成 exe 文件的操作如下，之后只需要直接执行 exe 文件即可安装：

```
//进入到setup.py所在的源码目录（D:\dmdbms\drivers\python\dmPython>），执行以下命令：

python setup.py bdist_wininst

在Linux操作系统下使用rpm包安装dmPython，生成rpm包的操作如下所示：

//进入到setup.py所在的源码目录（D:\dmdbms\drivers\python\dmPython>），执行以下命令：

python setup.py bdist_rpm
```

Linux 安装和卸载命令参考如下：

```
安装：rpm -ivh dmPython-2.1-7.1-py33-1.x86_64.rpm --nodeps

卸载：rpm -e dmPython-2.1-1.x86_64
```

第四步，查看 dmPython 版本号。

安装完成后，通过 pip 查看 dmPython 的版本。

```
pip show dmPython
```

#### 2.1.2.2 在线安装

在拥有网络的环境下安装 dmPython，可选择在线安装方式。在线安装方式目前支持的 python 版本为 2.7,3.4-3.12，支持的平台为 win_amd64，x86_64。

第一步，安装 dmPython。

在命令行工具中输入命令。

```
pip install dmPython
```

第二步，查看 dmPython 版本号。

安装完成后，通过 pip 查看 dmPython 的版本。

```
pip show dmPython
```

## 2.2 使用

在 Python 软件中使用 dmPython 接口时，用户既可以在交互界面逐行输入命令，也可以通过文件批量执行命令。

例 1 在 Python 交互界面逐行输入命令。

```
C:\Users\dameng>python

Python 3.7.4 (tags/v3.7.4:e09359112e, Jul 8 2019, 20:34:20) [MSC v.1916 64 bit (AMD64)] on win32

Type "help", "copyright", "credits" or "license" for more information.

>>> import dmPython

>>> conn = dmPython.connect('SYSDBA', '*****', 'localhost:5236')

>>> dmPython.apilevel

'2.0'

>>> 
```

例 2 通过文件批量执行命令。将待执行的 dmPython 命令全部存在一个文件中，然后一次性执行文件。达到批量执行命令的效果。

文件 test.py 包含多条命令。内容如下：

```
from datetime import date

d = date(2024,6,10)

print (d)

import dmPython

conn = dmPython.connect()

cursor = conn.cursor()

cursor.execute("create table test_date_xxx(c1 date)")

cursor.execute("insert into test_date_xxx values(?)", d)

Seq_params = [(d,), (d,)]

cursor.executemany("insert into test_date_xxx values(?)", Seq_params)

cursor.execute("select * from test_date_xxx ")

print(cursor.description)

print(cursor.fetchall())
```

执行 test.py 文件。

```
2024-06-10

[('C1', <class 'dmPython.DATE'>, 10, 10, 10, 0, 1)]

[(datetime.date(2024, 6, 10),), (datetime.date(2024, 6, 10),), (datetime.date(2024, 6, 10),)]
```

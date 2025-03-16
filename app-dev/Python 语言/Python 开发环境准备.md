

# Python 开发环境准备

## 一、环境准备

|**名称**|**版本**|
|--|--|
|DM 数据库|DM 8.0 及以上版本|
|Python|3.6.8 及以上版本|
|dmPython|2.3 及以上版本|


### 1.1 达梦数据库安装

请参考[《数据库环境准备》](https://eco.dameng.com/document/dm/zh-cn/app-dev/index.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 1.2 配置环境

#### 1.2.1 Linux 环境

根据实际路径，本例达梦安装目录为 /dm/dmdbms，添加环境变量到用户或系统下。

```
[root@dm8 dmPython]# vi /root/.bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi

# User specific environment and startup programs
export DM_HOME="/dm/dmdbms"
export PATH=$PATH:$DM_HOME/bin
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:$DM_HOME/drivers/dpi:$DM_HOME/bin

[root@dm8 dmPython]# source /root/.bash_profile
```

#### 1.2.2 Windows 环境

1、在 path 系统变量下添加 dpi 和达梦环境变量。

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240517165837DGLQPFVTC6XHJWO8B6)

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240517165848ZBWNMMCBUA0REHW9V5)

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405171658597SHBP3EU6GKHNOQ0TA)

2、安装编译工具 Microsoft Visual C++ Build Tools 。

先点击 [Visual Studio 官网](https://visualstudio.microsoft.com/zh-hans/)进行下载需要版本。

![1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240722101445H08K1XHYEOJH11NAY9)

下载完成后进行安装。

![2afc448b313744028de726d35064ee4d.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407221015114QAFCC6QR0X1FSN39W)

安装完成后，点击修改可以增加或删除组件。

![3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240722102000DYBG2R4MBCO2FQBNNC)

![4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407221020517ZXHQPWMR4012EZTU9)

## 二、安装 Python

### 2.1 在 Linux 下安装 Python

  1. 打开 WEB 浏览器访问 \<https://www.python.org/downloads/source/>
  2. 选择适用 Unix/Linux 的源码压缩包
  3. 下载并解压压缩包
  4. 执行 ./configure 脚本
  5. 依次执行 make 和 make install 后安装完成



### 2.2 在 Winodws 下安装 Python

1、打开 WEB 浏览器访问 [https://www.python.org/downloads/windows/](https://www.python.org/downloads/windows/) ，在下载列表中选择适合自己电脑环境的 Window 平台安装包。

![微信图片_20240729101203.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240729101222MR8CBX0YU0GIY3GCPL)

2、下载后，双击下载包，进入 Python 安装向导，只需要使用默认的设置一直点击“下一步”按钮直到安装完成即可。

## 三、编译安装 DM 驱动

### 3.1 在 Linux 下安装 dmPython

1、安装编译工具 gcc。

Linux 环境下安装 dmPython 需要安装编译工具 gcc，手动执行命令 yum install gcc 进行安装（这里需要预先配置好 yum 源）。

![微信图片_20240711111756.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407111130412MUATZ5ULN29ZBURF8)

2、在 Linux 下安装 dmPython 有两种方法。

方法一：使用达梦数据库软件自带的 dmPython 驱动包。

由于应用（不论什么语言开发）都需要通过对应的数据库驱动来连接数据库对数据库的数据进行操作，达梦数据库的驱动主要存放在数据库安装服务器的数据库软件目录下，例如：

数据库软件安装目录为：/dm/dmdbms，则 dmPython 驱动目录在： /dm/dmdbms/drivers/python，如下图所示：

![微信截图_20240711113159.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240711113359FDGDDMMRH2N25FW628)

切换到达梦软件安装目录下的 /drivers/python/dmPython 目录下。

```
[root@RS1821 /]# cd /dm/dmdbms/drivers/python/dmPython
[root@RS1821 dmPython]# ls
Buffer.c      Cursor.c                Environment.c  exLob.c      README.txt  strct.h    var.c      vDateTime.c  vNumber.c
Buffer.h      DatabaseCheck_2.7.spec  Error.c        exObject.c   row.c       tObject.c  var_pub.h  vInterval.c  vObject.c
build         dist                    Error.h        py_Dameng.c  row.h       trc.c      vBfile.c   vLob.c       vString.c
Connection.c  dmPython.egg-info       exBfile.c      py_Dameng.h  setup.py    trc.h      vCursor.c  vlong.c
```

执行命令进行手动安装：python setup.py install 。

```
[root@dm8 dmPython]# python setup.py install
……
creating dist
creating 'dist/dmPython-2.3-py2.7-linux-x86_64.egg' and adding 'build/bdist.linux-x86_64/egg' to it
removing 'build/bdist.linux-x86_64/egg' (and everything under it)
Processing dmPython-2.3-py2.7-linux-x86_64.egg
Copying dmPython-2.3-py2.7-linux-x86_64.egg to /usr/lib64/python2.7/site-packages
Adding dmPython 2.3 to easy-install.pth file

Installed /usr/lib64/python2.7/site-packages/dmPython-2.3-py2.7-linux-x86_64.egg
Processing dependencies for dmPython==2.3
Finished processing dependencies for dmPython==2.3
[root@dm8 dmPython]#
```

方法二：使用驱动源码安装 dmPython，获取[达梦 python 驱动源码](https://download.dameng.com/eco/docs/python-126594-20201027.zip)并解压。

```
[root@RS1821 /]# unzip python-126594-20201027.zip
[root@RS1821 /]# cd python/dmPython_C/dmPython/
[root@RS1821 dmPython]# python setup.py install
...
...
...
Installed /usr/lib64/python2.7/site-packages/dmPython-2.3-py2.7-linux-x86_64.egg
Processing dependencies for dmPython==2.3
Finished processing dependencies for dmPython==2.3
[root@RS1821 dmPython]#
```

3、获取 dmPython 版本。

使用以上任意方法成功安装 dmPython 之后可以获取 dmPython 版本，查询方式有以下两种方法：

方法一：通过驱动内置常量获取版本号信息。

在操作系统执行 python 命令，进入 python 交互式开发界面。

在交互式开发界面执行 import dmPython，导入驱动。

在交互式开发界面执行 dmPython.version，获取当前的模块版本号常量，如图，当前 dmPython 版本号为 2.3 。

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240517171846TITGDA3WDN6SPZXJQX)

方法二：通过 python 包管理工具 pip 查看各 python 包的版本（需要预先安装 pip ），在操作系统执行 pip list 命令查询各 python 包版本，其中也包含 dmPython 的版本信息，如图所示：

![微信截图_20240711172849.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240711172902J6K4XO55UV23Z1KVYR)

### 3.2 在 Window 下安装  dmPython

1、进入到 dmPython 驱动源码目录。

```
cd D:\dmdbms\drivers\python\dmPython
```

2、执行命令 python setup.py install，编译安装 dmPython 。

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240517172313L0GX19LZ6GNQFIGKTS)

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240517172322J5ZGWKDLL3KGP9C38H)

3、编译安装结束后执行 pip list 命令，查看是否安装成功。

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240711120226RBS18P5IW3M8K9RC9E)

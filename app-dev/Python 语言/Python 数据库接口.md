

# Python 数据库接口

## 一、前言

Python 是一个高层次的结合了解释性、编译性、互动性和面向对象的脚本语言。运行 Python 程序需要解释器的支持，只要在不同的平台安装不同的解释器，Python 代码就可以跨平台运行，可移植性强，不用担心任何兼容性问题。

DMPython 是 DM 提供的依据 Python DB API version 2.0 中 API 使用规定而开发的数据库访问接口。DMPython 实现这些 API，使 Python 应用程序能够对 DM 数据库进行访问。

DMPython 通过调用 DM DPI 接口完成 Python 模块扩展。在其使用过程中，除 Python 标准库以外，还需要 DPI 的运行环境。以下是通过 DMPython 对 DM 数据库进行一些基本操作的示例。

## 二、数据库连接

  1. Python 接口登录、登出示例程序 py_conn.py 如下：



```
#!/usr/bin/python
#coding:utf-8
import dmPython
try:
    conn = dmPython.connect(user='SYSDBA', password='*****', server='localhost',  port=51236)
    cursor  = conn.cursor()
    print('python: conn success!')
    conn.close()
except (dmPython.Error, Exception) as err:
    print(err)
```

  2. 执行结果如下：



```
[root@RS1821 pytest]# python py_conn.py
python: conn success!
[root@RS1821 pytest]#
```

## 三、开发示例

### 3.1 基础操作示例

  1. Python 接口增、删、改、查四个基本操作，示例程序 py_dml.py 如下：



```
#!/usr/bin/python
#coding:utf-8
import dmPython
try:
    conn = dmPython.connect(user='SYSDBA', password='*****', server='localhost',  port=51236)
    cursor  = conn.cursor()
    try:
        #清空表，初始化测试环境
        cursor.execute ('delete from PRODUCTION.PRODUCT_CATEGORY')
    except (dmPython.Error, Exception) as err:
        print(err)

    try:
        #插入数据
        cursor.execute ("insert into PRODUCTION.PRODUCT_CATEGORY(NAME) values('语文'), ('数学'), ('英语'), ('体育')")
        print('python: insert success!')
        #删除数据
        cursor.execute ("delete from PRODUCTION.PRODUCT_CATEGORY where name='数学'")
        print('python: delete success!')

        #更新数据
        cursor.execute ('update PRODUCTION.PRODUCT_CATEGORY set name = \'英语-新课标\' where name=\'英语\'')
        print('python: update success!')

        #查询数据
        cursor.execute ("select name from PRODUCTION.PRODUCT_CATEGORY")
        res = cursor.fetchall()
        for tmp in res:
            for c1 in tmp:
                print(c1)

        print('python: select success!')
    except (dmPython.Error, Exception) as err:
        print(err)

    conn.close()
except (dmPython.Error, Exception) as err:
    print(err)
```

  2. 执行结果如下：



```
[root@RS1821 pytest]# python py_dml.py
python: insert success!
python: delete success!
python: update success!
语文
英语-新课标
体育
python: select success!
[root@RS1821t pytest]#
```

### 3.2 绑定变量示例

  1. Python 接口绑定变量示例程序 py_bind.py 如下：



```
#!/usr/bin/python
#coding:utf-8
import dmPython
try:
    conn = dmPython.connect(user='SYSDBA', password=*****, server='localhost',  port=51236)
    cursor  = conn.cursor()
    try:
        #清空表，初始化测试环境
        cursor.execute ('delete from PRODUCTION.PRODUCT_CATEGORY')
    except (dmPython.Error, Exception) as err:
        print(err)

    try:
        #插入数据
        values = ('物理')
        cursor.execute ("insert into PRODUCTION.PRODUCT_CATEGORY(name) values(?)", values)
        print('python: insert success!')

        #查询数据
        cursor.execute ("select name from PRODUCTION.PRODUCT_CATEGORY")
        res = cursor.fetchall()
        for tmp in res:
            for c1 in tmp:
                print(c1)

        print('python: select success!')
    except (dmPython.Error, Exception) as err:
        print(err)


    conn.close()
except (dmPython.Error, Exception) as err:
    print(err)
```

  2. 执行结果如下：



```
[root@RS1821 pytest]# python py_bind.py
python: insert success!
物理
python: select success!
[root@RS1821 pytest]#
```

### 3.3 大字段操作示例

  1. Python 接口操作大字段（本例以 blob、clob 为例）示例程序 py_blob.py 如下：



```
import sys
longstring = ""
longstring += 'ABCDEF0123456789' * 500
cvalue = longstring
if sys.version_info[0] >= 3 :
    bvalue = longstring.encode("ascii")
else :
    bvalue = longstring
import dmPython
conn = dmPython.connect(user='SYSDBA', password='*****', server='localhost',  port=51236)
cursor = conn.cursor()
try:
    #清理测试环境
    cursor.execute("select object_id from all_objects where object_type='TABLE' and OBJECT_NAME='BIG_DATA';")
    bigdata_id = cursor.fetchone()
    if(bigdata_id):
        cursor.execute('drop table PRODUCTION.BIG_DATA;')
        print('drop table success') 
    cursor.execute('create table PRODUCTION.BIG_DATA(c1 blob, c2 clob)')
    print('create table success!')
    cursor.execute('insert into PRODUCTION.BIG_DATA values(?, ?)', bvalue, cvalue)
    print('insert success!')
    cursor.execute('select * from PRODUCTION.BIG_DATA')
    print('select success!')
    cursor.description
    row = cursor.fetchone()
    (blob, clob) = row
    if sys.version_info[0] >= 3 :
        type(blob)
        type(clob)
    blob
    clob
except(dmPython.Error,Exception) as err:
    print(err)
conn.close
```

  2. 执行结果如下：



```
[root@RS1821 pytest]# python py_blob.py
drop table success!
create table success!
insert success!
select success!
[root@RS1821 pytest]#
```

### 3.4 多线程示例

  1. Python 接口利用多线程连接数据库示例程序 py_multi.py 如下：



```
#!/usr/bin/python
# -*- coding: UTF-8 -*-

import dmPython
import _thread
import time

# 为线程定义一个函数
def print_time( threadName, delay):
    count = 0
    while count < 3:
        try:
            conn = dmPython.connect(user='SYSDBA',password='*****',server='localhost',port=51236)
            cursor = conn.cursor()
            print ("%s: %s" % ( threadName, time.ctime(time.time()) ))
            conn.close()
            time.sleep(delay)
            count += 1
        except(dmPython.Error, Exception) as err:
            print(err)

# 创建两个线程
try:
    _thread.start_new_thread(print_time, ("Thread-1", 1,))
    _thread.start_new_thread(print_time, ("Thread-2", 2,))
except:
    print ("Error: unable to start thread")

while 1:
    pass
```

  2. 执行结果。



```
[root@RS1821 pytest]# python py_multi.py
Thread-2: Fri Jun 10 10:47:26 2022,connect success!!
Thread-1: Fri Jun 10 10:47:26 2022,connect success!!
Thread-1: Fri Jun 10 10:47:27 2022,connect success!!
Thread-2: Fri Jun 10 10:47:28 2022,connect success!!
Thread-1: Fri Jun 10 10:47:28 2022,connect success!!
Thread-2: Fri Jun 10 10:47:30 2022,connect success!!
```

### 3.5 Trace 跟踪

在 dm_svc.conf 文件中配置打开 dpi trace 跟踪开关，设置 `DPI_TRACE=(1)` 如图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216211744MZSRWN80YFTOLT5NQS)

设置生效后再执行 python 脚本时，就会在执行位置生成 trace 日志文件。例如，修改 py_conn.py 脚本，模拟连接出错的情况，执行结果如下，同时会在执行位置生成 dmPython_trace.log、dpi_trace.log 两个 trace 文件。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216211753FXU4AYDQL1C4TOMMQZ)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216211804455JM3LBX666K8A0JE)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216211814AJJ2JFFGKO7SFV5CNK)

## 四、常见问题

1. Linux 下安装 dmPython 时 permission denied

【问题描述】：

```
[dmdba@dm8 dmPython]$ python setup.py install
running install
error: can't create or remove files in install directory

The following error occurred while trying to add or remove files in the
installation directory:

    [Errno 13] Permission denied: '/usr/lib64/python2.7/site-packages/test-easy-install-3032.write-test'

The installation directory you specified (via --install-dir, --prefix, or
the distutils default setting) was:

    /usr/lib64/python2.7/site-packages/

Perhaps your account does not have write access to this directory?  If the
installation directory is a system-owned directory, you may need to sign in
as the administrator or "root" account.  If you do not have administrative
access to this machine, you may wish to choose a different installation
directory, preferably one that is listed in your PYTHONPATH environment
variable.

For information on other options, you may wish to consult the
documentation at:

  https://pythonhosted.org/setuptools/easy_install.html

Please make the appropriate changes for your system and try again.

[dmdba@dm8 dmPython]$
```

【问题解决】：

由报错信息：Permission denied: '/usr/lib64/python2.7/site-packages/test-easy-install-3032.write-test' 可知，是由于在安装 dmPython 过程中权限不足导致的错误，不能使用 dmdba 用户执行 setup.py 脚本，需要切换为 root 用户操作。

2. Linux 下安装 dmPython 时 cannot locate an Dameng software installation

【问题描述】：

```
[root@dm8 ~]# cd /dm/dmdbms/drivers/python/dmPython
[root@dm8 dmPython]# python setup.py install
Traceback (most recent call last):
 File "setup.py", line 103, in <module>
   raise DistutilsSetupError("cannot locate an Dameng software " /
distutils.errors.DistutilsSetupError: cannot locate an Dameng software installation
[root@dm8 dmPython]#
```

【问题解决】：这是 DPI 环境问题，因为 dmPython 的运行需要使用 dpi 动态库，应该将 dpi 所在目录（通常是 $DM_HOME/bin 目录）加入系统的环境变量，如下所示：

```
[root@dm8 dmPython]# cat ~/.bash_profile 
# .bash_profile

# Get the aliases and functions
if [ -f ~/.bashrc ]; then
    . ~/.bashrc
fi

# User specific environment and startup programs

PATH=$PATH:$HOME/bin

export PATH

export PATH="/dm/dmdbms/bin:$PATH"

export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/dm/dmdbms/bin"
export DM_HOME="/dm/dmdbms"
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/dm/dmdbms/drivers/dpi/
[root@dm8 dmPython]#
```

3. Linux 下安装 dmPython 时 unable to execute gcc

【问题描述】：

```
gcc -pthread -fno-strict-aliasing -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -DNDEBUG -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -fPIC -DDM64 -I/dm/dmdbms/include -I/usr/include/python2.7 -c py_Dameng.c -o build/temp.linux-x86_64-2.7/py_Dameng.o -DBUILD_VERSION=2.3
unable to execute gcc: No such file or directory
error: command 'gcc' failed with exit status 1
[root@dm8 dmPython]#
```

【问题解决】：根据报错信息：command 'gcc' failed with exit status 1 可知，缺少 gcc 依赖包，所以直接通过 yum 安装补齐即可。

```
[root@dm8 dmPython]# yum install gcc* -y
```

4. Liunx 下安装 dmPython 时报错："Python.h 没有那个文件或目录"

【问题描述】：

```
[root@RS1821 dmPython]# python setup.py install
running install
running bdist_egg
running egg_info
writing dmPython.egg-info/PKG-INFO
writing top-level names to dmPython.egg-info/top_level.txt
writing dependency_links to dmPython.egg-info/dependency_links.txt
reading manifest file 'dmPython.egg-info/SOURCES.txt'
writing manifest file 'dmPython.egg-info/SOURCES.txt'
installing library code to build/bdist.linux-x86_64/egg
running install_lib
running build_ext
building 'dmPython' extension
gcc -pthread -fno-strict-aliasing -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -DNDEBUG -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -fPIC -DDM64 -I/dm8/include -I/dm8/drivers/python/dmPython -I/usr/include/python2.7 -c py_Dameng.c -o build/temp.linux-x86_64-2.7/py_Dameng.o -DBUILD_VERSION=2.4.4
In file included from py_Dameng.c:3:0:
py_Dameng.h:8:20: 致命错误：Python.h：没有那个文件或目录
 #include <Python.h>
                    ^
编译中断。
error: command 'gcc' failed with exit status 1
```

【问题解决】：此问题是由于缺少 python-devel 包导致。使用命令：`yum install python-devel` 安装 devel 包后即可成功安装 dmPython。

5. Windows 下导入时找不到指定的 dmPython 模块

【问题描述】：

```
C:\Users\Administrator>python
Python 3.9.6 (tags/v3.9.6:db3ff76, Jun 28 2021, 15:26:21) [MSC v.1929 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import dmPython
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
ImportError: DLL load failed while importing dmPython: 找不到指定的模块。
```

【问题解决】：Python 3.8 变更了 Windows 下动态链接库 (DLL) 的加载规则。新的规则提高了安全性，默认情况下仅能从可信的位置（Trusted Locations）加载 DLL 依赖，一定程度上避免诸如 DLL 劫持之类的安全风险。此时需要搜索路径并把 dpi 文件复制到指定目录下。

6. Python“Non-ASCII character 'xe5' in file” 报错

【问题描述】：编译 python 程序时，出现错误如下：

```
SyntaxError: Non-ASCII character '\xe5' in file kNN.py on line 24, but no encoding declared; see http://python.org/dev/peps/pep-0263/ for details
```

【问题解决】：Python 默认是以 ASCII 作为编码方式的，如果在自己的 Python 源码中包含了中文（或者其他非英语系的语言），此时即使你把自己编写的 Python 源文件以 UTF-8 格式保存了，但实际上，这依然是不行的。需要在报错源码的第一行加上以下代码，则指定文件的编码格式是 utf-8。

```
# -*- coding: UTF-8 -*-
```

7. 无法解析的外部符号错误

【问题描述】：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228113241HC4I9SYM3ILG2K7SPA)

【问题解决】：考虑环境变量是否配置正确，以及 python 源码的位数和数据库软件的位数（32 或 64 位）是否一致。

8. 其他问题

其他更多 Python 相关的常见问题请参考：[https://eco.dameng.com/document/dm/zh-cn/faq/faq-python.html](https://eco.dameng.com/document/dm/zh-cn/faq/faq-python.html)

## 五、参考

  1. 示例代码下载：[Python_Code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221216213103715I7UBQ8JRIE8O3QP)。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。



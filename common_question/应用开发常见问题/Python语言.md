

# Python语言

## 前言

本章节主要介绍 Python 应用开发常见问题，为用户提供 Python 应用开发常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [如何关闭 autoCommit 及事务控制相关内容](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E5%A6%82%E4%BD%95%E5%85%B3%E9%97%AD%20autoCommit%20%E5%8F%8A%E4%BA%8B%E5%8A%A1%E6%8E%A7%E5%88%B6%E7%9B%B8%E5%85%B3%E5%86%85%E5%AE%B9)
  * [Linux 下安装 dmPython 时报错：permission denied](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Linux%20%E4%B8%8B%E5%AE%89%E8%A3%85%20dmPython%20%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9Apermission%20denied)
  * [Linux 下安装 dmPython 时报错：cannot locate an Dameng software installation](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Linux%20%E4%B8%8B%E5%AE%89%E8%A3%85%20dmPython%20%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9Acannot%20locate%20an%20Dameng%20software%20installation)
  * [Linux 下安装 dmPython 时报错：unable to execute gcc](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Linux%20%E4%B8%8B%E5%AE%89%E8%A3%85%20dmPython%20%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9Aunable%20to%20execute%20gcc)
  * [Liunx 下安装 dmPython 时报错：​"Python.h 没有那个文件或目录​"](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Liunx%20%E4%B8%8B%E5%AE%89%E8%A3%85%20dmPython%20%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%22Python.h%20%E6%B2%A1%E6%9C%89%E9%82%A3%E4%B8%AA%E6%96%87%E4%BB%B6%E6%88%96%E7%9B%AE%E5%BD%95%22)
  * [Windows 下导入时找不到指定的 dmPython 模块](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Windows%20%E4%B8%8B%E5%AF%BC%E5%85%A5%E6%97%B6%E6%89%BE%E4%B8%8D%E5%88%B0%E6%8C%87%E5%AE%9A%E7%9A%84%20dmPython%20%E6%A8%A1%E5%9D%97)
  * [Non-ASCII character ​'xe5​' in file](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Non-ASCII%20character%20'xe5'%20in%20file)
  * [无法解析的外部符号错误](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E6%97%A0%E6%B3%95%E8%A7%A3%E6%9E%90%E7%9A%84%E5%A4%96%E9%83%A8%E7%AC%A6%E5%8F%B7%E9%94%99%E8%AF%AF)
  * [windows 10/11 及 server 2016 版本安装 dmPython 报错](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#windows%2010/11%20%E5%8F%8A%20server%202016%20%E7%89%88%E6%9C%AC%E5%AE%89%E8%A3%85%20dmPython%20%E6%8A%A5%E9%94%99)
  * [无法识别 django-admin 命令](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E6%97%A0%E6%B3%95%E8%AF%86%E5%88%AB%20django-admin%20%E5%91%BD%E4%BB%A4)
  * [SyntaxError: (unicode error) ​'utf-8​' codec can​'t decode byte 0xb2 in position 0: invalid start byte](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#SyntaxError:%20\(unicode%20error\)%20'utf-8'%20codec%20can't%20decode%20byte%200xb2%20in%20position%200:%20invalid%20start%20byte)
  * [​'int​' object is not subscriptable](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#'int'%20object%20is%20not%20subscriptable)
  * [error：Unable to find vcvarsall.bat](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#error%EF%BC%9AUnable%20to%20find%20vcvarsall.bat)
  * [导入 dmpython 模块时报错找不到指定的模块](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E5%AF%BC%E5%85%A5%20dmpython%20%E6%A8%A1%E5%9D%97%E6%97%B6%E6%8A%A5%E9%94%99%E6%89%BE%E4%B8%8D%E5%88%B0%E6%8C%87%E5%AE%9A%E7%9A%84%E6%A8%A1%E5%9D%97)
  * [DMPython 驱动在哪里可以找到](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#DMPython%20%E9%A9%B1%E5%8A%A8%E5%9C%A8%E5%93%AA%E9%87%8C%E5%8F%AF%E4%BB%A5%E6%89%BE%E5%88%B0)
  * [DMPython 的安装教程](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#DMPython%20%E7%9A%84%E5%AE%89%E8%A3%85%E6%95%99%E7%A8%8B)
  * [DMPython 连接 DM，报错 ImportError: No module named dmPython](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#DMPython%20%E8%BF%9E%E6%8E%A5%20DM%EF%BC%8C%E6%8A%A5%E9%94%99%20ImportError:%20No%20module%20named%20dmPython)
  * [DMPython 安装报错：不是有效的 Win32 应用程序](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#DMPython%20%E5%AE%89%E8%A3%85%E6%8A%A5%E9%94%99%EF%BC%9A%E4%B8%8D%E6%98%AF%E6%9C%89%E6%95%88%E7%9A%84%C2%A0Win32%C2%A0%E5%BA%94%E7%94%A8%E7%A8%8B%E5%BA%8F)
  * [DMPython 安装报错：依赖 VC++14.0](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#DMPython%20%E5%AE%89%E8%A3%85%E6%8A%A5%E9%94%99%EF%BC%9A%E4%BE%9D%E8%B5%96%20VC++14.0)
  * [cannot locate an Dameng software](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#cannot%20locate%20an%20Dameng%20software)
  * [ImportError: DLL load failed](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#ImportError:%20DLL%20load%20failed)
  * [Python 数据 insert 进 DM，报错：codec can​'t encode character](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Python%20%E6%95%B0%E6%8D%AE%20insert%20%E8%BF%9B%20DM%EF%BC%8C%E6%8A%A5%E9%94%99%EF%BC%9Acodec%20can't%20encode%20character)
  * [ImportError: libdmdpi.so: cannot open shared object file: No such file or directory](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#ImportError:%20libdmdpi.so:%20cannot%20open%20shared%20object%20file:%20No%20such%20file%20or%20directory)
  * [匹配没有可登陆的服务器](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E5%8C%B9%E9%85%8D%E6%B2%A1%E6%9C%89%E5%8F%AF%E7%99%BB%E9%99%86%E7%9A%84%E6%9C%8D%E5%8A%A1%E5%99%A8)
  * [同一个应用向 DM7.1 和 DM7.6 中插入数据，DM7.1 的库中数据正常，DM7.6 的库中中文出现乱码](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E5%90%8C%E4%B8%80%E4%B8%AA%E5%BA%94%E7%94%A8%E5%90%91%20DM7.1%20%E5%92%8C%20DM7.6%20%E4%B8%AD%E6%8F%92%E5%85%A5%E6%95%B0%E6%8D%AE%EF%BC%8CDM7.1%20%E7%9A%84%E5%BA%93%E4%B8%AD%E6%95%B0%E6%8D%AE%E6%AD%A3%E5%B8%B8%EF%BC%8CDM7.6%20%E7%9A%84%E5%BA%93%E4%B8%AD%E4%B8%AD%E6%96%87%E5%87%BA%E7%8E%B0%E4%B9%B1%E7%A0%81)
  * [Python 使用连接池 PooledDB 连接达梦数据库](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Python%20%E4%BD%BF%E7%94%A8%E8%BF%9E%E6%8E%A5%E6%B1%A0%20PooledDB%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [Python 运行时报错：load fun EVP_sm3_fun fail!](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#Python%20%E8%BF%90%E8%A1%8C%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9Aload%20fun%20EVP_sm3_fun%20fail!)
  * [导入 dmpython 报错 ：PyUnicodeUCS2_Format](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E5%AF%BC%E5%85%A5%20dmpython%20%E6%8A%A5%E9%94%99%20%EF%BC%9APyUnicodeUCS2_Format)
  * [SqlAlchemy_dm 插入数据报错：TypeError: ​'NoneType​' object is not subscriptable](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#SqlAlchemy_dm%20%E6%8F%92%E5%85%A5%E6%95%B0%E6%8D%AE%E6%8A%A5%E9%94%99%EF%BC%9ATypeError:%20'NoneType'%20object%20is%20not%20subscriptable)
  * [安装 dmPython rpm 包成功，但程序使用 import dmPython 报错](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E5%AE%89%E8%A3%85%20dmPython%20rpm%20%E5%8C%85%E6%88%90%E5%8A%9F%EF%BC%8C%E4%BD%86%E7%A8%8B%E5%BA%8F%E4%BD%BF%E7%94%A8%20import%20dmPython%20%E6%8A%A5%E9%94%99)
  * [R 语言连接 DM 的主备集群如何以服务名的形式配置高可用？](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#R%20%E8%AF%AD%E8%A8%80%E8%BF%9E%E6%8E%A5%20DM%20%E7%9A%84%E4%B8%BB%E5%A4%87%E9%9B%86%E7%BE%A4%E5%A6%82%E4%BD%95%E4%BB%A5%E6%9C%8D%E5%8A%A1%E5%90%8D%E7%9A%84%E5%BD%A2%E5%BC%8F%E9%85%8D%E7%BD%AE%E9%AB%98%E5%8F%AF%E7%94%A8%EF%BC%9F)
  * [使用 django 框架项目启动报错： mportError: cannot import name ​'force_text​' from ​'django.utils.encoding​'](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E4%BD%BF%E7%94%A8%20django%20%E6%A1%86%E6%9E%B6%E9%A1%B9%E7%9B%AE%E5%90%AF%E5%8A%A8%E6%8A%A5%E9%94%99%EF%BC%9A%20mportError:%20cannot%20import%20name%20'force_text'%20from%20'django.utils.encoding')
  * [使用 django 框架项目启动报错：​'django_dmPython​' isn​'t an available database backend xxx](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#%E4%BD%BF%E7%94%A8%20django%20%E6%A1%86%E6%9E%B6%E9%A1%B9%E7%9B%AE%E5%90%AF%E5%8A%A8%E6%8A%A5%E9%94%99%EF%BC%9A'django_dmPython'%20isn't%20an%20available%20database%20backend%20xxx)
  * [django 框架连接 DM 的主备集群如何以服务名的形式配置高可用？](https://eco.dameng.com/document/dm/zh-cn/faq/go-Python-new.html#django%20%E6%A1%86%E6%9E%B6%E8%BF%9E%E6%8E%A5%20DM%20%E7%9A%84%E4%B8%BB%E5%A4%87%E9%9B%86%E7%BE%A4%E5%A6%82%E4%BD%95%E4%BB%A5%E6%9C%8D%E5%8A%A1%E5%90%8D%E7%9A%84%E5%BD%A2%E5%BC%8F%E9%85%8D%E7%BD%AE%E9%AB%98%E5%8F%AF%E7%94%A8%EF%BC%9F)



## 正文

### 如何关闭 autoCommit 及事务控制相关内容

【问题描述】

如何关闭 autocommit 及事务控制相关内容。

【问题解决】

以下为代码解决方案。

  1. 建表语句。



```
create table tab1 (
                id   varchar2(32)   ,
                col1 varchar(32)    ,
                col2 varchar(32)    ,
                col3 varchar(32)    ,
                col4 varchar(32)    ,
                col5 varchar(32)    ,
                col6 varchar(32)    ,
                createtime timestamp,
                modifytime timestamp
        );
insert into tab1
select
        rownum as id               ,
        dbms_random.string('x', 20),
        dbms_random.string('x', 20),
        dbms_random.string('x', 20),
        dbms_random.string('x', 20),
        dbms_random.string('x', 20),
        dbms_random.string('x', 20),
        sysdate                    ,
        sysdate
from
        dual connect by level <= 100;
commit;
```

  2. 示例代码。



```
# -*- coding:utf-8 -*-
import dmPython

def demo():
    # 关闭自动提交
    conn = dmPython.connect(user='SYSDBA', password='SYSDBA', server='localhost', port=5236, autoCommit=True)
    cursor = conn.cursor()

    # 模拟循环处理过程中分步提交控制事务体积
    for i in range(1, 100000):
        str_i = str(i)
        insert_sql = "insert into tab1(col1,col2,col3,col4,col5,col6) values ('%s','%s','%s','%s','%s','%s')" % (
            "colA" + str_i, "colB" + str_i, "colC" + str_i, "colD" + str_i, "colE" + str_i, "colF" + str_i)
        update_sql = "update tab1 set col1='%s' where id=%s" % ("update" + str_i, i)
        delete_sql = "delete from tab1 where id=%s" % (i)
        try:
            cursor.execute(insert_sql)

            # 每10000行提交一次
            if (i % 10000) == 0:
                conn.commit()
        except Exception as err:
            print("error", err)
            conn.rollback()

    conn.commit()
    cursor.close()
    conn.close()

if __name__ == '__main__':
    demo()
```

### Linux 下安装 dmPython 时报错：permission denied

【问题描述】

安装 dmPython 时，报错如下：

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

【问题解决】

由报错信息 '/usr/lib64/python2.7/site-packages/test-easy-install-3032.write-test' 可知：在安装 dmPython 过程中权限不足导致出现报错，不能使用 dmdba 用户执行 setup.py 脚本，需要切换为 root 用户操作。

### Linux 下安装 dmPython 时报错：cannot locate an Dameng software installation

【问题描述】

使用如下命令报错：

```
[root@dm8 ~]# cd /dm/dmdbms/drivers/python/dmPython
[root@dm8 dmPython]# python setup.py install
Traceback (most recent call last):
 File "setup.py", line 103, in <module>
   raise DistutilsSetupError("cannot locate an Dameng software " /
distutils.errors.DistutilsSetupError: cannot locate an Dameng software installation
```

【问题解决】

这是 DPI 环境问题，因为 dmPython 的运行需要使用 dpi 动态库，应该将 dpi 所在目录（通常是数据库安装目录的 bin 目录）加入系统的环境变量，如图所示：

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

[root@dm8 dmPython]# source ~/.bash_profile 
```

### Linux 下安装 dmPython 时报错：unable to execute gcc

【问题描述】

使用如下命令报错：

```
[root@dm8 dmPython]# gcc -pthread -fno-strict-aliasing -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -DNDEBUG -O2 -g -pipe -Wall -Wp,-D_FORTIFY_SOURCE=2 -fexceptions -fstack-protector-strong --param=ssp-buffer-size=4 -grecord-gcc-switches -m64 -mtune=generic -D_GNU_SOURCE -fPIC -fwrapv -fPIC -DDM64 -I/dm/dmdbms/include -I/usr/include/python2.7 -c py_Dameng.c -o build/temp.linux-x86_64-2.7/py_Dameng.o -DBUILD_VERSION=2.3

unable to execute gcc: No such file or directory
error: command 'gcc' failed with exit status 1
```

【问题解决】

根据报错信息：command 'gcc' failed with exit status 1 可知，缺少 gcc 依赖包，所以直接通过 yum 安装。

```
[root@dm8 dmPython]# yum install gcc* -y
```

### Liunx 下安装 dmPython 时报错："Python.h 没有那个文件或目录"

【问题描述】

使用如下命令报错：

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

【问题解决】

此问题是由于缺少 python-devel 包导致，使用命令 `yum install python-devel` 安装 devel 包后即可成功安装 dmPython。

### Windows 下导入时找不到指定的 dmPython 模块

【问题描述】

使用如下命令报错：

```
C:\Users\Administrator>python
Python 3.9.6 (tags/v3.9.6:db3ff76, Jun 28 2021, 15:26:21) [MSC v.1929 64 bit (AMD64)] on win32
Type "help", "copyright", "credits" or "license" for more information.
>>> import dmPython
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
ImportError: DLL load failed while importing dmPython: 找不到指定的模块。
```

【问题解决】

Python 3.8 变更了 Windows 下动态链接库（DLL）的加载规则，新的规则提高了安全性，默认情况下仅能从可信的位置（Trusted Locations）加载 DLL 依赖，一定程度上避免诸如 DLL 劫持之类的安全风险。因此需要搜索路径并把 dpi 文件复制到指定目录下。

### Non-ASCII character 'xe5' in file

【问题描述】

编译 Python 程序时，出现错误如下：

```
SyntaxError: Non-ASCII character '\xe5' in file kNN.py on line 24, but no encoding declared; see http://python.org/dev/peps/pep-0263/ for details
```

【问题解决】

Python 默认是以 ASCII 作为编码方式的，如果在自己的 Python 源码中包含了中文（或者其他非英语系的语言），此时即使你把自己编写的 Python 源文件以 UTF-8 格式保存了，这依然是不行的，需要在报错源码的第一行加上以下代码，则指定文件的编码格式是 utf-8。

```
# -*- coding: UTF-8 -*-
```

### 无法解析的外部符号错误

【问题描述】

报错信息如下图：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405211708351LRWAAGY30P7U7IU5A)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps1.jpg)

【问题解决】

考虑环境变量是否配置正确，以及 python 源码的位数和数据库软件的位数（32 或 64 位）是否一致。

### windows 10/11 及 server 2016 版本安装 dmPython 报错

【问题描述】

windows 10/11 及 server 2016 版本测试中安装 dmPython 驱动时会报错：

```
error: Microsoft Visual C++ 14.0 is required. Get it with "Build Tools for Visual Studio":https://visualstudio.microsoft.com/downloads/Tools
```

【问题解决】

通过 [http://go.microsoft.com/fwlink/?LinkId=691126](http://go.microsoft.com/fwlink/?LinkId=691126) ，下载 vc++ build tools 工具，选择默认安装：

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521171029TU1TC88YID80QRSP7N)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps2.jpg)

### 无法识别 django-admin 命令

【问题描述】

报错信息如下图：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522093141Y18YUWFQRNV76BL6VA)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps3.jpg)

【问题解决】

需要包含 django-admin 的全路径，通常在 python 目录的 scripts 下有 django-admin.exe。

### SyntaxError: (unicode error) 'utf-8' codec can't decode byte 0xb2 in position 0: invalid start byte

【问题描述】

报错信息如下图：

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522093413Q3V671EJ5S1OTLSNAV)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps4.jpg)

【问题解决】

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522094316713DTAUEXHUV9DUFV9)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps5.jpg)

代码中文编码和操作系统编码不一致，如上图所示，把编码由 utf-8 修改为 gbk。

### 'int' object is not subscriptable

【问题描述】

如下图所示：

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522094342K13MVYZG74FD4VEN12)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps6.jpg)

【问题解决】

Dmpython 版本和 SQLAlchemy、sqlalchemy-dm 版本不匹配，更新到合适版本，例如 dmPython2.4.5、sqlalchemy2.0.29、sqlalchemy-dm2.0.0。

### error：Unable to find vcvarsall.bat

【问题解决】

进入 python 安装目录的 lib/distutils，找到文件 msvc9compiler.py，在文件中找到 `vc_env = query_vcvarsall(VSERSION,plat_spec) `，根据使用本机的 VS 版本号进行修改，如：对应安装目录 C:\Program Files\Microsoft Visual Studio 10.0，改为：`vc_env = query_vcvarsall(10,plat_spec) `。

可参考[ Python 连接达梦数据库](https://eco.dameng.com/community/article/45246b17939be87311deaac7e4de8b23)。

### 导入 dmpython 模块时报错找不到指定的模块

【问题描述】

导入 dmpython 模块时报错，代码及报错信息如下：

```
import dmPython

Traceback (most recent call last):

File "<stdin>", line 1, in <module>

ImportError: DLL load failed: 找不到指定的模块
```

【问题分析】

因为 dmPython 找不到 DPI 动态库，需要到 DPI 所在目录执行或者配置环境变量指向 DPI 所在目录。

【问题解决】

右键【我的电脑】-【属性】，打开的界面中点击【高级系统设置】，添加环境变量。

变量名：PATH 变量值：DPI 所在路径。

如下图所示：

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405221002241LCIGPHT1LYRV0NASR)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps7.jpg)

然后重新启动 python，输入 import dmPython 就可以了。

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522100240FAET8B3A45UDI0Q6C9)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps8.jpg)

### DMPython 驱动在哪里可以找到

【问题解决】

进入 [Python 数据库接口](https://eco.dameng.com/document/dm/zh-cn/app-dev/python-python)下载。

### DMPython 的安装教程

【问题解决】

请参考 [Python 数据库接口](#%E5%AE%89%E8%A3%85-Python) 。

### DMPython 连接 DM，报错 ImportError: No module named dmPython

【问题解决】

若用户已经找到对应的 dmPython 驱动，可以将 Python 路径添加到环境变量，把 dmPython.pyd 文件路径添加到环境变量，在环境变量中添加变量名：

```
 PYTHONPATH:  F:\dmdbms\drivers\python\dmPython\build\lib.win-amd64-2.7 
```

若没有对应驱动，可以进入 [Python 数据库接口](https://eco.dameng.com/document/dm/zh-cn/app-dev/python-python)下载。

### DMPython 安装报错：不是有效的 Win32 应用程序

【问题描述】

报错信息如下：

```
importError: DLL load failed: %1 不是有效的 Win32 应用程序。
```

【问题解决】

本机的达梦数据库是 32 位的，需重新要安装 64 位的数据库。

### DMPython 安装报错：依赖 VC++14.0

【问题解决】

C++ 相关组件不完善导致的报错，安装相应依赖即可。

### cannot locate an Dameng software

【问题描述】

Python 连接达梦数据库，根据安装提示，切换到 root 用户，报错：

```
raise DistutilsSetupError("cannot locate an Dameng software " /distutils.errors.DistutilsSetupError: cannot locate an Dameng software installation
```

【问题解决】

需要依赖 DM 的环境，在 root 用户下配置 DM 的环境变量。

```
'# User specific environment and startup programs'

PATH=$PATH:$HOME/bin
export PATH
export PATH="/dm/dmdbms/bin:$PATH"

export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/dm/dmdbms/bin"
```

### ImportError: DLL load failed

【问题解决】

检查环境变量配置，加上 dmdbms\bin、dmdbms、dmdbms\drives\dpi 的环境变量。确认生效后，然后进入 dmPython 目录下执行 python setup.py install ，然后再导入驱动。

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522100853NYNFRBEQ010GN1UFXN)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps9.jpg)

导入驱动成功：

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522100900TA4DCOVXCEW12DK7QE)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps10.jpg)

### Python 数据 insert 进 DM，报错：codec can't encode character

【问题描述】

Python 达梦数据库插入特殊字符报错，提示：

```
'gbk' codec can't encode character '\ue622' in position 0: illegal multibyte sequence。
```

【问题解决】

设置连接参数 local_code。

### ImportError: libdmdpi.so: cannot open shared object file: No such file or directory

【问题描述】

导入 dmpython 驱动时，报错如下：

```
ImportError: libdmdpi.so: cannot open shared object file: No such file or directory
```

【问题解决】

将数据库的 bin 目录添加进环境变量即可。

### 匹配没有可登陆的服务器

【问题描述】

达梦数据库里有建立两个用户，这两个用户都能正常登陆数据库。在 python 里面用这两个用户去连接达梦数据库，一个用户可以，一个用户连接的时候报错：“匹配没有可登陆的服务器”。

【问题解决】

这种情况，请检查用户名是否和关键字冲突了。

### 同一个应用向 DM7.1 和 DM7.6 中插入数据，DM7.1 的库中数据正常，DM7.6 的库中中文出现乱码

【问题解决】

查看应用连接串：

```
conn = dmPython.connect(user='###', password='###', server='10.221.144.80', port=5236, autoCommit=True)
```

增加 local_code 参数后改写为：

```
properties = { 'user' : 'SYSDBA', 'password' : 'SYSDBA', 'server' : '127.0.0.1', 'port' : 5236, 'autoCommit' : True,'local_code' : 1, }
conn = dmPython.connect(**properties)
```

### Python 使用连接池 PooledDB 连接达梦数据库

【问题解决】

DBUtils 是一套用于管理数据库连接池的 Python 包，为高频高并发的数据库访问提供更好的性能，可以自动管理连接对象的创建和释放，并允许对非线程安全的数据库接口进行线程安全包装。DBUtils.PooledDB 实现了稳定、线程安全的缓存连接池。连接步骤如下：

  1. 安装 DBUtils 包。



```
pip install DBUtils==1.3
```

  2. 使用 DBUtils 的连接池 PooledDB 连接达梦数据库示例代码。



```
import dmPython
from DBUtils.PooledDB import PooledDB

pool = PooledDB(creator=dmPython, mincached=50,maxcached=100, server='192.168.201.118',  port=5236, user='SYSDBA', password='*******')

conn = pool.connection()
cur = conn.cursor()
SQL = "select username from dba_users"
r = cur.execute(SQL)
r = cur.fetchall()
cur.close()
conn.close()
```

如下图所示：

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522103349XAW9F90QY4USTFZ4CG)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps11.jpg)

### Python 运行时报错：load fun EVP_sm3_fun fail!

【问题描述】

Python 运行过程中，执行 python manager.py runserver 出现报错，如下所示：

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522104814S0IRB3AVCYEUCK9GGP)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps12.jpg)

【问题解决】

将 dmdbms/drivers/dpi 目录下的 libeay32.dll 和 ssleay32.dll 文件拷贝到 python 所在目录即可。如下所示：

![图片 12.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405221048420WRR1IUXQRSKAO3WDD)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps13.png)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps14.jpg)

### 导入 dmpython 报错 ：PyUnicodeUCS2_Format

【问题描述】

使用服务器自带的 python 环境编译 dm 驱动后报错：PyUnicodeUCS2_Format 。

![图片 13.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405221052288PEV1WXJGIBOFGOT1T)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps15.jpg)

【问题分析】

通过错误信息分析，unicode 编译环境和运行环境不一致导致报错：

```
undefiend symbol: PyUnicodeUCS2_Format/PyUnicodeUCS4_Format。
```

【问题解决】

前提：确保 python 和 dmpython 的 unicode 字符集一致。

使用 unicode 为 ucs4 的 python 环境重新编译 dmPython 驱动。

![图片 14.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522105248AJHCWAJGI9LP9ZGXZK)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps16.jpg)

将 python 的 unicode 重新编译为 ucs2 。

```
./configure --enable-shared --enable-unicode=ucs2
make && make install
```

如果报错："undefiend symbol: PyUnicodeUCS4_Format" 可以重新将 unicode 为 ucs2 的 python 环境编译成 unicode 为 ucs4 的 python 环境。

```
./configure --enable-shared --enable-unicode=ucs4
make && make install
```

### SqlAlchemy_dm 插入数据报错：TypeError: 'NoneType' object is not subscriptable

【问题描述】

SqlAlchemy_dm 插入数据时报错：

```
TypeError: 'NoneType' object is not subscriptable
或者 
ValueError: invalid literal for int() with base 10: 'AAAAAAAAAAAAAAAAAD'？
```

【问题分析】

新版本数据库的 rowid 是非纯整数，在 SqlAlchemy_dm 源码中可以看到调用 _setup_ins_pk_from_lastrowid 函数时 self.get_lastrowid() 已经是非整数类型，而后  _DMInteger 函数将该 lastrowid 强转为 int ，所以导致报错。

![图片 15.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522111912BXL2D15J81RFG6DKPP)

![图片 16.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522111919MO9WB34B6GX8YNHRSD)

【问题解决】

将 dmPython 更新到 2.4.5 版本，并将 SqlAlchemy_dm 驱动更新到最新版本。

### 安装 dmPython rpm 包成功，但程序使用 import dmPython 报错

【问题描述】

安装 dmPython rpm 包成功，但是程序使用 import dmPython 时报如下错误：

```
Traceback (most recent call last)
file"<stdin>",line 1 ,in<module>
ImportError :/lib64/libc.so.6:version `GLIBC_2.14` not found (required by /usr/local/lib/python3.7/site-packages/dmPython=2.4.5-py3.7-linux-x86_64.egg)
```

【问题分析】

该问题主要是由于打包 dmPython 环境的 glibc 版本与安装环境的 glibc 版本不匹配导致。

【问题解决】

使用同版本的 glibc 版本重新打包 dmPython 环境即可解决。

### R 语言连接 DM 的主备集群如何以服务名的形式配置高可用？

【问题描述】

R 语言连接 DM 的主备集群如何以服务名的形式配置高可用？

【问题解决】

R 语言连接 DM 数据库有以下两种写法，如果想要连接高可用只能使用第二种写法。

第一种写法：

```
drv <- dbDriver("Dm")

connect.string <- paste("(DESCRIPTION=(ADDRESS=(PROTOCOL=tcp)(HOST=", dbHost, ")(PORT= 5236))(CONNECT_DATA=(SERVICE_NAME=", dbServerName, ")))", sep = "")

dm8Con <- dbConnect(drv,username = dbUserName, password = dbPassword,dbname = connect.string)
```

第二种写法（注：dbname 属性为 dm_svc.conf 中的服务名）:

```
dmCon <- dbConnect(drv,username = "SYSDBA", password = "123456",dbname = "DM")

#在 /etc/dm_svc.conf 中填写主备的 IP 及端口信息：

TIME_ZONE=(480)
LANGUAGE=(cn)

DM=(10.130.250.9:5236, 10.130.250.10:5236)
[DM]
LOGIN_MODE=(1)
SWITCH_TIME=(300)
SWITCH_INTERVAL=(200)
```

### 使用 django 框架项目启动报错： mportError: cannot import name 'force_text' from 'django.utils.encoding'

【问题描述】

django 框架引入 dmpython+django-dmpython 后项目启动，报错如下：

```
ImportError: cannot import name 'force_text' from 'django.utils.encoding'。
```

![图片 17.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405221128041929W0N41WKG35FRXG)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps19.jpg)

【问题分析】

该问题可能是由于以下两种原因导致：

（1）django.utils.encoding 模块导入 force_text 函数，而 DM 的 dmpython 驱动使用的 Django 版本中找不到该函数。

（2）在 Django 3.0 及更高版本中，force_text 函数被移除，并用 force_str 和 force_bytes 函数替代。

【问题解决】

修改 DM 驱动中的 django_dmPython 驱动的 base.py、operations.py、utils.py 文件头部引用。

修改前 from django.utils.encoding import force_bytes, force_text 。

修改后 from django.utils.encoding import force_bytes, force_str  。

### 使用 django 框架项目启动报错：'django_dmPython' isn't an available database backend xxx

【问题描述】

django 框架引入 dmpython+django-dmpython 后项目启动，报错如下:

```
"django.core.exceptions.ImproperlyConfigured: 'django_dmPython' isn't an available database backend or 
couldn't be imported. Check the above exception. To use one of the built-in backends, use 
'django.db.backends.XXX', where XXX is one of:'mysql', 'oracle', 'postgresql', 'sqlite3'"。
```

![图片 18.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522112837ML77O9LP1K1AKPYTIU)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps20.jpg)

【问题分析】

使用 pip install 方式安装的 DM 驱动，驱动包会默认放到与 django 框架的同级目录，而项目启动后扫描的是 django.db.backends 的目录，所以会报错。

【问题解决】

可以通过以下方法尝试解决：

将安装包下的 django_dmPython 目录放到 django.db.backends 目录下。

修改 settings.py 文件中 DATABASES 的 ENGINE 属性为 django.db.backends.django_dmPython。

![图片 19.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522112849VU1VLOZP6SA9FQFCO9)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml6660\wps21.jpg)

### django 框架连接 DM 的主备集群如何以服务名的形式配置高可用？

【问题解决】

配置步骤如下：

修改 django 框架 settings.py 中 DATABASES 对象 HOST 属性即可。（这里不必纠结 HOST 以及 PORT 的指向，数据库启动时会自动扫描并替换掉端口）

```
DATABASES = {
'default': {
   'ENGINE': 'django.db.backends.dm8',
   'NAME': 'DAMENG',
   'USER': 'SYSDBA',
   'PASSWORD': 'SYSDBA',
   'OPTIONS': {'schema': 'SYSDBA'},
   'HOST': 'DM',
   # 'HOST': '10.15.1.17',
   'PORT': '5237',
}
}
```

在 /etc/dm_svc.conf 中填写主备的 IP 及端口信息。

```
TIME_ZONE=(480)
LANGUAGE=(cn)

DM=(10.130.250.9:5236, 10.130.250.10:5236)
[DM]
LOGIN_MODE=(1)
SWITCH_TIME=(300)
SWITCH_INTERVAL=(200)
```

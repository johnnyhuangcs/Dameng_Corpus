

# SQLAlchemy_dm方言包

## 5.1 简介及安装

SQLAlchemy 是 python 下的开源软件，提供了 SQL 工具包及对象关系映射（ORM）工具，让应用程序开发人员使用上 SQL 的强大功能和灵活性。SQLAlchemy_dm 方言包是 DM 提供用于 SQLAlchemy 连接 DM 数据库的方法。

1.SQLAlchemy 软件的安装。例如：SQLAlchemy-1.1.10.win-amd64-py2.7.exe。

2.sqlalchemy_dm 方言包的软件生成与安装。

sqlalchemy_dm 可以运行在任何安装了 Python 的平台上。生成工具 setup.py 位于drivers\python\sqlalchemy目录中。

可以使用如下命令很方便地在 Windows 和 Linux 操作系统下编译并安装 sqlalchemy_dm:

```
//进入到setup.py所在的源码目录，执行以下命令：

python setup.py install
```

也可以先生成安装文件再进行安装，不同平台生成安装包的命令如下：

```
Windows：python setup.py bdist_wininst

Linux：python setup.py bdist_rpm   
```

生成之后的安装包（例如 sqlalchemy_dm-1.1.10.win-amd64.exe）位于 drivers\python\sqlalchemy\dist 目录中。点击安装包安装即可。

## 5.2 engine 的配置

create_engine()返回一个数据库引擎，下面是 DM 数据库的配置方法。

```
from sqlalchemy import create_engine

engine =
create_engine('dm://SYSDBA:*****@localhost:5236/',connect_args={'local_code':1,'connection_timeout':15})

	或

engine =
create_engine('dm+dmPython://SYSDBA:*****@localhost:5236/',connect_args={'local_code':1,'connection_timeout':15})
```

其中，connect_args 是字典选项，只要在 connect_args 中以字典对象的方式配置 dmPython.connect 支持的选项即可。可以包含多个字典对象，用逗号分隔。dmPython.connect 请参考 3.1.1.1 dmPython.connect。其他配置参考 SQLAlchemy 官网 [http://docs.sqlalchemy.org](http://docs.sqlalchemy.org) 文档。

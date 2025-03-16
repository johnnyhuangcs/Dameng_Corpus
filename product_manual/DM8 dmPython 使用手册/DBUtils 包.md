

# DBUtils 包

## 6.1 简介

DBUtils 是一套用于管理数据库连接池的 Python 包，可以自动管理连接对象的创建和释放。并允许对非线程安全的数据库接口进行线程安全包装。DBUtils 可为高频度高并发的数据库访问提供更好的性能。

DBUtils 提供两种外部接口：PersistentDB 和 PooledDB。

PersistentDB 提供线程专用的数据库连接，并自动管理连接。为每个线程创建一个连接，线程即使调用了 close 方法也不会关闭连接，只是把连接重新放到连接池，供自己线程再次使用。当线程终止时，连接自动关闭。

PooledDB 提供线程间可共享的数据库连接，并自动管理连接。创建一批连接并放到连接池中，供所有线程共享使用。

PersistentDB 和 PooledDB 都是为了重用数据库连接来提高性能，并保持数据库的稳定性。PersistentDB 将会保持一定数量的连接供频繁使用，在这种情况下总是保持固定数量的连接。如果应用程序频繁的启动和关闭线程，最好使用 PooledDB。

## 6.2 使用方法

dmpython 支持 DBUtils1.3-DBUtils3.02 之间的版本。其中，DBUtils1.3-DBUtils2.0 版本和 DBUtils2.0 以上-DBUtils3.02 版本的导入模块用法略有不同，具体的细节在下面的章节中会进行介绍。

DBUtils 的用法分为两步：一是创建连接池；二是从连接池中获取连接。

下面对 PersistentDB 和 PooledDB 分别进行介绍。

### 6.2.1 PersistentDB

一 使用 PersistentDB 创建连接池。

```
import dmPython

#如果是DBUtils 1.3 -DBUtils 2.0版本使用如下语句导入模块

from DBUtils.PersistentDB import PersistentDB

#如果是DBUtils2.0以上版本，则将上面一句替换为下面的语句

#from dbutils.persistent_db import PersistentDB

POOL = PersistentDB(

  creator=dmPython, # 使用链接数据库的模块

  maxusage=None, # 一个链接最多被重复使用的次数，None表示无限制

  setsession=[], # 开始会话前执行的命令列表。如：["set datestyle to ...", "set time zone ..."]

  ping=0,

  # ping DM服务端，检查是否服务可用。# 如：0 = None = never, 1 = default = whenever it is requested, 2 = when a cursor is created, 7 = always

  closeable=False,

  # 如果为False时， conn.close() 实际上被忽略，供下次使用，再线程关闭时，才会自动关闭链接。如果为True时， conn.close()则关闭链接，那么再次调用pool.connection时就会报错，因为已经真的关闭了连接（pool.steady_connection()可以获取一个新的链接）

  threadlocal=None, # 表示thread-local数据的类。  

host='127.0.0.1', # 主机号

  port=5236, # 端口号

  user='SYSDBA', # 用户名

  password='*****', # 密码

)
```

二 从连接池获取连接的方法。

获取连接的方法为 conn = POOL.connection()。

获取到的连接的使用方法可以参考 dmPython 使用手册中 Connection 接口的使用方法，具体操作实例如下：

```
def func():

  conn = POOL.connection()

  cursor = conn.cursor()

  cursor.execute('select * from tb1')

  result = cursor.fetchall()

  cursor.close()

  conn.close()
```

### 6.2.2 PooledDB

一 使用 PooledDB 创建连接池。

```
import dmPython

#如果是DBUtils 1.3 -DBUtils 2.0版本使用如下语句导入模块

from DBUtils.PooledDB import PooledDB

#如果是DBUtils2.0以上版本，则将上面一句替换为下面的语句

#from dbutils.pooled_db import PooledDB

POOL = PooledDB(

  creator=dmPython, # 使用链接数据库的模块

  maxconnections=6, # 连接池允许的最大连接数，0和None表示不限制连接数

  mincached=2, # 初始化时，链接池中至少创建的空闲的链接，0表示不创建

  maxcached=5, # 链接池中最多闲置的链接，0和None不限制

  maxshared=3, # 链接池中最多共享的链接数量，0和None表示全部共享      blocking=True, # 连接池中如果没有可用连接后，是否阻塞等待。True，等待；False，不等待然后报错

  maxusage=None, # 一个链接最多被重复使用的次数，None表示无限制

  setsession=[], # 开始会话前执行的命令列表。如：["set datestyle to ...", "set time zone ..."]

  ping=0,

  # pingDM服务端，检查是否服务可用。# 如：0 = None = never, 1 = default = whenever it is requested, 2 = when a cursor is created, 7 = always

  host='127.0.0.1', # 主机号

  port=5236, # 端口号

  user='SYSDBA', # 用户名

  password='*****', # 密码

)
```

二 从连接池获取连接的方法。

![](media/bottom.jpg)

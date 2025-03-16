

# SQLAlchemy 框架

## 一、前言

SQLAlchemy 是 Python 社区中最广泛使用的 ORM 框架之一，该框架建立在数据库 DBAPI 之上，使用关系对象映射进行数据库操作，即将对象转换成 SQL，然后使用数据库 DBAPI 执行 SQL 并获取执行结果。SQLAlchemy 操纵的是 Python 对象而不是 SQL 查询，也就是在代码层面考虑的是对象，而不是 SQL，体现的是一种程序化思维，这样使得 Python 程序更加简洁易读。

## 二、开发环境准备

SQLAlchemy 使用方言 Dialect 与各种类型的数据库进行通信，每种数据库都有对应的 DBAPI，所有方言都要求安装对应的 DBAPI 驱动程序。SQLAlchemy 根据配置的方言 Dialect 不同调用不同的数据库 DBAPI，从而实现对数据库的操作。

达梦数据库的方言是 sqlalchemy_dm，使用 dmPython 作为默认的 DBAPI。因此使 SQLAlchem 访问达梦数据库时需要先安装 dmPython 和 sqlalchemy_dm，此外还需要安装 SQLAlchemy。

安装完 DM 数据库软件后，在安装路径下的 drivers 目录下的 python 目录中，可以找到 dmPython 和 sqlalchemy 方言的驱动源码，由于提供的是源码，需要自己编译安装。

|**名称**|**版本**|
|--|--|
|开发平台|Windows|
|SQLAlchemy|1.3.23|
|sqlalchemy_dm|1.1.10|


### 2.1 安装 SQLAlchemy

在终端执行命令：

```
pip install SQLAlchemy==1.3.23
```

如果安装失败，可使用镜像安装，命令如下：

```
pip install SQLAlchemy==1.3.23 -i http://pypi.doubanio.com/simple/ --trusted-host pypi.doubanio.com
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216215634N5E7N228F6AU6E9BHV)

> **注意**
>
> 需要SQLAlchemy版本和SQLAlchemy_dm版本对应：  
>
>
> 1.SQLAlchemy_dm 1.1.10版本对应SQLAlchemy1.3.X  
>  2.SQLAlchemy_dm 1.4.39版本对应SQLAlchemy1.4.X  
>  3.SQLAlchemy_dm 2.0.0版本对应SQLAlchemy2.0.X



### 2.2 编译安装达梦数据库的 sqlalchemy 方言

  1. 到 $DM_HOME\drivers\python\sqlalchemy 目录下执行命令手动编译安装：python setup.py install。如图所示则代表安装成功。



```
cd D:\dmdbms\drivers\python\sqlalchemy
python setup.py install
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228145950DQ1LWN3GYGRW7B118B)

  2. 使用 pip list 命令查看是否安装成功。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216215809P5EZ3FLVL2JDULLGMZ)

## 三、开发示例

### 3.1 基础操作示例

该代码实现增、删、改、查四个基本操作。

  1. 创建数据库 PRODUCT 表。



```
CREATE TABLE "SYSDBA"."PRODUCT"
(
"PRODUCTID" INT IDENTITY(1, 1) NOT NULL,
"NAME" VARCHAR(100) NOT NULL,
"AUTHOR" VARCHAR(25) NOT NULL,
"PUBLISHER" VARCHAR(50) NOT NULL,
"PUBLISHTIME" DATE NOT NULL,
"PRODUCTNO" VARCHAR(25) NOT NULL,
"SATETYSTOCKLEVEL" SMALLINT NOT NULL,
"ORIGINALPRICE" DEC(19,4) NOT NULL,
"NOWPRICE" DEC(19,4) NOT NULL,
"DISCOUNT" DECIMAL(2,1) NOT NULL,
"DESCRIPTION" TEXT,
"TYPE" VARCHAR(5),
"PAPERTOTAL" INT,
"WORDTOTAL" INT,
"SELLSTARTTIME" DATE NOT NULL,
"SELLENDTIME" DATE,
NOT CLUSTER PRIMARY KEY("PRODUCTID"),
UNIQUE("PRODUCTNO")) STORAGE(ON "BOOKSHOP", CLUSTERBTR);
```

  2. 创建 Product.py 文件。



```
# coding: utf-8
from sqlalchemy import Column, Integer, String,Date,Numeric,Text
from sqlalchemy.ext.declarative import declarative_base
# 创建对象的基类:
Base = declarative_base()

class Product(Base):
    # 表的名字:
    __tablename__ = 'product'
    # 表的结构:
    PRODUCTID = Column(Integer,autoincrement=True, primary_key=True)
    NAME = Column(String(100))
    AUTHOR = Column(String(25))
    PUBLISHER = Column(String(50))
    PUBLISHTIME = Column(Date)
    PRODUCTNO = Column(String(25))
    SATETYSTOCKLEVEL = Column(Integer)
    ORIGINALPRICE = Column(Numeric(19,4))
    NOWPRICE = Column(Numeric(19,4))
    DISCOUNT = Column(Numeric(2,1))
    DESCRIPTION = Column(Text)
    TYPE = Column(String(5))
    PAPERTOTAL = Column(Integer)
    WORDTOTAL = Column(Integer)
    SELLSTARTTIME = Column(Date)
    SELLENDTIME = Column(Date)
```

  3. 创建 dm_sqlalchemy.py 文件。



```
# coding: utf-8
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker
from Product import Product
def main():
    #dialect 是SQLAlchemy用来与各种类型的DBAPI和数据库通信的系统。
    conn_url = 'dm+dmPython://SYSDBA: *****@192.168.201.118:5236'
    #创建Engine对象
    engine = create_engine(conn_url)
    #创建DBSession对象
    DBSession = sessionmaker(bind=engine)

    fun_select_all(DBSession)
    # 插入
    fun_insert(DBSession)
    fun_select_all(DBSession)
    # 更新
    fun_update(DBSession)
    fun_select_all(DBSession)
    # 删除
    fun_delete(DBSession)
    fun_select_all(DBSession)

def fun_select_all(DBSession):
    # 创建Session
    session = DBSession()
    # 查询所有的
    list_product = session.query(Product).all()
    print('查询所有结果：')
    for product in list_product:
        print(product.NAME, product.AUTHOR, product.PUBLISHER )
    print('')
    session.close()

def fun_insert(DBSession):
    # 创建Session
    session = DBSession()
    new_product = Product()
    new_product.NAME = '水浒传'
    new_product.AUTHOR = '施耐庵，罗贯中'
    new_product.PUBLISHER = '中华书局'
    new_product.PUBLISHTIME = '2005-4-1'
    new_product.PRODUCTNO = '9787101046137'
    new_product.SATETYSTOCKLEVEL = '10'
    new_product.ORIGINALPRICE = '19'
    new_product.NOWPRICE = '14.3'
    new_product.DISCOUNT = '7.5'
    new_product.DESCRIPTION = '''  《水浒传》是宋江起义故事在民间长期流传基础上产生出来的，吸收了民间文学的营养。'''
    new_product.PHOTO = ''
    new_product.TYPE = '16'
    new_product.PAPERTOTAL = '922'
    new_product.WORDTOTAL = '912000'
    new_product.SELLSTARTTIME = '2006-03-20'
    new_product.SELLENDTIME = ''

    session.add(new_product)
    session.commit()
    print('插入成功')
    session.close()

def fun_update(DBSession):
    # 创建Session
    session = DBSession()
    product = session.query(Product).filter(Product.NAME == '水浒传').one()
    product.NAME = '水浒'
    session.commit()
    print('更新成功')
    session.close()

def fun_delete(DBSession):
    # 创建Session
    session = DBSession()
    session.query(Product).filter(Product.NAME == '水浒').delete()
    session.commit()
    print('删除成功')
    session.close()

if __name__ == '__main__':
main()
```

  4. 运行结果如下：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212162200003I2RNTTUA9NR5FVSUL)

## 四、参考

  1. 示例代码下载：[SQLAlchemy_Code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221223102449X4O5XHXSW4OR04U7VL)。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。



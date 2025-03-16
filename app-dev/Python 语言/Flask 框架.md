

# Flask 框架

## 一、前言

Flask 是一个轻量级的可定制框架，使用 Python 语言编写，较其他同类型框架更为灵活、轻便、安全且容易上手。它可以很好地结合 MVC 模式进行开发，开发人员分工合作，小型团队在短时间内就可以完成功能丰富的中小型网站或 Web 服务的实现。另外，Flask 还有很强的定制性，用户可以根据自己的需求来添加相应的功能，在保持核心功能简单的同时实现功能的丰富与扩展，其强大的插件库可以让用户实现个性化的网站定制，开发出功能强大的网站。Flask-SQLAlchemy 集成了 SQLAlchemy，它简化了连接数据库服务器、管理数据库操作会话等各类工作，让 Flask 中的数据处理体验变得更加轻松。

## 二、环境准备

|**名称**|**版本**|
|--|--|
|开发平台|Linux|
|数据库版本|DM8|
|PyCharm|2017.3.7|
|Python|3.7|
|flask|2.2.5|
|flask-sqlalchemy|3.0.5|
|dmPython|2.3|
|sqlalchemy-dm|2.0.0|


> **注意**
>
> 需要 SQLAlchemy 版本和 SQLAlchemy_dm 版本对应。



|**SQLAlchemy_dm 版本**|**版本**|
|----------------|----------------|
|1.1.10|1.3.X|
|1.4.39|1.4.X|
|2.0.0|2.0.X|


### 2.1 数据库环境准备

请参考[《数据库环境准备》](https://eco.dameng.com/vip/details/74b567330c377336dc50e010c75317c9)章节。

### 2.2 Flask 相关依赖包安装

安装步骤如下，如需下载最新版本，删除安装命令中相应版本号即可，例如本章节安装 flask 步骤：

```
pip install flask -i http://pypi.doubanio.com/simple/ --trusted-host pypi.doubanio.com 
```

1．安装 flask 。

```
pip install flask==3.0.2 -i http://pypi.doubanio.com/simple/ --trusted-host pypi.doubanio.com
```

2．安装 flask-sqlalchemy 。

```
pip install flask-sqlalchemy==3.1.1 -i http://pypi.doubanio.com/simple/ --trusted-host pypi.doubanio.com
```

3．安装 dmPython 。

```
/home/dmdba/dmdbms/drivers/python/dmPython
python setup.py install
```

4．安装 sqlalchemy-dm 。

```
/home/dmdba/dmdbms/drivers/python/sqlalchemy2.0.0
python setup.py install
```

5．环境检验。

```
pip list
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240423113443OS6C5YGSNJTHU6POU5)

2.3 创建测试表。

```
create table "SYSDBA"."FLASK"
(
	"PRODUCTID" INTEGER not null ,
	"NAME" VARCHAR(50),
	"AUTHOR" VARCHAR(50),
	"PUBLISHER" VARCHAR(50),
	"PUBLISHTIME" DATE,
	"PRODUCTNO" VARCHAR(50),
	"SATETYSTOCKLEVEL" INTEGER,
	"ORIGINALPRICE" DECIMAL(22, 6),
	"NOWPRICE" DECIMAL(22, 6),
	"DISCOUNT" DECIMAL(22, 6),
	"DESCRIPTION" TEXT,
	"PHOTO" BLOB,
	"TYPE" VARCHAR(50),
	"PAPERTOTAL" INTEGER,
	"WORDTOTAL" INTEGER,
	"SELLSTARTTIME" DATE,
	"SELLENDTIME" DATE,
	primary key("PRODUCTID")
)
storage(initial 1, next 1, minextents 1, fillfactor 0)
;

```

## 三、Flask 应用开发

### 3.1 新建 Flask 项目

使用 PyCharm 工具新建 Flask 项目。

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240418152332HBMB9IJCESEVF0OL9X)

### 3.2 初始化 Flask 和 SQLAlchemy

编辑 FlaskDm/application/_init _.py 文件。

```
# -*- coding: utf-8 -*-

from flask import Flask
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()


def init_app():
    app = Flask(__name__)

    # 解决页面中问乱码问题
    app.config['JSON_AS_ASCII'] = False
    # 设置数据库链接地址
    app.config['SQLALCHEMY_DATABASE_URI'] = "dm+dmPython://SYSDBA:*****@127.0.0.1:5236"
    # 设置显示底层执行的sql语句
    app.config['SQLALCHEMY_ECHO'] = True
    app.config['SQLALCHEMY_TRACK_MODIFICATIONS'] = True

    # 初始化组件对象，关联flask应用
    db.init_app(app)

    return app
```

### 3.3 构建模型类

编辑 FlaskDm/application/models/ProductModel.py 文件。

```
# -*- coding: utf-8 -*-


# 构建模型类
from application._init_ import db


# 构建模型类
class Product(db.Model):
    # 设置表名，表名默认为类名小写
    __tablename__ = 'FLASK'
    PRODUCTID = db.Column(db.Integer, primary_key=True, autoincrement=True)
    NAME = db.Column(db.String(100))
    AUTHOR = db.Column(db.String(25))
    PUBLISHER = db.Column(db.String(50))
    PUBLISHTIME = db.Column(db.Date)
    PRODUCTNO = db.Column(db.String(25))
    SATETYSTOCKLEVEL = db.Column(db.Integer)
    ORIGINALPRICE = db.Column(db.Numeric(19, 4))
    NOWPRICE = db.Column(db.Numeric(19, 4))
    DISCOUNT = db.Column(db.Numeric(2, 1))
    DESCRIPTION = db.Column(db.Text)
    # 图片转化为二进制数据进行存储
    PHOTO = db.Column(db.LargeBinary)
    TYPE = db.Column(db.String(5))
    PAPERTOTAL = db.Column(db.Integer)
    WORDTOTAL = db.Column(db.Integer)
    SELLSTARTTIME = db.Column(db.Date)
    SELLENDTIME = db.Column(db.Date)

    def __repr__(self):  # 自定义 交互模式 & print() 的对象打印
        return "(%d, %s, %s, %s)" % (self.PRODUCTID, self.NAME, self.AUTHOR, self.DESCRIPTION)
```

### 3.4 示例代码

编辑 FlaskDm/FlaskDm.py 文件。

```
# -*- coding: utf-8 -*-
from flask import Response, json

from application._init_ import init_app, db
from application.ProductModel import Product

app = init_app()


@app.route("/query_all")
def query_all():
    # 查询所有用户数据, 返回User列表,
    data = Product.query.all()
    response_data = []
    for item in data:
        info = dict()
        info['PRODUCTID'] = item.PRODUCTID
        info['NAME'] = item.NAME
        info['AUTHOR'] = item.AUTHOR
        info['DESCRIPTION'] = item.DESCRIPTION
        response_data.append(info)

    return Response(json.dumps(response_data), mimetype='application/json')


@app.route("/query_first")
def query_filter():
    # 查询所有用户数据, 返回User列表,
    data = Product.query.filter_by(NAME='面纱').first()
    response_data = dict()
    response_data['PRODUCTID'] = data.PRODUCTID
    response_data['NAME'] = data.NAME
    response_data['AUTHOR'] = data.AUTHOR
    response_data['DESCRIPTION'] = data.DESCRIPTION

    return Response(json.dumps(response_data), mimetype='application/json')


@app.route("/add_one")
def add_one():
    # 添加一条数据
    # 将图片转化为二进制数据进行存储
    product = Product(PRODUCTID=1,
                      NAME='万历十五年',
                      AUTHOR='黄仁宇',
                      PUBLISHER='中华书局',
                      PUBLISHTIME='1981-09-01',
                      PRODUCTNO='9787101046126',
                      SATETYSTOCKLEVEL=10, ORIGINALPRICE=39.8000, NOWPRICE=20.0000,
                      DISCOUNT=5.0,
                      DESCRIPTION='《万历十五年》以1587年为关节点，在历史的脉络中延伸，从政治、经济、军事等各个方面的历史大事与人物着手，记叙了明朝中晚期的种种社会矛盾和开始走向衰败的迹象。',
                      PHOTO=open('C:/Users/Administrator/PycharmProjects/FlaskDm/application/OIP.jpg', 'rb').read(),
                      TYPE='16', PAPERTOTAL=684, WORDTOTAL=68000,
                      SELLSTARTTIME='2006-03-20', SELLENDTIME='2023-11-1'
                      )
    db.session.add(product)
    db.session.commit()
    return Response('数据插入成功，本次插入数据的PRODUCTID为{}'.format(product.PRODUCTID), mimetype='application/json')


@app.route("/add_list")
def add_list():
    # 一次添加多条数据
    data_list = [
        Product(PRODUCTID=2,
                NAME='面纱', AUTHOR='威廉·萨默塞特·毛姆', PUBLISHER='人民文学出版社', PUBLISHTIME='2020-04-01',
                PRODUCTNO='9787101046127',
                SATETYSTOCKLEVEL=10, ORIGINALPRICE=39.8000, NOWPRICE=20.0000,
                DISCOUNT=5.0,
                DESCRIPTION='小说的故事发生在香港和一个叫“湄潭府”的地方。女主人公凯蒂·费恩因为和香港助理布政司查理通奸，被丈夫瓦尔特（细菌学家）发现后胁迫她去了霍乱横行的湄潭府，最终瓦尔特不幸染病死去，凯蒂回到香港，重投查理怀抱后羞愧不已，最终回到英国和父亲和解，并和父亲同往巴哈马群岛生活。',
                PHOTO=open('C:/Users/Administrator/PycharmProjects/FlaskDm/application/ORF.jpg', 'rb').read(),
                TYPE='16', PAPERTOTAL=684, WORDTOTAL=68000, SELLSTARTTIME='2006-03-20',
                SELLENDTIME='2023-11-1'),
        Product(PRODUCTID=3,
                NAME='乖，摸摸头', AUTHOR='大冰', PUBLISHER='湖南文艺出版社', PUBLISHTIME='2014-10-01',
                PRODUCTNO='9787101046129',
                SATETYSTOCKLEVEL=10, ORIGINALPRICE=39.8000, NOWPRICE=20.0000,
                DISCOUNT=5.0,
                PHOTO=open('C:/Users/Administrator/PycharmProjects/FlaskDm/application/OWW.jpg', 'rb').read(),
                DESCRIPTION='此书记录了大冰十余年的江湖游历，以及他和他朋友们的爱与温暖的传奇故事。',
                TYPE='16', PAPERTOTAL=684, WORDTOTAL=68000, SELLSTARTTIME='2006-03-20',
                SELLENDTIME='2023-11-1')
    ]

    db.session.add_all(data_list)
    db.session.commit()
    return Response('数据插入成功，本次共插入{}条数据'.format(len(data_list)))


@app.route("/updata_info")
def updata_info():
    # 直接根据条件修改
    Product.query.filter(Product.PRODUCTID == 2).update({Product.NAME: '面纱(中文)'})
    db.session.commit()

    # 查询修改后的值
    data = Product.query.filter_by(PRODUCTID=2).first()
    response_data = dict()
    response_data['PRODUCTID'] = data.PRODUCTID
    response_data['NAME'] = data.NAME

    return Response(json.dumps(response_data), mimetype='application/json')


@app.route("/delete_info")
def delete_info():
    # 直接根据条件修改
    Product.query.filter(Product.PRODUCTID == 1).delete()
    db.session.commit()

    # 查询修改后的值
    t_count = Product.query.count()

    return Response('数据删除成功！目前表中还有{}条数据！'.format(t_count))

@app.route("/")
def HELLO_DM():
    return 'HELLO DM'

if __name__ == '__main__':
    app.run(host="127.0.0.1", port=5000, debug=True)
```

### 3.5 运行代码

  1. 启动应用。



![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202404181533076KMEWN5DAQGXMUNUI3)

  2. 代码运行结果。



（1）查询所有数据。

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240418153727H8E8P123IQIJCOBZN4)

（2）查询指定数据。

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240418153827459PNC37QZ30U1EIN1)

（3）插入一条数据。

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240418153843FF5RK9P43CFMYJJKWK)

（4）插入多条数据。

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240418153856PJJX2A6LCN4A2YD1I7)

![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024041815391491JKEK4A4RC786BAIJ)

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240418153925GN2AJ4KU5UA3F9GYX0)

## 四、常见问题

### 4.1 启动程序提示：AttributeError: module 'sqlalchemy.util' has no attribute 'py2k' 。

【问题描述】：启动程序提示：AttributeError: module 'sqlalchemy.util' has no attribute 'py2k' 。

【解决方法】：sqlalchemy-dm 版本过低，安装更高版本的 sqlalchemy-dm 。

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml22080\wps3.jpg)

### 4.2 其他问题

其他更多 Flask 相关的常见问题请参考：

[https://eco.dameng.com/document/dm/zh-cn/faq/faq-python.html](https://eco.dameng.com/document/dm/zh-cn/faq/faq-python.html) 。

## 五、参考

1．示例代码位于下方，名称为 [FlaskDm.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240418154213U6YUHGRNAUTDBAZFUE) 。

2．以上文档内容参考过程中遇到任何问题，可到[达梦技术社区](https://eco.dameng.com/community/question/)提问交流。

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml22080\wps4.png)

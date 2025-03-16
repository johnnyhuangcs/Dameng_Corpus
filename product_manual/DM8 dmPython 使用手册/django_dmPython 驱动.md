

# django_dmPython 驱动

## 4.1 简介及安装

Django 是基于 Python 的 Web 应用程序框架，django_dmPython 是 DM 提供的 Django 连接 DM 数据库的驱动，两者的版本对应关系如下：

表4.1 django_dmPython版本对应表

|**django_dmPython 版本**|**Django 版本**|
|------------------|------------------|
|django_dmPython2.x.x|Django1.9、Django1.10、Django1.11、Django2.0、Django2.1、Django2.2、Django3.0|
|django_dmPython3.x.x|Django3.1、Django3.2、Django4.0、Django4.1|


django_dmPython 可以运行在任何安装了 python 的平台上，可以使用安装包安装，也可以直接用源码安装。

可以使用如下命令很方便地在 Windows 和 Linux 操作系统下编译并安装 django_dmPython :

```
//进入到setup.py所在的源码目录，执行以下命令：

python setup.py install
```

也可以先生成安装文件再进行安装。

在 Windows 操作系统下生成 exe 文件的操作如下，之后只需要直接执行 exe 文件即可安装：

```
//进入到setup.py所在的源码目录，执行以下命令：

python setup.py bdist_wininst
```

在 Linux 操作系统下使用 rpm 包安装 dmPython，生成 rpm 包的操作如下所示：

```
//进入到setup.py所在的源码目录，执行以下命令：

python setup.py bdist_rpm
```

安装和卸载命令参考如下：

```
安装：rpm -ivh django_dmPython-1.0-1.noarch.rpm

卸载：rpm -e django_dmPython-1.0-1.noarch.rpm
```

## 4.2 举例说明

### 4.2.1 创建 Django 项目

进入创建项目所在目录，例如 D:\django_dmPython，在该目录下使用如下命令创建 mysite 项目：

```
django-admin startproject mysite
```

Django 将会创建一个 mysite 目录，该目录包含 mysite 文件夹与 manage.py 文件。

### 4.2.2 数据库配置

Django 配置数据库默认为 sqlite3，这是一个小型数据库。要连接 DM 数据库，需修改 settings.py 中的 DATABASES 元组。配置方法如下：

```
DATABASES = {  

	'default': {  

		'ENGINE': 'django_dmPython',
  
		'NAME': 'DAMENG',  

		'USER': 'SYSDBA',  

		'PASSWORD': '*****',  

		'HOST': 'localhost', 
  
		'PORT': '5236',  

		'OPTIONS': {'local_code':1,'connection_timeout':5}
  
	}  

}
```

OPTIONS：是各个驱动都支持的选项，只要在 OPTIONS 中以字典对象的方式配置 dmPython.connect 支持的选项即可，例如：'local_code':1。可以包含多个字典对象，用逗号分隔。dmPython.connect 请参考 [3.1.1.1 dmPython.connect](https://eco.dameng.com/document/dm/zh-cn/pm/dmpython-interface.html#3.1.1.1%20dmPython.connect)

### 4.2.3 创建 dm 应用

在 Django 中，项目是一个网站使用的配置和应用的集合，一个项目可以包含很多个应用。例如，使用如下命令创建一个 dm 应用：

```
cd mysite
python manage.py startapp dm
```

### 4.2.4 创建模型

编辑 dm\models.py 文件。

```
from django.db import models

# Create your models here.
class Book(models.Model):
    class Meta:
        db_table = 'PRODUCT'
    PRODUCTID = models.AutoField(primary_key=True,  db_column='PRODUCTID')
    NAME = models.CharField(max_length=100, null=True)
    AUTHOR = models.CharField(max_length=25, null=True)
    PUBLISHER = models.CharField(max_length=50, null=True)
    PUBLISHTIME = models.DateField(null=True)
    PRODUCTNO = models.CharField(max_length=25, null=True)
    SATETYSTOCKLEVEL = models.IntegerField(null=True)
    ORIGINALPRICE = models.DecimalField(max_digits=5, decimal_places=2, null=True)
    NOWPRICE = models.DecimalField(max_digits=10, decimal_places=4, null=True)
    DISCOUNT = models.DecimalField(max_digits=2, decimal_places=1, null=True)
    DESCRIPTION = models.TextField(null=True)
    TYPE = models.CharField(max_length=5, null=True)
    PAPERTOTAL = models.IntegerField(null=True)
    WORDTOTAL = models.IntegerField(null=True)
    SELLSTARTTIME = models.DateField(null=True)
    SELLENDTIME = models.DateField( null=True)
```

同时需要在 settings.py 文件中的 INSTALLED_APPS 中添加设置'dm.apps.DmConfig'，将 dm 应用安装到 Django 项目里。

### 4.2.5 进行数据迁移

在 D:\django_dmPython\mysite 目录下打开命令行工具，执行以下命令，为模型的改变生成迁移文件：

```
python manage.py makemigrations dm
python manage.py migrate
```

### 4.2.6 对数据库进行操作

（1）在 dm 目录中新建一个 services.py 文件，在该文件中实现增、删、改、查四个基本操作。

```
from .models import Book
#查询操作
def fun_get_books():
    books = Book.objects.all() #返回QuerySet
    total_rows=books.count()
    print('查询到'+str(total_rows)+'条记录')
    for book in books:
        print(book.NAME, book.AUTHOR, book.PUBLISHER)
    print('')
#增加操作
def fun_insert_book():
    NewBook = Book()
    NewBook.NAME = '水浒传'
    NewBook.AUTHOR = '施耐庵，罗贯中'
    NewBook.PUBLISHER = '中华书局'
    NewBook.PUBLISHTIME = '2005-4-1'
    NewBook.PRODUCTNO = '9787101046137'
    NewBook.SATETYSTOCKLEVEL = '10'
    NewBook.ORIGINALPRICE = '19'
    NewBook.NOWPRICE = '14.3'
    NewBook.DISCOUNT = '7.5'
    NewBook.DESCRIPTION = '''
        《水浒传》是宋江起义故事在民间长期流传基础上产生出来的，吸收了民间文学的营养。
        《水浒传》是我国人民最喜爱的古典长篇白话小说之一。它产生于明代，是在宋、元以来有关水浒的故事、话本、戏曲的基础上，由作者加工整理、创作而成的。
         全书以宋江领导的农民起义为主要题材，艺术地再现了中国古代人民反抗压迫、英勇斗争的悲壮画卷。
         作品充分暴露了封建统治阶级的腐朽和残暴，揭露了当时尖锐对立的社会矛盾和“官逼民反”的残酷现实，成功地塑造了鲁智深、李逵、武松、林冲、阮小七等一批英雄人物。
         小说故事情节曲折，语言生动，人物性格鲜明，具有高度的艺术成就。但作品歌颂、美化宋江，鼓吹“忠义”和“替天行道”，表现出严重的思想局限。
        '''
    NewBook.PHOTO = ''
    NewBook.TYPE = '16'
    NewBook.PAPERTOTAL = '922'
    NewBook.WORDTOTAL = '912000'
    NewBook.SELLSTARTTIME = '2006-03-20'
    NewBook.SELLENDTIME = '2021-07-12'

    NewBook.save()
    print('插入成功')
#更新操作
def fun_update_book():
    try:
        obj = Book.objects.get(PRODUCTID=1)  #返回结果有且只有一个
        obj.NAME='水浒'
        obj.save()
        print('更新成功')
    except Book.DoesNotExist:
        print('PRODUCTID=1的记录不存在，未更新任何数据')
#删除操作
def fun_delete_book():
    obj = Book.objects.filter(NAME='水浒') #返回QuerySet
    del_rows=obj.count()
    obj.delete()
    print('删除'+str(del_rows)+'条记录')
```

（2）编写 dm\views.py 文件。

```
from django.shortcuts import render
# Create your views here.
from django.http import HttpResponse
from .services import fun_get_books
from .services import fun_insert_book
from .services import fun_update_book
from .services import fun_delete_book

def index(request):
    # 查询
    fun_get_books()

    # 增加
    fun_insert_book()
    fun_get_books()

    # 更新
    fun_update_book()
    fun_get_books()

    # 删除
    fun_delete_book()
    fun_get_books()
    return HttpResponse("执行结束，请到控制台查看输出结果。")
```

（3）在 dm 目录里新建 urls.py 文件。

```
from . import views
from django.urls import path
urlpatterns = [
    path('', views.index, name='index'),
]
```

（4）在 mysite\urls.py 文件的 urlpatterns 列表中指定创建的 dm.urls 模块。

```
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('dm/',include('dm.urls')),
    path('admin/', admin.site.urls),
]
```

（5）在命令行工具中输入以下命令，启动 Django 自带的简易服务器：

```
python manage.py runserver
```

默认使用浏览器访问 http://127.0.0.1:8000/dm/，即可查看输出结果。

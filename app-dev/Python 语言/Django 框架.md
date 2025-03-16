

# Django 框架

## 一、前言

Django 是一个开放源代码的 Python Web 应用框架，采用 MVT 的软件设计模式，即模型（Model），视图（View）和模板（Template）。Django 将 MVC 中的视图进一步分解为 Django 视图和 Django 模板两个部分。MVC 中的控制器由 Django 框架的 URLconf 来实现。

Django 被认为是"大而全"的重量级 Web 框架，其自带大量的常用工具和组件（比如数据库 ORM 组件、用户认证、权限管理、分页、缓存)，甚至还自带了管理后台 Admin，适合快速开发功能完善的企业级网站。

  * URL 映射器：用于根据请求 URL 将 HTTP 请求重定向到相应的视图。
  * View：视图是一个请求处理函数，它接收 HTTP 请求并返回 HTTP 响应。
  * Models：模型是定义应用程序数据结构的 Python 对象，并提供在数据库中管理（添加，修改，删除）和查询记录的机制。Django 的 Model 层自带数据库 ORM 组件。



## 二、开发环境准备

|**名称**|**版本**|
|--|--|
|开发平台|Windows|
|Django_dmPython|3.1.7|
|Django|3.1.7|
|PyCharm|PyCharm 2021.1.3|


### 2.1 编译安装达梦 Django 驱动 Django_dmPython

  1. 安装完 DM 数据库软件后，在安装路径下的 drivers 目录下的 python 目录中，可以找到 dmPython 和 Django_dmPython 的驱动源码，由于提供的是源码，需要找到对应版本自行编译安装。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212281422082F6BHLO9EV6I38K3G0)

> **注意**
>
> 编译安装与 django 版本对应的 Django_dmPython，驱动源码与 django 源码的版本对应关系，例如 django317，能适配
> django3.1.x 所有版本。



  2. 编译安装 django 驱动 django_dmPython，到 djangoxxx/django_dmPython 下执行命令：python  
setup.py install，如图所示表示安装成功。



```
cd D:\dmdbms\drivers\python\django317\django_dmPython
python setup.py install
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228142334B77B5L2V2K4JFWNJTF)

### 2.2 安装 Django

  1. 使用 pip 安装 Django。



```
pip install  django==3.1.7
```

如果安装失败，可使用镜像安装，命令如下：

```
pip install django==3.1.7 -i http://pypi.doubanio.com/simple/ --trusted-host pypi.doubanio.com
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228142436KB195P1Q2CSDOYFUBB)

> **注意**
>
> 安装时需指定 Django 版本。



  2. 使用 pip list 命令查看是否安装成功。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2022121621380682GMSBH6ISP90BL5KN)

## 三、Django 应用开发

### 3.1 使用 PyCharm 新建项目 dm_django317

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228142530FMWL7K6DXMA4BRJYMS)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216213843N9281SYDXD4WAPISNS)

### 3.2 创建 Django 项目

在 PyCharm 中打开 Terminal 执行：

```
django-admin startproject mysite
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228142609RUUN1873FR5HRU67IL)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216213932MV4TF54AXGCU45CKEP)

### 3.3 创建 dm 应用

在 Django 中，项目是一个网站使用的配置和应用的集合。项目可以包含很多个应用。

```
cd mysite
python manage.py startapp dm
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228142724ZJIEAFX8515H1CXSDJ)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216214016EUM2Y5F1WVHIHXSD7X)

### 3.4 创建视图

编辑 dm/views.py 文件。

```
from django.http import HttpResponse

def index(request):
    return HttpResponse("Hello, 达梦数据库。 ")
```

### 3.5 在 dm 目录里新建 urls.py 文件

在 dm 目录里新建 urls.py 文件。

```
from . import views
from django.urls import path
urlpatterns = [
    path('', views.index, name='index'),
]
```

### 3.6 在根 URLconf 文件中指定创建的 dm.urls 模块

在 mysite/urls.py 文件的 urlpatterns 列表里插入一个 include()。

```
from django.contrib import admin
from django.urls import include, path

urlpatterns = [
    path('dm/', include('dm.urls')),
    path('admin/', admin.site.urls),
]
```

### 3.7 启动 Django 自带的简易服务器，验证是否正常工作

在 PyCharm 中打开 Terminal 执行：

```
python manage.py runserver
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228142845M07SLNHWF9LRYMV3RB)

浏览器访问 http://127.0.0.1:8000/dm/。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216214242T5BAM7CJHZ9897ECEI)

### 3.8 数据库配置

编辑 mysite/settings.py 配置文件。

设置时区：

```
TIME_ZONE='Asia/Shanghai'
USE_TZ=False
```

设置 DATABASES 设置项：

```

DATABASES = {
    'default': {
        'ENGINE': 'django_dmPython',  #使用的数据库后端
        'NAME': 'DAMENG',
        'USER': 'SYSDBA',
        'PASSWORD': '*****',
        'HOST': '192.168.201.118',
        'PORT': '5236',
        'OPTIONS': {'local_code': 1, 'connection_timeout': 5}
    }
}
```

### 3.9 创建模型

编辑 dm/models.py 文件。

```
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

### 3.10 把 dm 应用安装到 Django 项目里

在 Django 项目中包 dm 应用，需要在配置类 INSTALLED_APPS 中添加设置。

```
'dm.apps.DmConfig',
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212162144444B2SS44179GC0QEBK5)

### 3.11 运行 python manage.py makemigrations 为模型的改变生成迁移文件

在 PyCharm 中打开 Terminal 执行：

```
python manage.py makemigrations dm
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228143338VLJ5597TW38E2K7K4K)

迁移是 Django 对于模型定义（也就是你的数据库结构）的变化的储存形式 - 它们其实也只是一些磁盘上的文件。

### 3.12 在 dm 目录里新建一个 services.py 文件

该代码实现增、删、改、查四个基本操作。

```
from .models import Book
```

  1. 查询操作。



```
def fun_get_books():
    books = Book.objects.all() #返回QuerySet
    total_rows=books.count()
    print('查询到'+str(total_rows)+'条记录')
    for book in books:
        print(book.NAME, book.AUTHOR, book.PUBLISHER)
    print('')
```

  2. 增加操作。



```
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
```

  3. 更新操作。



```
def fun_update_book():
    try:
        obj = Book.objects.get(PRODUCTID=1)  #返回结果有且只有一个
        obj.NAME='水浒'
        obj.save()
        print('更新成功')
    except Book.DoesNotExist:
        print('PRODUCTID=1的记录不存在，未更新任何数据')
```

  4. 删除操作。



```
def fun_delete_book():
    obj = Book.objects.filter(NAME='水浒') #返回QuerySet
    del_rows=obj.count()
    obj.delete()
print('删除'+str(del_rows)+'条记录')
```

### 3.13 编写视图

编辑 dm/views.py 文件。

```
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

### 3.14 启动 Django 自带的简易服务器

在 PyCharm 中打开 Terminal 执行：

```
python manage.py runserver
```

浏览器访问 http://127.0.0.1:8000/dm/

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216215024BCU874AW4F9OI7VHSS)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221216215104GBE7Y92JOPMSDZ2W53)

## 四、常见问题

1. 无法识别 django-admin 命令

【问题描述】：无法识别 django 命令，具体报错信息如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221228143700Y5HONXJ19IYLITTN2K)

【问题解决】：需要包含 django-admin 的全路径，通常在 python 目录的 scripts 下有 django-admin.exe。

## 五、参考

  1. 示例代码下载：[Django_Code.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221223102548J65RF5YU5AUJDXP8CO)
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。



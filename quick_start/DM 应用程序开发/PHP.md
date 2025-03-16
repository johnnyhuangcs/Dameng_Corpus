

# PHP

本章主要介绍在 PHP 开发时，如何使用 PHP 快速连接达梦数据库。

# 一、达梦数据库安装

可访问达梦云适配中心[下载试用](https://eco.dameng.com/download/?_blank)，下载 DM8 数据库试用版并安装，安装步骤请参考 [DM 数据库安装](https://eco.dameng.com/document/dm/zh-cn/start/install-dm-windows-prepare.html)。

# 二、Linux 下安装 PHP

进入 [PHP 官网](https://www.php.net/) 下载合适版本 PHP ，或者直接使用 [php-7.4.33.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240808120444YH6QU0GE4XYBX9DBGO)  。

参考如下命令，安装 PHP：

```
./configure --prefix=/usr/local/php/  --with-config-file-path=/usr/local/php 
make && make install
ln -s /usr/local/php/bin/php /usr/bin/php
cp php.ini-production /usr/local/php/php.ini
```

使用 php -v 命令，查看版本：

```
[root@localhostphp7]# php -v
PHP 7.4.33 (cli) (built: Nov  4 2022 11:47:15) ( ZTS )
Copyright (c) The PHP Group
Zend Engine v3.4.0, Copyright (c) Zend Technologies
```

# 三、数据库连接

## 3.1 PHP 配置 DM 驱动

  1. 配置 DM 驱动，在 /usr/local/php/php.ini 中增加以下内容：



```
[PHP_DM]
extension_dir ="/opt/dmdbms/drivers/php_pdo"
extension=libphp74_dm.so
extension=php74_pdo_dm.so
[dm]
dm.port=5236
; ; 是否允许持久性连接
 dm.allow_persistent = 1
; ; 允许建立持久性连接的最大数. -1 为没有限制.
 dm.max_persistent = -1
; ; 允许建立连接的最大数(包括持久性连接). -1 为没有限制.
 dm.max_links = -1
; ; 默认的主机地址
 dm.default_host = localhost
; ; 默认登录的数据库
 dm.default_db = SYSTEM
; ; 默认的连接用户名
 dm.default_user = SYSDBA
; ; 默认的连接口令.
 dm.default_pw = *****
; ;连接超时，这个参数未实际的用到，等待服务器支持
 dm.connect_timeout = 10
; ;对于各种变长数据类型，每列最大读取的字节数。如果它设置为 0 或是小于 0,那么，读取变长字段时，将显示 NULL 值
 dm.defaultlrl = 4096
; ; 是否读取二进制类型数据，如果它设置为 0，那么二进制将被 NULL 值代替
 dm.defaultbinmode = 1
; ;是否允许检察持久性连接的有效性，如果设置为 ON，那么当重用一个持久性连接时，会检察该连接是否还有效
 dm.check_persistent = ON
```

  2. 配置完成后将动态库文件进行复制：



```
cp /opt/dmdbms/drivers/php_pdo/*74* /usr/local/php/lib
```

  3. 使用 php -m 命令 PHP 扩展，结果如下：



![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240808120537DGBXY5DWDDK3FJ67VL)

## 3.2 测试 PHP 连接 DM 数据库

  1. 连接数据库示例代码：php_conn.php



```
<?php
   header("Content-type:text/html;charset=utf-8"); //防止页面乱码
try
{
    $link = dm_connect("localhost:5236", "SYSDBA", "*****")
        or die("Could not connect : " . dm_error()."\n");

    //使用 dm_error 会显示 dm 的 php 接口返回的错误，执行成功，则继续往下执行。
    print "php: Connected successfully"."\n";

    /*断开连接*/
    dm_close($link);
}
catch(Exception $e)
{
    $e->getMessage() . "<br/>";
}
?>
```

  2. 使用 php php_conn.php 命令运行代码，结果如下：



![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240808120609HHFA55E0F3M1P0A42B)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml26648\wps3.jpg)

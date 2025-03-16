

# PHP 语言

## 前言

本章节主要介绍 PHP 应用开发常见问题，为用户提供 PHP 应用开发常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [PHP Fatal error: Unable to start DM module in Unknown on line 0](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20Fatal%20error:%20Unable%20to%20start%20DM%20module%20in%20Unknown%20on%20line%200)
  * [PHP Warning: PHP Startup: DM: Unable to initialize module](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20Warning:%20PHP%20Startup:%20DM:%20Unable%20to%20initialize%20module_1)
  * [PHP Fatal error: Unable to start PDO_DM module in Unknown on line 0](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20Fatal%20error:%20Unable%20to%20start%20PDO_DM%20module%20in%20Unknown%20on%20line%200)
  * [PHP 连接数据库提示“加密模块加载失败”](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20%E8%BF%9E%E6%8E%A5%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8F%90%E7%A4%BA%E2%80%9C%E5%8A%A0%E5%AF%86%E6%A8%A1%E5%9D%97%E5%8A%A0%E8%BD%BD%E5%A4%B1%E8%B4%A5%E2%80%9D)
  * [Ubuntu 系统 php 连不上达梦数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#Ubuntu%20%E7%B3%BB%E7%BB%9F%20php%20%E8%BF%9E%E4%B8%8D%E4%B8%8A%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [PHP Fatal error: Uncaught Error: Call to undefined function dm_query()](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20Fatal%20error:%20Uncaught%20Error:%20Call%20to%20undefined%20function%20dm_query\(\))
  * [php 使用 PDO 连接达梦出现中文乱码](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#php%20%E4%BD%BF%E7%94%A8%20PDO%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E5%87%BA%E7%8E%B0%E4%B8%AD%E6%96%87%E4%B9%B1%E7%A0%81)
  * [PHP 连接达梦访问模式对象异常](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E8%AE%BF%E9%97%AE%E6%A8%A1%E5%BC%8F%E5%AF%B9%E8%B1%A1%E5%BC%82%E5%B8%B8)
  * [windows server 64 位服务器中 PHP 适配 DM 出现乱码](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#windows%20server%2064%20%E4%BD%8D%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%B8%AD%20PHP%20%E9%80%82%E9%85%8D%20DM%20%E5%87%BA%E7%8E%B0%E4%B9%B1%E7%A0%81)
  * [PHP Warning: PHP Startup: DM: Unable to initialize module](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP-Warning--PHP-Startup--DM--Unable-to-initialize-module-)
  * [php -m 命令报段错误](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#php%20-m%20%E5%91%BD%E4%BB%A4%E6%8A%A5%E6%AE%B5%E9%94%99%E8%AF%AF)
  * [PHP fatal error: Python.h: No such file or directory](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20fatal%20error:%20Python.h:%20No%20such%20file%20or%20directory)
  * [php 连接达梦，查询时间不对](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#php%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%EF%BC%8C%E6%9F%A5%E8%AF%A2%E6%97%B6%E9%97%B4%E4%B8%8D%E5%AF%B9)
  * [bindValue 报错 Error occurred when binding parameters ​':ThinkBind_1_2054953456_​'](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#bindValue%20%E6%8A%A5%E9%94%99%20Error%20occurred%20when%20binding%20parameters%20':ThinkBind_1_2054953456_')
  * [PHP 页面访问数据库报错 Could not connect : Create SOCKET connection failure](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#PHP%20%E9%A1%B5%E9%9D%A2%E8%AE%BF%E9%97%AE%E6%95%B0%E6%8D%AE%E5%BA%93%E6%8A%A5%E9%94%99%20Could%20not%20connect%20:%20Create%20SOCKET%20connection%20failure)
  * [php-fpm 重启报错：​"Unable to start DM module in Unknown on line 0​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#php-fpm%20%E9%87%8D%E5%90%AF%E6%8A%A5%E9%94%99%EF%BC%9A%22Unable%20to%20start%20DM%20module%20in%20Unknown%20on%20line%200%22)
  * [使用 PHP 连接达梦时报错：​"无法初始化模块​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-php-new.html#%E4%BD%BF%E7%94%A8%20PHP%20%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%97%B6%E6%8A%A5%E9%94%99%EF%BC%9A%22%E6%97%A0%E6%B3%95%E5%88%9D%E5%A7%8B%E5%8C%96%E6%A8%A1%E5%9D%97%22)



## 正文

### PHP Fatal error: Unable to start DM module in Unknown on line 0

【问题描述】

```
PHP Fatal error: Unable to start DM module in Unknown on line 0 
```

【问题解决】

运行缺少达梦的动态加载库。

尝试将数据库安装目录 bin 下的以下 dll 文件拷贝至 C:\Windows\SysWOW64 和 C:\Windows\System32 (替换时会提示是否替换原有文件，这里选择“否”)。

```
dmdpi.dll
dmcalc.dll
dmelog.dll
dmmem.dll
dmos.dll
dmcomm.dll
dmcpr.dll
dmcyt.dll
dmstrt.dll
dmclientlex.dll
dmdta.dll
dmcfg.dll
dmdcr.dll
dmmout.dll
dmcvt.dll
dmmsg.dll
```

> **注意**
>
> 如果上述方法不成功，则尝试把bin所有的dll文件拷贝过去。



### PHP Warning: PHP Startup: DM: Unable to initialize module

【问题描述】

```
PHP Warning: PHP Startup: DM: Unable to initialize module
```

【问题解决】

VC 或者 (ts/nts) 版本不匹配，下载和达梦的 VC / (ts/nts) 版本匹配的 PHP 安装文件。可查看 php 配置信息（Thread Safety），如果是 enable 即为 ts 版本（Thread Safety）。

### PHP Fatal error: Unable to start PDO_DM module in Unknown on line 0

【问题描述】

```
PHP Fatal error: Unable to start PDO_DM module in Unknown on line 0 
```

【问题分析】

此问题为读取到了 php_pdo 驱动但是无法加载模块导致，一般是由于 PHP 版本与 DM 数据库版本不一致、环境变量未设置或未生效导致。

【问题解决】

需要下载对应达梦版本，更改 dm 驱动与 php 版本一致，并把下载对应版本 DM 数据库的 bin 文件加入环境变量，并检查 php.ini 配置是否正确。如果版本一致，确认达梦 bin 目录加入了环境变量，如果环境变量不生效，则重启操作系统。

若达梦数据库和 web 应用不在一台机器上，需保证 PHP 版本与 DM 驱动版本一致。

window 下：

需要指定达梦 bin 目录和 so 文件，修改 php.ini 文件指定 dll 具体位置或者都放在 ext 下面，指定 dll 文件。

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522154048FJ8CXOF39LOK0ODNLT)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps1.jpg)

Linux 下：

需要将 linux 中同一版本达梦数据库的 bin 目录和 driver 目录传输到远程 web 应用服务器，并加入环境变量。

> **注意**
>
> 1\. php_pdo 目录是否正确，如果安装有多个版本的达梦数据库，需要指定的达梦 bin 目录和达梦驱动版本一致；
>
> 2\. php74_pdo_dm.so 版本和 php 版本是否对应，主要 php NTS 版本指定 so 文件为无 ts 版本，php ZTS 指定
> so 文件为有 ts 版本，libphp74_dm.so 对应 DM 模块，php74_pdo_dm.so 对应 PDO_DM 模块；
>
> 3\. 达梦版本需要和 php 版本一致，使用 php32 位版本需要对应 DM32 位版本，php64 位版本需要对应 DM64
> 位版本。例如：以下如果使用 php74NTS 版本需要使用 libphp74_dm.so 和 php74_pdo_dm.so，如果使用 php74ZTS
> 版本则需要使用 libphp74ts_dm.so 和 php74ts_pdo_dm.so。



![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522154153XA3GNA11F2OXCWU90W)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps2.jpg)

### PHP 连接数据库提示“加密模块加载失败”

【问题描述】

PHP 连接数据库提示“加密模块加载失败”。

【问题解决】

确认 Apache 服务正常，通过访问 phpinfo 测试界面确认，Apache 和 php 均正常。

配置 extension_dir 指向达梦安装目录 dirvers 目录的 php_pdo 驱动目录；

举例：配置 extension_dir = /home/dmdba/dmdbms/drivers/php_pdo 。

配置环境变量指向安装目录的 bin 目录。

检查/usr/lib64 下是否缺失 libcrypto.so 动态链接库：

```
[root@localhost ~]# cd /usr/lib64/
[root@localhost lib64]# ls -l libcrypto.so 
ls: cannot access libcrypto.so: No such file or directory
```

如果 /usr/lib64 下缺失 libcrypto.so 库文件，可以使用 DM 数据库 bin 目录下的 libcrypto.so 文件，手动在 /usr/lib64 下创建 libcrypto.so 软链接。或者直接将 libcrypto.so 文件拷贝到 /usr/lib64 目录。

```
[root@localhost lib64]# ln -s /opt/dmdbms/bin/libcrypto.so libcrypto.so
[root@localhost lib64]# ls -l libcrypto.so
lrwxrwxrwx. 1 root root 28 Aug 4 13:34 libcrypto.so -> /opt/dmdbms/bin/libcrypto.so
```

重启服务器后正常。

### Ubuntu 系统 php 连不上达梦数据库

【问题描述】

Ubuntu 系统 php 连不上达梦数据库

【问题解决】

  1. 首先通过 ./php -info 查看模块加载是否成功，其次检查 php 的配置文件 php.ini，检查扩展是否设置正确，如：extension_dir = "/opt/php/lib" ，可以将达梦的 php 驱动包拷贝到 /opt/php/lib 下；extension = php71_pdo_dm.so ，注意这里是 php7.1.33 NTS（非线程安全版），需要选对驱动版本。
  2. 最后要检查用户环境变量，要设置正确。比如：



```
vim /home/dmdba/.bash_profile
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/home/dmdba/dmdbms/bin"
```

### PHP Fatal error: Uncaught Error: Call to undefined function dm_query()

【问题描述】

PHP 高版本使用 dm_query 报错。

【问题解决】

PHP 版本更新导致此报错。

PHP5.x 版本前 不会报这个错，PHP7.x 后会报这个错。

PhP7.x 用 dm_exec 代替本扩展，dm_query 方法自 PHP5.5.0 起已废弃，并在自 PHP7.0.0 开始被移除。详见 DM 数据库安装路径的 doc 目录下 《DM8 程序员手册》 6.1.1 章节。

### php 使用 PDO 连接达梦出现中文乱码

【问题描述】

数据库初始化为 UTF8 编码，在达梦所在服务器 dm_svc.conf 配置文件设置了 utf8 编码，PDO 也设置了 UTF8 编码，但仍然在 UTF8 编码下出现乱码。

【问题解决】

通过设置应用端的 dm_svc.conf 文件，设置 CHAR_CODE 的值：(PG_UTF8)，修改完后重启 fpm 即可。

如果应用端没有安装 DM 数据库，可以从数据库所在服务器如下目录去拷贝 dm_svc.conf 文件，或者可以在应用端自行创建 dm_svc.conf 文件放在如下目录。

安装 DM 数据库所在服务器 dm_svc.conf 文件默认路径：

32 位的 DM 安装在 Win32 操作平台下，此文件位于 %SystemRoot%\system32 目录。

64 位的 DM 安装在 Win64 操作平台下，此文件位于 %SystemRoot%\system32 目录。

32 位的 DM 安装在 Win64 操作平台下，此文件位于 %SystemRoot%\SysWOW64 目录。

在 Linux 平台下，此文件位于 /etc 目录下。

### PHP 连接达梦访问模式对象异常

【问题描述】

使用 PHP 连接达梦，环境为一个用户多个模式，使用 a 用户连接数据库，但应用程序访问的具体对象为 b 模式下的对象，如果想要访问 a 模式下的对象此时如何解决。

【问题解决】

可在连接数据库成功后先执行下 set schema 模式名语句，改语法为会话级生效。

```
dm_exec($link,"set SCHEMA a");
```

详见 DM 安装路径 doc 下 《DM8 程序员手册》 6.1.2 章节。

### windows server 64 位服务器中 PHP 适配 DM 出现乱码

【问题描述】

windows server 64 位服务器中应用运行 php 代码无问题，但显示的数据内容为乱码。

【问题解决】

Windows 32 位：将 dm_svc.cof 文件放在应用服务器 system32 目录下。

Windows 64 位：将 dm_svc.cof 文件放在应用服务器 system32 和 syswow64 目录下。

如果 php 版本为 64 位，修改 C:\Windows\System32 下 dm_svc.conf  文件，根据数据库编码添加 CHAR_CODE 参数。如果无法解决，复制 dm_svc.conf 文件到 C:\Windows\SysWOW64 下，并根据数据库编码添加 CHAR_CODE 参数。

如果 php 版本为 32 位复制 dm_svc.conf 文件到 C:\Windows\SysWOW64 下，并根据数据库编码添加 CHAR_CODE 参数。

默认情况下 dm_svc.conf 内容如下：

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522160941I4TIDKPLSWUBUMWY41)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps3.jpg)

### PHP Warning: PHP Startup: DM: Unable to initialize module

【问题分析】

原因 1：达梦的 php 驱动文件没有拷贝进 PHP 的 ext 文件夹里。

原因 2：写进 php.ini 里面的驱动名称，和从达梦拷贝进 ext 文件的驱动名称不一致。

原因 3：下载的 PHP 版本 TS/NTS 和达梦数据库版本不匹配，TS 的依赖库和 NTS 的依赖库是不一样的，于是显示找不到 .dll 或 .so 文件。将 extension_dir = "ext" 前面的 ; 去掉，这个路径根据自己的 PHP 文件的路径配置修改。

原因 4：未设置依赖文件 libdmdpi.so 的环境变量。

【问题解决】

Windows 下：

  1. 解压 PHP 程序，复制和配置 php.ini 。



![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522161327ADSJZ5C1Y0IONVLYWQ)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps4.jpg)

  2. 拷贝驱动到对应的扩展目录下。



![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522161337OPW2QMPBV85D064A4T)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps5.jpg)

  3. 配置扩展目录，配置数据库扩展模块。



![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522161637AJDAFSPAS7L203COB5)

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202405221616441Y5PT2SSL10VDG8FHC)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps6.jpg)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps7.jpg)

Linux 下：

拷贝驱动至目录 /usr/lib64/php/modules ，添加达梦数据库安装目录到环境变量 LD_LIBRARY_PATH，配置具体的 dm 数据库 pdo 扩展。

方法一：
pdo 扩展配置文件在 /etc/php.d/pdo.ini ，将 dm、php、pdo 驱动添加到 pdo.ini 文件中，内容如下：

```
extension=pdo.so

extension=php54_pdo_dm.so

extension=libphp54_dm.so
```

> **注意**
>
> extension=pdo.so 要放到最上面。



方法二：
在 /etc/php.d 目录下，创建两个达梦驱动配置文件 dm8.ini 和 pdo_dm8.ini ，分别配置达梦 php 驱动扩展和 pdo 驱动扩展。内容如下：

```
[root@localhost php.d]# pwd

/etc/php.d

[root@localhost php.d]# cat dm8.ini

extension=libphp54_dm.so

[root@localhost php.d]# cat pdo_dm8.ini

extension=php54_pdo_dm.so
```

### php -m 命令报段错误

【问题描述】

php -m 报无效段错误。

【问题解决】

ibcrypto.so 文件可能冲突，重命名即可。

### PHP fatal error: Python.h: No such file or directory

【问题描述】

报错如下：

```
PHP fatal error: Python.h: No such file or directory 。
```

【问题解决】

这是因为缺少 python-devel 包导致的，可以使用 yum 进行安装解决，yum 源的配置，请参考 [Linux 平台下 yum 源配置](https://eco.dameng.com/docs/zh-cn/article/article-004.html) 。

### php 连接达梦，查询时间不对

【问题描述】

select FROM_UNIXTIME ('1620693231','YYYY-MM-DD %h:%i:%s') 查询时，时间对不上。

【问题解决】

检查 dm_svc.conf 是否添加了时区 TIME_ZONE=(480) 的配置，检查服务器时间是否正确。

### bindValue 报错 Error occurred when binding parameters ':ThinkBind_1_2054953456_'

【问题描述】

使用 PHP 版本为 7.2，数据库版本为  2020 年 9 月之前版本 ，报错如下：

```
Error occurred when binding parameters ':ThinkBind_1_2054953456_' 
```

【问题解决】

替换 libphp72_dm.so、php72_pdo_dm.so 为  2020 年 10 月之后的版本。

### PHP 页面访问数据库报错 Could not connect : Create SOCKET connection failure

【问题描述】

Centos7 上配置好 Apache+php+DM 数据库的环境，测试页面地址浏览器访问报以下错误：

情况 1：

```
Could not connect : Create SOCKET connection failure 。
```

情况 2：

```
Error: SQLSTATE[HY000] dpi_login: -70028 Create SOCKET connection failure 。
```

【问题解决】

  1. 确认 Apache 服务正常。
  2. 通过访问 phpinfo 测试界面确认，Apache 和 php 均正常，能访问则表示端口服务均正常。



![图片 8.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522163518UVV7CQ5H27HEJBPLRF)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps8.jpg)

  3. 查看 Apache error_log 。



发现有如下输出：

```
[core:notice] [pid 2122] SELinux policy enabled; httpd running as context system_u:system_r:httpd_t:s0
```

初步判断可能是由于 selinux 开启导致。

（1）检查 selinux 状态。

```
[root@localhost ~]# getenforce
```

（2）执行 setenforce 0 设置为 Permissive 模式，永久生效需要修改 /etc/selinux/config 文件并重启。

```
[root@localhost ~]# setenforce 0

[root@localhost ~]# getenforce
```

（3）也可以设置为 disabled 完全禁用 SELINUX。

修改/etc/selinux/config ，将 SELINUX 设置为 disabled，重启生效，再此访问地址，可以正常访问数据库。

> **注意**
>
> 目前 SELinux 支持三种模式，分别如下： 1\. enforcing：强制模式，代表 SELinux 运作中，且已经正确的开始限制
> domain/type 了；2. permissive：宽容模式，代表 SELinux 运作中，不过仅会有警告讯息并不会实际限制 domain/type
> 的存取，这种模式可以运来作为 SELinux 的 debug 之用；3. disabled：关闭，SELinux 并没有实际运作。



### php-fpm 重启报错："Unable to start DM module in Unknown on line 0"

【问题描述】

php-fpm 重启后报错：

```
PHP message: PHP Fatal error: Unable to start DM module in Unknown on line 0
```

报错信息如下图：

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522164540KZEY5L0GF1BEMNPI9X)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps9.jpg)

【问题解决】

达梦数据库安装的目录 bin 文件夹下的 libdmdpi.so 文件放入 /usr/lib 或者 /usr/lib64/ 目录里即可解决。

### 使用 PHP 连接达梦时报错："无法初始化模块"

【问题描述】

PHP 连接达梦，使用命令验证加载 DM PHP 驱动模块时报错："无法初始化模块"。

报错信息如下图：

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522164936WSRTTRCY5NCRD1EQW2)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps10.jpg)

【问题解决】

php.ini 文件中引用的 .so 文件版本需要和 php 版本保持一致。

使用命令：php -v 查看 PHP 版本。

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522164946PZW3YBZO3IJGAB9BAM)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps11.jpg)

php 的版本是 8.0， php.ini 文件中选择 php 版本对应的 .so 文件。

![图片 12.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240522165023PZ73VY2NPOIIOSIUOS)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml20400\wps12.jpg)

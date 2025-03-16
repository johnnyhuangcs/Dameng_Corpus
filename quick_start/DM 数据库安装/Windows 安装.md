

# 安装前准备

## 一、检查系统信息

用户在安装 DM 数据库前，需要检查当前操作系统的相关信息，确认 DM 数据库安装程序与当前操作系统匹配，以保证 DM 数据库能够正确安装和运行。

用户可以在终端通过 `Win+R` 打开运行窗口，输入 `cmd`，打开命令行工具，输入 `systeminfo` 命令进行查询，如下图所示：

![查询系统信息](https://download.dameng.com/eco/docs/asset/start/az0.png)

## 二、检查系统内存

为了保证 DM 数据库的正确安装和运行，要尽量保证操作系统至少 2 GB 以上的可用内存 (RAM)。如果可用内存过少，可能导致 DM 数据库安装或启动失败。

用户可以通过【任务管理器】查看可用内存，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123095921YRMAPAMLJN9C4LEYAG)

## 三、检查存储空间

DM 完全安装需要至少 1 GB 以上的存储空间，用户需要提前规划好安装目录，预留足够的存储空间。数据库在安装完成后用户还需要注册数据库实例用来管理和保存数据，所以还需要规划好实例的保存路径，同时还需要规划好备份保存路径，评估数据量的大小为实例和备份文件预留足够的空间。


# 数据库安装

## 一、装载镜像文件

  1. 选中下载好的安装包，右键点击装载。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123101943F57JT5W9VH74MI23G9)

  2. 弹出安全警告后不用担心，请放心点击【打开】。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123102140P175UVVGVG11L6M16Z)

  3. 挂载成功后会弹出安装程序 setup.exe



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123102455ICPIF6F0Z4C98GF5MJ)

## 二、安装数据库

### 2.1 运行安装程序

双击运行安装程序 setup.exe，系统可能出现保护提示，阻止安装数据库，点击警告中的【更多信息】选择【仍要运行】即可开始安装。

### 2.2 选择语言与时区

双击运行【setup.exe】安装程序，根据系统配置选择相应语言与时区，点击【确定】按钮继续安装。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123104132T0ADOWK696JTTCENF1)

### 2.3 安装向导

点击【下一步】继续安装，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123104542OKJF466VAYK8U75XA4)

### 2.4 许可证协议

在安装和使用 DM 数据库之前，需要用户阅读并接受许可证协议，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123104626PI6UDC2G7O4JITPN1E)

### 2.5 验证 Key 文件

验证 Key 文件环节可跳过，如果没有 Key 文件，点击【下一步】即可。如有则点击【浏览】按钮，选取 Key 文件，安装程序将自动验证 Key 文件合法性，点击【下一步】继续安装，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123104729I046P8YKN2KT9NA2Z3)

### 2.6 选择安装组件

DM 安装程序提供四种安装方式：“典型安装”、“服务器安装”、“客户端安装”和“自定义安装”，此处建议选择【典型安装】，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401231050091UR0B1B0LLTJUJVAPJ)

  * 典型安装包括：服务器、客户端、驱动、用户手册、数据库服务。
  * 服务器安装包括：服务器、驱动、用户手册、数据库服务。
  * 客户端安装包括：客户端、驱动、用户手册。
  * 自定义安装包括：用户根据需求勾选组件，可以是服务器、客户端、驱动、用户手册、数据库服务中的任意组合。



### 2.7 选择安装目录

DM 默认安装在 C:\dmdbms 目录下，不建议使用默认目录，改为其他任意盘符即可，以 D:\dmdba\dmdbms 为例，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123105335CAVN73ARTGRKIHGMUO)

> **注意**
>
> 安装路径里的目录名由英文字母、数字和下划线等组成，不建议使用包含空格和中文字符的路径等。



### 2.8 安装前小结

显示用户即将进行的数据库安装信息，例如产品名称、安装类型、安装目录、所需空间、可用空间、可用内存等信息，用户检查无误后点击【安装】按钮进行 DM 数据库的安装，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401231055254NN1V5N4HZNOI71RZK)

### 2.9 数据库安装

安装过程需耐心等待 1~2 分钟，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401231056472M1GYJ3ACKH0PTYHKS)

### 2.10 数据库安装完成

数据库安装完成后，选择【初始化】数据库，具体操作步骤详见[配置实例](/document/dm/zh-cn/start/dm-install-windows)。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401231058463V2HM3XRIQVAONSG5Y)


# 配置实例

## 一、选择操作方式

此处选择【创建数据库实例】，点击【开始】进入下一步骤，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123141510GQML9NL4OWUJTZUPEJ)

## 二、创建数据库模板

此处可以根据实际需求选择合适的数据库模板，一般建议选择【一般用途】其它保持默认即可，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123141600M3BBEJ36RZ239YWXFE)

## 三、选择数据库实例目录

本例中数据库安装路径为 D:\dmdba\dmdbms\data，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123141922YLOHU8I1LFDUCX93MJ)

## 四、输入数据库标识

可自定义输入或保持默认数据库名称、实例名、端口号等参数，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123141957Y0YBMT0F0ZMCBKYOQF)

## 五、数据库文件所在位置

此处可选择自定义或保持默认配置路径，如下图所示：

控制文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123142421I13NL8L2JDCYZZWHYF)

数据文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123142522LNIV5AYZM9S6LHA55S)

redo 日志文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123142604Y41S1YM14CHEOX5HPH)

初始化日志：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123142654452P0Z7KZOU9NPCWAD)

用户可通过选择或输入确定数据库控制文件、数据文件、日志文件、初始化日志等文件的所在位置，并可通过右侧功能按钮，对文件进行添加或删除。

## 六、数据库初始化参数

此处配置可根据实际需求进行配置，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123144003BBETUCAWQNSV7M9USP)

需要注意的是页大小 、簇大小 、大小写敏感 、字符集 、空格填充模式等部分参数， 一旦确定无法修改 ，需谨慎设置。

常见参数说明：

  1. 数据文件使用的簇大小：默认值 16，可选值： 16、 32、 64，单位：页。
  2. 数据页大小：默认值 8，可选值： 4、 8、 16、 32，单位： KB。
  3. 日志文件大小：默认值 256，单位为： MB，范围为： 64 MB~2 GB。
  4. 大小敏感：默认值 Y，可选值： Y/N， 1/0。
  5. 字符集：默认值 0，可选值： 0[GB18030]， 1[UTF-8]， 2[EUC-KR]。



## 七、口令管理

建议用户在首次安装数据库初始化实例时，立即修改数据库系统用户的初始密码，并设置一定的密码强度，以保障数据安全性。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220191542JGV6XPXLFPTG7FW9HC)

用户可输入 SYSDBA，SYSAUDITOR 的密码，如果安装版本为安全版，将会增加 SYSSSO 用户的密码修改。

## 八、选择创建示例库

此处建议勾选创建示例库 `BOOKSHOP` 或 `DMHR`，作为测试环境，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401231447393FJ8Z5TT46F4CKU4UO)

## 九、创建数据库摘要

在安装数据库之前，将显示用户通过数据库配置工具设置的相关参数。点击【完成】进行数据库实例的初始化工作，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123144901MOBMO36DENZEIR54PD)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123150948PXLP6UNVJVRV0HRSVT)

安装完成

安装完成后将弹出数据库相关参数及文件位置。点击【完成】即可，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123145035XMAV1JDBV2TUSFAAGP)


# 启动、停止数据库

## 一、启动达梦服务查看器

在计算机【开始】栏找到【DM 服务查看器】快捷方式并打开。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240301140245YEGQHMTDEKGNAPMAVB)

或者在数据库安装目录下 tool 目录例如 D:\dmdba\dmdbms\tool 中找到 dmservice 应用程序并双击启动。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401231501289RYMLNNTQWUDGPGGBO)

## 二、启、停数据库服务

可以通过【DM 服务查看器】查看到对应的 DM 服务，可选择【启动】或【停止】对应的服务，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240123150019ZKP6JCRFQTPREM7DOS)

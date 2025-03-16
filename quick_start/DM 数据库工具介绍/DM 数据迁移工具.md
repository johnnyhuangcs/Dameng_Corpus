

# DM 数据迁移工具

## 一、概述

DM 数据迁移工具 DM DTS 提供了主流大型数据库迁移到 DM、DM 到 DM、文件迁移到 DM 以及 DM 迁移到文件等功能。

得益于 DM 数据库对目前主流大型关系型数据库系统有着业界领先的兼容性，在存储层面、语法层面、接口层面和它们保持高度兼容，借助于 DM 图形界面且采用向导方式引导各个迁移步骤的 DTS 工具，移植工作可以变得非常的简单。

## 二、迁移准备

  1. 停止应用；
  2. 确认要迁移的用户（模式、数据库）；
  3. 记录原数据库中要迁移的对象的数量；
  4. 记录原数据库中要迁移的所有对象名称；
  5. 记录原数据库中要迁移的表的数据量（行数）；
  6. 创建目标数据及实例；
  7. 创建目标数据的表空间及用户。



## 三、启动迁移工具

  * Windows 环境启动 DM 数据迁移工具



点击【开始界面】，选择【达梦数据库】菜单，点击【DM 数据迁移工具】，即可进入数据迁移工具，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240301140422C6TE1DG1SSIETLNMBV)

  * Linux 环境启动 DM 数据迁移工具



启用图形化安装界面前需要通过如下命令将图形界面权限放开：

```
[root@localhost mnt]# xhost +
access control disabled, clients can connect from any host
[root@localhost mnt]# echo $DISPLAY
[root@localhost mnt]# su - dmdba
Last login: 四 1月 25 16:41:51 CST 2024 on pts/1
[dmdba@localhost ~]$ export DISPLAY=:0.0
```

> **注意**
>
> 该方法为本地调用图形化界面，如果希望通过其它机器调用该图形化界面需设置 export DISPLAY=调用图形化机器的IP:0.0，例如，数据库安装机器
> IP 为 10.10.12.25，需要在 IP 为 192.132.32.12 的机器上调用图形化界面，需要设置 export
> DISPLAY=192.132.32.12:0.0



进入数据库安装路径 /tool 目录下，运行 ./dts 即可启动 DM 数据迁移工具。

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/tool/
[dmdba@localhost tool]$ ll
[dmdba@localhost tool]$ ./dts
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240204150221JR0RXOUA3VEKKPR4HS)

## 四、迁移管理

### 4.1 新建工程和迁移

点击按钮创建新的工程，输入工程名和工程描述，点击【确定】，即可创建工程，如下图所示：

![文档和图片文件夹图示](https://download.dameng.com/eco/docs/asset/start/dts-01.png)

以 ORACLE-DM 为例：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061915574068Y9K0PEJPD6UQZFLW)

### 4.2 迁移评估

右键选择【新建评估】，输入评估名称和评估描述，即可创建评估，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619153859WXPR8R4WT6BVUMYJST)

目前达梦迁移工具支持的迁移评估如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160043BZ1PAJWH0M358F74VN)

以 ORACLE 迁移达梦评估为例：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160211QFR84H667R48ERND5M)

输入数据源（Oracle 数据库）的信息：主机名 (IP) 、端口，服务名（Oracle 默认服务名 ORCL），角色（默认），用户名和口令，要确认数据库信息正确，保持开启状态，Oracle 监听已启动，然后点击【下一步】。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160333MOXB30ZEHCQXRZ0VKQ)

选择评估对象模式以及数据对象类型，点击【下一步】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160420B27KSVXXW8W2VUE8FE)

选择评估的模式，点击【下一步】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160458X6QLQ2BY26TL7EUIR1)

选择评估的具体数据对象，点击【下一步】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160533WF1A8FJCSQ8J35VJ4C)

迁移评估对象详情总览，点击下一步。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160606JBOA1QV38ZBYPKUE1C)

点击【完成】，迁移评估开始，等待迁移评估结束，查看迁移报告。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160648EP4SJ5PZ7DX9WU05NM)

评估报告选项如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160721T5DWWI2HAKGVDK3UTB)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061916074633FVS52ZLKY1ZW4Z6V)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061916080439QC4WZX3JHU70H0HX)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406191608233P7GQQYN37VD7Z7I0D)

点击“不兼容”，点击“详情”我们可以了解到具体不兼容原因，并进行修改，补充到“转换后 SQL”模块中。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160851UKGRO9LQL4C096JI9H)

点击【修改】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619160916ZMB4U8133XD2UKO2UQ)

手动进行语法修改后，点击【校验】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619161203XSN0HC2IM2AHWKGFJU)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619161222V0GU8ZO33PL17HY6UF)

将修改后的 SQL 保存下来，后面迁移时可用，然后关闭。

### 4.3 创建迁移

右键选择【新建迁移】，输入迁移名称和迁移描述，即可创建迁移，如下图以 ORACLE 迁移到 DM 为例：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240204161801TA3BTNN7RXJCY2PZ9Q)

输入迁移名称：Oracle-DM，点击【确定】按钮，完成迁移的创建，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619161519Z67ZC3P1QT396Q3SOV)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619161628RNOWNJN559GVFQV8UY)

选中新建的迁移，即可查看迁移工具的欢迎界面，介绍了迁移工具的支持情况等信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202402041616067ZYHF1M4H7LYHNPQ2C)

#### 4.3.1 选择数据源和目的库

在欢迎界面点击【下一步】，选择要迁移的方式，以 Oracle 迁移到 DM 数据库为例，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240204161540UDEYGG0T099J6MBRW3)

输入数据源（Oracle 数据库）的信息：主机名 (IP) 、端口，服务名（Oracle 默认服务名 ORCL），角色（默认），用户名和口令，要确认数据库信息正确，保持开启状态，Oracle 监听已启动。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619161744VR7LXZW7CT1INOLOU9)

在创建连接 Oracle 数据库时，默认不需要修改驱动，如果迁移过程中报错提示有驱动相关的错误，建议通过指定驱动的方式来连接数据库，驱动可以在 Oracle 官网获取与 Oracle 迁移版本相对应的驱动。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312061425317K9H50UI42I52WJB9N)

再输入目的数据库（DM 数据库）的信息：主机名 (IP) 、端口（DM 默认端口 5236），用户名和口令，要确认 DM 数据库信息正确，保持开启状态。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240204161452MWYARJV2OYI564LGGU)

#### 4.3.2 配置迁移选项

配置好数据源后会有配置迁移选项，常用配置迁移对象方式为【选择迁移对象】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619162019TS50AEMWZ8JUNVIXGT)

迁移对象建议勾选【保持对象名大小写】、【使用默认数据类型映射关系】，如果是 GBK18030 字符集迁移到 UTF8 字符集且知道库中存有大量中文字符的情况，字符长度可以设置为 2/4/8，此选项会将所有字符集类型字段长度自动扩展所选字符长度的倍数，因为 GBK18030 一个中文字符占用 2 个字节，而 UTF8 占用 3 个字节，如果不扩展长度会出现字符串截断情况。

#### 4.3.3 指定对象复制或查询

选择源模式和目的模式。

  * 复制的对象包括：模式及模式对象、目录、公共同义词、上下文等，根据不同数据源，支持复制不同的对象。
  * 模式及模式对象包括：模式、表、视图、物化视图、序列、存储过程/函数、包、类、同义词以及自定义类型，根据数据源不同，支持的模式及模式对象也不同。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619162203XJKSNK1TPCLYGFPNI8)

#### 4.3.4 选择迁移对象

此处选择要迁移的具体表格、视图、存储过程等，用户可根据名称具体选择。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619162235FACEVCPA57LGWJKAGR)

#### 4.3.5 设置表映射关系

设置表的映射关系，包括列的映射、创建表的策略、复制数据的策略、是否手工编辑建表的 SQL。

迁移策略选项：可设置迁移策略，包括表定义、主键、约束条件、索引、数据、读写行数设置等参数，可供用户调整策略。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619162304JEJW5GLFCFCHTYSF6O)

如果开启并发会增加迁移速度，但是同时也会耗用更多的系统资源，请根据实际情况设置。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061916250771IJTFY534DB7URK1B)

勾选应用当前选项到其他同类对象，则该策略会应用到其他同时同步的表。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619162537O5AK8V6PYWAACK3KSR)

#### 4.3.6 审阅迁移任务

审阅列表中列出的要执行的任务，用户可查看迁移过程重要参数和修改条件，点击【完成】进入下一步操作，如需修改，可点击【上一步】进行修改。如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619162903K43VHZ6NGF72AR5H1Z)

点击【完成】开始数据迁移，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619162924AOKCWKFAL7YLCH15PG)

#### 4.3.7 验证数据

查询迁移后数据库中要迁移的对象的数量，示例语句如下：

```
select object_type,count(*) from all_objects where owner='HR' group by object_type order by 1;
```

如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619163159PORRGMVRXN7PJU44AN)

查询记录迁移后数据库中要迁移的所有对象名称，如下图所示：

```
SELECT OWNER,OBJECT_TYPE,OBJECT_NAME FROM ALL_OBJECTS WHERE OWNER='HR';
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619163245KD367B2I58W7CZXH4N)

完成数据迁移后，我们还需对迁移的数据进行验证，表、视图、存储过程、序列都存在且与原数据一致，确认数据无异常后对迁移后的数据库做一次全备。

> **注意**
>
>
> 迁移过程中可能遇到原对象名跟迁移后的对象名对不上，但数量是相同的情况，这种情况主要是约束类对象。因为原库使用了系统自动命名，迁移后也是系统自动命名，虽然名称不同，但内容和功能相同。



### 4.4 数据类型映射

DM 数据迁移工具支持自定义数据类型映射，支持的数据库列表如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406191633543X95DRJL7IW3948VZL)

首先自定一个字段映射，这里把 VARCHAR2 映射成 VARCHAR 并扩大字符长度为两倍。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061916341591A2V2M6WBKCV8N4MO)

在迁移时将【使用默认数据类型映射关系】不勾选并点开【配置类型映射关系】。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619163446UOEJZSWE9HI2C52YN6)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619163504VGEVSWM1CY0FLW424R)

可以看到这个迁移已经应用自定义的映射关系，迁移完成后查看 VARCHAR2 字段类型映射成功。

源端：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619163551I59TARRMVQGYGTFP74)

目的端：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619163640LTDYOXYHV69L4XW7AE)

根据对比可以看出 “VARCHAR2 映射成 VARCHAR 并扩大字符长度为两倍”已经映射成功了。

### 4.5 配置作业与调度

达梦迁移工具支持作业调度，即定时执行：

  1. 右键创建新的调度，输入调度名，配置调度类型及执行频率、时间等，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619163934BIVTB9IQXVEN5779CY)

  2. 右键新建作业，作业名及作业描述然后点击【确定】。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164044TBCLY6GO2F2848WEYU)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164114HAGC8P7N11MH3EFPS4)

  3. 添加迁移



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164151GBUW93C35PZCR9WDBX)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164159NDMEMRHTKE18TQWQIX)

  4. 添加调度



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061916422535B9VFLZF49Y67MSKG)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164232UIKK3KTIXYLVYA636Z)

  5. 启动作业



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164257PCK3SIV0620E8TW0BC)

该作业是将 ORACLE 中 HR 模式迁移到 DM 中并修改模式名为 HR2 ：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164349HKBTZMQJIZI837KZYT)

到调度中配置的时间后，迁移开始执行并执行成功，HR2 模式如下图所示。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240619164423NGM1DAFCCK290CDJBN)

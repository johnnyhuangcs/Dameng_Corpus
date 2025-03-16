

# DM 控制台工具

## 一、 概述

DM 控制台工具是管理和维护数据库的基本工具。通过使用控制台工具，数据库管理员可以完成服务器参数配置、管理 DM 服务、脱机备份与还原、查看系统信息、查看许可证信息等功能。

控制台通过 dm.ini 配置文件来连接实例，该工具必须在数据库实例服务端运行。无法像达梦客户端（DM Manager）工具和达梦性能监视（DM Monitor）等工具进行远程连接。

Windows 平台安装完成后，在开始菜单中直接找到打开即可。

Linux 平台在服务器端执行 DM_HOME/tool/console 启动。Linux 中打开必须依赖于图形页面，如果 Linux 服务器安装了桌面环境，也可以像 Windows 平台一样通过开始菜单中找到该工具直接打开。

下图是 windows 环境中数据库安装完成后，开始菜单里面显示的工具项。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613142035VEKPF5S2I62IGWK4AJ)

## 二、 服务器配置

服务器配置功能下包含实例配置，支持多实例，右键【实例配置】选择【添加实例】即可添加新实例，服务器配置节点下面可以包含 0 个或多个实例。当每一个实例存在 ini 配置文件时，例如 dmmal.ini、dmarch.ini、sqllog.ini 等，则【实例配置】节点下会包含多个子 ini 节点。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240219102341NFXPT87WJZ8OD664WZ)

## 三、 控制台登录

在数据库安装完成并初始化后，控制台工具会自动连接当前注册的实例，并且该实例无法删除。如下图所示，DMSERVER 是当前数据库实例名称，右键只有刷新选项，无删除选项。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406131425223TKKC4WFTAKTCO7O4X)

如果新建实例未通过数据库助手注册系统服务，可以通过右键“实例配置”，添加新的实例。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613142627SFNV2DV0MD12OK7OXR)

例如添加一个新实例 dmtest，通过指定 dm.ini 路径进行连接，如果是 DSC 集群，还需要指定 dmdcr.ini 路径。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613143320AENAPD11F2X0FJ7RGJ)

点击确认后，完成实例连接。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613143421TE0YLPCHNA2Y5931PO)

如果不需要连接此实例，右键点击“删除实例”即可。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613143444VRRL5BDH5FWS4Q2LUE)

## 四、 实例参数查看与修改

在控制台工具中配置好实例后，可以直接查看和修改实例的参数。下图是所有参数分类：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061314372919D9RQ6UZEPN9PJIH3)

点开具体参数分类，即可查看到参数名称、当前值与参数描述。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613143800ET2HFNA38KBV4CW5U7)

双击参数对应的值，进入编辑模式，可以进行修改，如下图所示将“SORT_BUF_GLOBAL_SIZE”由 1000 修改为 800：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613143826A5JTTFTSWW0S6Y3TYF)

修改完成后点击“保存”，完成修改。如果不想修改，可以点击“重置”，即恢复到原来的值。

> **注意**
>
> 在控制台工具修改都是静态修改，即使该参数是动态参数也不会立即生效，需要重启数据库才能生效。



通过客户端查询该参数也可以看到，修改的是文件里的值。

```
select * from v$dm_ini where para_name='SORT_BUF_GLOBAL_SIZE';
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613143924DI9FJ6B13BXNOQP0SB)

另外此页面还提供了搜索功能，可以在查找框中快速找到需要的参数。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613143947LIAC2A2NRE200UNGBI)

## 五、 备份还原

管理脱机备份还原。备份还原页面主要管理脱机备份与还原功能（包括 rac ,mpp 库级备份）。为保证备份文件的数据完整性，必须确保数据库是正常关闭的。

### 5.1 备份集查看

可以通过指定备份目录，查看备份集信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406131442556FKIB75U52NV7UDSK1)

指定目录后，点击“获取备份”查看目录下有哪些备份集。可以看到，备份类型、时间等详细信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613144335P7HP4TBFHF3VIVUMA4)

### 5.2 新建备份

可以通过控制台工具新建备份，点击“新建备份”，如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613144421KXEO659919QS6XLHQT)

备份对象：库备份、归档备份。默认为库备份。

INI 文件路径：默认为当前实例 INI 文件路径。

备份名：备份集名称。

备份集路径：选择备份所在路径，这里需要注意，在 linux 下备份集路径需要对数据库安装用户有读写权限。

备份片限制大小：单个备份文件大小，默认无限制。

备份类型：完全备份、增量备份、数据库克隆。

如果选择增量备份，则还需要进行如下选项设置：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613144503UTPFC8YRLBTL0DZUFY)

默认是差异增量备份，可以通过勾选“累积”设置为累积增量备份。

由于增量备份需要一个全备作为基础备份。可以有如下三种选择：

  1. 使用最近一次备份作为基备份。即在“备份集路径”目录下搜索最新的全备作为基备份。
  2. 基备份集搜索目录。添加指定的备份集路径，在目录下搜索最新的全备作为基备份。
  3. 基备份集目录。指定具体的全备作为基备份。



备份描述：添加对该备份的具体描述。

常规选项设置完成后，点击确定即可进行备份，如果实例未关闭，则会备份失败。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061314461603NIZ7EU53W6D8XVC4)

把实例关闭后，重新备份成功。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406131446499DRNBTM7AQ0NE62L6T)

备份完成后，在对应目录下，可以看到备份文件。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613144731DC1279ZTWA8X3W84T6)

在“高级”选项中，还可以设置“ 备份压缩”、“备份日志”、“加密类型”等参数，如下图所示，可以按照需求配置对应参数。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061314480371GXPLRAN6P58Y4Q8Q)

归档备份

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613144837WR226D1I2D25NBMUFY)

INI 文件路径、备份集名、备份集目录、备份片限制大小与库备份设置相同。

备份类型：

1.备份所有归档，备份指定 LSN，备份指定时间点。

2.备份指定 LSN，指定备份的开始 LSN 和截止 LSN，进行归档备份

3.备份指定时间点，指定备份的开始时间点和截止时间点，进行归档备份

备份归档过滤：满足指定条件的归档文件不再备份。包括:所有已经备份过的归档文件，归档文件已经备份的次数，指定时间之后已经备份的归档文件

备份完删除归档：选择备份完成时是否删除归档。

备份描述：对备份文件的描述，用户可以根据需要填写。

### 5.3 校验备份集

在备份还原管理界面中，点击校验备份集按钮，打开校验备份集对话框。校验备份集

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613145020S8BEASJ2EZ9ITEVOE7)

如果选择校验备份集，则需要配置以下参数：

备份集目录：必填，指定待校验备份集路径。

介质类型：指定存储备份集的设备类型，目前暂支持磁盘和磁带，默认选择磁盘。

介质参数：使用第三方介质类型（磁带类型）时使用的参数，

INI 文件路径：数据库的 dm.ini 文件路径，若指定，则该数据库的默认备份目录将作为备份集搜索目录之一。

### 5.4 数据还原

可以通过控制台工具进行数据还原，点击“还原”，如下图：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613145210BFURX8X0D6SCEIJ3H2)

INI 文件路径：要进行还原操作的目标数据库实例的 DM.INI 路径。

使用备份集的 dm.ini 的参数：选中该选项，则会将备份集中备份的 dm.ini 中除路径相关的 INI 参数外，均拷贝到当前 dm.ini 上。

还原后库的名称：指定还原数据库后是否更改库的名字。

还原时覆盖已存在文件：若未指定，若库路径所在路径中存在待还原的库配置文件，则报错；若指定，则将已经存在的文件删除重建。

给未使用的数据页分配磁盘：指定还原数据库时不再为数据文件尾部未使用的数据页分配磁盘。

文件自动扩展：还原数据库时设置文件自动拓展，避免指定 WITHOUT SPACE 关键字后，由于源库未设置文件自动拓展，从而导致恢复过程或从 DDL_CLONE 库还原后更新 DB_MAGIC 时存储空间不足。

高级选项中有如下设置项，可以根据需要进行设置。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613145330EMS27XW7G53UGIJEWL)

基备份集搜索目录：用于增量备份中，指定基备份集搜索目录，通过点击右侧的添加按钮和删除按钮来增删基备份搜索目录。同备份中的基备份集搜索目录。

映射文件：指定存放还原目标路径的文件。通过点击浏览按钮指定映射文件，如果映射文件没有生成，可以点击生成 按钮根据指定的备份集，ini 文件路径以及映射文件路径生成映射文件，如果映射文件已经存在可以通过点击编辑按钮打开编辑映射文件对话框 编辑映射文件的内容。

介质类型：指存储备份集的设备类型，暂支持磁盘和磁带，默认磁盘。

介质参数：供第三方存储介质管理使用。

还原密码：指定备份时使用的加密密码，供还原过程解密使用。

加密算法：指定备份时使用的加密算法，供还原过程解密使用，若未指定，则使用默认算法。

任务线程数：指定还原过程中用于处理解压缩和解密任务的线程个数，若未指定，则默认为 4，上限 64。线程数设置较大的话，则消耗的内存比较多，导致一次申请的内存比较多，使用时应根据实际机器内存情况调整。

WITH CHECK：指定还原前校验备份集数据完整性。缺省不校验。

另外还可以进行归档和表空间还原，归档还原支持还原到指定的 LSN 或者指定的时间点。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613145451L2EPAB3G557KIB7ADM)

### 5.5 数据恢复

达梦控制台工具支持库恢复与表空间恢复。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613163302GGVFPFH7Z8XMUX49IO)

INI 文件路径：要进行恢复操作的目标数据库实例的 DM.INI 路径。

备份集目录：指定使用备份集恢复的目标备份集目录，若备份集为联机且 WITHLOG=TRUE，则恢复失败。

介质类型：指存储备份集的设备类型。

介质参数：供第三方存储介质管理使用。

恢复密码：指定使用备份集备份时使用的加密密码，供恢复过程解密日志使用。

加密算法：指定使用备份集备份时使用的加密算法，供恢复过程解密日志使用，若未指定，则使用默认算法。
也可以指定归档进行恢复。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613163339TZSWUSIET954BWUQUO)

INI 文件路径：要进行恢复操作的目标数据库实例的 DM.INI 路径。

归档日志目录：包括本地归档日志所在目录以及多站点归档日志恢复环境中，对应各站点的站点编号，每个站点可指定自己的归档目录和重做归档的起始结束点。通过添加和删除归档日志目录。

数据库 MAGIC：指定本地归档日志对应数据库的数据库 magic，若不指定，则默认使用目标恢复数据库的数据库 magic。

恢复时间：指定重做本地归档结束时间。

恢复 LSN 号：指定重做本地归档指定起始或结束 LSN 值。

借助归档日志恢复，主要有两种类型：

1.没有经过还原操作，直接在目标库上执行恢复操作，这种恢复，主要针对一些已经滞后的库，用最新的归档，把目标库恢复到某个时间点，或者恢复到最新的状态。

2.经过了还原操作，这个时候，需要借助归档，把目标恢复到一个指定的时间点。可能还原的备份集是 without log 的备份集，没有办法执行从备份集恢复，或者希望恢复到更新的状态，而从备份集恢复，又不能够满足要求，所以需要借助归档，恢复到某个时间点。在未指定恢复到的时间或者恢复到 LSN 时，则默认恢复到最新状态，有多少归档就会重做多少。

表空间恢复

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613163840AXA2ENC0JF1N3P27QD)

INI 文件路径：要进行恢复操作的目标数据库实例的 DM.INI 路径。

表空间名：要进行恢复操作的目标表空间名。

归档日志目录：包括本地归档日志所在目录以及多站点归档日志恢复环境中，对应各站点的站点编号，每个站点可指定自己的归档目录和重做归档的起始结束点。通过添加 和删除 归档日志目录。

数据库 MAGIC:指定本地归档日志对应数据库的数据库 magic，若不指定，则默认使用目标恢复数据库的数据库 magic。

> **注意**
>
> 普通恢复和更新 MAGIC
> 恢复这两种恢复，都是需要，首先对目标库执行还原操作，才能够执行的恢复。而指定归档恢复，允许不执行还原操作，直接执行恢复操作。也允许先执行还原操作，在执行恢复操作。所以，指定归档恢复比较灵活，只要用户有完整的归档即可。



### 5.6 更新 db_magic

更新 DB_Magic 对话框主要用于更新 db_magic，恢复到备份的时间点。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613164108LF2VKC71YGIC4MHCQO)

点击“确定”，完成更新即可。

## 六、 归档修复

当数据库出现归档不一致的情况，可以通过指定 INI 文件，执行归档修复操作。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061316415011WFYPPI7GAASPBTM1)

点击“确定”完成修复即可。

## 七、 生成映射文件

映射文件，又称为 mapped file。备份集映射文件导出，是备份管理的主要功能，是将备份集中各数据文件的原始路径或者调整后的路径生成到一个本地文件中。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613164249EWH89A669TFDG7M0XR)

备份集目录：待生成映射文件的备份集目录。

介质类型：指存储备份集的设备类型，暂支持磁盘和磁带，默认磁盘。

介质参数：供第三方存储介质管理使用。

INI 文件路径：备份集还原到目标库的 INI 路径。

库目录：数据库目录中 SYSTEM.DBF 数据文件所在目录，作为数据库系统目录处理。

映射文件：生成映射文件路径。

生成文件如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202406131643565DZ5V8E4GPU03L5WJP)

通过修改映射文件可以将数据库文件还原到指定的目录下。

下面是使用映射文件进行还原的一个示例。

选择一个备份集。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613164545BF9DGMF9IFO4AY30S7)

点击浏览选择映射文件。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613164616JEUYASBNQQXO3E4LQH)

修改 SYSTEM.DBF 文件路径，将原先 DAMENG 路径修改为 dbdata 路径。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613164901IVH9AWSC8FHQDI983C)

设置完成后，开始还原。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613165010KGZ10Y0MYJSB9VGGWY)

还原完成后，可以看到 SYSTEM.DBF 已被还原到 dbdata 目录下。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613165054SG6VAC0RHTVPQXAL2M)

映射文件里面指定的路径无需提前创建，在还原的时候会自动创建目录。

## 八、 备份属性

可以点击“属性”查看备份集相关信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024061316514788V5Z6394OEBIOG07Z)

详细信息如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613165217D1UA3EVP83E72FATI7)

## 九、 备份删除

选中对应的备份后，点击删除即可进行备份删除。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613165256GQX3RGBORCFGFCU56T)

系统信息

系统信息页显示数据库实例的信息，包括页大小、簇大小、大小写敏感等相关设置。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613165334ZSJVTPQHVN8RMN7LWG)

## 十、 许可证信息

许可证信息页显示许可证的相关信息，包括许可证版本号、产品序列号、产品版本、有效日期和是否激活等相关信息。

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240613165410H0EFS9PDVZCKGQ1NN0)

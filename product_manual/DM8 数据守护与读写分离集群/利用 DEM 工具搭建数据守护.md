

# 利用 DEM 工具搭建数据守护

DM web 版数据库管理工具(DEM)提供了针对数据守护集群的图形化部署与监控功能。DEM 支持部署与监控的数据守护集群类型有四种：实时主备、读写分离、大规模并行处理主备（MPP 主备）和共享存储主备（DMDSC 主备）。

本章以实时主备为例，来介绍数据守护的部署与监控。关于 DEM 工具的更多使用说明请参考 DEM 工具帮助文档。

## 8.1 实时主备管理

实时主备管理分为两大步骤：部署和监控。在使用 DEM 对集群进行部署和监控之前需要在各节点所在机器部署 DM 数据库代理工具 dmagent，关于 DM 数据库代理工具 dmagent 的部署与使用，请查看相关文档。

### 8.1.1 部署

启动 DEM 进入 DEM 主界面，点击左侧导航栏的集群搭建按钮进入集群搭建界面，如下图所示：

![图 8.1 集群部署界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023122710384844H7NF9RVIYRME1G59)

图8.1 集群搭建界面

点击“+”按钮，打开新建集群搭建的对话框，如下图:

![图 8.2 新建集群部署对话框.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202312271039383T7RAO7YAEO7OD7MGJ)

图8.2 新建集群搭建对话框

给定集群搭建名称和集群搭建描述，点击“确定”以后，进入“选择集群类型”界面：

![选择部署集群类型](https://download.dameng.com/eco/docs/asset/pm/dm8-data-watch/图8.3-选择部署集群类型.png)

图8.3 选择集群类型

选择集群类型为“数据守护 v4.0”下的“实时主备”，点击“下一步”后，进入“环境准备”界面：

![部署界面-环境准备](https://download.dameng.com/eco/docs/asset/pm/dm8-data-watch/图8.4-部署界面-环境准备.png)

图8.4 集群搭建-环境准备

参数详解：

刷新：刷新主机列表。

搜索：输入 IP 地址，快速搜索指定主机。

选择要部署集群的主机，点击“下一步”，进入“实例规划”界面：

![部署界面-实例规划](https://download.dameng.com/eco/docs/asset/pm/dm8-data-watch/图8.5-部署界面-实例规划.png)

图8.5 集群搭建-实例规划

参数详解：

部署名称：部署名称，会在各部署主机的工作目录创建对应名称的目录来存储该集群。

参数配置：统一配置实例列表中所有实例的参数。另外也可以通过双击实例列表中某一个实例的某一个参数进行单独配置。

添加实例：添加实例，可以在选择的主机中添加实例，添加之后会出现在实例列表中。

删除实例：删除选中的实例。

注册服务：如果想让 dmserver、dmwatcher 和 dmmonitor 服务开机自启动，则需把 dmserver、dmwatcher 和 dmmonitor 注册成服务，注册完成后重启机器时，就会自动启动 dmserver、dmwatcher 和 dmmonitor 服务。如果选择了注册服务，该部署工具只会把 dmserver，dmwatcher，确认监视器注册成服务，普通监视器不注册服务。

配置脚本名：配置注册的脚本名，默认 DMSERVER 脚本名为 DmService_实例名，DMAP 脚本名为 DmAPService_集群名，DMWATCHER 脚本名为 DmWatcherService_实例名，确认监视器服务名为 DmMonitorService_集群名。

在配置好实例的分布，以及规划好端口后，点击“下一步”，进入“主备关系配置”界面：

![部署界面-主备关系配置](https://download.dameng.com/eco/docs/asset/pm/dm8-data-watch/图8.6-部署界面-主备关系配置.png)

图8.6 集群搭建-主备关系配置

参数详解：

组名：可修改守护组名。

添加主库：从配置的实例中选择实例作为主库。

添加备库：从配置的实例中选择实例作为主库。

删除主/备库：删除实例的主/备库配置。

在配置好实例的分布，以及规划好端口后，点击“下一步”，进入“数据准备”界面：

![部署界面-数据准备](https://download.dameng.com/eco/docs/asset/pm/dm8-data-watch/图8.7-部署界面-数据准备.png)

图8.7 集群搭建-数据准备

参数详解：

可以通过点击实例列表的不同实例来切换当前正在配置的实例。可在编辑中对数据库实例的初始化参数进行配置，并支持自定义库初始化参数。

同步修改所有实例：勾选则会将当前配置的初始化库参数同步至该集群的所有实例。

![图 8.8 dm.ini 配置界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725160846UAM0FEZ3GO7R2FE978)

图8.8 dm.ini配置界面

参数说明：

在面板的上面表格中显示各实例的 dm.ini 参数配置，选择每个实例可以在下面编辑面板中编辑对应实例的每一个 dm.ini 参数。

同步修改同一组的其它实例：修改的参数，同步应用到所选择的实例的同组的其它实例。

应用到其它实例：可以选择哪些参数的修改应用到哪些实例上。

具体 dm.ini 参数的配置参考 [5.1 dm.ini](https://eco.dameng.com/document/dm/zh-cn/pm/configuration-description.html#5.1%20dm.ini)。

点击“下一步”，进入“dmmal.ini 配置”界面，配置 dmmal.ini 相关属性。

![图 8.9 dmmal.ini 配置界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023072516091186PA55TQS517223ME1)

图8.9 dmmal.ini配置界面

参数说明：

具体 dmmal.ini 参数的配置参考 [5.2 dmmal.ini](https://eco.dameng.com/document/dm/zh-cn/pm/configuration-description.html#5.2%20dmmal.ini)。

点击“下一步”，进入“dmarch.ini 配置”界面，配置 dmarch.ini 的属性，异步备库需要配置定时器信息。

![图 8.10 dmarch.ini 配置界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725160935I515N3Q1XYEK8PJD95)

图8.10 dmarch.ini配置界面

参数说明：

在面板的上面表格中显示各实例的 dmarch.ini 参数配置，选择每个实例可以在下面编辑面板中编辑对应实例的每一个 dmarch.ini 参数。

应用到其它实例：可以选择哪些参数的修改应用到哪些实例上。

具体 dmarch.ini 参数的配置参考 [5.3 dmarch.ini](https://eco.dameng.com/document/dm/zh-cn/pm/configuration-description.html#5.3%20dmarch.ini)。

点击“下一步”，进入“dmwatcher.ini 配置”界面，配置 dmwatcher.ini 的属性。

![图 8.11 dmwatcher.ini 配置界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725161001VUVOJR9ZEVZ7SWIQ9B)

图8.11 dmwatcher.ini配置界面

参数说明：

在面板的上面表格中显示各实例的 dmwatcher.ini 参数配置，选择每个实例可以在下面编辑面板中编辑对应实例的每一个 dmwatcher.ini 参数。

同步修改同一组的其它实例：修改的参数，同步应用到所选择的实例的同组的其它实例。

应用到其它实例：可以选择哪些参数的修改应用到哪些实例上。

具体 dmwatcher.ini 参数的配置参考 [5.4 dmwatcher.ini](https://eco.dameng.com/document/dm/zh-cn/pm/configuration-description.html#5.4%20dmwatcher.ini)。

点击“下一步”，进入“监视器 dmmonitor 配置”界面，配置监视器相关属性。

![图 8.12 监视器配置界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725161025582KWQAAZNMKMZGL19)

图8.12 监视器配置界面

参数说明：

监视器启用：选择将要启用的监视器类型。

监视器主机：选择要将监视器部署到那个主机上。

监视器工作空间：配置监视器部署在主机的工作空间。

启动监视器：选择部署完成后，是否启动监视器。

其它监视器参数的配置参考 [5.5 dmmonitor.ini](https://eco.dameng.com/document/dm/zh-cn/pm/configuration-description.html#5.5%20dmmonitor.ini)。

点击“下一步”，进入“上传服务器文件”界面，请选择上传的服务器文件。

![图 8.13 上传服务器文件.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725161052SAOCQHS1YPFSJUEEJC)

图8.13 上传服务器文件

参数说明：

各节点将使用同一个达梦数据库服务器文件：选择该选项，则为所有为 Linux 的主机上传一份服务器文件，为所有为 Windows 的主机上传一份服务器文件。

各节点将单独配置达梦数据库服务器文件：为每一个主机单独上传服务器文件。

各节点已部署达梦数据库服务器文件：填写节点的可执行文件 dmserver.exe 的文件目录。

使用 ssl 通讯加密：如果上传的服务器是使用 ssl 通讯加密的，则需要上传客户端 SYSDBA 的 ssl 密钥文件，和输入 ssl 验证密码。

点击“下一步”，进入“详情总览”界面。列出将要部署的集群环境的所有配置信息：

![图 8.14 详情总览.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202307251611463B8G2NW8RIBA9WFCMO)

图8.14 详情总览

点击“下一步”，开始执行部署任务。执行过程如下：

![图 8.15 执行部署任务界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725161211V2S8X5Q4SK5YO530M4)

图8.15 执行部署任务界面

参数说明：

刷新：刷新任务列表。

查看部署信息：对部署配置信息进行详细浏览。

停止所有任务：停止执行所有任务。

回滚所有任务：执行结束后，可以回滚所有执行的任务，清除环境。

重做失败任务：重新执行失败的任务。

部署任务执行完成后显示如下：

![图 8.16 部署任务执行完成界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725161233HQO0KA3IPSTYXILA4Y)

图8.16 部署任务执行完成界面

参数说明：

重新配置：重新对集群进行配置。

添加到监控：把该数据守护环境添加到监控中，自动跳转到“集群添加”界面，点击“确定”后，该集群被添加到监控面板。

至此，数据守护搭建完成。

### 8.1.2 监控

在 DEM 主界面，点击左侧导航栏的资源监控按钮进入资源监控界面，点击“数据库监控”进入数据库监控界面，如下图所示：

![图 8.17 数据库监控界面.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20231227104043YMW4HZ63N3OE07J7BU)

图8.17 数据库监控界面

点击“+”按钮，下拉选择“集群”。打开集群添加对话框：

![图 8.18 集群添加对话框.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725161340A9AU1PWWSI8GE8QED1)

图8.18 集群添加对话框

给定集群名称，选择集群类型“DW”，点击“站点扫描”，输入主库的连接信息，即可扫描出对应的集群节点，点击“确定”，把该集群添加到数据库监控页面，则可以对该集群进行监控管理。

![图 8.19 集群监控页面-连接节点.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230725161417VDMXTDFL5GQV8AL4K2)

图8.19 集群监控页面-连接节点

对集群的监控与管理操作包括启动/停止集群，负载分析等，具体操作请查看 DEM 帮助手册。

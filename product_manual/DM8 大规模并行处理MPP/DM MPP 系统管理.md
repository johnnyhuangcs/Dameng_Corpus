

# DM MPP 系统管理

DM web 版数据库管理工具（DEM）提供了图形化部署与监控 DM MPP 的功能。数据库管理员对规划好 DM MPP 系统的机器与端口，就可以使用 DEM 的集群部署功能，根据页面的指示一步步地搭建好 DM MPP 环境。部署完成后还可以使用 DEM 的集群监控功能对 MPP 集群的运行情况进行监控。

本章还介绍了 MPP 环境的 DDL 克隆与还原。

## 5.1 使用 DEM 部署与监控 DM MPP

DEM 提供 DM MPP 的图形化搭建与管理功能。关于 DEM 工具的启动，请查看 DEM 相关文档。DEM 启动后，通过浏览器访问，如下图所示：

![DEM 工具主界面](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.1DEM工具主界面.png)

图5.1 DEM工具主界面

在 DEM 中，对于集群的管理包括集群部署和集群监控。集群部署在左导航栏点击“集训部署”可以打开，集群监控在“资源监控”栏的“数据库监控”中添加对集群的监控即可打开。

> **建议**
>
> 在使用DEM对集群进行部署和监控之前需要在各节点所在机器部署DM数据库代理工具DMAgent。



### 5.1.1 使用 DEM 部署 DM MPP

在 DEM 的“客户端工具”栏，选择部署集群工具，打开新建集群部署的对话框，如下图所示：

![新建集群部署对话框](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.2新建集群部署对话框.png)

图5.2 新建集群搭建对话框

输入集群名称，点击“确定”后，进入“选择部署集群类型”界面：

![选择部署集群类型](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.3选择部署集群类型.png)

图5.3 选择集群类型

选择集群类型为“大规模并行处理”，点击“下一步”后，进入“环境准备”界面：

![MPP 部署-环境准备](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.4MPP部署-环境准备.png)

图5.4 MPP部署-环境准备

参数详解：

刷新：刷新主机列表。

搜索：输入 IP 地址，快速搜索指定主机。

选择要部署 MPP 的主机，点击“下一步”，进入“实例规划”界面：

![MPP 部署-实例规划](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.5MPP部署-实例规划.png)

图5.5 MPP部署-实例规划

参数详解：

部署名称：部署名称，会在各部署主机的工作目录创建对应名称的目录来存储该集群。

参数配置：统一配置实例列表中所有实例的参数。另外也可以通过双击实例列表中某一个实例的某一个参数进行单独配置。

添加实例：添加实例，可以在选择的主机中添加实例，添加之后会出现在实例列表中。

删除实例：删除选中的实例。

注册服务：如果想让 dmserver 服务开机自启动，则需把 dmserver 注册成服务，注册完成后重启机器时，就会自动启动 dmserver 服务。

配置脚本名：可以在此配置注册的脚本名。默认 DMSERVER 脚本名为 DmService_脚本名；DMAP 脚本名为 DmAPService_部署名称。

在配置好各实例的参数后，点击“下一步”，进入“数据准备”页面。

![MPP 部署-数据准备](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.6MPP部署-数据准备.png)

图5.6 MPP部署-数据准备

可以通过点击对应的主机 IP 切换设置的示例。

参数详解：

自定义库初始化参数：在此用户可以添加自定义参数。

同步修改所有实例：勾选后可将该实例的配置同步配置至该集群的其他所有实例上。

应用到其他实例：点击后可以在对话框中勾选需要同步的参数，并设置应用到的对象。

备好数据之后，点击“下一步”，进入“dm.ini 配置”界面，配置 dm.ini 相关参数。

![MPP 部署-dm.ini 配置](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.7MPP部署-dm.ini配置.png)

图5.7 MPP部署-dm.ini配置

可以在这个页面中对 dm.ini 的相关参数进行配置。

点击“下一步”，进入“dmmal.ini 配置”界面，配置 dmmal.ini 相关参数。

![MPP 部署-dmmal.ini 配置](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.8MPP部署-dmmal.ini配置.png)

图5.8 MPP部署-dmmal.ini配置

可以在这个页面中对 dmmal.ini 的相关参数进行配置。

点击“下一步”，进入“dmarch.ini 配置”界面，配置 dmarch.ini 参数。

![MPP 部署-dmarch.ini 配置](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.9MPP部署-dmarch.ini配置.png)

图5.9 MPP部署-dmarch.ini配置

MPP 环境下并不要求必须配置归档，但用户也可以根据应用实际需要进行配置，选中“是否配置归档”选择框，即可进行配置。

点击“下一步”，进入“上传服务器文件”界面，选择上传的服务器文件。

![MPP 部署-上传服务器文件](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.10MPP部署-上传服务器文件.png)

图5.10 MPP部署-上传服务器文件

参数说明：

各节点将使用同一个达梦数据库服务器文件：选择该选项，则为所有为 Linux 的主机上传一份服务器文件，为所有为 windows 的主机上传一份服务器文件。

各节点将单独配置达梦数据库服务器文件：为每一个主机单独上传服务器文件。

使用 ssl 通讯加密：如果上传的服务器是使用 ssl 通讯加密的，则需要上传客户端 SYSDBA 的 ssl 密钥文件，和输入 ssl 验证密码。

点击“下一步”，进入“详情总览”界面。列出将要部署的集群环境的所有配置信息：

![MPP 部署-详情总览](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.11MPP部署-详情总览.png)

图5.11 MPP部署-详情总览

点击“保存并执行”，开始执行部署任务。执行过程如下：

![MPP 部署-执行部署任务](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.12MPP部署-执行部署任务.png)

图5.12 MPP部署-执行部署任务

参数说明：

刷新：刷新任务列表。

查看部署信息：对部署配置信息进行详细浏览。

停止所有任务：停止执行所有任务。

回滚任务：执行结束后，可以回滚所有执行的任务，清除环境。

重做失败任务：重新执行失败的任务。

等待一段时间，只要之前的配置都没有错误，就能成功完成 MPP 的部署了，如下图所示：

![MPP 部署-完成执行部署任务](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.13MPP部署-完成执行部署任务.png)

图5.13 MPP部署-完成执行部署任务

至此，DM MPP 环境部署完成，如果点击“添加到监控”可直接将配置好的 MPP 集群添加到监控中。

### 5.1.2 使用 DEM 监控 DM MPP 运行

在 DEM 左导航栏上点击“资源监控”，在上方点击数据库监控，打开数据库监控面板，下图展示的是已加入资源监控的数据库列表。

![数据库监控面板](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.14数据库监控面板.png)

图5.14 数据库监控面板

选择工具栏形状为“+”的添加按钮，下拉选择“集群”。打开集群添加对话框：

![集群添加对话框](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.15集群添加对话框.png)

图5.15 集群添加对话框

添加好需要监控的 MPP 集群后，点击“确定”，就可以对 MPP 集群进行监控了。

![集群监控面板](https://download.dameng.com/eco/docs/asset/pm/dm8-mpp/图5.16集群监控面板.png)

图5.16 集群监控面板

对集群的监控与管理操作包括集群分析，集群管理，启动/停止集群，sql 监控，表监控等，具体操作请查看 DEM 联机帮助。

## 5.2 MPP 环境的 DDL 克隆与还原

DDL 克隆可以把系统中的对象定义信息进行备份，之后再还原到别的数据库节点。利用这个功能，可以将一个 MPP 系统的对象定义信息克隆并还原到另一个 MPP 系统中，且两个 MPP 系统的节点数不必一致。

需要注意的是，由于 MPP 环境下执行 DDL 克隆时会将 dmmpp.ctl 也一并备份还原，若还原的 MPP 环境与备份环境不一致时，会导致还原后的 MPP 系统不能正确启动。因此在执行完还原操作后，需要使用使用 dmctlcvt 工具，将当前环境的 dmmpp.ini 再次生成 dmmpp.ctl，替换还原生成的 dmmpp.ctl。

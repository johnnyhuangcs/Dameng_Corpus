

# DM 服务查看器

## 一、概述

DM 服务查看器，顾名思义是对数据库服务进行查看管理的工具。通过服务查看器服务可关闭，开启，重启，查看数据库各个服务的状态，方便快捷的对数据库实例服务进行管理。

数据库实例服务运行安装在操作系统上，通常系统运行时数据库服务的状态要保持运行状态。数据库出现异常可以通过服务查看器来查看数据的状态，手动进行服务的重启和关闭等。更换硬件、系统升级等操作，需要提前停止数据库服务，防止出现故障。

## 二、Windows 环境

### 2.1 启动 DM 服务查看器

点击【开始界面】，选择【达梦数据库】，点击【DM 服务查看器】，即可启动服务查看器，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240301140245YEGQHMTDEKGNAPMAVB)

选中服务，鼠标右键即可对服务进行启动，停止，修改，注册等操作，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240130143453CNM00JCJX9BQGXMYP9)

### 2.2 注册服务

可以通过以下方式图形化注册服务。以 DmWatch 服务为例，进入 DM 服务查看器，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202407241452587YNHV5GUO5Q9283PZ5)

右击上图空白处，选择【注册服务】，进入如下图所示界面：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240723145820S3IMLPBL1CT8CZSXLV)

在上图注册服务界面，输入服务名称后缀，选择服务启动类型为自动或者手动，选择 dmwatcher.ini 所在目录，点击确定按钮。

## 三、Linux 环境

### 3.1 启动 DM 服务查看器

Linux 图形化界面使用的时候，同样支持 DM 服务查看器的图形化方式启动，如同 Windows 服务查看器方式进行管理。

启用图形界面前需要通过如下方法将图形界面权限放开：

```
[root@localhost mnt]# xhost +
access control disabled, clients can connect from any host
[root@localhost mnt]# echo $DISPLAY
[root@localhost mnt]# su - dmdba
Last login: 四 1月 25 16:41:51 CST 2024 on pts/1
[dmdba@localhost ~]$ export DISPLAY=:0.0
```

进入数据库安装目录的 tool 目录，启动数据库服务查看器，如下所示：

```
[dmdba@localhost ~]$ cd /home/dmdba/dmdbms/tool/
[dmdba@localhost tool]$ ll
[dmdba@localhost tool]$ ./dmservice.sh
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024012516492348VDPQ6PFJDEIQ7VGO)

图形化界面与 Windows 环境一致，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401301438212Y3FUZTMRZ9HEQCFWD)

### 3.2 注册服务

操作与 Windows 环境一致，可参考 2.2 章节。

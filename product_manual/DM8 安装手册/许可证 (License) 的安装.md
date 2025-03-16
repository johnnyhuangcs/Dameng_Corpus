

# 许可证 (License) 的安装

用户安装DM时，可导入相应的许可证(License)。License的载体是一个加密文件dm.key，内容是达梦公司对用户使用DM软件的授权。DM软件安装后，如果需要得到更多授权的用户，可联系达梦公司获取相应的License，并按照下面的操作方法进行安装。

## 3.1 Windows下License的安装

用户获得License文件dm.key后，首先将达梦服务器关闭，然后将dm.key复制到DM安装目录下，DM服务器所在的子目录中即可。

操作方法如下：

首先，打开DM服务器所在的目录。

例如，当DM安装目录为C:\dmdbms时，则DM服务器所在的目录为C:\dmdbms\bin。然后，先将达梦服务器关闭，再将dm.key复制到该目录下，替换原来的dm.key文件即可。

说明：该目录下原有的dm.key文件请事先做好备份。

## 3.2 Linux(Unix)下License的安装

用户获得License文件dm.key后，首先将达梦服务器关闭，然后将dm.key复制到DM安装目录下，DM服务器所在的子目录中即可。这与Windows下License的安装类似。

操作方法如下：

首先，找到DM服务器所在的目录，方法是以root用户或安装用户登录到Linux系统，启动终端，执行以下命令即可进入DM服务器程序安装的目录：

```
##注：假设安装目录为/opt/dmdbms

cd /opt/dmdbms/bin
```

然后，先将达梦服务器关闭，再将dm.key文件拷贝到该目录，替换原有的dm.key即可。

说明：该目录下原有的dm.key文件请事先做好备份。

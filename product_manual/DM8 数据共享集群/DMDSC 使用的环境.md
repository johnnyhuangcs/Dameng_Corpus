

# DMDSC 使用的环境

部署DMDSC集群所用到的硬件和软件环境。

  * **硬件环境**



主机两台。用于部署数据库实例dmserver、DMCSS、DMASMSVR。内存大小要求：至少2GB。

共享存储。两台机器可以同时访问到的，可以划分为裸设备的磁盘。

网卡。每台主机至少准备2块网卡。提供内部网络和外部网络服务。

  * **软件环境**



操作系统。Linux、Unix、Windows等。

达梦数据库软件。安装好DM数据库软件之后，将拥有配置和管理DMDSC所需的所有软件：dmserver、dminit、dmasmcmd、dmasmsvr、dmasmtool、dmcss、dmcssm等。这些软件位于安装目录/dmdbms/bin中。

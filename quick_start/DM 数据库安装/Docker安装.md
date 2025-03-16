

# Docker安装

## 一、安装前准备

|**软硬件**|**版本**|
|---|---|
|终端|X86-64 架构|
|Docker|2024 年 4 月版|


## 二、下载 Docker 安装包

请在达梦数据库官网下载 [Docker 安装包](https://eco.dameng.com/download/)。

## 三、导入安装包

拷贝安装包到 /opt 目录下，执行以下命令导入安装包：

```
docker load -i dm8_20240422_x86_rh6_64_rq_std_8.1.3.100_pack2.tar
```

结果显示如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605162740LFE4UFKW64AWT76VSE)

导入完成后，可以使用 `docker images` 查看导入的镜像。结果显示如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605162830JB2SEQO5AVH7K30J38)

## 四、启动容器

镜像导入后，使用 `docker run` 启动容器，启动命令如下：

```
docker run -d -p 30236:5236 --restart=always --name=dm8_test --privileged=true -e LD_LIBRARY_PATH=/opt/dmdbms/bin -e PAGE_SIZE=16 -e EXTENT_SIZE=32 -e LOG_SIZE=1024 -e UNICODE_FLAG=1  -e INSTANCE_NAME=dm8_test -v /opt/data:/opt/dmdbms/data dm8:dm8_20240422_rev215128_x86_rh6_64
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605163754QDMVF4UEWEVBIJZO6U)

容器运行相关参数说明：

|**参数名**|**参数描述**|
|----|----|
|-d|-detach 的简写，在后台运行容器，并且打印容器 id。|
|-p|指定容器端口映射，比如 -p 30236:5236 是将容器里数据库的 5236 端口映射到宿主机 30236 端口，外部就可以通过宿主机 ip 和 30236 端口访问容器里的数据库服务。|
|--restart|指定容器的重启策略，默认为 always，表示在容器退出时总是重启容器。|
|--name|指定容器的名称。|
|--privileged|指定容器是否在特权模式下运行。|
|-v|指定在容器创建的时候将宿主机目录挂载到容器内目录，默认为/home/mnt/disks|


使用 -e 命令指定数据库初始化参数时，需要注意的是目前只支持预设以下九个 DM 参数。

|**参数名**|**参数描述**|**备注**|
|----|----|----|
|PAGE_SIZE|页大小，可选值 4/8/16/32，默认值：8|设置后不可修改|
|EXTENT_SIZE|簇大小，可选值 16/32/64，默认值：16|设置后不可修改|
|CASE_SENSITIVE|1:大小写敏感；0：大小写不敏感，默认值：1|设置后不可修改|
|UNICODE_FLAG|字符集选项；0:GB18030;1:UTF-8;2:EUC-KR，默认值：0|设置后不可修改|
|INSTANCE_NAME|初始化数据库实例名字，默认值：DAMENG|可修改|
|SYSDBA_PWD|初始化实例时设置 SYSDBA 的密码|可修改|
|BLANK_PAD_MODE|空格填充模式，默认值：0|设置后不可修改|
|LOG_SIZE|日志文件大小，单位为：M，默认值：256|可修改|
|BUFFER|系统缓存大小，单位为：M，默认值：1000|可修改|


> **注意**
>
> 1.SYSDBA_PWD 预设的时候，密码长度为 9~48 个字符，docker 版本使用暂不支持特殊字符为密码  
> 2.强烈建议用户在首次安装数据库初始化实例时，立即修改数据库系统用户的初始密码，并设置一定的密码强度，以保障数据安全性。  
> 3.-e 设置的时候 初始化参数必须使用大写，不可使用小写。



通过以下命令可以查看 Docker 镜像中数据库初始化的参数。

```
docker inspect dm8_test
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220200700EKQH36PH9IJIZHQ6E1)

找到 Env 项可以看到在数据库初始化时设置的参数值，包括页大小(PAGE_SIZE)、簇大小(EXTENT_SIZE)、字符集(UNICODE_FLAG)、密码等。更多数据库初始化实例参数解释可参考达梦数据库安装目录下 doc 目录中《DM8_dminit 使用手册》。

容器启动完成后，使用命令 `docker ps` 查看镜像的启动情况，结果显示如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605163938AGNELUN8AEPG6UFHYO)

启动完成后，可通过日志检查启动情况，命令如下：

```
docker logs -f  dm8_test
或
docker logs -f a1d3053287b2
```

结果显示如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605164051R5HLUC2ID98BRD7VWY)

## 五、启动/停止数据库

停止数据库命令如下：

```
docker stop  dm8_test
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605164211IBPCRFHSN61P6STTZY)

启动数据库命令如下：

```
docker start  dm8_test
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605164328U82ZAVZHYRQA6A4PB5)

重启命令如下：

```
docker restart  dm8_test
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605164511XCPBMKAU6ELEX2IRJR)

## 六、进入 DM8 容器连接数据库

通过以下命令进入容器：

```
docker exec -it dm8_test bash
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240605164756L6SL6885KS2QN7XTTZ)

```
./disql SYSDBA/*****
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241220193419VVPWQ3ZO4W7D7MZAK4)

> **注意**
>
> 如果使用 docker 容器里面的 disql，进入容器后，先执行 source /etc/profile 防止中文乱码。



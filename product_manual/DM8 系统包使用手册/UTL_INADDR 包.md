

# UTL_INADDR 包

为了在 PL/SQL 中，提供网络地址转换的支持，开发 UTL_INADDR 包，提供一组 API，实现主库的 IP 地址和主库名之间的转换。

## 26.1 相关方法

  1. GET_HOST_ADDRESS



依据给定的主库名返回其 ip 地址。返回给定 host 主库的 ip 地址（以 char 的形式展现）。

语法如下：

```
FUNCTION GET_HOST_ADDRESS (
	HOST 	IN 		VARCHAR2 	DEFAULT NULL
) RETURN VARCHAR2;
```

参数详解

  * HOST 主库名，VARCHAR2 类型。默认值为 NULL，用本机的主库名代替，表示获取本机的 IP 地址；



  2. GET_HOST_NAME



依据给定的 ip 地址返回主库名。

语法如下：

```
FUNCTION GET_HOST_NAME(
	IP 		IN 		VARCHAR2 	DEFAULT NULL
) RETURN VARCHAR2;
```

参数详解

  * IP IP 地址，VARCHAR2 类型。默认值为 NULL，用本机的 IP 地址代替，表示获取本机的主库名。



## 26.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'UTL_INADDR');
```

例 获得 192.168.0.30 机器的名称，以及名称为 TEST 机器的 IP 地址。

```
select UTL_INADDR.GET_HOST_NAME('192.168.0.30');
select UTL_INADDR.GET_HOST_ADDRESS('test');
```



# JBOSS 添加达梦数据源

## 一、前言

JBoss 是一个基于 JavaEE 的开源容器，遵循 LGPL 许可，意味着可以在任何商业应用中免费使用。

## 二、环境准备

### 2.1 达梦数据库安装

可访问达梦云适配中心[下载试用](https://eco.dameng.com/download/?_blank)，下载 DM8 数据库试用版并安装，安装步骤请参考[数据库环境准备](#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.2 JBOSS 下载

进入 [JBOSS 官网](https://developers.redhat.com/products/eap/download)进行下载。

## 三、JBOSS 安装配置步骤

下面给出 jboss-eap-8.0.0 版本的配置过程，提供参考。

### 3.1 解压 JBOSS 包

```
unzip jboss-eap-8.0.0.zip
```

### 3.2 添加达梦 JDBC 驱动包

```
cd /add/jboss/jboss-eap-8.0/modules/system/layers/base/com/
mkdir -p dm/main
cd /add/jboss/jboss-eap-8.0/modules/system/layers/base/com/dm/main
cp /opt/dmdbms/drivers/jdbc/DmJdbcDriver18.jar  /add/jboss/jboss-eap-8.0/modules/system/layers/base/com/dm/main/
```

### 3.3 添加配置文件

```
cd /add/jboss/jboss-eap-8.0/modules/system/layers/base/com/dm/main
vi module.xml

<module xmlns="urn:jboss:module:1.3" name="com.dm">
    <resources>
        <resource-root path="DmJdbcDriver18.jar"/>
    </resources>
    <dependencies>
        <module name="javax.api"/>
        <module name="javax.transaction.api"/>
    </dependencies>
</module>
```

### 3.4 创建用户

```
cd /add/jboss/jboss-eap-8.0/bin
./add-user.sh
```

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926143453BQ8Y3BFF5XQYGQVYQD)

### 3.5 修改监听地址和端口

```
cd /add/jboss/jboss-eap-8.0/standalone/configuration
//修改 ip 地址为本地 ip
vi standalone.xml
```

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202409261435519JXM6QSS4JT3856VSA)

## 四、添加达梦数据源

### 4.1 启动 JBOSS

```
cd /add/jboss/jboss-eap-8.0/bin/
./standalone.sh
```

### 4.2 使用浏览器登陆

地址为 standalone.xml 配置文件中的 ip 地址，默认端口号为 9990 。

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202409261445587TDJJQ2JFPBT8ZNWU5)

### 4.3 添加达梦 JDBC 驱动

![图片 4.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926144645F1MWIXIXTE0R0O1MP6)

![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2024092614471271X92M21D6W78QUL57)

内容最终如下：

![图片 6.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926144749HXDULHV3GNPSAFH329)

### 4.4 添加达梦数据源

![图片 7.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926144824SZL7QKWR7TP5OIMII2)

![图片 9.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926144938KOCV2EGT1OVQYBVV4Z)

![图片 10.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202409261450560N0SXJ9HS8H9D4LUJR)

![图片 11.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202409261453500D9K0XYUT7REUM0KQH)

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241223120023WE5M10A5CG3SR96DFC)

测试连接，出现下面图片，则连接成功。

![图片 13.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926145709G0W6XQ37F04JRFNDUP)

![图片 14.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926145730EM8535HCG5SOV5Y61N)

![图片 15.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926145856NVOU9WRPQXEK7ADO73)

![图片 16.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926145911ZYL4ZJA9348JFPSM9I)

内容最终如下：

![图片 17.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240926150116TYGWUXFJ64XXRBXT3L)

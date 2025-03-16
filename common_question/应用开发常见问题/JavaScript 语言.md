

# JavaScript 语言

## 前言

本章节主要介绍 JavaScript 应用开发常见问题，为用户提供 JavaScript 应用开发常见问题的分析和解决思路。除此之外，用户还可前往[达梦技术社区](https://eco.dameng.com/community/question/)参与更多问题讨论。

## 目录

  * [Npm 代理问题](https://eco.dameng.com/document/dm/zh-cn/faq/faq-JavaScript-new.html#Npm%20%E4%BB%A3%E7%90%86%E9%97%AE%E9%A2%98)
  * [安装 dmdb 时 network 相关报错](https://eco.dameng.com/document/dm/zh-cn/faq/faq-JavaScript-new.html#%E5%AE%89%E8%A3%85%20dmdb%20%E6%97%B6%20network%20%E7%9B%B8%E5%85%B3%E6%8A%A5%E9%94%99)
  * [Error: Cannot find module ​'dmdb​'](https://eco.dameng.com/document/dm/zh-cn/faq/faq-JavaScript-new.html#Error:%20Cannot%20find%20module%20'dmdb')
  * [Node.js 怎么连接达梦数据库](https://eco.dameng.com/document/dm/zh-cn/faq/faq-JavaScript-new.html#Node.js%20%E6%80%8E%E4%B9%88%E8%BF%9E%E6%8E%A5%E8%BE%BE%E6%A2%A6%E6%95%B0%E6%8D%AE%E5%BA%93)
  * [node.js+electron 适配 DM 报错: ”Error: [6001] 网络通信错误异常​"](https://eco.dameng.com/document/dm/zh-cn/faq/faq-JavaScript-new.html#node.js+electron%20%E9%80%82%E9%85%8D%20DM%20%E6%8A%A5%E9%94%99:%20%E2%80%9DError:%20\[6001\]%20%E7%BD%91%E7%BB%9C%E9%80%9A%E4%BF%A1%E9%94%99%E8%AF%AF%E5%BC%82%E5%B8%B8%22)



## 正文

### Npm 代理问题

【问题描述】

报错信息如下：

![图片 1.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521094709B2URF5J5VBQCA4HH3G)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml2764\wps1.jpg)

使用淘宝镜像进行下载：

```
npm --registry https://registry.npm.taobao.org install dmdb
```

### 安装 dmdb 时 network 相关报错

【问题描述】

报错信息如下：

![图片 2.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521094824HJ19YHU99ZY9N2PG2Z)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml2764\wps2.jpg)

【问题解决】

设置 Properly ：

```
npm config set registry https://registry.npm.taobao.org
```

### Error: Cannot find module 'dmdb'

【问题描述】

报错信息如下：

![图片 3.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521095415OK3931BE0H2YH09AKQ)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml2764\wps3.jpg)

【问题解决】

Linux/windows 环境：添加环境变量 NODE_PATH 。

### Node.js 怎么连接达梦数据库

【问题解决】

具体的连接方式以及步骤，请参考 [DM Node.js 编程指南](https://eco.dameng.com/document/dm/zh-cn/pm/nodejs-rogramming-guide.html#) 的基本示例章节 。

### node.js+electron 适配 DM 报错: ”Error: [6001] 网络通信错误异常"

【问题描述】

使用新的 dm 驱动包执行 electron-rebuild 后，启动程序报错信息如下：

```
Error: [6001] 网络通信的异常 Error: [6071] 消息加密失败 Error: Unknown cipher at new n (D:\tool\electron-
quick-start\node_modules\dmdb\src\driver\error.js:28:19) at Function.n.ECJS_COMMUNICATION_ERROR 
(D:\tool\electron-quick-start\node_modules\dmdb\src\driver\error.js:35:16) at e.<anonymous> 
(D:\tool\electron-quick-start\node_modules\dmdb\src\driver\connection.js:466:91) at D:\tool\electron-quick-
start\node_modules\dmdb\src\driver\connection.js:108:27 at Object.throw (D:\tool\electron-quick-
start\node_modules\dmdb\src\driver\connection.js:119:14) at e (D:\tool\electron-quick-
start\node_modules\dmdb\src\driver\connection.js:31:26) at processTicksAndRejections 
(internal/process/task_queues.js:97:5)
```

【问题分析】

node 使用的是 OpenSSL 密码库，而 Electron 使用的是 BoringSSL 密码库，可以通过 crypto.getCiphers() 查看 Electron 支持的加密算法。因为数据库连接串属性 loginEncrypt 默认是 true，与数据库通信会用特定加密算法加密消息（dm.ini 中的 COMM_ENCRYPT_NAME 控制具体使用的加密算法）。

【问题解决】

方法一：如果对加密要求不高或者开发环境，更改数据库连接串，加上 loginEncrypt=false，示例如下：

```
connectString:"dm://SYSDBA:SYSDBA@localhost:5236?autoCommit=false&loginEncrypt=false"
```

方法二：需要查看 electron 支持的加密算法和达梦数据库支持的加密算法（查看 V$CIPHERS 视图）具体步骤如下：

  1. 查看 electron 支持的加密算法，通过 crypto.getCiphers()，代码如下：



```
const crypto = require('crypto') const ciphers = crypto.getCiphers() console.log(ciphers)
```

  2. 可以将其加入到 js 代码开头，然后执行可以看到支持的加密算法。例如：



```
D:\tool\node_electron>npm start> node_electron@1.0.0 start D:\tool\node_electron > electron . [ 'aes-128-cbc', 'aes-128-cfb', 'aes-128-ctr', 'aes-128-ecb', 'aes-128-gcm', 'aes-128-ofb', 'aes-192-cbc', 'aes-192-ctr', 'aes-192-ecb', 'aes-192-gcm', 'aes-192-ofb', 'aes-256-cbc', 'aes-256-cfb', 'aes-256-ctr', 'aes-256-ecb', 'aes-256-gcm', 'aes-256-ofb', 'des-cbc', 'des-ecb', 'des-ede', 'des-ede-cbc', 'des-ede3-cbc', 'rc2-cbc', 'rc4' ]
```

  3. 查看达梦数据库支持的相同的加密算法：



```
SELECT * FROM V$CIPHERS Where CYT_NAME in ('AES_128_CBC', 'AES_128_CFB', 'AES_128_CTR', 'AES_128_ECB', 'AES_128_GCM', 'AES_128_OFB', 'AES_192_CBC', 'AES_192_CTR', 'AES_192_ECB', 'AES_192_GCM', 'AES_192_OFB', 'AES_256_CBC', 'AES_256_CFB', 'AES_256_CTR', 'AES_256_ECB', 'AES_256_GCM', 'AES_256_OFB', 'DES_CBC', 'DES_ECB', 'DES_EDE', 'DES_EDE_CBC', 'DES_EDE3_CBC', 'RC2_CBC', 'RC4');
```

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml2764\wps4.jpg)

  4. 可以查询到 electron 与达梦数据库都支持的算法有 3 种，那么可以将 dm.ini 中的 COMM_ENCRYPT_NAME 设置为 RC4，命令如下：



```
SP_SET_PARA_STRING_VALUE(2,'COMM_ENCRYPT_NAME','RC4');
```

  5. 然后重启数据库，执行以下查询命令：



```
SQL> select para_name,para_value from v$dm_ini where para_name='COMM_ENCRYPT_NAME';
行号    PARA_NAME                        PARA_VALUE
1         COMM_ENCRYPT_NAME     RC4
```

  6. 启动程序，正常运行：



![图片 5.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240521103129M6JX5KU9819I3Y5IOS)

![](file:///C:\Users\Administrator\AppData\Local\Temp\ksohtml2764\wps5.jpg)



# Node.js 框架

## 一、前言

### 1.1 概述

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。Node.js 使用了一个事件驱动、非阻塞式 I/O 的模型，由于 Node.js 没有标准的数据库接口规范，故达梦公司根据达梦数据库的特点，为开发人员提供一套 DM Node.js 数据库驱动接口，其包名为 dmdb。下面将详细介绍 dmdb 包在达梦数据库上的用法。

本次所需的 dmdb 在 npm 的地址为：[https://www.npmjs.com/package/dmdb](https://www.npmjs.com/package/dmdb) 。

### 1.2 版本要求

Node.js 安装包及源码下载地址为：[https://nodejs.org/en/download/](https://nodejs.org/en/download/) 。

Node.js 历史版本下载地址：[https://nodejs.org/dist/](https://nodejs.org/dist/) 。

> **注意**
>
> 达梦数据库要求 Node.js 的版本至少为 v12.0.0。



## 二、Linux 下连接达梦

### 2.1 开发环境准备

|**名称**|**版本**|
|--|--|
|操作系统|Centos7 64 位|
|DM 数据库|DM 8.0 及以上版本|
|Node.js|14.16.0|


### 2.2 达梦数据库安装

请参考[《数据库环境准备》](https://eco.dameng.com/document/dm/zh-cn/app-dev/index.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 2.3 安装 node.js

  1. 下载安装包。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126170023X7LYAZ8NPIEOZTIJVI)

  2. 解压压缩包。



```
tar xvf  node-v14.16.0-linux-x64.tar.xz
```

  3. 修改文件所属权限。



```
 chown -R dmdba.dinstall /home/dmdba/node-v14.16.0-linux-x64/
```

  4. 切换用户。



```
su - dmdba
```

  5. 输出版本号。



```
./node -v
```

![微信图片_20240325165633.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240325165708Y18L8E0R2DCTMY4FQX)

  6. 配置环境变量。



```
vi .bash_profile 
PATH=$PATH:$HOME/.local/bin:$HOME/bin:/home/dmdba/node-v14.16.0-linux-x64/bin
export PATH
source .bash_profile 
```

### 2.4 安装 dmdb 包

#### 2.4.1 有网络环境下

使用 npm 命令下载 dmdb 。

```
npm install dmdb
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126170741PQHS1TDD1WD7OJ0HSK)

```
 查看 node_modules 目录下是否有 dmdb 
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126170850DEKH2HB7Q2IWEJ9B47)

> **注意**
>
> 安装部署如有问题参考 https://www.npmjs.com/packagenode-gyp，也可直接使用无网络环境下的离线压缩包安装！



#### 2.4.2 无网络的环境下

使用安装包 node_modules_linux.zip ，本安装包是从镜像源 [https://registry.npmjs.org/](https://registry.npmjs.org/) 下载， linux 环境下请使用此压缩包，使用方法为找到 node 安装目录，然后把压缩包解压在安装目录的 node_modules 目录下。

[node_modules_linux.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202403211159543WPG5NCVTAF81O1ILB)

### 2.5 测试代码编写

  1. 提前在 /data/nodejs_test/ 目录下准备“三国演义.jpg”图片。
  2. 编写 nodeJsTest.js 文件。



```
//该例程实现插入数据，修改数据，删除数据，数据查询等基本操作。
//引入 dmdb 包
var db = require('dmdb');
var fs = require('fs');
var pool, conn;
async function example() {
	try {
		pool = await createPool();
		conn = await getConnection();
		await queryWithResultSet();
		await insertTable();
		await updateTable();
		await queryTable();
		await deleteTable();
		await queryWithResultSet();
	} catch (err) {
		console.log(err);
	} finally {
		try {
			await conn.close();
			await pool.close();
		} catch (err) {}
	}
}
example();

//创建连接池
async function createPool() {
	try {
		return db.createPool({
			connectString: "dm://SYSDBA:*****\@localhost:5236?autoCommit=false",
		poolMax: 10,
		poolMin: 1
	});
  } catch (err) {
		throw new Error("createPool error: " + err.message);
	}
}
// 
//获取数据库连接
async function getConnection() {
	try {
		return pool.getConnection();
	} catch (err) {
		throw new Error("getConnection error: " + err.message);
	}
}

//往产品信息表插入数据 
async function insertTable() {
	try {
		var sql = "INSERT INTO production.product(name,author,publisher,publishtime,"
+
"product_subcategoryid,productno,satetystocklevel,originalprice,nowprice,discount,"
+ "description,photo,type,papertotal,wordtotal,sellstarttime,sellendtime) "
+ "VALUES(:1,:2,:3,:4,:5,:6,:7,:8,:9,:10,:11,:12,:13,:14,:15,:16,:17);";
		console.log("开始插入数据！");
		var blob = fs.createReadStream("/data/nodejs_test/三国演义.jpg");
		await conn.execute(
			sql,
			[
				{ val: "三国演义" },
				{ val: "罗贯中" },
				{ val: "中华书局" },
				{ val: new Date("2005-04-01") },
				{ val: 4 },
				{ val: "9787101046121" },
				{ val: 10 },
				{ val: 19.0000 },
				{ val: 15.2000 },
				{ val: 8.0 },
				{ val:"《三国演义》是中国第一部长篇章回体小说，中国小说由短篇发展至长篇的原因与说书有关。"},
				{ val: blob },
				{ val: "25" },
				{ val: 943 },
				{ val: 93000 },
				{ val: new Date("2006-03-20") },
				{ val: new Date("1900-01-01") }
			]
		);
		conn.execute("commit;");
		console.log("插入数据结束！");
	} catch (err) {
		throw new Error("insertTable error: " + err.message);
	}
}

//修改产品信息表数据
async function updateTable() {
	try {
		console.log("更新数据！")
		var sql = "UPDATE production.product SET name = :name " + "WHERE productid = 6;";
		// 按名称绑定变量
		await conn.execute(sql, { name: { val: "三国演义(上) " } });
		await conn.execute("commit;");
	} catch (err) {
		throw new Error("updateTable error: " + err.message);
	}
}

//删除产品信息表数据
async function deleteTable() {
	try {
		console.log("删除数据！")
		var sql = "DELETE FROM production.product WHERE productid = 11;"
		await conn.execute(sql);
		await conn.execute("commit;");
	} catch (err) {
		throw new Error("deleteTable error: " + err.message);
	}
}

//查询产品信息表
async function queryTable() {
	try {
		var sql = "SELECT productid,name,author,publisher,photo FROM production.product"
		var result = await conn.execute(sql);
		var lob = result.rows[result.rows.length - 1][4];
		var buffer = await readLob(lob);
		// Lob 对象使用完需关闭
		await lob.close();
		console.log(buffer);
		return result;
	} catch (err) {
		throw new Error("queryTable error: " + err.message);
	}
}

//读取数据库返回的 Lob 对象
function readLob(lob) {
	return new Promise(function (resolve, reject) {
		var blobData = Buffer.alloc(0);
		var totalLength = 0;
		lob.on('data', function (chunk) {
			totalLength += chunk.length;
			blobData = Buffer.concat([blobData, chunk], totalLength);
		});
		lob.on('error', function (err) {
			reject(err);
		});
		lob.on('end', function () {
			resolve(blobData);
		});
	});
}

//结果集方式查询产品信息表
async function queryWithResultSet() {
	try {
		var sql = "SELECT productid,name,author,publisher FROM production.product";
		var result = await conn.execute(sql, [], { resultSet: true });
		var resultSet = result.resultSet;
		// 从结果集中获取一行
		result = await resultSet.getRow();
		while (result) {
			console.log(result);
			result = await resultSet.getRow();
		}
	} catch (err) {
		throw new Error("queryWithResultSet error: " + err.message);
	}
}
```

  3. 运行代码。



```
node nodeJsTest.js
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401261712528YXGGGBS0WRQK8T4RP)

  4. 使用服务名进行连接。



```
vi  /etc/dm_svc.conf 
```

单机环境：

![微信图片_20240325170321.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240325170352HOJQ1GJW87U0VMBJR8)

主备环境：

![微信图片_20240325170552.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202403251706206L107VI1CKWWIA8RSX)

修改对应连接串：

![111.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20241223130127E92TMUI6JRK9MN5EIL)

## 三、Windows 下连接达梦

### 3.1 开发环境准备

|**名称**|**版本**|
|--|--|
|操作系统|windows10 64 位|
|DM 数据库|DM 8.0 及以上版本|
|Node.js|12.18.3|


### 3.2 达梦数据库安装

请参考[《数据库环境准备》](https://eco.dameng.com/document/dm/zh-cn/app-dev/index.html#%E6%95%B0%E6%8D%AE%E5%BA%93%E7%8E%AF%E5%A2%83%E5%87%86%E5%A4%87)。

### 3.3 安装 Node.js

  1. 下载安装包：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126111525BNVTE2SVHZ2XCGU4WB)

  2. 点击安装包进行安装：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401261115418I5EGYFDMQJNULMCHS)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126111553Q2J9P4Q52QMZ9XQBWV)

  3. 安装完成测试：



```
node -v
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126172444MD974ENCP1J2IEWOWX)

### 3.4 安装 dmdb 包

#### 3.4.1 有网络环境下

使用 npm 命令下载 dmdb 。

```
npm install dmdb
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202401261725187DT6JC7OF6FZVQI1BD)

> **注意**
>
> 安装部署如有问题参考 https://www.npmjs.com/packagenode-gyp，也可直接使用无网络环境下的离线压缩包安装！



#### 3.4.2 无网络环境下

使用安装包 node_modules_windows.zip ，本安装包是从镜像源 [https://registry.npmjs.org/](https://registry.npmjs.org/) 下载，window 环境下请使用此压缩包，使用方法为找到 node 安装目录，然后把压缩包解压在安装目录的 node_modules 目录下。

[node_modules_windows.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240321120423TZ7AFADOEO4HWZSSBO)

### 3.5 测试代码编写

  1. 编写 nodeJsTest.js 文件。代码参考 linux 环境下的代码。
  2. 运行代码。



```
node nodeJsTest.js
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126172726ES8X9H71YD9FFCL8DS)

## 四、常见问题

### 4.1 Npm 代理问题

【问题描述】：报错信息如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126112104M738VZLRN4P6J2RNJ1)

【问题解决】：使用淘宝镜像进行下载。

```
npm --registry https://registry.npm.taobao.org install dmdb
```

### 4.2 安装 dmdb 时报错

【问题描述】：报错信息如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126112749C19P1F9IJFYNMRL41H)

```
npm config set registry https://registry.npm.taobao.org
```

### 4.3 Error: Cannot find module 'dmdb'

【问题描述】：报错信息如下：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20240126112838YMOPSRHTTJQR9TL8FV)

【问题解决】：Linux/windows 环境：添加环境变量 NODE_PATH。

## 五、参考

1．示例代码下载：[nodeJsTest.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20240126173619C6MPA50BLIJ8U9EL1O) 文件。

2．以上文档内容参考过程中遇到任何问题，可到[达梦技术社区](https://eco.dameng.com/community/question/)提问交流。

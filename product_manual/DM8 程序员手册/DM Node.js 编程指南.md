

# DM Node.js 编程指南

## 10.1 Node.js DM 数据库驱动介绍

由于 Node.js 没有标准的数据库接口规范，故达梦公司根据达梦数据库的特点，为开发人员提供一套 DM Node.js 数据库驱动接口，其包名为 dmdb。下面将详细介绍 dmdb 包在达梦数据库上的用法。

## 10.2 Node.js 驱动包安装与环境准备

1．版本要求

Node.js 的版本至少为 v12.0.0。

2．安装 dmdb 驱动包

2.1 在线安装

使用 npm install 命令在线安装 dmdb 包

```
> npm install dmdb
```

上述命令会自动安装所需的依赖包，如 iconv-lite、snappy。

2.2 离线安装

如果要安装驱动的主机没有联网，则需要一台联网机器（比如 windows 主机）按照如下步骤制作离线驱动包：

  1. 获取安装主机操作系统平台和架构。



```
> node –p process.platform
> node –p process.arch
```

  2. 在联网主机上安装相同版本的 Node.js，并安装 dmdb 包。
    
        > npm install dmdb
    

  3. 驱动依赖的 snappy 包不是跨平台的，联网主机 node_modules/@napi-rs/snappy 只能适用于与其平台和架构相同的主机，故我们还需要下载适配安装主机的 @napi-rs/snappy 版本。

  4. 在联网主机 node_modules/snappy/package.json 文件的“dependencies”或“optionalDependencies”中，根据 1 中获取的平台和架构信息，找到要安装的 @napi-rs/snappy 的实际包名，这里假设为 @napi-rs/snappy-linux-x64-gun。

  5. 在联网主机上执行如下语句，node_modules/@napi-rs 下就有了 snappy-linux-x64-gnu。
    
        > npm install @napi-rs/snappy-linux-x64-gnu --force
    

  6. 将联网主机上的整个 node_modules 打包上传到安装主机即完成了离线安装。



3．在项目中引用 dmdb 包

```
const db = require("dmdb");

// your code
```

## 10.3 对象使用

### 10.3.1 dmdb 对象

#### 10.3.1.1 常量

|**常量名**|**值**|**描述**|
|---|---|---|
|dmdb.OUT_FORMAT_ARRAY|4001|获取结果集时，用数组来获取一行数据|
|dmdb.OUT_FORMAT_OBJECT|4002|获取结果集时，用 json 对象来获取一行数据，键为列名|
|dmdb.BLOB|2019|数据类型，Blob|
|dmdb.BUFFER|2006|数据类型，Buffer|
|dmdb.CLOB|2017|数据类型，Clob|
|dmdb.CURSOR|2021|数据类型，Cursor|
|dmdb.DATE|2014|数据类型，Date|
|dmdb.DEFAULT|0|默认数据类型|
|dmdb.NUMBER|2010|数据类型，Number|
|dmdb.STRING|2001|数据类型，String|
|dmdb.BIND_IN|3001|参数绑定方向，绑入|
|dmdb.BIND_INOUT|3002|参数绑定方向，绑入和绑出|
|dmdb.BIND_OUT|3003|参数绑定方向，绑出|
|dmdb.POOL_STATUS_CLOSED|6002|连接池状态，关闭|
|dmdb.POOL_STATUS_DRAINING|6001|连接池状态，正在关闭|
|dmdb.POOL_STATUS_OPEN|6000|连接池状态，打开|
|dmdb.STMT_TYPE_ALTER|7|alter 语句|
|dmdb.STMT_TYPE_BEGIN|8|begin 语句|
|dmdb.STMT_TYPE_CALL|10|call 语句|
|dmdb.STMT_TYPE_COMMIT|21|commit 语句|
|dmdb.STMT_TYPE_CREATE|5|create 语句|
|dmdb.STMT_TYPE_DECLARE|9|declare 语句|
|dmdb.STMT_TYPE_DELETE|3|delete 语句|
|dmdb.STMT_TYPE_DROP|6|drop 语句|
|dmdb.STMT_TYPE_EXPLAIN_PLAN|15|explain plan 语句|
|dmdb.STMT_TYPE_INSERT|4|insert 语句|
|dmdb.STMT_TYPE_MERGE|16|merge 语句|
|dmdb.STMT_TYPE_ROLLBACK|17|rollback 语句|
|dmdb.STMT_TYPE_SELECT|1|select 语句|
|dmdb.STMT_TYPE_UNKNOWN|0|未知类型语句|
|dmdb.STMT_TYPE_UPDATE|2|update 语句|


#### 10.3.1.2 属性

以下属性的修改会影响由 dmdb 创建出来的 Pool 和 Connection 对象。

|**属性名**|**类型**|**描述**|
|---|---|---|
|autoCommit|Boolean|语句执行后，是否自动提交，缺省为 true|
|extendedMetaData|Boolean|查询结果集中 metadata 是否要扩展，缺省为 false|
|fetchAsBuffer|Array|指定结果集中的数据类型以 Buffer 显示，取值范围：dmdb.BLOB|
|fetchAsString|Array|指定结果集中的数据类型以 String 显示，取值范围：dmdb.BUFFER、dmdb.CLOB、dmdb.DATE、dmdb.NUMBER|
|outFormat|Number|查询结果的格式，取值为 dmdb.OUT_FORMAT_ARRAY 或 dmdb.OUT_FORMAT_OBJECT，表示格式化成数组还是 json 对象，缺省为 dmdb.OUT_FORMAT_ARRAY|
|poolMax|Number|最大连接数，缺省为 4|
|poolMin|Number|最小连接数，缺省为 0|
|poolTimeout|Number|连接闲置多久后自动关闭，单位秒。缺省为 60|
|queueMax|Number|获取连接请求等待队列最大个数。取值范围-1~9007199254740991。-1 表示无限制，缺省为 500|
|queueRequests|Boolean|连接池达到最大连接数后，后续获取连接请求是否进入等待队列。取值范围 ture、false。缺省为 true|
|queueTimeout|Number|进入等待队列的获取连接请求在等待多少毫秒后，认为请求失败，单位毫秒。取值范围 0~ 9007199254740991。0 表示永久等待，缺省为 60000|


#### 10.3.1.3 函数原型

  1. **createPool**



|**函数原型**|**功能说明**|
|----|----|
|createPool(poolAttrs)|根据配置创建连接池，返回 promise 对象|
|createPool(poolAttrs, callback)|根据配置创建连接池，执行回调函数|


参数

  1. poolAttrs(Object)：JS 对象，配置连接池属性，如连接串、最大连接数等。具体如下：



|**属性名**|**类型**|**描述**|
|---|---|---|
|connectString/connectionString|String|连接串。必选|
|poolAlias|String|连接池别名。可选|
|poolMax|Number|最大连接数，缺省为 4。可选|
|poolMin|Number|最小连接数，缺省为 0。可选|
|poolTimeout|Number|连接闲置多久后自动关闭，单位秒，缺省为 60，0 代表永不关闭闲置连接。可选|
|queueMax|Number|获取连接请求等待队列最大个数。取值范围-1~9007199254740991。-1 表示无限制，缺省为 500。可选|
|queueRequests|Boolean|连接池达到最大连接数后，后续获取连接请求是否进入等待队列。取值范围 ture、false。缺省为 true。可选|
|queueTimeout|Number|进入等待队列的获取连接请求在等待多少毫秒后，认为请求失败，单位毫秒。取值范围 0~ 9007199254740991。0 表示永久等待，缺省为 60000。可选|
|testOnBorrow|Boolean|连接获取前是否验证有效性，如果连接失效，则继续取池中其他连接。可选|
|validationQuery|String|连接有效性检查使用的 SQL 语句，必须至少有一行结果集，缺省为”select 1;”。可选|


  2. callback(Function)：执行完 createPool 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若创建连接池失败，error 不为空|
|Pool pool|若创建连接池成功，pool 为函数返回的 Pool 对象|


下面通过一个简单的例子来说明创建连接池。

```
// 回调函数
db.createPool({
	    connectString: "dm://SYSDBA:sysDBA*00@localhost:5236"
},
function(err, pool) {
do_something_with_pool();
}
)

// Promise
db.createPool({
	connectString: "dm://SYSDBA:sysDBA*00@localhost:5236",
poolMin: 1,
poolMax: 3,
poolAlias: "DM5236"
}).then(function(pool) {
do_something_with_pool();
}) .catch(function(err) {
console.log(err);
})
```

若无特别说明，本章下文示例中使用的都是回调函数。

  2. **getConnection**



|**函数原型**|**功能说明**|
|----|----|
|getConnection(poolAlias)|从指定连接池中获取连接，返回 promise 对象|
|getConnection(poolAlias, callback)|从指定连接池中获取连接，执行回调函数|
|getConnection(connURL)|根据配置创建连接，返回 promise 对象|
|getConnection(connURL, callback)|根据配置创建连接，执行回调函数|
|getConnection(connAttrs)|根据连接属性创建连接，返回 promise 对象|
|getConnection(connAttrs, callback)|根据连接属性创建连接，执行回调函数|


参数

  1. poolAlias(String)：连接池别名
  2. connURL(String)：连接串
  3. connAttrs(Object)：连接属性，具体字段如下：



|**字段名**|**类型**|**描述**|
|---|---|---|
|connectString|String|连接串，与 connectionString 二选一，connectString 更优先，格式 host:port 或服务名|
|connectionString|String|连接串，与 connectString 二选一，connectString 更优先，格式 host:port 或服务名|
|user|String|登录用户名|
|password|String|登录密码|
|其余字段：与连接串属性相同，参考 10.4 节|……|……|


  4. callback(Function)：执行完 getConnection 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若创建连接失败，error 不为空|
|Conection connection|若获取连接成功，connection 为函数返回的 Connection 对象|


下面通过一个简单的例子来说明创建连接。

```
db.getConnection("dm://SYSDBA:sysDBA*00@localhost:5236", function(err, connection) {
	do_something_with_connection();
})
```

  3. **getPool**



|**函数原型**|**功能说明**|
|----|----|
|getPool(poolAlias)|获取 Pool 连接池对象|


参数

poolAlias(String)：连接池别名

下面通过一个简单的例子来说明获取连接池。

```
db.createPool(
    {
	   poolAlias: "DM5236",
        connectString: "dm://SYSDBA:sysDBA*00@localhost:5236"
    },
function(err, pool) {
		console.log(pool === db.getPool("DM5236")); // true
	}
)
```

### 10.3.2 Connection 对象

#### 10.3.2.1 函数原型

  1. **close/release**



|**函数原型**|**功能说明**|
|----|----|
|close()/release()|关闭连接，返回 promise 对象|
|close(callback)/release(callback)|关闭连接，执行回调函数|


参数

callback(Function)：执行完 close/release 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若断开连接失败，error 不为空|


下面通过一个简单的例子来说明关闭连接。

```
conn.close(function(err) {

console.log(err);

})
```

  2. **commit**



|**函数原型**|**功能说明**|
|----|----|
|commit()|提交事务，返回 promise 对象|
|commit(callback)|提交事务，执行回调函数|


参数

callback(Function)：执行完 commit 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若提交失败，error 不为空|


下面通过一个简单的例子来说明提交事务。

```
conn.commit(function(err) {
	console.log(err);
})
```

  3. **createLob**



|**函数原型**|**功能说明**|
|----|----|
|createLob(type)|创建 Lob 对象，返回 promise 对象|
|createLob(type, callback)|创建 Lob 对象，执行回调函数|


参数

  1. type(Number)：Lob 对象类型，取值为 dmdb.CLOB 或 dmdb.BLOB
  2. callback(Function)：执行完 createLob 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若创建 Lob 失败，error 不为空|
|Lob lob|若创建 Lob 成功，lob 为函数返回的 Lob 对象|


下面通过一个简单的例子来说明创建 LOB 对象。

```
conn.createLob(dmdb.CLOB, function(err, lob) {
	do_something_with_lob();
})
```

  4. **execute**



|**函数原型**|**功能说明**|
|----|----|
|execute(sql, [bindParams, [options]])|执行语句，返回 promise 对象|
|execute(sql, [bindParams, [options]], callback)|执行语句，执行回调函数|


参数

  1. sql(String)：SQL 或 PL/SQL 语句，可包含绑定变量
  2. bindParams(Object)：绑定参数，按照名称绑定时，为 JS 对象，按照位置绑定时，为 Array 数组。可配置属性具体如下：



|**绑定属性**|**类型**|**描述**|
|----|----|----|
|dir|Number|绑定的方向，取值为 dmdb.BIND_IN，dmdb.BIND_INOUT，dmdb.BIND_OUT 其一，默认 dmdb.BIND_IN|
|val|Object|变量以 IN 或 IN OUT 方式绑定时，可设置输入值|


  3. options(Object)：语句执行的选项，为 JS 对象。可配置属性具体如下：



|**属性**|**类型**|**描述**|
|--|--|--|
|extendedMetaData|Boolean|覆盖 dmdb.extendedMetaData|
|fetchInfo|Object|结果集列以何种 JavaScript 类型展示，可以覆盖全局设置 dmdb.fetchAsString 和 dmdb.fetchAsBuffer. 例如： fetchInfo: { “COL_NAME_DATE”: { type: dmdb.STRING}, “COL_NAME_ANY”: { type: dmdb.DEFAULT} } 其中 type 的值可以是： dmdb.STRING，对于数字，日期时间，二进制数据，CLOB 的结果集列返回 String； dmdb.BUFFER，对于 BLOB 的结果集列返回 Buffer； dmdb.DEFAULT，覆盖 dmdb.fetchAsString 和 dmdb.fetchAsBuffer 设置，结果集列返回原始数据；|
|outFormat|Number|覆盖 dmdb.outFormat|
|resultSet|Boolean|查询结果是否返回 ResultSet 对象，缺省为 false|


  4. callback(Function)：执行完 execute 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若执行语句失败，error 不为空|
|Object result|若执行语句成功，result 为函数返回结果，包含的字段可能有：metaData、implicitResults 、outBinds、resultSet、rows、rowsAffected其中各字段含义如下：metaData：查询语句的列信息，当且仅当有多个结果集时为二维数组，含如下字段：    name：列名    dbType：列类型    dbTypeName：列类型名称    precision：列数据精度    scale：列数据标度    nullable：列是否可为空implicitResults：当有多个结果集时，结果保存在此字段中outBinds：绑出参数resultSet：查询语句的结果集，当执行选项的 resultSet 为 false 时，resultSet 为 undefinedrows：查询语句的所有结果行，当执行选项的 resultSet 为 true 时，rows 为 undefinedrowsAffected：DML 语句影响行数。对于非 DML 语句(如 select、PL/SQL)，rowsAffected 为 undefined|


下面通过一个简单的例子来说明执行语句。

```
conn.execute(

	"select name, id from sysobjects where name = :na",

	{ na: { val: 'SYSDBA', dir: db.BIND_IN } },

	{ resultSet: false },

	function(err, result) {

	do_something_with_result();

	}

)
```

  5. **executeMany**



|**函数原型**|**功能说明**|
|----|----|
|executeMany(sql, binds, [options])|批量执行语句，返回 promise 对象|
|executeMany(sql, numIterations, [options])|批量执行语句，返回 promise 对象|
|executeMany(sql, binds, [options], callback)|批量执行语句，执行回调函数|
|executeMany(sql, numIterations, [options], callback)|批量执行语句，执行回调函数|


参数

  1. sql(String)：SQL 或 PL/SQL 语句，必须包含绑定变量
  2. binds(Array)：绑定参数，按照名称绑定时，为 JS 对象组成的 Array 数组，按照位置绑定时，为 Array 数组组成的 Array 数组
  3. numIterations(Number)：SQL 或 PL/SQL 语句执行次数
  4. options(Object)：语句执行的选项，为 JS 对象。可配置属性具体如下：



|**属性**|**类型**|**描述**|
|--|--|--|
|batchErrors|Boolean|为 true 时，遇到数据库报错后，仍会执行之后的数据行，默认 false|
|bindDefs|Object|包含 dir 字段，详细描述见 execute 方法的参数 bindParams 中的 dir 字段|
|dmlRowCounts|Boolean|结果中是否显示批量执行影响行数，缺省为 false|


  5. callback(Function)：执行完 executeMany 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若批量执行语句失败，error 不为空|
|Object result|若批量执行语句成功，result 为函数返回结果，包含的字段可能有：batchErrors、dmlRowCounts、outBinds、rowsAffected其中各字段含义如下：batchError：批量执行错误列表dmlRowCounts：批量执行每一次影响的行数outBinds：绑出参数rowsAffected：语句影响行数|


下面通过一个简单的例子来说明 executeMany 用法。

```
conn.executeMany(
	"insert into PERSON values(:1, :2, :3)",
	[	[101, '张三', '男'],
		[102, '李四', '女'] 
	],
	{
	dmlRowCounts: true,
	batchErrors: true,
	bindDefs: [	{ dir: db.BIND_IN },{ dir: db.BIND_IN },{ dir: db.BIND_IN }	]
	},
	function(err, result) {	do_something_with_result();	}
)
```

  6. **getStatementInfo**



|**函数原型**|**功能说明**|
|----|----|
|getStatementInfo(sql)|分析 sql 语句信息，返回 promise 对象|
|getStatementInfo(sql, callback)|分析 sql 语句信息，执行回调函数|


参数

  1. sql(String)：SQL 语句
  2. callback(Function)：执行完 getStatementInfo 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若分析 sql 语句信息失败，error 不为空|
|Object information|若分析 sql 语句信息成功，information 为函数返回结果，包含的字段有：bindNames、metaData、statementType|


下面通过一个简单的例子来说明 getStatementInfo 的用法。

```
conn.getStatementInfo("select name from person where id = :i", function (err,info) {

do_something_with_info();

})
```

  7. **rollback**



|**函数原型**|**功能说明**|
|----|----|
|rollback()|回滚，返回 promise 对象|
|rollback(callback)|回滚，执行回调函数|


参数

callback(Function)：执行完 rollback 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若回滚失败，error 不为空|


下面用一个简单的例子来说明 rollback 的用法。

```
conn.rollback(function (err) {

do_something_with_err();

})
```

  8. **queryStream**



|**函数原型**|**功能说明**|
|----|----|
|queryStream(sql  [, bindParams [, options]])|执行查询语句，返回可读流|


参数

  1. sql(String)：查询 SQL 或 PL/SQL 语句，可包含绑定变量。
  2. bindParams(Object)：绑定参数，按照名称绑定时，为 JS 对象，按照位置绑定时，为 Array 数组。可配置属性具体如下：



|**绑定属性**|**类型**|**描述**|
|----|----|----|
|dir|Number|绑定的方向，可取值 dmdb.BIND_IN、dmdb.BIND_INOUT 或 dmdb.BIND_OUT，默认 dmdb.BIND_IN|
|val|Object|变量以 IN 或 IN OUT 方式绑定时，可设置输入值|


  3. options(Object)：语句执行的选项，为 JS 对象。可配置属性具体如下：



|**属性**|**类型**|**描述**|
|--|--|--|
|extendedMetaData|Boolean|覆盖 dmdb.extendedMetaData|
|fetchInfo|Object|结果集列以何种 JavaScript 类型展示，可以覆盖全局设置 dmdb.fetchAsString 和 dmdb.fetchAsBuffer. 例如：  fetchInfo: {   “COL_NAME_DATE”: { type: dmdb.STRING},   “COL_NAME_ANY”: { type: dmdb.DEFAULT}  }  其中 type 的值可以是：  dmdb.STRING，对于数字，日期时间，二进制数据，CLOB 的结果集列返回 String；  dmdb.BUFFER，对于 BLOB 的结果集列返回 Buffer；  dmdb.DEFAULT，覆盖 dmdb.fetchAsString 和 dmdb.fetchAsBuffer 设置，结果集列返回原始数据；|
|outFormat|Number|覆盖 dmdb.outFormat|
|resultSet|Boolean|查询结果是否返回 ResultSet 对象，缺省为 false|


下面通过一个简单的例子来说明 queryStream 用法。

```
let stream = conn.queryStream(“select name from sysobjects”);
stream.on("error", function (error) {
handle_error();
});
stream.on("metadata", function (metadata) {
do_something_with_metadata();
});
stream.on("data", function (data) {
// data是表中的一行数据
do_something_with_data();
});
stream.on("end", function () {
// 结果集读取完毕
stream.destroy();
});
stream.on("close", function () {
// 流关闭
handle_close();
});
```

### 10.3.3 Lob 对象

#### 10.3.3.1 属性

|**属性名**|**类型**|**描述**|
|---|---|---|
|type|Number|值为 dmdb.BLOB、dmdb.CLOB 其一，只读|


#### 10.3.3.2 函数原型

  1. **close**



使用 createLob()创建的 Lob 对象应调用 close()关闭

|**函数原型**|**功能说明**|
|----|----|
|close()|关闭 Lob，返回 promise 对象|
|close(callback)|关闭 Lob，执行回调函数|


参数

callback(Function)：执行 close 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若关闭 Lob 失败，error 不为空|


下面用一个简单的示例来说明 close()的用法。

```
lob.close(function(err) {

console.log(err);

})
```

  2. **getData**



获取 Lob 对象中存储的全部数据，对于 BLOB，返回为 Buffer；对于 CLOB，返回为 String

|**函数原型**|**功能说明**|
|----|----|
|getData()|获取 Lob 全部数据，返回 promise 对象|
|getData(callback)|获取 Lob 全部数据，执行回调函数|


参数

callback(Function)：执行 getData 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若获取 Lob 全部数据失败，error 不为空|
|String|Buffer data|Lob 存储的全部数据|


下面用一个简单的示例来说明 getData()的用法。

```
lob.getData(function(err, data) {

do_something_with_data();

})
```

  3. **getLength**



获取 Lob 对象的长度，对于 BLOB，返回为字节数组长度；对于 CLOB，返回为字符串字符数

|**函数原型**|**功能说明**|
|----|----|
|getLength()|获取 Lob 长度，返回 promise 对象|
|getLength(callback)|获取 Lob 长度，执行回调函数|


参数

callback(Function)：执行 getLength 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若获取 Lob 全部数据失败，error 不为空|
|Number length|Lob 的长度|


下面用一个简单的示例来说明 getLength()的用法。

```
lob.getLength(function(err, length) {

do_something_with_length();

})
```

### 10.3.4 Pool 对象

#### 10.3.4.1 属性

|**属性名**|**类型**|**描述**|
|---|---|---|
|connectionsInUse|Number|连接池中正在使用的连接数，只读|
|connectionsOpen|Number|连接池中已打开的连接数，只读|
|poolAlias|String|createPool()指定的连接池别名，只读|
|poolMax|Number|最大连接数，只读|
|poolMin|Number|最小连接数，只读|
|poolTimeout|Number|连接闲置多久后自动关闭，单位秒，只读|
|testOnBorrow|Boolean|连接获取前是否验证有效性，只读|
|validationQuery|String|连接有效性检查使用的 SQL 语句，只读|
|status|Number|连接池状态，只读|


#### 10.3.4.2 函数原型

  1. **close/terminate**



|**函数原型**|**功能说明**|
|----|----|
|close()/terminate()|关闭连接池，返回 promise 对象|
|close(callback)/terminate(callback)|关闭连接池，执行回调函数|


参数

callback(Function)：执行 close/terminate 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若关闭连接池失败，error 不为空|


下面通过一个简单的示例来说明 close 的用法。

```
pool.close(function(err) {

console.log(err);

})
```

  2. **getConnection**



|**函数原型**|**功能说明**|
|----|----|
|getConnection()|从连接池中获取数据库连接，返回 promise 对象|
|getConnection(callback)|从连接池中获取数据库连接，执行回调函数|


参数

callback(Function)：执行 getConnection 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若获取数据库连接失败，error 不为空|
|Connection conn|若获取数据库连接成功，conn 为函数返回的 Connection 对象|


下面用一个简单的示例来说明 getConnection 的用法。

```
pool.getConnection(function(err, conn) {

do_something_with_conn();

})
```

### 10.3.5 ResultSet 对象

ResultSet 对象每次可以从数据库中获取一行或多行数据。当查询结果集很大，或不知道结果集行数时，建议使用 ResultSet 对象。

#### 10.3.5.1 函数原型

  1. **close**



|**函数原型**|**功能说明**|
|----|----|
|close()|关闭结果集，返回 promise 对象|
|close(callback)|关闭结果集，执行回调函数|


参数

callback(Function)：执行 close 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若关闭结果集失败，error 不为空|


下面通过一个简单的示例来说明 close()的用法。

```
resultSet.close(function(err) {

do_something_with_err();

})
```

  2. **getRow**



|**函数原型**|**功能说明**|
|----|----|
|getRow()|获取结果集中的下一行，返回 promise 对象|
|getRow(callback)|获取结果集中的下一行，执行回调函数|


参数

callback(Function)：执行 getRow 后的回调函数。参数如下：

|**回调函数参数**|**描述**|
|------|------|
|Error error|若获取结果集中的下一行失败，error 不为空|
|Object row|若获取结果集中的下一行成功，row 为返回的结果集中的下一行|


下面用一个简单的示例来说明 getRow 的用法。

```
resultSet.getRow(function(err, row) {

do_something_with_row();

})
```

  3. **getRows**



|**函数原型**|**功能说明**|
|----|----|
|getRows(numRows)|获取结果集中的多行，返回 promise 对象|
|getRows(numRows, callback)|获取结果集中的多行，执行回调函数|


参数

  1. numRows(Number)：要获取的行数
  2. callback(Function)：执行 getRows 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若获取结果集中的多行失败，error 不为空|
|Array rows|若获取结果集中的多行成功，row 为返回的结果集中行组成的数组|


下面用一个简单的示例来说明 getRows 的用法。

```
resultSet.getRows(10, function(err, rows) {

do_something_with_rows();

})
```

  4. **getRowCount**



|**函数原型**|**功能说明**|
|----|----|
|getRowCount()|获取结果集总行数，返回 promise 对象|
|getRowCount(callback)|获取结果集总行数，执行回调函数|


参数

  1. callback(Function)：执行 getRowCount 后的回调函数。参数如下：



|**回调函数参数**|**描述**|
|------|------|
|Error error|若获取结果集中的多行失败，error 不为空|
|Number rowCount|若获取结果集总行数成功，rowCount 为返回的结果集总行数|


下面用一个简单的示例来说明 getRowCount 的用法。

```
resultSet.getRowCount(function(err, rowCount) {

	do_something_with_rowCount();

})
```

  5. **toQueryStream**



|**函数原型**|**功能说明**|
|----|----|
|toQueryStream()|将结果集转换成可读流，返回可读流|


下面用一个简单的示例来说明 toQueryStream 的用法。

```
let stream = resultSet.toQueryStream();
stream.on("error", function (error) {
handle_error();
});
stream.on("metadata", function (metadata) {
do_something_with_metadata();
});
stream.on("data", function (data) {
// data是表中的一行数据
do_something_with_data();
});
stream.on("end", function () {
// 结果集读取完毕
stream.destroy();
});
stream.on("close", function () {
// 流关闭
handle_close();
});
```

## 10.4 连接串可配置属性

连接串基本格式：

```
dm://user:password@host:port[?propName1=propValue1][&propName2=propValue2]…
dm://user:password@GroupName[?propName1=propValue1][&propName2=propValue2]…
dm://user:password@GroupName?GroupName=(host1:port1,host2:port2,…)[&propName1=propValue1]…
```

参数介绍：

user：用户名；

password：密码；

host：数据库所在 IP 地址；

port：数据库端口号；

？：参数分隔符，表示后面的都是参数；

&：参数间隔符，多个参数用&分开；

propName：连接串属性名称，详见表 10.1；

propValue：连接串属性值；

GroupName：数据库服务名。若未指定服务名对应的 host:port，则将在配置文件 dm_svc.conf 中匹配相应的服务名。

连接串中可以设置的属性及其说明见下表。

表10.1 Node.js连接串属性

|**属性名称**|**类型**|**说明**|
|----|----|----|
|addressRemap|String|地址重定向，格式：(IP_1:PORT_1,IP_2:PORT_2)，如：addressRemap=(192.168.0.1:5236,localhost:5237)，如果连接 192.168.0.1:5236，则实际连接到 localhost:5237。可以配置多个地址重定向，如：addressRemap=(localhost:5236,localhost:5237)(localhost:5238,localhost:5239)，缺省为空|
|alwaysAllowCommit|Boolean|在自动提交开关打开时，是否允许手动提交回滚，缺省为 true|
|appName|String|客户端应用程序名称，缺省为空|
|autoCommit|Number|是否自动提交，缺省为 true|
|batchAllowMaxErrors|Number|continueBatchOnError 开启时，最大可容错行数，超过则停止执行，但不报错，取值范围 0~65535，缺省为 0，表示无限制|
|batchNotOnCall|Boolean|存储过程是否不批量执行，缺省为 false，取值范围（true，false）|
|batchType|Number|批处理类型，缺省为 1，1：进行批量绑定；2：不进行批量绑定|
|bufPrefetch|Number|结果集 fetch 预取消息 buffer 大小；单位 KB，取值范围 32~65535。缺省为 0，表示按服务器配置|
|cluster|String|当 doSwitch=2，epSelector=1 时，用于检测 DSC 集群节点故障恢复是否成功，缺省为空，取值（DSC）|
|continueBatchOnError|Boolean|批量执行出错时是否继续执行；缺省为 false|
|columnNameUpperCase|Boolean|列名是否全部大写，缺省为 false，取值范围（true，false）|
|columnNameCase|String|列名大小写显示策略，缺省为空，取值范围（upper，lower）|
|compatibleMode|String|兼容其他数据库, 属性值为数据库名称（例如：oracle），支持兼容 Oracle 和 Mysql|
|compress|Number|是否压缩消息。取值范围 0~2。0 表示不压缩；1 表示完全压缩，采用第三方压缩包进行压缩；2 表示优化的压缩，只有当消息长度超过 8192 字节并且非本地连接，才会采用第三方压缩包进行压缩，其他情况不压缩。缺省为 0。采用的第三方压缩包类型由 compressID 指定|
|compressId|Number|消息压缩算法标识，最终与服务器支持情况协商决定。取值 0 和 1。缺省为 0。 0 指定 zip 算法，zip 算法由第三方压缩包 jzlib-X.X.X.jar 提供；1 指定 snappy 算法，snappy 算法由第三方压缩包 snappy-java- X.X.X.jar 提供。X.X.X 表示版本号。指定了 compressID 参数后，用户 JAVA 代码中须引用相应的第三方压缩包。第三方压缩包位于 DM 安装目录\tool\dropins\com.dameng\plugins\com.dameng.third 中|
|connectTimeout|Number|连接数据库超时时间；单位 ms，取值范围 0~2147483647，0 表示无限制；缺省为 5000|
|doSwitch|Number|连接发生异常或一些特殊场景下的连接处理策略。0：关闭连接；1：当连接发生异常时自动切换到其他库，无论切换成功还是失败都会报错，用于通知上层应用进行事务执行失败时的相关处理；2：配合 epSelector=1 使用，如果服务名列表前面的节点恢复了，将当前连接切换到前面的节点上；缺省为 0|
|enRsCache|Boolean|是否开启结果集缓存；缺省为 false；取值范围（true，false）|
|epSelector|Number|服务名连接策略。0：依次选取列表中的不同节点建立连接，使得所有连接均匀地分布在各个节点上；1：选择列表中最前面的节点建立连接，只有当前节点无法建立连接时才会选择下一个节点进行连接；缺省为 0|
|escapeProcess|Boolean|是否进行语法转义处理，缺省为 false；取值范围（true，false）|
|injectArray|Boolean|绑定参数为数组时，是否直接注入到 SQL 语句中，非数组类型的参数不受影响，仅支持 select、insert、update、delete 语句；缺省为 false，取值范围（true，false）例如：conn.execute("select * from test where id in (?);", [[1,2,3]])实际执行的 SQL 语句会被改写为：select * from test where id in (1, 2, 3);|
|isBdtaRS|Boolean|是否使用列模式结果集，缺省为 false；取值范围（true，false）|
|lobMode|Number|Lob 模式，缺省为 1；1： 分批缓存到本地，2：一次将大字段数据缓存到本地|
|localTimezone|Number|指定客户端本地时区，对于本地时区相关时间类型会自动完成服务器时区与本地时区的转换；单位分钟，取值范围-779~840；缺省为当前系统时区|
|logDir|String|日志等其他一些驱动过程文件生成目录，缺省值是当前工作目录|
|logFlushFreq|Number|日志刷盘频率，单位 s，取值范围 0~2147483647，缺省为 10|
|loginCertificate|String|指定登录加密用户名密码公钥所在的路径，一旦配置即认为开启了客户端的证书加密用户名密码模式；|
|loginDscCtrl|Boolean|服务名连接数据库时是否只选择 dsc control 节点的库，缺省为 false，取值范围（true，false）|
|loginMode|Number|指定优先登录的服务器模式。取值范围 0~4；0：优先连接 PRIMARY 模式的库，NORMAL 模式次之，最后选择 STANDBY 模式；1：只连接主库；2：只连接备库；3：优先连接 STANDBY 模式的库，PRIMARY 模式次之，最后选择 NORMAL 模式；4：优先连接 NORMAL 模式的库，PRIMARY 模式次之，最后选择 STANDBY 模式；缺省为 4|
|loginStatus|Number|服务名方式连接数据库时只选择状态匹配的库；取值范围 0、3~5；0 表示不限制；3 表示 mount 状态；4 表示 open 状态；5 表示 suspend 状态；缺省为 0|
|logLevel|String|生成日志的级别，日志按从低到高依次如下（off：不记录；error：只记录错误日志；warn：记录警告信息；sql：记录 sql 执行信息；info：记录全部执行信息；all：记录全部），默认 off，高级别同时记录低级别的信息|
|maxRows|Number|结果集行数限制，若超过上限，则对结果集进行截断，取值范围 0~2147483647，缺省为 0，表示无限制|
|mppLocal|Boolean|是否 MPP 本地连接，缺省为 false；取值范围（true，false）|
|osName|String|操作系统名称，缺省为空|
|rsCacheSize|Number|结果集缓存区大小，单位 MB，缺省为 20，取值范围 1~65536|
|rsRefreshFreq|Number|结果集缓存检查更新的频率，单位秒，缺省为 10，取值范围 0~10000；如果设置为 0，则不需检查更新|
|rwHA|Boolean|是否开启读写分离系统高可用；取值范围 1/0 或 true/false；缺省为 false|
|rwIgnoreSql|Boolean|读写分离是否忽略 sql 类型，并按比例分发到主/备库，缺省为 false，取值范围（true，false）|
|rwPercent|Number|分发到主库的事务占主备库总事务的百分比，取值范围 0~100，缺省为 25|
|rwSeparate|Boolean|是否使用读写分离系统，缺省为 false；取值范围（false：不使用，true：使用）|
|rwStandbyRecoverTime|Number|读写分离系统备库故障恢复检测频率，单位 ms，取值范围 0~2147483647，缺省为 1000；0 表示不恢复|
|schema|String|指定用户登录后的当前模式，缺省为用户的默认模式|
|sessionTimeout|Number|会话超时时间，单位 s，取值范围 0~2147483647，缺省为 0|
|socketTimeout|Number|网络通讯超时时间，单位 ms。取值范围 0~2147483647，缺省为 0。0 表示无超时限制。如果配置了该参数，将导致执行耗时大于超时时间的数据库操作报错|
|stmtPoolSize|Number|语句句柄池大小，取值范围 0~2147483647，缺省为 15|
|sslPath|String|指定 ssl 加密证书文件、密钥文件、CA 证书文件的目录，目录内必须包含 client-cert.pem、client-key.pem、ca-cert.pem|
|svcConfPath|Number|自定义客户端配置文件(dm_svc.conf)的完整路径|
|switchInterval|Number|服务名连接数据库时，若遍历了服务名中所有库列表都未找到符合条件的库成功建立连接，等待一定时间再继续下一次遍历；单位 ms，取值范围 0~2147483647，缺省为 1000|
|switchTimes|Number|服务名连接数据库时，若未找到符合条件的库成功建立连接，将尝试遍历服务名中库列表的次数，取值范围 0~2147483647，缺省为 1|
|userRemap|String|用户名重定向，格式：(USER1,USER2)，如：userRemap=(USER1,USER2)，如果使用 USER1 用户连接，则实际连接用户为 USER2。可以配置多个重定向，如：userRemap=(USER1,USER2)(USER3,USER4)，缺省为空|


## 10.5 ORM 方言包

达梦 Node.js 数据库驱动支持 ORM 框架 typeorm，提供方言包 typeorm-dm 进行适配。方言包安装方法：npm install typeorm-dm。安装方法将自动安装依赖包 typeorm 和 dmdb。

下面用一个例子来介绍方言包 typeorm-dm 的用法。

```
import "reflect-metadata"
import { DmdbDataSource } from "typeorm-dm"
import { Photo } from "./entity/Photo"
// 注意：typeorm根据选项中的type字段自动加载数据库驱动；而达梦方言包typeorm-dm必须手动指定DmdbDataSource类型的数据源！
const AppDataSource = new DmdbDataSource({
    type: "oracle",
    innerType: "dmdb",
    host: "localhost",
    port: 5236,
    username: "SYSDBA",
    password: "sysDBA*00",
    schema: "SYSDBA",
    // url优先于host/port/username等参数，建议使用url，因为可以支持更多自定义连接串参数
    url: "dm://SYSDBA:sysDBA*00@localhost:5236?schema=SYSDBA",
    entities: [Photo],
    synchronize: true,
    logging: false,
})

AppDataSource.initialize()
    .then(async () => {
        let photoRepo = AppDataSource.getRepository(Photo)
     let photo = new Photo()
     photo.name = "photo1"
// 插入
await photoRepo.save(photo)
// 查询
let allPhoto = await photoRepo.find()
console.log(allPhoto)
await AppDataSource.destroy()
    })
    .catch((error) => console.log(error))
```

## 10.6 基本示例

下面用一个具体的编程实例来展示利用 Node.js 驱动程序进行数据库操作。

```
//该例程实现插入数据，修改数据，删除数据，数据查询等基本操作。
//引入dmdb包
var db = require('dmdb');
var fs = require('fs');

var pool, conn;
async function example() {
    try {
        pool = await createPool();
        conn = await getConnection();
        await insertTable();
        await updateTable();
        await queryTable();
        await queryWithResultSet();
        await deleteTable();
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

// 创建连接池
async function createPool() {
    try {
        return await db.createPool({
            connectString: "dm://SYSDBA:sysDBA*00@localhost:5236?autoCommit=false",
            poolMax: 10,
            poolMin: 1
        });
    } catch (err) {
        throw new Error("createPool error: " + err.message);
    }
}
//获取数据库连接 
async function getConnection() {
    try {
        return await pool.getConnection();
    } catch (err) {
        throw new Error("getConnection error: " + err.message);
    }
}
//往产品信息表插入数据 
async function insertTable() {
    try {
        var sql = "INSERT INTO production.product(name,author,publisher,publishtime,"
            + "product_subcategoryid,productno,satetystocklevel,originalprice,nowprice,discount,"
            + "description,photo,type,papertotal,wordtotal,sellstarttime,sellendtime) "
            + "VALUES(:1,:2,:3,:4,:5,:6,:7,:8,:9,:10,:11,:12,:13,:14,:15,:16,:17);";
        console.log("开始插入数据！");
        var blob = fs.createReadStream("c:\\三国演义.jpg");
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
                { val: "《三国演义》是中国第一部长篇章回体小说，中国小说由短篇发展至长篇的原因与说书有关。" },
                { val: blob },
                { val: "25" },
                { val: 943 },
                { val: 93000 },
                { val: new Date("2006-03-20") },
                { val: new Date("1900-01-01") }
            ]
        );
        await conn.execute("commit;");
        console.log("插入数据结束！");
    } catch (err) {
        throw new Error("insertTable error: " + err.message);
    }
}
//修改产品信息表数据
async function updateTable() {
    try {
    console.log("更新数据！");
        var sql = "UPDATE production.product SET name = :name "
            + "WHERE productid = 6;";
        // 按名称绑定变量
        await conn.execute(sql, { name: { val: "三国演义（上）" } });
        await conn.execute("commit;");
    } catch (err) {
        throw new Error("updateTable error: " + err.message);
    }
}
//删除产品信息表数据 
async function deleteTable() {
try {
        console.log("删除数据！");
        var sql = "DELETE FROM production.product WHERE productid = 27;"
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
        // Lob对象使用完需关闭
        await lob.close();
        console.log(buffer);
        return result;
    } catch (err) {
        throw new Error("queryTable error: " + err.message);
    }
}
//读取数据库返回的Lob对象
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

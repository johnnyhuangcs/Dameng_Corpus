

# UTL_HTTP 包

UTL_HTTP 包提供了通过 HTTP 协议获取网页内容的功能。

在发起 HTTP 请求和读取 HTTP 响应消息的过程中，各函数的调用顺序如下图所示。

![UTL_HTTP 包中函数使用顺序图.jpg](https://eco.dameng.com/eco-file-server/file/eco/preview/20240816113643XKQ7I092UB6FUNEU6F)

图35.1  HTTP请求/响应消息相关函数使用顺序图

REQUEST、SET_TRANSFER_TIMEOUT、SET_WALLET、SET_DETAILED_EXCP_SUPPORT、GET_DETAILED_EXCP_SUPPORT 和 SET_RESPONSE_ERROR_CHECK 不参与上述使用顺序图，用户可在任意时刻调用上述函数和过程。REQUEST 函数相当于上图中各函数的缺省配置，最终返回指定网页的前 2000 个字节。

另外，达梦支持基于 HTTP 1.1 协议的持久连接功能，与该功能相关的所有过程及函数也不参与上述使用顺序图，用户可在任意时刻调用相关过程或函数，以对当前会话的持久连接进行相应设置。

## 35.1 相关方法

下面对各个函数和过程进行详细说明。

  1. REQ



定义 REQ 为 RECORD 类型，用来存放请求消息。

语法如下：

```
TYPE REQ IS RECORD (
	URL           		VARCHAR2(32767),
	METHOD        		VARCHAR2(64),
	HTTP_VERSION  		VARCHAR2(64)
);
```

参数详解

  * url web 服务器的 url 地址。url 中可以包含用户名密码。
  * method 对指定 url 发起的操作命令。 常见的命令有 get、post，默认为 Get。
  * http_version  
http 协议版本号，UTL_HTTP 包提供 1.0 和 1.1 两个版本宏定义，若此参数设为 NULL，则默认使用最新的 http
协议版本。此参数默认为 NULL。



  2. RESP



定义 RESP 为 RECORD 类型，用来存放响应消息。

语法如下：

```
TYPE RESP IS RECORD (
	STATUS_CODE			PLS_INTEGER,
   	REASON_PHRASE  		VARCHAR2(256),
   	HTTP_VERSION   		VARCHAR2(64)
);
```

参数详解

  * STATUS_CODE web 服务器返回的状态码。
  * REASON_PHRASE 关于状态码的简单文本描述。
  * HTTP_VERSION http 协议版本号，UTL_HTTP 包提供 1.0 和 1.1 两个版本宏定义，若此参数设为 NULL，则默认使用最新的 http 协议版本。此参数默认为 NULL。



  3. CONNECTION



定义 CONNECTION 为 RECORD 类型，用来存放 HTTP 请求完成后的持久连接信息。

语法如下：

```
TYPE CONNECTION IS RECORD (
	HOST         		VARCHAR2(256), 
	PORT         		INTEGER, 
	PROXY_HOST  		VARCHAR2(256), 
    PROXY_PORT  		INTEGER,
    SSL           	BOOLEAN
);
```

参数详解

  * HOST WEB 服务器 IP 地址。
  * PORT WEB 服务器端口号。
  * PROXY_HOST WEB 服务器代理 IP 地址。
  * PROXY_PORT WEB 服务器代理端口号。
  * SSL 是否使用 SSL，保留参数。



  4. CONNECTION_TABLE



定义 CONNECTION_TABLE 为 CONNECTION 的索引表类型，用来存放所有持久连接相关信息。

语法如下：

```
TYPE CONNECTION_TABLE IS TABLE OF CONNECTION INDEX BY BINARY_INTEGER;
```

  5. BEGIN_REQUEST



用于发起一个新的 http 请求。单个会话若调用 BEGIN_REQUEST 后未调用 END_REQUEST 结束，则最多同时发起 20 个 http 请求。

语法如下：

```
FUNCTION 	BEGIN_REQUEST (
   	URL               	IN  		VARCHAR2,
  	METHOD            	IN  		VARCHAR2				DEFAULT 'GET',
   	HTTP_VERSION      	IN  		VARCHAR2				DEFAULT NULL,
   	REQUEST_CONTEXT   	IN  		REQUEST_CONTEXT_KEY		DEFAULT NULL
)RETURN REQ;
```

参数详解

  * url web 服务器的 url 地址。url 中可以包含用户名密码。
  * method 对指定 url 发起的操作命令。
  * http_version http 协议版本号，UTL_HTTP 包提供 1.0 和 1.1 两个版本宏定义，若此参数设为 NULL，则默认使用最新的 http 协议版本。此参数默认为 NULL。
  * request_context 目前达梦不支持此参数，仅用作兼容参数。



返回值

HTTP 请求消息。

  6. END_REQUEST



用于结束 http 请求。

语法如下：

```
PROCEDURE END_REQUEST (
	R		IN OUT		REQ
);
```

参数详解

  * R http 请求句柄。



  7. GET_RESPONSE



用于读取 http 响应消息。

语法如下：

```
FUNCTION GET_RESPONSE (

	R		IN OUT		REQ

) RETURN RESP;
```

参数详解

  * R http 请求消息。



返回值

HTTP 请求句柄。

  8. END_RESPONSE



用于结束 http 响应操作。

语法如下：

```
PROCEDURE END_RESPONSE (
	R  		IN OUT 		RESP
);
```

参数详解

  * R http 响应句柄。



  9. WRITE_TEXT



用于向 http 请求 body 中填写数据。

语法如下：

```
PROCEDURE WRITE_TEXT(
   	R     	IN OUT 		REQ,
	DATA  	IN          VARCHAR2 CHARACTER SET ANY_CS
);
```

参数详解

  * R http 请求消息。
  * DATA 准备向 body 写入的数据。



  10. READ_TEXT



用于读取 http 响应消息中 body 的内容并将内容输出到指定的缓冲区中。body 中的数据将自动转换成数据库所指定的字符集。

语法如下：

```
PROCEDURE READ_TEXT(
   	R     	IN OUT 		RESP,
	DATA  	OUT 		VARCHAR2 CHARACTER SET ANY_CS,
   	LEN   	IN 			PLS_INTEGER DEFAULT NULL
);
```

参数详解

  * R http 请求消息。
  * DATA 读取 body 中的数据。
  * LEN 请求读取的数据长度。



  11. READ_RAW



用于读取 http 响应消息中 body 的内容并将内容输出到指定的缓冲区中。不对 body 中的数据进行字符集转码操作。

语法如下：

```
PROCEDURE READ_RAW(
   	R     	IN OUT 		RESP,
	DATA  	OUT 		VARBINARY,
   	LEN   	IN 			PLS_INTEGER DEFAULT NULL
);
```

参数详解

  * R http 请求消息。
  * DATA 读取 body 中的数据。
  * LEN 请求读取的数据长度。



  12. READ_LINE



用于按行读取 http 响应消息中 body 的内容并将内容输出到指定的缓冲区中。body 中的数据将自动转换成数据库所指定的字符集。

语法如下：

```
PROCEDURE READ_LINE(
   	R			 	IN OUT 		RESP,
	DATA		 	OUT 		VARCHAR2,
   	REMOVE_CRLF	 	IN 			BOOLEAN DEFAULT FALSE
);
```

参数详解

  * R http 请求消息。
  * DATA 读取 body 中的数据。
  * REMOVE_CRLF 读取的数据是否去除换行标识，默认不去除。



  13. SET_HEADER



用于设置 http 请求的头信息。设置头信息的请求将被立即发送到 web 服务器。

语法如下：

```
PROCEDURE SET_HEADER (
   	R      		IN OUT 		REQ,
   	NAME   		IN 			VARCHAR2,
	VALUE  		IN 			VARCHAR2 	DEFAULT NULL
);
```

参数详解

  * R http 请求消息。
  * NAME header 中请求设置的属性名。
  * VALUE header 中请求设置的属性值。



  14. GET_HEADER



用于获取 http 响应头信息。过程将响应头中的第 n 个属性名及属性值返回。

语法如下：

```
PROCEDURE GET_HEADER (
   	R      		IN OUT 		RESP,
   	N      		IN 			PLS_INTEGER,
   	NAME   		OUT 		VARCHAR2,
	VALUE  		OUT 		VARCHAR2
);
```

参数详解

  * R http 响应消息。
  * N header 中的第 n 个属性。
  * NAME 属性名。
  * VALUE 属性值。



  15. SET_BODY_CHARSET



用于设置 body 的字符集，至于是请求还是响应消息中的 body，则需要看上下文。

语法如下：

```
PROCEDURE SET_BODY_CHARSET (
	CHARSET  	IN 			VARCHAR2 DEFAULT NULL
);
```

或者

```
PROCEDURE SET_BODY_CHARSET(
   	R        		IN OUT 		REQ,
	CHARSET  		IN 			VARCHAR2 DEFAULT NULL
);
```

或者

```
PROCEDURE SET_BODY_CHARSET(
   	R        		IN OUT 		RESP,
	CHARSET  		IN 			VARCHAR2 DEFAULT NULL
);
```

参数详解

  * R http 请求（或响应）句柄。
  * CHARSET 字符集名称。



  16. SET_AUTHENTICATION



用于设置 http 请求消息中的权限。WEB 服务器需要这些权限信息用于授权访问。

语法如下：

```
PROCEDURE SET_AUTHENTICATION(
   	R         	IN OUT 		REQ,
	USERNAME  	IN 			VARCHAR2,
	PASSWORD  	IN 			VARCHAR2,
   	SCHEME    	IN 			VARCHAR2 DEFAULT 'BASIC',
  	FOR_PROXY 	IN 			BOOLEAN DEFAULT FALSE
);
```

参数详解

  * R http 请求消息。
  * USENAME web 服务器端所需的用户名。
  * PASSWORD web 服务器端所需的密码。
  * SCHEME 访问模式，仅支持"basic"。
  * FOR_PROXY 是否通过代理来访问 web，默认为 FALSE。



  17. GET_AUTHENTICATION



从 http 响应消息中获取 WEB 服务器所需要的授权信息。

语法如下：

```
PROCEDURE GET_AUTHENTICATION(
   	R          	IN OUT 		RESP,
   	SCHEME     	OUT 		VARCHAR2,
   	REALM      	OUT 		VARCHAR2,
   	FOR_PROXY  	IN 			BOOLEAN  DEFAULT FALSE
);
```

参数详解

  * R http 响应消息。
  * SCHEME web 服务器的授权模式。
  * REALM web 服务器需要授权的区域。
  * FOR_PROXY 是否返回访问代理所需的权限而不是 WEB 服务器的权限，默认 FALSE。



  18. REQUEST



返回网页的前 2000 个字节。该函数可以直接用在 SQL 查询中。

语法如下：

```
FUNCTION 	REQUEST (
   	URL			IN  			VARCHAR2,
	PROXY		IN			VARCHAR2 DEFAULT NULL
)RETURN VARCHAR;
```

参数详解

  * URL web 服务器的 url 地址。url 中可以包含用户名密码。
  * PROXY 代理服务器的 URL。



返回值

返回网页的前 2000 个字节。

  19. SET_TRANSFER_TIMEOUT



设置 HTTP 相关连接的超时时长。

语法如下：

```
PROCEDURE SET_TRANSFER_TIMEOUT(
   	TIMEOUT		IN 		INT 		DEFAULT 60
);
```

或者

```
PROCEDURE SET_TRANSFER_TIMEOUT(
 	R			IN OUT	REQ ,
	TIMEOUT		IN 		INT 		DEFAULT 60
);
```

参数详解

  * R HTTP 请求句柄。若指定该参数，则仅设置指定的 HTTP 相关连接的超时时长；若不指定该参数，则设置当前会话后续所有 HTTP 相关连接的超时时长。
  * TIMEOUT 超时时长，若 http 连接超过该时长未响应，则报错：传输超时。单位为秒，取值范围为 1～2147483647，缺省为 60。



  20. SET_PERSISTENT_CONN_SUPPORT



打开或关闭当前会话/当前请求的持久连接功能。

语法如下：

```
PROCEDURE SET_PERSISTENT_CONN_SUPPORT (
	ENABLE    	IN 			BOOLEAN,
  	MAX_CONNS 	IN 			INTEGER 			DEFAULT 0
);
```

或者

```
PROCEDURE SET_PERSISTENT_CONN_SUPPORT(
    R      		IN OUT 		REQ,
	ENABLE 		IN     		BOOLEAN 			DEFAULT FALSE
);
```

参数详解

  * ENABLE 是否打开持久连接功能。
  * MAX_CONNS 持久连接个数的最大值。
  * R HTTP 请求句柄。



  21. GET_PERSISTENT_CONN_SUPPORT



获取当前会话的持久连接设置信息。

语法如下：

```
PROCEDURE GET_PERSISTENT_CONN_SUPPORT(
  	ENABLED   	OUT 		BOOLEAN,
  	MAX_CONNS 	OUT 		INTEGER
);
```

参数详解

  * ENABLED 当前会话是否已打开持久连接功能。
  * MAX_CONNS 持久连接个数的最大值。



  22. GET_PERSISTENT_CONN_COUNT



获取当前会话的持久连接个数。

语法如下：

```
FUNCTION GET_PERSISTENT_CONN_COUNT() RETURN INTEGER;
```

返回值

当前会话的持久连接个数。

  23. GET_PERSISTENT_CONNS



获取当前会话所有持久连接的相关信息。

语法如下：

```
PROCEDURE GET_PERSISTENT_CONNS(
	CONNECTIONS 	IN OUT 		CONNECTION_TABLE
);
```

参数详解

  * CONNECTIONS 当前会话持久连接的索引表。



  24. CLOSE_PERSISTENT_CONN



关闭指定的持久连接。

语法如下：

```
PROCEDURE CLOSE_PERSISTENT_CONN(
   	CONN	IN		CONNECTION  
);
```

参数详解

  * CONN 持久连接句柄。



  25. CLOSE_PERSISTENT_CONNS



关闭指定的持久连接或所有持久连接。

语法如下：

```
PROCEDURE CLOSE_PERSISTENT_CONNS(
	HOST       	IN 		VARCHAR2    	DEFAULT NULL,
  	PORT       	IN 		INTEGER 		DEFAULT NULL,
  	PROXY_HOST 	IN 		VARCHAR2    	DEFAULT NULL,
  	PROXY_PORT 	IN 		INTEGER 		DEFAULT NULL,
  	SSL        	IN 		BOOLEAN     	DEFAULT NULL
);
```

参数详解

  * HOST 待关闭持久连接的 IP 地址。若为 NULL，则关闭当前会话的所有持久连接。
  * PORT 待关闭持久连接的端口号。若为 NULL，则关闭指定 HOST 的所有持久连接。
  * PROXY_HOST 待关闭持久连接的代理 IP 地址。
  * PROXY_PORT 待关闭持久连接的代理端口号。
  * SSL 待关闭持久连接是否使用 SSL，保留参数，暂时忽略。



  26. WRITE_RAW



用于向 HTTP 请求消息的 BODY 中填写 BINARY 数据。

语法如下：

```
PROCEDURE WRITE_RAW(
   	R     	IN OUT 		REQ,
	DATA  	IN          VARBINARY
);
```

参数详解

  * R HTTP 请求消息。
  * DATA 准备向 BODY 写入的数据。



  27. GET_HEADER_BY_NAME



用于获取 HTTP 响应头信息。过程将获取响应头中的第 N 个属性名为指定属性名的属性值。

语法如下：

```
PROCEDURE GET_HEADER_BY_NAME (
   	R      		IN OUT 		RESP,
   	NAME   		IN 		VARCHAR2,
	VALUE  		OUT 		VARCHAR2,
   	N      		IN 			PLS_INTEGER DEFAULT 1
);
```

参数详解

  * R HTTP 响应消息。
  * NAME 指定的属性名。
  * VALUE 属性值。
  * N HEADER 中的第 N 个指定属性名的属性。



  28. SET_PROXY



用于配置 HTTP 代理服务器，便于数据库会话进行 HTTP 请求时经过代理服务器。

语法如下：

```
PROCEDURE SET_PROXY (
   PROXY             	   IN VARCHAR2,
   NO_PROXY_DOMAINS  IN VARCHAR2 DEFAULT NULL
);
```

参数详解

  * PROXY 指定代理服务器的 url。
  * NO_PROXY_DOMAINS 用于指定不需要经过代理服务器的 url，用“,”隔开。



  29. GET_PROXY



用于获取 HTTP 代理服务器的配置，获取的就是用 SET_PROXY 配置的内容。

语法如下：

```
PROCEDURE GET_PROXY (
	PROXY             OUT NOCOPY VARCHAR2,
	NO_PROXY_DOMAINS  OUT NOCOPY VARCHAR2
);
```

参数详解

  * PROXY 指定代理服务器的 url。
  * NO_PROXY_DOMAINS 用于指定不需要经过代理服务器的 url，用“,”隔开。



  30. SET_WALLET



用于设置钱包文件路径，钱包文件中包含客户端相关证书密钥等信息。该方法目前仅做语法兼容，暂无实际效果。

语法如下：

```
PROCEDURE SET_WALLET(
	PATH          IN VARCHAR2,
	PASSWORD    IN VARCHAR2 DEFAULT NULL
);
```

参数详解

  * PATH 钱包文件路径。
  * PASSWORD 钱包文件密码。



  31. SET_DETAILED_EXCP_SUPPORT



发起 http 请求过程中发生异常时，是否抛出详细的异常信息。

语法如下：

```
PROCEDURE SET_DETAILED_EXCP_SUPPORT(
	ENABLE_FLAG     IN BOOLEAN DEFAULT FALSE
);
```

参数详解

  * ENABLE_FLAG 是否抛出详细异常。TRUE：是；FALSE：否。默认值为 FALSE，此时请求异常时会固定抛出 REQUEST_FAILED 异常。



  32. GET_DETAILED_EXCP_SUPPORT



获取当前 http 请求异常配置信息。

语法如下：

```
PROCEDURE GET_DETAILED_EXCP_SUPPORT(
	ENABLE_FLAG     OUT BOOLEAN
);
```

参数详解

  * ENABLE_FLAG 是否抛出详细异常。



  33. SET_RESPONSE_ERROR_CHECK



是否检查 http 响应码。若是，则响应码为 4xx（4 开头的三位数字）时抛出 HTTP_CLIENT_ERROR 异常，响应码为 5xx（5 开头的三位数字）时抛出 HTTP_SERVER_ERROR 异常。

语法如下：

```
PROCEDURE SET_RESPONSE_ERROR_CHECK(
	ENABLE_FLAG     IN BOOLEAN DEFAULT FALSE
);
```

参数详解

  * ENABLE_FLAG 是否检查 http 响应码。TRUE：是；FALSE：否。默认值为 FALSE。



  34. GET_DETAILED_SQLCODE



获取 UTL_HTTP 包执行过程中抛出的异常码。

语法如下：

```
FUNCTION GET_DETAILED_SQLCODE RETURN INTEGER;
```

  35. GET_DETAILED_SQLERRM



获取 utl_http 包执行过程中抛出的异常错误描述。

语法如下：

```
FUNCTION GET_DETAILED_SQLERRM RETURN VARCHAR;
```

## 35.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
zzSP_CREATE_SYSTEM_PACKAGES (1,'UTL_HTTP');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

举一个使用 UTL_HTTP 包获取网页信息的例子。

```
declare
req utl_http.REQ; 
resp utl_http.resp; 
data varchar2(32563); 
receiveDate varchar; 
begin
req := utl_http.begin_request('http://192.168.0.104/blog/2015_06_05/CSDN_public_1433381803730.html', 'POST'); 
data := ' '; 
utl_http.write_text(req, data); 
resp := utl_http.get_response(req); 
utl_http.set_body_charset(resp, 'utf-8');
//使用read_text时需注意LEN参数取值，当其超出数据实际长度时会报错“[-20018]:已达到正文的末尾”
utl_http.read_text(resp, receiveDate, 1024); 
dbms_output.put_line(receiveDate); 
utl_http.end_response(resp); 
end;
```

输出的网页内容为：

```
<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "www.w3.org/TR/xhtml1/DTD/xhtml1-strict.dtd">
<html xmlns="www.w3.org/1999/xhtml">
 <head>
  <script type="text/javascript" src="c.csdnimg.cn/pubfooter/js/tracking.js" charset="utf-8"></script>
  <script type="text/javascript">
        var protocol = window.location.protocol;
        document.write('<script type="text/javascript" src="csdnimg.cn/pubfooter/js/repoAddr2.js?v=' + Math.random() + '"></' + 'script>');
    </script>
  <script id="allmobilize" charset="utf-8" src="a.yunshipei.com/46aae4d1e2371e4aa769798941cef698/allmobilize.min.js"></script>
  <meta http-equiv="Cache-Control" content="no-siteapp">
  <link rel="alternate" media="handheld" href="#">
  <title>使用UTL_HTTP包获取网页内容 - IndexMan的专栏
        - 博客频道 - CSDN.NET</title>
  <meta http-equiv="Content-Type" content="text/html; charset=utf-8">
  <meta name="description" content="UTL_HTTP 包提供了容易的方式通过HTTP协议获取网页内容"> 
```

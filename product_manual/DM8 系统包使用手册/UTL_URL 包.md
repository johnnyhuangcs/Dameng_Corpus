

# UTL_URL 包

UTL_URL 系统包用于对 URL 进行转码与解码操作。URL 是一个指向网页、图片等网络资源的字符串。访问网络资源使用的是 HTTP 协议。例如，DM 网站的 URL 为：http://www.dameng.com。通常，一个正确的 URL 字符串包含字母、数字、标点符号三种规范字符。这些字符都是非保留字。如果 URL 中含有非规范字符，那么就需要转码；如果 URL 中含有保留字符，则根据用户需要决定是否转码。

URL 中可以使用的规范字符和保留字符。如下所示：

  * 规范字符包括：



[A-Z]、[a-z]、[0-9]；

减号（-）、下划线（_）、句点 (.)、感叹号、波浪号 (~)、星号(*)、重音符号(')、左圆括号(()、右圆括号 () )。

  * 保留字符包括：



分号 (;)、斜线 (/)、问号 (?)、冒号 (:)、(@)、地址符 (&)、等号 (=)、加号(+)、美元符号 ($)、逗号 (,)。

## 31.1 相关方法

  1. UTL_URL.ESCAPE



对 URL 中的非法字符（及保留字符）进行转码。

语法如下：

```
UTL_URL.ESCAPE (
	URL                   	IN 		VARCHAR,
	ESCAPE_RESERVED_CHARS 	IN 		BOOLEAN  	DEFAULT  FALSE,
	URL_CHARSET        		IN 		VARCHAR2 	DEFAULT  'GBK'
)RETURN VARCHAR2;
```

参数详解

  * url 待转码的 URL。
  * escape_reserved_chars 指明是否转义 URL 的保留字符。
  * url_charset 指定 URL 的转码格式使用的字符集。字符集包含：UTF8、GBK、BIG5、ISO_8859_9、EUC_JP、EUC_KR、KOI8R、ISO_8859_1、SQL_ASCII、GB18030、ISO_8859_11。



返回值

返回转码后的 URL。

使用说明：

此存储函数转义基于 RFC2396 标准的 URL 中包含的非规范字符。

  2. UTL_URL.UNESCAPE



对 URL 进行解码。

语法如下：

```
UTL_URL.UNESCAPE (
	URL            	IN 		VARCHAR,
	URL_CHARSET    	IN 		VARCHAR2 	DEFAULT 'GBK'
)RETURN VARCHAR2;
```

参数详解

  * url 待解码 URL。

  * url_charset

待解码 URL
的编码格式使用的字符集。字符集包含：UTF8、GBK、BIG5、ISO_8859_9、EUC_JP、EUC_KR、KOI8R、ISO_8859_1、SQL_ASCII、GB18030、ISO_8859_11。



返回值

返回解码后的 URL。

## 31.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'UTL_URL');
```

第一步，对 URL（http：//wwwdameng.com/a url with space.html）使用 UTF8 字符集进行转码：

```
SELECT UTL_URL.ESCAPE ('http://www.dameng.com/a url with space.html', FALSE,'UTF8') FROM DUAL;
```

查询结果如下：

```
http://www.dameng.com/a%20url%20with%20space.html
```

第二步，对上面得到的 URL 进行解码：

```
SELECT UTL_URL.UNESCAPE ('http://www.dameng.com/a%20url%20with%20space.html', 'UTF8');
```

查询结果如下：

```
http://www.dameng.com/a url with space.html
```

比较可以发现，经过转码再解码后的 URL 与原来相同。

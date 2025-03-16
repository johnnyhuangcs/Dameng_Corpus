

# UTL_I18N 包

UTL_I18N 包提供字符串与十六进制编码的相互转换功能。

UTL_I18N 依赖于 UTL_RAW。创建 UTL_I18N 之前，确保 UTL_RAW 已成功创建。

## 36.1 相关方法

下面对各个函数和过程进行详细说明。

  1. STRING_TO_RAW



将字符串转换成指定字符集下的十六进制串数值。

语法如下：

```
FUNCTION STRING_TO_RAW(
	DATA			IN		VARCHAR,
	TO_CHARSET		IN		VARCHAR		DEFAULT NULL
) RETURN VARBINARY
```

参数详解

  * DATA 输入参数，字符串。
  * TO_CHARSET 输入参数，字符集。指定转换后的字符集。



返回值

转换后的十六进制串数值。若字符集输错了，转换失败，返回 NULL；单字节 0~255 用任何字符集编码都可以转成 ASCII 值；三种字符集库（GBK 库、UTF8 库和韩文库）下都支持转换到 UTF8 编码，只有 UTF8 库支持转到字符集（GBK、BIG5、ISO_8859_9、EUC_JP、EUC_KR、KOI8R、GB18030、SQL_ASCII、ISO_8859_1）。字符串输入 NULL，返回当前字符集下的编码。

  2. RAW_TO_CHAR



将从指定的字符集下的十六进制串数值转到字符串。

语法如下：

```
FUNCTION RAW_TO_CHAR(
	DATA  			IN 		VARBINARY,
	FROM_CHARSET 	IN 		VARCHAR 		DEFAULT NULL
) RETURN VARCHAR
```

参数详解

  * DATA 输入参数，VARBINARY 数据。
  * FROM_CHARSET 输入参数，字符集。转换前的字符集。



返回值

转换后的字符串。注意：若字符集输错了，转换失败，返回 NULL；ASCII 值用任何字符集编码都可以转成单字节；只有 UTF8 库支持指定字符集（GBK、BIG5、ISO_8859_9、EUC_JP、EUC_KR、KOI8R、GB18030、SQL_ASCII、ISO_8859_1）。十六进制串输入 NULL，返回在当前字符集下的字符串。

  3. ESCAPE_REFERENCE



将字符串中的特殊字符转义成引用表示形式。

语法如下：

```
FUNCTION ESCAPE_REFERENCE(
	STR  			IN 		VARCHAR,
	PAGE_CS_NAME 	IN 		VARCHAR 		DEFAULT NULL
) RETURN VARCHAR
```

参数详解

  * STR 输入参数，待转义的字符串。
  * PAGE_CS_NAME 输入参数，页字符集。目前仅做语法兼容，无实际作用。



返回值

转换后的字符串。

  4. UNESCAPE_REFERENCE



将字符串中的引用表示形式反转义成特殊字符。

语法如下：

```
FUNCTION UNESCAPE_REFERENCE (
	STR  			IN 		VARCHAR
) RETURN VARCHAR
```

参数详解

  * STR 输入参数，待反转义的字符串。



返回值

转换后的字符串。

## 36.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包。请先调用系统过程

SP_CREATE_SYSTEM_PACKAGES(1)创建系统包

```
SP_CREATE_SYSTEM_PACKAGES(1,'UTL_RAW');
SP_CREATE_SYSTEM_PACKAGES(1,'UTL_I18N');
```

例 1 string_to_raw 函数的使用。

一 将 GBK 库中，GBK 编码的字符串转换成指定字符集下的十六进制串数值。

```
SQL> select utl_i18n.string_to_raw('中国','utf8') from dual; 
查询结果如下：
0xE4B8ADE59BBD

SQL> select utl_i18n.string_to_raw('中国','GBK') from dual; 
查询结果如下：
0xD6D0B9FA

SQL> select utl_i18n.string_to_raw('abcdef','utf8') from dual;
查询结果如下：
0x616263646566

SQL> select utl_i18n.string_to_raw('abcdef','GBK') from dual;
查询结果如下：
0x616263646566
```

二 UTF8 库中，将 UTF8 编码的字符串转换成指定字符集下的十六进制串数值。

```
SQL> select utl_i18n.string_to_raw('備註','utf8');
查询结果如下：
0xB3C6B5F9
```

三 韩文库中，将韩文编码的字符串转换成指定字符集下的十六进制串数值。

```
SQL>select utl_i18n.string_to_raw('철수','EUC_KR') from dual;
查询结果如下：
0xC3B6BCF6
```

例 2 raw_to_char 函数的使用。

一 从指定字符集下的十六进制串数值转为 GBK 编码（例如，本机为 GBK 库）字符串。

```
select UTL_I18N.RAW_TO_CHAR('61', 'utf8') from dual;
查询结果如下：
a

select UTL_I18N.RAW_TO_CHAR('E4B8ADE59BBD', 'UTF8') from dual;
查询结果如下：
中国

select UTL_I18N.RAW_TO_CHAR('D6D0B9FA', 'GBK') from dual;
查询结果如下：
中国

SQL> select UTL_I18N.RAW_TO_CHAR('616263', 'GBK') from dual; 
查询结果如下：
abc 

select UTL_I18N.RAW_TO_CHAR('616263', 'UTF8') from dual;
查询结果如下：
abc 
```

二 从指定字符集下的十六进制串数值转为 UTF8 编码（例如，本机为 UTF8 库）字符串。

```
select UTL_I18N.RAW_TO_CHAR(utl_i18n.string_to_raw('備註','utf8'),'utf8') from dual;
查询结果如下：
備註
```

三 从指定字符集下的十六进制串数值转为韩文编码（例如，本机为韩文库）字符串。

```
select UTL_I18N.RAW_TO_CHAR(0xC3B6BCF6,'EUC_KR') from dual; 
查询结果如下：
철수
```

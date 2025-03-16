

# DBMS_XMLGEN 包

DBMS_XMLGEN 系统包用于将 SQL 查询结果转换成 XML 文档。

## 23.1 使用前提

DBMS_XMLGEN 包的使用依赖 dbms_LOB 包。所以，在创建 DBMS_XMLGEN 包之前，请先成功创建 dbms_LOB 包。

## 23.2 相关方法

  1. NEWCONTEXT()



申请上下文句柄。

语法如下：

```
FUNCTION NEWCONTEXT ( 
	QUERYSTRING		IN		VARCHAR(32767)
) RETURN CTXHANDLE;
或
FUNCTION NEWCONTEXT ( 
	QUERY			IN		CURSOR
) RETURN CTXHANDLE;
```

参数详解

  * queryString 查询语句，字符串类型。
  * query 查询语句的引用游标。



返回值

ctxHandle：上下文句柄。

  2. GETXML()



GETXML()方法用来获取 XML 数据，有以下三种调用方式。

  1. 根据指定的最大获取行数，将获取的数据添加在传入的 clob 的内容之后。使用该函数可以重用 clob，避免额外的 clob 复制。



语法如下：

```
FUNCTION GETXML( 
    CTX     		IN		INTEGER,
	TMPCLOB			INOUT	CLOB,
	DTDORSCHEMA		IN		INTEGER
) RETURN INTEGER;
```

参数详解

  * cxt 之前根据查询语句获取的上下文句柄。
  * tmpclob 写入 XML 数据的 clob。
  * dtdOrSchema 仅支持 0，缺省为 0，可以不指定。



返回值

总是为 0。

  2. 此方法会将获取的 XML 数据放在一个临时生成的 clob 中，并返回这个临时 clob，该临时 clob 必须通过 DBMS_LOB.FREETEMPORARY 进行释放。



语法如下：

```
FUNCTION GETXML( 
	CTX     		IN		INTEGER,
	DTDORSCHEMA		IN		INTEGER
) RETURN CLOB;
```

参数详解

  * cxt 之前根据查询语句获取的上下文句柄。
  * dtdOrSchema 仅支持 0，缺省为 0，可以不指定。



返回值

包含获取的 XML 数据的 clob。

  3. 此方法根据指定的 SQL 语句，生成内部临时上下文句柄，获取 XML 数据并返回，并释放临时上下文句柄。



语法如下：

```
FUNCTION GETXML( 
	SQLQUERY    	IN		VARCHAR(32767),
	DTDORSCHEMA		IN		INTEGER
)RETURN CLOB;
```

参数详解

  * sqlquery 查询语句。
  * dtdOrSchema 仅支持 0，缺省为 0，可以不指定。



返回值

包含获取的 XML 数据的 clob。

  3. GETNUMROWSPROCESSED()



调用 GETXML 函数之后，使用该函数获取实际获取的数据行数，该函数的返回值不包含跳过的数据。

语法如下：

```
FUNCTION GETNUMROWSPROCESSED(
	CTX		IN		INTEGER
)RETURN BIGINT;
```

参数详解

  * ctx 操作的上下文句柄。



返回值

GETXML 实际获取的行数。

  4. CLOSECONTEXT()



关闭上下文句柄并释放相关资源。

语法如下：

```
PROCEDURE CLOSECONTEXT(
	CTX		IN		INTEGER
)RETURN BIGINT;
```

参数详解

  * ctx 待关闭的上下文句柄。



  5. CONVERT()



将 XML 数据在是否需要保留特殊字符之间进行转换。

由于 CONVERT 为系统保留字，使用该方法时需要在方法名外添加双引号，即 DBMS_XMLGEN."CONVERT"。

语法如下：

```
FUNCTION CONVERT(
	XMLDATA		IN		VARCHAR(32767),
	FLAG		IN		INTEGER
)RETURN VARCHAR(32767);
或
FUNCTION CONVERT(
	XMLDATA		IN		CLOB,
	FLAG		IN		INTEGER
)RETURN CLOB;
```

参数详解

  * xmlData 需要转换的 XML 数据。
  * flag 转换标记，取值 ENTITY_ENCODE 和 ENTITY_DECODE，缺省为 ENTITY_ENCODE。



返回值

转换之后的数据。

  6. RESTARTQUERY()



重新查询。

语法如下：

```
PROCEDURE RESTARTQUERY(
	CTX		IN		INTEGER
);
```

参数详解

  * ctx 操作的上下文句柄。



  7. SETCONVERTSPECIALCHARS()



设置获取结果集 XML 时特殊字符是否转化。

语法如下：

```
PROCEDURE SETCONVERTSPECIALCHARS(
	CTX		IN		INTEGER,
	CONV	IN		BOOLEAN
);
```

参数详解

  * ctx 操作的上下文句柄。
  * conv 是否要转化特殊字符。



  8. SETMAXROWS()



设置每次调用 GETXML 时的最大获取行数。

语法如下：

```
PROCEDURE SETMAXROWS(
	CTX			IN		INTEGER,
	MAXROWS		IN		BIGINT
);
```

参数详解

  * ctx 操作的上下文句柄。
  * maxRows 设置的最大获取行数。



  9. SETNULLHANDLING()



设置 XML 中对 NULL 值的表示方法。

语法如下：

```
PROCEDURE SETNULLHANDLING(
	CTX		IN		INTEGER,
	FLAG	IN		INTEGER
);
```

参数详解

  * ctx 操作的上下文句柄。

  * flag NULL 值的表示方法，可有如下三种取值。

    * DROP_NULLS 0，也是缺省值，直接跳过值为 NULL 的列。
    * NULL_ATTR 1，在值为 NULL 的列的列名标签中显示 xsi:nil="true"。
    * EMPTY_TAG 2，值为 NULL 的列仅显示列名标签。



  10. SETROWSETTAG()



设置 XML 文档中 ROWSET 标签的名称，默认的标签名为 ROWSET。

语法如下：

```
PROCEDURE SETROWSETTAG(
	CTX				IN		INTEGER,
	ROWSETTAGNAME	IN		VARCHAR(32767)
);
```

参数详解

  * ctx 操作的上下文句柄。
  * rowSetTagName 设置的标签名。



  11. SETROWTAG()



设置 XML 文档中 ROW 标签的名称，默认的标签名为 ROW。

语法如下：

```
PROCEDURE SETROWTAG(
	CTX				IN		INTEGER,
	ROWTAGNAME		IN		VARCHAR(32767)
);
```

参数详解

  * ctx 操作的上下文句柄。
  * rowTagName 设置的标签名。



  12. SETSKIPROWS()



设置每次调用 GETXML 函数时跳过的行数，可以用在 XML 或 HTML 数据获取时各页数据的生成，第一页时，设置为 0，后面各页时，设置为前面已经获取的数据行数。

语法如下：

```
PROCEDURE SETSKIPROWS(
	CTX			IN		INTEGER,
	SKIPROWS	IN		BIGINT
);
```

参数详解

  * ctx 操作的上下文句柄。
  * skipRows 跳过的行数。



  13. USEITEMTAGSFORCOLL()



设置生成的 XML 中，集合类型元素的标签名。DM 目前仅仅语法支持，调用后无实际效果。

语法如下：

```
PROCEDURE USEITEMTAGSFORCOLL(
	CTX		IN		INTEGER
);
```

参数详解

  * ctx 操作的上下文句柄。



  14. USENULLATTRIBUTEINDICATOR()



另一种设置 NULL 值表现方式的过程，设置是否用一个 XML 属性来表示 NULL，或者忽略 NULL。

语法如下：

```
PROCEDURE USENULLATTRIBUTEINDICATOR(
	CTX			IN		INTEGER,
	ATRRIND		IN		BOOLEAN
);
```

参数详解

  * ctx 操作的上下文句柄。
  * attrind TRUE 对应 SETNULLHANDLING 的 NULL_ATTR，为缺省值；FALSE 对应 SETNULLHANDLING 的 DROP_NULLS。



## 23.3 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_LOB');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_XMLGEN');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

  1. 数据准备



```
CREATE TABLE TEST_XML(A INT, B VARCHAR(30));
INSERT INTO TEST_XML VALUES(0, NULL);
DECLARE 
    I INT; 
BEGIN 
FOR I IN 1..100 LOOP 
    INSERT INTO TEST_XML VALUES(I, '<XML>$'); 
END LOOP; 
END;
/
COMMIT;
```

  2. 最简单的获取 XML 数据的例子



```
DECLARE
    DTA  CLOB;
BEGIN
    DTA := DBMS_XMLGEN.GETXML('SELECT * FROM TEST_XML WHERE ROWNUM < 4');
    DBMS_OUTPUT.PUT_LINE(DTA);
    DBMS_LOB.FREETEMPORARY(DTA);
END;
/
```

在这个例子中，直接指定查询语句调用 GETXML 方法，在 GETXML 中会生成临时的上下文句柄，这个句柄的所有属性都会使用缺省值。GETXML 返回一个临时生成的 CLOB，之后由程序员负责调用 DBMS_LOB.FREETEMPORARY 来释放它。

上述 PL/SQL 执行后将打印出以下的 XML 数据。

```
<?xml version="1.0"?>
<ROWSET>
<ROW>
<A>0</A>
</ROW>
<ROW>
<A>1</A>
<B><XML>$<B>
</ROW>
<ROW>
<A>2</A>
<B><XML>$</B>
</ROW>
</ROWSET>
```

  3. 典型的获取 XML 数据的例子



```
DECLARE
    CTX  DBMS_XMLGEN.CTXHANDLE;
    DTA  CLOB;
BEGIN
    CTX := DBMS_XMLGEN.NEWCONTEXT('SELECT * FROM TEST_XML');
    DBMS_XMLGEN.SETMAXROWS(CTX, 2);
    DTA := DBMS_XMLGEN.GETXML(CTX);
    DBMS_OUTPUT.PUT_LINE(DTA);
    DBMS_LOB.FREETEMPORARY(DTA);
    DBMS_XMLGEN.SETSKIPROWS(CTX, 5);
    DTA := DBMS_XMLGEN.GETXML(CTX);
    DBMS_OUTPUT.PUT_LINE(DTA);
    DBMS_LOB.FREETEMPORARY(DTA);
    DBMS_XMLGEN.CLOSECONTEXT(CTX);
END;
/
```

这是一个较为典型的获取 XML 数据的例子，首先申请一个上下文句柄，然后使用这个上下文句柄进行一些属性设置，再调用 GETXML 获取 XML 数据，最后关闭上下文句柄。

上述 PL/SQL 执行后将打印出以下的 XML 数据。

```
<?xml version="1.0"?>
<ROWSET>
<ROW>
<A>0</A>
<ROW>
<ROW>
<A>1</A>
<B><XML>$</B>
</ROW>
</ROWSET>

<?xml version="1.0"?>
<ROWSET>
<ROW>
<A>7</A>
<B><XML>$</B>
</ROW>
<ROW>
<A>8</A>
<B><XML>$</B>
</ROW>
</ROWSET>
```

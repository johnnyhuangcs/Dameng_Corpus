

# DBMS_XMLDOM包

DBMS_XMLDOM 包用于将 XML 数据解析成一系列结点，这些结点组成一个 DOM 树状结构。通过对这些结点的增删改操作从而达到对 XML 数据的增删改操作。

一个 XML 数据的 DOM 树形结构由各种不同类型的结点组成。

## 46.1  数据类型

DBMS_XMLDOM 包定义了若干 XML 数据类型：

  1. DOMNODE



DOMNODE 类型结点，是其它类型结点的总称。下文的任何一种结点都属于 DOMNODE。

例 黑体字为一个 NOMNODE 结点。

<A>ABC</A>

  2. DOMNAMEDNODEMAP



存放 \< 名称-DOM 结点 > 的映射表，通常用于根据结点名称来检索 DOM 结点。

  3. DOMNODELIST



DOM 结点链表。

  4. DOMATTR



DOM 属性类型结点。

例 黑体字为一个 DOMATTR 结点。

\<A ATTR=’VALUE1’>\</A>

  5. DOMCDATASECTION



DOM 数据区块类型结点。

例 黑体字为一个 DOMCDATASECTION 结点。

\<A>\<![CDATA [……]]>\</A>

  6. DOMCHARACTERDATA



DOM 字符串类型数据结点。

例 黑体字（省略号代表的部分）为一个 DOMCHARACTERDATA 结点。

\<A>\<![CDATA [……]]>\</A>

  7. DOMCOMMENT



DOM 注释类型结点。

例 黑体字为一个 DOM COMMENT 结点。

\<A> \<!-- -- …… -->\</A>

  8. DOMDOCUMENTFRAGMENT



DOM 分片文档类型结点。

  9. DOMELEMENT



DOM 元素类型结点。

例 黑体字为一个 DOMELEMENT 结点。

<A>ELEMENT</A>

  10. DOMENTITY



DOM 实体类型结点。

例 黑体字为一个 DOMENTITY 结点。

\<A> \<! ENTITY XML "EXTENSIBLE MARKUP LANGUAGE">\</A>

  11. DOMENTITYREFERENCE



DOM 实体引用类型结点。

例 黑体字为一个 DOMENTITYREFERENCE 结点。

 \<!DOCTYPE DOC [ \<!ENTITY ENT "HELLO">]>

\<DOC>&ENT;\</DOC>

  12. DOMNOTATION



DOM 符号类型结点。

例 黑体字为一个 DOMNOTATION 结点。

\<!DOCTYPE EXAMPLE SYSTEM "EXAMPLE.DTD"[ \<! NOTATION ABC1 SYSTEM "GOD1">]>

  13. DOMPROCESSINGINSTRUCTION



DOM 指令类型结点。

例 黑体字为一个 DOMPROCESSINGINSTRUCTION 结点。

\<A> \<?APP PRINT TEXT?>\</A>

  14. DOMTEXT



DOM 文本类型结点。

例 黑体字为一个 DOM TEXT 结点。

\<A> THIS IS TEXT NODE\</A>

  15. DOMIMPLEMENTATION



DOM 文档实现体结点。

  16. DOMDOCUMENTTYPE



DOM 文档定义类型结点。

例 黑体字为一个 DOMDOCUMENTTYPE 结点。

\<!DOCTYPE DOC [\<!ENTITY ENT "HELLO">]>

  17. DOMDOCUMENT



DOM 文档类型结点。

例 黑体字为一个 DOMDOCUMENT 结点。

\< ?XML VERSION='1.0'  ?>\<A>ELEMENT\</A>

## 46.2  相关方法

  1. HASFEATURE



DBMS_XMLDOM 包是否支持指定的特性。

语法如下：

```
FUNCTION HASFEATURE(
  DI DOMIMPLEMENTATION, 
  FEATURE IN VARCHAR2, 
  VERSION IN VARCHAR2
) RETURN BOOLEAN;
```

参数详解

  * DI



DOMIMPLEMENTATION 类型结点。

  * FEATURE



需要检测的特性，字符串类型。如 CORE 或者 XML。

  * VERSION



XML 的版本号，字符串类型。

返回值

TRUE：支持指定特性；FALSE：不支持指定特性。

  2. ISNULL



判断 XML 结点是否为空。

语法如下：

```
FUNCTION ISNULL(
  N <DOMTYPE>
) RETURN BOOLEAN;
```

参数详解

  * N

DOM 类型参数。如 DOMNAMEDNODEMAP、DOMNODELIST、DOMATTR 等。



返回值

TRUE：结点为空；FALSE：结点不为空。

  3. GETNODENAME



获取结点名。

语法如下：

```
FUNCTION GETNODENAME(
  N DOMNODE
) RETURN VARCHAR2;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

字符串类型的结点名。

  4. GETNODEVALUE



获取结点值。

语法如下：

```
FUNCTION GETNODEVALUE (
  N DOMNODE
) RETURN VARCHAR2;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

字符串类型的结点值。

  5. SETNODEVALUE



设置节点值。

语法如下：

```
PROCEDURE SETNODEVALUE (
  N DOMNODE,
  NODEVALUE VARCHAR2
);
```

参数详解

  * N

DOMNODE 类型结点。

  * NODEVALUE

新的结点值。



  6. GETNODETYPE



获取结点类型。

语法如下：

```
FUNCTION GETNODETYPE (
  N DOMNODE
) RETURN INT;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

整数形式的 XML 结点类型。

  7. GETPARENTNODE



获取父结点。

语法如下：

```
FUNCTION GETPARENTNODE (
  N DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前 XML 结点的父结点。

  8. GETCHILDNODES



获取孩子结点。

语法如下：

```
FUNCTION GETCHILDNODES (
  N DOMNODE
) RETURN DOMNODELIST;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的孩子结点列表。

  9. GETFIRSTCHILD



获取第一个结点。

语法如下：

```
FUNCTION GETFIRSTCHILD (
  N DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的第一个孩子结点。

  10. GETLASTCHILD



获取最后一个孩子结点。

语法如下：

```
FUNCTION GETLASTCHILD (
  N DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的最后一个孩子结点

  11. GETPREVIOUSSIBLING



获取前一个兄弟结点。

语法如下：

```
FUNCTION GETPREVIOUSSIBLING (
  N DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的前一个兄弟结点。

  12. GETNEXTSIBLING



获取后一个兄弟结点。

语法如下：

```
FUNCTION GETNEXTSIBLING (
  N DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的后一个兄弟孩子结点。

  13. GETATTRIBUTES



获取结点的属性。

语法如下：

```
FUNCTION GETATTRIBUTES (
  N DOMNODE
) RETURN DOMNAMEDNODEMAP;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的属性列表。

  14. GETOWNERDOCUMENT



获取结点所属的文档结点。

语法如下：

```
FUNCTION GETOWNERDOCUMENT (
  N DOMNODE
) RETURN DOMDOCUMENT;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的文档结点。

  15. GETPREFIX



获取结点命名空间标签。

语法如下：

```
FUNCTION GETPREFIX (
  N DOMNODE
) RETURN VARCHAR2;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

当前结点的命名空间标签。

  16. SETPREFIX



设置结点命名空间标签。

语法如下：

```
FUNCTION SETPREFIX (
  N DOMNODE,
  PREFIX VARCHAR2
);
```

参数详解

  * N

DOMNODE 类型结点。



  17. INSERTBEFORE



在指定结点前插入新结点。

语法如下：

```
FUNCTION INSERTBEFORE (
  N DOMNODE,
  NEWCHILD IN DOMNODE, 
  REFCHILD IN DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。

  * NEWCHHILD



新结点。

  * REFCHILD

参照结点。



返回值

插入结点。

  18. REPLACECHILD



替换指定的结点。

语法如下：

```
FUNCTION REPLACECHILD (
  N DOMNODE,
  NEWCHILD IN DOMNODE, 
  OLDCHILD IN DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。

  * NEWCHILD

新结点。

  * OLDCHILD

被替换结点。



返回值

被替换结点。

  19. REMOVECHILD



删除指定的结点。

语法如下：

```
FUNCTION REMOVECHILD (
  N DOMNODE,
  OLDCHILD IN DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。

  * OLDCHILD

待删除结点。



返回值

待删除结点。

  20. APPENDCHILD



追加新结点。

语法如下：

```
FUNCTION APPENDCHILD (
  N DOMNODE,
  NEWCHILD IN DOMNODE
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。

  * NEWCHILD

新结点。



返回值

新结点。

  21. HASCHILDNODES



判断指定结点是否有孩子结点。

语法如下：

```
FUNCTION HASCHILDNODES (
  N DOMNODE
) RETURN BOOLEAN;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

TRUE：有孩子结点；FALSE：没有孩子结点。

  22. HASATTRIBUTES



判断指定结点是否有属性。

语法如下：

```
FUNCTION HASATTRIBUTES (
  N DOMNODE
) RETURN BOOLEAN;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

TRUE：有属性；FALSE：无属性。

  23. CLONENODE



拷贝指定结点。

语法如下：

```
FUNCTION CLONENODE (
  N DOMNODE,
  DEEP BOOLEAN
) RETURN DOMNODE;
```

参数详解

  * N

DOMNODE 类型结点。

  * DEEP

是否递归拷贝子孙结点。



返回值

拷贝得到的新结点。

  24. WRITETOFILE



将结点格式化输出到指定文件。

语法如下：

```
PROCEDURE WRITETOFILE (
  N DOMNODE,
  FILENAME VARCHAR2
);

PROCEDURE WRITETOFILE(
  DOC DOMDOCUMENT, 
  FILENAME VARCHAR2
);
```

参数详解

  * N

DOMNODE 类型结点。

  * DOC

DOMDOCUMENT 类型结点。

  * FILENAME

文件名。



  25. WRITETOBUFFER



将结点格式化输出到指定缓冲区中。

语法如下：

```
PROCEDURE WRITETOBUFFER (
  N DOMNODE,
  BUFFER IN OUT VARCHAR2
);

PROCEDURE WRITETOBUFFER(
  DOC DOMDOCUMENT, 
  BUFFER IN OUT VARCHAR2
);
```

参数详解

  * N

DOMNODE 类型结点。

  * DOC

DOMDOCUMENT 类型结点。

  * BUFFER

缓冲区。



  26. WRITETOCLOB



将结点的内容写入到 LOB 类型变量里。

语法如下：

```
PROCEDURE WRITETOCLOB (
  N DOMNODE,
  CL IN OUT CLOB
);

PROCEDURE WRITETOCLOB(
  DOC DOMDOCUMENT, 
  CL IN OUT CLOB
);
```

参数详解

  * N

DOMNODE 类型结点。

  * DOC

DOMDOCUMENT 类型结点。

  * CL

CLOB 类型数据。



  27. MAKENODE



获取结点名。

语法如下：

```
FUNCTION MAKENODE(
  N <DOMTYPE>
) RETURN DOMNODE;
```

参数详解

  * N

DOM 类型参数。如 DOMNAMEDNODEMAP，DOMNODELIST，DOMATTR 等。



返回值

DOMNODE 类型结点。

  28. MAKEATTR



将 DOMNODE 类型结点转换为 DOMATTR 类型结点。

语法如下：

```
FUNCTION MAKEATTR (
  N DOMNODE
) RETURN DOMATTR;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMATTR 类型结点。

  29. MAKECDATASECTION



将 DOMNODE 类型结点转换为 DOMCDATASECTION 类型结点。

语法如下：

```
FUNCTION MAKECDATASECTION (
  N DOMNODE
) RETURN DOMCDATASECTION;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMCDATASECTION 类型结点。

  30. MAKECHARACTERDATA



将 DOMNODE 类型结点转换为 DOMCHARACTERDATA 类型结点。

语法如下：

```
FUNCTION MAKECHARACTERDATA (
  N DOMNODE
) RETURN DOMCHARACTERDATA;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMCHARACTERDATA 类型结点。

  31. MAKECOMMENT



将 DOMNODE 类型结点转换为 DOMCOMMENT 类型结点。

语法如下：

```
FUNCTION MAKECOMMENT (
  N DOMNODE
) RETURN DOMCOMMENT;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMCOMMENT 类型结点。

  32. MAKEDOCUMENTFRAGMENT



将 DOMNODE 类型结点转换为 DOMDOCUMENTFRAGMENT 类型结点。

语法如下：

```
FUNCTION MAKEDOCUMENTFRAGMENT (
  N DOMNODE
) RETURN DOMDOCUMENTFRAGMENT;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMDOCUMENTFRAGMENT 类型结点。

  33. MAKEDOCUMENTTYPE



将 DOMNODE 类型结点转换为 DOMDOCUMENTTYPE 类型结点。

语法如下：

```
FUNCTION MAKEDOCUMENTTYPE (
  N DOMNODE
) RETURN DOMDOCUMENTTYPE;
```

参数详解

  * N

DOMNODE 类型结点



返回值

DOMDOCUMENTTYPE 类型结点。

  34. MAKEELEMENT



将 DOMNODE 类型结点转换为 DOMELEMENT 类型结点。

语法如下：

```
FUNCTION MAKEELEMENT (
  N DOMNODE
) RETURN DOMELEMENT;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMELEMENT 类型结点。

  35. MAKEENTITY



将 DOMNODE 类型结点转换为 DOMENTITY 类型结点。

语法如下：

```
FUNCTION MAKEENTITY (
  N DOMNODE
) RETURN DOMENTITY;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMENTITY 类型结点。

  36. MAKEENTITYREFERENCE



将 DOMNODE 类型结点转换为 DOMENTITYREFERENCE 类型结点。

语法如下：

```
FUNCTION MAKEENTITYREFERENCE (
  N DOMNODE
) RETURN DOMENTITYREFERENCE;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMENTITYREFERENCE 类型结点。

  37. MAKENOTATION



将 DOMNODE 类型结点转换为 DOMNOTATION 类型结点。

语法如下：

```
FUNCTION MAKENOTATION (
  N DOMNODE
) RETURN DOMNOTATION;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMNOTATION 类型结点。

  38. MAKEPROCESSINGINSTRUCTION



将 DOMNODE 类型结点转换为 DOMPROCESSINGINSTRUCTION 类型结点。

语法如下：

```
FUNCTION MAKEPROCESSINGINSTRUCTION (
  N DOMNODE
) RETURN DOMPROCESSINGINSTRUCTION;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMPROCESSINGINSTRUCTION 类型结点。

  39. MAKETEXT



将 DOMNODE 类型结点转换为 DOMTEXT 类型结点。

语法如下：

```
FUNCTION MAKETEXT (
  N DOMNODE
) RETURN DOMTEXT;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMTEXT 类型结点。

  40. MAKEDOCUMENT



将 DOMNODE 类型结点转换为 DOMDOCUMENT 类型结点。

语法如下：

```
FUNCTION MAKEDOCUMENT (
  N DOMNODE
) RETURN DOMDOCUMENT;
```

参数详解

  * N

DOMNODE 类型结点。



返回值

DOMDOCUMENT 类型结点。

  41. GETNAMESPACE



获取节点的命名空间。

语法如下：

```
PROCEDURE GETNAMESPACE (
  N DOMNODE,
  DATA IN OUT VARCHAR2
);
```

参数详解

  * N

·DOMNODE 类型结点。

  * DATA

结点命名空间。



  42. GETLOCALNAME



获取结点名。

语法如下：

```
PROCEDURE GETLOCALNAME (
  N DOMNODE,
  DATA OUT VARCHAR2
);
```

参数详解

  * N

DOMNODE 类型结点。

  * DATA

结点名。



  43. GETEXPANDEDNAME



获取结点扩展名。

语法如下：

```
PROCEDURE GETEXPANDEDNAME (
  N DOMNODE,
  DATA OUT VARCHAR2
);
```

参数详解

  * N

DOMNODE 类型结点。

  * DATA

结点扩展名。



  44. FREENODE



释放结点。

语法如下：

```
PROCEDURE FREENODE (
  N DOMNODE
);
```

参数详解

  * N

DOMNODE 类型结点。



  45. ITEM



获取列表指定下标的结点。

语法如下：

```
FUNCTION ITEM (
  NL DOMNODELIST, 
  IDX IN INT
) RETURN DOMNODE;
```

参数详解

  * NL

NODE 列表。

  * IDX

下标号。



返回值

指定下标的结点。

  46. GETLENGTH



获取结点列表长度。

语法如下：

```
FUNCTION GETLENGTH (
  NL DOMNODELIST
) RETURN INT;
```

参数详解

  * NL

结点列表。



返回值

结点列表长度。

  47. FREENODELIST



释放结点列表。

语法如下：

```
PROCEDURE FREENODELIST (
  NL DOMNODELIST
);
```

参数详解

  * N

DOMNODE 类型结点。



  48. GETNAMEDITEM



获取指定名结点。

语法如下：

```
FUNCTION GETNAMEDITEM(
  NNM DOMNAMEDNODEMAP, 
  NAME IN VARCHAR2) 
RETURN DOMNODE;
```

参数详解

  * NNM

DOMNAMEDNODEMAP 类型结点。

  * NAME

结点名。



返回值

指定名的结点。

  49. SETNAMEDITEM



设置新结点。

语法如下：

```
FUNCTION SETNAMEDITEM(
  NNM DOMNAMEDNODEMAP, 
  ARG IN DOMNODE) 
RETURN DOMNODE;
```

参数详解

  * NNM

DOMNAMEDNODEMAP 类型结点。

  * ARG

新结点。



返回值

新结点。

  50. REMOVENAMEDITEM



删除指定名结点。

语法如下：

```
FUNCTION REMOVENAMEDITEM(
  NNM DOMNAMEDNODEMAP, 
  NAME IN VARCHAR2) 
RETURN DOMNODE;
```

参数详解

  * NNM

DOMNAMEDNODEMAP 类型结点。

  * NAME

结点名。



返回值

被删除的结点。

  51. ITEM



获取指定下标的结点。

语法如下：

```
FUNCTION ITEM(
  NNM DOMNAMEDNODEMAP, 
  IDX IN INT) 
RETURN DOMNODE;
```

参数详解

  * NNM

DOMNAMEDNODEMAP 类型结点。

  * IDX

下标。



返回值

指定下标结点。

  52. GETLENGTH



获取列表长度。

语法如下：

```
FUNCTION GETLENGTH (
  NNM DOMNAMEDNODEMAP
) RETURN INT;
```

参数详解

  * NNM

DOMNAMEDNODEMAP 类型结点。



返回值

列表长度。

  53. GETDATA



获取节点数据。

语法如下：

```
FUNCTION GETDATA (
  CD DOMCHARACTERDATA
) RETURN VARCHAR2;
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。



返回值

结点数据。

  54. SETDATA



为结点设置新数据。

语法如下：

```
PROCEDURE SETDATA(
  CD DOMCHARACTERDATA, 
  DATA IN VARCHAR2
);
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。

  * DATA

新数据。



  55. GETLENGTH



获取 DOMCHARACTERDATA 类型数据长度。

语法如下：

```
FUNCTION GETLENGTH(
  CD DOMCHARACTERDATA
) RETURN INT;
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。



返回值

字符类型数据长度。

  56. SUBSTRINGDATA



取指定位置下的字符串。

语法如下：

```
FUNCTION SUBSTRINGDATA (
  CD DOMCHARACTERDATA, 
  OFFSET IN INT, 
  CNT IN INT
) RETURN VARCHAR2;
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。

  * OFFSET

偏移。

  * CNT

字符数量。



返回值

字符串子串。

  57. APPENDDATA



追加字符串数据。

语法如下：

```
PROCEDURE APPENDDATA(
  CD DOMCHARACTERDATA, 
  ARG IN VARCHAR2
);
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。

  * ARG

追加的字符串。



  58. INSERTDATA



插入新字符串。

语法如下：

```
PROCEDURE INSERTDATA(
  CD DOMCHARACTERDATA, 
  OFFSET IN INT, 
  ARG IN VARCHAR2
);
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。

  * OFFSET

偏移位置。

  * ARG

插入的字符串。



  59. DELETEDATA



删除指定位置下的字符串。

语法如下：

```
PROCEDURE DELETEDATA(
  CD DOMCHARACTERDATA, 
  OFFSET IN INT, 
  CNT IN INT
);
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。

  * OFFSET

偏移位置。

  * CNT

删除字符数量。



  60. REPLACEDATA



替换字符数据。

语法如下：

```
PROCEDURE REPLACEDATA(
  CD DOMCHARACTERDATA, 
  OFFSET IN INT, 
  CNT IN INT, 
  ARG IN VARCHAR2
);
```

参数详解

  * CD

DOMCHARACTERDATA 类型结点。

  * OFFSET

偏移位置。

  * CNT

替换字符数量。

  * ARG

新字符串。



  61. GETNAME



获取属性名。

语法如下：

```
FUNCTION GETNAME (
  A DOMATTR
) RETURN VARCHAR2;
```

参数详解

  * A

DOMATTR 类型结构。



返回值

属性名。

  62. GETOWNERELEMENT



获取属性所属元素类型结点。

语法如下：

```
FUNCTION GETOWNERELEMENT (
  A DOMATTR
) RETURN DOMELEMENT;
```

参数详解

  * A

DOMATTR 类型结构。



返回值

元素类型结点。

  63. GETVALUE



获取属性值。

语法如下：

```
FUNCTION GETVALUE (
  A DOMATTR
) RETURN VARCHAR2;
```

参数详解

  * A

DOMATTR 类型结构。



返回值

属性值。

  64. SETVALUE



设置属性值。

语法如下：

```
PROCEDURE SETVALUE (
  A DOMATTR,
  NEWVALUE IN VARCHAR2
);
```

参数详解

  * A

DOMATTR 类型结构。

  * l NEWVALUE

新的属性值。



  65. GETQUALIFIEDNAME



获取属性全名。

语法如下：

```
FUNCTION GETQUALIFIEDNAME (
  A DOMATTR
) RETURN VARCHAR2;
```

参数详解

  * A

DOMATTR 类型结构。



返回值

字符串类型属性全名。

  66. GETNAMESPACE



获取属性的命名空间。

语法如下：

```
FUNCTION GETNAMESPACE (
  A DOMATTR
) RETURN VARCHAR2;
```

参数详解

  * A

DOMATTR 类型结构



返回值

属性的命名空间。

  67. GETLOCALNAME



获取属性名。

语法如下：

```
FUNCTION GETLOCALNAME (
  A DOMATTR
) RETURN VARCHAR2;
```

参数详解

  * A

DOMATTR 类型结构。



返回值

属性名。

  68. GETEXPANDEDNAME



获取属性的扩展名。

语法如下：

```
FUNCTION GETEXPANDEDNAME (
  A DOMATTR
) RETURN VARCHAR2;
```

参数详解

  * A

DOMATTR 类型结构。



返回值

属性扩展名。

  69. GETTAGNAME



获取元素结点名。

语法如下：

```
FUNCTION GETTAGNAME(
  ELEM DOMELEMENT
)RETURN VARCHAR2;
```

参数详解

  * ELEM

DOMELEMENT 类型结点。



返回值

元素结点名。

  70. GETATTRIBUTE



获取元素的属性值。

语法如下：

```
FUNCTION GETATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2
)RETURN VARCHAR2;

FUNCTION GETATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2, 
  NS IN VARCHAR2
)RETURN VARCHAR2;
```

参数详解

  * N

DOMNODE 类型结点。

  * NAME

属性名。

  * NS

命名空间。



返回值

元素属性值。

  71. HASATTRIBUTE



元素结点是否有指定属性。

语法如下：

```
FUNCTION HASATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2 
)RETURN BOOLEAN;

FUNCTION HASATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2, 
  NS IN VARCHAR2 
)RETURN BOOLEAN;
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * NAME

属性名。

  * NS

命名空间。



返回值

TRUE：有指定属性；FALSE：指定属性不存在。

  72. SETATTRIBUTE



设置属性。

语法如下：

```
PROCEDURE SETATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2, 
  NEWVALUE IN VARCHAR2
);

PROCEDURE SETATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2, 
  NEWVALUE IN VARCHAR2, 
  NS IN VARCHAR2
);
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * NAME

属性名。

  * NEWVALUE

新的属性值。

  * NS

命名空间。



  73. REMOVEATTRIBUTE



移除指定属性。

语法如下：

```
PROCEDURE REMOVEATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2
);

PROCEDURE REMOVEATTRIBUTE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2, 
  NS IN VARCHAR2
);
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * NAME

属性名。

  * NS

命名空间。



  74. GETATTRIBUTENODE



获取指定的属性结点。

语法如下：

```
FUNCTION GETATTRIBUTENODE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2 
)RETURN DOMATTR;

FUNCTION GETATTRIBUTENODE(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2, 
  NS IN VARCHAR2
)RETURN DOMATTR;
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * NAME

属性名。

  * NS

命名空间。



返回值

属性结点。

  75. SETATTRIBUTENODE



将属性结点设置到元素结点上。

语法如下：

```
FUNCTION SETATTRIBUTENODE(
  ELEM DOMELEMENT, 
  NEWATTR IN DOMATTR 
)RETURN DOMATTR;

FUNCTION SETATTRIBUTENODE(
  ELEM DOMELEMENT, 
  NEWATTR IN DOMATTR, 
  NS IN VARCHAR2 
)RETURN DOMATTR;
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * NEWATTR

新的属性结点。

  * NS

命名空间。



返回值

新设置的属性结点。

  76. REMOVEATTRIBUTENODE



移除元素结点上的属性结点。

语法如下：

```
FUNCTION REMOVEATTRIBUTENODE(
  ELEM DOMELEMENT, 
  OLDATTR IN DOMATTR 
)RETURN DOMATTR;
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * OLDATTR

待移除的属性结点。



返回值

移除的属性结点。

  77. GETELEMENTSBYTAGNAME



根据元素结点名查找元素结点。

语法如下：

```
FUNCTION GETELEMENTSBYTAGNAME(
  ELEM DOMELEMENT, 
  NAME IN VARCHAR2 
)RETURN DOMNODELIST;

FUNCTION GETELEMENTSBYTAGNAME(
  DOC DOMDOCUMENT, 
  NAME IN VARCHAR2 
)RETURN DOMNODELIST;
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * DOC

DOMDOCUMENT 类型结点。

  * NAME

元素结点名。



返回值

元素结点列表。

  78. GETCHILDRENBYTAGNAME



获取元素结点下的孩子结点。

语法如下：

```
FUNCTION GETCHILDRENBYTAGNAME(
  ELEM DOMELEMENT, 
  NAME VARCHAR2 
)RETURN DOMNODELIST;

FUNCTION GETCHILDRENBYTAGNAME(
  ELEM DOMELEMENT, 
  NAME VARCHAR2, 
  NS VARCHAR2 
)RETURN DOMNODELIST;
```

参数详解

  * ELEM

DOMELEMENT 类型结点。

  * NAME

结点名。

  * NS

命名空间。



返回值

孩子结点列表。

  79. FREEELEMENT



释放元素结点。

语法如下：

```
PROCEDURE FREEELEMENT(
  ELEM DOMELEMENT
);
```

参数详解

  * ELEM

DOMELEMENT 类型结点。



  80. SPLITTEXT



将指定文本结点分隔成两个文本结点。

语法如下：

```
FUNCTION SPLITTEXT(
  T DOMTEXT, 
  OFFSET IN INT 
)RETURN DOMTEXT;
```

参数详解

  * T

DOMTEXT 类型结点。

  * OFFSET

分割位置。



返回值

分割后的文本结点。

  81. GETDOCTYPE



获取文档的定义描述结构。

语法如下：

```
FUNCTION GETDOCTYPE(
  DOC DOMDOCUMENT 
)RETURN DOMDOCUMENTTYPE;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。



返回值

文档定义描述结构。

  82. NEWDOMDOCUMENT



构建 XML 文档结点。

语法如下：

```
FUNCTION NEWDOMDOCUMENT RETURN DOMDOCUMENT;或
FUNCTION NEWDOMDOCUMENT(
  XMLDOC IN VARCHAR2 
)RETURN DOMDOCUMENT;

FUNCTION NEWDOMDOCUMENT(
  CL IN CLOB 
)RETURN DOMDOCUMENT;
```

参数详解

  * XMLDOC

VARCHAR 类型的 XML 数据。

  * CL

CLOB 类型的 XML 数据。



返回值

DOMDOCUMENT 类型结点。

  83. GETDOCUMENTELEMENT



获取文档的根元素。

语法如下：

```
FUNCTION GETDOCUMENTELEMENT(
  DOC DOMDOCUMENT 
)RETURN DOMELEMENT;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。



返回值

文档根元素结点。

  84. CREATEELEMENT



创建元素结点。

语法如下：

```
FUNCTION CREATEELEMENT(
  DOC DOMDOCUMENT, 
  TAGNAME IN VARCHAR2 
)RETURN DOMELEMENT;

FUNCTION CREATEELEMENT(
  DOC DOMDOCUMENT, 
  TAGNAME IN VARCHAR2, 
  NS IN VARCHAR2 
)RETURN DOMELEMENT;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。

  * TAGNAME

元素结点名。

  * NS

命名空间。



返回值

元素类型结点。

  85. CREATETEXTNODE



创建文本类型结点。

语法如下：

```
FUNCTION CREATETEXTNODE(
  DOC DOMDOCUMENT, 
  DATA IN VARCHAR2 
)RETURN DOMTEXT;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。

  * DATA

文本内容。



返回值

文本类型结点。

  86. CREATECOMMENT



创建注释结点。

语法如下：

```
FUNCTION CREATECOMMENT(
  DOC DOMDOCUMENT, 
  DATA IN VARCHAR2 
)RETURN DOMCOMMENT;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。

  * DATA

注释内容。



返回值

注释类型结点。

  87. CREATECDATASECTION



创建字符区类型结点。

语法如下：

```
FUNCTION CREATECDATASECTION(
  DOC DOMDOCUMENT, 
  DATA IN VARCHAR2 
)RETURN DOMCDATASECTION;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。

  * DATA

字符区内容。



返回值

字符区类型结点。

  88. CREATEPROCESSINGINSTRUCTION



创建指令类型结点。

语法如下：

```
FUNCTION CREATEPROCESSINGINSTRUCTION(
  DOC DOMDOCUMENT, 
  TARGET IN VARCHAR2, 
  DATA IN VARCHAR2 
)RETURN DOMPROCESSINGINSTRUCTION;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。

  * TARGET

指令结点名。

  * DATA

指令结点内容。



返回值

指令类型结点。

  89. CREATEATTRIBUTE



创建属性类型结点。

语法如下：

```
FUNCTION CREATEATTRIBUTE(
  DOC DOMDOCUMENT, 
  NAME IN VARCHAR2
  )RETURN DOMATTR;
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。

  * NAME

属性名。



返回值

属性类型结点。

  90. FREEDOCUMENT



释放文档类型结点。

语法如下：

```
PROCEDURE FREEDOCUMENT(
  DOC DOMDOCUMENT
);
```

参数详解

  * DOC

DOMDOCUMENT 类型结点。



  91. FREEDOCTYPE



释放文档定义描述类型结点。

语法如下：

```
PROCEDURE FREEDOCTYPE(
  DT DOMDOCUMENTTYPE
);
```

参数详解

  * DT

DOMDOCUMENTTYPE 类型结点。



  92. IMPORTNODE



向指定文档中导入新结点，与 ADOPTNODE 接口相比，IMPORTNODE 是拷贝新结点到指定 DOC 上。

语法如下：

```
FUNCTION IMPORTNODE(
	DOC 			DOMDOCUMENT,
	IMPORTEDNODE 	DOMNODE,
	DEEP			BOOLEAN
) RETURN DOMNODE;
```

参数详解

  * DOC

DOCUMENT 文档结点。

  * IMPORTEDNODE

待导入的结点。

  * DEEP

是否级联导入孩子结点。



  93. ADOPTNODE



用于收养新结点，与 IMPORTNODE 的区别在于，ADOPTNODE 会将结点从源 DOC 中摘除。

语法如下：

```
FUNCTION ADOPTNODE(
	DOC 			DOMDOCUMENT,
	ADOPTEDNODE 	DOMNODE
) RETURN DOMNODE;
```

参数详解

  * DOC

DOCUMENT 文档结点。

  * ADOPTNODE

待收养的结点。



## 46.3  举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_XMLDOM');
```

以下是对 XML 数据进行解析，生成 DOM 结构，并对 DOM 类型数据进行操作。

```
DECLARE
SRC   VARCHAR(1000);
BUF   VARCHAR(1000);
DOC	 DBMS_XMLDOM.DOMDOCUMENT;
ELE   DBMS_XMLDOM.DOMELEMENT;
NODE 	DBMS_XMLDOM.DOMNODE;
SUBNODE 	DBMS_XMLDOM.DOMNODE;
LST   DBMS_XMLDOM.DOMNODELIST;
ATTR  DBMS_XMLDOM.DOMATTR;
BEGIN
SRC := '<A ATTR1="VAL1">TXT<B>SUB TXT</B></A>';
//解析并生成生成文档结构
DOC := DBMS_XMLDOM.NEWDOMDOCUMENT(SRC);
//获取根元素结点
ELE := DBMS_XMLDOM.GETDOCUMENTELEMENT(DOC);
//获取元素的ATTR1属性
ATTR := DBMS_XMLDOM.GETATTRIBUTENODE(ELE, 'ATTR1');
DBMS_OUTPUT.PUT_LINE('ATTR VALUE IS ' || DBMS_XMLDOM.GETVALUE(ATTR));
//获取ELE的孩子结点列表
NODE := DBMS_XMLDOM.MAKENODE(ELE);
LST := DBMS_XMLDOM.GETCHILDNODES(NODE);
DBMS_OUTPUT.PUT_LINE('LIST LEN IS ' || DBMS_XMLDOM.GETLENGTH(LST));
//第一个孩子结点为文本结点
SUBNODE := DBMS_XMLDOM.ITEM(LST, 0);
DBMS_OUTPUT.PUT_LINE('NODE VALUE IS ' || DBMS_XMLDOM.GETNODEVALUE(SUBNODE));
//第二个孩子结点为元素结点
SUBNODE := DBMS_XMLDOM.ITEM(LST, 1);
DBMS_XMLDOM.WRITETOBUFFER(SUBNODE, BUF);
DBMS_OUTPUT.PUT_LINE(BUF);
//获取第二个孩子结点的信息
IF DBMS_XMLDOM.HASCHILDNODES(SUBNODE) = TRUE THEN
  DBMS_OUTPUT.PUT_LINE('NODE HAS CHILD');
  SUBNODE := DBMS_XMLDOM.GETFIRSTCHILD(SUBNODE);
  DBMS_OUTPUT.PUT_LINE('SUB NODE VALUE IS ' || DBMS_XMLDOM.GETNODEVALUE(SUBNODE));
END IF;
//释放文档
DBMS_XMLDOM.FREEDOCUMENT(DOC);
END;
/
```

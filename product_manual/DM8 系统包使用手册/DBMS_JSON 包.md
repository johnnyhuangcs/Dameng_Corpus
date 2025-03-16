

# DBMS_JSON 包

DBMS_JSON 包中提供了五个 JSON 相关类，具体包括：JDOM_T、JSON_ELEMENT_T、JSON_SCALAR_T、JSON_ARRAY_T 和 JSON_OBJECT_T。

类说明：

JDOM_T：JSON 基础方法类。

JSON_ELEMENT_T：JSON 元素类，包含一个 JDOM_T 类的属性 DOM，此类不能实例化。JSON 元素实体可以为 JSON_SCALAR_T、JSON_ARRAY_T 或 JSON_OBJECT_T。

JSON_SCALAR_T：JSON 标量类，继承于 JSON_ELEMENT_T。JSON 标量包括：JSON STRING、NUMBER、true、false 和 null。

JSON_ARRAY_T：JSON ARRAY 类，继承于 JSON_ELEMENT_T。

JSON_OBJECT_T：JSON OBJECT 类，继承于 JSON_ELEMENT_T。

## 49.1 数据类型

  1. JSON_KEY_LIST



用于存放 JSON OBJECT 键值对名称。

语法如下：

```
CREATE OR REPLACE TYPE SYS.JSON_KEY_LIST IS TABLE OF VARCHAR(4000);
```

## 49.2 JDOM_T 类方法

  1. PARSE



分析文本，构造 JSON 元素，包括 JSON_SCALAR_T、JSON_ARRAY_T 和 JSON_OBJECT_T。

语法如下：

```
STATIC FUNCTION PARSE(JSN IN VARCHAR2) RETURN JDOM_T

或

STATIC FUNCTION parse(jsn IN CLOB) RETURN JDOM_T

或

STATIC FUNCTION parse(jsn IN BLOB) RETURN JDOM_T

或

STATIC FUNCTION parse(jsn IN BLOB, format IN VARCHAR2) RETURN JDOM_T
```

参数详解

  * JSN：表示 JSON 文本。
  * FORMAT：暂不支持。



返回值

JDOM_T 类

使用说明：

  1. 若 JSN 为 NULL，则返回 NULL；
  2. 若 JSN 为非法的 JSON 文本，则报错。



  2. CREATE_OBJ



构造一个空的 JSON OBJECT，不包含任何键值对，等价于“{}”。

语法如下：

```
STATIC FUNCTION CREATE_OBJ() RETURN JDOM_T
```

返回值

JDOM_T 类

  3. CREATE_ARR



构造一个空的 JSON ARRAY，不包含任何值，等价于“[]”。

语法如下：

```
STATIC FUNCTION create_Arr() RETURN JDOM_T
```

返回值

JDOM_T 类

  4. STRINGIFY



将 JSON 元素转换为 VARCHAR2 文本。

语法如下：

```
MEMBER FUNCTION stringify() RETURN VARCHAR2
```

返回值

表示 JSON 元素的 VARCHAR2 文本。

  5. to_String



将 JSON 元素以 VARCHAR2 文本返回。

语法如下：

```
MEMBER FUNCTION to_String() RETURN VARCHAR2
```

返回值

表示 JSON 元素的 VARCHAR2 文本。

  6. to_Clob



将 JSON 元素以 CLOB 文本返回。

语法如下：

```
MEMBER FUNCTION TO_CLOB(C  IN OUT  CLOB) RETURN CLOB
```

参数详解

  * C： 输入输出参数。



返回值

表示 JSON 元素的 CLOB 文本。

使用说明：

若 C 非 NULL，C 输出值与返回值相同。

  7. to_Blob



将 JSON 元素以 BLOB 文本返回。

语法如下：

```
MEMBER FUNCTION TO_BLOB(B  IN OUT  BLOB) RETURN BLOB
```

参数详解

  * B：输入输出参数。



返回值

表示 JSON 元素的 BLOB 文本。

使用说明：

若 B 非 NULL，B 输出值与返回值相同。

  8. to_Boolean



将 JSON 元素以 BOOLEAN 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Boolean() RETURN BOOLEAN
```

返回值

表示 JSON 元素的 BOOLEAN 值。

  9. to_Number



将 JSON 元素以 NUMBER 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Number() RETURN NUMBER
```

返回值

表示 JSON 元素的 NUMBER 值。

  10. to_Date



将 JSON 元素以 DATE 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Date() RETURN DATE
```

返回值

表示 JSON 元素的 DATE 值。

  11. to_Timestamp



将 JSON 元素以 TIMESTAMP 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Timestamp() RETURN TIMESTAMP
```

返回值

表示 JSON 元素的 TIMESTAMP 值。

  12. is_Object



检查 JSON 元素是否为 JSON OBJECT 类型。

语法如下：

```
MEMBER FUNCTION is_Object() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON OBJECT 类型；

FALSE：表示 JSON 元素不是 JSON OBJECT 类型。

  13. is_Array



检查 JSON 元素是否为 JSON ARRAY 类型。

语法如下：

```
MEMBER FUNCTION is_Array() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON ARRAY 类型；

FALSE：表示 JSON 元素不是 JSON ARRAY 类型。

  14. is_Scalar



检查 JSON 元素是否为 JSON 标量类型，包括：JSON STRING、NUMBER、true、false 和 null。

语法如下：

```
MEMBER FUNCTION is_Scalar() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON 标量类型；

FALSE：表示 JSON 元素不是 JSON 标量类型。

  15. is_String



检查 JSON 元素是否为 JSON STRING 类型。

语法如下：

```
MEMBER FUNCTION IS_STRING() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON STRING 类型；

FALSE：表示 JSON 元素不是 JSON STRING 类型。

  16. is_Number



检查 JSON 元素是否为 JSON NUMBER 类型。

语法如下：

```
MEMBER FUNCTION IS_NUMBER() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON NUMBER 类型；

FALSE：表示 JSON 元素不是 JSON NUMBER 类型。

  17. is_Boolean



检查 JSON 元素是否为 JSON 布尔类型，包括：true、false。

语法如下：

```
MEMBER FUNCTION is_Boolean() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON 布尔类型；

FALSE：表示 JSON 元素不是 JSON 布尔类型。

  18. is_True



检查 JSON 元素是否为 JSON 布尔类型 true。

语法如下：

```
MEMBER FUNCTION is_True() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON true；

FALSE：表示 JSON 元素不是 JSON true。

  19. is_False



检查 JSON 元素是否为 JSON 布尔类型 false。

语法如下：

```
MEMBER FUNCTION is_False() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON false；

FALSE：表示 JSON 元素不是 JSON false。

  20. is_Null



检查 JSON 元素是否为 JSON null。

语法如下：

```
MEMBER FUNCTION is_Null() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON null；

FALSE：表示 JSON 元素不是 JSON null。

  21. is_Date



检查 JSON 元素是否可以转换为 DATE 数据类型。

语法如下：

```
MEMBER FUNCTION is_Date() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素可以转换为 DATE；

FALSE：表示 JSON 元素不可以转换为 DATE。

  22. is_Timestamp



检查 JSON 元素是否可以转换为 TIMESTAMP 数据类型。

语法如下：

```
MEMBER FUNCTION IS_TIMESTAMP() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素可以转换为 TIMESTAMP；

FALSE：表示 JSON 元素不可以转换为 TIMESTAMP。

  23. get_Size



获取 JSON 元素的长度。

语法如下：

```
MEMBER FUNCTION get_Size() RETURN NUMBER
```

返回值

针对 JSON OBJECT 类型，返回键值对个数；

针对 JSON ARRAY 类型，返回数组元素个数；

针对 JSON 标量，返回 1。

  24. clone



克隆 JSON 元素。

语法如下：

```
MEMBER FUNCTION clone() RETURN JDOM_T
```

返回值

JDOM_T 类。

  25. put



针对 JSON OBJECT 或者 JSON ARRAY，PUT 函数的用法不同。

![](./media/clip_image001.gif)

为 JSON OBJECT 添加一个键值对。若指定的名称已存在，则覆盖指定名称对应的旧值。

语法如下：

```
MEMBER FUNCTION put(key VARCHAR2, val VARCHAR2) RETURN BOOLEAN

或

MEMBER FUNCTION put(key VARCHAR2, val NUMBER) RETURN BOOLEAN

或

MEMBER FUNCTION put(key VARCHAR2, val BOOLEAN) RETURN BOOLEAN

或

MEMBER FUNCTION put(key VARCHAR2, val DATE) RETURN BOOLEAN

或

MEMBER FUNCTION put(key VARCHAR2, val TIMESTAMP) RETURN BOOLEAN

或

MEMBER FUNCTION put(key VARCHAR2, val CLOB) RETURN BOOLEAN

或

MEMBER FUNCTION put(key VARCHAR2, val BLOB) RETURN BOOLEAN

或

MEMBER FUNCTION put(key VARCHAR2, val JDOM_T) RETURN BOOLEAN
```

参数详解

  * KEY：表示键值对的名称。
  * VAL：表示键值对的值。



返回值

TRUE：表示添加成功；

FALSE：表示添加失败。

![](./media/clip_image001.gif)

在 JSON ARRAY 指定位置添加一个数组元素。

语法如下：

```
MEMBER FUNCTION PUT(POS NUMBER, VAL VARCHAR2, OVERWRITE BOOLEAN

) RETURN BOOLEAN

或

MEMBER FUNCTION PUT(POS NUMBER, VAL NUMBER, OVERWRITE BOOLEAN

) RETURN BOOLEAN

或

MEMBER FUNCTION PUT(POS NUMBER, VAL BOOLEAN, OVERWRITE BOOLEAN

) RETURN BOOLEAN

或

MEMBER FUNCTION PUT(POS NUMBER, VAL DATE, OVERWRITE BOOLEAN

) RETURN BOOLEAN

或

MEMBER FUNCTION PUT(POS NUMBER, VAL TIMESTAMP, OVERWRITE BOOLEAN

) RETURN BOOLEAN

或

MEMBER FUNCTION PUT(POS NUMBER, VAL CLOB, OVERWRITE BOOLEAN

) RETURN BOOLEAN

或

MEMBER FUNCTION PUT(POS NUMBER, VAL BLOB, OVERWRITE BOOLEAN

) RETURN BOOLEAN

或

MEMBER FUNCTION PUT(POS NUMBER, VAL JDOM_T, OVERWRITE BOOLEAN

) RETURN BOOLEAN
```

参数详解

  * POS：表示添加元素的数组下标。
  * VAL：表示添加元素的值。
  * OVERWRITE：表示是否覆盖指定下标的旧值。



返回值

TRUE：表示添加成功；

FALSE：表示添加失败。

使用说明：

  1. 若 POS 位置不存在则报错；
  2. 若 OVERWRITE 为 TRUE，则 VAL 直接覆盖 POS 下标的元素；
  3. 若 OVERWRITE 为 FALSE，则 VAL 作为 POS 下标的元素，原始 POS 下标的元素以及 POS 之后的所有元素向后移动（即下标加 1）。



  26. put_Null



针对 JSON OBJECT 或者 JSON ARRAY，PUT_NULL 函数的用法不同。

![](./media/clip_image001.gif)

为 JSON OBJECT 添加一个键值对，键值对的值为 null。

语法如下：

```
MEMBER FUNCTION put_Null(key VARCHAR2) RETURN BOOLEAN
```

参数详解

  * KEY：表示键值对的名称。



返回值

TRUE：表示添加成功；

FALSE：表示添加失败。

![](./media/clip_image001.gif)

在 JSON ARRAY 指定位置添加一个数组元素，数组元素的值为 null。

语法如下：

```
MEMBER FUNCTION put_Null(pos NUMBER, overwrite BOOLEAN) RETURN BOOLEAN
```

参数详解

  * POS：表示添加元素的数组下标。
  * OVERWRITE：表示是否覆盖指定下标的旧值。



返回值

TRUE：表示添加成功；

FALSE：表示添加失败。

使用说明：

  1. 若 POS 位置不存在则报错；
  2. 若 OVERWRITE 为 TRUE，则 null 直接覆盖 POS 下标的元素；
  3. 若 OVERWRITE 为 FALSE，则 null 作为 POS 下标的元素，原始 POS 下标的元素以及 POS 之后的所有元素向后移动（即下标加 1）。



  27. "GET"



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 JDOM_T 方式返回。

语法如下：

```
MEMBER FUNCTION "GET"(key VARCHAR2) RETURN JDOM_T

或

MEMBER FUNCTION "GET"(pos NUMBER) RETURN JDOM_T
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 JDOM_T 类。

使用说明：

DM 的 GET 为关键字，使用 GET 函数请加上双引号。

  28. get_String



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 VARCHAR2 文本返回。

语法如下：

```
MEMBER FUNCTION get_String(key VARCHAR2) RETURN VARCHAR2

或

MEMBER FUNCTION get_String(pos NUMBER) RETURN VARCHAR2
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 VARCHAR2 文本。

  29. get_Boolean



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 BOOLEAN 数据类型返回。

语法如下：

```
MEMBER FUNCTION get_Boolean(key VARCHAR2) RETURN BOOLEAN

或

MEMBER FUNCTION get_Boolean(pos NUMBER) RETURN BOOLEAN
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 BOOLEAN 值。

  30. get_Number



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 NUMBER 数据类型返回。

语法如下：

```
MEMBER FUNCTION get_Number(key VARCHAR2) RETURN NUMBER

或

MEMBER FUNCTION get_Number(pos NUMBER) RETURN NUMBER
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 NUMBER 值。

  31. get_Date



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 DATE 数据类型返回。

语法如下：

```
MEMBER FUNCTION get_Date(key VARCHAR2) RETURN DATE

或

MEMBER FUNCTION get_Date(pos NUMBER) RETURN DATE
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 DATE 值。

  32. get_Timestamp



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 TIMESTAMP 数据类型返回。

语法如下：

```
MEMBER FUNCTION get_Timestamp(key VARCHAR2) RETURN TIMESTAMP

或

MEMBER FUNCTION get_Timestamp(pos NUMBER) RETURN TIMESTAMP
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 TIMESTAMP 值。

  33. get_Clob



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 CLOB 文本返回。

语法如下：

```
MEMBER FUNCTION get_Clob(key VARCHAR2, c IN OUT CLOB) return CLOB

或

MEMBER FUNCTION get_Clob(pos NUMBER, c IN OUT CLOB) return CLOB
```

参数详解

  * KEY：表示键值对的名称。
  * C： 输入输出参数。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 CLOB 文本。

使用说明：

若 C 非 NULL，则输出值 C 与返回值相同。

  34. get_Blob



将 JSON OBJECT 指定名称的 JSON 元素，或者 JSON ARRAY 指定下标的 JSON 元素，以 BLOB 文本返回。

语法如下：

```
MEMBER FUNCTION GET_BLOB(KEY VARCHAR2, B IN OUT BLOB) RETURN BLOB

或

MEMBER FUNCTION GET_BLOB(POS NUMBER, B IN OUT BLOB) RETURN BLOB
```

参数详解

  * KEY：表示键值对的名称。
  * B：输入输出参数。
  * POS：表示数组下标。



返回值

表示 JSON 元素的 BLOB 文本。

使用说明：

若 B 非 NULL，则输出值 B 与返回值相同。

  35. has_Key



JSON OBJECT 是否存在指定名称的键值对。

语法如下：

```
MEMBER FUNCTION has_Key(key VARCHAR2) RETURN BOOLEAN
```

参数详解

  * KEY：表示键值对的名称。



返回值

TRUE：表示 JSON OBJECT 存在名称为 KEY 的键值对；

FALSE：表示 JSON OBJECT 不存在名称为 KEY 的键值对。

  36. get_Keys



获取 JSON OBJECT 的所有键值对名称。

语法如下：

```
MEMBER FUNCTION get_Keys() RETURN JSON_KEY_LIST
```

返回值

表示 JSON OBJECT 的所有键值对名称的链表。

  37. get_Type



获取 JSON OBJECT 指定名称对应 JSON 元素的 JSON 类型，或者 JSON ARRAY 指定下标对应 JSON 元素的 JSON 类型。

语法如下：

```
MEMBER FUNCTION get_Type(key VARCHAR2) RETURN VARCHAR2

或

MEMBER FUNCTION get_Type(pos NUMBER) RETURN VARCHAR2
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

表示 JSON 元素类型的字符串，包括：SCALAR、OBJECT 和 ARRAY，分别表示 JSON 标量、JSON OBJECT 和 JSON ARRAY。

  38. remove



删除 JSON OBJECT 指定名称的键值对，或者 JSON ARRAY 指定下标的数组元素。

语法如下：

```
MEMBER FUNCTION remove(key VARCHAR2) RETURN BOOLEAN

或

MEMBER FUNCTION remove(pos NUMBER) RETURN BOOLEAN
```

参数详解

  * KEY：表示键值对的名称。
  * POS：表示数组下标。



返回值

TRUE：表示删除成功；

FALSE：表示删除失败。

  39. rename_key



重命名 JSON OBJECT 中的键值对名称。

语法如下：

```
MEMBER FUNCTION rename_Key(keyOld VARCHAR2, keyNew VARCHAR2) RETURN BOOLEAN
```

参数详解

  * KEYOLD：表示键值对的旧名称。
  * KEYNEW：表示键值对的新名称。



返回值

TRUE：表示重命名成功；

FALSE：表示重命名失败。

  40. append



在 JSON ARRAY 末尾添加一个数组元素。

语法如下：

```
MEMBER FUNCTION append(val VARCHAR2) RETURN BOOLEAN

或

MEMBER FUNCTION append(val NUMBER) RETURN BOOLEAN

或

MEMBER FUNCTION append(val BOOLEAN) RETURN BOOLEAN

或

MEMBER FUNCTION append(val DATE) RETURN BOOLEAN

或

MEMBER FUNCTION append(val TIMESTAMP) RETURN BOOLEAN

或

MEMBER FUNCTION append(val CLOB) RETURN BOOLEAN

或

MEMBER FUNCTION append(val BLOB) RETURN BOOLEAN

或

MEMBER FUNCTION append(val JDOM_T) RETURN BOOLEAN
```

参数详解

  * VAL：表示添加数组元素的值。



返回值

TRUE：表示添加成功；

FALSE：表示添加失败。

  41. append_Null



在 JSON ARRAY 末尾添加一个数组元素，数组元素的值为 null。

语法如下：

```
MEMBER FUNCTION append_Null() RETURN BOOLEAN
```

返回值

TRUE：表示添加成功；

FALSE：表示添加失败。

## 49.3 JSON_ELEMENT_T 类方法

  1. parse



分析文本，构造 JSON 元素，包括 JSON_SCALAR_T、JSON_OBJECT_T 和 JSON_ARRAY_T。

语法如下：

```
STATIC FUNCTION parse(jsn IN varchar2) RETURN JSON_ELEMENT_T
或
STATIC FUNCTION parse(jsn IN CLOB) RETURN JSON_ELEMENT_T
或
STATIC FUNCTION parse(jsn IN BLOB) RETURN JSON_ELEMENT_T
或
STATIC FUNCTION parse(jsn IN BLOB, format IN VARCHAR2) RETURN JSON_ELEMENT_T
```

参数详解

  * JSN：表示 JSON 文本。
  * FORMAT：暂不支持。



返回值

JSON_ELEMENT_T 类。

使用说明：

  1. 若 JSN 为 NULL，则返回 NULL；
  2. 若 JSN 为非法的 JSON 文本，则报错；
  3. 若 JSN 为 JSON 标量，则报错，只支持 JSON ARRAY 和 JSON OBJECT。



  2. stringify



将 JSON 元素转换为 VARCHAR2 文本。

语法如下：

```
MEMBER FUNCTION stringify() RETURN VARCHAR2
```

返回值

表示 JSON 元素转换的 VARCHAR2 文本。

  3. to_String



将 JSON 元素以 VARCHAR2 文本返回。

语法如下：

```
MEMBER FUNCTION to_String() RETURN VARCHAR2
```

返回值

表示 JSON 元素的 VARCHAR2 文本。

  4. to_Boolean



将 JSON 元素以 BOOLEAN 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Boolean() RETURN BOOLEAN
```

返回值

表示 JSON 元素的 BOOLEAN 值。

  5. to_Clob



将 JSON 元素以 CLOB 文本返回。

语法如下：

```
MEMBER FUNCTION to_Clob() return CLOB

或

MEMBER PROCEDURE to_Clob(c IN OUT CLOB)
```

参数详解

  * C： 输入输出参数。



返回值

表示 JSON 元素的 CLOB 文本。

  6. to_Blob



将 JSON 元素以 BLOB 文本返回。

语法如下：

```
MEMBER FUNCTION to_Blob() return BLOB

或

MEMBER PROCEDURE to_Blob(b IN OUT BLOB)
```

参数详解

  * b： 输入输出参数。



返回值

表示 JSON 元素的 BLOB 文本。

  7. to_Number



将 JSON 元素以 NUMBER 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Number() RETURN NUMBER
```

返回值

表示 JSON 元素的 NUMBER 值。

  8. to_Date



将 JSON 元素以 DATE 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Date() RETURN DATE
```

返回值

表示 JSON 元素的 DATE 值。

  9. to_Timestamp



将 JSON 元素以 TIMESTAMP 数据类型返回。

语法如下：

```
MEMBER FUNCTION to_Timestamp() RETURN TIMESTAMP
```

返回值

表示 JSON 元素的 TIMESTAMP 值。

  10. is_Object



检查 JSON 元素是否为 JSON OBJECT 类型。

语法如下：

```
MEMBER FUNCTION is_Object() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON OBJECT 类型；

FALSE：表示 JSON 元素不是 JSON OBJECT 类型。

  11. is_Array



检查 JSON 元素是否为 JSON ARRAY 类型。

语法如下：

```
MEMBER FUNCTION is_Array() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON ARRAY 类型；

FALSE：表示 JSON 元素不是 JSON ARRAY 类型。

  12. is_Scalar



检查 JSON 元素是否为 JSON 标量类型，包括：JSON STRING、NUMBER、true、false 和 null。

语法如下：

```
MEMBER FUNCTION is_Scalar() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON 标量类型；

FALSE：表示 JSON 元素不是 JSON 标量类型。

  13. is_String



检查 JSON 元素是否为 JSON STRING 类型。

语法如下：

```
MEMBER FUNCTION IS_STRING() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON STRING 类型；

FALSE：表示 JSON 元素不是 JSON STRING 类型。

  14. is_Number



检查 JSON 元素是否为 JSON NUMBER 类型。

语法如下：

```
MEMBER FUNCTION IS_NUMBER() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON NUMBER 类型；

FALSE：表示 JSON 元素不是 JSON NUMBER 类型。

  15. is_Boolean



检查 JSON 元素是否为 JSON 布尔类型，包括：true、false。

语法如下：

```
MEMBER FUNCTION is_Boolean() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON 布尔类型；

FALSE：表示 JSON 元素不是 JSON 布尔类型。

  16. is_True



检查 JSON 元素是否为 JSON 布尔类型 true。

语法如下：

```
MEMBER FUNCTION is_True() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON true；

FALSE：表示 JSON 元素不是 JSON true。

  17. is_False



检查 JSON 元素是否为 JSON 布尔类型 false。

语法如下：

```
MEMBER FUNCTION is_False() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON false；

FALSE：表示 JSON 元素不是 JSON false。

  18. is_Null



检查 JSON 元素是否为 JSON null。

语法如下：

```
MEMBER FUNCTION is_Null() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素是 JSON null；

FALSE：表示 JSON 元素不是 JSON null。

  19. is_Date



检查 JSON 元素是否可以转换为 DATE 数据类型。

语法如下：

```
MEMBER FUNCTION is_Date() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素可以转换为 DATE；

FALSE：表示 JSON 元素不可以转换为 DATE。

  20. is_Timestamp



检查 JSON 元素是否可以转换为 TIMESTAMP 数据类型。

语法如下：

```
MEMBER FUNCTION IS_TIMESTAMP() RETURN BOOLEAN
```

返回值

TRUE：表示 JSON 元素可以转换为 TIMESTAMP；

FALSE：表示 JSON 元素不可以转换为 TIMESTAMP。

  21. get_Size



获取 JSON 元素的长度。

语法如下：

```
MEMBER FUNCTION get_Size() RETURN NUMBER
```

返回值

针对 JSON OBJECT 类型，返回键值对个数；

针对 JSON ARRAY 类型，返回数组元素个数；

针对 JSON 标量，返回 1。

## 49.4 JSON_SCALAR_T 类方法

  1. 构造函数



构造 JSON_SCALAR_T 类。

语法如下：

```
CONSTRUCTOR FUNCTION JSON_SCALAR_T(jsn JDOM_T) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_SCALAR_T(jsn JSON_ELEMENT_T) RETURN SELF AS RESULT
```

参数详解

  * JSN：表示 JDOM_T 类或 JSON_ELEMENT_T 类。



返回值

JSON_SCALAR_T 类。

使用说明：

若 JSN 对应 JSON 元素不是 JSON 标量，则无法构造 JSON_SCALAR_T，函数报错。

  2. clone



克隆 JSON_SCALAR_T 类。

语法如下：

```
MEMBER FUNCTION clone() RETURN JSON_SCALAR_T
```

返回值

JSON_SCALAR_T 类。

## 49.5 JSON_ARRAY_T 类方法

  1. 构造函数



构造 JSON_ARRAY_T 类。

语法如下：

```
CONSTRUCTOR FUNCTION JSON_ARRAY_T() RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_ARRAY_T(jsn JDOM_T) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_ARRAY_T(JSN JSON_ELEMENT_T) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_ARRAY_T(jsn VARCHAR2) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_ARRAY_T(jsn CLOB) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_ARRAY_T(jsn BLOB) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_ARRAY_T(jsn BLOB, format IN VARCHAR2) RETURN SELF AS RESULT
```

参数详解

  * JSN：表示 JSON 文本。
  * FORMAT：暂不支持。



返回值

JSON_ARRAY_T 类。

使用说明：

  1. 若没有 JSN 参数，则构造一个空的 JSON ARRAY，不包含任何值，等价于“[]”；
  2. 若 JSN 为非法的 JSON 文本，则报错；
  3. 若 JSN 对应 JSON 类型不是 JSON ARRAY，则报错。



  2. parse



分析文本，构造 JSON_ARRAY_T。

语法如下：

```
STATIC FUNCTION parse(jsn IN varchar2, no_use_1 int default null

) RETURN JSON_ARRAY_T

或

STATIC FUNCTION parse(jsn IN CLOB, no_use_1 int default null) RETURN JSON_ARRAY_T

或

STATIC FUNCTION parse(jsn IN BLOB, no_use_1 int default null) RETURN JSON_ARRAY_T

或

STATIC FUNCTION parse(jsn IN BLOB, format IN VARCHAR2, no_use_1 int default null

) RETURN JSON_ARRAY_T
```

参数详解

  * JSN：表示 JSON 文本。
  * NO_USE_1：暂不支持。
  * FORMAT：暂不支持。



返回值

JSON_ARRAY_T 类。

使用说明：

  1. 若 JSN 为 NULL，则返回 NULL；
  2. 若 JSN 为非法的 JSON 文本，则报错；
  3. 若 JSN 对应 JSON 类型不是 JSON ARRAY，则报错。



  3. clone



克隆 JSON_ARRAY_T 类。

语法如下：

```
MEMBER FUNCTION clone() RETURN JSON_ARRAY_T
```

返回值

JSON_ARRAY_T 类。

  4. "GET"



将 JSON ARRAY 指定下标的 JSON 元素以 JSON_ELEMENT_T 方式返回。

语法如下：

```
MEMBER FUNCTION "GET"(pos NUMBER) RETURN JSON_ELEMENT_T
```

参数详解

  * POS：表示数组下标。



返回值

表示 JSON 元素的 JSON_ELEMENT_T 类。

使用说明：

DM 的 GET 为关键字，使用 GET 函数请加上双引号。

  5. GET_STRING



将 JSON ARRAY 指定下标的 JSON 元素以 VARCHAR2 文本返回。

语法如下：

```
MEMBER FUNCTION GET_STRING(POS NUMBER) RETURN VARCHAR2
```

参数详解

  * POS：表示数组下标。



返回值

表示 JSON 元素的 VARCHAR2 文本。

  6. GET_BOOLEAN



将 JSON ARRAY 指定下标的 JSON 元素以 BOOLEAN 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_BOOLEAN(POS NUMBER) RETURN BOOLEAN
```

参数详解

  * POS：表示数组下标。



返回值

表示 JSON 元素的 BOOLEAN 值。

  7. GET_NUMBER



将 JSON ARRAY 指定下标的 JSON 元素以 NUMBER 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_NUMBER(POS NUMBER) RETURN NUMBER
```

参数详解

  * POS：表示数组下标。



返回值

表示 JSON 元素的 NUMBER 值。

  8. GET_DATE



将 JSON ARRAY 指定下标的 JSON 元素以 DATE 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_DATE(POS NUMBER) RETURN DATE
```

参数详解

  * POS：表示数组下标。



返回值

表示 JSON 元素的 DATE 值。

  9. GET_TIMESTAMP



将 JSON ARRAY 指定下标的 JSON 元素以 TIMESTAMP 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_TIMESTAMP(POS NUMBER) RETURN TIMESTAMP
```

参数详解

  * POS：表示数组下标。



返回值

表示 JSON 元素的 TIMESTAMP 值。

  10. GET_CLOB



将 JSON ARRAY 指定下标的 JSON 元素以 CLOB 文本返回。

语法如下：

```
MEMBER FUNCTION GET_CLOB(POS NUMBER) RETURN CLOB

或

MEMBER PROCEDURE GET_CLOB(POS NUMBER, C IN OUT CLOB)
```

参数详解

  * POS：表示数组下标。
  * C： 输入输出参数。



返回值

表示 JSON 元素的 CLOB 文本。

  11. get_Blob



将 JSON ARRAY 指定下标的 JSON 元素以 BLOB 文本返回。

语法如下：

```
MEMBER FUNCTION GET_BLOB(POS NUMBER) RETURN BLOB

或

MEMBER PROCEDURE GET_BLOB(POS NUMBER, B IN OUT BLOB)
```

参数详解

  * POS：表示数组下标。
  * B： 输入输出参数。



返回值

表示 JSON 元素的 BLOB 文本。

  12. APPEND



在 JSON ARRAY 末尾添加一个数组元素。

语法如下：

```
MEMBER PROCEDURE APPEND(VAL VARCHAR2) 

或

MEMBER PROCEDURE APPEND(VAL NUMBER) 

或

MEMBER PROCEDURE APPEND(VAL BOOLEAN)

或

MEMBER PROCEDURE APPEND(VAL DATE)

或

MEMBER PROCEDURE APPEND(VAL TIMESTAMP)

或

MEMBER PROCEDURE APPEND(VAL CLOB)

或

MEMBER PROCEDURE APPEND(VAL BLOB)

或

MEMBER PROCEDURE APPEND(VAL JSON_ELEMENT_T)
```

参数详解

  * VAL：表示添加数组元素的值。



  13. append_Null



在 JSON ARRAY 末尾添加一个数组元素，数组元素的值为 null。

语法如下：

```
MEMBER PROCEDURE append_Null()
```

  14. put



在 JSON ARRAY 指定位置添加一个数组元素。

语法如下：

```
MEMBER PROCEDURE put(pos NUMBER, val VARCHAR2, overwrite BOOLEAN DEFAULT FALSE) 

或

MEMBER PROCEDURE put(pos NUMBER, val NUMBER, overwrite BOOLEAN DEFAULT FALSE)

或

MEMBER PROCEDURE put(pos NUMBER, val BOOLEAN, overwrite BOOLEAN DEFAULT FALSE)

或

MEMBER PROCEDURE put(pos NUMBER, val DATE, overwrite BOOLEAN DEFAULT FALSE)

或

MEMBER PROCEDURE put(pos NUMBER, val TIMESTAMP, overwrite BOOLEAN DEFAULT FALSE)

或

MEMBER PROCEDURE put(pos NUMBER, val CLOB, overwrite BOOLEAN DEFAULT FALSE)

或

MEMBER PROCEDURE put(pos NUMBER, val BLOB, overwrite BOOLEAN DEFAULT FALSE)

或

MEMBER PROCEDURE put(pos NUMBER, val JSON_ELEMENT_T, overwrite BOOLEAN DEFAULT FALSE)
```

参数详解

  * POS：表示添加元素的数组下标。
  * VAL：表示添加元素的值。
  * OVERWRITE：表示是否覆盖指定下标的旧值。



使用说明：

  1. 若 POS 位置不存在则报错；
  2. 若 OVERWRITE 为 TRUE，则 VAL 直接覆盖 POS 下标的元素；
  3. 若 OVERWRITE 为 FALSE，则 VAL 作为 POS 下标的元素，原始 POS 下标的元素以及 POS 之后的所有元素向后移动（即下标加 1）。



  15. put_Null



为 JSON ARRAY 添加一个数组元素，数组元素的值为 null。

语法如下：

```
MEMBER PROCEDURE put_Null(pos NUMBER, overwrite BOOLEAN DEFAULT FALSE)  
```

参数详解

  * POS：表示添加元素的数组下标。
  * OVERWRITE：表示是否覆盖指定下标的旧值。



使用说明：

  1. 若 POS 位置不存在则报错；
  2. 若 OVERWRITE 为 TRUE，则 VAL 直接覆盖 POS 下标的元素；
  3. 若 OVERWRITE 为 FALSE，则 VAL 作为 POS 下标的元素，原始 POS 下标的元素以及 POS 之后的所有元素向后移动（即下标加 1）。



  16. remove



删除 JSON ARRAY 指定下标的 JSON 元素。

语法如下：

```
MEMBER PROCEDURE remove(pos NUMBER) 
```

参数详解

  * POS：表示数组下标。



  17. get_Type



获取 JSON ARRAY 指定下标对应 JSON 元素的 JSON 类型。

语法如下：

```
MEMBER FUNCTION get_Type(pos NUMBER) RETURN VARCHAR2
```

参数详解

  * POS：表示数组下标。



返回值

表示 JSON 元素类型的字符串，包括：SCALAR、OBJECT 和 ARRAY，分别表示 JSON 标量、JSON OBJECT 和 JSON ARRAY。

## 49.6 JSON_OBJECT_T 类方法

  1. 构造函数



构造 JSON_OBJECT_T 类。

语法如下：

```
CONSTRUCTOR FUNCTION JSON_OBJECT_T() RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_OBJECT_T(jsn JDOM_T) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_OBJECT_T(jsn JSON_ELEMENT_T) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_OBJECT_T(jsn VARCHAR2) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_OBJECT_T(jsn CLOB) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_OBJECT_T(jsn BLOB) RETURN SELF AS RESULT

或

CONSTRUCTOR FUNCTION JSON_OBJECT_T(jsn BLOB, format IN VARCHAR2) RETURN SELF AS RESULT
```

参数详解

  * JSN：表示 JSON 文本。
  * FORMAT：暂不支持。



返回值

JSON_OBJECT_T 类。

使用说明：

  1. 若没有 JSN 参数，则构造一个空的 JSON OBJECT，不包含任何值，等价于“{}”；
  2. 若 JSN 为非法的 JSON 文本，则报错；
  3. 若 JSN 对应 JSON 类型不是 JSON OBJECT，则报错。



  2. parse



分析文本，构造 JSON_OBJECT_T。

语法如下：

```
STATIC FUNCTION parse(jsn IN varchar2, no_use_1 int default null

) RETURN JSON_OBJECT_T

或

STATIC FUNCTION parse(jsn IN CLOB, no_use_1 int default null) RETURN JSON_OBJECT_T

或

STATIC FUNCTION parse(jsn IN BLOB, no_use_1 int default null) RETURN JSON_OBJECT_T

或

STATIC FUNCTION parse(jsn IN BLOB, format IN VARCHAR2, no_use_1 int default null

) RETURN JSON_OBJECT_T
```

参数详解

  * JSN：表示 JSON 文本。
  * NO_USE_1：暂不支持。
  * FORMAT：暂不支持。



返回值

JSON_OBJECT_T 类。

使用说明：

  1. 若 JSN 为 NULL，则返回 NULL；
  2. 若 JSN 为非法的 JSON 文本，则报错；
  3. 若 JSN 对应 JSON 类型不是 JSON OBJECT，则报错。



  3. clone



克隆 JSON_OBJECT_T 类。

语法如下：

```
MEMBER FUNCTION clone() RETURN JSON_OBJECT_T
```

返回值

JSON_OBJECT_T 类。

  4. "GET"



将 JSON OBJECT 指定名称的 JSON 元素以 JSON_ELEMENT_T 方式返回。

语法如下：

```
MEMBER FUNCTION "GET"(KEY VARCHAR2) RETURN JSON_ELEMENT_T
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 JSON_ELEMENT_T 类。

使用说明：

DM 的 GET 为关键字，使用 GET 函数请加上双引号。

  5. get_Object



将 JSON OBJECT 指定名称的 JSON 元素以 JSON_OBJECT_T 类方式返回。

语法如下：

```
MEMBER FUNCTION GET_OBJECT(KEY VARCHAR2) RETURN JSON_OBJECT_T
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 JSON_OBJECT_T 类。

  6. get_Array



将 JSON OBJECT 指定名称的 JSON 元素以 JSON_ARRAY_T 类方式返回。

语法如下：

```
MEMBER FUNCTION GET_ARRAY(KEY VARCHAR2) RETURN JSON_ARRAY_T
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 JSON_ARRAY_T 类。

  7. get_String



将 JSON OBJECT 指定名称的 JSON 元素以 VARCHAR2 文本返回。

语法如下：

```
MEMBER FUNCTION GET_STRING(KEY VARCHAR2) RETURN VARCHAR2
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 VARCHAR2 文本。

  8. get_Boolean



将 JSON OBJECT 指定名称的 JSON 元素以 BOOLEAN 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_BOOLEAN(KEY VARCHAR2) RETURN BOOLEAN
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 BOOLEAN 值。

  9. get_Number



将 JSON OBJECT 指定名称的 JSON 元素以 NUMBER 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_NUMBER(KEY VARCHAR2) RETURN NUMBER
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 NUMBER 值。

  10. get_Date



将 JSON OBJECT 指定名称的 JSON 元素以 DATE 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_DATE(KEY VARCHAR2) RETURN DATE
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 DATE 值。

  11. get_Timestamp



将 JSON OBJECT 指定名称的 JSON 元素以 TIMESTAMP 数据类型返回。

语法如下：

```
MEMBER FUNCTION GET_TIMESTAMP(KEY VARCHAR2) RETURN TIMESTAMP
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素的 TIMESTAMP 值。

  12. get_Clob



将 JSON OBJECT 指定名称的 JSON 元素以 CLOB 文本返回。

语法如下：

```
MEMBER FUNCTION GET_CLOB(KEY VARCHAR2) RETURN CLOB

或

MEMBER PROCEDURE GET_CLOB(KEY VARCHAR2, C IN OUT CLOB)
```

参数详解

  * KEY：表示键值对的名称。
  * C： 输入输出参数。



返回值

表示 JSON 元素的 CLOB 文本。

  13. get_Blob



将 JSON OBJECT 指定名称的 JSON 元素以 BLOB 文本返回。

语法如下：

```
MEMBER FUNCTION GET_BLOB(KEY VARCHAR2) RETURN BLOB

或

MEMBER PROCEDURE GET_BLOB(KEY VARCHAR2, B IN OUT BLOB)
```

参数详解

  * KEY：表示键值对的名称。
  * B： 输入输出参数。



返回值

表示 JSON 元素的 BLOB 文本。

  14. put



为 JSON OBJECT 添加一个键值对。若指定的名称已存在，则覆盖指定名称对应的旧值。

语法如下：

```
MEMBER PROCEDURE put(key VARCHAR2, val VARCHAR2)

或

MEMBER PROCEDURE put(key VARCHAR2, val NUMBER)

或

MEMBER PROCEDURE put(key VARCHAR2, val BOOLEAN) 

或

MEMBER PROCEDURE put(key VARCHAR2, val DATE) 

或

MEMBER PROCEDURE put(key VARCHAR2, val TIMESTAMP) 

或

MEMBER PROCEDURE put(key VARCHAR2, val CLOB) 

或

MEMBER PROCEDURE put(key VARCHAR2, val BLOB) 

或

MEMBER PROCEDURE put(key VARCHAR2, val JSON_ELEMENT_T) 
```

参数详解

  * KEY：表示键值对的名称。
  * VAL：表示键值对的值。



  15. put_Null



为 JSON OBJECT 添加一个键值对，键值对的值为 NULL。

语法如下：

```
MEMBER PROCEDURE PUT_NULL(KEY VARCHAR2)
```

参数详解

  * KEY：表示键值对的名称。



  16. has



JSON OBJECT 是否存在指定名称的键值对。

语法如下：

```
MEMBER FUNCTION has(key VARCHAR2) RETURN BOOLEAN
```

参数详解

  * KEY：表示键值对的名称。



返回值

TRUE：表示 JSON OBJECT 存在名称为 KEY 键值对；

FALSE：表示 JSON OBJECT 不存在名称为 KEY 键值对。

  17. get_Keys



获取 JSON OBJECT 的所有键值对名称。

语法如下：

```
MEMBER FUNCTION GET_KEYS() RETURN JSON_KEY_LIST
```

返回值

表示 JSON OBJECT 的所有键值对名称的链表。

  18. get_Type



获取 JSON OBJECT 指定名称对应 JSON 元素的 JSON 类型。

语法如下：

```
MEMBER FUNCTION GET_TYPE(KEY VARCHAR2) RETURN VARCHAR2
```

参数详解

  * KEY：表示键值对的名称。



返回值

表示 JSON 元素类型的字符串，包括：SCALAR、OBJECT 和 ARRAY，分别表示 JSON 标量、JSON OBJECT 和 JSON ARRAY。

  19. remove



删除 JSON OBJECT 指定名称的键值对。

语法如下：

```
MEMBER PROCEDURE remove(key VARCHAR2) 
```

参数详解

  * KEY：表示键值对的名称。



  20. rename_key



重命名 JSON OBJECT 中的键值对名称。

语法如下：

```
MEMBER PROCEDURE RENAME_KEY(KEYOLD VARCHAR2, KEYNEW VARCHAR2) 
```

参数详解

  * KEYOLD：表示键值对的旧名称。
  * KEYNEW：表示键值对的新名称。



## 49.7 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程 SP_CREATE_SYSTEM_PACKAGES 创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES(1,'DBMS_JSON');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

可以查询 SYSOBJECTS 系统表判断当前 JSON 类是否处于有效状态，如下所示表示 JSON 类均处于有效状态：

```
SQL> SELECT NAME,TYPE$,SUBTYPE$,VALID FROM SYSOBJECTS WHERE NAME LIKE 'J%';
行号     NAME           TYPE$  SUBTYPE$ VALID
---------- -------------- ------ -------- -----
1          JDOM_T         SCHOBJ CLASS    Y
2          JDOM_T         DSYNOM NULL     Y
3          JSON_ARRAY_T   SCHOBJ CLASS    Y
4          JSON_ARRAY_T   DSYNOM NULL     Y
5          JSON_ELEMENT_T SCHOBJ CLASS    Y
6          JSON_ELEMENT_T DSYNOM NULL     Y
7          JSON_KEY_LIST  SCHOBJ TYPE     Y
8          JSON_KEY_LIST  DSYNOM NULL     Y
9          JSON_OBJECT_T  SCHOBJ CLASS    Y
10         JSON_OBJECT_T  DSYNOM NULL     Y
11         JSON_SCALAR_T  SCHOBJ CLASS    Y
12         JSON_SCALAR_T DSYNOM NULL     Y
```

下面将以 JDOM_T 为例进行举例说明，若用户需要使用系统包中的其余类，只需将 JDOM_T 替换为其他类型，并根据对应类的方法说明进行调用即可。

例 1 举例说明 PARSE 和 GET_NUMBER 方法。

语句如下：

```
DECLARE
    P_CLOB CLOB := '{"AGE":123}';
    //构造一个JDOM_T类型元素DOM
    DOM JDOM_T := JDOM_T.PARSE(P_CLOB);
    //将DOM中指定名称对应的值以NUMBER数据类型返回
    V_AGE_ID NUMBER := DOM.GET_NUMBER('AGE');
BEGIN
    DBMS_OUTPUT.PUT_LINE(V_AGE_ID);
END;
/
```

执行结果如下：

```
123
```

例 2 举例说明 PUT(KEY,VAL)和 TO_STRING 方法。

语句如下：

```
DECLARE
    P_CLOB CLOB := '{"AGE":123}';
    //构造一个JDOM_T类型元素DOM
    DOM JDOM_T := JDOM_T.PARSE(P_CLOB);
    RET BOOLEAN;
BEGIN
    //在DOM中添加一个键值对
    RET := DOM.PUT('NAME', '张三');
    //将DOM以VARCHAR2文本返回并进行打印
    DBMS_OUTPUT.PUT_LINE(DOM.TO_STRING);
END;
/
```

执行结果如下：

```
{"AGE":123,"NAME":"张三"}
```

例 3 举例说明 PUT(POS,VAL,OVERWRITE)、IS_OBJECT 和 IS_ARRAY 方法。

语句如下：

```
DECLARE
    P_CLOB CLOB := '[1,2]';
    //构造一个JDOM_T类型元素DOM
    DOM JDOM_T := JDOM_T.PARSE(P_CLOB);
    RET BOOLEAN;
BEGIN
    //依次判断DOM的JSON元素类型
    IF (DOM.IS_OBJECT) THEN
        DBMS_OUTPUT.PUT_LINE('OBJECT');
    ELSIF (DOM.IS_ARRAY) THEN
        DBMS_OUTPUT.PUT_LINE('ARRAY');
    ELSE
        DBMS_OUTPUT.PUT_LINE('SCALAR');
    END IF;
    //在DOM中添加一个数组元素
    RET := DOM.PUT(1, '张三', FALSE);
    DBMS_OUTPUT.PUT_LINE(DOM.TO_STRING);
END;
/
```

执行结果如下：

```
ARRAY
[1,"张三",2]
```

例 4 举例说明 CLONE、REMOVE 和 GET_KEYS 方法。

语句如下：

```
DECLARE
    P_CLOB CLOB := '{"A":1,"B":2,"C":3}';
    //构造一个JDOM_T类型元素DOM
    DOM JDOM_T := JDOM_T.PARSE(P_CLOB);
    DOM2 JDOM_T;
    RET BOOLEAN;
    V JSON_KEY_LIST;
    N NUMBER;
BEGIN
    //将DOM克隆至DOM2，DOM2与DOM完全相同
    DOM2 := DOM.CLONE();
    //删除DOM2中名称为“B”的键值对
    RET := DOM2.REMOVE('B');
    DBMS_OUTPUT.PUT_LINE(DOM.STRINGIFY);
    DBMS_OUTPUT.PUT_LINE(DOM2.STRINGIFY);
    //获取DOM中所有键值对名称，并进行打印
    V := DOM.GET_KEYS();
    N := V.COUNT();
    DBMS_OUTPUT.PUT_LINE('KEYS_NUM:'||N);
    FOR I IN 1..N LOOP
        DBMS_OUTPUT.PUT_LINE(V(I));
    END LOOP;
END;
/
```

执行结果如下：

```
{"A":1,"B":2,"C":3}
{"A":1,"C":3}
KEYS_NUM:3
A
B
C
```

例 5 举例说明 CREATE_ARR、APPEND、APPEND_NULL 和"GET"方法。

```
DECLARE
    //构造一个空的JSON ARRAY，等价于“[]”
    DOM JDOM_T := JDOM_T.CREATE_ARR();
    DOM2 JDOM_T;
    RET BOOLEAN;
BEGIN
    //在DOM的末尾添加一个数组元素1
    RET := DOM.APPEND(1);
    --在DOM的末尾添加一个数组元素null
    RET := DOM.APPEND_NULL();
    DBMS_OUTPUT.PUT_LINE(DOM.STRINGIFY);
    --获取DOM中下标为0的数组元素，结果以JDOM_T方式返回
    DOM2 := DOM."GET"(0);
    DBMS_OUTPUT.PUT_LINE(DOM2.TO_STRING());
    --在DOM中下标为0的位置添加一个数组元素null，指定覆盖
    RET := DOM.PUT_NULL(0, TRUE);
    --在DOM中下标为1的位置添加一个数组元素20，指定覆盖
    RET := DOM.PUT(1, 20, TRUE);
    DBMS_OUTPUT.PUT_LINE(DOM.STRINGIFY);
END;
/
```

执行结果如下：

```
[1,null]
1
[null,20]
```

例 6 举例说明 GET_SIZE 和 GET_TYPE 方法。

```
DECLARE
    P_CLOB CLOB := '[1,"ABC",[2],{"A":1}]';
    //构造一个JDOM_T类型元素DOM
    DOM JDOM_T := JDOM_T.PARSE(P_CLOB);
    N NUMBER;
BEGIN
    //获取DOM中数组元素的个数
    N := DOM.GET_SIZE;
    DBMS_OUTPUT.PUT_LINE('GET_SIZE:'||N);
    //依次打印DOM中每个数组元素的JSON类型
    FOR I IN 1..N LOOP
        DBMS_OUTPUT.PUT_LINE(DOM.GET_TYPE(I-1));
    END LOOP;
END;
/
```

执行结果如下：

```
GET_SIZE:4
SCALAR
SCALAR
ARRAY
OBJECT
```

例 7 举例说明 HAS_KEY 和 RENAME_KEY 方法。

```
DECLARE
    P_CLOB CLOB := '{"张三":13, "李四":14}';
    //构造一个JDOM_T类型元素DOM
    DOM JDOM_T := JDOM_T.PARSE(P_CLOB);
    N NUMBER;
    RET BOOLEAN;
BEGIN
    //判断DOM中是否含有名称为“张三”的键值对
    IF (DOM.HAS_KEY('张三')) THEN
        //将DOM中的键值对名称“张三”改为“张三峰”
        RET := DOM.RENAME_KEY('张三', '张三峰');
    END IF;
    DBMS_OUTPUT.PUT_LINE(DOM.STRINGIFY);
END;
/
```

执行结果如下：

```
{"李四":14,"张三峰":13}
```

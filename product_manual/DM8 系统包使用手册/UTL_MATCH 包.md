

# UTL_MATCH 包

UTL_MATCH 包用于计算两个字符串差异字符个数和相似度。

## 28.1 相关方法

utl_match 包所支持的 2 个函数，分别计算源字符串和目标字符串的差异字符个数和相似度。

  1. EDIT_DISTANCE



输出字符串 S1 转化为 S2 所需要被插入、删除或替换字符的个数。

语法如下：

```
FUNCTION EDIT_DISTANCE(
	S1		IN		VARCHAR,
	S2		IN 		VARCHAR
)RETURN INTEGER;
```

参数详解

  * S1 输入参数，VARCHAR 数据。
  * S2 输入参数，VARCHAR 数据。



  2. EDIT_DISTANCE_SIMILARITY



输出两个字符串的相似度。通过字符串 s1 转化为 s2 所需要被插入、删除或替换字符的个数，计算出字符串 S1 和 S2 的相似度。返回 1～100 之间的整数，0 代表完全不匹配，100 代表完全匹配。

语法如下：

```
FUNCTION EDIT_DISTANCE_SIMILARITY (
	S1 		IN 		VARCHAR, 
	S2 		IN 		VARCHAR
)RETURN INTEGER;
```

参数详解

  * S1 输入参数，VARCHAR 数据。
  * S2 输入参数，VARCHAR 数据。



  3. JARO_WINKLER



采用 JARO_WINKLER 算法评估两个字符串的相似度。

语法如下：

```
FUNCTION JARO_WINKLER(
   S1 		 		VARCHAR, 
   S2 		 		VARCHAR
)RETURN DOUBLE;
```

参数详解

  * S1 输入参数，VARCHAR 数据。
  * S2 输入参数，VARCHAR 数据。



  4. JARO_WINKLER_SIMILARITY



采用 JARO_WINKLER 算法计算两个字符串的相似度指标。相似度指标范围为：0～100 之间的整数，0 代表完全不匹配，100 代表完全匹配。

语法如下：

```
FUNCTION JARO_WINKLER_SIMILARITY(
   S1 		 		VARCHAR, 
   S2 		 		VARCHAR
)RETURN INT;
```

参数详解

  * S1 输入参数，VARCHAR 数据。
  * S2 输入参数，VARCHAR 数据。



## 28.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'UTL_MATCH');
```

例 utl_MATCH 包的应用。

例 1 EDIT_DISTANCE

```
SELECT UTL_MATCH.EDIT_DISTANCE('china','chin') FROM DUAL;
```

查询结果如下：

```
1
```

例 2 EDIT_DISTANCE_SIMILARITY

```
SELECT UTL_MATCH.EDIT_DISTANCE_SIMILARITY('china','chin') FROM DUAL;
```

查询结果如下：

```
80
```

例 3 例 1 JARO_WINKLER

```
SELECT UTL_MATCH.JARO_WINKLER('china','chin') FROM DUAL;
```

查询结果如下：

```
9.600000000000000E-001
```

例 4 JARO_WINKLER_SIMILARITY

```
SELECT UTL_MATCH.JARO_WINKLER_SIMILARITY('china','chin') FROM DUAL;
```

查询结果如下：

```
96
```

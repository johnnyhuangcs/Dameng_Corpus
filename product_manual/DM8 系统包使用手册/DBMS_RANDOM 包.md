

# DBMS_RANDOM 包

DBMS_RANDOM 系统包用于随机产生 INT 类型或 NUMBER 类型数、随机字符串以及符合正态分布的随机数。

使用 DBMS_RANDOM 包生成随机数或随机字符串步骤如下：

  1. 调用 INITIALIZE 方法初始化随机种子。若不初始化，则系统将自动生成随机种子；
  2. 调用相应方法生成随机数或随机字符串。



## 14.1 相关方法

  1. INITIALIZE



初始化随机种子。

语法如下：

```
PROCEDURE INITIALIZE(
	VAL		IN		INT
);
```

参数详解

  * val



任意整数，取值范围为：-2147483648 ~ +2147483647。该值将作为随机种子。

  2. SEED



重置随机种子。

语法如下：

```
PROCEDURE SEED(
	VAL		IN		INT
);
或
PROCEDURE SEED(
	VAL		IN		VARCHAR2
);
```

参数详解

  * val



任意整数或字符串。整数取值范围为：-2147483648 ~ +2147483647。若输入字符串，则 SEED 方法会将字符串转换为整数。输入的整数或转换后的整数将作为随机种子。

  3. TERMINATE



仅语法支持，无实际作用。

语法如下：

```
PROCEDURE TERMINATE;
```

  4. RANDOM_NORMAL



生成符合正态分布的随机数。

语法如下：

```
FUNCTION RANDOM_NORMAL RETURN  NUMBER;
```

  5. RANDOM



生成随机整数。

语法如下：

```
FUNCTION RANDOM RETURN  INTEGER;  
```

  6. RANDOM_STRING/STRING



生成随机字符串。

语法如下：

```
FUNCTION RANDOM_STRING (
	OPT		IN		CHAR,
	LEN		IN		NUMBER
)RETURN VARCHAR2;
或
FUNCTION STRING (
	OPT		IN		CHAR,
	LEN		IN		NUMBER
)RETURN VARCHAR2;
```

参数详解

  * OPT

CHAR 类型，表示字符串模式，模式解释如下：

    
        'u', 'U'：只产生随机的大写字母字符串；
    
    
        'l', 'L'：只产生随机的小写字母字符串；
    
    
        'a', 'A'：产生大小写混合的字母字符串；
    
    
        'x', 'X'：返回大写字母和数字随机字符串；
    
    
        'p', 'P'：返回随机可打印字符字符串。
    

当模式字符不是上述给定的字符时，系统会为按'U'的方式处理。

  * LEN

表示生成字符串的长度，最大为 32767，单位为字节。若超过 32767，则置为 32767。



  7. value



生成一个指定范围内的随机数。

语法如下：

```
FUNCTION VALUE (
	LOW		IN		NUMBER		DEFAULT 0, 
	HIGH	IN		NUMBER		DEFAULT 1
)RETURN NUMBER;
```

参数详解

  * LOW、HIGH

指定随机数的范围区间。当 LOW 小于等于 HIGH 时，生成大于等于 LOW 小于等于 HIGH 的随机数；当 LOW 大于 HIGH 时，生成大于等于
HIGH 小于等于 LOW 的随机数。



## 14.2 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_RANDOM');
```

例 1 初始化随机种子。

```
CALL DBMS_RANDOM.INITIALIZE(15);
```

例 2 返回大于等于 10 小于 100 的 NUMBER 类型随机数。

```
select DBMS_RANDOM.VALUE(10,100);
```

结果如下：

```
10.112613
```

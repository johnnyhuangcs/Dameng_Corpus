

# DBMS_OBFUSCATION_TOOLKIT 包

为了保护敏感数据，DM 提供一个数据加密包 dbms_obfuscation_toolkit。利用这个包，用户可以对数据进行 DES、DES3 加密，或者对数据进行 MD5 散列。

## 11.1 相关方法

dbms_obfuscation_toolkit 包中包含的过程和函数如下详细介绍：

  1. DES3DECRYPT



对 varbinary 数据进行 DES3 解密。

语法如下：

```
PROCEDURE DES3DECRYPT(
	INPUT				IN		VARBINARY,
	KEY					IN		VARBINARY,
	DECRYPTED_DATA		OUT		VARBINARY,
	WHICH				IN		INT			DEFAULT	0,
	IV					IN		VARBINARY	DEFAULT	NULL
);
```

返回值

解密后的 varbinary 数据。

对 VARCHAR2 数据进行 DES3 解密。

语法如下：

```
PROCEDURE DES3DECRYPT(
	INPUT_STRING		IN		VARCHAR2,
	KEY_STRING			IN		VARCHAR2,
	DECRYPTED_STRING	OUT		VARCHAR2,
	WHICH				IN		INT			DEFAULT	0,
	IV_STRING			IN		VARCHAR2	DEFAULT	NULL
);
```

返回值

解密后的 VARCHAR2 类型数据。

对 varbinary 数据进行 DES3 解密。

语法如下：

```
FUNCTION DES3DECRYPT(
	INPUT		IN		VARBINARY,
	KEY			IN		VARBINARY,
	WHICH		IN		INT			DEFAULT 0,
	IV			IN		VARBINARY	DEFAULT NULL
)RETURN VARBINARY;
```

返回值

解密后的 varbinary 数据。

对 VARCHAR2 数据进行 DES3 解密。

语法如下：

```
FUNCTION DES3DECRYPT(
	INPUT_STRING	IN		VARCHAR2,
	KEY_STRING		IN		VARCHAR2,
	WHICH			IN		INT			DEFAULT 0,
	IV_STRING		IN		VARCHAR2	DEFAULT NULL
)RETURN VARCHAR2;
```

返回值

解密后的 VARCHAR2 数据。

参数详解

  * INPUT_STRING

输入参数，需要解密的数据。

  * KEY_STRING

输入参数，解密数据使用的密钥。

  * WHICH

输入参数，解密模式，可选值 0、1。0 表示双密钥 DES3 解密，1 表示三密钥 DES3 解密。

  * IV_STRING

输入参数，解密数据使用的初始化向量。



  2. DES3ENCRYPT



对 varbinary 数据进行 DES3 加密。

语法如下：

```
PROCEDURE DES3ENCRYPT(
	INPUT			IN		VARBINARY,
	KEY				IN		VARBINARY,
	ENCRYPTED_DATA	OUT		VARBINARY,
	WHICH			IN		INT			DEFAULT 0,
	IV				IN		VARBINARY	DEFAULT NULL
);
```

参数详解

  * INPUT

输入参数，需要加密的数据。

  * KEY

输入参数，加密数据使用的密钥。

  * ENCRYPTED_DATA

输出参数，被加密后的数据。

  * WHICH

输入参数，加密模式，可选值 0、1。0 表示双密钥 DES3 加密，1 表示三密钥 DES3 加密。

  * IV

输入参数，加密数据使用的初始化向量。



对 VARCHAR2 数据进行 DES3 加密。

语法如下：

```
PROCEDURE DES3ENCRYPT(
	INPUT_STRING		IN		VARCHAR2,
	KEY_STRING			IN		VARCHAR2,
	ENCRYPTED_STRING	OUT		VARCHAR2,
	WHICH				IN		INT			DEFAULT	0,
	IV_STRING			IN		VARCHAR2	DEFAULT	NULL
);
```

参数详解

  * input_string

输入参数，需要加密的数据。

  * key_string

输入参数，加密数据使用的密钥。

  * encrypted_string

输出参数，被加密后的数据。

  * WHICH

输入参数，加密模式，可选值 0、1。0 表示双密钥 DES3 加密，1 表示三密钥 DES3 加密。

  * iv_string

输入参数，加密数据使用的初始化向量。



对 varbinary 数据进行 DES3 加密。

语法如下：

```
FUNCTION DES3ENCRYPT(
	INPUT		IN		VARBINARY,
	KEY			IN		VARBINARY,
	WHICH		IN		INT			DEFAULT	0,
	IV			IN		VARBINARY	DEFAULT	NULL
)RETURN VARBINARY;
```

参数详解

  * INPUT

输入参数，需要加密的数据。

  * KEY

输入参数，加密数据使用的密钥。

  * WHICH

输入参数，加密模式，可选值 0、1。0 表示双密钥 DES3 加密，1 表示三密钥 DES3 加密。

  * IV

输入参数，加密数据使用的初始化向量。



返回值

返回值加密后的 VARBINARY 数据。

对 VARCHAR2 数据进行 DES3 加密。

语法如下：

```
FUNCTION DES3ENCRYPT(
	INPUT_STRING	IN		VARCHAR2,
	KEY_STRING		IN		VARCHAR2,
	WHICH			IN		INT			DEFAULT	0,
	IV_STRING		IN		VARCHAR2	DEFAULT	NULL
)RETURN VARCHAR2;
```

参数详解

  * input_string

输入参数，需要加密的数据。

  * key_string

输入参数，加密数据使用的密钥。

  * WHICH

输入参数，加密模式，可选值 0、1。0 表示双密钥 DES3 加密，1 表示三密钥 DES3 加密。

  * iv_string

输入参数，加密数据使用的初始化向量。



返回值

返回值加密后的 VARCHAR2 数据。

  3. DES3GETKEY



生成 VARBINARY 类型数据的 DES3 加密密钥。

语法如下：

```
PROCEDURE DES3GETKEY(
	WHICH		IN		INT			DEFAULT	0,
	SEED		IN		VARBINARY,
	KEY			OUT		VARBINARY
);
```

参数详解

  * WHICH

输入参数，DES3 工作模式，可选值 0、1。0 表示双密钥 DES3，1 表示三密钥 DES3。

  * SEED

输入参数，获取 DES3 密钥使用的种子。

  * KEY

输出参数，VARBINARY 类型的 DES3 加密密钥。



生成 VARchar2 类型的 DES3 加密密钥。

语法如下：

```
PROCEDURE DES3GETKEY(
	WHICH			IN		INT			DEFAULT	0,
	SEED_STRING		IN		VARCHAR2,
	KEY				OUT		VARCHAR2
);
```

参数详解

  * WHICH

输入参数，DES3 工作模式，可选值 0、1。0 表示双密钥 DES3，1 表示三密钥 DES3。

  * SEED_STRING

输入参数，获取 DES3 密钥使用的种子。

  * KEY

输出参数，VARchar2 类型的 DES3 加密密钥。



生成 VARBINARY 类型的 DES3 加密密钥。

语法如下：

```
FUNCTION DES3GETKEY(
	WHICH		IN		INT		DEFAULT  0,
	SEED		IN		VARBINARY
)RETURN VARBINARY;
```

参数详解

  * WHICH

输入参数，DES3 工作模式，可选值 0、1。0 表示双密钥 DES3，1 表示三密钥 DES3。

  * SEED

输入参数，获取 DES3 密钥使用的种子。



返回值

返回值 VARBINARY 类型的 DES3 加密密钥。

生成 VARchar2 类型的 DES3 加密密钥。

语法如下：

```
FUNCTION DES3GETKEY(
	WHICH			IN		INT		DEFAULT	0,
	SEED_STRING		IN		VARCHAR2
)RETURN VARCHAR2;
```

参数详解

  * WHICH

输入参数，DES3 工作模式，可选值 0、1。0 表示双密钥 DES3，1 表示三密钥 DES3。

  * SEED_STRING

输入参数，获取 DES3 密钥使用的种子。



返回值

返回值 VARchar2 类型的 DES3 加密密钥。

  4. DESDECRYPT



对 varbinary 数据进行 DES 解密。

语法如下：

```
PROCEDURE DESDECRYPT(
	INPUT				IN		VARBINARY,
	KEY					IN		VARBINARY,
	DECRYPTED_DATA		OUT		VARBINARY
);
```

参数详解

  * INPUT

输入参数，需要解密的数据。

  * KEY

输入参数，解密数据使用的密钥。

  * decrypted_data

输出参数，被解密后的数据。



对 VARCHAR2 数据进行 DES 解密。

语法如下：

```
PROCEDURE DESDECRYPT(
	INPUT_STRING		IN		VARCHAR2,
	KEY_STRING			IN		VARCHAR2,
	DECRYPTED_STRING	OUT		VARCHAR2
);
```

参数详解

  * input_string

输入参数，需要解密的数据。

  * key_string

输入参数，解密数据使用的密钥。

  * decrypted_string

输出参数，被解密后的数据。



对 varbinary 数据进行 DES 解密。

语法如下：

```
FUNCTION DESDECRYPT(
	INPUT		IN		VARBINARY,
	KEY			IN		VARBINARY
)RETURN VARBINARY;
```

参数详解

  * INPUT

输入参数，需要解密的数据。

  * KEY

输入参数，解密数据使用的密钥。



返回值

返回值解密后的 varbinary 数据。

对 VARCHAR2 数据进行 DES 解密。

语法如下：

```
FUNCTION DESDECRYPT(
	INPUT_STRING	IN		VARCHAR2,
	KEY_STRING		IN		VARCHAR2
)RETURN VARCHAR2;
```

参数详解

  * INPUT_string

输入参数，需要解密的数据。

  * KEY_string

输入参数，解密数据使用的密钥。



返回值

返回值解密后的 VARCHAR2 数据。

  5. DESENCRYPT



对 varbinary 数据进行 DES 加密。

语法如下：

```
PROCEDURE DESENCRYPT(
	INPUT				IN		VARBINARY,
	KEY					IN		VARBINARY,
	ENCRYPTED_DATA		OUT		VARBINARY
);
```

参数详解

  * INPUT

输入参数，需要加密的数据。

  * KEY

输入参数，加密数据使用的密钥。

  * eNcrypted_data

输出参数，被加密后的数据。



对 VARCHAR2 数据进行 DES 加密。

语法如下：

```
PROCEDURE DESENCRYPT(
	INPUT_STRING		IN		VARCHAR2,
	KEY_STRING			IN		VARCHAR2,
	ENCRYPTED_STRING	OUT		VARCHAR2
);
```

参数详解

  * input_string

输入参数，需要加密的数据。

  * key_string

输入参数，加密数据使用的密钥。

  * encrypted_string

输出参数，被加密后的数据。



对 varbinary 数据进行 DES 加密。

语法如下：

```
FUNCTION DESENCRYPT(
	INPUT		IN		VARBINARY,
	KEY			IN		VARBINARY
)RETURN VARBINARY;
```

参数详解

  * INPUT

输入参数，需要加密的数据。

  * KEY

输入参数，加密数据使用的密钥。



返回值

加密后的 VARBINARY 数据。

对 VARCHAR2 数据进行 DES 加密。

语法如下：

```
FUNCTION DESENCRYPT(
	INPUT_STRING	IN		VARCHAR2,
	KEY_STRING		IN		VARCHAR2
)RETURN VARCHAR2;
```

参数详解

  * input_string

输入参数，需要加密的数据。

  * key_string

输入参数，加密数据使用的密钥。



返回值

返回值加密后的 VARCHAR2 数据。

  6. DESGETKEY



生成 VARBINARY 类型的 DES 加密密钥。

语法如下：

```
PROCEDURE DESGETKEY(
	SEED		IN		VARBINARY,
	KEY			OUT		VARBINARY
);
```

参数详解

  * seed

输入参数，获取 DES 密钥使用的种子。

  * KEY

输出参数，VARBINARY 类型的 DES 加密密钥。



生成 VARchar2 类型的 DES 加密密钥。

语法如下：

```
PROCEDURE DESGETKEY(
	SEED_STRING		IN		VARCHAR2,
	KEY				OUT		VARCHAR2
);
```

参数详解

  * seed_string

输入参数，获取 DES 密钥使用的种子。

  * KEY

输出参数，VARchar2 类型的 DES 加密密钥。



生成 VARBINARY 类型的 DES 加密密钥。

语法如下：

```
FUNCTION DESGETKEY(
	SEED		IN		VARBINARY
)RETURN VARBINARY;
```

参数详解

  * seed

输入参数，获取 DES 密钥使用的种子。



返回值

返回 VARBINARY 类型的 DES 加密密钥。

生成 VARchar2 类型的 DES 加密密钥。

语法如下：

```
FUNCTION DESGETKEY(

	SEED_STRING		IN		VARCHAR2

)RETURN VARCHAR2;
```

参数详解

  * seed_string

输入参数，获取 DES 密钥使用的种子。



返回值

VARchar2 类型的 DES 加密密钥。

  7. MD5



生成 VARBINARY 类型数据的 mD5 散列值。同一台机器配置，每次运行的结果一致。

语法如下：

```
PROCEDURE MD5(
	INPUT			IN		VARBINARY,
	CHECKSUM		OUT		VARBINARY
);
```

参数详解

  * input

输入参数，需要进行散列的数据。

  * checksum

输出参数，经 MD5 散列后的数据。



生成 VARCHAR2 类型数据的 MD5 散列值。同一台机器配置，每次运行的结果一致。

语法如下：

```
PROCEDURE MD5(
	INPUT_STRING		IN		VARCHAR2,
	CHECKSUM_STRING		OUT		VARCHAR2
);
```

参数详解

  * INPUT_STRING

输入参数，需要进行散列的数据。

  * CHECKSUM_STRING

输出参数，经 MD5 散列后的数据。



生成 VARBINARY 类型数据的 mD5 散列值。同一台机器配置，每次运行的结果一致。

语法如下：

```
FUNCTION MD5(
	INPUT		IN		VARBINARY
)RETURN VARBINARY;
```

参数详解

  * input

输入参数，需要进行散列的数据。



返回值

返回 MD5 散列后的 varbinary 数据。

生成 VARCHAR2 类型数据的 mD5 散列值。同一台机器配置，每次运行的结果一致。

语法如下：

```
FUNCTION MD5(
	INPUT_STRING	IN		VARCHAR2
)RETURN VARCHAR2;
```

参数详解

  * input_STRING

输入参数，需要进行散列的数据。



返回值

```
返回MD5散列后的varchar2数据。
```

## 11.2 使用说明

  1. 被加解密的数据不能为空。
  2. IV 加解密数据使用的初始化向量目前不使用，默认为 NULL，此时采用系统默认的初始化向量。
  3. des3getkey 和 desgetkey 生成加密密钥的种子 seed 目前不使用，因为 DM 用于生成密钥的环境已经配置好，只需要根据密钥的大小生成指定长度的密钥即可。
  4. DES 和 DES3 加解密的块大小为 8BYTES，因此用于加解密的数据必须是 8 的整数倍。
  5. DES 加解密使用的密钥长度为 8，多于 8 后的字符被忽略。
  6. 双密钥 DES3 加解密使用的密钥长度为 16，多于 16 后的字符被忽略。
  7. 三密钥 DES3 加解密使用的密钥长度为 24，多于 24 后的字符被忽略。
  8. 十六进制在 DM 对应的是 VARBINARy 数据类型，在 oracle 中对应的是 RAW 类型。
  9. MD5 主要用途是对数据进行散列，用于校验。不同的机器生成的散列值不同。



## 11.3 举例说明

使用包内的过程和函数之前，如果还未创建过系统包，请先调用系统过程创建系统包。

```
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OBFUSCATION_TOOLKIT');
SP_CREATE_SYSTEM_PACKAGES (1,'DBMS_OUTPUT');
SET SERVEROUTPUT ON;  //DBMS_OUTPUT.PUT_LINE需要设置这条语句，才能打印出消息
```

例 1 分别使用 DES3ENCRYPT 加密算法、des3decrypt 解密算法对 VARBINARY 类型数据进行加密解密。

```
DECLARE
raw_input VARBINARY(128) := '74696765727469676572746967657274';
//'tigertigertigert'
raw_key VARBINARY(128) := '73636F747473636F747473636F747473';
//'scottscottscotts'
encrypted_raw VARBINARY(2048);
decrypted_raw VARBINARY(2048);
BEGIN
dbms_output.put_line('> ========= BEGIN TEST RAW DATA =========');
dbms_output.put_line('> Raw input : ' ||
	raw_input);
  dbms_obfuscation_toolkit.DES3Encrypt(raw_input,
           raw_key, encrypted_raw );
  dbms_output.put_line('> encrypted hex value : ' ||
          encrypted_raw);
  dbms_obfuscation_toolkit.DES3Decrypt(encrypted_raw,
         raw_key, decrypted_raw);
  dbms_output.put_line('> Decrypted raw output : ' ||
         decrypted_raw);
  dbms_output.put_line('> ');
  if raw_input =
         decrypted_raw THEN
     dbms_output.put_line('> Raw DES Encyption and Decryption successful');
  END if;
END;
/
```

结果如下：

```
>========= BEGIN TEST RAW DATA =========
> Raw input : 74696765727469676572746967657274
> encrypted hex value : F77BB98AF8E7F59E329C31027CAF6C98
> Decrypted raw output : 74696765727469676572746967657274
>
> Raw DES Encyption and Decryption successful
```

例 2 分别使用 DESENCRYPT 加密算法、desdecrypt 解密算法对 VARCHAR2 类型的数据进行加密、解密。本示例建库时，未指定 unicode 参数（unicode=0）。

```
DECLARE
input_string VARCHAR2(8) := 'DM123456';
key_string VARCHAR2(8) := 'PASSWORD';
encrypted_string VARCHAR2(2048);
decrypted_string VARCHAR2(2048);
BEGIN
	dbms_obfuscation_toolkit.DESEncrypt(
		input_string, key_string, encrypted_string );
	dbms_output.put_line('> Encrypted string : ' ||
		encrypted_string);
	dbms_obfuscation_toolkit.DESDecrypt(
		encrypted_string,
		key_string,
		decrypted_string);
	dbms_output.put_line('> Decrypted output : ' ||
		decrypted_string);
	dbms_output.put_line('> ');
	if input_string =
		decrypted_string THEN
	dbms_output.put_line('> DES Encryption and Decryption successful');
	END IF;
END;
/
```

结果如下：

```
> Encrypted string : 盿0娒苳

> Decrypted output : DM123456

>

> DES Encryption and Decryption successful
```

例 3 使用 MD5 算法对 varbinary 类型数据散列，查看其散列值。

```
DECLARE
	retval varbinary(100);
	input_string varbinary(100) := '74696765727469676572746967657274';
	BEGIN
	dbms_output.put_line('> ========= BEGIN TEST VARBINARY DATA =========');
	dbms_output.put_line('> input string : '
		|| input_string);
	retval := DBMS_OBFUSCATION_TOOLKIT.MD5(input_string);
	dbms_output.put_line( '> MD5 string output : ' ||
		retval);
	END;
/
```

结果如下：

```
> ========= BEGIN TEST VARBINARY DATA =========
> input string : 74696765727469676572746967657274
> MD5 string output : 831F2AA79221A0F8F330E43A76B53323
```

例 4 使用 DES3GETKEY，生成 VARCHAR2 类型的 DES3 加密密钥，输出加密密钥。

```
DECLARE
	retval varchar2(100);
	input_string varchar2(100) :=
'012345678901234567890123456789012345678901234567890123456789012345678901234567890123456789';
	BEGIN
		dbms_output.put_line('> ========= BEGIN TEST STRING DATA =========');
	dbms_output.put_line('> input string : '
		|| input_string);
		DBMS_OBFUSCATION_TOOLKIT.DES3GetKey(SEED_STRING => INPUT_STRING, KEY =>
RETVAL);
		dbms_output.put_line('> decrypted string output : ' ||
		retval);
END;
/
```

结果如下：

```
> ========= BEGIN TEST STRING DATA =========
> input string : 0123456789012345678901234567890123456789012
> 34567890123456789012345678901234567890123456789
> decrypted string output : 4OXdw4FkLe8386jQ //每次生成密码均不一样
```

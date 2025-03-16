

# DM DEXP/DIMP JNI编程指南

## 8.1 接口介绍

Java 调用 DM 逻辑导出导入接口导数据是通过 JNI 接口来完成的。所引用的对象在 com.dameng.impexp.jar 中的 com.dameng.impexp.ImpExpDLL。主要类是 com.dameng.impexp. ImpExpDLL。

## 8.2 接口说明

1． 固定参数赋值接口 dll_exp_dm

描述：

用于逻辑导出。可对接口中固定的 dexp 参数（用户 ID、模式名和表名）进行赋值。

格式：

```
public static native int dll_exp_dm(

	int 		expMode, 

	byte[] 		userid, 

	byte[] 		schemaName, 

	byte[] 		tableName,

	byte[] 		expFilePath, 

	byte[] 		logFilePath

);
```

参数：

expMode（输入）：值为 1 时，以表方式导出；值为 2 时，以模式方式导出；值为 3 时，以整库方式导出。

userid（输入）：连接字符串，与导出工具 userid 格式一致。

schemaName（输入）：需要导出的模式，以模式方式导出时有效，格式为（schema1,schema2,...）。

tableName（输入）：需要导出的表，以表方式导出时有效，格式为（table1,table2,...）。

expFilePath（输入）：导出文件路径。

logFilePath（输入）：日志文件路径。

返回值：

执行成功返回 0，执行失败返回其他值。

2． 固定参数赋值接口 dll_imp_dm

描述：

用于逻辑导入。可对接口中固定的参数（用户 ID、模式名和表名）进行赋值。

格式：

```
public static native int dll_imp_dm(

	int 		impMode, 

	byte[] 		userid, 

	byte[] 		schemaName, 

	byte[] 		tableName,

	byte[] 		impFilePath, 

	byte[] 		logFilePath, 

	byte[] 		remapSchema

);
```

参数：

impMode（输入）：值为 1 时，以表方式导入；值为 2 时，以模式方式导入；值为 3 时，以整库方式导入。

userid（输入）：连接字符串，与导入工具 userid 格式一致。

schemaName（输入）：需要导入的模式，以模式方式导入时有效，格式为（schema1,schema2,...）。

tableName（输入）：需要导入的表，以表方式导入时有效，格式为（table1,table2,...）。

impFilePath（输入）：导入文件路径。

logFilePath（输入）：日志文件路径。

remapSchema（输入）：模式映射串，格式为(SOURCE_SCHEMA:TARGET_SCHEMA，…)。

返回值：

执行成功返回 0，执行失败返回其他值。

3． dll_exec_sql_file

描述：

用于执行 SQL 脚本文件。

格式：

```
public static native int dll_exec_sql_file(

	byte[] 		userid, 

	byte[] 		sqlFilePath, 

	byte[] 		logFilePath

);
```

参数：

userid（输入）：连接字符串，与导入工具 userid 格式一致。

sqlFilePath（输入）：SQL 脚本文件路径。

logFilePath（输入）：日志文件路径。

返回值：

执行成功返回 0，执行失败返回其他值。

4.指定参数接口 dll_exp_dm

描述：

用于逻辑导出。用户可以根据需要自行指定 dexp 工具参数。

格式：

```
public static native int dll_exp_dm(

    List<String[]> params

);
```

参数：

params（输入）：List 参数对象，成员为 String 数组键值对，第一个元素为参数名，第二个元素为参数值。支持除了 DUMMY 参数以外的所有 dexp 工具参数。

返回值：

执行成功返回 0，执行失败返回其他值。

5. 指定参数接口 dll_imp_dm

描述：

用于逻辑导入。用户可以根据需要自行指定 dimp 工具参数。

格式：

```
public static native int dll_imp_dm(

    List<String[]> params

);
```

参数：

params（输入）：List 参数对象，成员为 String 数组键值对，第一个元素为参数名，第二个元素为参数值。支持除了 DUMMY 参数以外的所有 dimp 工具参数。

返回值：

执行成功返回 0，执行失败返回其他值。

## 8.3 基本示例

例 1 使用固定参数赋值的 dll_exp_dm 接口编写逻辑导出类，包含逻辑导出数据表、模式以及数据库的方法。

```
import java.io.*;
import com.dameng.impExp.*;
public class TestExp {
	//导出普通表
	public static void TestExpTable()
	{
		int expMode = 1;
		String userid = "SYSDBA/sysDBA*00@localhost";
		//String schemaName = "";
		String tableName = "TEST";
		String expFilePath = "D:\\test_tab.dmp";
		String logFilePath = "D:\\test_tab.log";
		//System.out.println("测试信息：导出表（普通表）开始...");
		try {
			int ret=ImpExpDLL.dll_exp_dm(expMode, userid.getBytes(), null, tableName.getBytes(), expFilePath.getBytes(),logFilePath.getBytes());
			System.out.println("导出表成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导出表产生异常："+  e.getMessage());
		}
	}

	//导出表名为中文的表
	public static void TestExpTable_01()
	{
		int expMode = 1;
		String userid = "SYSDBA/sysDBA*00@localhost";
		//String schemaName = "";
		String tableName = "表名为中文";
		String expFilePath = "D:\\test_tab_1.dmp";
		String logFilePath = "D:\\test_tab_1.log";
		//System.out.println("测试信息：导出表(中文表名)开始...");
		try {
			int ret=ImpExpDLL.dll_exp_dm(expMode, userid.getBytes(), null, tableName.getBytes(), expFilePath.getBytes(),logFilePath.getBytes());
			System.out.println("导出表成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导出表产生异常："+  e.getMessage());
		}
	}

	//导出不存在的表
	public static void TestExpTable_02()
	{
		int expMode = 1;
		String userid = "SYSDBA/sysDBA*00@localhost";
		String tableName = "T_NOT_EXISTED";
		String expFilePath = "D:\\test_tab_2.dmp";
		String logFilePath = "D:\\test_tab_2.log";
		//System.out.println("测试信息：导出表(表不存在)开始...");
		try {
			int ret=ImpExpDLL.dll_exp_dm(expMode, userid.getBytes(), null, tableName.getBytes(), expFilePath.getBytes(),logFilePath.getBytes());
			System.out.println("测试信息：测试失败，未获取到预期的异常 ");
		} 
		catch (Exception e) {
			System.out.println("测试信息：测试成功，获取到预期的异常"+  e.getMessage());
		}
	}

	//导出模式
	public static void TestExpSchema() throws UnsupportedEncodingException
	{
		int expMode = 2;
		String userid = "SYSDBA/sysDBA*00@localhost";
		String schemaName = "SYSDBA";
		//String tableName = "";
		String expFilePath = "D:\\test_sch.dmp";
		String logFilePath = "D:\\test_sch.log";
		//String errMess = null;
		try {
			int ret=ImpExpDLL.dll_exp_dm(expMode, userid.getBytes(), schemaName.getBytes(),null, expFilePath.getBytes(),logFilePath.getBytes());
			System.out.println("导出模式成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导出模式产生异常："+  e.getMessage());
		}
	}

	//导出数据库
	public static void TestExpDB() throws UnsupportedEncodingException
	{
		int expMode = 3;
		String userid = "SYSDBA/sysDBA*00@localhost";
		//String schemaName = "";
		//String tableName = "";
		String expFilePath = "D:\\test_db.dmp";
		String logFilePath = "D:\\test_db.log";
		//String errMess = null;
		try {
			int ret=ImpExpDLL.dll_exp_dm(expMode, userid.getBytes(), null, null, expFilePath.getBytes(),logFilePath.getBytes());
			System.out.println("导出全库成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导出全库产生异常："+  e.getMessage());
		}
	}
}
```

例 2 使用固定参数赋值的 dll_imp_dm 接口编写逻辑导入类，包含逻辑导入数据表、模式以及数据库的方法。

```
import java.io.*;
import com.dameng.impExp.*;
public class TestImp {
	//导入普通表
	public static void TestImpTable()
	{
		int impMode = 1;
		String userid = "SYSDBA/sysDBA*00@127.0.0.1";
		//String schemaName = "";
		String tableName = "TEST";
		String impFilePath = "D:\\test_tab.dmp";
		String logFilePath = "D:\\test_tab2.log";
		//System.out.println("测试信息：导入表(普通表)开始...");
		try {
			int ret=ImpExpDLL.dll_imp_dm(impMode, userid.getBytes(), null, tableName.getBytes(), impFilePath.getBytes(),logFilePath.getBytes(),null);
			System.out.println("导入表成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导入表产生异常："+  e.getMessage());
		}
	}

	//导入表名为中文的表
	public static void TestImpTable_01()
	{
		int impMode = 1;
		String userid = "SYSDBA/sysDBA*00@localhost";
		String tableName = "表名为中文";
		String impFilePath = "D:\\tmp_del\\test_tab_imp_01.dmp";
		String logFilePath = "D:\\tmp_del\\test_tab_imp_01.log";
		System.out.println("测试信息：导入表(中文表名)开始...");
		try {
			int ret=ImpExpDLL.dll_imp_dm(impMode, userid.getBytes(), null, tableName.getBytes(), impFilePath.getBytes(),logFilePath.getBytes(),null);
			System.out.println("导入表成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导入表产生异常："+  e.getMessage());
		}
	}

	//导入模式
	public static void TestImpSchema()
	{
		int impMode = 2;
		String userid = "SYSDBA/sysDBA*00@127.0.0.1";
		String schemaName = "SYSDBA";
		//String tableName = "";
		String impFilePath = "D:\\test_sch.dmp";
		String logFilePath = "D:\\test_sch2.log";
		try {
			int ret=ImpExpDLL.dll_imp_dm(impMode, userid.getBytes(), schemaName.getBytes(),null, impFilePath.getBytes(),logFilePath.getBytes(),null);
			System.out.println("导入模式成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导入模式产生异常："+  e.getMessage());
		}
	}

	//导入数据库
	public static void TestImpDB()
	{
		int impMode = 3;
		String userid = "SYSDBA/sysDBA*00@localhost";
		//String schemaName = "";
		//String tableName = "";
		String impFilePath = "D:\\test_db.dmp";
		String logFilePath = "D:\\test_db2.log";
		try {
			int ret=ImpExpDLL.dll_imp_dm(impMode, userid.getBytes(), null, null, impFilePath.getBytes(),logFilePath.getBytes(),null);
			System.out.println("导入库成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("导入库产生异常："+  e.getMessage());
		}
	}
}
```

例 3 使用 dll_exec_sql_file 编写执行 SQL 脚本文件类。

```
import java.io.*;
import com.dameng.impExp.*;
public class TestSql {
	public static void TestSql01()
	{
		String userid = "SYSDBA/sysDBA*00@127.0.0.1";
		String sqlFilePath = "D:\\test.sql";
		String logFilePath = "D:\\test.log";
		try {
			int ret=ImpExpDLL.dll_exec_sql_file(userid.getBytes(), sqlFilePath.getBytes(),logFilePath.getBytes());
			System.out.println("执行SQL成功，ret = " + ret);
		} 
		catch (Exception e) {
			System.out.println("执行SQL产生异常："+  e.getMessage());
		}
	}
}
```

例 4 测试上述例 1-例 3 各类中的方法。

```
import java.io.UnsupportedEncodingException;
import java.sql.*;
public class TestImpExp {
	/**
	 * @param args
	 * @throws UnsupportedEncodingException 
	 */
	public static void main(String[] args) throws UnsupportedEncodingException {
		// TODO Auto-generated method stub
		Connection conn = null;
		try{
			//导出普通表
			TestExp.TestExpTable();
			//导入普通表
            	TestImp.TestImpTable();
			//执行SQL脚本文件
			TestSql.TestSql01();
         	}
		catch(Exception e){
			System.out.println("产生未预期的异常：" + e.getMessage());
		}
	}
}
```

例 5  使用指定参数赋值的 dll_exp_dm 和 dll_imp_dm 接口进行导入和导出。

```
package com.dameng.test;
import java.io.File;
import java.io.IOException;
import java.util.ArrayList;
import java.util.List;
import com.dameng.impExp.ImpExpDLL;
public class TestImpExpDLL
{
    public static void main(String[] args)
    {
        test_2();
    }
    public static void test_3()
    {
        List<String[]> list = new ArrayList<String[]>();
        list.add(new String[] {"USERID", "SYSDBA/sysDBA*00@192.168.100.226"});
        list.add(new String[] {"FILE", "dexp.dmp"});
        list.add(new String[] {"DIRECTORY", "e:\\tmp"});
        list.add(new String[] {"TABLES", "T1"});
        list.add(new String[] {"TABLE_EXISTS_ACTION", "REPLACE"});
        ImpExpDLL.dll_imp_dm(list);
    }
    public static void test_2()
    {
        List<String[]> list = new ArrayList<String[]>();
        list.add(new String[] {"USERID", "SYSDBA/sysDBA*00@192.168.100.226"});
        list.add(new String[] {"FILE", "dexp.dmp"});
        list.add(new String[] {"DIRECTORY", "e:\\tmp"});
        list.add(new String[] {"TABLES", "T1"});
        ImpExpDLL.dll_exp_dm(list);
    }
}
```

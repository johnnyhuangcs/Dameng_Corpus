

# DM JDBC 编程指南

## 4.1 JDBC 介绍

JDBC（Java Database Connectivity）是 Java 应用程序与数据库的接口规范，旨在让各数据库开发商为 Java 程序员提供标准的数据库应用程序编程接口（API）。JDBC 定义了一个跨数据库、跨平台的通用 SQL 数据库 API。

DM JDBC 驱动程序是 DM 数据库的 JDBC 驱动程序，它是一个能够支持基本 SQL 功能的通用应用程序编程接口，支持一般的 SQL 数据库访问。

通过 JDBC 驱动程序，用户可以在应用程序中实现对 DM 数据库的连接与访问，JDBC 驱动程序的主要功能包括：

  1. 建立与 DM 数据库的连接；
  2. 转接发送 SQL 语句到数据库；
  3. 处理并返回语句执行结果。



## 4.2 基本示例

利用 JDBC 驱动程序进行编程的一般步骤为：

  1. 获得 java.sql.Connection 对象。

利用 DriverManager 或者数据源来建立同数据库的连接。

  2. 创建 java.sql.Statement 对象。这里也包含了 java.sql.PreparedStatement 和 java.sql.CallableStatement 对象。

利用连接对象的创建语句对象的方法来创建。在创建的过程中，根据需要来设置结果集的属性。

  3. 数据操作。

数据操作主要分为两个方面，一个是更新操作，例如更新数据库、删除一行、创建一个新表等；另一个就是查询操作，执行完查询之后，会得到一个
java.sql.ResultSet 对象，可以操作该对象来获得指定列的信息、读取指定行的某一列的值。

  4. 释放资源。

在操作完成之后，用户需要释放系统资源，主要是关闭结果集、关闭语句对象，释放连接。当然，这些动作也可以由 JDBC 驱动程序自动执行，但由于 Java
语言的特点，这个过程会比较慢(需要等到 Java 进行垃圾回收时进行)，容易出现意想不到的问题。



下面用一个具体的编程实例来展示利用 JDBC 驱动程序进行数据库操作的基本步骤：

```
/*该例程实现插入数据，修改数据，删除数据，数据查询等基本操作。
import java.awt.Color;
import java.awt.Font;
import java.awt.Graphics2D;
import java.awt.font.FontRenderContext;
import java.awt.geom.Rectangle2D;
import java.awt.image.BufferedImage;
import java.io.BufferedInputStream;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.math.BigDecimal;
import java.sql.CallableStatement;
import java.sql.Connection;
import java.sql.Date;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.ResultSetMetaData;
import java.sql.SQLException;
import java.sql.Statement;
import javax.imageio.ImageIO;
public class BasicApp {
	// 定义DM JDBC驱动串
	String jdbcString = "dm.jdbc.driver.DmDriver";
	// 定义DM URL连接串
	String urlString = "jdbc:dm://localhost:5236";
	// 定义连接用户名
	String userName = "SYSDBA";
	// 定义连接用户口令
	String password = "sysDBA*00";
	// 定义连接对象
	Connection conn = null;
	/* 加载JDBC驱动程序
	 * @throws SQLException 异常 */
	public void loadJdbcDriver() throws SQLException {
		try {
			System.out.println("Loading JDBC Driver...");
			// 加载JDBC驱动程序
			Class.forName(jdbcString);
		} catch (ClassNotFoundException e) {
			throw new SQLException("Load JDBC Driver Error : " + e.getMessage());
		} catch (Exception ex) {
			throw new SQLException("Load JDBC Driver Error : "
					+ ex.getMessage());
		}
	}
	/* 连接DM数据库
	 * @throws SQLException 异常 */
	public void connect() throws SQLException {
		try {
			System.out.println("Connecting to DM Server...");
			// 连接DM数据库
			conn = DriverManager.getConnection(urlString, userName, password);
		} catch (SQLException e) {
			throw new SQLException("Connect to DM Server Error : "
					+ e.getMessage());
		}
	}
	/* 关闭连接 
	 * @throws SQLException 异常 */
	public void disConnect() throws SQLException {
		try {
			// 关闭连接
			conn.close();
		} catch (SQLException e) {
			throw new SQLException("close connection error : " + e.getMessage());
		}
	}
	/* 往产品信息表插入数据
	 * @throws SQLException 异常 */
	public void insertTable() throws SQLException {
		// 插入数据语句
		String sql = "INSERT INTO production.product(name,author,publisher,publishtime,"
				+ "product_subcategoryid,productno,satetystocklevel,originalprice,nowprice,discount,"
				+ "description,photo,type,papertotal,wordtotal,sellstarttime,sellendtime) "
				+ "VALUES(?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?,?);";
		// 创建语句对象
		PreparedStatement pstmt = conn.prepareStatement(sql);
		// 为参数赋值
		pstmt.setString(1, "三国演义");
		pstmt.setString(2, "罗贯中");
		pstmt.setString(3, "中华书局");
		pstmt.setDate(4, Date.valueOf("2005-04-01"));
		pstmt.setInt(5, 4);
		pstmt.setString(6, "9787101046121");
		pstmt.setInt(7, 10);
		pstmt.setBigDecimal(8, new BigDecimal(19.0000));
		pstmt.setBigDecimal(9, new BigDecimal(15.2000));
		pstmt.setBigDecimal(10, new BigDecimal(8.0));
		pstmt.setString(11, "《三国演义》是中国第一部长篇章回体小说，中国小说由短篇发展至长篇的原因与说书有关。");
		// 设置大字段参数 
		try {
			// 创建一个图片用于插入大字段
			String filePath = "c:\\三国演义.jpg";
			CreateImage(filePath);
			File file = new File(filePath); 
			InputStream in = new BufferedInputStream(new FileInputStream(file)); 
			pstmt.setBinaryStream(12, in, (int) file.length());
		} catch (FileNotFoundException e) {
			System.out.println(e.getMessage());
			// 如果没有图片设置为NULL
			pstmt.setNull(12, java.sql.Types.BINARY);
		} catch (IOException e) {
		System.out.println(e.getMessage());
		} 
		pstmt.setString(13, "25");
		pstmt.setInt(14, 943);
		pstmt.setInt(15, 93000);
		pstmt.setDate(16, Date.valueOf("2006-03-20"));
		pstmt.setDate(17, Date.valueOf("1900-01-01"));
		// 执行语句
		pstmt.executeUpdate();
		// 关闭语句
		pstmt.close();
	}
	/* 查询产品信息表
	 * @throws SQLException 异常 */
	public void queryProduct() throws SQLException {
		// 查询语句
		String sql = "SELECT productid,name,author,description,photo FROM production.product WHERE productid=11";
		// 创建语句对象
		Statement stmt = conn.createStatement();
		// 执行查询
		ResultSet rs = stmt.executeQuery(sql);
		// 显示结果集
		displayResultSet(rs);
		// 关闭结果集
		rs.close();
		// 关闭语句
		stmt.close();
	} 
	/* 修改产品信息表数据
	 * @throws SQLException 异常 */
	public void updateTable() throws SQLException {
		// 更新数据语句
		String sql = "UPDATE production.product SET name = ?"
				+ "WHERE productid = 11;";
		// 创建语句对象
		PreparedStatement pstmt = conn.prepareStatement(sql);
		// 为参数赋值
		pstmt.setString(1, "三国演义（上）");
		// 执行语句
		pstmt.executeUpdate();
		// 关闭语句
		pstmt.close();
	}
	/* 删除产品信息表数据
	 * @throws SQLException 异常 */
	public void deleteTable() throws SQLException {
		// 删除数据语句
		String sql = "DELETE FROM production.product WHERE productid = 11;";
		// 创建语句对象
		Statement stmt = conn.createStatement();
		// 执行语句
		stmt.executeUpdate(sql);
		// 关闭语句
		stmt.close();
	}
	/* 查询产品信息表
	 * @throws SQLException 异常 */
	public void queryTable() throws SQLException {
		// 查询语句
		String sql = "SELECT productid,name,author,publisher FROM production.product";
		// 创建语句对象
		Statement stmt = conn.createStatement();
		// 执行查询
		ResultSet rs = stmt.executeQuery(sql);
		// 显示结果集
		displayResultSet(rs);
		// 关闭结果集
		rs.close();
		// 关闭语句
		stmt.close();
	}
	/* 调用存储过程修改产品信息表数据
	 * @throws SQLException 异常 */
	public void updateProduct() throws SQLException {
		// 更新数据语句
		String sql = "{ CALL production.updateProduct(?,?) }";
		// 创建语句对象
		CallableStatement cstmt = conn.prepareCall(sql);
		// 为参数赋值
		cstmt.setInt(1, 1);
		cstmt.setString(2, "红楼梦（上）");
		// 执行语句
		cstmt.execute();
		// 关闭语句
		cstmt.close();
	}
	/* 显示结果集
	 * @param rs 结果集对象
	 * @throws SQLException 异常 */
	private void displayResultSet(ResultSet rs) throws SQLException {
		// 取得结果集元数据
		ResultSetMetaData rsmd = rs.getMetaData();
		// 取得结果集所包含的列数
		int numCols = rsmd.getColumnCount();
		// 显示列标头
		for (int i = 1; i <= numCols; i++) {
			if (i > 1) {
				System.out.print(",");
			}
			System.out.print(rsmd.getColumnLabel(i));
		}
		System.out.println("");
		// 显示结果集中所有数据
		while (rs.next()) {
			for (int i = 1; i <= numCols; i++) {
				if (i > 1) {
					System.out.print(",");
				}
				// 处理大字段
				if ("IMAGE".equals(rsmd.getColumnTypeName(i))) {
					byte[] data = rs.getBytes(i);
					if (data != null && data.length > 0) {
						FileOutputStream fos;
						try {
							fos = new FileOutputStream("c:\\三国演义1.jpg");
							fos.write(data);
							fos.close();
						} catch (FileNotFoundException e) {
							System.out.println(e.getMessage());
						} catch (IOException e) {
							System.out.println(e.getMessage());
						}
					}
		System.out.print("字段内容已写入文件c:\\三国演义1.jpg，长度" + data.length);
				} else {
					// 普通字段
					System.out.print(rs.getString(i));
				}
			}
			System.out.println("");
		}
	}
	 /* 创建一个图片用于插入大字段
	 * @throws IOException 异常 */
	private void CreateImage(String path) throws IOException {
		int width = 100;
		int height = 100;
		String s = "三国演义";
		File file = new File(path);
		Font font = new Font("Serif", Font.BOLD, 10);
		BufferedImage bi = new BufferedImage(width, height,
				BufferedImage.TYPE_INT_RGB);
		Graphics2D g2 = (Graphics2D) bi.getGraphics();
		g2.setBackground(Color.WHITE);
		g2.clearRect(0, 0, width, height);
		g2.setPaint(Color.RED);
		FontRenderContext context = g2.getFontRenderContext();
		Rectangle2D bounds = font.getStringBounds(s, context);
		double x = (width - bounds.getWidth()) / 2;
		double y = (height - bounds.getHeight()) / 2;
		double ascent = -bounds.getY();
		double baseY = y + ascent;
		g2.drawString(s, (int) x, (int) baseY);
		ImageIO.write(bi, "jpg", file);
	}

    //类主方法 @param args 参数
	public static void main(String args[]) {
		try {
			// 定义类对象
			BasicApp basicApp = new BasicApp();
			// 加载驱动程序
			basicApp.loadJdbcDriver();
			// 连接DM数据库
			basicApp.connect();
			// 插入数据
			System.out.println("--- 插入产品信息 ---");
			basicApp.insertTable();
			// 查询含有大字段的产品信息
			System.out.println("--- 显示插入结果 ---");
			basicApp.queryProduct();
			// 在修改前查询产品信息表
			System.out.println("--- 在修改前查询产品信息 ---");
			basicApp.queryTable();
			// 修改产品信息表
			System.out.println("--- 修改产品信息 ---");
			basicApp.updateTable();
			// 在修改后查询产品信息表
			System.out.println("--- 在修改后查询产品信息 ---");
			basicApp.queryTable();
			// 删除产品信息表
			System.out.println("--- 删除产品信息 ---");
			basicApp.deleteTable();
			// 在删除后查询产品信息表
			System.out.println("--- 在删除后查询产品信息 ---");
			basicApp.queryTable();
			// 调用存储过程修改产品信息表
			System.out.println("--- 调用存储过程修改产品信息 ---");
			basicApp.updateProduct();
			// 在存储过程更新后查询产品信息表
			System.out.println("--- 调用存储过程后查询产品信息 ---");
			basicApp.queryTable();
			// 关闭连接
			basicApp.disConnect();
		} catch (SQLException e) {
			System.out.println(e.getMessage());
		}
	}
}
```

运行程序，启动达梦数据库服务器并创建用于修改产品信息的存储过程。存储过程代码如下：

```
//创建修改产品信息的存储过程
create or replace procedure PRODUCTION.updateProduct
(
  v_id int,
  v_name varchar(50)
)
as
begin
  UPDATE production.product SET name = v_name WHERE productid = v_id;
end;
```

将以上 Java 程序保存为 C:\BasicApp.java 文件，打开命令行窗口进入 C 盘根目录，输入 javac BasicApp.java 编译该类文件，编译完成后在命令行窗口输入 java -classpath D:\dmdbms\jdbc\DmJdbcDriver.jar; BasicApp 运行该文件。其中，DmJdbcDriver.jar 有四个版本： DmJdbcDriver6.jar、DmJdbcDriver7.jar、DmJdbcDriver8.jar 和 DmJdbcDriver11.jar，分别对应 JDK 版本 1.6、1.7、1.8 和 11，用户根据需要自行选择。

注意这里还需要将达梦数据库驱动程序 DmJdbcDriver.jar（根据 JDK 版本选择上述四个驱动之一）文件加入到 classpath 中，达梦数据库驱动程序 DmJdbcDriver.jar 文件存在于达梦数据库安装路径下的 jdbc 目录下，这里假设达梦数据库安装在 D:\dmdbms 目录，那么达梦数据库驱动程序 DmJdbcDriver.jar 文件就在 D:\dmdbms\drivers\jdbc 目录下。

## 4.3 DM JDBC 特性

DM JDBC 驱动程序有 JDBC3.0 和 JDBC4.0 两种类型。

DM JDBC 3.0 驱动程序符合 SUN JDBC3.0 标准，实现了 JDBC3.0 规范所要求的下列接口：

```
java.sql.Driver
java.sql.Connection 
java.sql.Statement 
java.sql.PreparedStatement 
java.sql.CallableStatement 
java.sql.ResultSet
java.sql.ResultSetMetaData
java.sql.DatabaseMetaData
java.sql.Blob 
java.sql.Clob
java.sql.PrameterMetaData 
java.sql.Savepoint
javax.sql.DataSource
javax.sql.ConnectionEvent
javax.sql.ConnectionEventListener
javax.sql.ConnectionPoolDataSource
javax.sql.PooledConnection
```

DM JDBC 4.0 驱动程序符合 SUN JDBC4.0 标准，实现了下列新特性：

  1. JDBC 驱动类的自动加载；
  2. 连接管理的增强；
  3. 对 RowId SQL 类型的支持；
  4. SQL 的 DataSet 实现使用了 Annotations；
  5. SQL 异常处理的增强；
  6. 对 SQL XML 的支持；
  7. 对 BLOB/CLOB 的改进支持以及对国际字符集的支持。



## 4.4 DM JDBC 扩展

### 4.4.1 数据类型扩展

DM JDBC 驱动程序为了支持 DM 特有的数据类型，采用了自定义扩展包的方式来进行解决。dm.jdbc.driver 这个包中包含了 DM 特有数据类型所对应的类，而 dm.jdbc.driver.DmdbType 类中定义了 DM 的数据类型，见下表 4.1。通常情况下，使用 jdbc 绑定参数时，指定数据类型的位置都是指定 Java 中的标准数据类型（java.sql.Types），并使用 java.sql.Types 中的类型。除特殊情况下（例如，Java 标准数据类型中没有相关的 DM 数据类型），用户在使用 jdbc 时一般不会用到 DmdbType 中的数据类型。

表4.1 dm.jdbc.driver.DmdbType中定义的类型


|**类型编号**|**DmdbType 类型**|**说明**|
|-----------|-----------|-----------|
|0|CHAR|字符串类型|
|1|VARCHAR2|VARCHAR2 字符串类型|
|2|VARCHAR|VARCHAR 字符串类型|
|3|BIT|BIT STRING|
|5|TINYINT|整型 1 字节|
|6|SMALLINT|整型 2 字节|
|7|INT|整型 4 字节|
|8|BIGINT|整型 8 字节|
|9|DECIMAL|XDEC，定点数类型|
|10|REAL|浮点数类型，取值范围-3.4E+38  ～  3.4E+38|
|11|DOUBLE|浮点数类型，取值范围-1.7E+308  ～  1.7E+308|
|12|BLOB|变长的二进制大对象类型|
|13|BOOLEAN|BOOLEAN 类型|
|14|DATE|日期类型|
|15|TIME|时间类型|
|16|DATETIME|日期时间类型|
|17|BINARY|定长二进制数据类型|
|18|VARBINARY|变长二进制数据类型|
|19|CLOB|变长字符串类型|
|20|INTERVAL_YM|年-月间隔类型类型|
|21|INTERVAL_DT|日-时间隔类型类型|
|22|TIME_TZ|时间带时区类型|
|23|DATETIME_TZ|日期时间带时区类型|
|26|DATETIME2|DATETIME2 类型，毫秒精度 9 位|
|27|DATETIME2_TZ|DATETIME2 类型，带时区，毫秒精度 9 位|
|28|ROWID|ROWID 类型 msgVersion  >= 9*|
|29|NULL|空值类型|
|40|RECORD|RECORD 类型|
|41|TYPE|TYPE 类型|
|42|TYPE_REF|TYPE_REF 类型|
|54|UNKNOWN|UNKNOWN 类型|
|117|ARRAY|ARRAY 类型|
|119|CLASS|CLASS 类型|
|120|CURSOR|游标类型|
|121|PLTYPE_RECORD|PLTYPE_RECORD 类型|
|122|SARRAY|SARRAY 类型|


时间间隔类型是 DM 具有的数据类型，而 JDBC 标准中没有相对应的类型。故令 DmdbIntervalYM 和 DmdbIntervalDT 分别对应于年-月间隔类型和日-时间隔类型。JDBC 标准中的 java.sql.Types.Time 类型不允许带有纳秒，为了表示 DM 中带纳秒的时间类型，设立了 DmdbTimestamp 类型。这些类型的主要方法为：

1.DmdbIntervalDT

|**方法名**|**功能说明**|
|----|----|
|DmdbIntervalDT(byte[])|利用字节数组作为参数的构造函数|
|DmdbIntervalDT(byte[],int,int)|利用字节数组，指定引导精度、纳秒精度构造函数|
|DmdbIntervalDT(String)|利用字符串作为参数的构造函数|
|DmdbIntervalDT(String,int,int)|设置字符串，指定引导精度、纳秒精度构造函数|
|getByteArrayValue()|获取字节数组|
|getDTString()|把 DmdbIntervalDT 的值以字符串的形式返回|
|getDTType()|获取 DmdbIntervalDT 值类型|
|getDay()|获取 DmdbIntervalDT 值中日值|
|getHour()|获取 DmdbIntervalDT 值中时值|
|getMinute()|获取 DmdbIntervalDT 值中分值|
|getSecond()|获取 DmdbIntervalDT 值中秒值|
|getMsec()|获取 DmdbIntervalDT 值中毫秒值|
|getLoadPrec()|获取 DmdbIntervalDT 值中引导精度数|
|getSecPrec()|获取 DmdbIntervalDT 值中纳秒精度数|
|clear()|清除 DmdbIntervalDT 值|


2. DmdbIntervalYM

|**方法名**|**功能说明**|
|----|----|
|DmdbIntervalYM (byte[])|利用字节数组作为参数的构造函数|
|DmdbIntervalYM (byte[],int)|利用字节数组、精度作为参数构造函数，精度默认为 0|
|DmdbIntervalYM (String)|利用字符串作为参数的构造函数|
|DmdbIntervalYM (String,int)|利用字符串、精度作为参数的构造函数，精度默认为 0|
|getByteArrayValue()|把 DmdbIntervalYM 的值以字节数组的形式返回|
|getYMString()|把 DmdbIntervalYM 的值以字符串的形式返回|
|getYMType()|获取 DmdbIntervalYM 值类型|
|getYear()|返回 DmdbIntervalYM 值中的年值|
|getMonth()|返回 DmdbIntervalYM 值中的月值|
|getLoadPrec()|获取 DmdbIntervalYM 的引导精度|
|toString()|把 DmdbIntervalYM 的值转化为字符串的形式|
|clear()|清除 DmdbIntervalYM 值信息|


3.DmdbTimestamp

|**方法名**|**功能说明**|
|----|----|
|valueOf(Date)|根据指定的 java.util.Date 类型构造一个 DmdbTimestamp 类型|
|valueOf(String)|根据指定的时间类型字符串构造一个 DmdbTimestamp 类型|
|getDt()|返回一个此对象表示的时间值数组，内容为[年，月，日，时，分，秒，微秒，时区]|
|getTime ()|返回此对象表示的自 1970 年 1 月 1 日 00:00:00 GMT 以来的毫秒数|
|setTime(long)|使用给定毫秒时间值设置现有 DmdbTimestamp 对象|
|getTimezone()|获取 DmdbTimestamp 值中的时区值|
|setTimezone(int)|设置此对象的时区|
|getNanos()|获取此对象的纳秒值|
|setNanos(long)|设置此对象的纳秒值|
|toString()|把 DmdbTimestamp 的值转化为字符串的形式|


4.访问方式

为了能够在 DM JDBC 驱动程序中访问这些特有的数据类型，我们在 DmdbPreparedStatement、DmdbCallableStatement 和 DmdbResultSet 类中增加了许多方法。客户如果想使用这些方法，必须把对象强制转化为 DmdbXXX 类型：

```
PreparedStatement pstmt = connection.prepareStatement("insert into testInterval " + "values(?)");
((DmdbPreparedStatement)pstmt).setINTERVALYM(1, new DmdbIntervalYM("Interval '0015-08' year to month"));
int updateCount = pstmt.executeUpdate();
```

其它方法的使用方式与此类同。而且，时间间隔类型可以利用 setString 和 getString 方法进行存取，带纳秒的时间类型也可以当作普通的时间类型(不带纳秒)来进行操作。

另外的，在 Java1.8 之前的版本中，使用 Cursor 类型需要用到 dm.jdbc.driver.DmdbType 类；Java1.8 之后的版本中已经新增了 Types.REF_CURSOR 类型，此时可以直接使用 Java 标准数据类型。

使用示例如下：

```
/* 
create or replace procedure proc(p1 out cursor) 
as
declare
c1 cursor is select top 10 * from sysobjects;
begin
p1 = c1;
open p1;
end; 
*/
//java1.8之前的版本
CallableStatement cstmt = conn.prepareCall("call proc(?);");
cstmt.registerOutParameter(1, DmdbType.CURSOR);
cstmt.execute();

//java1.8及之后版本
CallableStatement cstmt = conn.prepareCall("call proc(?);");
cstmt.registerOutParameter(1, Types.REF_CURSOR);
cstmt.execute();
```

### 4.4.2 读写分离集群下的错误信息

当使用 JDBC 接口连接 DM 读写分离集群系统时，对返回的报错信息进行了扩展。在报错信息前增加了一个字符前缀用于标识报错信息是来自读写分离集群的主库还是备库：

  * 前缀[P]表示是来自主库的报错信息；
  * 前缀[S]表示是来自备库的报错信息。



## 4.5 建立 JDBC 连接

通常有两种途径来获得 JDBC 的连接对象，一种是通过驱动管理器 DriverManager 的 getConnection(String url, String user, String
password)方法来建立，另外一种就是通过数据源的方式来建立。

### 4.5.1 通过 DriverManager 建立连接

这种建立连接的途径是最常用的，也称作编程式连接。利用这种方式来建立连接通常需要以下几个步骤：

注册数据库驱动程序(driver)。可以通过调用 java.sql.DriverManager 类的 registerDriver 方法显式注册驱动程序，也可以通过加载数据库驱动程序类隐式注册驱动程序。

```
//显示注册
DriverManager.registerDriver(new dm.jdbc.driver.DmDriver());
//隐式注册
Class.forName("dm.jdbc.driver.DmDriver");
```

隐式注册过程中加载实现了 java.sql.Driver 的类，该类中有一静态执行的代码段，在类加载的过程中向驱动管理器 DriverManager 注册该类。而这段静态执行的代码段其实就是上述显式注册的代码。

建立连接。注册驱动程序之后，就可以调用驱动管理器的 getConnection 方法来建立连接。建立数据库连接需要指定连接数据库的 url、登录数据库所用的用户名 user 和密码 password。

通过 DriverManager 建立连接的具体过程如下：

  1. **加载 DM JDBC 驱动程序**



dm.jdbc.driver.DmDriver 类包含一静态部分，它创建该类的实例。当加载驱动程序时，驱动程序会自动调用 DriverManager.registerDriver 方法向 DriverManager 注册自己。通过调用方法 Class.forName(String str)，将显式地加载驱动程序。以下代码加载 DM 的 JDBC 驱动程序：

```
Class.forName("dm.jdbc.driver.DmDriver");
```

  2. **建立连接**



加载 DM JDBC 驱动程序并在 DriverManager 类中注册后，即可用来与数据库建立连接。DriverManager 对象提供三种建立数据库连接的方法。每种方法都返回一个 Connection 对象实例，区别是参数不同。

```
Connection DriverManager.getConnection(String url, java.util.Properties info);
Connection DriverManager.getConnection(String url);
Connection DriverManager.getConnection(String url, String user, String password);
```

通常采用第三种方式进行数据库连接，该方法通过指定数据库 url、用户名、口令来连接数据库。

以下代码建立与数据库的连接：

```
Class.forName("dm.jdbc.driver.DmDriver"); // 加载驱动程序
String url = "jdbc:dm://223.254.254.19"; // 主库IP = 223.254.254.19
String userID = "SYSDBA";
String passwd = "sysDBA*00";
Connection con = DriverManager.getConnection(url, userID, passwd);
```

利用这种方式来建立数据库连接，连接数据库所需要的参数信息都被硬编码到程序中，这样每次更换不同的数据库或登录用户信息都要对应用进行重新改写、编译，不够灵活。而且，当用户同时需要多个连接时，就不得不同时建立多个连接，造成资源浪费和性能低下。为了解决这些问题，SUN 公司在 JDBC 2.0 的扩展包中定义了数据源接口，提供了一种建立连接的新途径。

### 4.5.2 创建 JDBC 数据源

数据源是在 JDBC 2.0 中引入的一个概念。在 JDBC 2.0 扩展包中定义了 javax.sql.DataSource 接口来描述这个概念。如果用户希望建立一个数据库连接，通过查询在 JNDITM 服务中的数据源，可以从数据源中获取相应的数据库连接。这样用户就只需要提供一个逻辑名称（Logic Name），而不是数据库登录的具体细节。

JNDITM 的全称是 Java Naming and Directory Interface，可以理解为 Java 名称和目录服务接口。JNDI 向应用程序提供了一个查询和使用远程服务的机制。这些远程服务可以是任何企业服务。对于 JDBC 应用程序来说，JNDI 提供的是数据库连接服务。JNDI 使应用程序通过使用逻辑名称获取对象和对象提供的服务，从而使程序员可以避免使用与提供对象的机构有关联的代码。

一个 DataSource 对象代表一个实际的数据源。在数据源中存储了所有建立数据库连接的信息。系统管理员用一个逻辑名字对应 DataSource 对象，这个名字可以是任意的。在下面的例子中 DataSource 对象的名字是 NativeDB。依照传统习惯,DataSource 对象的名字包含在 jdbc/下，所以这个数据源对象的完整名字是：jdbc/NativeDB。

数据源的逻辑名字确定之后，就需要向 JNDI 服务注册该数据源。下面的代码展示了向 JNDI 服务注册数据源的过程。

```
// 初始化名称-目录服务环境
Context ctx = null;
	try{
		Hashtable env = new Hashtable (5);
		env.put (Context.INITIAL_CONTEXT_FACTORY, 
		"com.sun.jndi.fscontext.RefFSContextFactory");
		env.put (Context.PROVIDER_URL, "file:JNDI");
		ctx = new InitialContext(env);
	}
catch (NamingException ne)
{
		ne.printStackTrace();
}
bind(ctx, "jdbc/NativeDB");
```

程序首先生成了一个 Context 实例。javax.naming.Context 接口定义了名称服务环境(Naming Context)及该环境支持的操作。名称服务环境实际上是由名称和对象间的相互映射组成的。程序中初始化名称服务环境的环境工厂(Context Factory)是 com.sun.jndi.fscontext.RefFSContextFactory(该类在 fscontext.jar 中可以找到，由于 fscontext.jar 中包含的不是标准的 API，用户需要从 www.javasoft.com 中的 JNDI 专区下载 fscontext.jar)类，环境工厂的作用是生成名称服务环境的实例。javax.naming.spi.InitialContextFactory 接口定义了环境工厂应该如何初始化名称服务环境(该接口在 providerutil.jar 中实现，由于 providerutil.jar 中包含的不是标准的 API，用户需要从 www.javasoft.com 中的 JNDI 专区下载 providerutil.jar)。在初始化名称服务环境时还需要定义环境的 URL。程序中使用的是"file:JNDI"，也就是把环境保存在本地硬盘的 JNDI 目录下。目前很多 J2EETM 应用服务器都实现了自己的 JNDI 服务，用户可以选用这些服务包。

初始化了名称服务环境后，就可以把数据源实例注册到名称服务环境中。注册时调用 javax.naming.Context.bind()方法，参数为注册名称和注册对象。注册成功后，在 JNDI 目录下会生成一个.binding 文件，该文件记录了当前名称-服务环境拥有的名称及对象。具体实现如下例所示：

```
void bind (Context ctx, String ln)throws NamingException, SQLException
{
// 创建一个DmdbDataSource实例
DmdbDataSource dmds = new DmdbDataSource ();
// 把DmdbDataSource实例注册到JNDI中
ctx.bind (ln, dmds);
}
```

当需要在名称服务环境中查询一个对象时，需要调用 javax.naming.Context.lookup（）方法，并把查询到的对象显式转化为数据源对象。然后通过该数据源对象进行数据库操作。

```
DataSource ds = (DataSource) lookup (ctx, "jdbc/NativeDB");
Connection conn = ds.getConnection();
```

DataSource 对象中获得的 Connection 对象和用 DriverManager.getConnection 方法获得的对象是等同的。由于 DataSource 方法具有很多优点，该方法成为获得连接的推荐方法。

### 4.5.3 数据源与连接池

利用数据源可以增强代码的可移植性，方便代码的维护。而且还可以利用连接池的功能来提高系统的性能。连接缓冲池的工作原理是：当一个应用程序关闭一个连接时，这个连接并不真正释放而是被循环利用。因为建立连接是消耗较大的操作，循环利用连接可以减少新连接的建立，能够显著地提高性能。

JDBC 规范为连接池定义了两个接口，一个客户端接口和一个服务器端接口。客户端接口就是 javax.sql.DataSource，这样客户先前采用数据源来获得连接的代码就不需要任何的修改。通过数据源所获得的连接是否是缓冲的，这取决于具体实现的 JDBC 驱动程序是否实现了连接池的服务器端接口 javax.sql.ConnectionPoolDataSource。DM JDBC 实现了连接缓冲池，在实现连接缓冲池的过程中采用了新水平的高速缓存。一般说来，连接高速缓存是一种在一个池中保持数目较小的物理数据库连接的方式，这个连接池由大量的并行用户共享和重新使用，从而避免在每次需要时建立一个新的物理数据库连接，以及当其被释放时关闭该连接的昂贵的操作。连接池的实现对用户来说是透明的，用户不需为其修改任何代码。

### 4.5.4 DM 扩展连接属性的使用

除了标准 JDBC 接口功能，DM 扩展了一些具有自身特点的功能处理特性，这些特性可以通过在连接串上设置连接属性进行控制。另外，这些连接串属性也可以在 dm_svc.conf 中使用，而 dm_svc.conf 中的属性（通用配置项和 JDBC 配置项）也可以在 JDBC 连接串上设置。另外，dm_svc.conf 中 dexp 配置项、dpc_new 配置项、.Net provider 配置项、DPI 配置项与 JDBC 连接串无关，无须设置。

连接串的书写格式有以下三种：

格式一 host、port 不作为连接属性，此时只需输入值即可。

格式：

```
jdbc:dm://[host][:port][?propName1=propValue1][&propName2=propValue2][&…]…
```

参数介绍：

host：数据库所在 IP 地址，缺省为 localhost。若 host 为 ipv6 地址，则应包含在[]中；

port：数据库端口号，缺省为 5236；

？：是参数分隔符，表示后面的都是参数；

&：是参数间隔符，多个参数用&分开；

propName：连接串属性名称。详见表 4.2；

propValue：连接串属性值。

例如：

```
jdbc:dm://192.168.0.96:5236?resultSetType=1003
```

格式二 host、port 作为连接属性，此时必须按照表 4.2 中说明进行设置，且属性名称大小写敏感。

格式：

```
jdbc:dm://[?propName1=propValue1][&propName2=propValue2][&…]…
```

参数介绍：

host 和 port：设置与否，以及在属性串中的位置没有限制；

其它参数：和格式一相同。

例如：

```
jdbc:dm://?host=192.168.0.96&port=5236
```

格式三 使用自定义服务名，可指定多个数据库节点

格式：

```
jdbc:dm://GroupName[?propName1=propValue1][&propName2=propValue2][&…]…
或
jdbc:dm://GroupName?GroupName=(host1:port1,host2:port2,…) [&propName1=propValue1][&propName2=propValue2][&…]…
```

参数介绍：

GroupName：数据库服务名。若未指定服务名对应的 host:port，则将在配置文件 dm_svc.conf 中匹配相应的服务名；

其它参数：和格式一相同。

例如：

```
jdbc:dm://test?test=(192.168.0.96:5236,192.168.0.96:5237)
```

JDBC 连接串中可设置的属性中除了 user 和 password 是必须要设置的，其它属性均为可选项。如果同一个属性在 JDBC 连接串中和 dm_svc.conf 配置项中均有设置，但值却不同，则以 JDBC 连接串优先。

连接串中可以设置的属性及其说明见下表。表中所有时间均可以使用（h：小时，m：分钟，s：秒，ms：毫秒），不带单位使用时取配置项默认单位 ms，有效值范围 0~2147483647。缺省值为 5 分钟。

表 4.2 JDBC 连接串属性

|**属性名称**|**说明**|**是否必须设置**|
|------|------|------|
|host|主库地址，包括 IP 地址、localhost 或者配置文件中主库地址列表对应的变量名|否|
|port|端口号，服务器登录端口号|否|
|unixSocketFile|LINUX 系统中，当服务器与客户端之间使用 UNIXSOCETUNIX-IPC 方式通信时，用于指定客户端连接的 UNIXSOCKET 路径文件名。使用 unixSocketFile 时，不需要指定 host 和 port。例如：jdbc:dm://?user=SYSDBA&password=sysDBA*00&unixSocketFile=/home/te/foo.sock|否|
|user|登录用户。若指定 OsAuthType 参数使用操作系统用户登录，则无需指定 user，其他情况下需要指定 user|否|
|password|登录密码。若指定 OsAuthType 参数使用操作系统用户登录，则无需指定 password，其他情况下需要指定 password|否|
|appName|客户端应用程序名称|否|
|osName|操作系统名称|否|
|socketTimeout|网络通信链路超时时间；单位 ms，取值范围 0~2147483647，0 表示无限制；缺省为 0|否|
|sessionTimeout|会话超时时间；单位 s，取值范围 0~2147483647，0 表示无限制；缺省为 0|否|
|connectTimeout|连连接数据库超时时间；单位 ms，取值范围 0~2147483647，0 表示无限制；缺省为 5000|否|
|StmtPoolSize|语句句柄池大小；取值范围 0~2147483647，0 表示关闭；缺省为 15|否|
|PStmtPoolSize|prepare 语句句柄池大小；取值范围 0~2147483647，0 表示关闭；缺省为 0|否|
|pstmtPoolValidTime|prepare 语句缓存的有效时间；单位 ms，取值范围 0~2147483647，0 表示无限制；缺省为 0|否|
|escapeProcess|是否进行语法转义处理；取值 1/0 或 true/false；缺省为 true；1/true：是，0/false：否|否|
|autoCommit|是否自动提交；取值 1/0 或 true/false；缺省为 true；1/true：是，0/false：否|否|
|alwayseAllowCommit|在自动提交开关打开时，调用 rollback()接口是否不报错；取值 1/0 或 true/false；缺省为 true；1/true：是，0/false：否|否|
|localTimezone|指定客户端本地时区，对于本地时区相关时间类型会自动完成服务器时区与本地时区的转换；单位分钟，取值范围-779~840；缺省为当前系统时区|否|
|maxRows|结果集行数限制，超过上限结果集截断；取值范围 0~2147483647，0 表示无限制；缺省为 0|否|
|bufPrefetch|结果集 fetch 预取消息 buffer 大小；单位 KB，取值范围 32~65535。缺省为 0，表示按服务器配置，若结果集上指定了 fetchSize 会自动预估大小|否|
|LobMode|大字段数据获取模式；1 表示 get 数据时从服务器段获取，2 表示结果集生成时将大字段数据完整缓存到本地；缺省为 1|否|
|ignoreCase|结果集列名是否忽略大小写；取值 1/0 或 true/false；缺省为 true；1/true：是，0/false：否|否|
|continueBatchOnError 或 batchContinueOnError|批量执行出错时是否继续执行；缺省为 false；取值（true/True，false/False）；true/True：是，false/False：否|否|
|batchType|批处理模式；1 表示批量绑定执行，2 表示一行一行执行；缺省为 1|否|
|resultSetType|指定默认创建结果集类型，与 java 标准中结果集类型相对应；取值 1003/1004/1005，缺省为 1003；1003：对应 ResultSet.TYPE_FORWARD_ONLY；1004：对应 ResultSet.TYPE_SCROLL_INSENSITIVE；1005：对应 ResultSet.TYPE_SCROLL_SENSITIVE|否|
|dbmdChkPrv|编目函数是否进行权限检测；取值 1/0 或 true/false；缺省为 true；1/true：是，0/false：否|否|
|isBdtaRS|是否使用列模式结果集，需同步服务器开启该功能；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|clobAsString|clob 类型列是否调用 resultSetMetaData 的 getColumnType()映射为 Types.VARCHAR 类型，以及 resultSet.getObject 方法获取 clob 时是否将 clob 转为 string 并返回；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|columnNameCase|结果集列名大小写转换。取值 upper/lower/空值，缺省为空值。upper 表示转为大写；lower 表示转为小写；空值表示保持不变|否|
|compatible_mode 或 compatibleMode|兼容其他数据库。取值为数据库名称：oracle 表示兼容 Oracle，oracle11 表示兼容 Oracle 11c，oracle19 表示兼容 Oracle 19c，mysql 表示兼容 Mysql。本参数和 DM.INI 中的 COMPATIBLE_MODE 同名，但用途不同|否|
|schema|指定用户登录后的当前模式，默认为用户的默认模式|否|
|loginMode|指定优先登录的服务器模式。取值范围 0~4；0：优先连接 PRIMARY 模式或者 NORMAL 模式的库，最后选择 STANDBY 模式；1：只连接主库；2：只连接备库；3：优先连接 STANDBY 模式的库，PRIMARY 模式次之，最后选择 NORMAL 模式；4：优先连接 PRIMARY 模式的库，NORMAL 模式次之，最后选择 STANDBY 模式。缺省为 4。当启用读写分离时（即 rwSeparate 不为 0），loginMode 不生效|否|
|loginStatus|服务名方式连接数据库时只选择状态匹配的库； 取值范围 0、3~5；0 表示不限制；3 表示 mount 状态；4 表示 open 状态；5 表示 suspend 状态；缺省为 0|否|
|loginDscCtrl|服务名连接数据库时只选择 dsc control 节点的库；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|epSelector|服务名连接数据库时采用何种模型建立连接。取值范围 0~n。 0: 依次选取列表中的不同节点建立连接，使得所有连接均匀地分布在各个节点上；取值 >0：选择列表中第 n 个节点建立连接，只有当前节点无法建立连接时才会选择下一个节点进行连接。缺省为 0|否|
|epSelectorDynamic|表示是否根据节点存活状态动态调整 epSelector 的值；取值 true/false，true：开启，false：关闭；缺省为 false。若开启，一但本来要连的节点故障，连接移到下个节点，即使前面的节点恢复了，后续也连接不回前面的节点上，还是在下个节点上|否|
|autoReconnect 或 reconnect|连接发生异常或一些特殊场景下连接处理策略。取值范围 0~7；0：关闭连接；1：当连接发生异常时自动切换到其他库，无论切换成功还是失败都会抛一个 SQLException，用于通知上层应用进行事务执行失败时的相关处理；2：配合 epSelector=1 使用，如果服务名列表前面的节点恢复了，将当前连接切换到前面的节点上；4：保持各节点会话动态均衡，通过后台线程检测节点及会话数变化，并切换连接使之保持均衡。也可以将 autoReconnect 置为上述几个值的组合值，表示同时进行多项配置，如置为 3 表示同时配置 1 和 2；缺省为 0|否|
|switchTimes|服务名连接数据库时，若未找到符合条件的库成功建立连接，将尝试遍历服务名中库列表的次数；取值范围 1~2147483647；缺省为 1|否|
|switchInterval|服务名连接数据库时，若遍历了服务名中所有库列表都未找到符合条件的库成功建立连接，等待一定时间再继续下一次遍历；单位 ms，取值范围 0~2147483647；缺省为 1000|否|
|cluster|用于标识的集群类型。DW：主备；RW：读写分离；MPP：大规模并行处理集群；DPC：分布计算集群；DSC：数据共享集群。cluster=DSC 须配合 autoReconnect=2、epSelector=1 使用，用于检测 DSC 集群节点故障恢复是否成功;NORMAL：表示为单机库；缺省 NORMAL|否|
|dbAliveCheckFreq|检测数据库是否存活的频率。单位 ms，取值范围 0~2147483647，0 表示不检测；缺省为 0|否|
|compress|是否压缩消息。取值范围 0~2。0 表示不压缩；1 表示完全压缩；2 表示优化的压缩；缺省为 0|否|
|compressID|消息压缩算法标识，最终与服务器支持情况协商决定。0 表示 zip；1 表示 snappy； 缺省为 0|否|
|sslFilesPath|数据库端开启 ssl 通信加密，该参数指定 ssl 加密文件的路径|否|
|sslKeystorePass|数据库端开启 ssl 通信加密，该参数指定 ssl 加密文件的指令|否|
|uKeyName|Ukey 文件的绝对路径（例如 E:\thirdparty\ukey\x64\Debug\dmukey_v1.dll）|否|
|uKeyPin|Ukey 的口令|否|
|cipherPath|第三方加密算法引擎所在路径|否|
|OsAuthType|指定操作系统认证用户类型，开启操作系统认证时，用户名使用系统用户名。取值范围 0~4；0 表示关闭；1 表示 DBA；2 表示 SSO； 3 表示 AUDITOR；4 表示自适应。缺省为 0|否|
|loginCertificate|指定登录加密用户名密码公钥所在的路径，一旦配置即认为开启了客户端的证书加密用户名密码模式|否|
|mppLocal|是否 MPP 本地连接；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|rwSeparate|是否使用读写分离系统。0 表示不启用；1 表示启用；2 表示启用，备库由客户端进行选择，且只会选择服务名中配置的节点；3 表示启用，备库由服务器通过消息告知；4 表示启用，备库由客户端进行选择，只连接事务一致性备库；5 表示启用，为 gateway 准备，连接 epList 列表内第一个可用的备库。缺省为 0当启用读写分离时，loginMode 失效|否|
|rwPercent|分发到主库的事务占主备库总事务的百分比；单位 %，取值范围 0~100；缺省为 25|否|
|rwAutoDistribute|读写分离系统事务分发是否由 JDBC 自动管理；取值 1/0 或 true/false；缺省为 true；1/true：是，false：事务分发由用户管理，用户可通过设置连接上的 readOnly 属性标记事务为只读事务|否|
|rwHA|是否开启读写分离系统高可用；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|rwStandbyRecoverTime|读写分离系统备库故障恢复检测间隔，单位 ms，取值范围 0~2147483647，0 表示不检测；缺省为 60000|否|
|enRsCache|是否开启结果集缓存；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|rsCacheSize|设置结果集缓冲区大小，单位 MB。取值范围 1~65535，如果设置太大，可能导致空间分配失败，进而使缓存失效|否|
|rsRefreshFreq|结果集缓存检查更新的频率，单位 S，取值范围 0~10000，缺省为 10。如果设置为 0，则不需检查更新|否|
|keyWords|标识用户关键字，所有在列表中的字符串，如果以单词的形式出现在 sql 语句中，则这个单词会被加上双引号；默认为空串|否|
|logDir|日志等其他一些 JDBC 过程文件生成目录，默认为 jvm 当前工作目录|否|
|logLevel|生成日志的级别，日志按从低到高依次如下（off：不记录；assert：断言日志信息；error：只记录错误日志；warn：记录警告信息；sql：记录 sql 执行信息；info：记录全部执行信息；DEBUG：记录 debug 信息；all：记录全部），高级别同时记录低级别的信息；缺省为 off|否|
|logFlushFreq|日志刷盘频率；单位 s，有效值范围 0~2147483647；缺省为 30|否|
|statEnable|是否启用状态监控；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|statDir|状态监控信息以文本文件形式输出的目录，默认为 jvm 当前工作目录|否|
|statFlushFreq|状态监控统计信息写文件刷盘频率；单位 s，取值范围 0~2147483647；0 表示不写文件；缺省为 30|否|
|statSlowSqlCount|统计慢 sql top 行数；取值范围 0~1000；缺省为 100|否|
|statHighFreqSqlCount|统计高频 sql top 行数；取值范围 0~1000；缺省为 100|否|
|statSqlMaxCount|状态监控可以统计不同 sql 的个数；取值范围 0~100000；缺省为 1000|否|
|statSqlRemoveMode|执行的不同 sql 个数超过 statSqlMaxCount 时使用的淘汰方式；取值 latest/eldest；latest 表示淘汰最近执行的 sql，eldest 表示淘汰最老的 sql；缺省为 latest|否|
|dmsvcconf|指定 url 属性配置文件所在路径|否|
|dbAliveCheckTimeout|检测数据库是否存活的连接超时时间。单位 ms，范围 0~2147483647，0 表示无超时。缺省为 10000|否|
|checkFreq|服务名连接数据库时，循环检测连接是否需要重置的时间间隔。即每间隔设定时间检测连接对象是否发生改变，若连接对象发生改变，JDBC 连接会自动重置到新对象。单位 ms，取值范围 0~2147483647。缺省值为 300000|否|
|prepareOptimize|是否对预编译 SQL 做优化；取值 1/0 或 true/false；缺省为 false;1/true：是，0/false：否|否|
|allowRange|允许动态负载均衡误差范围的百分比，百分比越大表示允许的误差范围越大，取值范围 0~50。缺省为 5|否|
|localEncrypt|是否启用用户名密码本地加密；取值 1/0 或 true/false；缺省为 false;1/true：是，0/false：否|否|
|localEncryptEngine|本地加密引擎名，不指定时使用系统内置的加密引擎，用户可指定自定义的加密引擎。缺省为空|否|
|genKeyNameCase|调用 preparedStatement(String sql, String[] columnName)接口 prepare sql 时可同时指定列名，用于在表数据增删改后返回指定的列值。该参数用于指定这里的列名的大小写。取值 0/1/2 或 none/upper/lower；缺省为 upper。取值 0 或 none:保持用户输入不变；1 或 upper: 将用户输入转为大写；2 或 lower: 将用户输入转为小写|否|
|afterGetMoreResults|调用 JDBC 标准接口 getMoreResults()后是否关闭之前的结果集；取值 1/2/3；缺省为 1；1：关闭当前一个结果集；2：保持当前结果集打开；3：关闭当前所有结果集|否|
|checkExecType|是否检查 executeXXX 接口执行的 SQL 语句类型与接口是否匹配；取值 1/0 或 true/false；缺省为 false;1/true：是，0/false：否|否|
|quoteReplace|是否将 SQL 语句中的双引号改成单引号；取值 1/0 或 true/false；缺省为 false;1/true：是，0/false：否|否|
|ignoreWarnings|用于设置忽略指定的 SQLWarnings，取值与含义如下：all：忽略所有警告；sqlCode：忽略指定的警告；op sqlCode：忽略满足条件的警告，op 可以为符号 >、\<、>=、\<=、!；(sqlCode,sqlCode)：忽略指定的多个警告，默认为空|否|
|paramBindMode|用于设置参数绑定类型，取值 1/0，缺省为 0。0：参数为推荐类型时，按用户类型进行绑定；1：参数为推荐类型时，按服务器推荐类型进行绑定|否|
|language|使用的语言信息：CN（表示简体中文）、EN（表示英文）和 CNT_HK（表示香港繁体中文）。可以不指定，若不指定，系统会读取操作系统信息获得语言信息，建议有需要才指定。如果此处想使用 CNT_HK，则建库时 CHARSET/UNICODE_FLAG 必须指定为 UTF-8|否|
|errMap|自定义 DM 错误码和 Oracle 错误码的映射关系。格式：errMap=(dmCode:oracleCode, ...., dmCode:oracleCode)例如：errMap=(-2223:955, -2207:2289)|否|
|customFilter|SQL 拦截器，用于在执行 SQL 语句之前实现其他相关操作|否|
|zipkinServiceName|zipkin 服务名，用于 zipkin 链路追踪，必须和 sql 拦截器 customFilter =com.dameng.zipkin.DmZipkinCustom 一起使用，缺省值为“dmService”。例如：jdbc:dm://localhost:5236?customFilter=com.dameng.zipkin.DmZipkinCustom&zipkinServiceName=myDatabaseService|否|
|reconnectErrors|用于指定需要重连的异常，即当用服务名进行连接时，若发生指定的异常时将继续找下一个节点进行连接，发生其他异常则会抛出异常停止连接。  配置格式：reconnectErrors=(errorCode,errorCode,......)。errorCode 为数据库错误码（其中，超过了最大连接限制错误码-6001 不论设置与否，都会继续重连）。  例如：reconnectErrors=(-14009,-13217)|否|
|LogSwitchMode|logLevel 不为 off 时生效。logSwitchMode 表示 SQL 日志文件切换的模式：0：不切换；1：按文件中记录数量切换；2：按文件大小切换;3:按时间间隔切换。缺省值为 2|否|
|logSwitchLimit|logLevel 不为 off 时生效。logSwitchLimit 默认值与切换模式有关。  不同切换模式(logSwitchMode)下，意义不同：logSwitchMode=1(按数量切换时)，一个日志文件中的记录条数达到多少条之后，系统自动将日志切换到另一个文件中。取值范围 1000-10000000，缺省为 100000；  logSwitchMode=2(按文件大小切换时)，一个日志文件达到该大小后，系统自动将日志切换到另一个文件中，单位为 MB，取值范围 1-2000，缺省 128；  logSwtichMode=3(按时间间隔切换时)，每隔指定的时间间隔，系统自动将日志切换到另一个文件中，单位分钟，取值范围 1-30000，缺省值为 60|否|
|logFileNum|总共记录多少个日志文件（会清除当前目录下旧有的 dmjdbc 日志，请注意使用），当日志文件达到这个设定值以后，再生成新的文件时，会删除最早的那个日志文件。取值范围 2-1024。日志文件名称中将包含日期时间信息。缺省值为 5|否|
|sslProtocol|指定 SSL 协议版本。例如 SSLv3，缺省为空|否|
|GWDSN|Gateway 连接的数据源名称|否|
|numTruncSwitch|对超级小的数字做截断处理。缺省为 false|否|
|userNewPwd|当前用户修改的新密码。缺省为空|否|
|serverOption(同义词 server_option)|登录数据库的附加信息。缺省为空|否|
|statFileType|监控信息记录的文件格式：csv、txt。缺省 csv|否|
|highConcurrency|是否为高并发模式。取值 true/false。缺省为 false|否|
|compatibleURL|第三方 URL 解析标识符。缺省为 true|否|
|Shakecrypto|加密引擎名，例如 ZB。缺省为空|否|
|rsCacheEnable|是否开启对特定 SQL 获取所有结果集并缓存。取值 1/0 或 true/false；缺省为 false；特定 SQL 需满足两个条件：1.类型为 SELECT；2. SQL 语句中包含/jdbcrscache/或 SQL 在文件中被指定|否|
|rsCacheTimeout|缓存有效时间。缺省为 60，取值范围 0~ 2147483647|否|
|rsCacheTimeoutUnit|缓存有效时间单位，可选项 0/1/2，对应单位为秒/分/时，缺省为秒|否|
|rsCacheLimit|每个 SQL 绑定参数的值存在多种组合情况，本参数表示组合情况的最大值，缺省为 100，取值范围 1~ 2147483647|否|
|rsCacheFilePath|指定文件路径。文件用于保存需要被缓存的类型为 SELECT 的 SQL 语句。缺省为 NULL|否|
|failOverWait|是否等待 epSelector 号节点被拉起（epSelector>0 生效），取值 true/false，默认值为 true|否|
|trxRetry|是否开启 statement 事务重做，默认 0,取值范围[0, 2]；0 代表关闭事务重做；1 代表重做全部事务；2 代表重做事务，但略过 executeQuery 的事务|否|
|trxRetryInterval|statement 事务重做间隔时间，单位毫秒，默认 200|否|
|trxRetryTimes|statement 事务最大重做次数，默认 3 次|否|
|nvarcharSwitch|结果集元数据信息，是否开启 nvarchar 到 varchar 类型名称的转换，取值 true/false；默认 false||
|compatibleMybatis|是否开启 mybatis 对应 generate keys 的兼容模式；取值 1/0 或 true/false；缺省为 false；1/true：是，0/false：否|否|
|walletLocation|钱包文件路径|否|
|walletPwd|钱包文件秘钥|否|
|walletServerName|钱包文件中包含的服务名（用于匹配用户）|否|


### 4.5.5 JDBC 驱动端与其他数据库的兼容性处理

上一节中介绍的 JDBC 驱动端 compatible_mode 参数，与 DM 数据库服务器端的 DM.INI 参数 compatible_mode 名称相同，但用途不同。

JDBC 驱动端 compatible_mode 的作用是在 DM JDBC 接口表现上与其他数据库的 JDBC 接口表现上做的兼容性处理。该参数取值： oracle：表示兼容 Oracle JDBC 接口；oracle11 表示兼容 Oracle 11c  JDBC 接口；Oracle19：表示兼容 Oracle 19c JDBC 接口；mysql：表示兼容 Mysql JDBC 接口。下面分别进行介绍：

#### 4.5.5.1 兼容 Oracle 的情况

兼容 Oracle 的情况分为对老版本 Oracle 的兼容和对新版本 Oracle 19c 的兼容。

当 JDBC 驱动端 compatible_mode=oracle，兼容 Oracle JDBC 接口时，会出现以下情况：

  1. 会话上的 readOnly 属性设置无效。

Connection.setReadOnly()接口忽略设置，Connection.isReadOnly() == false。

  2. 会话关闭前做事务提交。

相关接口 Connection.close() ，调用时会先执行 Connection.commit()。

  3. statement 有多种执行接口，各执行接口不再有对 SQL 类型的限制。

Statement 的多种执行接口包括 execute、executeUpdate、executeQuery 等。当 checkExecType=1
时，这些接口对执行的 sql 语句类型有限制，executeUpdate 不能执行查询语句，executeQuery 不能执行更新语句等诸如此类的。当
checkExecType=0 时，兼容 Oracle 的处理，各执行接口不再有对 SQL 类型的限制。

  4. DatabaseMetaData 类的 getFunctions / getProcedures / getFunctionColumns / getProcedureColumns 接口，可以搜索到包对象的内部存储过程和函数 。

  5. DatabaseMetaData.getPrimaryKeys()返回结果中的列 pk_name 为索引名。

DM 本来返回的为约束名，Oracle 为索引名。

  6. Url 格式串兼容 Oracle url 格式串前缀"jdbc:oracle:@" / "jdbc:oracle:thin:@"。

例如：连接 DM 数据库时 url 可写为：”jdbc:oracle:thin:@localhost:5236”

  7. CLOB/BLOB 类的写入数据接口(setXXX)，参数偏移允许从 0 开始。

DM 偏移从 1 开始, 而 Oracle 从 0 开始，内部做兼容处理，允许偏移从 0 开始。

  8. 对于所有的数值类型，ResultSetMetaData 和 ParameterMetaData 的类型描述都为 Types.NUMERIC，当调用 getObject 接口获取数据时返回的为 BigDecimal。

  9. 从数据库中获取到 dec 类型的数据，不保留无效的末尾 0。



当 JDBC 驱动端 compatible_mode=oracle19 时，兼容 Oracle 19c JDBC 接口，同时也包含对老版本 Oracle 的兼容，但进行批量绑定时，executeBatch 的返回值存在差异：配置为 compatiblemode=oracle19 时正常返回实际影响的行数，配置为 compatiblemode=oracle 时返回-2。

#### 4.5.5.2 兼容 Mysql 的情况

当 JDBC 驱动端 compatible_mode=mysql，兼容 Mysql JDBC 接口时，会出现以下情况：

会话上设置事务隔离级别 Connection.TRANSACTION_REPEATABLE_READ 时，系统强制改为 Connection.TRANSACTION_READ_COMMITTED。DM 不支持隔离级 Connection.TRANSACTION_REPEATABLE_READ，不开兼容时会忽略设置使用默认隔离级。开启兼容时强制设为 Connection.TRANSACTION_READ_COMMITTED 级别。

### 4.5.6 获取执行计划

Connection 对象提供了获取执行计划的方法 getExplainInfo(SQL 语句)。

以下代码展示了如何获取并打印执行计划：

```
import dm.jdbc.driver.DmdbConnection;
import java.sql.*;
public class database {
    static String dmname = "dm.jdbc.driver.DmDriver";
    public static void main(String[] args) throws ClassNotFoundException {
        try {
            Class.forName(dmname);//加载驱动程序
			String url = "jdbc:dm://223.254.254.6:5236";  //连接机器IP = 223.254.254.6
            String userid = "SYSDBA";
            String pwd = "sysDBA*00";
            DmdbConnection connection = (DmdbConnection) DriverManager.getConnection(url, userid, pwd);
            //获取执行计划
            String getExplan = connection.getExplainInfo("select * from T1;");
            //打印执行计划
            System.out.println(getExplan);
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }
    }
}
```

## 4.6 Statement/PreparedStatement/CallableStatement

### 4.6.1 Statement

  1. **概述**



Statement 对象用于将 SQL 语句发送到数据库服务器。DM JDBC 提供三种类型的语句对象：Statement，PreparedStatement，CallableStatement。其中 PreparedStatement 是 Statement 的子类，CallableStatement 是 PreparedStatement 的子类。每一种语句对象用来运行特定类型的 SQL 语句。

Statement 对象用来运行简单类型的 SQL 语句，语句中无需指定参数。

PreparedStatement 对象用来运行包含（或不包含）IN 类型参数的预编译 SQL 语句。

CallableStatement 对象用来调用数据库存储过程。

  2. **创建 Statement 对象**



建立连接后，Statement 对象用 Connection 对象的 createStatement 方法创建，以下代码创建 Statement 对象：

```
Connection con = DriverManager.getConnection(url, "SYSDBA", "sysDBA*00");
Statement stmt = con.createStatement();
```

  3. **使用 Statement 对象执行语句**



Statement 接口提供了三种执行 SQL 语句的方法：executeQuery、executeUpdate 和 execute。

方法 executeQuery 用于产生单个结果集的语句，例如 SELECT 语句。

方法 executeUpdate 用于执行 INSERT、UPDATE 或 DELETE 语句以及 SQL DDL 语句，如 CREATE TABLE 和 DROP TABLE。INSERT、UPDATE 或 DELETE 语句的效果是修改表中零行或多行中的一列或多列。executeUpdate 的返回值是一个整数，表示受影响的行数。对于 CREATE TABLE 或 DROP TABLE 等 DDL 语句，executeUpdate 的返回值总为零。

方法 execute 用于执行返回多个结果集、多个更新元组数或二者组合的语句。

执行语句的三种方法都将关闭所调用的 Statement 对象的当前打开结果集（如果存在）。这意味着在重新执行 Statement 对象之前，需要完成对当前 ResultSet 对象的处理。

  4. **关闭 Statement 对象**



Statement 对象可由 Java 垃圾收集程序自动关闭。但作为一种良好的编程风格，应在不需要 Statement 对象时显式地关闭它们。这将立即释放数据库服务器资源，有助于避免潜在的内存问题。

  5. **性能优化调整**



  1. 批处理更新



DM JDBC 驱动程序提供批处理更新的功能，通过批处理更新可以一次执行一个语句集合。JDBC 提供了三个方法来支持批处理更新：通过 addBatch 方法，向批处理语句集中增加一个语句；通过 executeBatch 执行批处理更新；通过 clearBatch 来清除批处理语句集。

批处理更新过程中，如果出现执行异常，DM 将立即退出批处理的执行，同时返回已经被执行语句的更新元组数(在自动提交模式下)。

推荐批处理更新在事务的非自动提交模式下执行。同时，批处理更新成功后，需要主动提交以保证事务的永久性。

2)性能优化参数设置

DM JDBC 驱动程序提供了 setFetchDirection、setFetchSize 来向驱动程序暗示用户操作语句结果集的缺省获取方向和一次获取的缺省元组数。这些值的设定可以为驱动程序优化提供参考。

  6. **语句对象**



JDBC 在创建语句对象时，可以指定语句对象的缺省属性：结果集类型和结果集并发类型。DM JDBC 驱动程序支持 TYPE_FORWARD_ONLY 和 TYPE_SCROLL_INSENSITIVE 两种结果集类型，不支持 TYPE_SCROLL_SENSITIVE 结果集类型；支持 CONCUR_READ_ONLY、CONCUR_UPDATABLE 两种结果集并发类型。

语句对象的缺省属性用来指定执行语句产生的结果集的缺省类型。

注意事项：

DM JDBC 驱动程序中当执行的查询语句涉及多个基表时，结果集不能更新，结果集的类型可能被自动转换为 CONCUR_READ_ONLY 类型。

### 4.6.2 PreparedStatement

  1. **概述**



PreparedStatement 继承 Statement，并与之在两方面有所不同：

1)PreparedStatement 对象包含已编译的 SQL 语句，语句已经“准备好”；

2)包含于 PreparedStatement 对象中的 SQL 语句可具有一个或多个 IN 参数。IN 参数的值在 SQL 语句创建时未被指定。相反，该语句为每个 IN
参数保留一个问号（“?”）作为占位符。每个问号所对应的值必须在该语句执行之前，通过适当的 setXXX 方法来提供。

由于 PreparedStatement 对象已预编译过，所以其执行速度要快于 Statement 对象。因此，需要多次重复执行的 SQL 语句经常创建为 PreparedStatement 对象，以提高效率。

作为 Statement 的子类，PreparedStatement 继承了 Statement 的所有功能。另外它还添加了一整套方法，用于设置发送给数据库以取代 IN 参数占位符的值。同时，三种方法 execute、executeQuery 和 executeUpdate 能执行设置好参数的语句对象。

  2. **创建 PreparedStatement 对象**



以下的代码段（其中 con 是 Connection 对象）创建一个 PreparedStatement 对象：

```
PreparedStatement pstmt = con.prepareStatement(
"UPDATE person.person SET name = ? WHERE personid = ?");
```

对象 pstmt 包含语句 "UPDATE person.person SET name = ? WHERE personid =?"，该语句带两个 IN 参数占位符，它已发送给数据库，并由服务器为其执行作好了准备。

  3. **传递 IN 参数**



在执行 PreparedStatement 对象之前，必须设置占位符（“?”）的值。这可通过调用 setXXX 方法来完成，其中 XXX 是与该参数相应的类型。例如，如果参数具有 Java 类型 String，则使用的方法就是 setString。setXXX 方法的第一个参数是要设置的参数的序号（从 1 算起），第二个参数是设置给该参数的值。譬如，以下代码将第一个参数设为“张三”，第二个参数设为“18”：

```
pstmt.setString(1, "张三");
pstmt.setInt(2, 18);
```

每当设置了给定语句的参数值，就可执行该语句。设置一组新的参数值之前，应先调用 clearParameters 方法清除原先设置的参数值。

  4. **使用 setObject**



setObject 方法可显式地将输入参数转换为特定的 JDBC 类型。该方法可以接受三个参数，其中第三个参数用来指定目标 JDBC 类型。将 Java Object 发送给数据库之前，驱动程序将把它转换为指定的 JDBC 类型。例如，上面的 setXXX 语句就可以改写为：

```
pstmt.setObject(1, "张三", java.sql.Types.VARCHAR);

pstmt.setObject(2, 18, java.sql.Types.INTEGER);
```

如果没有指定 JDBC 类型，驱动程序就会将 Java Object 映射到其缺省的 JDBC 类型，然后将它发送到数据库，这与常规的 setXXX 方法类似。在这两种情况下，驱动程序在将值发送到数据库之前，会将该值的 Java 类型映射为适当的 JDBC 类型。二者的差别在于 setXXX 方法使用从 Java 类型到 JDBC 类型的标准映射，而 setObject 方法使用从 Java Object 类型到 JDBC 类型的映射。

方法 setObject 允许接受所有 Java 对象，这使应用程序更为通用，并可在运行时接受参数的输入。这样，如果用户在编辑应用程序时不能确定输入类型，可以通过使用 setObject，对应用程序赋予可接受的 Java 对象，然后由 JDBC 驱动程序自动将其转换成数据库所需的 JDBC 类型。但如果用户已经清楚输入类型，使用相应的 setXXX 方法是值得推荐的，可以提高效率。

  5. **将 JDBC NULL 作为 IN 参数发送**



setNull 方法允许程序员将 JDBC NULL 值作为 IN 参数发送给数据库。在这种情况下，可以把参数的目标 JDBC 类型指定为任意值，同时参数的目标精度也不再起作用。

  6. **发送大的 IN 参数**



setBytes 和 setString 方法能够发送无限量的数据。但是，内存要足够容纳相关数据。有时程序员更喜欢用较小的块传递大型的数据，这可通过将 IN 参数设置为 Java 输入流来完成。当语句执行时，JDBC 驱动程序将重复调用该输入流，读取其内容并将它们当作实际参数数据传输。

JDBC 提供了四种将 IN 参数设置为输入流的方法：setBinaryStream 用于字节流，setAsciiStream 用于 ASCII 字符流，setUnicodeStream 用于 Unicode 字符流，从 JDK1.2 起，输入字符流的新方法为 setCharacterStream，而 setAsciiStream 和 setUnicodeStream 已经很少用。

  7. **获得参数元数据**



DM JDBC 实现了 getParameterMetaData()方法，通过这个方法可以获得有关 IN 参数的各种属性信息，比如类型、精度、刻度等信息，类似于结果集元数据的内容。通过这些信息，用户可以更准确地设置 IN 参数的值。

在下面的代码中涉及到了这种方法：

```
PreparedStatement pstmt = conn.prepareStatement("SELECT * FROM person.person " + "WHERE personid = ?");
...
//获得参数元数据对象
ParameterMetaData pmd = pstmt.getParameterMetaData();
//获得参数的个数
int paramCount = pmd.getParameterCount();
//获得第一参数的类型
int colType = pmd.getParameterType(1);
…
```

  8. **自定义方法列表**



为了实现对达梦数据库所提供的时间间隔类型和带纳秒的时间类型的支持，在实现 PreparedStatement 接口的过程中，增加了一些自定义的扩展方法。用户将获得的 PreparedStatement 对象反溯成 DmdbPreparedStatment 类型就可以访问这些方法。这些方法所涉及到的扩展类请参看 4.4 节。

表4.3 自定义方法列表

|**方法名**|**功能说明**|
|----|----|
|setINTERVALYM|设置参数为年-月时间间隔类型值|
|setINTERVALDT|设置参数为日-时时间间隔类型值|
|setTIME|设置参数为时间类型(带纳秒)|


例如，想要插入一个年月时间间隔类型，代码如下（pstmt 为 PreparedStatement 类型）：

```
DmdbPreparedStatement dmps = (DmdbPreparedStatement)pstmt;
DmdbIntervalYM dmiym = new DmdbIntervalYM("interval '20-10' year to month");
dmps.setINTERVALYM(1, dmiym);
```

### 4.6.3 CallableStatement

  1. **概述**



CallableStatement 用来运行 SQL 存储过程。存储过程是数据库中已经存在的 SQL 语句，它通过名字调用。

CallableStatement 是 PreparedStatement 的子类。CallableStatement 中定义的方法用于处理 OUT 参数或 INOUT 参数的输出部分：注册 OUT 参数的 JDBC 类型（一般 SQL 类型）、从这些参数中检索结果，或者检查所返回的值是否为 JDBC NULL。

  2. **创建 CallableStatement 对象**



CallableStatement 对象是用 Connection.prepareCall 创建的。以下代码创建 CallableStatement 对象，其中含有对存储过程 p1 的调用，con 为连接对象：

```
CallableStatement cstmt = con.prepareCall("call p1(?, ?)");
```

其中"?"占位符为 IN、OUT 还是 INOUT 参数，取决于存储过程 p1。

  3. **IN 和 OUT 参数**



将 IN 参数传给 CallableStatement 对象是通过 setXXX 方法完成的。该方法继承自 PreparedStatement。所传入参数的类型决定了所用的 setXXX 方法（例如，用 setString 来传入 String 值等）。

如果存储过程返回 OUT 参数，则在执行 CallableStatement 对象之前必须先注册每个 OUT 参数的 JDBC 类型，有的参数还要同时提供刻度。注册 JDBC 类型是用 registerOutParameter 方法来完成的。语句执行完后，CallableStatement 的 getXXX 方法将取回参数值。其中 XXX 是为各参数所注册的 JDBC 类型所对应的 Java 类型。换言之，registerOutParameter 使用的是 JDBC 类型（因此它与数据库返回的 JDBC 类型匹配），而 getXXX 将之转换为 Java 类型。

设存储过程 p1 的定义如下：

```
CREATE OR REPLACE PROCEDURE p1( a1 IN VARCHAR(10), a2 OUT VARCHAR(50)) AS
DECLARE
CURSOR CUR1 FOR SELECT name FROM person.person WHERE personid = a1;
BEGIN
OPEN CUR1;
FETCH CUR1 INTO a2;
END;
```

以下代码先注册 OUT 参数，执行由 cstmt 所调用的存储过程，然后检索通过 OUT 参数返回的值。方法 getString 从 OUT 参数中取出字符串：

```
CallableStatement cstmt = con.prepareCall("call p1(?, ?)");
cstmt.setString(1, "1");
cstmt.registerOutParameter(2, java.sql.Types.VARCHAR);
cstmt.executeUpdate();
String x = cstmt.getString(2);
```

  4. **INOUT 参数**



如果参数为既接受输入又接受输出的参数类型（IN OUT 参数），那么除了调用 registerOutParameter 方法外，还要调用对应的 setXXX 方法（继承自 PreparedStatement）。setXXX 方法将参数值设置为输入参数，而 registerOutParameter 方法将它的 JDBC 类型注册为输出参数。setXXX 方法提供一个 Java 值，驱动程序先把这个值转换为 JDBC 值，然后将它送到数据库服务器。

该 IN 值的 JDBC 类型和提供给 registerOutParameter 方法的 JDBC 类型应该相同。如果要检索输出值，就要用对应的 getXXX 方法。

设有一个存储过程 p2 的定义如下：

```
CREATE OR REPLACE PROCEDURE p2(a1 IN OUT VARCHAR(10)) AS
DECLARE
CURSOR CUR1 FOR SELECT name FROM person.person WHERE personid = a1;
BEGIN
OPEN CUR1;
FETCH CUR1 INTO a1;
END;
```

以下代码中，方法 setString 把参数设为“1”。然后，registerOutParameter 将该参数注册为 JDBC VARCHAR。执行完该存储过程后，将返回一个新的 JDBC VARCHAR 值。方法 getString 将把这个新值作为 Java 的 String 类型返回。

```
CallableStatement cstmt = con.prepareCall("call p2(?)");
cstmt.setString(1, "1");
cstmt.registerOutParameter(1, java.sql.Types.VARCHAR);
cstmt.executeUpdate();
String x = cstmt.getString(1);
```

  5. **利用参数名进行操作**



在通常的情况下一般采用参数索引来进行赋值。JDBC3.0 规范要求可以利用参数名来对参数进行赋值，DM  JDBC 驱动程序实现了这一点。如果某个存储过程的一些参数有默认值，这时候采用参数名进行赋值就非常有用，用户可以只对那些没有默认值的参数进行赋值。参数名可以通过调用 DatabaseMetaData.getProcedureColumns()来获得。

下面的代码中，存储过程 p2 为上面那个存储过程 p2。利用参数名进行操作：

```
CallableStatement cstmt = con.prepareCall("CALL p2 (?)");
cstmt.setString("a1", "1");
cstmt.registerOutParameter("a1", java.sql.Types.VARCHAR);
cstmt.executeUpdate();
String x = cstmt.getString("a1");
```

而且，在读取参数的值和进行参数注册的时候，setXXX、getXXX、registerOutParameter 也都可以采用参数名来进行操作。通过调用 DatabaseMetaData.supportsNamedParameters()方法就可以确定 JDBC 驱动程序是否支持利用参数名来进行操作。

在执行同一条语句的过程中，不允许交叉使用参数索引和参数名来进行操作，否则会抛出异常。

  6. **自定义方法列表**



为了实现对达梦数据库所提供的时间间隔类型和带纳秒的时间类型的支持，在实现 CallableStatement 接口的过程中，增加了一些自定义的扩展方法。用户将获得的 CallableStatement 对象反溯成 DmdbCallableStatment 类型就可以访问这些方法。这些方法所涉及到的扩展类请参看 4.4 DM JDBC 扩展这一节。

表4.4 自定义方法列表

|**方法名**|**功能说明**|
|----|----|
|getINTERVALYM(int)|获得参数的值|
|getINTERVALYM(String)|获得参数的值|
|getINTERVALDT(int)|获得参数的值|
|getINTERVALDT(String)|获得参数的值|
|getTIME(int)|获得参数的值|
|getTIME(String)|获得参数的值|
|setTIME(String,String)|根据参数名来设置 DmdbTime 类型值。|
|setINTERVALDT(String,String)|根据参数名来设置 DmdbIntervalDT 类型值。|
|setINTERVALYM(String,String)|设置参数为年-月时间间隔类型值|


由于 CallableStatement 是 PreparedStatement 的子类，因此，它将继承 PreparedStatement 的所有的方法。

### 4.6.4 获取执行过程中服务器返回的打印消息

DM 的 Statement 对象提供了扩展的 getPrintMsg()方法，可以获得语句执行过程中服务器返回的打印消息，继承自 Statement 的 PreparedStatement 和 CallableStatement 也提供此方法。

下面的例子展示了如何在语句执行过程中获取服务器返回的打印消息。

```
//建立连接
Class.forName("dm.jdbc.driver.DmDriver");  //加载驱动程序
DmdbConnection connection = (DmdbConnection) DriverManager.getConnection("jdbc:dm://127.0.0.1:5236", "SYSDBA", "sysDBA*00");

//执行语句
Statement stmt = connection.createStatement();
stmt.executeUpdate("begin dbms_output.enable(100000); end;");
DmdbCallableStatement cstmt = (DmdbCallableStatement)connection.prepareCall(
"begin\n "
+"dbms_output.put_line('使用dbms_output包打印日志');\n"
+"dbms_output.put_line('这是日志1');\n"
+"dbms_output.put_line('这是日志2');\n"
+ "end;"
);
cstmt.execute();

System.out.println(cstmt.getPrintMsg());  //获取语句执行时服务器返回的打印消息

stmt.execute("begin dbms_output.disable(); end;");
cstmt.close();
stmt.close();
connection.close();

```

## 4.7 ResultSet

  1. **概述**



ResultSet 提供执行 SQL 语句后从数据库返回结果中获取数据的方法。执行 SQL 语句后数据库返回结果被 JDBC 处理成结果集对象，可以用 ResultSet 对象的 next 方法以行为单位进行浏览，用 getXXX 方法取出当前行的某一列的值。

通过 Statement，PreparedStatement，CallableStatement 三种不同类型的语句进行查询都可以返回 ResultSet 类型的对象。

  2. **行和光标**



ResultSet 维护指向其当前数据行的逻辑光标。每调用一次 next 方法，光标向下移动一行。最初它位于第一行之前，因此第一次调用 next 将把光标置于第一行上，使它成为当前行。随着每次调用 next 导致光标向下移动一行，按照从上至下的次序获取 ResultSet 行。

在 ResultSet 对象或对应的 Statement 对象关闭之前，光标一直保持有效。

  3. **列**



方法 getXXX 提供了获取当前行中某列值的途径。在每一行内，可按任何次序获取列值。

列名或列号可用于标识要从中获取数据的列。例如，如果 ResultSet 对象 rs 的第二列名为“title”，则下列两种方法都可以获取存储在该列中的值：

```
String s = rs.getString("title");
String s = rs.getString(2);
```

如果查询语句的查询项中的列存在别名，则除了可以使用列名和列号外，还可以使用列的别名来获取存储在该列中的值。

如果查询语句的查询项中存在多个表的同名列，则需要使用别名或列号来获取存储在列中的值。如果不存在别名，则只能使用列号来获取。

注意列是从左至右编号的，并且从 1 开始。

关于 ResultSet 中列的信息，可通过调用方法 ResultSet.getMetaData 得到。返回的 ResultSetMetaData 对象将给出其 ResultSet 对象各列的名称、类型和其他属性。

  4. **NULL 结果值**



要确定给定结果值是否是 JDBC NULL，必须先读取该列，然后使用 ResultSet 对象的 wasNull 方法检查该次读取是否返回 JDBC NULL，如下：

```
String sql="select * from person.person";
ResultSet rs;
rs = stmt.executeQuery(sql);
while (rs.next()){
	if(rs.wasNull())
		System.out.println("Get A Null Value");
}
```

当使用 ResultSet 对象的 getXXX 方法读取 JDBC NULL 时，将返回下列值之一：

  1. Java null 值：对于返回 Java 对象的 getXXX 方法（如 getString、getBigDecimal、getBytes、getDate、getTime、getTimestamp、getAsciiStream、getUnicodeStream、getBinaryStream、getObject 等）；
  2. 零值：对于 getByte、getShort、getInt、getLong、getFloat 和 getDouble；
  3. false 值：对于 getBoolean。



  5. **结果集增强特性**



在 DM JDBC 驱动程序中提供了符合 JDBC 2.0 标准的结果集增强特性：可滚动、可更新结果集，及 JDBC3.0 标准的可持有性。

  1. 结果集的可滚动性



通过执行语句而创建的结果集不仅支持向前（从第一行到最后一行）浏览内容，而且还支持向后（从最后一行到第一行）浏览内容的能力。支持这种能力的结果集被称为可滚动的结果集。可滚动的结果集同时也支持相对定位和绝对定位。绝对定位指的是通过指定在结果集中的绝对位置而直接移动到某行的能力，而相对定位则指的是通过指定相对于当前行的位置来移动到某行的能力。DM 支持可滚动的结果集。

DM JDBC 驱动程序中支持只向前滚结果集(ResultSet.TYPE_FORWARD_ONLY)和滚动不敏感结果集(ResultSet.TYPE_SCROLL_INSENSITIVE)两种结果集类型，不支持滚动敏感结果集(ResultSet.TYPE_SCROLL_SENSITIVE)。当结果集为滚动不敏感结果集时，它提供所含基本数据的静态视图，即结果集中各行的成员顺序、列值通常都是固定的。

  2. 结果集的可更新性



DM JDBC 驱动程序中提供了两种结果集并发类型：只读结果集(ResultSet.CONCUR_READ_ONLY)和可更新结果(ResultSet.CONCUR_UPDATABLE)。采用只读并发类型的结果集不允许对其内容进行更新。可更新的结果集支持结果集的更新操作。

  3. 结果集的可持有性



JDBC 3.0 提供了两种结果集可持有类型：

提交关闭结果集(ResultSet.CLOSE_CURSORS_AT_COMMIT)和跨结果集提交(ResultSet.HOLD_CURSORS_OVER_COMMIT)。采用提交关闭结果集类型的结果集在事务提交之后被关闭，而跨结果集提交类型的结果集在事务提交之后仍能保持打开状态。

通过 DatabaseMetaData.supportsResultSetHoldability()方法可以确定驱动程序是否支持结果集的可持有性。

  4. 性能优化



DM JDBC 驱动程序的结果集对象中提供了方法 setFetchDirection 和 setFetchSize 来设置缺省检索结果集的方向和缺省一次从数据库获取的记录条数。它们的含义与用法和语句对象中的同名函数是相同的。

  6. **更新大对象数据**



从 DM JDBC 2.0 驱动程序就支持可更新的结果集，但是对 LOB 对象只能读取，而不能更新，这也是 JDBC 2.0 标准所规定的。而 JDBC 3.0 规范规定用户可以对 LOB 对象进行更新，DM JDBC 3.0 驱动程序中实现了这一点：

假设数据库中存在 BOOKLIST 表，且含有 id、comment 两个字段，建表语句如下：

```
CREATE TABLE BOOKLIST ("ID" INTEGER,"COMMENT" TEXT);
INSERT INTO SYSDBA.BOOKLIST VALUES (1, '测试数据');
Statement stmt = conn.createStatement(
	ResultSet.TYPE_SCROLL_INSENSITIVE,
	ResultSet.CONCUR_UPDATABLE);
	ResultSet rs = stmt.executeQuery("select\"COMMENT\"from booklist " +"where id = 1"
);
rs.next();
Clob commentClob = new Clob(...)；
rs.updateClob("COMMENT", commentClob); // commentClob is a Clob Object
rs.updateRow();
```

  7. **自定义方法列表**



为了实现对达梦数据库所提供的时间间隔类型和带纳秒的时间类型的支持，在实现 ResultSet 接口的过程中，增加了一些自定义的扩展方法。用户将获得的 ResultSet 对象反溯成 DmdbResultSet 类型就可以访问这些方法。这些方法所涉及到的扩展类请参看 4.4 节。

表4.5 自定义方法列表

|**方法名**|**功能说明**|
|----|----|
|getTime(int)|根据列号(1 开始)获取时间信息，以 java.sql.Time 类型返回。|
|getTime(int, Calendar)|根据列号(1 开始)、Calendar 对象获取时间信息， 以 java.sql.Time 类型返回。|
|getTime(String)|根据列名获取时间信息，以 java.sql.Time 类型返回。|
|getTime(String, Calendar)|根据列名、Calendar 对象获取时间信息， 以 java.sql.Time 类型返回。|
|getTimestamp(int)|根据列号(1 开始)获取时间信息，以 java.sql.Timestamp 类型返回。|
|getTimestamp(int, Calendar)|根据列号(1 开始)、Calendar 对象获取时间信息， 以 java.sql.Timestamp 类型返回。|
|getTimestamp(String)|根据列名获取时间信息，以 java.sql.Timestamp 类型返回。|
|getTimestamp(String, Calendar)|根据列名、Calendar 对象获取时间信息， 以 java.sql.Timestamp 类型返回。|
|updateINTERVALYM(int, DmdbIntervalYM )|设置列为年-月时间间隔类型值|
|updateINTERVALYM(String, DmdbIntervalYM)|设置列为年-月时间间隔类型值|
|updateINTERVALDT(int, DmdbIntervalDT)|设置列为日-时时间间隔类型值|
|updateINTERVALDT(String, DmdbIntervalDT)|设置列为日-时时间间隔类型值|


8. sql 结果集缓存

在 DM JDBC 驱动程序中增加了 SQL 结果集缓存的功能。

  1. 功能描述



本功能针对部分满足条件的 SELECT 类型 SQL，在执行该 SQL 后一次性从服务器获取所有结果集并缓存在本地，并返回具体所有结果集数据的 DmdbCachedResultSet 对象。若在缓存有效时间内多次执行该 SQL，则第 2 次及以后的查询都直接从缓存中获取结果集而不是从服务器查询，从而提高查询效率。本功能是驱动级别的，配置后对所有 CONNECTION 及相关的 STATEMENT 全局生效。

结果集缓存有效性受两方面影响。一方面，缓存的结果集保存了缓存时间戳，驱动设置了缓存最大有效时间 rsCacheTimeout，若查询到缓存存在时间已经超过最大有效时间，则该缓存过期；另一方面，缓存的 SQL 数量没有限制，但当 SQL 需要进行参数绑定时，对其参数绑定的值的组合情况数量进行限制，限制参数为 rsCacheLimit。如执行”select * from t1 where c1 = ? and c2 = ?”时，假设该 sql 被配置为可缓存 sql 且参数都为 INT 类型，则可保存类似{c1=4,c2=3}、{c1=2,c2=7}等不同参数配置的缓存结果，这些结果的总数最大为 rsCacheLimit。

当结果集缓存中查询不到对应的结果集，或对应结果集已过期时，此时仍会一次性从服务器获取所有结果集并缓存在本地。若缓存数已达到最大限制，则淘汰最早的一个缓存结果集。

  2. 使用方法



本功能通过配置连接串中参数生效，可选参数如下：rsCacheEnable、rsCacheTimeout、rsCacheTimeoutUnit、rsCacheLimit、rsCacheFilePath。

指定待缓存的 SQL 语句，SQL 类型必须为 SELECT 语句。指定的方式有两种：一 在 rsCacheFilePath 文件中指定；二在 SQL 语句中通过 HINT 指定，HINT 为 “/jdbcrscache/”。二中方式任选一种即可。

  3. 示例



使用 SQL 结果集缓存功能。例子中的 sql.txt 包含了准备缓存的 SQL 语句。例如：包含 select * from sysobjects；

```
public static void testRsCache() throws SQLException
    {
        String url = "jdbc:dm://localhost:5239?rsCacheEnable=true&rsCacheTimeout=20&rsCacheTimeoutUnit=0&rsCacheLimit=1000&rsCacheFilePath=C:\\Users\\dameng\\Desktop\\sql.txt";
        String username = "SYSDBA";
        String password = "sysDBA*00";
        DmdbConnection connection = (DmdbConnection)DriverManager.getConnection(url, username, password);

        // statement
        String sql = "select /*jdbcrscache*/ * from t1";
    Statement statement = connection.createStatement();
        ResultSet resultSet = statement.executeQuery(sql);
        if (resultSet instanceof DmdbCachedResultSet)
        {
            while (resultSet.next())
            {
                int i = resultSet.getInt(1);
                System.out.println(i);
            }
        }
        statement.close();
        connection.close();
    }
```

  4. 注意事项



1.本功能以过滤链的方式实现，若进入结果集缓存逻辑，则无法进入主备分发逻辑。因此对于主备集群，缓存 SQL 的查询都只在主节点进行。

2.使用 DmdbPreparedStatement 执行 SQL 时，若服务器开启了常量参数化处理（prepareOptimize）开关，则将导致 SQL 无法与文件指定 SQL 匹配上，若 SQL 中不包含 HINT，则将导致缓存失败。

3.本功能未对 SQL 类型进行检查，需用户自己保证进行结果集缓存的 SQL 类型为 SELECT，若为其他类型，可能出现未知错误。

## 4.8 流与大对象

### 4.8.1 Stream 使用

为了获取大数据量的列，DM JDBC 驱动程序提供了四个获取流的方法：

  1. getBinaryStream 返回只提供数据库原字节而不进行任何转换的流；
  2. getAsciiStream 返回提供单字节 ASCII 字符的流；
  3. getUnicodeStream 返回提供双字节 Unicode 字符的流；
  4. getCharacterStream 返回提供双字节 Unicode 字符的 java.io.Reader 流。



在这四个函数中，JDBC 规范不推荐使用 getUnicodeStream 方法，其功能可以用 getCharacterStream 代替。以下是采用其它三种方法获取流的示例代码：

  1. **采用 getBinaryStream 获取流**



```
// 查询语句
String sql = "SELECT description FROM production.product WHERE productid=1";
// 创建语句对象
Statement stmt = conn.createStatement();
// 执行查询
ResultSet rs = stmt.executeQuery(sql);
// 显示结果集
while (rs.next()) {
	try {
		InputStream stream = rs.getBinaryStream("description");
		ByteArrayOutputStream baos = new ByteArrayOutputStream();
		int num = -1;
		while ((num = stream.read()) != -1) {
			baos.write(num);
		}
		System.out.println(baos.toString());
	} catch (IOException e) {
		e.printStackTrace();
	}
}
// 关闭结果集
rs.close();
// 关闭语句
stmt.close();
```

  2. **采用 getAsciiStream 获取流**



```
// 查询语句
String sql = "SELECT description FROM production.product WHERE productid=1";
// 创建语句对象
Statement stmt = conn.createStatement();
// 执行查询
ResultSet rs = stmt.executeQuery(sql);
// 显示结果集
while (rs.next()) {
	try {
		InputStream stream = rs.getAsciiStream("description");
		StringBuffer desc = new StringBuffer();
		byte[] b = new byte[1024];
		for (int n; (n = stream.read(b)) != -1;) {
			desc.append(new String(b, 0, n));
		}
		System.out.println(desc.toString());
	} catch (IOException e) {
		e.printStackTrace();
	}
}
// 关闭结果集
rs.close();
// 关闭语句
stmt.close();
```

  3. **采用 getCharacterStream 获取流**



```
// 查询语句
String sql = "SELECT description FROM production.product WHERE productid=1";
// 创建语句对象
Statement stmt = conn.createStatement();
// 执行查询
ResultSet rs = stmt.executeQuery(sql);
// 显示结果集
while (rs.next()) {
	try {
		Reader reader = rs.getCharacterStream("description");
		BufferedReader br = new BufferedReader(reader);
		String thisLine;
		while ((thisLine = br.readLine()) != null) {
			System.out.println(thisLine);
		}
		 
	} catch (IOException e) {
		e.printStackTrace();
	}
}
// 关闭结果集
rs.close();
// 关闭语句
stmt.close();
```

### 4.8.2 LOB 对象使用

  1. **概述**



JDBC 标准为了增强对大数据对象的操作，在 JDBC3.0 标准中增加了 java.sql.Blob 和 java.sql.Clob 这两个接口。这两个接口定义了许多操作大对象的方法，通过这些方法就可以对大对象的内容进行操作。

  2. **产生 Lob 对象**



在 ResultSet 和 CallableStatement 对象中调用 getBlob()和 getClob()方法就可以获得 Blob 对象和 Clob 对象：

```
Blob blob = rs.getBlob(1);
Clob clob = rs.getClob(2);
```

  3. **设置 Lob 对象**



Lob 对象可以像普通数据类型一样作为参数来进行参数赋值，在操作 PreparedStatement、CallableStatement、ResultSet 对象时使用：

```
PreparedStatement pstmt = conn.prepareStatement("INSERT INTO bio (image, text) " + "VALUES (?, ?)");
//authorImage is a Blob Object
pstmt.setBlob(1, authorImage);
//authorBio is a Clob Object
pstmt.setClob(2, authorBio);
```

在一个可更新的结果集中，也可以利用 updateBlob(int,Blob)、updateBlob(String,Blob)和 updateClob(int,Clob)、updateClob(String,Clob)来更新当前行。

  4. **改变 Lob 对象的内容**



Lob 接口提供了方法让用户可以对 Lob 对象的内容进行任意修改：

```
byte[] val = {0,1,2,3,4};
Blob data = rs.getBlob("DATA");
int numWritten = data.setBytes(1, val); // 在指定的位置插入数据
PreparedStatement ps = conn.prepareStatement("UPDATE datatab SET data = ?");
ps.setBlob("DATA", data);
ps.executeUpdate();
```

目前 LOB 内容的更新和其当前所处的事务之间没有直接的联系。更新 LOB 时，会直接写入到数据库中，即便当前事务最终回滚也不能恢复。

## 4.9 元数据

### 4.9.1 ResultSetMetaData

  1. **概述**



ResultSetMetaData 提供许多方法，用于读取 ResultSet 对象返回数据的元信息。包括：列名、列数据类型、列所属的表、以及列是否允许为 NULL 值等，通过这些方法可以确定结果集中列的一些信息。

  2. **创建结果集元数据对象**



结果集元数据是用来描述结果集的特征，所以，需要首先执行查询获得结果集，才能创建结果集元数据对象。

  3. **创建 ResultSetMetaData 对象如下例所示：**



假如有一个表 TESTTABLE(no int,name varchar(10))，利用下面的代码就可以知道这个表的各个列的类型：

```
ResultSet rs = stmt.executeQuery("SELECT * FROM TESTTABLE");
ResultSetMetaData rsmd = rs.getMetaData();
for(int i = 1; i <= rsmd.getColumnCount(); i ++)
{
   String typeName = rsmd.getColumnTypeName(i);
   System.out.println("第" + i + "列的类型为：" + typeName);
}
```

### 4.9.2 DatabaseMetaData

  1. **概述**



DatabaseMetaData 提供了许多方法，用于获取数据库的元数据信息。包括：描述数据库特征的信息(如是否支持多个结果集)、目录信息、模式信息、表信息、表权限信息、表列信息、存储过程信息等。DatabaseMetaData 有部分方法以 ResultSet 对象的形式返回结果，可以用 ResultSet 对象的 getXXX()方法获取所需的数据。

  2. **创建数据库元数据对象**



数据库元数据对象由连接对象创建。以下代码创建 DatabaseMetaData 对象（其中 con 为连接对象）：

```
DatabaseMetaData dbmd = con.getMetaData();
```

利用该数据库元数据对象就可以获得一些有关数据库和 JDBC 驱动程序的信息：

```
// 数据库产品的名称
String databaseName = dbmd.getDatabaseProductName();
// JDBC驱动程序的主版本号，如3表示JDBC3.0
int majorVersion = dbmd.getJDBCMajorVersion();
// DM提供的JDBC驱动程序版本号，如8.1.2.174
String driverVersion = dbmd.getDriverVersion();
// 获取表信息
String[] types ={"TABLE"};
ResultSet tablesInfor = dbmd.getTables(null,  null,  "TE%",  types);
```

### 4.9.3 ParameterMetaData

  1. **概述**



参数元数据是 JDBC 3.0 标准新引入的接口，它主要是对 PreparedStatement、CallableStatement 对象中的占位符（“?”）参数进行描述，例如参数的个数、参数的类型、参数的精度等信息，类似于 ResultSetMetaData 接口。通过引入这个接口，就可以对参数进行较为详细、准确的操作。

  2. **创建参数元数据对象**



通过调用 PreparedStatement 或 CallableStatement 对象的 getParameterMetaData()方法就可以获得该预编译对象的 ParameterMetaData 对象：

```
ParameterMetaData pmd = pstmt.getParameterMetaData();
```

然后就可以利用这个对象来获得一些有关参数描述的信息：

```
// 获取参数个数
int paraCount = pmd.getParameterCount();
for(int i = 1; i <= paraCount; i ++)	{
// 获取参数类型
System.out.println("The Type of Parameter("+i+") is " + pmd.getParameterType(i));
// 获取参数类型名
System.out.println("The Type Name of Parameter("+i+") is " 
+ pmd.getParameterTypeName(i));
// 获取参数精度
System.out.println("The Precision of Parameter("+i+") is " + pmd.getPrecision(i));
// 获取参数是否为空
System.out.println("Parameter("+i+") is nullable? " + pmd.isNullable (i));
}
```

## 4.10 RowSet

RowSet 接口扩展了标准 java.sql.ResultSet 接口。RowSetMetaData 接口扩展了 java.sql.ResultSetMetaData 接口。JDK 5.0 定义了 5 个标准的 JDBCRowSet 接口，DM 实现了其中的 CachedRowSet 和 JdbcRowSet。

RowSet 对象可以建立一个与数据源的连接并在其整个生命周期中维持该连接，在此情况下，该对象被称为连接的 RowSet。RowSet 还可以建立一个与数据源的连接，从其获取数据，然后关闭它。这种 RowSet 被称为非连接 RowSet。非连接 RowSet 可以在断开时更改其数据，然后将这些更改发送回原始数据源，不过它必须重新建立连接才能完成此操作。相比较 java.sql.ResultSet 而言，RowSet 的离线操作能够有效的利用计算机越来越充足的内存，减轻数据库服务器的负担，由于数据操作都是在内存中进行然后批量提交到数据源，灵活性和性能都有了很大的提高。RowSet 默认是一个可滚动，可更新，可序列化的结果集，而且它作为 JavaBeans，可以方便地在网络间传输，用于两端的数据同步。

DM 合并了 CachedRowSet 和 JdbcRowSet 的代码实现到 DmdbRowSet 类中，用 boolean 类型成员变量 m_KeepConnecting 来区分两者的不同，true 表示当前对象为保持连接的 JdbcRowSet；false 表示当前对象为不保持连接的 CachedRowSet。然而 m_KeepConnecting 不能进行手动设置，只会在调用某些方法时被赋值为 true。在创建 DmdbRowSet 对象时，m_KeepConnecting 默认为 false，若想使用保持连接的 JdbcRowSet 对象，则需要使用带参数的 execute(Connection conn)和 acceptChanges(Connection con)方法。

### 4.10.1 CachedRowSet

CachedRowSet 是非连接的 RowSet，数据行均被缓存至本地内存，但并未保持与数据库服务器的连接。Dm8JDBC 中通过 DmdbRowSet 类实现，由于 m_KeepConnecting 初始值为 false，故新建的对象就是 CachedRowSet 对象。

1．使用 URL、用户名、密码和一个查询 SQL 语句作为设置属性，创建一个 DmdbRowSet 对象。RowSet 使用 execute 方法完成 DmdbRowSet 对象的填充。完成 execute 方法执行后，可以像使用 java.sql.ResultSet 对象方法一样，使用 RowSet 对象返回、滚动、插入、删除或更新数据。

```
//创建DmdbCachedRowSet对象示例 
String sql = "SELECT productid,name,author FROM production.product";
DmdbRowSet crs = new DmdbRowSet();
crs.setUrl("jdbc:dm://localhost:5236");
crs.setUsername("SYSDBA");
crs.setPassword("sysDBA*00");
crs.setCommand(sql);
crs.execute();
while (crs.next())
{
	System.out.println("productid: " + crs.getInt(1));
	System.out.println("name: " + crs.getString(2));
	System.out.println("author: " + crs.getString(3));
}
```

2．CachedRowSet 对象也可以通过调用 populate 方法，使用一个已经存在的 ResultSet 对象填充。完成填充后，便可以像操作 ResultSet 对象一样，返回、滚动、插入、删除或更新数据。

```
//使用populate方法填充代码片段
// 执行查询，获取ResultSet对象
String sql = "SELECT productid,name,author FROM production.product";
ResultSet rs = stmt.executeQuery(sql);

// 填充CachedRowSet
DmdbRowSet crs = new DmdbRowSet();
crs.populate(rs);
```

3．其他功能特点

创建一个 CachedRowSet 的拷贝：

```
CachedRowSet copy = crs.createCopy();
```

创建一个 CachedRowSet 的共享：

```
RowSet shard = crs.createShared();
```

4．CachedRowSet 限制

  * 仅支持单表查询，且无连接操作；
  * 因数据缓存在内存，故不支持大数据页；
  * 连接属性，如事务隔离级等，不能在填充（执行 execute 或 populate）后设置，因为此时已经断开了与数据库服务器的连接，不能将这些属性设置到返回数据的同一个连接上。



### 4.10.2 JdbcRowSet

JdbcRowSet 是连接的 RowSet。Dm8JDBC 中通过 DmdbRowSet 类实现，与 CachedRowSet 不同的是，JdbcRowSet 对象需要调用 execute(Connection conn)和 acceptChanges(Connection con)方法实现相关功能。

JdbcRowSet 在其生命期中始终保持着与数据库服务器的连接。其所有调用操作，均渗透进对 JDBC Connection、statement 和 ResultSet 调用。而 CachedRowSet 则不存在于打开数据库服务器的任何连接。

CachedRowSet 在其操作过程中不需要 JDBC 驱动的存在，而 JdbcRowSet 需要。但两者在填充 RowSet 和提交数据修改的过程中，均需 JDBC 驱动的存在。

```
//使用JdbcRowSet接口示例 
String sql = "SELECT name,author,publisher FROM production.product";
DmdbRowSet jrs = new DmdbRowSet();
jrs.setUrl("jdbc:dm://localhost:5236");
jrs.setUsername("SYSDBA");
jrs.setPassword("sysDBA*00");
jrs.setCommand(sql);
jrs.execute(jrs.getConnection());
int numcolsSum = jrs.getMetaData().getColumnCount();
while (jrs.next()) {
	for (int i = 1; i <= numcolsSum; i++) {
		System.out.print(jrs.getString(i) + "\t");
	}
	System.out.println();
}
jrs.close();
```

## 4.11 分布式事务支持

DM 对分布式事务的支持是依据 X/OPEN 分布式事务处理模型 XA 规范实现的。

DM 数据库系统实现了 X/OPEN DTP 模型中的 RM 组件，通过 JDBC 接口与第三方 TM 工具配合完成分布式事务处理。JDBC 标准中，对 XA 协议进行了部分剪裁，仅支持 TM 对 RM 的单向调用，不允许 RM 向 TM 的动态注册。因此 DM 系统对 XA 协议的支持也仅为单向调用。

一个会话同时只能处理一个分支事务，一个分支事务同时也只能被一个会话绑定处理。

DM JDBC 支持 XA 标准接口，包括：

```
javax.sql.XADataSource
javax.sql.XAConnection
javax.transaction.xa. XAResource
javax.transaction.xa.Xid
```

### 4.11.1 XADataSource

1. 概述

XADataSource 是分布式连接的数据源，通过其可以获取分布式连接。

2. 获取分布式连接

XADataSource 提供了两个方法用于获取分布式连接：

```
public XAConnection getXAConnection() throws SQLException
public XAConnection getXAConnection(String user, String password) throws
SQLException
```

第一个方法使用时无需设置用户名和密码。需先使用 setUser 和 setPassword 给数据源设置好用户名和口令；然后再使用 getXAConnection 直接进行连接，并返回得到的分布式连接。

第二个方法使用时需设置用户名和口令。getXAConnection 使用参数中设置的用户名和口令进行连接，并返回得到的分布式连接。

3.对象创建

XADataSource 继承自 DataSource，创建时可通过对象上的 set 相关接口指定连接的必要属性。示例如下：

```
DmdbXADataSource xads = new DmdbXADataSource();
xads.setURL("jdbc:dm://localhost:5236");
xads.setUser("SYSDBA");
xads.setPassword("sysDBA*00");
```

### 4.11.2 XAConnection

1. 概述

XAConnection 是对分布式事务进行支持的对象。XAConnection 对象通常是以 XAResource 对象的方式被纳入到分布式事务的管理中。

2. 获取 XAResource 对象

XAConneciton 提供了 javax.transaction.xa.XAResource getXAResource() throws SQLException 方法用于获取该分布式连接对应的 XAResource 对象，这个 XAResource 对象将代表 XAConnection 对象参加分布式事务的管理。

### 4.11.3 XAResource

1. 概述

XAResource 对象是代表 XAConnection 对象参与分布式事务管理的，真正对分布式事务进行操控的方法都在这个接口中实现。

2. 主要方法介绍

  1. public void start(Xid xid, int flags) throws XAException



该方法用于开始一个事务分支。第一个参数 xid 用于指定事务分支的 id，第二个参数 flags 用于指明开始这个事务分支的方式，其合法值及意义如下：

TMJOIN：这个标志指名该事务分支将被合并到之前的具有相同 xid 的事务分支上，如果资源管理器发现之前没有这个 xid 的事务分支，则抛出异常。

TMRESUME：这个标志指明该事务分支将重新开始之前用 TMSUSPEND 标志结束的一个事务分支。

TMNOFLAGS：当没有特殊操作而只是开始一个普通的事务分支时，将 flags 置为 TMNOFLAGS。

  2. public void end(Xid xid, int flags) throws XAException



该方法用于结束一个事务分支。第一个参数 xid 用于指定事务分支的 id，第二个参数 flags 用于指明结束这个事务分支的方式，其合法值及意义如下：

TMSUSPEND：这个标志指明暂时终止该事务分支，之后该事务分支应该被用 TMRESUME 重新开始或者用 TMSUCCESS 或 TMFAIL 正式结束。

TMSUCCESS：这个标志指明该事务分支成功结束。

TMFAIL：这个标志指明这个事务已经失败，则这个事务分支被资源管理器标记为只能回滚。

  3. public int prepare(Xid xid) throws XAException



该方法用来预提交一个事务分支。参数 xid 用于指定事务分支的 id。

可能的返回值有：

XA_OK：该标志表明预提交成功。

XA_RDONLY：该标志表明事务分支具有只读属性并已被提交。

错误码：预提交失败并返回具体的错误码对应不同的失败信息。

  4. public void commit(Xid xid, boolean onePhase) throws XAException



该方法用来提交一个事务分支。第一个参数 xid 用于指定事务分支的 id。第二个参数 onePhase 用来指定是否进行一阶段提交。

  5. public void rollback(Xid xid) throws XAException



该方法用来回滚一个事务分支。参数 xid 用于指定事务分支的 id。

  6. public boolean isSameRM(XAResource xares) throws XAException



该方法用来判断指定的 xares 与当前 XAResource 对象是否是指向同一数据源的两个实例。

  7. public Xid[] recover(int flag) throws XAException



该方法用于返回资源管理器中所有已被预提交的事务分支的 id。

flags 的合法值及意义为：

TMSTARTRSCAN | TMENDRSCAN：一次获取到所有的已预提交的事务分支的 id。

TMSTARTRSCAN：开始扫描并返回已预提交的事务分支的 id，如果资源管理器中的已预提交事务分支过多，可能返回的不是全部。

TMNOFLAGS：用于继续扫描已预提交的事务分支的 id。使用这一标志时，之前应该已经用 TMSTARTRSCAN 开始了扫描。

TMENDRSCAN：这个标志表明在此次扫描并返回对应的已预提交的事务分支的 id 后，结束此次扫描。

  8. public void forget(Xid xid) throws XAException



该方法用来“遗忘”一个自主完成的事务分支，这里的“遗忘”表示真正释放事务分支，之前即使一个事务分支已经自主完成，其事务分支对象仍然保留。对于非自主完成的事务分支使用此方法时将返回异常。

### 4.11.4 Xid

1. 概述

Xid 接口是 X/Open 事务标识符 XID 结构的 Java 映射，由全局事务格式 ID、全局事务 ID 和分支限定符构成。

2. 使用介绍

DmdbXid 提供了构造函数 public DmdbXid(int fmtId, byte[] gTranId, byte[] bQual) throws XAException 供用户生成一个 Xid 实例。

同时 DmdbXid 也实现了 Xid 标准接口中的三个用于获取 XID 的格式标识符部分、XID 的全局事务标识符部分作为字节数组和 XID 的事务分支标识符部分作为字节数组的三个方法。

### 4.11.5 实例解析

以下是一个使用 JDBC 的 XA 接口用两阶段提交协议来提交一个事务分支的例子。

```
DmdbXADataSource xaDS; 
XAConnection xaCon; 
XAResource xaRes; 
Xid xid; 
Connection con; 
Statement stmt; 
int ret; 

//设置连接串
DmdbXADataSource xaDS = new DmdbXADataSource(); 
xaDS.setURL("jdbc:dm://localhost:5236");

//获取分布式连接
xaCon = xaDS.getXAConnection("jdbc_user", "jdbc_password"); 

//获取代表分布式连接的XAResource实例
xaRes = xaCon.getXAResource(); 
con = xaCon.getConnection(); 
stmt = con.createStatement(); 

DmdbXid xid = new DmdbXid(100, new byte[] { 0x01 }, new byte[] { 0x02 });  
try { 
//开始一个事务分支
　　xaRes.start(xid, XAResource.TMNOFLAGS); 
　　stmt.executeUpdate("insert into test_table values (100)"); 
//结束一个事务分支
　　xaRes.end(xid, XAResource.TMSUCCESS); 
//预提交该事务分支
　　ret = xaRes.prepare(xid); 
if (ret == XAResource.XA_OK) { 
	//提交事务分支
　　　　xaRes.commit(xid, false); 
　　　} 
} 
catch (XAException e) { 
　e.printStackTrace(); 
} 
finally { 
　stmt.close(); 
　con.close(); 
　xaCon.close(); 
}
```

## 4.12 如何使用 JDBC 接口操作空间数据

空间数据主要用来表示物体的位置、形态、大小和分布等信息，是对具有定位意义的物体和现象的定量描述。

DM 可从两方面来对空间数据进行操作：一通过 DMGEO 系统包；二通过 JDBC 接口；三通过 DmProvider 接口。DMGEO 系统包的用法请参考《DM8 系统包使用手册》中 DMGEO 包，本节重点介绍如何通过 JDBC 接口操作空间数据。

### 4.12.1 空间数据类型

DMGEO 系统包中的空间数据类型 ST_GEOMETRY，在 DM 数据库中本质为 struct 类型，类型的读写按标准 struct 类型处理即可。空间数据类型的结构体中包含三个成员：srid、geo_wkb 和 geo_typeid。三个成员的更多详情介绍请参考《DM8 系统包使用手册》中 DMGEO 包。下面简要介绍一下三个成员的用途：

● srid 空间参考坐标系 ID，数据类型 INT。

● geo_wkb 二进制格式的几何体信息，数据类型 Blob。wkb 字段为标准 wkb 格式，可以使用通用 wkb 解析工具进行解析处理。jts 是 java 处理空间数据类型的通用组件，使用可参考 https://github.com/locationtech/jts。

● geo_typeid 几何体类型 ID，数据类型 INT。几何类型 ID 如下：

```
GEOS_GEOMETRY         -1;

GEOS_POINT         0;

GEOS_LINESTRING       1;

GEOS_LINEARRING       2;

GEOS_POLYGON        3;

GEOS_MULTIPOINT       4;

GEOS_MULTILINESTRING    5;

GEOS_MULTIPOLYGON      6;

GEOS_GEOMETRYCOLLECTION   7;  
```

### 4.12.2 空间数据的读写步骤

如何使用 JDBC 接口操作空间数据，具体分为以下几个步骤：

一 先使用标准 JDBC 功能 getObject 从数据库中检索对象 java.sql.Struct.该 getObject 返回 java.lang.Object,因此必须将方法的输出强转为 struct。

示例如下：

```
ResultSet rs = stmt.executeQuery("select sub_mpolygon from sub_types;");

java.sql.Struct struct = (jav.sql.Struct)rs.getObject(1);  
```

二 从 java.sql.Struct 对象中获取成员属性使用 getAttributes 方法。

示例如下：

```
Object[] attrs = struct.getAttributes(); //获取结构体中的成员  

int srid = (Integer)attrs[0]; // srid  

Blob blob = (Blob)attrs[1]; // wkb  

int type = (Integer)attrs[2]; //type   
```

三 使用 jts 读取 wkb 解析为 org.locationtech.jts.geom.Geometry。

示例如下：

```
WKBReader reader = new WKBReader(); 

Geometry geometry = reader.read(blob.getBytes(1, (int)blob.length()));   
```

四 将 org.locationtech.jts.geom.Geometry 对象转换为 wkb 数据。

示例如下：

```
WKBWriter writer = new WKBWriter();  

byte[] bytes = writer.write(geometry);   
```

五 封装空间数据类型为 java.sql.Struct 对象。

示例如下：

```
Object[] attrs = new Object[3];  

attrs[0] = srid;

attrs[1] = DmdbBlob.newInstanceOfLocal(bytes, (DmdbConnection)conn);  

attrs[2] = 6;

struct = conn.createStruct("ST_MULTIPOLYGON", attrs);  //指定类型名称和结构体成员创建  
```

六 将 java.sql.Struct 对象绑定到语句，使用标准的 setObject 方法。

示例如下：

```
PreparedStatement pstmt = conn.prepareStatement("insert into sub_types(sub_mpolygon) values(?)");

pstmt.setObject(1, struct);  
```

### 4.12.3 基本示例

下面用一个完整的示例，展示如何使用 JDBC 接口操作空间数据。

```
/*  

  * geometry simple   

  * create table sub_types (sub_mpolygon st_multipolygon );  

  * insert into sub_types values (dmgeo.st_mpolyfromtext ('multipolygon(((10 10, 10 20, 20 20, 20 15, 10 10)), ((50 40, 50 50, 60 50, 60 40, 50 40)))' , 4269));  

*/  

@SuppressWarnings ("unused")  

public static void test_spatial() throws SQLException, org.locationtech.jts.io.ParseException  

{  

   Connection conn = getConnection();  

   Statement stmt = conn.createStatement();  

   ResultSet rs = stmt.executeQuery("select sub_mpolygon from sub_types;");  

   while (rs.next())  

   {  

// from struct to geometry  

      Struct struct = (Struct)rs.getObject(1);  

      Object[] attrs = struct.getAttributes(); //获取结构体中的成员  

      int srid = (Integer)attrs[0]; // srid  

      Blob blob = (Blob)attrs[1]; // wkb  

      int type = (Integer)attrs[2]; //type  

  

      WKBReader reader = new WKBReader();  

      Geometry geometry = reader.read(blob.getBytes(1, (int)blob.length()));  

      System.out.println(geometry);  

//from geometry to struct, the struct object can used for insert by pstmt.setObject(struct)  

      WKBWriter writer = new WKBWriter();  

      attrs[1] = DmdbBlob.newInstanceOfLocal(writer.write(geometry), (DmdbConnection)conn);  

      struct = conn.createStruct("ST_MULTIPOLYGON", attrs);  //指定类型名称和结构体成员创建

      PreparedStatement pstmt = conn.prepareStatement("insert into sub_types(sub_mpolygon) values(?)");

      pstmt.setObject(1, struct);

      pstmt.execute();

   }  

   rs.close();  

   stmt.close();  

   conn.close();  

}

```

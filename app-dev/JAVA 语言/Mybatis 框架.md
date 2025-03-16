

# Mybatis 框架

## 一、前言

MyBatis 是一款优秀的持久层框架，它支持自定义 SQL、存储过程以及高级映射。MyBatis 免除了几乎所有的 JDBC 代码以及设置参数和获取结果集的工作。MyBatis 可以通过简单的 XML 或注解来配置和映射原始类型、接口和 Java POJO（Plain Old Java Objects，普通老式 Java 对象）为数据库中的记录。

本文将介绍如何配置 MyBatis 连接达梦数据库，对数据进行基本的增删改查及大字段对象的基本操作。本文需要有一定的 Java 开发基础，相关工具使用和术语不详细讲解，请自行学习。关于 MyBatis 详细使用也不做过多讲解，请自行学习。

## 二、环境信息

|**项目**|**信息**|
|--|--|
|系统环境|Windows 11 专业版|
|JDK 版本|Java(TM) SE Runtime Environment (build 1.8.0_241-b07)|
|开发环境|Eclipse(20180405)|
|数据库信息|DM8.0（本地安装实例，端口为 8270）|
|JDBC 驱动版本|DmJdbcDriver18.jar(这里 18 表示 JDK1.8 环境)|
|MyBatis 版本|mybatis-3.4.4.jar|


## 三、环境搭建

环境搭建的相关 jar 和程序文件见工程压缩包：[JAVA_Mybatis.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202303031444080OVWPJT28YS7PQAPOY)。

附件目录说明：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302160350V0XS57C7MVVHH2AR5Z)

  * JAVA_Mybatis：工程目录
  * lib：相关 jar 包
  * lob_files：大字段对象文件



### 3.1 数据对象准备

数据库软件安装、实例的创建本文不做详细介绍，请查阅达梦在线服务平台相关文章。需要注意的是实例初始化时大小写敏感参数设置，快速设置使得在 mybatis 下对象不用添加双引号。

说明：

  1. 设置大小写不敏感，此方法会影响查询结果集，因为其对结果集匹配也不区分大小写，需要根据业务自行评估是否可行。
  2. 在 mysql 数据库中，对象名默认是小写，达梦对象名默认是大写，在用达梦 DTS 工具迁移的时候，去掉”保持对象名大小写”的勾选，使对象名自动转换成大写，在 mybatis 查询的时候，即不需要加双引号强调小写。



### 3.2 创建数据表空间和用户模式

在进行应用开发时建议创建单独的用户和表空间，为保障数据安全，禁止使用管理员用户 SYSDBA 作为应用系统的数据库用户。

```
--创建用户表空间
CREATE TABLESPACE PRODUCTION DATAFILE 'PRODUCTION.DBF' SIZE 128 ;
--创建用户
CREATE USER PRODUCTION IDENTIFIED BY "******";
--设置用户默认表空间
ALTER USER PRODUCTION DEFAULT TABLESPACE PRODUCTION DEFAULT INDEX TABLESPACE PRODUCTION;
--给用户给予必要权限
GRANT RESOURCE,VTI,SOI TO PRODUCTION;
```

### 3.3 创建表对象和初始数据

```
--创建表普通对象
CREATE TABLE PRODUCTION.PRODUCT_CATEGORY
(PRODUCT_CATEGORYID INT,
NAME VARCHAR(100));
--插入测试数据
insert into PRODUCTION.PRODUCT_CATEGORY(PRODUCT_CATEGORYID,NAME)
values(1,'小说'),(2,'国学'),(3,'魔幻'),(4,'语文'),(5,'体育'),(7,'金融');
--插入后数据库查询的数据如下：
select * from PRODUCTION.PRODUCT_CATEGORY;
--查询结果如下图
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202303021609006WHTC1VJUPI2XUO8XF)

```
--创建带有大字段的表对象
CREATE TABLE PRODUCTION.BIG_DATA
(
ID INT IDENTITY(1, 1) NOT NULL,
PHOTO IMAGE,
DESCRIBE BLOB,
TXT CLOB,
NOT CLUSTER PRIMARY KEY(ID)) ;
--大字段对象数据见附件压缩包的lob_files目录中“mybatis_bigdata_test.txt”和“mybatis_bigdata_test.jpg”，解压到D盘根目录下，或者自行修改大字段测试类Test_TestBigData.java中filePath路径，执行测试时先插入数据对象到数据库，再读取数据库中数据对象并输入到对应路径下。建议先存放到D盘进行测试验证。
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302160947Q43E4XS3GURHFVE6B9)

### 3.4 MyBatis 框架的搭建

若希望快速运行用例请使用开发工具直接导入附件中工程执行测试类进行验证，可跳过环境搭建步骤。

1. 新建项目

新建一个项目 JAVA_Mybatis。完整的项目结构如下图：

![](https://eco.dameng.com/eco-file-server/file/eco/preview/202303021652284FIELV64LU5Z6ZRHQ6)

2. 导入 jar 包

导 mybatis 和驱动等 jar 包，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302161404OCEL33YD3T3HL8P1TR)

> **注意**
>
> 复制 jar 包到 lib 文件夹后，要选择所有 jar 包，点击鼠标右键，选择 Build path-->Add to Build path。



3. 创建相关包结构

在 src 源码目录下创建如下包名，完整项目目录结构如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302161614DVT5LN4R5ZE85WTBUW)

4. 配置 Mybatis 主配置文件

编写 Mybatis 的主配置文件：mybatis-config.xml，存放在 src 目录下。文件如下所示：

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!-- <!– 引入 db.properties 文件 –> -->
    <properties resource="jdbc.properties"></properties>
    <!-- 配置 mybatis 运行环境-->
    <environments default="development">
        <environment id="development">
            <!-- 配置事务管理，采用 JDBC 的事务管理 -->
            <transactionManager type="JDBC" />
            <!-- POOLED:mybatis 自带的数据源，JNDI：基于 Tomcat 的数据源 -->
            <!--使用 DB.properties-->
            <dataSource type="POOLED">
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 将 mapper 文件加入到配置文件中 mapper 文件是写 SQL 语句的文件 -->
    <mappers>
        <!--普通数据的增删改查/-->
        <mapper resource="dameng/dao/ProductCategoryMapper.xml" />
        <!--大字段的操作/-->
        <mapper resource="dameng/dao/TestBigDataMapper.xml" />
    </mappers>
</configuration>
```

5. 配置 JDBC 连接文件

编写 JDBC 的连接数据库配置信息文件 jdbc.properties ，(主要配置 driver、url、username、password 等)。根据实际数据库环境信息修改数据库连接信息。文件如下所示：

```
jdbc.driver=dm.jdbc.driver.DmDriver
jdbc.url=jdbc:dm://localhost:8270
jdbc.username=PRODUCTION
jdbc.password=******
```

## 四、开发示例

请完成以上准备步骤后再进行下面操作。

### 4.1 基础绑定参数示例

基本操作使用的数据表为示例库中 PRODUCTION 用户模式的 PRODUCT_CATEGORY 表。相关实体类和配置文件如下。对应于数据库表添加实体类 ProductCategory，其位置放于包 dameng.pojo 下。

1. 操作数据库对应的实体类

对应于数据库表添加实体类 ProductCategory，其位置放于包 dameng.pojo 下。ProductCategory.java 文件如下所示：

```
package dameng.pojo;
public class ProductCategory {
	private Integer PRODUCT_CATEGORYID;
	private String NAME;
	public int getPRODUCT_CATEGORYID() {
		return PRODUCT_CATEGORYID;
	}
	public void setPRODUCT_CATEGORYID(Integer PRODUCT_CATEGORYID) {
		this.PRODUCT_CATEGORYID = PRODUCT_CATEGORYID;
	}
	public String getNAME() {
		return NAME;
	}
	public void setNAME(String NAME) {
		this.NAME = NAME;
	}
	public ProductCategory(Integer PRODUCT_CATEGORYID, String NAME) {
		super();
		this.PRODUCT_CATEGORYID = PRODUCT_CATEGORYID;
		this.NAME = NAME;
	}
	public ProductCategory() {
		super();
	}
	@Override
	public String toString() {
		return "ProductCategory [PRODUCT_CATEGORYID=" + PRODUCT_CATEGORYID + ", NAME=" + NAME + "]";
	}
}
```

2. 数据访问层接口

对应于数据访问层接口 ProductCategoryMapper，其位置放于包 dameng.dao 下。ProductCategoryMapper.java 文件如下所示：

```
package dameng.dao;
import java.util.List;
import dameng.pojo.ProductCategory;
public interface ProductCategoryMapper {
    public List<ProductCategory> selectAll();
    public int selectCount();
    public ProductCategory selectProductCategoryById(Integer id);
    public void insertProductCategory(ProductCategory ProductCategory);
    public void deleteById(Integer id);
    public void updateProductCategory(ProductCategory ProductCategory);
}
```

3. 数据访问层接口对应配置文件

对应于数据访问层接口对应配置文件 ProductCategoryMapper.xml，其位置放于包 dameng.dao 下。ProductCategoryMapper.xml 文件如下所示：

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--命名空间  会映射到 接口-->
<mapper namespace="dameng.dao.ProductCategoryMapper">
    <!--parameterType 参数类型 -->
    <!--resultType 返回结果集类型 -->
    <!--查询所有数据-->
    <select id="selectAll" resultType="dameng.pojo.ProductCategory">
      select * from PRODUCTION.PRODUCT_CATEGORY;
    </select>
    <!--查询总条数-->
    <select id="selectCount" resultType="int">
      select count(*) from PRODUCTION.PRODUCT_CATEGORY;
    </select>
    <!--根据 ID 查询指定信息-->
    <!--有参数   有返回值-->
    <select id="selectProductCategoryById" parameterType="java.lang.Integer"
            resultType="dameng.pojo.ProductCategory">
      select * from PRODUCTION.PRODUCT_CATEGORY WHERE PRODUCT_CATEGORYID = #{PRODUCT_CATEGORYID};
    </select>
    <!--插入信息   -->
    <insert id="insertProductCategory" parameterType="dameng.pojo.ProductCategory" >
        INSERT into PRODUCTION.PRODUCT_CATEGORY(PRODUCT_CATEGORYID,name)VALUES(#{PRODUCT_CATEGORYID},#{NAME});
    </insert>
    <!--根据 id 删除信息-->
    <delete id="deleteById" parameterType="int">
        delete from PRODUCTION.PRODUCT_CATEGORY where PRODUCT_CATEGORYID = #{PRODUCT_CATEGORYID};
    </delete>
    <!--根据 id 修改信息-->
    <update id="updateProductCategory" parameterType="dameng.pojo.ProductCategory">
        update PRODUCTION.PRODUCT_CATEGORY set NAME = #{NAME}
        where PRODUCT_CATEGORYID = #{PRODUCT_CATEGORYID}
    </update>
</mapper>
```

4. 基础数据测试类

对应于基础数据测试类 TestProduct，其位置放于包 dameng.test 下。TestProduct.java 文件如下所示：

```
package dameng.test;

import java.io.IOException;
import java.io.InputStream;
import java.util.List;

import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;
import dameng.dao.ProductCategoryMapper;
import dameng.pojo.ProductCategory;

public class TestProduct {
	SqlSession sqlSession = null;
	ProductCategoryMapper productCategoryMapper = null;
	public void init() {
		try {
		//1. 生成 sqlsession factory biulder 对象
	    SqlSessionFactoryBuilder sfb = new SqlSessionFactoryBuilder();
	    //2. 加载配置文件作为一个输入流
	    //这里Resources 使用的包是 ibatis 包
	    InputStream resourceAsStream;
			resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
	    //3. 通过会话工厂构造器 对象和  配置文件流 构建一个会话构造工厂
	    SqlSessionFactory factory = sfb.build(resourceAsStream);
	    //4. 通过 sql 会话工厂 //true 设置 mybatis 事务自动提交
	    sqlSession = factory.openSession(true);
	  
	    productCategoryMapper = sqlSession.getMapper(ProductCategoryMapper.class);
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	//测试插入信息
    @Test
    public void testInstert(){
		this.init();
		productCategoryMapper.insertProductCategory(new ProductCategory(6, "少儿 "));
    }
    //测试修改信息
    @Test
    public void testUpdate(){
    	ProductCategory productCategory = productCategoryMapper.selectProductCategoryById(4);
    	productCategory.setNAME("英语");
    	productCategoryMapper.updateProductCategory(productCategory);
    }
    //测试根据 id 查询指定人信息
    @Test
    public void testSelectPersonById(){
    	ProductCategory productCategory = productCategoryMapper.selectProductCategoryById(1);
        System.out.println(productCategory);
    }
    //测试全查
    @Test
    public void testSelectAll(){
        List<ProductCategory> list = productCategoryMapper.selectAll();
        for(ProductCategory p: list){
            System.out.println(p);
        }
    }
    //测试删除
    @Test
    public  void testDelete(){
    	productCategoryMapper.deleteById(5);
    }
	public static void main(String[] args) {
		TestProduct test = new TestProduct();
		test.init();
		test.testInstert();
		test.testDelete();
		test.testUpdate();
        test.testSelectPersonById();
        test.testSelectAll();
	}
}
```

5. 运行结果

完成以上配置后执行测试类 TestProduct.java，运行后控制台输出结果如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2023030216342870PQC5TOC380XIO17V)

运行后在数据库查询数据数据如下图所示：

```
select * from PRODUCTION.PRODUCT_CATEGORY;
```

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302163544D19PRWRQNVPFJI5QA7)

### 4.2 大字段操作示例

1. 操作数据库对应的实体类

对应于数据库表添加实体类 TestBigData，其位置放于包 dameng.pojo 下。TestBigData.java 文件如下所示：

```
package dameng.pojo;
public class TestBigData {
	private Integer id;
	private byte[] photo; //mybatis将 Image 和Blob 映射成byte[]
	private byte[] describe;
	private String txt; //mybatis 将 Clob 映射成 String
	public Integer getId() {
		return id;
	}
	public void setId(Integer id) {
		this.id = id;
	}
	public byte[] getPhoto() {
		return photo;
	}
	public void setPhoto(byte[] photo) {
		this.photo = photo;
	}
	public byte[] getDescribe() {
		return describe;
	}
	public void setDescribe(byte[] describe) {
		this.describe = describe;
	}
	public String getTxt() {
		return txt;
	}
	public void setTxt(String txt) {
		this.txt = txt;
	}
	@Override
	public String toString() {
		return "TestBigDate [id=" + id + ", txt=" + txt + "]";
	}
	public TestBigData(Integer id, byte[] photo, byte[] describe, String txt) {
		super();
		this.id = id;
		this.photo = photo;
		this.describe = describe;
		this.txt = txt;
	}
	public TestBigData() {
		super();
	}
}
```

2. 数据访问层接口

对应于数据访问层接口类 TestBigDataMapper，其位置放于包 dameng.dao 下。TestBigDataMapper.java 文件如下所示：

```
package dameng.dao;
import java.util.List;
import dameng.pojo.TestBigData;

public interface TestBigDataMapper {
	//插入大字段
	public void InsertIntoTestBigDate(TestBigData bigdate);
	//查询大字段
	public List<TestBigData> SelectFromTestBigDate();
}
```

3. 数据访问层接口对应配置文件

对应于数据访问层接口对应配置文件 TestBigDataMapper，其位置放于包 dameng.dao 下。TestBigDataMapper.xml 文件如下所示：

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--命名空间  会映射到 接口-->
<mapper namespace="dameng.dao.TestBigDataMapper">
    <!--parameterType 参数类型 -->
    <!--resultType 返回结果集类型 -->
    <!--查询所有数据-->
    <select id="SelectFromTestBigDate" resultType="dameng.pojo.TestBigData">
      select * from PRODUCTION.BIG_DATA;
    </select>
    <!--插入信息   -->
    <insert id="InsertIntoTestBigDate" parameterType="dameng.pojo.TestBigData" >
        INSERT into PRODUCTION.BIG_DATA(photo,describe,txt)VALUES (
        	#{photo},#{describe},#{txt}
        );
    </insert>
</mapper>
```

4. 大字段数据测试类

对应于基础数据测试类 Test_TestBigDat，其位置放于包 dameng.test 下。Test_TestBigDat.java 文件如下所示：

以下代码中 testInsert 和 filePath2 的路径请保持与对象文件实际存放一致。

```
package dameng.test;

import java.io.BufferedInputStream;
import java.io.BufferedReader;
import java.io.File;
import java.io.FileInputStream;
import java.io.FileNotFoundException;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.io.UnsupportedEncodingException;
import java.util.List;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import dameng.dao.TestBigDataMapper;
import dameng.pojo.TestBigData;
public class Test_TestBigData {
	SqlSession sqlSession = null;
	TestBigDataMapper testBigDateMapper = null;
	public void init() {
		try {
		//1. 生成 sqlsession factory biulder 对象
	    SqlSessionFactoryBuilder sfb = new SqlSessionFactoryBuilder();
	    //2. 加载配置文件作为一个输入流
	    //这里 Resources 使用的包是 ibatis 包
	    InputStream resourceAsStream;
			resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
	    //3. 通过会话工厂构造器 对象和  配置文件 流  构建一个会话构造工厂
	    SqlSessionFactory factory = sfb.build(resourceAsStream);
	    //4. 通过 sql 会话工厂 //true 设置 mybatis 事务自动提交
	    sqlSession = factory.openSession(true);
	    testBigDateMapper = sqlSession.getMapper(TestBigDataMapper.class);
	    resourceAsStream.close();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	public static void main(String[] args) {
		Test_TestBigData test = new Test_TestBigData();
		test.init();
		test.testInsert();
		test.testSelect();
	}
	//测试插入大字段表
	private void testInsert() {
		try {
		String filePath = "D:\\mybatis_bigdata_test.jpg";
		File file = new File(filePath);
		String filePath2 = "D:\\mybatis_bigdata_test.txt";
		File file2 = new File(filePath2);
		InputStream in;
		in = new BufferedInputStream(new FileInputStream(file));
		byte[] bytes1 = new byte[732160];//缓冲区要够大
		byte[] bytes2 = new byte[732160];//缓冲区要够大
		in.read(bytes1);
		InputStream in2 = new BufferedInputStream(new FileInputStream(file));
		in2.read(bytes2);
		BufferedReader reader = new BufferedReader(new InputStreamReader
				(new FileInputStream(file2),"UTF-8"));
		//char[] c = new char[10240];
		char[] chars = new char[4096];
		reader.read(chars);
		TestBigData bigDate = new TestBigData(null,bytes1,bytes2,new String(chars));
		testBigDateMapper.InsertIntoTestBigDate(bigDate);
		in.close();
		in2.close();
		reader.close();
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (UnsupportedEncodingException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
	//测试查询大字段表
	private void testSelect() {
		List<TestBigData> list = testBigDateMapper.SelectFromTestBigDate();
		try {
			for(TestBigData big:list) {
				//打印出 id
				System.out.println("id = "+big.getId());
				//将 photo 列信息 输出到指定路径
				FileOutputStream fos = new FileOutputStream("D:/"+big.getId()+"_mybatis_photo_test.jpg");
				fos.write(big.getPhoto());
				//将 describe 列信息 输出到指定路径
				FileOutputStream fos2 = new FileOutputStream("D:/"+big.getId()+"_mybatis_describe_test.jpg");
				fos2.write(big.getDescribe());
				//将 txt 列信息 输出到控制台
				System.out.println("txt="+big.getTxt());
				fos.close();
				fos2.close();
		}
		} catch (FileNotFoundException e) {
			e.printStackTrace();
		} catch (IOException e) {
			e.printStackTrace();
		}
	}
}
```

5. 运行结果

运行前如下图所示，只有原始大对象文件：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302164049Z3XQFCWRNWX0I2WSCV)

完成以上配置后执行测试类 Test_TestBigDat.java 后控制台输出 CLOB 里保存的大字段文本信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302164114GKIK7HHFPMFNCSU9V9)

运行后会将大对象插入数据库，查看数据库对应数据信息如下图所示：

```
select * from PRODUCTION.BIG_DATA;
```

  * **IMAGE 数据：**



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302164626R4GO2RZA38PTTEQHMB)

  * **BLOB 数据：**



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302164753D4IDERH0JVFW3TXEAY)

  * **CLOB 数据：**



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302164819RY15KEQG5N4YYGKC5L)

运行后读取处理 IMAGE 大字段和 BLOB 大字段后输出到文件，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230302164927WGSXU9R3MQ06YPS0WK)

## 五、常见问题

1. 快速设置使得在 mybatis 下对象不用添加双引号。

【问题解决】：

  * 设置大小写不敏感，此方法会影响查询结果集，因为其对结果集匹配也不区分大小写，需要根据业务自行评估是否可行。
  * 在 mysql 数据库中，对象名默认是小写，达梦对象名默认是大写，在用达梦 DTS 工具迁移的时候，去掉“保持对象名大小写”的勾选，使对象名自动转换成大写，在 mybatis 查询的时候，即不需要加双引号强调小写。



## 六、参考

  1. 示例代码下载：[JAVA_Mybatis.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20230302165430K6SQLBW9PE13G7BICC)
  2. 以上文档内容参考过程中遇到任何问题，可到[达梦技术社区](https://eco.dameng.com/community/question)提问交流。





# iBatis 框架

## 一、前言

iBatis 一词来源于“internet”和“abatis”的组合，是一个由 Clinton Begin 在  2001 年发起的开放源代码项目。于 2010 年 6 月 16 号被谷歌托管，改名为 MyBatis。iBatis 是一个基于 SQL 映射支持 JAVA 和 .NET 的持久层框架。

## 二、数据库连接

### 2.1 iBatis 框架引入

1.准备项目所需 jar 包：[JAVA_iBatis_lib.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221215101406JALRNZK2AZ7HLH4TS1) 。

2.新建项目 JAVA_iBatis，并导入项目所需 jar 包，如下图所示：

> **注意**
>
> 复制 jar 包到 lib 文件夹后，要选择所有 jar 包，点击鼠标右键，选择 Build path-->Add to Build path。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214164306EZPZ7V30XEXBEWYSJ2)

  3. 在 src 源码目录下创建如下包名，完整项目目录结构如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214164323J4EDYBCD7TUF9I36YB)

```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE sqlMapConfig
PUBLIC "-//iBATIS.com//DTD SQL Map Config 2.0//EN"
"http://www.ibatis.com/dtd/sql-map-config-2.dtd">
<sqlMapConfig>
<properties  resource ="jdbc.properties" /> 
<settings
cacheModelsEnabled="true"
enhancementEnabled="true"
lazyLoadingEnabled="true"
errorTracingEnabled="true"
maxRequests="32"
maxSessions="10"
maxTransactions="5"
useStatementNamespaces="false"
/>
<transactionManager type="JDBC">
<dataSource type="SIMPLE" >
<property name="JDBC.Driver" value="${jdbc.driver}"/>
<property name="JDBC.ConnectionURL" value="${jdbc.url}"/>
<property name="JDBC.Username" value="${jdbc.username}"/>
<property name="JDBC.Password" value="${jdbc.password}"/>
</dataSource>
</transactionManager>
<sqlMap resource="dameng/pojo/ProductCategory.xml"/>
<sqlMap resource="dameng/pojo/BigData.xml"/>
</sqlMapConfig>
```

  5. 添加 jdbc.properties 配置文件（主要配置 driver、url、username、password 等）。修改数据库连接信息，只修改 jdbc.properties 里的字段即可。



jdbc.properties 文件如下所示：

```
jdbc.driver=dm.jdbc.driver.DmDriver
jdbc.url=jdbc:dm://localhost:5236
jdbc.username=SYSDBA
jdbc.password=*****
```

## 三、开发示例

### 3.1 基础绑定参数示例

基本操作使用的数据表为示例库中 Product 的 PRODUCT_CATEGORY 表，对应于数据库表添加实体类 PRODUCT_CATEGORY，其位置放 dameng.pojo 下。

  1. 操作数据库对应的实体类



PRODUCT_CATEGORY.java 文件如下所示：

```
package dameng.pojo;
public class PRODUCT_CATEGORY {
    private int PRODUCT_CATEGORYID;
    private String NAME;
    public int getPRODUCT_CATEGORYID() {
        return PRODUCT_CATEGORYID;
    }
    public void setPRODUCT_CATEGORYID(int pRODUCT_CATEGORYID) {
        PRODUCT_CATEGORYID = pRODUCT_CATEGORYID;
    }
    public String getNAME() {
        return NAME;
    }
    public void setNAME(String nAME) {
        NAME = nAME;
    }
    public PRODUCT_CATEGORY(int pRODUCT_CATEGORYID, String nAME) {
        super();
        PRODUCT_CATEGORYID = pRODUCT_CATEGORYID;
        NAME = nAME;
    }
    public PRODUCT_CATEGORY() {
        super();
    }
}
```

  2. 集中管理 SQL 语句的配置文件 ProductCategory.xml



此配置文件由 SqlMapConfig.xml 配置文件映射。

```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMap
PUBLIC "-//iBATIS.com//DTD SQL Map 2.0//EN"
"http://www.ibatis.com/dtd/sql-map-2.dtd">

<sqlMap namespace="ProductCategory">
<typeAlias alias="ProductCategory" type="dameng.pojo.ProductCategory"/>

<resultMap class="dameng.pojo.ProductCategory" id="ProductCategory">
        <result property="productCategoryId" column="PRODUCT_CATEGORYID"/>
        <result property="name" column="NAME"/>
</resultMap>
<!--根据 ID 查询指定信息-->
<!--有参数,有返回值-->
<select id="selectProductCategoryById" parameterClass="java.lang.Integer" 
    resultMap="ProductCategory">
    select * from
    PRODUCTION.PRODUCT_CATEGORY
    WHERE PRODUCT_CATEGORYID = #productCategoryId#;
</select>
<!--查询总条数-->
<select id="selectCount" resultClass="java.lang.Integer">
    elect count(*) from PRODUCTION.PRODUCT_CATEGORY;
</select>
<!--parameterType 参数类型 -->
<!--resultType 返回结果集类型 -->
<!--查询所有数据-->
<select id="selectAll" resultMap="ProductCategory">
    select * from PRODUCTION.PRODUCT_CATEGORY;
</select>
<!--插入信息   -->
<insert id="insertProductCategory" parameterClass="ProductCategory" >
    INSERT into PRODUCTION.PRODUCT_CATEGORY(name)VALUES (
      #name#
    );
</insert>
<!--根据 id 删除信息-->
<delete id="deleteById" parameterClass="int">
    delete from PRODUCTION.PRODUCT_CATEGORY where PRODUCT_CATEGORYID = #productCategoryId#;
</delete>
<!--根据 id 修改信息-->
<update id="updateProductCategory" parameterClass="dameng.pojo.ProductCategory">
    update PRODUCTION.PRODUCT_CATEGORY set NAME = #name#
    where PRODUCT_CATEGORYID = #productCategoryId#
</update>
</sqlMap>
```

  3. 测试类



TestProduct.java 文件如下所示：

```
package dameng.test;
import java.io.IOException;
import java.io.Reader;
import java.sql.SQLException;
import java.util.List;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import com.ibatis.common.resources.Resources;
import com.ibatis.sqlmap.client.SqlMapClient;
import com.ibatis.sqlmap.client.SqlMapClientBuilder;
import dameng.pojo.ProductCategory;
public class TestProduct {
    SqlMapClient sqlMap = null;
    Reader reader = null;
    @Before
    public void before() {
        try {
            //1. 读取 SqlMapConfig.xml 配置文件
            reader = Resources.getResourceAsReader("SqlMapConfig.xml");
            //2. 通过读取的配置文件构建 sqlMap 对象
            sqlMap = SqlMapClientBuilder.buildSqlMapClient(reader);
        } catch (IOException e) {
            e.printStackTrace();
        } 
    }
    //测试插入信息
    @Test
    public void testInstert(){
        try {
            //注意此处 sqlmap 调用传递的字符串参数与对应 xml 文件里 SQL 语句的 ID 相对应
            sqlMap.insert("insertProductCategory",new ProductCategory(null, "物理"));
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    //测试修改信息
    @Test
    public void testUpdate(){
        ProductCategory productCategory;
        try {
            productCategory = (ProductCategory) sqlMap.queryForObject("selectProductCategoryById",4);
            productCategory.setName("英语2");
            //注意此处 sqlmap 调用传递的字符串参数与对应 xml 文件里 SQL 语句的 ID 相对应
            sqlMap.update("updateProductCategory", productCategory);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    //测试根据 ID 查询指定人信息
    @Test
    public void testSelectPersonById(){
        ProductCategory productCategory;
        try {
            //注意此处 sqlmap 调用传递的字符串参数与对应 xml 文件里 SQL 语句的 ID 相对应
            productCategory = (ProductCategory) sqlMap.queryForObject("selectProductCategoryById",4);
            System.out.println(productCategory);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    //测试全查
    @Test
    public void testSelectAll(){
        List<ProductCategory> list;
        try {
            //注意此处 sqlmap 调用传递的字符串参数与对应 xml 文件里 SQL 语句的 ID 相对应
            list = sqlMap.queryForList("selectAll");
            for(ProductCategory p: list){
                System.out.println(p);
            }
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    //测试删除
    @Test
    public void testDelete(){
        try {
            //注意此处 sqlmap 调用传递的字符串参数与对应 xml 文件里 SQL 语句的 ID 相对应
            sqlMap.delete("deleteById",5);
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    @After
    public void after() {
        try {
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) {
        TestProduct test = new TestProduct();
        test.before();
        test.testInstert();
        test.testUpdate();
        test.testSelectPersonById();
        test.testSelectAll();
        test.testDelete();
    }
}
```

  4. 运行结果截图。运行后控制台输出结果如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227112421ADW2I3SXY1C07XABBC)

运行后数据库数据如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227112153TQYU55TT697A5VV85H)

### 3.2 大字段操作示例

  1. 创建需要操作的含大字段类型的数据表。



```
CREATE TABLE "PRODUCTION"."BIG_DATA"
(
"ID" INT IDENTITY(1, 1) NOT NULL,
"PHOTO" IMAGE,
"DESCRIBE" BLOB,
"TXT" CLOB,
NOT CLUSTER PRIMARY KEY("ID")) STORAGE(ON "BOOKSHOP", CLUSTERBTR) ;
```

  2. 在 D 盘根目录下，创建 DM8 特点 .jpg、达梦产品简介 .txt 两个文件，作为大字段存储，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227112516YBJJUHGH7AHWPIZBDF)

  3. 操作数据库对应的实体类。BigData.java 文件如下所示：



```
package dameng.pojo;

public class BigData {
    private Long id;
    private byte[] photo; //iBatis 将 Image 和 Blob 映射成 byte[]
    private byte[] describe;
    private String txt; //mybatis 将 Clob 映射成 String
    public Long getId() {
        return id;
    }
    public void setId(Long id) {
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
        return "TestBigData [id=" + id + ", txt=" + txt + "]";
    }
    public BigData(Long id, byte[] photo, byte[] describe, String txt) {
        super();
        this.id = id;
        this.photo = photo;
        this.describe = describe;
        this.txt = txt;
    }
    public BigData() {
        super();
    }
}
```

  4. 集中管理 SQL 语句的配置文件 BigData.xml。此配置文件由 SqlMapConfig.xml 配置文件映射。



```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE sqlMap
PUBLIC "-//iBATIS.com//DTD SQL Map 2.0//EN"
"http://www.ibatis.com/dtd/sql-map-2.dtd">

<sqlMap namespace="BigData">
<typeAlias alias="BigData" type="dameng.pojo.BigData"/>
<!-- <resultMap class="dameng.pojo.BigData" id="BigData">
        <result property="productCategoryId" column="PRODUCT_CATEGORYID"/>
        <result property="name" column="NAME"/>
</resultMap> -->
<!--parameterClass 参数类型 -->
<!--resultClass 返回结果集映射 -->
<!--查询所有数据-->
<select id="selectAllBigData" resultClass="BigData">
  	select * from PRODUCTION.BIG_DATA;
</select>
<!--插入信息-->
<insert id="insertBigData" parameterClass="BigData" >
    INSERT into PRODUCTION.BIG_DATA(photo,describe,txt)VALUES (
      #photo#,#describe#,#txt#
    );
</insert>
</sqlMap>
```

  5. 测试类。TestBigData.java 文件如下所示：



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
import java.io.Reader;
import java.io.UnsupportedEncodingException;
import java.sql.SQLException;
import java.util.List;

import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import com.ibatis.common.resources.Resources;
import com.ibatis.sqlmap.client.SqlMapClient;
import com.ibatis.sqlmap.client.SqlMapClientBuilder;
import dameng.pojo.BigData;
import dameng.pojo.ProductCategory;
public class TestBigData {
    SqlMapClient sqlMap = null;
    Reader reader = null;
    @Before
    public void before() {
        try {
            //1. 读取 SqlMapConfig.xml 配置文件
            reader = Resources.getResourceAsReader("SqlMapConfig.xml");
            //2. 通过读取的配置文件构建 sqlMap 对象
            sqlMap = SqlMapClientBuilder.buildSqlMapClient(reader);
        } catch (IOException e) {
            e.printStackTrace();
        } 
    }
    //测试插入大字段表
    @Test
    public void testInstert(){
        try {
            String filePath = "D:\\DM8特点.jpg";
            File file = new File(filePath);
            String filePath2 = "D:\\达梦产品简介.txt";
            File file2 = new File(filePath2);
            InputStream in;
            in = new BufferedInputStream(new FileInputStream(file));
            byte[] bytes1 = new byte[102400];
            byte[] bytes2 = new byte[102400];
            in.read(bytes1);
            InputStream in2 = new BufferedInputStream(new FileInputStream(file));
            in2.read(bytes2);
            BufferedReader reader = new BufferedReader(new InputStreamReader
                    (new FileInputStream(file2),"UTF-8"));
            StringBuffer stringBuffer = new StringBuffer("");
            String str = null;
            while((str = reader.readLine())!=null) {
                stringBuffer.append(str);
                stringBuffer.append("\n");
            }
            BigData bigData = new BigData(null,bytes1,bytes2,stringBuffer.toString());
            //注意此处 sqlmap 调用传递的字符串参数与对应 xml 文件里 SQL 语句的 ID 相对应
            sqlMap.insert("insertBigData",bigData);
        } catch (SQLException e) {
            e.printStackTrace();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //测试查询大字段表
    @Test
    public void testSelectAll(){
        try {
            List<BigData> list = sqlMap.queryForList("selectAllBigData");
            for(BigData big:list) {
                //打印出 id
                System.out.println("id = "+big.getId());
                //将 photo 列信息输出到指定路径
                FileOutputStream fos = new FileOutputStream("D:/"+big.getId()+"_DM8特点.jpg");
                fos.write(big.getPhoto());
                //将 describe 列信息输出到指定路径
                FileOutputStream fos2 = new FileOutputStream("D:/"+big.getId()+"_Blob_DM8特点.jpg");
                fos2.write(big.getDescribe());
                //将 photo 列信息输出到控制台
                System.out.println("txt="+big.getTxt());
                fos.close();
                fos2.close();
        }
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        } catch (SQLException e) {
            e.printStackTrace();
        }
    }
    @After
    public void after() {
        try {
            reader.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) {
        TestBigData test = new TestBigData();
        test.before();
        test.testInstert();
        test.testSelectAll();
        test.after();
    }
}
```

  6. 运行结果截图。运行前如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227113847W4XAIJCNPFNJW0O6OC)

运行后控制台输出 Clob 里保存的大字段文本信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227113906ZPCGTTP1C6MFU8UE78)

运行后读取处理 Image 大字段和 Blob 大字段后的信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212271141237L6YTCR7UCX3ERMWZK)

运行后数据库数据信息如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227114154DDXRG2A1GCOMW4WWJ2)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227114232L3VEMONI7ZX4J72R0H)

## 四、参考

  1. 示例代码下载：[JAVA_iBatis.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221229155010EDD8X6CC4XGR9VNRYS)。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。



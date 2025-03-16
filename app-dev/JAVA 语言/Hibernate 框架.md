

# Hibernate 框架

## 一、前言

Hibernate 是一个开放源代码的对象关系映射框架，它对 JDBC 进行了非常轻量级的对象封装，它将 POJO 与数据库表建立映射关系，是一个全自动的 ORM 框架，Hibernate 可以自动生成 SQL 语句，自动执行，使得 Java 程序员可以随心所欲的使用对象编程思维来操纵数据库。

## 二、数据库连接

### 2.1 Hibernate 框架引入

  1. 准备项目所需 jar 包：[JAVA_Hibernate_lib.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221215101545VDTCQHAM5JD0C86Q5D)。
  2. 新建项目 JAVA_Hibernate，并导入项目所需 jar 包，如下图所示：



> **注意**
>
> 复制 jar 包到 lib 文件夹后，要选择所有 jar 包，点击鼠标右键，选择 Build path-->Add to Build path。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214161042B38F92GQC8ZD2ENY6W)

  3. 在 src 源码目录下创建如下包名，完整项目目录结构如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214161109VG3VGXX66BQERCUVED)

  4. 书写 Hibernate 的主配置文件：hibernate.cfg.xml，存放在 src 目录下。hibernate.cfg.xml 文件如下所示：



```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-configuration SYSTEM 
"http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd">

<hibernate-configuration>
   <session-factory>

   <!-- 这个属性使 Hibernate 应用为被选择的数据库生成适当的 SQL -->
   <property name="hibernate.dialect">org.hibernate.dialect.DmDialect</property>

   <!-- JDBC 驱动程序类 -->
   <property name="hibernate.connection.driver_class">dm.jdbc.driver.DmDriver</property>

   <!-- Assume test is the database name -->
   <!-- 数据库实例的 JDBC URL -->
   <property name="hibernate.connection.url">jdbc:dm://localhost:5236</property>

   <!-- 数据库用户名 -->
   <property name="hibernate.connection.username">SYSDBA</property>

   <!-- 数据库密码 -->
   <property name="hibernate.connection.password">*****</property>

   <!-- 是否显示 SQL -->
   <property name="hibernate.show_sql">true</property>

   <!-- 是否将 SQL 格式化 -->
   <property name="hibernate.format_sql">true</property>

   <!-- 是否自动在数据库中生成表 -->
   <property name="hibernate.hbm2ddl.auto">update</property>

   <!-- 事物自动提交 -->
   <property name="hibernate.connection.autocommit">true</property>

   <!-- 针对大字段处理 -->
   <property name="hibernate.connection.SetBigStringTryClob">true</property>

   <!-- List of XML mapping files -->
   <!-- 下面是映射的类 -->
   <mapping resource="dameng/pojo/BigData.hbm.xml"/>
   <mapping resource="dameng/pojo/ProductCategory.hbm.xml"/>

</session-factory>
</hibernate-configuration>
```

## 三、开发示例

### 3.1 基础操作示例

基本操作使用的数据表为示例库中 Product 的 PRODUCT_CATEGORY 表，对应于数据库表添加实体类 ProductCategory，其位置放于 dameng.pojo 下。

  1. 操作数据库对应的实体类。ProductCategory.java 文件如下所示：



```
package dameng.pojo;
public class ProductCategory {
    private Long productCategoryId;
    private String name;
    public Long getProductCategoryId() {
        return productCategoryId;
    }
    public void setProductCategoryId(Long productCategoryId) {
        this.productCategoryId = productCategoryId;
    }
    public String getName() {
        return name;
    }
    public void setName(String name) {
        this.name = name;
    }
    @Override
    public String toString() {
        return "ProductCategory [productCategoryId=" + productCategoryId + ", name=" + name + "]";
    }
    public ProductCategory(Long productCategoryId, String name) {
        super();
        this.productCategoryId = productCategoryId;
        this.name = name;
    }
    public ProductCategory() {
        super();
    }
 }
```

  2. 配置实体类与数据库映射的配置文件。



```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">
<hibernate-mapping>
    <class name="dameng.pojo.ProductCategory" table="PRODUCTION.PRODUCT_CATEGORY">
        <id name="productCategoryId" type="java.lang.Long">
            <column name="PRODUCT_CATEGORYID" />
            <generator class="identity" />
        </id>
        <property name="name" type="java.lang.String">
            <column name="NAME" length="50" not-null="true" />
        </property>
    </class>
</hibernate-mapping>
```

  3. 测试类。TestProduct.java 文件如下所示：



```
package dameng.test;
import java.util.List;
import org.hibernate.Query;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import org.hibernate.classic.Session;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import dameng.pojo.ProductCategory;

public class TestProductCategory {
    SessionFactory sf = null;
    Session session = null;
    @Before
    public void before() {
        //(1)读取并解析hibernate.cfg.xml配置文件
        Configuration config = new Configuration().configure();
        //(2)由hibernate.cfg.xml中的<mapping resource="dameng/xx/User.hbm.xml"/>读取并解析映射信息*/
        //(3)创建会话SessionFactory对象
        sf = config.buildSessionFactory();
        //(4)加载Session对象
        session = sf.openSession();
    }
    //测试插入
    @Test
    public void testInsert() {
        ProductCategory pCategory = new ProductCategory(null, "美术");
        session.save(pCategory);
    }
    //测试修改
    @Test
    public void testUpdate() {
        ProductCategory pCategory = null; 
        pCategory = (ProductCategory)session.get(ProductCategory.class,new Long(4));
        System.out.println(pCategory);
        pCategory.setName("英语");
        System.out.println(pCategory);
        session.update(pCategory);
    }
    //测试全查
    @Test
    public void testSelectAll() {
        String hql = "from ProductCategory";
        Query query = session.createQuery(hql);
        List<ProductCategory> list = query.list();
        for(ProductCategory pCategory:list) {
            System.out.println(pCategory);
        }
    }
    //测试删除
    @Test
    public  void testDelete(){
        ProductCategory pCategory = null; 
        pCategory = (ProductCategory)session.get(ProductCategory.class,new Long(5));
        if(pCategory!=null) {
            session.delete(pCategory);
        }
    }
    @After
    public void after() {
        if (session != null) { 
            session.flush();
            session.clear();
            //(9) 关闭session对象
            session.close();
            /*
                *关闭session工厂,清空资源.由于sessionFactory对象持有整个持久化操作的资源,
                *关闭之后,下次在使用又需要创建,太耗内存,通过监听器application servletContext对象,
                *当服务器关闭或Web应用被移除时,ServletContext对象跟着销毁,
                *当停止进程时,再清理sessionFactory资源
                */
            //(10) 关闭sessionFactory对象
            sf.close();
        }
    }
    public static void main(String[] args) {
        TestProductCategory test = new TestProductCategory();
        test.before();
        test.testInsert();
        test.testUpdate();
        test.testSelectAll();
        test.testDelete();
        test.after();
    }
}
```

  4. 运行结果截图。运行后控制台输出结果如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227101202HD5PFJ228W9ZI93Q3P)

运行后数据库数据如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227101356VD732OXUFG9RW9JOQ8)

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



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227101715HRBCBG076QBZUQM3GW)

  3. 操作数据库对应的实体类。BigData.java 文件如下所示：



```
package dameng.pojo;
public class BigData {
    private Long id;
    private byte[] photo; //mybatis将 Image 和Blob 映射成byte[]
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
        return "TestBigDate [id=" + id + ", txt=" + txt + "]";
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

  4. 配置实体类与数据库映射的配置文件。



```
<?xml version="1.0" encoding="utf-8"?>
<!DOCTYPE hibernate-mapping PUBLIC
        "-//Hibernate/Hibernate Mapping DTD 3.0//EN"
        "http://hibernate.sourceforge.net/hibernate-mapping-3.0.dtd">

<hibernate-mapping>
    <class name="dameng.pojo.BigData" table="PRODUCTION.BIG_DATA">
        <id name="id" type="java.lang.Long">
            <column name="ID" />
            <generator class="identity" />
        </id>
        <property name="photo" type="byte[]">
            <column name="PHOTO" />
        </property>
        <property name="describe" type="byte[]">
            <column name="DESCRIBE" />
        </property>
        <property name="txt" type="java.lang.String">
            <column name="TXT" />
        </property>
    </class>
</hibernate-mapping>
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
import java.io.UnsupportedEncodingException;
import java.util.List;

import org.hibernate.Query;
import org.hibernate.SessionFactory;
import org.hibernate.cfg.Configuration;
import org.hibernate.classic.Session;
import org.junit.After;
import org.junit.Before;
import org.junit.Test;
import dameng.pojo.BigData;

public class TestBigData {
    SessionFactory sf = null;
    Session session = null;
    @Before
    public void before() {
        //(1)读取并解析 hibernate.cfg.xml 配置文件
        Configuration config = new Configuration().configure();
        //(2)由 hibernate.cfg.xml 中的<mapping resource="dameng/xx/User.hbm.xml"/>读取并解析映射信息*/
        //(3)创建会话 SessionFactory 对象
        sf = config.buildSessionFactory();
        //(4)加载 Session 对象
        session = sf.openSession();
    }
    //测试插入大字段表
    @Test
    public void testInsert() {
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
        session.save(bigData);
        in.close();
        in2.close();
        reader.close();
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        } catch (UnsupportedEncodingException e){
            e.printStackTrace();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    //测试查询大字段表
    @Test
    public void testSelect() {
        String hql = "from BigData";
        Query query = session.createQuery(hql);
        List<BigData> list = query.list();
        try {
            for(BigData big:list) {
                //打印出 id
                System.out.println("id = "+big.getId());
                //将 photo 列信息 输出到指定路径
                FileOutputStream fos = new FileOutputStream("D:/"+big.getId()+"_DM8特点.jpg");
                fos.write(big.getPhoto());
                //将 describe 列信息 输出到指定路径
                FileOutputStream fos2 = new FileOutputStream("D:/"+big.getId()+"_Blob_DM8特点.jpg");
                fos2.write(big.getDescribe());
                //将 photo 列信息 输出到控制台
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
    @After
    public void after() {
        if (session != null) { 
            session.flush();
            session.clear();
            //关闭 session 对象
            session.close();
            // 关闭 sessionFactory 对象
            sf.close();
        }
    }
    public static void main(String[] args) {
        TestBigData test = new TestBigData();
        test.before();
        test.testInsert();
        test.testSelect();
        test.after();
    }
}
```

  6. 运行结果截图。运行前如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227101938YBPOINDZ8KRM4S8D3N)

运行后控制台输出 Clob 里保存的大字段文本信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227101958NV5QNUSTNET20UH95I)

运行后读取处理 Image 大字段和 Blob 大字段后的信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227102058W1ZSBCOXRJT46TBQB8)

运行后数据库数据信息如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227102115UG440280EYS7YBQ5R7)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227102544TL0UVZY8T6GQ6XWUZ8)

## 四、常见问题

1. 发生 Error parsing XML: /hibernate.cfg.xml(1) 报错：文档类型声明包含或指向的标记声明格式有误

【问题描述】：使用该链接 http://www.hibernate.org/dtd/hibernate-configuration-3.0.dtd 获得是一个显示 DTD 的简单 HTML 页面，没有获取 DTD 文件本身。

【问题解决】：将该 DTD 保存到本地硬盘，并修改 URL 解决：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212271029420MYGSEL3XCREOY7DC3)

2. 插入大字段数据报错 Caused by: dm.jdbc.driver.DMException: 数据大小已超过可支持范围

【问题描述】：排查发现数据库中存储图片的数据类型为 VARBINARY。

【问题解决】：数据类型修改为 IMAGE/BLOB 后解决。

3. Hibernate 连接数据库的方言写法

Hibernate.cfg.xml 配置要求：

```
驱动名称
<property name="connection.driver_class">dm.jdbc.driver.DmDriver</property>
方言包名称
<property name="dialect">org.hibernate.dialect.DmDialect</property>
```

## 五、参考

  1. 示例代码下载：[JAVA_Hibernate.zip](https://eco.dameng.com/eco-file-server/file/eco/download/20221229154509EISTGG81NVHZTYE0UY) 。
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question/) 提问交流。



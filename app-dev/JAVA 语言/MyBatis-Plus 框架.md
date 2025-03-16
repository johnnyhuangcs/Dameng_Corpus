

# MyBatis-Plus 框架

## 一、前言

MyBatis-Plus（简称 MP ）是一个 MyBatis 的增强工具，在 MyBatis 的基础上只做增强不做改变，为简化开发、提高效率而生。

## 二、数据库连接

### 2.1 MyBatis-Plus 增强工具的引入

mybatis-plus 依托于 mybatis，两者相辅相成，MyBatis-Plus 增强工具的引入不会对 mybatis 框架造成影响。

  1. 准备项目所需 jar 包：[JAVA_Mybatis_Plus_lib.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202212201506344NGIPFGT38IV8TDOA9)。



> **注意**
>
> mybatis-plus3.0 之后的版本才支持 DM 数据库，在选择使用 mybatisplus-jar 包版本时请注意版本号。



若选择的 mybatis-plus 版本不支持 DM 数据库则会报错，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/202212271437020CIKTO8P7QOBHN8IJD)

  2. 新建项目 Java_Mybatis_Plus，并导入项目所需 jar 包，如下图所示：



> **注意**
>
> 复制 jar 包到 lib 文件夹后，要选择所有 jar 包，点击鼠标右键，选择 Build path-->Add to Build path。



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214102859FCFGD3BZ19JQHYWIJ2)

  3. 在 src 源码目录下创建如下包名，完整项目目录结构如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221214102929H7TAREZ2O2AS5MQWP5)

  4. 书写 Mybatis 的主配置文件：mybatis-config.xml，存放在 src 目录下。mybatis-config.xml 文件如下所示：



```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--<!– 引入 db.properties 文件 –>-->
    <properties resource="jdbc.properties"></properties>
    <!--配置接口-->
    <typeAliases>
        <package name="dameng.dao"/>
    </typeAliases>
    <!-- 配置 mybatis 运行环境-->
    <environments default="development">
        <environment id="development">
            <!-- 配置事务管理，采用 JDBC 的事务管理-->
            <transactionManager type="JDBC" />
            <!-- POOLED:mybatis 自带的数据源，JNDI：基于 Tomcat 的数据源 -->
            <!--使用 DB.properties-->
            <dataSource type="POOLED" >
                <property name="driver" value="${jdbc.driver}"/>
                <property name="url" value="${jdbc.url}"/>
                <property name="username" value="${jdbc.username}"/>
                <property name="password" value="${jdbc.password}"/>
            </dataSource>
        </environment>
    </environments>
    <!-- 将 mapper 文件加入到配置文件中 mapper 文件是写 SQL 语句的文件 -->
    <mappers>
        <!-- 这里如果是配置文件用/ -->
        <mapper resource="dameng/dao/ProductCategoryMapper.xml" />
        <mapper resource="dameng/dao/BigDataMapper.xml" />
    </mappers>
</configuration>
```

  5. 添加 jdbc.properties 配置文件，(主要配置 driver、url、username、password 等)。修改数据库连接信息，只修改 jdbc.properties 里的字段即可。jdbc.properties 文件如下所示：



```
jdbc.driver=dm.jdbc.driver.DmDriver
jdbc.url=jdbc:dm://localhost:5236
jdbc.username=SYSDBA
jdbc.password=*****
```

## 三、开发示例

### 3.1 基本绑定变量操作示例

基本操作使用的数据表为示例库中 Product 的 PRODUCT_CATEGORY 表，对应于数据库表添加实体类 PRODUCT_CATEGORY，其位置放于 dameng.pojo 下。

  1. 操作数据库对应的实体类。PRODUCT_CATEGORY.java 文件如下所示：



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

  2. 数据访问层接口。此处使用 mybatisplus 工具，接口继承 BaseMapper 规定泛型为 ProductCategory，无需自己手动添加增删改查接口。ProductCategoryMapper.java 文件如下所示：



```
package dameng.dao;
import org.apache.ibatis.annotations.Mapper;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import dameng.pojo.ProductCategory;

@Mapper
public interface ProductCategoryMapper extends BaseMapper<ProductCategory>{
}
```

  3. 数据访问层接口对应配置文件。由于此处使用 mybatisplus 工具，该工具自动映射与之前相应的 mapper 接口，无需再写基础增删改查方法的 xml。



```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--命名空间会映射到接口-->
<mapper namespace="dameng.dao.ProductCategoryMapper">
</mapper>                                    
```

  4. 测试类。TestProduct.java 文件如下所示：



```
package dameng.test;
import java.io.InputStream;
import java.util.List;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.junit.Test;
import com.baomidou.mybatisplus.core.MybatisSqlSessionFactoryBuilder;
import dameng.dao.ProductCategoryMapper;
import dameng.pojo.ProductCategory;
public class TestProduct {
    SqlSession sqlSession = null;
    ProductCategoryMapper productCategoryMapper = null;
    public void init() {
        try {
            //1. 生成 sqlsession factory biulder 对象
            MybatisSqlSessionFactoryBuilder builder = new MybatisSqlSessionFactoryBuilder();
            //2. 加载配置文件作为一个输入流
            InputStream resourceAsStream =
                    Resources.getResourceAsStream("mybatis-config.xml");
            SqlSessionFactory factory = builder.build(resourceAsStream);
            //这里 Resources 使用的包是 ibatis 包
            //3. 通过会话工厂构造器对象和配置文件流构建一个会话构造工厂
            //4. 通过 SQL 会话工厂 //true 设置 mybatis 事务自动提交
            sqlSession = factory.openSession(true);
            productCategoryMapper = sqlSession.getMapper(ProductCategoryMapper.class);
        }catch (Exception e){
            e.printStackTrace();
        }
    }
    //测试插入信息
    @Test
    public void testInstert(){
        productCategoryMapper.insert((new ProductCategory(null, "语文")));
    }
    //测试修改信息
    @Test
    public void testUpdate(){
        ProductCategory productCategory = productCategoryMapper.selectById(4);
        productCategory.setName("英语");
        productCategoryMapper.updateById(productCategory);
    }
    //测试根据 ID 查询指定人信息
    @Test
    public void testSelectPersonById(){
        ProductCategory productCategory = productCategoryMapper.selectById(1);
        System.out.println(productCategory);
    }
    //测试全查
    @Test
    public void testSelectAll(){
        List<ProductCategory> selectList = productCategoryMapper.selectList(null);
        for(ProductCategory p: selectList){
            System.out.println(p);
        }
    }
    //测试删除
    @Test
    public  void testDelete(){
        productCategoryMapper.deleteById(5);
    }
    //测试增删改查
    public static void main(String[] args) {
        TestProduct test = new TestProduct();
        test.init();
        test.testInstert();
        test.testSelectPersonById();
        test.testUpdate();
        test.testSelectAll();
        test.testDelete();
    }
}
```

  5. 运行结果截图。运行后控制台输出结果如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/2022122714524405MSBMITQGYY8IVEUG)

运行后数据库数据如下图所示：

![企业微信截图_1672123992128.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227145321ZQN3UW4WDEUSAYCEX8)

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

  2. 在 D 盘根目录下，创建 DM8 特点 .jpg、达梦产品简介.txt 两个文件，作为大字段存储，如下图所示：



![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227145450423K6NEX9WC82TJP54)

  3. 操作数据库对应的实体类。TestBigData.java 文件如下所示：



```
package dameng.pojo;

import com.baomidou.mybatisplus.annotation.IdType;
import com.baomidou.mybatisplus.annotation.TableId;
import com.baomidou.mybatisplus.annotation.TableName;
@TableName(value="PRODUCTION.BIG_DATA")
public class BigData {
    @TableId(value = "id",type = IdType.AUTO)
    private Long id;
    private byte[] photo; //mybatis 将 Image 和 Blob 映射成 byte[]
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

  4. 数据访问层接口。TestBigDataMapper.java 文件如下所示：



```
package dameng.dao;
import com.baomidou.mybatisplus.core.mapper.BaseMapper;
import dameng.pojo.BigData;
public interface BigDataMapper extends BaseMapper<BigData>{
}
```

  5. 数据访问层接口对应配置文件。



```
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<!--命名空间会映射到接口-->
<mapper namespace="dameng.dao.BigDataMapper">
</mapper>
```

  6. 测试类。TestBigData.java 文件如下所示：



```
package dameng.test;
import java.io.*;
import java.util.List;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.*;
import com.baomidou.mybatisplus.core.MybatisSqlSessionFactoryBuilder;
import dameng.dao.BigDataMapper;
import dameng.pojo.BigData;
public class TestBigData {
    SqlSession sqlSession = null;
    BigDataMapper bigDataMapper = null;
    public void init() {
        try {
        //1. 生成 sqlsession factory biulder 对象
        MybatisSqlSessionFactoryBuilder sfb = new MybatisSqlSessionFactoryBuilder();
        //2. 加载配置文件作为一个输入流
        //这里 Resources 使用的包是 ibatis 包
        InputStream resourceAsStream;
            resourceAsStream = Resources.getResourceAsStream("mybatis-config.xml");
        //3. 通过会话工厂构造器对象和配置文件流构建一个会话构造工厂
        SqlSessionFactory factory = sfb.build(resourceAsStream);
        //4. 通过 SQL 会话工厂 //true 设置 mybatis 事务自动提交
        sqlSession = factory.openSession(true);
        bigDataMapper = sqlSession.getMapper(BigDataMapper.class);
        resourceAsStream.close();
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
    public static void main(String[] args) {
        TestBigData test = new TestBigData();
        test.init();
        test.testInsert();
        test.testSelect();
    }
    //测试插入大字段表
    private void testInsert() {
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
        bigDataMapper.insert(bigData);
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
        List<BigData> list = bigDataMapper.selectList(null);
        try {
            for(BigData big:list) {
                //打印出id
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
        }
    }
}
```

  7. 运行结果截图。运行前如下图所示：



![企业微信截图_16721242174784.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227145701C4NGO31VTVOE957XSH)

运行后控制台输出 Clob 里保存的大字段文本信息，如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227145759SMT3NAQLWZ75BBEFVM)

运行后读取处理 Image 大字段和 Blob 大字段后的信息，如下图所示：

![企业微信截图_16721243076259.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227145830Z8QF4IOBC50M8HZFK2)

运行后数据库数据信息如下图所示：

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227145845E3VXECLDQV1LP4MM00)

![image.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20221227150302KNAMB63XCBPLDNXD3I)

## 四、参考

  1. 示例代码下载：[JAVA_Mybatis_Plus.zip](https://eco.dameng.com/eco-file-server/file/eco/download/202212301031532RKQL1FHJJYTV35VQI)
  2. 以上文档内容参考过程中遇到任何问题，可到 [达梦技术社区](https://eco.dameng.com/community/question) 提问交流。





# DMGEO 包

DMGEO 系统包实现了 SFA 标准（《OpenGIS® Implementation Standard for Geographic information - Simple feature access - Part 2: SQL option》）中规定的 SQL 预定义 schema，基于 SQL UDT（自定义数据类型）的空间数据类型和空间数据类型的初始化，以及针对空间数据类型的几何体计算函数。

DM 根据空间数据第一个点的坐标维度来确定该空间数据的几何维度，若该空间数据的坐标中存在维度小于几何维度的坐标，则在该坐标参数的末尾自动补充 NaN；若存在维度大于几何维度的坐标，则自动忽略该坐标参数末尾多余的维度值。因此若同时使用不同维度的坐标来描述一个空间数据，则无法保证该空间数据的准确性。

## 2.1 数据类型

达梦的空间数据类型是以类的方式进行体现的，具体有：

  * ST_Geometry：最基本的几何体，是其他几何类型的基类
  * ST_Point：点几何体
  * ST_Curve，ST_LineString（ST_LineArring）：线几何体。ST_Curve 是抽象类，ST_LineString 是 ST_Curve 可实例化的子类
  * ST_Surface，ST_Polygon：面几何体。ST_Surface 是抽象类，ST_Polygon 是 ST_Surface 可实例化的子类
  * ST_GeomCollection：几何体集合
  * ST_Multipoint：点集合
  * ST_Multicurve，ST_Multilinestring：线集合。ST_Multicurve 是抽象类，ST_Multilinestring 是 ST_Multicurve 可实例化的子类
  * ST_Multisurface，ST_Multipolygon：多边形集合。ST_Multisurface 是抽象类，ST_Multipolygon 是 ST_Multisurface 可实例化的子类



相关类型的继承关系如下图所示（带*号的表示抽象父类）：

![geo.png](https://eco.dameng.com/eco-file-server/file/eco/preview/20230529171253WACWB39T7W7C0F0RAV)

图2.1 DMGEO支持的空间数据类型关系图

如下详细介绍各数据类型：

  1. ST_Geometry



基础几何体类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_GEOMETRY AS OBJECT(
    SRID                INT,
    GEO_WKB             BLOB,
    GEO_TYPEID          INT,
    CONSTRUCTOR FUNCTION ST_GEOMETRY() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION ST_GEOMETRY(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT,
    FUNCTION ST_WKT()       	RETURN CLOB,
    FUNCTION ST_DIM()       	RETURN INT,
    FUNCTION ST_TYPE()      	RETURN VARCHAR(20),
    FUNCTION ST_ISEMPTY()   	RETURN INT,
    FUNCTION ST_ISSIMPLE()  	RETURN INT,  
    FUNCTION ST_ISVALID()   	RETURN INT,
    FUNCTION ST_COORDIM()   	RETURN INT, 
    FUNCTION ST_NUMGEOS()   	RETURN INT,
    FUNCTION ST_NUMPOINTS()	RETURN INT,
    FUNCTION ST_LENGTH()    	RETURN DOUBLE,
    FUNCTION ST_POINT_X() 	RETURN DOUBLE,
    FUNCTION ST_POINT_Y() 	RETURN DOUBLE,
    FUNCTION ST_START_POINT() 	RETURN SYSGEO.ST_GEOMETRY,
    FUNCTION ST_END_POINT() 		RETURN SYSGEO.ST_GEOMETRY,
    FUNCTION ST_IS_RING() 		RETURN INT,
    FUNCTION ST_IS_CLOSED() 		RETURN INT,
    FUNCTION ST_CENTROID() 		RETURN SYSGEO.ST_GEOMETRY,
    FUNCTION ST_POINT_ON() 		RETURN SYSGEO.ST_GEOMETRY,
    FUNCTION ST_AREA() 			RETURN DOUBLE,
    FUNCTION ST_EXT_RING() 		RETURN SYSGEO.ST_GEOMETRY,
    FUNCTION ST_NUM_INTER_RING()	RETURN INT) NOT FINAL;
```

类成员说明：

  * SRID 空间参考坐标系 ID。
  * GEO_WKB 二进制格式的几何体信息。
  * GEO_TYPEID 几何体类型 ID。-1 表示 ST_Geometry；0 表示 ST_Point；1 表示 ST_LineString；2 表示 ST_LineArring；3 表示 ST_Polygon；4 表示 ST_Multipoint；5 表示 ST_Multilinestring；6 表示 ST_Multipolygon；7 表示 ST_GeomCollection。



类方法说明：

  * ST_GEOMETRY()：ST_Geometry 类的构造函数。
  * ST_WKT()：返回文本格式表示的几何体。
  * ST_DIM()：几何体的几何维度。
  * ST_TYPE()：几何体的类型名。
  * ST_ISEMPTY()：几何体是否为空。
  * ST_ISSIMPLE()：是否为简单几何体。
  * ST_ISVALID()：几何体信息是否有效。
  * ST_COORDIM()：几何体的坐标维度。
  * ST_NUMGEOS()：几何体对象个数。
  * ST_NUMPOINTS()：几何体点的个数。
  * ST_LENGTH()：几何体长度。
  * ST_POINT_X()：点几何体的 X 坐标。
  * ST_POINT_Y()：点几何体的 Y 坐标。
  * ST_START_POINT()：线几何体的起点。
  * ST_END_POINT()：线几何体的终点。
  * ST_IS_RING()：线几何体是否是环。
  * ST_IS_CLOSED()：几何体是否是封闭的。
  * ST_CENTROID()：几何体质心。
  * ST_POINT_ON()：几何体表面上的点。
  * ST_AREA()：几何体面积。
  * ST_EXT_RING()：几何体外环。
  * ST_NUM_INTER_RING()：几何体内环数。



  2. ST_Point



点类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_POINT UNDER SYSGEO.ST_GEOMETRY(
    CONSTRUCTOR	FUNCTION	ST_POINT() RETURN SELF AS RESULT,
    CONSTRUCTOR	FUNCTION	ST_POINT(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT) NOT FINAL;
```

类说明：

  * ST_POINT()：点类构造方法。



  3. ST_Curve



抽象线类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_CURVE UNDER SYSGEO.ST_GEOMETRY(
	CONSTRUCTOR FUNCTION 	ST_CURVE() RETURN SELF AS RESULT,
	CONSTRUCTOR FUNCTION 	ST_CURVE(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT) NOT FINAL;
```

类说明：

  * ST_CURVE()：几何线基类构造方法。



  4. ST_Linestring



线类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_LINESTRING UNDER SYSGEO.ST_CURVE(
    CONSTRUCTOR FUNCTION 	ST_LINESTRING() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION 	ST_LINESTRING(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT,
	FUNCTION CURVE() RETURN SYSGEO.ST_CURVE) NOT FINAL;
```

类说明：

  * ST_LINESTRING()：几何线类构造方法。
  * CURVE()：返回 ST_Curve 几何类对象。



  5. ST_Surface



基础面类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_SURFACE UNDER SYSGEO.ST_GEOMETRY(
    CONSTRUCTOR FUNCTION ST_SURFACE() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION ST_SURFACE(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT) NOT FINAL;
```

类说明：

  * ST_SURFACE()：几何面基类构造方法。



  6. ST_Polygon



面类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_POLYGON UNDER SYSGEO.ST_SURFACE(
    CONSTRUCTOR FUNCTION ST_POLYGON() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION ST_POLYGON(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT,
    FUNCTION SURFACE() RETURN SYSGEO.ST_SURFACE) NOT FINAL;
```

类说明：

  * ST_POLYGON()：几何面类构造方法。
  * SURFACE()：返回 ST_Surface 几何类对象。



  7. ST_GeomCollection



基础几何体集合类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_GEOMCOLLECTION UNDER SYSGEO.ST_GEOMETRY(
    CONSTRUCTOR FUNCTION ST_GEOMCOLLECTION() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION ST_GEOMCOLLECTION(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT)NOT FINAL;
```

类说明：

  * ST_GEOMCOLLECTION()：几何体集合类构造方法。



  8. ST_Multipoint



多点类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_MULTIPOINT UNDER SYSGEO.ST_GEOMETRY(
    CONSTRUCTOR FUNCTION 	ST_MULTIPOINT() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION 	ST_MULTIPOINT(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT) NOT FINAL;
```

类说明：

  * ST_MULTIPOINT()：多点类构造方法。



  9. ST_MultiCurve



抽象多线类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_MULTICURVE UNDER SYSGEO.ST_GEOMETRY(
    CONSTRUCTOR FUNCTION 	ST_MULTICURVE() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION 	ST_MULTICURVE(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT) NOT FINAL;
```

类说明：

  * ST_MULTICURVE()：多线基类构造方法。



  10. ST_Multilinestring



多线类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_MULTILINESTRING UNDER SYSGEO.ST_MULTICURVE(
    CONSTRUCTOR FUNCTION 	ST_MULTILINESTRING() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION 	ST_MULTILINESTRING(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT,
    FUNCTION ST_MCURVE() RETURN SYSGEO.ST_MULTICURVE) NOT FINAL;
```

类成员说明：

  * ST_MULTILINESTRING()：多线类构造方法。
  * ST_MCURVE()：返回 ST_MultiCurve 几何类对象。



  11. ST_MultiSurface



抽象多面类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_MULTISURFACE UNDER SYSGEO.ST_GEOMETRY(
    CONSTRUCTOR FUNCTION 	ST_MULTISURFACE() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION 	ST_MULTISURFACE(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT) NOT FINAL;
```

类说明：

  * ST_MULTISURFACE()：多面基类构造方法。



  12. ST_Multipolygon



多面类。

语法如下：

```
CREATE OR REPLACE TYPE SYSGEO.ST_MULTIPOLYGON UNDER SYSGEO.ST_MULTISURFACE(
    CONSTRUCTOR FUNCTION 	ST_MULTIPOLYGON() RETURN SELF AS RESULT,
    CONSTRUCTOR FUNCTION 	ST_MULTIPOLYGON(SRID INT, WKB BLOB, TYPEID INT) RETURN SELF AS RESULT,
    FUNCTION ST_MSURFACE() RETURN SYSGEO.ST_MULTISURFACE) NOT FINAL;
```

类说明：

  * ST_MULTIPOLYGON()：多面类构造方法。
  * ST_MSURFACE()：返回 ST_Multisurface 几何类对象。



## 2.2 创建、检测、删除语句

用户在使用 DMGEO 包之前，需要提前调用系统过程 SP_INIT_GEO_SYS(1)创建 DMGEO 包，包创建成功后就可以使用空间数据类型以及包提供的方法。

```
SP_INIT_GEO_SYS(1);
```

如果 dmgeo 包已存在，调用 SP_INIT_GEO_SYS(2)，系统将只重建 dmgeo 包方法，而不影响现有的空间数据类型以及数据。

```
SP_INIT_GEO_SYS(2);
```

如果 dmgeo 包已存在，调用 SP_INIT_GEO_SYS(3)将只重建 SPATIAL_REF_SYS 表，而不影响现有的空间数据类型以及数据。

```
SP_INIT_GEO_SYS(3);
```

调用系统过程 SP_INIT_GEO_SYS(0)可以删除 DMGEO 包，任何与空间数据类型相关的表、函数、过程、触发器等对象均会被级联删除。

```
SP_INIT_GEO_SYS(0);
```

创建 DMGEO 包后，系统会创建表 GEOMETRY_COLUMNS 和 SPATIAL_REF_SYS，这两张表分别记录空间数据类型列创建情况和空间参考坐标系信息。GEOMETRY_COLUMNS 内容不需要用户干预，SPATIAL_REF_SYS 内容可以通过 DMGEO 包提供的存储过程 ST_ADD_SPATIAL_REF 和 ST_DEL_SPATIAL_REF 增加或删除。

建议在不需要保留现有空间数据时, 使用下列语句来重建 GEO 包。

```
SP_INIT_GEO_SYS(0);
SP_INIT_GEO_SYS(1);
```

需要保留现有空间数据时, 使用下列语句来重建 GEO 包。

```
SP_INIT_GEO_SYS(3);
SP_INIT_GEO_SYS(2);
```

本章将具体介绍创建、检测、删除 DMGEO 包的相关语句。

### 2.2.1 SP_INIT_GEO_SYS

创建或删除 DMGEO 系统包。

语法如下：

```
SP_INIT_GEO_SYS(
  CREATE_FLAG int
)
```

参数详解

  * CREATE_FLAG



为 0 表示删除该系统包；为 1 时表示创建 DMGEO 包。

返回值

无。

举例说明

例 创建 DMGEO 系统包。

```
SP_INIT_GEO_SYS(1);
```

> **注意**
>
> SP_INIT_GEO_SYS (create_flag)使用限制：  
>  1.不能在MPP全局模式下的存储过程中直接调用，在MPP LOCAL模式下可在存储过程中直接调用；  
> 2.不能在存储过程中带参数进行动态调用；  
> 3.Linux操作系统中使用DMGEO V1系统包时，系统的GLIBCXX版本至少为3.4.19（gcc4.8.5），GLIBC版本至少为2.12。



### 2.2.2 SF_CHECK_GEO_SYS

系统的 GEO 系统包启用状态检测。

语法如下：

```
int
SF_CHECK_GEO_SYS ()
```

返回值

0：未启用；1：已启用

举例说明

例 获得 GEO 系统包的启用状态。

```
SELECT SF_CHECK_GEO_SYS;
```

## 2.3 相关方法

### 2.3.1 几何体构造函数

几何体构造函数大体可分为两类：通过 WKT 信息与 SRID 信息构造和通过 WKB 信息与 SRID 信息构造。通常直接构造时选择通过 WKT 信息与 SRID 信息构造，从数据库获取信息时会使用 WKB 信息与 SRID 信息构造，因此下面仅给出第一种构造方式的例子。

达梦实现的几何体函数以包中方法的形式提供给用户，包名为 dmgeo。使用如下函数时，必须加上包名 dmgeo。

  1. ST_GeomFromText



根据 wkt 信息和 srid 信息构造空间数据基础类。

语法如下：

```
function ST_GeoMFromText(
  wkt clob,
  srid int
)return ST_Geometry;
```

参数详解

  * wkt：几何对象文本描述信息。
  * srid：空间参考坐标系 ID。



  2. ST_PointFromText



根据 wkt 信息和 srid 信息构造点类。

语法如下：

```
function ST_PointFromText (
  wkt clob,
  srid int
)return ST_Point;
```

参数详解

  * wkt：几何对象文本描述信息。
  * srid：空间参考坐标系 ID。



举例说明

```
dmgeo.ST_PointFromText('point empty' , 0); //空对象
dmgeo.ST_PointFromText('point (1 1)' , 0);
```

  3. ST_LineFromText



根据 wkt 信息和 srid 信息构造线类。

语法如下：

```
function ST_LineFromText (
  wkt clob,
  srid int
)return ST_Linestring;
```

参数详解

  * wkt：几何对象文本描述信息。
  * srid：空间参考坐标系 ID。



举例说明

```
dmgeo.ST_LineFromText('linestring empty' , 0); //空对象
dmgeo.ST_LineFromText('linestring (1 1, 2 2)' , 0);
```

  4. ST_PolyFromText



根据 wkt 信息和 srid 信息构造面类。

语法如下：

```
function ST_PolyFromText (
  wkt clob,
  srid int
)return ST_Polygon;
```

参数详解

  * wkt：几何对象文本描述信息。
  * srid：空间参考坐标系 ID。



举例说明

```
dmgeo.ST_PolyFromText('polygon empty' , 0); //空对象
dmgeo.ST_PolyFromText('polygon ((1 1, 1 2, 2 2, 2 1, 1 1))', 0);
```

  5. ST_MPointFromText



根据 wkt 信息和 srid 信息构造多点类。

语法如下：

```
function ST_MPointFromText (
  wkt clob,
  srid int
)return ST_Multipoint;
```

参数详解

  * wkt：几何对象文本描述信息。
  * srid：空间参考坐标系 ID。



举例说明

```
dmgeo.ST_MPointFromText('multipoint empty' , 0 ); //空对象
dmgeo.ST_MPointFromText('multipoint (1 1, 2 2)', 0 );
```

  6. ST_MLineFromText



根据 wkt 信息和 srid 信息构造多线类。

语法如下：

```
function ST_MLineFromText (
  wkt clob,
  srid int
)return ST_Multilinestring;
```

参数详解

  * wkt：几何对象文本描述信息。
  * srid：空间参考坐标系 ID。



举例说明

```
dmgeo.ST_MLineFromText('multilinestring empty' , 0); //空对象
dmgeo.ST_MLineFromText('multilinestring ((1 1,2 1),(4 4, 3 3))', 0);
```

  7. ST_MPolyFromText



根据 wkt 信息和 srid 信息构造多面类。

语法如下：

```
function ST_MPolyFromText (
  wkt clob,
  srid int
)return ST_Multipolygon;
```

参数详解

  * wkt：几何对象文本描述信息。
  * srid：空间参考坐标系 ID。



举例说明

```
dmgeo.ST_MPolyFromText('multipolygon empty' , 0); //空对象
dmgeo.ST_MPolyFromText('multipolygon (((1 1, 1 2, 2 2, 2 1, 1 1)), ((3 3, 3 
4, 4 4, 4 3, 3 3)))' , 0);
```

  8. ST_GeomFromWKB



根据 wkb 信息和 srid 信息构造空间数据基础类对象。

语法如下：

```
function ST_GeomFromWKB (
  wkB Blob,
  srid int
)return ST_GEOMETRY;
```

参数详解

  * wkb：几何对象序列化描述信息。
  * srid：空间参考坐标系 ID。



  9. ST_PointFromWKB



根据 wkb 信息和 srid 信息构造点类。

语法如下：

```
function ST_PointFromWKB (
  wkB Blob,
  srid int
)return ST_Point;
```

参数详解

  * wkb：几何对象序列化描述信息。
  * srid：空间参考坐标系 ID。



  10. ST_LineFromWKB



根据 wkb 信息和 srid 信息构造线类。

语法如下：

```
function ST_LineFromWKB (
  wkB Blob,
  srid int
)return ST_Linestring;
```

参数详解

  * wkb：几何对象序列化描述信息。
  * srid：空间参考坐标系 ID。



  11. ST_PolyFromWKB



根据 wkb 信息和 srid 信息构造面类。

语法如下：

```
function ST_PolyFromWKB (
  wkB Blob,
  srid int
)return ST_Polygon;
```

参数详解

  * wkb：几何对象序列化描述信息。
  * srid：空间参考坐标系 ID。



  12. ST_MPointFromWKB



根据 wkb 信息和 srid 信息构造多点类。

语法如下：

```
function ST_MPointFromWKB (
  wkB Blob,
  srid int
)return ST_Multipoint;
```

参数详解

  * wkb：几何对象序列化描述信息。
  * srid：空间参考坐标系 ID。



  13. ST_MLineFromWKB



根据 wkb 信息和 srid 信息构造多线类。

语法如下：

```
function ST_MLineFromWKB (
  wkB Blob,
  srid int
)return ST_Multilinestring;
```

参数详解

  * wkb：几何对象序列化描述信息。
  * srid：空间参考坐标系 ID。



  14. ST_MPolyFromWKB



根据 wkb 信息和 srid 信息构造多面类。

语法如下：

```
function ST_MPolyFromWKB (
  wkB Blob,
  srid int
)return ST_Multipolygon;
```

参数详解

  * wkb：几何对象序列化描述信息。
  * srid：空间参考坐标系 ID。



  15. ST_CreateCircle



根据几何体构造圆/椭圆。根据给定几何体的最左边、最上边、最右边和最下边四个边界点构建一个方形或矩形，再构建出该方形或矩形的内切圆或椭圆。

语法如下：

```
function ST_CreateCircle(
  geo	sysgeo.ST_Geometry,
  n		int
)return sysgeo.ST_Geometry;
```

参数详解

  * GEO：几何体对象。
  * N：指定生成圆（椭圆）的坐标个数，给定参数 N 将生成 N 个坐标点。N 个点均匀的排列在圆或椭圆上。考虑到 polygon 封闭的特征，给定参数 n 将生成(n+1)个点，因为必须保证 polygon 首尾相连，所以会有一个重复点。



返回值

一个 polygon 空间类型对象。

举例说明

例 构建一个圆。

```
declare
geo1 st_geometry;
geo3 st_geometry;
begin
    geo1 = dmgeo.st_geomfromtext( 'polygon ((0 0, 0 10, 10 10, 10 0, 0 0))', 
4296);
	geo3 = dmgeo.ST_CreateCircle(geo1, 6);
	select dmgeo.st_astext(geo3, 4);
end;
/
```

结果如下：

```
POLYGON((10 5,7.5 9.3301,2.5 9.3301,0 5,2.5 0.6699,7.5 0.6699,10 5))
```

  16. ST_CreateArc



根据几何体构建弧线(包括圆弧和椭圆弧)。根据给定几何体的最左边、最上边、最右边和最下边四个边界点构建一个方形或矩形，构建出该方形或矩形的内切圆或椭圆。进而根据起始角度和弧线的跨度在圆或椭圆的基础上构造出弧线。

语法如下：

```
function ST_CreateArc(
  geo		sysgeo.ST_Geometry,
  n			int,
  startAng	double,
  angExtent	double
) return sysgeo.ST_Geometry;
```

参数详解

  * GEO：几何体对象。
  * N：指定生成圆弧（椭圆弧）的坐标个数，给定参数 N 将生成 N 个坐标点。
  * startang：表示截取圆弧、椭圆弧线的起始角度。
  * angextent：表示弧线的跨度（由此可得到结束角度），它的取值范围为 0~2*π，不在此范围的取值一律视为 2*π。



返回值

一个 linestring 空间类型对象。

举例说明

例 构建一个弧线。

```
declare
geo1 st_geometry;
geo3 st_geometry;
begin
  geo1 = dmgeo.st_geomfromtext( 'polygon ((0 0, 0 10, 10 10, 10 0, 0 0))', 4296);
  geo3 = dmgeo.ST_CreateArc(geo1, 6, 0, 1.772);
  select dmgeo.st_astext(geo3, 4);
end;
/
```

结果如下：

```
LINESTRING(10 5,9.6893 6.7351,8.7957 8.2546,7.4304 9.3696,5.763 9.9414,4.0008 9.8991)
```

  17. ST_CreateArcPolygon



根据几何体构建扇形（包括椭圆扇形）。根据给定几何体的最左边、最上边、最右边和最下边四个边界点构建一个方形或矩形，构建出该方形或矩形的内切圆或椭圆。进而根据圆心（椭圆心）和半径，以及指定的起始角度和弧线的跨度在圆（或椭圆）的基础上构造出扇形。

语法如下：

```
function ST_CreateArcPolygon(
  geo		sysgeo.ST_Geometry,
  n			int,
  startAng	double,
  angExtent	double
) return sysgeo.ST_Geometry;
```

参数详解

  * GEO：几何体对象。
  * N ：指定生成扇形（包括椭圆扇形）的坐标个数。由于扇形的特殊性质，给定参数 n，将生成(n+2)个坐标点，额外的 2 个坐标点为圆心点，它作为起始和结束坐标而被重复一次。
  * startang：表示扇弧形的起始角度。
  * angextent：表示扇弧形的跨度，它的取值范围为 0~2*π，不在此范围的取值一律视为 2*π。



返回值

一个 polygon 空间类型对象。

举例说明

例 构建一个扇形。

```
declare
geo1 st_geometry;
geo3 st_geometry;
begin
  geo1 = dmgeo.st_geomfromtext( 'polygon ((0 0, 0 10, 10 10, 10 0, 0 0))', 
4296);
  geo3 = dmgeo.ST_CreateArcPolygon(geo1, 6, 0, 1.772);
  select dmgeo.st_astext(geo3, 4);
end;
/
```

结果如下：

```
POLYGON((5 5,10 5,9.6893 6.7351,8.7957 8.2546,7.4304 9.3696,5.763 9.9414,4.0008 9.8991,5 5))
```

  18. ST_CreateAnnulus



根据两个几何体构建一个圆环。首先，根据给定几何体的最左边、最上边、最右边和最下边四个边界点构建一个方形，两个方形的中心一定要求一致；其次，构建出方形的内切圆；最后，根据两个圆构造出一个圆环。

语法如下：

```
function ST_CreateAnnulus(
  geo1	sysgeo.ST_Geometry,
  geo2	sysgeo.ST_Geometry,
  n		int
) return sysgeo.ST_Geometry;
```

参数详解

  * GEO1：构建外圆的几何体对象。
  * GEO2：构建内圆的几何体对象。
  * N ：指定生成圆环的坐标个数。由于环有两个圆构造而成，因此给定参数 N，实际上会生成(2*N+2)个坐标点，环上的内外圆上各分布(N + 1)个点。



返回值

一个 polygon 空间类型对象。

举例说明

例 构建一个圆环。

```
declare
geo1 st_geometry;
geo2 st_geometry;
geo3 st_geometry;
begin
  geo1 = dmgeo.st_geomfromtext( 'polygon ((0 0, 0 10, 10 10, 10 0, 0 0))', 
4296);
  geo2 = dmgeo.st_geomfromtext( 'polygon ((3 3, 3 7, 7 7, 7 3, 3 3))', 
4296);
  geo3 = dmgeo.ST_CreateAnnulus(geo1,geo2, 6);
  select dmgeo.st_astext(geo3, 4);
end;
/
```

结果如下：

```
POLYGON((10 5,7.5 0.6699,2.5 0.6699,0 5,2.5 9.3301,7.5 9.3301,10 5),(7 5,6 6.7321,4 6.7321,3 5,4 3.2679,6 3.2679,7 5))
```

  19. ST_CreateAnnularSector



根据两个几何体构建一个扇环。首先，根据给定几何体构造出一个圆环；然后，根据圆心和内外圆半径，以及指定的起始角度和弧线的跨度在圆的基础上构造出扇形。

语法如下：

```
functionST_CreateAnnularSector(
  geo1		sysgeo.ST_Geometry,
  geo2		sysgeo.ST_Geometry,
  n			int,
  startAng	double,
  angExtent	double
) return sysgeo.ST_Geometry;
```

参数详解

  * GEO1：构建外圆的几何体对象。
  * GEO2：构建内圆的几何体对象。
  * N：指定生成扇环的坐标个数。由于扇环的特殊性质，给定参数 n，将生成(2*n +1)个坐标点，额外的 1 个坐标点，它作为起始和结束坐标而被重复一次。
  * startang：表示扇弧形的起始角度。
  * angextent：表示扇弧形的跨度，它的取值范围为 0~2*π，不在此范围的取值一律视为 2*π。



返回值

一个 polygon 空间类型对象。

举例说明

例 构建一个扇环。

```
declare
geo1 st_geometry;
geo2 st_geometry;
geo3 st_geometry;
begin
  geo1 = dmgeo.st_geomfromtext( 'polygon ((0 0, 0 10, 10 10, 10 0, 0 0))',4296);
  geo2 = dmgeo.st_geomfromtext( 'polygon ((3 3, 3 7, 7 7, 7 3, 3 3))', 4296);
  geo3 = dmgeo.ST_CreateAnnularSector(geo1,geo2, 6, 0, 1.772);
  select dmgeo.st_astext(geo3, 4);
end;
/
```

结果如下：

```
POLYGON((10 5,9.6893 6.7351,8.7957 8.2546,7.4304 9.3696,5.763 9.9414,4.0008 9.8991,4.6003 6.9597,5.3052 6.9766,5.9722 6.7478,6.5183 6.3018,6.8757 5.6941,7 5,10 5))
```

### 2.3.2 几何信息获取函数

  1. ST_Dimension



获取几何体对象的几何维度。

语法如下：

```
function ST_Dimension (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象的几何维度

  2. ST_CoordDim



获取几何体对象的坐标维度。

语法如下：

```
function ST_CoordDim (
  geo ST_Geometry
)return int;
```

参数详解

  * geo 几何体对象。



返回值

几何体对象的坐标维度

  3. ST_GeometryType



获取几何体对象的类型。

语法如下：

```
function ST_GeometryType (
  geo ST_Geometry
)return varchar(20);
```

参数详解

  * geo：几何体对象。



返回值

几何体对象的类型。

  4. ST_AsText



获取几何体对象的 wkt 格式文本描述信息。

语法如下：

```
function ST_AsText (
  geo ST_Geometry
)return clob;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象的文本表示形式。

  5. ST_AsText



获取指定坐标精度的几何体对象 wkt 格式文本描述信息。

语法如下：

```
function ST_AsText (
  geo			ST_Geometry,
  v_precision	int
)return clob;
```

参数详解

  * geo：几何体对象。
  * v_precision：坐标精度，即小数位的个数，精度取值范围 1~16，缺省为 16。



返回值

指定精度的几何体对象文本表示形式。

  6. ST_AsBinary



获取几何体对象的 wkb 格式序列化描述信息。

语法如下：

```
function ST_AsBinary (
  geo ST_Geometry
)returnblob;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象的序列化表示形式。

  7. ST_SRID



获取几何体对象的空间参考坐标系 ID。

语法如下：

```
function ST_SRID (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象的空间参考坐标系。

  8. ST_ISValid



几何体对象是否是合法对象。

语法如下：

```
function ST_IsValid(
  geo sysgeo.ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

0：非法；1：合法。

  9. ST_ISEmpty



获取几何体对象是否为空。

语法如下：

```
function ST_IsEmpty (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

0：非空；1：空。

  10. ST_ISSimple



判断几何体对象是否为简单几何体。

语法如下：

```
function ST_IsSimple (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

0：复杂几何体； 1：简单几何体。

  11. ST_X



获取几何体对象的 X 坐标。

语法如下：

```
function ST_X (
  geo ST_geometry
)return int;
```

参数详解

  * geo：几何体对象，必须是 POINT 几何类型。



返回值

几何体对象的 X 坐标值。

  12. ST_Y



获取几何体对象的 Y 坐标。

语法如下：

```
function ST_Y (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象，必须是 POINT 几何类型。



返回值

几何体对象的 Y 坐标值。

  13. ST_MinX



获取几何对象 X 坐标的最小值。

语法如下：

```
function ST_MinX (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象 X 坐标的最小值。

  14. ST_MAXX



获取几何体对象的 X 坐标的最大值。

语法如下：

```
function ST_MAXX (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象 X 坐标的最大值。

  15. ST_MINY



获取几何体对象 Y 坐标的最小值。

语法如下：

```
function ST_MINY (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象 Y 坐标的最小值。

  16. ST_MAXY



获取几何体对象 Y 坐标的最大值。

语法如下：

```
function ST_MAXY (
  geo ST_Geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象 Y 坐标的最大值。

  17. ST_StartPoint



获取几何体对象的起始点。

语法如下：

```
function ST_StartPoint (
  geo ST_Geometry
)return ST_POINT;
```

参数详解

  * geo：几何体对象，必须为 LINESTRING 几何类型。



返回值

几何体对象的起始点。

  18. ST_EndPoint



获取几何体对象的终点。

语法如下：

```
function ST_EndPoint (
  geo ST_geometry
)return ST_POINT;
```

参数详解

  * geo：几何体对象，必须为 LINESTRING 几何类型。



返回值

几何体对象的终点。

  19. ST_ISRing



获取几何体对象是否是环。

语法如下：

```
function ST_IsRing (
  geo ST_geometry
)return int;
```

参数详解

  * geo：几何体对象，必须为 LINESTRING 几何类型。



返回值

0：不是环；1：是环；NULL:参数为 NULL 时返回。

  20. ST_ISClosed



获取几何体对象是否闭合。

语法如下：

```
function ST_IsClosed (
  geo ST_geometry
)return int;
```

参数详解

  * geo ：几何体对象，必须为 LINESTRING 或 MULTILINESTRING 几何类型。但在处理 multilinestring 时，是对内部的 linestring 逐个调用 st_isclosed，内部的线与线不是作为一个整体进行 isclosed 判断的。



返回值

0：不闭合；1：闭合；NULL：参数为 NULL 时返回。

  21. ST_Length



获取几何体对象的长度。

语法如下：

```
function ST_Length (
  geo ST_geometry
)return int;
```

参数详解

  * geo：几何体对象，必须为 LINESTRING 或 MULTILINESTRING 几何类型。



返回值

几何体对象长度

  22. ST_Perimeter



获取几何体对象的周长。

语法如下：

```
function ST_Perimeter (
  geo ST_geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象周长。

  23. ST_NumPoints



获取几何体对象的节点数。

语法如下：

```
function ST_NumPoints (
  geo ST_geometry
)return int;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象节点数。

  24. ST_PointN



获取几何体对象的第 n 个节点。

语法如下：

```
function ST_PointN (
  geo	ST_geometry,
  n		int
)return ST_Point;
```

参数详解

  * geo：几何体对象。必须为 LINESTRING 几何类型。



返回值

几何体对象的第 n 个节点。

  25. ST_Centroid



获取几何体对象的中心点。

语法如下：

```
function ST_Centroid (
  geo ST_geometry
)return ST_Point;
```

参数详解

  * geo：几何体对象，必须是 POLYGON 或 MULTIPOLYGON 几何类型。



返回值

几何体对象的中心点。

  26. ST_PointOnSurface



获取几何体对象表面上的一点。

语法如下：

```
function ST_PointOnSurface (
  geo ST_geometry
)return ST_Point;
```

参数详解

  * geo：几何体对象，必须是 POLYGON 或 MULTIPOLYGON 几何类型。



返回值

几何体对象表面上的一点。

  27. ST_Area



获取几何体对象的面积。

语法如下：

```
function ST_Area (
  geo ST_geometry
)return double;
```

参数详解

  * geo：几何体对象，必须是 POLYGON 或 MULTIPOLYGON 几何类型。



返回值

几何体对象的面积。

  28. ST_ExteriorRing



获取几何体对象的外环。

语法如下：

```
function ST_ExteriorRing (
  geo ST_geometry
)return ST_Linestring;
```

参数详解

  * geo：几何体对象，必须是 POLYGON 几何类型。



返回值

几何体对象的外环。

  29. ST_NumInteriorRing



获取几何体对象的内环数。

语法如下：

```
function ST_NumInteriorRing (
  geo ST_geometry
)return int;
```

参数详解

  * geo：几何体对象，必须是 POLYGON 几何类型。



返回值

几何体对象的内环数。

30 ST_Boundary

获取几何体对象的边界。

语法如下：

```
function ST_Boundary (
  geo ST_Geometry
)return ST_geometry;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象的边界。

  31. ST_Envelope



获取几何体对象的矩形范围。

语法如下：

```
function ST_Envelope (
  geo ST_Geometry
)return ST_Polygon;
```

参数详解

  * geo：几何体对象。



返回值

几何体对象的矩形范围。

  32. ST_InteriorRingN



获取几何体对象的第 n 个内环。

语法如下：

```
function ST_InteriorRingN (
  geo	ST_geometry,
  n		int
)return ST_LineString;
```

参数详解

  * geo：几何体对象，必须是 POLYGON 几何类型。
  * n：第几个内环。



返回值

几何体对象的第 n 个内环。

33 ST_NumGeometries

获取几何对象个数。

语法如下：

```
function ST_NumGeometries (
  geo ST_geometry
)return int
```

参数详解

  * geo：几何体对象。



返回值

几何体对象个数，非 MULTI 类几何体返回 1。

  34. ST_GeometryN



获取第 n 个几何对象。

语法如下：

```
function ST_GeometryN (
  geo	ST_geometry,
  n		int
)return ST_Geometry
```

参数详解

  * geo：几何体对象。
  * n：第几个几何对象。



返回值

第 n 个几何对象。

### 2.3.3 空间关系判断函数

  1. ST_Equals



判断两个几何对象是否相同。

语法如下：

```
function ST_Equals (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：两个几何对象不相同；1：两个几何对象相同。

  2. ST_Disjoint



判断两个几何对象是否不相交。

语法如下：

```
function ST_Disjoint (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：两个几何对象相交；1：两个几何对象不相交。

  3. ST_Intersects



判断两个几何对象是否相交。

语法如下：

```
function ST_Intersects (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：两个几何对象不相交；1：两个几何对象相交。

  4. ST_Touches



判断两个几何对象是否接触，即存在边界点相同，内节点不相交。

语法如下：

```
function ST_Touches (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：两个几何对象不接触；1：两个几何对象接触。

  5. ST_Crosses



判断两个几何对象是否交叉，存在相同的点（不是几何对象完全一致）。不能处理面与面之间的交叉情况，可用于:点与线，点与面，线与面，线与线。

语法如下：

```
function ST_Crosses (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：两个几何对象不交叉；1：两个几何对象交叉。

举例说明

例 判断两个线对象是否交叉。

```
select dmgeo.ST_crosses(dmgeo.ST_MLineFromText('multilinestring ((0 5,4 1),(4 4,0 0))', 0),dmgeo.ST_MLineFromText('multilinestring ((1 0,1 2),(2 0, 0 4))', 0));
```

结果：

```
1
```

  6. ST_Within



判断对象是否完全包含，对象 g1 是否完全在 g2 的内部。

语法如下：

```
function ST_Within (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：g1 不完全在 g2 内部；1：g1 完全在 g2 内部。

  7. ST_Contains



判断对象是否包含，g2 不存在点在 g1 的外边界，且至少存在一个 g2 的内节点在 g1 内部。

语法如下：

```
function ST_Contains (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：g1 不包含 g2；1：g1 包含 g2。

  8. ST_Overlaps



判断两个几何对象是存在重叠，但并不被对方完全包含。

语法如下：

```
function ST_Overlaps (
  g1 ST_Geometry,
  g2 ST_Geometry
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

0：两个几何对象不存在重叠；1：两个几何对象存在重叠。

  9. ST_Relate



判断两个几何对象是否满足 DE-9IM 字符串关系。

语法如下：

```
function ST_Relate (
  g1 ST_Geometry,
  g2 ST_Geometry,
  pattern char(9)
) return int;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。
  * pattern：DE-9IM 字符串。



返回值

0：g1,g2 不满足 pattern 指定的关系；1：g1,g2 满足 pattern 指定的关系。

### 2.3.4 几何运算函数

  1. ST_Distance



获取几何对象间的最短距离。

语法如下：

```
function ST_Distance (
  g1 ST_Geometry,
  g2 ST_Geometry
) return double;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

几何对象间最短距离。

  2. ST_Intersection



获取几何对象的交集。

语法如下：

```
function ST_Intersection (
  g1 ST_Geometry,
  g2 ST_Geometry
) return ST_Geometry;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

几何对象的交集。

  3. ST_Difference



获取几何对象的差集。

语法如下：

```
function ST_Difference (
  g1 ST_Geometry,
  g2 ST_Geometry
) return ST_Geometry;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

g1 与 g2 的差集。

  4. ST_Union



获取几何对象的并集。

语法如下：

```
function ST_Union (
  g1 ST_Geometry,
  g2 ST_Geometry
) return ST_Geometry;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

g1 与 g2 的并集。

  5. ST_UNION_AGG



获取一列几何对象数据的并集。使用时无需添加 sysgeo 前缀。

语法如下：

```
FUNCTION ST_UNION_AGG(
	geom SYSGEO.ST_GEOMETRY SET，
) RETURN WKB;
```

参数详解

  * geom：几何对象数据列的列名。



返回值

几何对象的 wkb 数据。如需得到 sysgeo.ST_geometry 类型数据，需配合 ST_GEOFROMWKB 函数使用。

举例说明

例 1 ST_GeomFromWKB 不指定 srid，则 srid 默认为 0.

```
select dmgeo.ST_ASTEXT(dmgeo.ST_GeomFromWKB(ST_UNION_AGG(geom))) from T1;
```

例 2 ST_GeomFromWKB 指定 srid。

```
select dmgeo.ST_ASTEXT(dmgeo.ST_GeomFromWKB(ST_UNION_AGG(geom)，4326)) from T1;
```

  6. ST_SymDifference



获取几何对象的差异集。

语法如下：

```
function ST_SymDifference (
  g1 ST_Geometry,
  g2 ST_Geometry
) return ST_Geometry;
```

参数详解

  * g1：几何对象。
  * g2：几何对象。



返回值

g1 与 g2 的差异集。

  7. ST_Buffer



获取代替几何对象 g1 的几何对象，其到 g1 的距离小于等于 d。

语法如下：

```
function ST_Buffer (
  g1 ST_Geometry,
  d double
) return ST_Geometry;
```

参数详解

  * g1：几何对象。
  * d：距离。



返回值

几何对象 g1 以 d 为半径的外包几何对象。

  8. ST_ConvexHull



获取几何对象凸壳。

语法如下：

```
function ST_ConvexHull (
  g1 ST_Geometry
) return ST_Geometry;
```

参数详解

  * g1：几何对象。



返回值

外包几何对象。

  9. ST_Split



获取几何对象被另一几何对象切割后的几何对象。

支持 ST_LineString 类型对象被 ST_Point/ST_Multipoint、ST_LineString/ST_Multilinestring 以及 ST_Polygon/ST_Multipolygon 类型对象切割。

支持 ST_Polygon/ST_Multipolygon 类型对象被 ST_LineString/ST_Multilinestring 类型对象切割。

语法如下：

```
FUNCTION ST_SPLIT (
  G1  ST_GEOMETRY,
  G2  ST_GEOMETRY
) RETURN ST_GEOMETRY;
```

参数详解

  * g1：被切割的几何对象。
  * g2：切割 g1 的几何对象。



返回值

切割结果的几何对象集合。

  10. ST_MakeValid



在不丢失顶点坐标的情况下，尝试使一个无效的几何对象有效。

支持的对象类型包括：ST_Point/ST_Multipoint、ST_LineString/ST_Multilinestring、ST_Polygon/ST_Multipolygon 以及 ST_GeomCollection。

语法如下：

```
FUNCTION ST_MAKEVALID (
  G1  ST_GEOMETRY
) RETURN ST_GEOMETRY;
```

参数详解

  * g1：几何对象。



返回值

处理结果的几何对象。

  11. ST_AsGEOJSON



将几何对象转换成 JSON 格式。

语法如下：

```
FUNCTION ST_ASGEOJSON (
  G1	ST_GEOMETRY
) RETURN CLOB;
```

参数详解

  * g1：几何对象。



返回值

JSON 格式的几何对象。

  12. ST_Transform



将几何对象转换到新的空间参考坐标系。

使用 ST_Transform 之前，用户须提前安装好坐标转换软件 PROJ，或者直接使用 DM 系统提供的第三方坐标转换动态链接库。

在不同的操作系统环境中，第三方坐标转换动态链接库用法略有不同。在 Linux 环境中，第三方坐标转换动态链接库 libproj.so 和 libsqlite3.so 存放于 bin/thirdparty 目录中。当用户需要使用 ST_TRANSFORM 功能时，需要主动将其移动至/bin 目录下。在 Windows 环境中，用户无需额外操作，可直接使用第三方坐标转换动态链接库。

语法如下：

```
FUNCTION ST_TRANSFORM (
  G1	ST_GEOMETRY,
  SRID	INT
) RETURN ST_GEOMETRY;
```

参数详解

  * g1：几何对象。
  * srid：空间参考坐标系 ID。



返回值

新空间参考坐标系中的几何对象。

  13. ST_SetSRID



设置几何对象的空间参考坐标系 ID。

语法如下：

```
FUNCTION ST_SETSRID (
  G1	ST_GEOMETRY,
  SRID	INT
) RETURN ST_GEOMETRY;
```

参数详解

  * g1：几何对象。
  * srid：空间参考坐标系 ID。



返回值

空间参考坐标系 ID 为指定值的几何对象。

  14. ST_SimplifyPreserveTopology



获取几何对象的简化版本。使用 Douglas-Peucker 算法进行简化，避免构造无效的几何对象。

语法如下：

```
FUNCTION ST_ SimplifyPreserveTopology (
  G1			ST_GEOMETRY,
  TOLERANCE		DOUBLE
) RETURN ST_GEOMETRY;
```

参数详解

  * g1：几何对象。
  * tolerance：简化阈值。



返回值

简化后的几何对象。

### 2.3.5 其他过程函数

  1. ST_ADD_SPATIAL_REF



向 SPATIAL_REF_SYS 表中插入一条空间参考系信息。

语法如下：

```
procedure ST_ADD_SPATIAL_REF(
  V_SRID INTEGER,
  V_AUTH_NAME VARCHAR,
  V_AUTH_SRID INTEGER,
  V_SRTEXT VARCHAR
);
```

参数详解

  * V_SRID：空间参考系 ID。
  * V_AUTH_NAME：空间参考系名称。
  * V_AUTH_SRID：空间参考系代码。
  * V_SRTEXT：空间参考系的文本格式描述。



  2. ST_DEL_SPATIAL_REF



向 SPATIAL_REF_SYS 表中删除一条空间参考系信息。

语法如下：

```
procedure ST_DEL_SPATIAL_REF(
  V_SRID INTEGER
);
```

参数详解

  * V_SRID：空间参考系 ID。



  3. ST_geo_valid_check



语法如下：

```
function ST_geo_valid_check(geo sysgeo.ST_Geometry) return int;
```

功能说明

检验空间类型对象数据是否是有效。

参数详解

  * geo：空间类型对象。



  4. ST_UPDATE_SRID



更新列的参考系 SRID，可在 GEOMETRY_COLUMNS 表中查询到更新后列的 SRID。

语法如下：

```
PROCEDURE ST_UPDATE_SRID(
  V_SCH VARCHAR,
  V_TAB VARCHAR,
  V_COL VARCHAR,
  NEW_SRID INTEGER
);
```

参数详解

  * V_SCH：待修改列所在表的模式名。
  * V_TAB：待修改列所在表表名。
  * V_COL：待修改列列名。
  * NEW_SRID：用以更新的新值 SRID。



## 2.4 JDBC 访问 DM 空间类型数据

本章节主要介绍如何利用 JDBC 访问 DM 数据库中的空间类型数据。

### 2.4.1 使用说明

DM 数据库中最基本的空间数据类型为 ST_Geometry（具体请参见 [2.1 数据类型](https://eco.dameng.com/document/dm/zh-cn/pm/dmgeo-package.html#2.1%20%E6%95%B0%E6%8D%AE%E7%B1%BB%E5%9E%8B)），而 ST_Geometry 的类型为 STRUCT，因此 JDBC 对空间类型数据的访问方法与对 STRUCT 类型数据的访问方法相同。

JDBC 连接 DM 数据库后，访问空间类型数据的方法如下：

数据库准备数据：

```
CREATE TABLE sub_types(
	id int,
	sub_mpolygon ST_Multipolygon
);
INSERT INTO sub_types VALUES (1,dmgeo.ST_MPolyFromText ('multipolygon(((10 10, 10 20, 20 20, 20 15, 10 10)), ((50 40, 50 50, 60 50, 60 40, 50 40)))' , 4269));
INSERT INTO sub_types VALUES (2,dmgeo.ST_MPolyFromText('multipolygon (((1 1, 1 2, 2 2, 2 1, 1 1)), ((3 3, 3 4, 4 4, 4 3, 3 3)))' , 4269));
COMMIT;
```

  1. 获取 DM 数据库中的空间类型数据。



使用标准 JDBC 功能函数 getObject()检索数据表中的空间类型数据，getObject()返回对象类型为 java.lang.Object，由于 DM 数据库中的空间数据类型实质为 STRUCT 类型，因此需将 java.lang.Object 类型对象强制转换为 java.sql.Struct 类型对象。

例  获取 sub_types 表中的 ST_Multipolygon 类型数据。

```
ResultSet rs = stmt.executeQuery("select sub_mpolygon from sub_types;");
java.sql.Struct struct = (jav.sql.Struct)rs.getObject(1);
```

  2. 获取空间类型数据的成员属性信息。



获取 java.sql.Struct 类型对象中的成员属性需要使用 getAttributes()方法。

例  获取例 1 中查询结果对象 struct 的 SRID、GEO_WKB 以及 GEO_TYPEID 属性信息。

```
Object[] attrs = struct.getAttributes(); //获取结构体中的成员
int srid = (Integer)attrs[0];	//获取SRID属性信息
Blob blob = (Blob)attrs[1];		//获取GEO_WKB属性信息
int type = (Integer)attrs[2];	//获取GEO_TYPEID属性信息
```

  3. 解析 GEO_WKB 属性信息。



GEO_WKB 属性信息的格式为标准 WKB 格式，可以使用通用的 WKB 解析工具进行解析。JTS 是 JAVA 处理空间类型数据的通用组件，可用于解析 GEO_WKB 属性信息。JTS 的详细介绍可参考 https://github.com/locationtech/jts。

例 1  使用 JTS 解析 GEO_WKB 属性信息，获得 org.locationtech.jts.geom.Geometry 类型对象。

```
WKBReader reader = new WKBReader();
Geometry geometry = reader.read(blob.getBytes(1, (int)blob.length()));
```

也可使用 JTS 将 org.locationtech.jts.geom.Geometry 类型对象转换成 WKB 格式数据。

例 2  将 org.locationtech.jts.geom.Geometry 类型对象转换成 WKB 格式数据。

```
WKBWriter writer = new WKBWriter();
byte[] bytes = writer.write(geometry);
```

  4. 创建 java.sql.Struct 类型的空间类型数据。



可以根据空间类型数据的属性信息创建 java.sql.Struct 类型对象。

例  创建 ST_Multipolygon 类型的 java.sql.Struct 类型对象 struct2。

```
Object[] attrs = new Object[3];
attrs[0] = srid;
attrs[1] = DmdbBlob.newInstanceOfLocal(bytes, (DmdbConnection)conn);
attrs[2] = 6;
//通过指定空间数据类型名称和结构体成员创建java.sql.Struct类型对象
java.sql.Struct struct2 = conn.createStruct("ST_MULTIPOLYGON", attrs);
```

  5. 将 java.sql.Struct 类型的空间类型数据插入数据表。



使用标准 JDBC 功能函数 setObject()将 java.sql.Struct 类型对象绑定到 SQL 语句后，执行该 SQL 语句即可将 java.sql.Struct 类型的空间类型数据插入数据表。

例  将 java.sql.Struct 类型对象 struct2 插入数据表 sub_types。

```
PreparedStatement pstmt = conn.prepareStatement("insert into sub_types(sub_mpolygon) values(?)");
pstmt.setObject(1, struct2);
pstmt.execute();
```

### 2.4.2 举例说明

下面列出一个完整的 JDBC 访问 DM 空间类型数据的示例以供参考。

```
public static void test_spatial() throws SQLException, org.locationtech.jts.io.ParseException
{
//创建数据库连接
Connection conn = getConnection();   
Statement stmt = conn.createStatement();   
//获取数据表sub_types中的空间类型数据
ResultSet rs = stmt.executeQuery("select sub_mpolygon from sub_types;");  
while (rs.next())  
{  
	// 将空间类型数据转换为STRUCT类型后，获取结构体中的成员属性信息
	Struct struct = (Struct)rs.getObject(1); 
	Object[] attrs = struct.getAttributes(); 
	int srid = (Integer)attrs[0]; 
	Blob blob = (Blob)attrs[1]; 
	int type = (Integer)attrs[2]; 

	// 解析GEO_WKB属性信息
	WKBReader reader = new WKBReader();  
	Geometry geometry = reader.read(blob.getBytes(1, (int)blob.length()));   
	System.out.println(geometry);

	// 创建ST_Multipolygon空间类型的struct类型对象
	WKBWriter writer = new WKBWriter();
	attrs[1] = DmdbBlob.newInstanceOfLocal(writer.write(geometry), (DmdbConnection)conn);
	struct = conn.createStruct("ST_MULTIPOLYGON", attrs); 

	//将创建好的struct类型对象插入数据表sub_types
	PreparedStatement pstmt = conn.prepareStatement("insert into sub_types(sub_mpolygon) values(?)");
	pstmt.setObject(1, struct);
	pstmt.execute();
}  
rs.close();
stmt.close();
conn.close();
}
```

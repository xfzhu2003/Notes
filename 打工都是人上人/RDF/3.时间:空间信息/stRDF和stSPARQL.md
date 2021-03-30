# stRDF和stSPARQL

## 一、Introduction in stRDF and stSPARQL

### 1. OCG标准

#### 1.1 参考坐标系

- 地理坐标系
  - 三维系统
  - 经度、维度、高程
- 投影坐标系
  - 二维系统
  - 将三维椭球转换为二维平面
  - 横向墨卡托（UTM）系统

#### 1.2 WTK

使用WKT表示的几何图形具有以下特性：

- 所有几何图形都是拓扑闭合的，这意味着构成几何图形边界的所有点都假定属于几何图形，即使它们可能没有在几何图形中显式表示。
- 几何图形中的所有坐标都在同一坐标系中。
- 对于R3和R4中存在的几何对象，空间操作将根据其“地图几何”进行，即它们在R2上的投影。 因此，z和m值不会反映在计算中（例如，当调用函数相等，相交时等）或新的几何值的生成中（例如，当调用函数缓冲区，最小边界框等时）。 因此，WKT规范本质上是关于二维几何的，但是它也允许与这些几何和函数关联的z和m值访问它们。

让我们为图2.2中描述的每个类提供更多信息：

- Point。点代表坐标空间中的单个位置。一个点有x和y坐标值，可能有z和m取决于关联的坐标参考系。
- Curve。曲线是一维几何体。类曲线的子类定义了点之间使用的插值类型。
- LineString。线串是类曲线的一个子类，在点之间使用线性插值。如果一个线串的起点与终点相等，则该线串是闭合的。一个线串如果没有自交，则很简单。
- Line。线是一个精确两点的线串。
- LinearRing。线性环是既封闭又简单的线串。
- Surface。曲面是二维几何。此类是抽象的（即，可能无法实例化）。一个简单的表面可能包含一个“贴片”，该贴片具有一个“外部”边界和0个或多个“内部”边界（例如，一个带孔的多边形）。
- Polygon。多边形是一个简单的平面。它恰好具有一个外部边界，并且可能具有多个不相交的内部边界。每个多边形在拓扑上都是封闭的，并且没有两个边界（内部或外部）交叉。但是，两个边界可能在一个点处相交，但仅作为切线。多边形的内部是连接的点集，而带孔的多边形的外部未连接。
- Triangle。三角形是具有3个不同的非共线顶点且没有内部边界的多边形。
- Polyhedral Surface。多面体表面是共享公共边界线段的多边形的连续集合。每对接触的多边形都具有一个公共边界，该边界表示为线串的有限集合。每个这样的线串是最多2个多边形面片的边界的一部分。
- Triangulated Irregular Network。三角不规则网络是仅由三角形补丁组成的多面体表面。
- Geometry Collection。几何集合是一组不同的几何。
- MultiPoint。这是一个几何集合，其元素是不相连的点。
- MultiCurve。多曲线是其元素为曲线的几何集合。
- MultiLineString。多线串是一种几何集合，其元素是线串。
- MultiSurface。多重表面是二维几何集合，其元素是表面。任何两个表面的几何内部可能不相交。任何两个表面的边界可能不会交叉，但可能会在有限的点上接触。
- MultiPolygon。一个多多边形是一个多面集合，它的元素是多边形。每个多边形的边界不得相交。

#### 1.3 OpenGIS简单的特性访问

定义的函数：

1. ST_Dimension(A:Geometry):Integer。返回几何体的固有维数A，它必须小于或等于坐标维数。
2. ST_GeometryType(A:Geometry):String。返回[OGC10d]中定义的Geometry的可实例子类型的名称，其中几何图形A是可实例化成员。
3. ST_AsText(A:Geometry):String。将几何图形导出为其WKT表示。
4. ST_AsBinary(A:Geometry):Binary。将几何图形导出为其WKB（Well-Known Binary）表示。
5. ST_SRID(A:Geometry):Integer。返回几何A的坐标参考系统标识符。
6. ST_IsEmpty(A:Geometry):Boolean。如果几何图形为空几何图形则返回true。否则，返回false。
7. ST_IsSimple(A:Geometry):Boolean。如果几何体没有异常的几何点，如自交或自切，则返回true。否则，返回false。

定义了以下SQL函数，用于测试两个几何图形之间的拓扑空间关系，这些功能对应于[CSE94]中定义的Egenhofer和Clementini的尺寸扩展9交叉模型的关系：

1. ST_Equals(A:Geometry, B:Geometry):Boolean。如果几何图形A“在空间上等于” 几何图形B，则返回true。否则，它返回false。
2. ST_Disjoint(A:Geometry, B:Geometry):Boolean。如果几何图形A与几何图形B“在空间上不相交”，则返回true。否则返回false。
3. ST_Intersects(A:Geometry, B:Geometry):Boolean。如果几何图形A与几何图形B“空间相交”，则返回true。否则返回false。
4. ST_Touches(A:Geometry, B:Geometry):Boolean。如果几何图形A“空间接触” 几何图形B，则返回true。否则，它返回false。
5. ST_Crosses(A:Geometry, B:Geometry):Boolean。如果几何图形A“空间穿越”几何图形b返回true。否则返回false。
6. ST_Within(A:Geometry, B:Geometry):Boolean。如果几何图形A在几何图形B的“空间内”则返回true。否则returnsfalse。
7. ST_Contains(A:Geometry, B:Geometry):Boolean。如果几何图形A“在空间上包含”几何图形B则返回true。否则返回false。
8. ST_Overlaps(A:Geometry, B:Geometry):Boolean。如果几何图形A与几何图形B“在空间上重叠”则返回true。否则返回false。
9. ST_Relate(A:Geometry, B:Geometry, intersectionPatternMatrix:String):Boolean。根据[CFO93，CSE94]中的Egenhofer和Clementini相交模式矩阵（DE-9IM），通过测试两个几何图形的内部、边界和外部之间的相交，如果几何图形与几何图形“空间相关”，则返回true。

定义了以下SQL函数，用于从现有几何体构造新的几何体对象：

1. ST_Boundary(A:Geometry):Geometry, 返回一个几何图形，该几何图形是geometryA的边界。
2. ST_Envelope(A:Geometry):Geometry, 返回一个几何，该几何是输入geometryA的最小边界框。
3. ST_Intersection(A:Geometry, B:Geometry):Geometry, 表示几何图形A和B的点集相交的图形。
4. ST_Union(A:Geometry, B:Geometry):Geometry, 返回代表geometryesA和B的点集并集的几何。
5. ST_Difference(A:Geometry, B:Geometry):Geometry, 返回表示geometry e和B的点集差的geomtry。
6. ST_SymDifference(A:Geometry, B:Geometry):Geometry, 返回一个几何图形，该几何图形表示几何图形A和B的点集对称差。
7. ST_ConvexHull(A:Geometry):Geometry, 返回代表几何图形A的凸包的几何图形。
8. ST_Buffer(A:Geometry, distance:Double):Geometry, 返回一个几何图形，该几何图形表示与几何图形A的距离小于或等于distance的所有点。在该几何坐标系下进行了相应的计算。

ST_Distance(A:Geometry, B:Geometry):Double SQL函数定义用于计算两个几何图形之间的最短距离。计算出的标量值对应于在它们的参考系的单位系统中测量的这两个几何图形的最短距离。

标准ISO 13249 SQL/MM是一个用于SQL的多媒体和其他应用扩展的国际标准。该标准的第3部分定义了一组类型和方法，用于表示、处理、存储和查询关系数据库中的空间数据[Sto03, ME01]。空间数据的SQL / MM标准与此处预先提出的OpenGIS简单功能访问标准非常接近，实际上，这两项工作相互影响。因此，我们在本文档中不会提供任何详细信息。

#### 1.4 地理标记语言

地理标记语言（GML）[OGC07]是用于表示地理空间数据的最常见的基于XML的编码标准。 GML由OGC开发，它基于OGC抽象规范。 GML提供了XML模式，用于定义地理学中使用的各种概念：地理特征，几何形状，坐标参考系统，拓扑，时间和度量单位。最初，GML抽象模型基于RDF和RDFS，但后来该联盟决定使用XML和XMLSchema。 GML配置文件是GML的逻辑限制，可能对不想使用整个GML的应用程序有用。可以通过XML文档和XML模式或两者同时指定GML配置文件。

一些已提议公开使用的简介是：

（i）点配置文件，它定义了GML中的简单几何点。

（ii）GML简单功能配置文件，它是第2.1.3节中讨论的SQL简单功能的GML编码。

（iii）JPG的GML配置文件。

（iv）用于RSS的GML个人资料。

应该注意的是，GML配置文件与应用程序模式不同。这些配置文件是GML命名空间（OpenGIS GML）的一部分，并定义了GML的受限子集。应用程序模式是XML词汇表，这些词汇表是特定于应用程序的，并且在特定于应用程序的命名空间内有效。应用程序模式可以建立在特定的GML概要文件上，也可以使用完整的GML规范。

第2.1.3节中介绍的GML简单特征配置文件[OGC10a]和SQL的简单功能具有相似的结构，并描述了相似的几何。然而，GML简单特征配置文件也可以有三维的几何图形，而SQL的简单特性最多只能有二维的几何图形。在GML简单特征配置文件中，特征可以具有任意数量的几何属性，并且每个几何都应参考具有1、2或3个维度的坐标参考系统。

由于GML简单特征剖面可以用wkt表示类似的几何图形，因此我们在表2.2中给出了两个仅展示了一个点和一个多边形的GML表示的例子。GML表示几何图形的完整语法在[OGC07]中给出。



### 2. 新版本的stRDF和stSPARQL

在stRDF [KKN + 11]的最新版本中，我们使用OGC标准表示地理空间数据。引入了数据类型strdf:WKT和strdf:GML来表示使用第2.1.4节中介绍的OGC标准WKT和GML序列化的几何。

根据WKT的OGC规范，strdf: WKT数据类型被定义如下。该数据类型的词法空间包括可以从WKT规范中定义的WKT语法生成的有限长度的字符序列，后面可选地后跟分号和标识相应CRS的URI。默认情况被认为是WGS84坐标参考系统。值空间是WKT规范中定义的一组几何值。这些值是R2和R3的幂集并集的子集。

对strdf:GML的词法和值空间进行了类似的定义。在这种情况下，由于GML语法允许我们为我们定义的几何图形陈述坐标参考系，我们在strdf:GML文本中没有像在strdf:WKT文本中那样为它们单独的成分。数据类型strdf:geometry也被引入表示一个几何体的序列化，独立于所使用的序列化标准。数据类型strdf:geometry是数据类型strdf:WKT和strdf:GML的结合，它们的词汇空间和值空间都有适当的关系。

stRDF的原始版本[KK10]和新版本都对希望使用stRDF表示空间对象的语义Web开发人员提出了最低要求。他们要做的就是利用新的文字数据类型。这些数据类型(strdf:WKT,strdf:GML和strdf:geometry)可以用于应用程序所需的地理空间本体的定义，例如，类似于[Per08]中定义的本体。最近，在论文[BNM10]和GeoSPARQL标准[OGC10b]中也独立使用了基于空间文字的相同方法。数据类型strdf:geometry可用于想要使用stRDF的地理空间本体的定义，例如类似于[ Per08，OGC10b ]中定义的本体。现在我们给出一些用stRDF(使用Turtle符号)建模主题和空间数据的例子。

stRDF三元组来自GeoNames，它们代表关于希腊城镇奥林匹亚和扎查罗的信息，包括它们的地理近似。
```turtle
geonames:264637 geonames:name "Olympia";
		owl:sameAs dbpedia:Olympia_Greece;
		rdf:type noa:Town;
		strdf:hasGeometry "POLYGON((21.5 18.5, 23.5 18.5,23.5 21, 21.5 21, 21.5 18.5));
		<http://www.opengis.net/def/crs/EPSG/0/4326>"^^strdf:WKT.

geonames:251283 geonames:name "Zacharo";
		rdf:type noa:Town;
		strdf:hasGeometry "POLYGON((19 19, 21 19, 21 21,19 21, 19 19));		
		<http://www.opengis.net/def/crs/EPSG/0/4326>"^^strdf:WKT.
```

上面的三元组表示有关希腊城镇Olympia和Zacharo的一些信息，包括为我们的示例目的而修改的几何形状的近似值。GeoNames通常使用W3C基本地理词汇表，这是表示Web资源地理空间属性的基本本体和OWL词汇表。取而代之的是，我们使用数据类型trdf:WKT的类型化字面来定义WKT编码的城镇几何的多边形近似。数据类型strdf:WKT (分别为strdf:GML )与数据类型strdf:geometry，但用于表示空间对象的WKT (分别为GML )序列化。注意，URI用于表示这些几何的坐标在WGS84中使用我们上面讨论的语法给出。在我们的方法中，这也被视为默认情况，因此在此示例中，此URI的存在是可选的.

从雅典国家天文台的处理链中产生的代表燃烧区域的stRDF三元组：

```turtle
noa:BA1 a noa:BurntArea;
			noa:hasConfirmation noa:verified;
			strdf:hasGeometry "POLYGON((20 20, 20 22,22 22, 22 20, 20 20))"^^strdf:WKT.

noa:BA2 a noa:BurntArea;
			noa:hasConfirmation noa:verified;
			strdf:hasGeometry "POLYGON((23 18, 24 19,23 19, 23 18))"^^strdf:

WKT.noa:BA3 a noa:BurntArea.
			noa:hasConfirmation noa:verified;
			strdf:hasGeometry "POLYGON((20 15, 21 15,21 16, 20 15))"^^strdf:WKT.
```



以上三组描述了由雅典国家天文台产生的燃烧区域，并将在本章的以下例子中加以利用。

现在，让我们介绍新版本的stSPARQL的主要功能。stSPARQL是SPARQL 1.1的扩展，具有作为空间参数的参数，并且可以在SPARQL 1.1查询的SELECT，FILTER和HAVING子句中使用。空间项是空间文本(即带有数据类型strdf:geometry的类型化字面)、可以绑定到空间字面的查询变量、对空间字面进行集合操作(例如联合)的结果或对空间项进行几何操作(例如缓冲区)的结果。

stSPARQL的新版本通过2.1章中介绍的OpenGIS简单特性访问标准对SPARQL 1.1进行了扩展。我们通过为标准中定义的每个SQL函数定义URI并在SPARQL查询中使用它们来实现这一点。例如，对于OGC - SFA标准中定义的函数ST_ IsEmpty，我们引入SPARQL扩展函数xsd：boolean strdf：isEmpty ( strdf：geometry g )作为空间项g的参数，如果g是空几何，则返回true。类似地，我们为OGC-SFA(简单特征的拓扑关系)、[Ege89] (Egenhofer关系)和[CBGG97] (RCC-8关系)中定义的每个拓扑关系定义了一个布尔型SPARQL扩展函数。通过这种方式，stSPARQL支持用户可能熟悉的多种拓扑关系。使用这些功能，stSPARQL可以表达空间选择，即使用带有变量和常量的实参的FILTER函数查询(例如strdf:contains(?geo, "POINT(1 2)"^^strdf:WKT))。空间连接，即带参数为两个变量的Filter函数的查询 (例如,strdf:contains(?geoA, ?geoB)).

可以在SPARQL查询的SELECT子句中使用与OpenGIS简单功能访问标准的SQL功能相对应的SPARQL扩展功能。因此，在查询期间可以根据已有的空间文本生成新的空间文本。例如，要获取绑定到变量？GEO的空间文字的缓冲区，我们将使用以下选择表达式：SELECT (strdf:buffer(?geo,0.01) as ?geobuffer)

SPARQL 1.1中的一个新特性是支持聚合函数。在stSPARQL中，我们引入了以下三个处理地理空间数据的聚合函数:

- strdf:geometry strdf:union(set of strdf:geometry a)，返回一个几何对象，该对象是输入几何集的并集。
- strdf:geometry strdf:intersection(set of strdf:geometry a)，返回一个几何对象，该对象是输入几何的集合的交集。
- strdf:geometry strdf:extent(set of strdf:geometry a)，返回输入几何图形集合的最小边框的几何对象。

如果在应用中发现需要，将来可能会增加更多的聚合函数，stSPARQL还支持符合SPARQL的声明式更新语言SPARQL Update 1.1的更新操作(插入、删除和更新stRDF三元组)，这是W3C目前的建议。

以下示例演示了stSPARQL的功能。返回受火灾影响的城镇的名称。

```turtle
SELECT ?name
WHERE { ?town a noa:Town;
							geonames:name ?name;
							strdf:hasGeometry ?townGeom.
				?ba a noa:BurntArea;
							strdf:hasGeometry ?baGeom.
				FILTER(strdf:overlap(?townGeom,?baGeom))}
```

对示例3和3的数据集进行查询的结果如下所示:

| ?name     |
| --------- |
| "Olympia" |
| "Zacharo" |

上面的查询演示了如何在查询中使用拓扑函数。此查询的结果是其几何“空间重叠”与已燃烧区域相对应的几何形状的城镇的名称。

隔离位于针叶林中的部分燃烧区域。

```turtle
SELECT ?ba (strdf:intersection(?baGeom,strdf:union(?fGeom)) AS ?burnt)
WHERE { ?ba a noa:BurntArea. ?ba strdf:hasGeometry ?baGeom.
        ?f a noa:Area. ?f noa:hasLandCover noa:coniferousForest.
        ?f strdf:hasGeometry ?fGeom.
        FILTER(strdf:intersects(?baGeom,?fGeom)) }
GROUP BY ?ba ?baGeom
```

上面的查询测试烧过的区域是否与针叶林相交。在这种情况下，将根据燃烧区域进行分组。将与每个燃烧区域相对应的森林的几何形状合并，并计算与燃烧区域的交点并将其返回给用户。注意，只有strdf：unionis是SELECT子句中的聚合函数；strdf:intersection执行包含聚集结果和？baGeom值的计算，该值是确定分组的变量之一，根据该变量进行汇总计算。

对示例3和3的数据集进行查询的结果如下所示:

| ?ba             | ?burnt                                                       |
| :-------------- | :----------------------------------------------------------- |
| geonames:264637 | "POLYGON ((20 21, 20 22, 22 22, 22 21, 21.5 21,21.5 20, 21 20, 21 21, 20 21))"ˆˆstrdf:WKT |
| geonames:251283 | "MULTIPOLYGON (((23.5 18.5, 23 18, 23 18.5,23.5 18.5)), ((23.5 19, 24 19, 23.5 18.5, 23.519)))"ˆˆstrdf:WKT |

### 3. Strabon系统

#### 3.1 Strabon架构

我们决定以著名的RDF存储区Sesame为基础实施，即使Sesame不是可用的最高效的RDF存储区，但由于其开源特性，分层架构以及广泛的功能性，我们还是决定以它为基础进行实现以及集成“基于空间”的DBMS的能力，以利用其各种空间功能和运营商。Sesame的扩展是为了管理随后存储在PostGIS中的主题和空间RDF数据。PostGIS是postgresql的附加模块，通过添加对地理空间对象的支持来“在空间上启用”PostgreSQL。

当我们开始实现的时候，我们的目标是创建一个可以以透明的方式包含在Sesame软件堆栈中的层，这样它就不会影响Sesame的一系列功能，同时也可以从Sesame的新版本中获益。Strabon现在可以被视为Sesame风帆，可以放置在现有Sesame安装的顶部，而不影响其当前的功能。这种方法证明了我们的正确性，因为我们的第二轮系统开发与引入了SPARQL 1.1支持的Sesame版本相一致。

Strabon遵循Sesame的模块化架构，包括三个模块：

- Query Engine。Strabon的查询引擎对系统提出的stSPARQL查询进行评估。在接收到查询之后，将对其进行解析和优化。然后创建执行计划并执行，并将结果返回给客户端。为了处理stSPARQL查询，对Sesame的评估器和优化器进行了扩展。解析器和事务管理器没有被修改。

- Storage Manager。这个模块处理PostGIS RDBMSlayer中的数据存储。我们扩展了Sesame的组件，以便能够存储我们在3中描述的空间文本。

- PostGIS。PostGIS既用于存储stRDF数据，也用于评估stSPARQL查询。

## 二、Spatial and Temporal Data in RDF stRDF stSPARQL and GeoSPARQL

































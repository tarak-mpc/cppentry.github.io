---
layout:     post
title:      spark结构化数据处理：Spark SQL、DataFrame和Dataset
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
本文讲解Spark的结构化数据处理，主要包括：Spark SQL、DataFrame、Dataset以及Spark SQL服务等相关内容。本文主要讲解Spark 1.6.x的结构化数据处理相关东东，但因Spark发展迅速(本文的写作时值Spark 1.6.2发布之际，并且Spark 2.0的预览版本也已发布许久)，因此请随时关注Spark SQL官方文档以了解最新信息。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
文中使用Scala对Spark SQL进行讲解，并且代码大多都能在spark-shell中运行，关于这点请知晓。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
概述</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
相比于Spark RDD API，Spark SQL包含了对结构化数据和在其上的运算的更多信息，Spark SQL使用这些信息进行了额外的优化，使对结构化数据的操作更加高效和方便。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
有多种方式去使用Spark SQL，包括SQL、DataFrames API和Datasets API。但无论是哪种API或者是编程语言，它们都是基于同样的执行引擎，因此你可以在不同的API之间随意切换，它们各有各的特点，看你喜欢。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>SQL</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
使用Spark SQL的一种方式就是通过SQL语句来执行SQL查询。当在编程语言中使用SQL时，其返回结果将被封装为一个DataFrame。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>DataFrame</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
DataFrame是一个分布式集合，其中数据被组织为命名的列。它概念上等价于关系数据库中的表，但底层做了更多的优化。DataFrame可以从很多数据源构建，比如：已经存在的RDD、结构化文件、外部数据库、Hive表。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
DataFrame的前身是SchemaRDD，从Spark 1.3.0开始SchemaRDD更名为DataFrame。与SchemaRDD的主要区别是：DataFrame不再直接继承自RDD，而是自己实现了RDD的绝大多数功能。你仍旧可以在DataFrame上调用.rdd方法将其转换为一个RDD。RDD可看作是分布式的对象的集合，Spark并不知道对象的详细模式信息，DataFrame可看作是分布式的Row对象的集合，其提供了由列组成的详细模式信息，使得Spark SQL可以进行某些形式的执行优化。DataFrame和普通的RDD的逻辑框架区别如下所示：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/26e9e4ab9e4fb592f3b33d12b366e9e5.jpg" alt="数据" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
DataFrame不仅比RDD有更加丰富的算子，更重要的是它可以进行执行计划优化(得益于Catalyst SQL解析器)，另外Tungsten项目给DataFrame的执行效率带来了很大提升(不过Tungsten优化也可能在后续开发中加入到RDD API中)。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
但是在有些情况下RDD可以表达的逻辑用DataFrame无法表达，所以后续提出了Dataset API，Dataset结合了RDD和DataFrame的好处。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
关于Tungsten优化可以参见：Project Tungsten：让Spark将硬件性能压榨到极限</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>Dataset</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Dataset是Spark 1.6新添加的一个实验性接口，其目的是想结合RDD的好处(强类型(这意味着可以在编译时进行类型安全检查)、可以使用强大的lambda函数)和Spark SQL的优化执行引擎的好处。可以从JVM对象构造出Dataset，然后使用类似于RDD的函数式转换算子(map/flatMap/filter等)对其进行操作。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Dataset通过Encoder实现了自定义的序列化格式，使得某些操作可以在无需解序列化的情况下直接进行。另外Dataset还进行了包括Tungsten优化在内的很多性能方面的优化。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
实际上Dataset是包含了DataFrame的功能的，这样二者就出现了很大的冗余，故Spark 2.0将二者统一：保留Dataset API，把DataFrame表示为Dataset[Row]，即Dataset的子集。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>API进化</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Spark在迅速的发展，从原始的RDD API，再到DataFrame API，再到Dataset的出现，速度可谓惊人，执行性能上也有了很大提升。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
我们在使用API时，应该优先选择DataFrame &amp; Dataset，因为它的性能很好，而且以后的优化它都可以享受到，但是为了兼容早期版本的程序，RDD API也会一直保留着。后续Spark上层的库将全部会用 DataFrame &amp; Dataset，比如MLlib、Streaming、Graphx等。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
关于这三种API的更详细的讨论以及选择参见：Apache Spark: RDD, DataFrame or Dataset?</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
入门</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>起点之SQLContext</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
要想使用Spark SQL，首先你得创建一个SQLContext对象，在这之前你只需要创建一个SparkContext就行了，如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/c4c19cbd535ae5b83135bd83df024d91.jpg" alt="data" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
另外，你也可以使用HiveContext，它是SQLContext的超集，提供了一些额外的功能：使用HiveQL解析器、访问Hive用户定义函数、从Hive表读取数据。并且，你不需要安装Hive就可以使用HiveContext。不过将来版本的Spark SQL可能会逐步缩小SQLContext和HiveContext之间的差距。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
对于SQLContext，目前只有一个简单的SQL语法解析器sql，而对于HiveContext，则可以使用hiveql和sql两个解析器，默认是hiveql，我们可以通过如下语句来修改默认解析器：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/1b8caf060223889326e305733e208c2f.jpg" alt="Dataset" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
不过就目前来说，HiveQL解析器更加完善，因此推荐使用HiveQL。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>创建并使用DataFrame</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
通过SQLContext，应用程序可以从已经存在的RDD、结构化文件、外部数据库以及Hive表中创建出DataFrame。如下代码从JSON文件(该文件可以从Spark发行包中找到)创建出一个DataFrame：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/543346c1b4fe68cbd5eaef0a8235f802.jpg" alt="创建并使用DataFrame" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
DataFrame提供了一个领域特定语言(DSL)以方便操作结构化数据。下面是一些使用示例，更多更全的DataFrame操作参见Spark API文档中的org.apache.spark.sql.DataFrame：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/61a7ce8cbc511ef136b06d01413a4097.jpg" alt="提供" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
另外，org.apache.spark.sql.functions单例对象还包含了一些操作DataFrame的函数，主要有这几个方面：聚集操作、集合操作、字符串处理、排序、日期计算、通用数学函数、校验码操作、窗口操作、用户定义函数支持等等。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
在程序中执行SQL查询</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
我们可以通过在程序中使用SQLContext.sql()来执行SQL查询，结果将作为一个DataFrame返回：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/2cfd5e0cd86978d6eaf8014846a02750.jpg" alt="执行查询" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
我们还可以将DataFrame注册为一个临时表，然后就可以在其上执行SQL语句进行查询了，临时表的生命周期在与之关联的SQLContext结束生命之后结束。示例如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/f25e53bbe8d84edda0fc99dbcc2e2697.jpg" alt="临时表" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
创建并使用Dataset</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Dataset跟RDD相似，但是Dataset并没有使用Java序列化库和Kryo序列化库，而是使用特定Encoder来序列化对象。encoder通常由代码自动产生(在Scala中是通过隐式转换产生)，并且其序列化格式直接允许Spark执行很多操作，比如：filtering、sorting、hashing，而不需要解序列化。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/c9e36c65b1963a46180296d3924b852e.jpg" alt="创建使用" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
与RDD互操作</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Spark SQL支持两种不同的方法将RDD转换为DataFrame。第一种方法使用反射去推断包含特定类型对象的RDD的模式(schema)，该方法可以使你的代码更加精简，不过要求在你写Spark程序时已经知道模式信息(比如RDD中的对象是自己定义的case class类型)。第二种方法通过一个编程接口，此时你需要构造一个模式并将其应用到一个已存在的RDD以将其转换为DataFrame，该方法适用于在运行时之前还不知道列以及列的类型的情况。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
用反射推断模式</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Spark SQL的Scala接口支持将包含case class的RDD自动转换为DataFrame。case class定义了表的模式，case class的参数名被反射读取并成为表的列名。case class也可以嵌套或者包含复杂类型(如序列或者数组)。示例如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/d664b68a12ac8cee6da6622f204c8193.jpg" alt="推断模式" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
手动编程指定模式</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
当case class不能提前定义时(比如记录的结构被编码为字符串，或者当文本数据集被解析时不同用户需要映射不同的字段)，可以通过下面三步来将RDD转换为DataFrame：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
从原始RDD创建得到一个包含Row对象的RDD。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
创建一个与第1步中Row的结构相匹配的StructType，以表示模式信息。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
通过SQLContext.createDataFrame()将模式信息应用到第1步创建的RDD上。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/9e0f14904327c37bff4137211cc739fe.jpg" alt="指定模式" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
数据源</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
DataFrame可以当作标准RDD进行操作，也可以注册为一个临时表。将DataFrame注册为一个临时表，允许你在其上执行SQL查询。DataFrame接口可以处理多种数据源，Spark SQL也内建支持了若干种极有用的数据源格式(包括json、parquet和jdbc，其中parquet是默认格式)。此外，当你使用SQL查询这些数据源中的数据并且只用到了一部分字段时，Spark SQL可以智能地只扫描这些用到的字段。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
通用加载/保存函数</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
DataFrameReader和DataFrameWriter中包好一些通用的加载和保存函数，所有这些操作都将parquet格式作为默认数据格式。示例如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/dc7bf849dd2a3ce17e732bfd982bc6e1.jpg" alt="保存" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
手动指定选项</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
你也可以手动指定数据源和其他任何想要传递给数据源的选项。指定数据源通常需要使用数据源全名(如org.apache.spark.sql.parquet)，但对于内建数据源，你也可以使用它们的短名(json、parquet和jdbc)。并且不同的数据源类型之间都可以相互转换。示例如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/a1247f05b5b9297b5d21509ae44911b8.jpg" alt="指定选项" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
用SQL直接查询文件</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
也可以不用read API加载一个文件到DataFrame然后查询它，而是直接使用SQL语句在文件上查询：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
val df = sqlContext.sql(“SELECT * FROM parquet.`examples/src/main/resources/users.parquet`”)</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
保存到持久表</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
可以使用DataFrameWriter.saveAsTable()将一个DataFrame保存到一个持久化表，根据不同的设置，表可能被保存为Hive兼容格式，也可能被保存为Spark SQL特定格式，关于这一点请参见API文档。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
我们可以通过SQLContext.table()或DataFrameReader.table()来加载一个表并返回一个DataFrame。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
Parquet文件</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Parquet格式是被很多其他的数据处理系统所支持的列式数据存储格式。它可以高效地存储具有嵌套字段的记录，并支持Spark SQL的全部数据类型。Spark SQL支持在读写Parquet文件时自动地保存原始数据的模式信息。出于兼容性考虑，在写Parquet文件时，所有列将自动转换为nullable。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>加载数据</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/385e1e19507f142cb1d666d1c55e54eb.jpg" alt="文件查询" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<strong>下面是SQL示例：</strong></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/c98e68ae7f46d1e832a9b56b0a1f686b.jpg" alt="实例" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
分区发现</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
在很多系统中(如Hive)，表分区是一个通用的优化方法。在一个分区的表中，数据通常存储在不同的目录中，列名和列值通常被编码在分区目录名中以区分不同的分区。Parquet数据源能够自动地发现和推断分区信息。 如下是人口分区表目录结构，其中gender和country是分区列：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/9f168af1f679c8fc34cb95ee0cfa4107.jpg" alt="分区" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
当使用SQLContext.read.parquet 或 SQLContext.read.load读取path/to/table时，Spark SQL能够自动从路径中提取分区信息，返回的DataFrame的模式信息如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/6ebe26cf67c6003f367ce282b957fe4e.jpg" alt="模式信息" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
上述模式中，分区列的数据类型被自动推断。目前，支持的数据类型有数字类型和字符串类型。如果你不想数据类型被自动推断，可以配置spark.sql.sources.partitionColumnTypeInference.enabled，默认为true，如果设置为false，将禁用自动类型推断并默认使用字符串类型。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
从Spark 1.6.0开始，分区发现默认只发现给定路径下的分区。如果用户传递path/to/table/gender=male作为路径读取数据，gender将不被作为一个分区列。你可以在数据源选项中设置basePath来指定分区发现应该开始的基路径。例如，还是将path/to/table/gender=male作为数据路径，但同时设置basePath为path/to/table/，gender将被作为分区列。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
模式合并</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
就像ProtocolBuffer、Avro和Thrift，Parquet也支持模式演化(schema evolution)。这就意味着你可以向一个简单的模式逐步添加列从而构建一个复杂的模式。这种方式可能导致模式信息分散在不同的Parquet文件中，Parquet数据源能够自动检测到这种情况并且合并所有这些文件中的模式信息。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
但是由于模式合并是相对昂贵的操作，并且绝大多数情况下不是必须的，因此从Spark 1.5.0开始缺省关闭模式合并。开启方式：在读取Parquet文件时，设置数据源选项mergeSchema为true，或者设置全局的SQL选项spark.sql.parquet.mergeSchema为true。示例如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/a8babe5df390bff3129864fa05d99190.jpg" alt="模式" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
JSON数据</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
SQLContext.read.json()可以将RDD[String]或者JSON文件加载并转换为一个DataFrame。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
有一点需要注意的是：这里用的JSON文件并不是随意的典型的JSON文件，每一行必须是一个有效的JSON对象，如果一个对象跨越多行将导致失败。对于RDD[String]也是一样。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/9dafdebe03d38e83bb331b1733792ccb.jpg" alt="JSON数据" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
下面是SQL示例：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/407997153f4e1a4b8a91e412eef879fe.jpg" alt="数据实例" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
数据库</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Spark SQL也可以使用JDBC从其他数据库中读取数据，你应该优先使用它而不是JdbcRDD，因为它将返回的数据作为一个DataFrame，所以很方便操作。但请注意：这和Spark SQL JDBC服务是不同的，Spark SQL JDBC服务允许其他应用通过JDBC连接到Spark SQL进行查询。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
要在Spark SQL中连接到指定数据库，首先需要通过环境变量SPARK_CLASSPATH设置你的数据库的JDBC驱动的路径。例如在spark-shell中连接MySQL数据库，你可以使用如下命令：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/9b716294f1e49ce3632c1fcbfe9fedcb.jpg" alt="数据库命令" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
可以通过SQLContext.read.format(“jdbc”).options(…).load()或SQLContext.read.jdbc(…)从数据库中加载数据到DataFrame。如下示例，我们从MySQL数据库中加载数据：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/9c35fdd036fd162da73dc1e4d5a88b12.jpg" alt="加载数据" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
下面是SQL示例：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/d1b00513aa3d5d73ccbe28f170fd3573.jpg" alt="加载案例" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
JDBC驱动需要在所有节点的相同路径下都存在，因为是分布式处理嘛，就像Spark核心一样。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
有些数据库需要使用大写来引用相应的名字，好像Oracle中就需要使用大写的表名。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
分布式SQL引擎</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Spark SQL也可以扮演一个分布式SQL引擎的角色，你可以使用JDBC/ODBC或者Spark SQL命令行接口连接到它，并直接执行交互式SQL查询。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
运行Thrift JDBC/ODBC Server</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Spark SQL中实现的Thrift JDBC/ODBC Server跟Hive中的HiveServer2相一致。可以使用如下命令开启JDBC/ODBC服务，缺省情况下的服务监听地址为localhost:10000：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/07753f8181f44a252b9207f13e6dbd38.jpg" alt="分布式" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
这个脚本不仅可以接受spark-submit命令可以接受的所有选项，还支持–hiveconf 属性=值选项来配置Hive属性。你可以执行./sbin/start-thriftserver.sh –help来查看完整的选项列表。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
你可以使用beeline连接到上面已经开启的Spark SQL引擎。命令如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/6b83ffdb55ed895c236532f12314de8e.jpg" alt="AQL" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
连接到beeline需要输入用户名和密码。在非安全模式下，简单地输入你自己的用户名就可以了，密码可以为空。对于安全模式，参见beeline文档。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
运行Spark SQL CLI</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
Spark SQL CLI的引入使得在Spark SQL中可方便地通过Hive metastore对Hive进行查询。目前为止还不能使用Spark SQL CLI与Thrift JDBC/ODBC Server进行交互。这个脚本主要对本地开发比较有用，在共享的集群上，你应该让各用户连接到Thrift JDBC/ODBC Server。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
使用Spark SQL CLI前需要注意：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
将hive-site.xml配置文件拷贝到$SPARK_HOME/conf目录下。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
需要在./conf/spark-env.sh中的SPARK_CLASSPATH添加jdbc驱动的jar包。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
启动Spark SQL CLI的命令如下：</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<img src="http://tc.sinaimg.cn/maxwidth.800/tc.service.weibo.com/www_36dsj_com/46a9630c78880e087bb627b4dde3be53.jpg" alt="SQL命令" title="spark结构化数据处理：Spark &lt;wbr&gt;SQL、DataFrame和Dataset" style="border:0px;list-style:none;"></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
在启动spark-sql时，如果不指定master，则以local的方式运行，master既可以指定standalone的地址，也可以指定yarn。当设定master为yarn时(spark-sql –master yarn)时，可以通过http://master:8088 页面监控到整个job的执行过程。如果在./conf/spark-defaults.conf中配置了spark master的话，那么在启动spark-sql时就不需要再指定master了。spark-sql启动之后就可以在其中敲击SQL语句了。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
关于Spark SQL CLI的可用命令和参数，请敲击./bin/spark-sql –help以查看。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
性能调优</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
缓存数据在内存中</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
通过调用sqlContext.cacheTable(“tableName”)或dataFrame.cache()，Spark SQL可以将表以列式存储缓存在内存中。这样的话，Spark SQL就可以只扫描那些使用到的列，并且将自动压缩数据以减少内存占用和GC开销。你可以调用sqlContext.uncacheTable(“tableName”)将缓存的表从内存中移除。另外，你也可以在SQL/HiveQL中使用CACHE tablename和UNCACHE tablename来缓存表和移除已缓存的表。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
和缓存RDD时的动机一样，如果想在同样的数据上多次运行任务或查询，就应该把这些数据表缓存起来。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
将数据缓存在内存中，同样支持一些选项，参见Spark SQL官方文档性能调优部分。</p>
<h2 style="border:0px;list-style:none;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;background-color:rgb(75,140,202);">
其他调优相关参数</h2>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
还有其他一些参数可以用于优化查询性能，参见Spark SQL官方文档性能调优部分。</p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
<br></p>
<p style="border:0px;list-style:none;line-height:21px;color:rgb(50,62,50);font-family:'Microsoft YaHei', 'Helvetica Neue', SimSun;font-size:14px;background-color:rgb(75,140,202);">
End.</p>
            </div>
                </div>
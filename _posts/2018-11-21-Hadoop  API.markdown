---
layout:     post
title:      Hadoop  API
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div class="toc">
<div class="toc">
<ul><li>
<ul><li><a href="http://blog.csdn.net/allocator/article/details/55211473#hdfs-java-apiversion-273" rel="nofollow">HDFS JAVA APIversion 273</a></li><li><a href="http://blog.csdn.net/allocator/article/details/55211473#hbase-java-apiversion-124" rel="nofollow">HBASE JAVA APIversion 124</a></li><li><a href="http://blog.csdn.net/allocator/article/details/55211473#mapreduce-java-apiversion-273" rel="nofollow">MAPREDUCE JAVA APIversion 273</a></li></ul></li></ul></div>
</div>
<h2 id="hdfs-java-apiversion-273"><a name="t0"></a>HDFS JAVA API(version 2.7.3)</h2>
<p>HDFS 分布式文件系统JAVA API。与local的java 文件系统的API类似。</p>
<table><thead><tr><th align="left">Name</th>
<th align="left">Description</th>
</tr></thead><tbody><tr><td align="left">org.apache.hadoop.conf.configuration</td>
<td align="left">hadoop中java API使用最多的一个类，很多其他类会使用到配置文件,该类的实例代表内存中的配置文件.提供操作配置文件的方法。存储在配置文件中写入的键值对。一般使用构造函数构造实例即可，Configuration conf = new Configuration() 其他操作参数的API使用不多。</td>
</tr><tr><td align="left">org.apache.hadoop.fs.FileSystem</td>
<td align="left">文件系统中的抽象基类,HDFS对其实现为DistributedFileSystem代表整个文件系统。抽象类中提供两个方法open用于创建一个输入流FSDataInputStream,同样是一个抽象基类，其HDFS的实现为DFSInputStream。create用于创建一个输出流FSDataOutputStream。在HDFS中对于输出流的实现是DFSOutputStream。凡是需要使用到HDFS的读写都要使用这个抽象基类的实现。这个抽象基类还可以作为本地文件系统实现。作为本地文件系统实现的类是LocalFileSystem。重要方法copyFromLocalFile从本地拷贝文件到系统中。create创建新的文件，并且返回一个输出流。delete删除文件，可以递归删除文件夹中的内容。exists检测path中的内容是否存在。get方法，比较重要的方法，本身FileSystem是抽象的类，不可以实例化，get方法是FileSystem中存在的一个抽象的方法，返回FileSystem的实例，之后就可以用这个实例完成分布式文件系统的所有操作。这个方法一般是作为第一个调用的方法。get(Uri(可选),Configuration(org.apache.hadoop.conf.Configuration),user(可选))Uri这个变量用于指定实际访问的机器。分布式场景中使用FileSystem需要指定操作的文件系统。如果不指定会在本机的文件系统中去操作文件，所以Uri在此处是比较重要的变量。getStatus
 返回一个FsStatus实例表征文件系统的空间以及使用量。参数Path代表分区，为空或者不写则是表示根分区的使用量以及容量。容易与FileStatus搞混。一般调用方法getCapacity() and getUsed().listStatus返回一个FileStatus的Array。表征文件夹下的所有内容(文件或者子文件夹)的状态。操作对象为文件夹,非文件夹返回本身代表的FileStatus.该函数可以接收的参数类型比较多。Path与Path[] 区别在于path获取的是path内部的内容转换为FileStatus。而Path[]将这个list内容转换为FileStatus[]。两种调用都可以添加Filter进行过滤。isDirectory
 isFile后面接Path用于判断给定Path是文件还是文件夹。mkdirs(path)创建目录。rename(pathsrc,pathdst)改变path可以理解为在文件系统中移动文件</td>
</tr><tr><td align="left">org.apache.hadoop.fs.FSDataInputStream</td>
<td align="left">文件输入流的抽象基类</td>
</tr><tr><td align="left">org.apache.hadoop.fs.FSDataOutputStream</td>
<td align="left">文件输出流的抽象基类</td>
</tr><tr><td align="left">org.apache.hadoop.fs.Path</td>
<td align="left">表征文件系统中的文件或者文件夹路径的实例，纯粹路径没有其他功能，基本上FileSystem的方法中都有传递这个参数</td>
</tr><tr><td align="left">org.apache.hadoop.fs.FileStatus</td>
<td align="left">提供文件系统中的详细信息。反应文件组路径，大小拥有者等文件相关信息，但是不提供返回读写流的方法。所以需要借助于FileSystem类提供的方法完成文件的读写操作。</td>
</tr><tr><td align="left">org.apache.hadoop.fs.FsStatus</td>
<td align="left">文件代表文件系统的空间以及使用量,有getCapacity和getUsed方法调用</td>
</tr><tr><td align="left">java.net.URI</td>
<td align="left">URI类在hadoop文件系统中用于指定文件系统的模式，在创建FileSystem实例的时候需要用URI指定这个文件系统实例实际对应集群中的某一台机器。URI.create(Schema)</td>
</tr></tbody></table><p>综上所述。HDF HAVA操作的API主要是围绕FileSystem抽象基类展开。get用于创建实例。需要用URI指定系统的schema以及Configuration配置实例。文件创建使用create返回一个FSDataOutputStream 文件打开使用Open返回FSDataInputStream。返回的都是抽象类在具体系统的实现。FileStatus用是文件的状态抽象。</p>
<h2 id="hbase-java-apiversion-124"><a name="t1"></a>HBASE JAVA API(version 1.2.4)</h2>
<p>HBASE JAVA API</p>
<table><thead><tr><th align="left">Name</th>
<th align="left">Description</th>
</tr></thead><tbody><tr><td align="left">org.apache.hadoop.hbase.HBaseConfiguration</td>
<td align="left">HBase使用的Configuration对象，同样表示HBase的配置文件。创建方式略微不同使用静态的create()方法创建。因为HBaseConfiguration继承自Configuration所以返回类型可以是Configuration</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.HTableDescriptor</td>
<td align="left">HBase中table的抽象描述,定义了整个HBase表的结构。主要用于在创建表的时候决定表的schema.addFamily(HColumnDescriptor)添加列族,removeFamily(HColumnDescriptor)移除列族.方法中传递参数为byte[]型.setName/getName设置与返回表名字,getFamilies()获取列族名字构成的一个set.返回类型为Set byte[].创建的时候使用new 调用构造函数创建，不过方法被舍弃了</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.HBaseAdmin</td>
<td align="left">用于管理HBase中表的信息,包括表的创建createTable(HTableDescriptor),表的是能disableTable/enableTable(tablename)以及表的删除deleteTable(String tablename)或者是表的扩展，为表项添加新的列族等</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.HTable</td>
<td align="left">客户端内存中用于与HBase表进行通信，CRUD操作是在HTable上进行的本身具有缓冲区，不适合于在多线程的应用场景中，多线程应用场景可能导致缓冲区溢出，所以多线程情况下使用HBasePool，即是HBase的链接池。常用方法:close()刷新缓冲区，将缓冲区内容刷入HBase数据库中.HTable中没有提供update这样的方法，本来HBase数据库单元中存储数据就有多个版本，没有比较使用update函数，本身任何单元数据的改变都会被记录，而且针对于NoSql，update的意义本来就不大。delete(Delete
 item)删除内容，get(Get get)查询单元格内容，put(Put put)插入内容。exists(Get get)内容是否存在。getScanner(**)更广的一个查询方式，返回的是ResultScanner。get这样的API需要指定查询的行号，所以查询范围更加的限定。getTableDescriptor()返回HTableDescriptor。创建时用new 构造函数创建，不过还是被舍弃了</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.HColumnDescriptor</td>
<td align="left">列族的描述,类比于HTableDescriptor.构建关于列的描述,常用方法是getName获取列族名称，setValue以及getValue</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.Put</td>
<td align="left">执行单元格数据的添加操作，主要是指定列族与列限定符号，将数据封装如Put里面进行数据库插入，主要是使用Put中的add方法添加限定的查询信息</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.Get</td>
<td align="left">与Put类似，Get用于获取Hbase数据库中的单行的信息.主要方法是addColumn()添加列族以及列限定符号的信息，用于查询</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.Result</td>
<td align="left">返回Get或者Scan操作后的结果，存储为键值对</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.Scan</td>
<td align="left">用于限定要查找的数据，比Get使用更加灵活，可以限定查询行号的范围，而Get不具备这种功能</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.ResultScanner</td>
<td align="left">本质上为一个迭代器，调用next返回下一个Result对象，表示的是一系列的查询结果</td>
</tr><tr><td align="left">org.apache.hadoop.client.Connection</td>
<td align="left">链接对象，使用ConnectionFactory.createConnection()创建，用于构建操作数据库的HBaseAdmin</td>
</tr><tr><td align="left">org.apache.hadoop.client.ConnectionFactory</td>
<td align="left">构建Connection对象，表征与数据库的链接，后续用于创建操作数据库的HBaseAdmin</td>
</tr><tr><td align="left">org.apache.hadoop.habse.client.Cell</td>
<td align="left">HBase返回的Result中的数据单元，通过rawCells返回数据单元格的一个list</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.client.CellUtil</td>
<td align="left">数据库单元操作工具,主要用于拷贝单元数据</td>
</tr></tbody></table><p>综上所述。Hbase的API调用比HDFS的多。基本的类型是HBaseConfiguration用于构建配置文件的实例，与表的Schema相关的为HTableDescriptor以及HColumnDescriptor还有HBaseAdmin。构建Connection是每一步必须有的操作。</p>
<pre class="prettyprint"><code class="language-Java hljs avrasm has-numbering">Connection con = ConnectionFactory<span class="hljs-preprocessor">.createConnection</span>(configuration)<span class="hljs-comment">;</span>
HBaseAdmin admin = (HBaseAdmin)con<span class="hljs-preprocessor">.getAdmin</span>()<span class="hljs-comment">;</span>
HTable table = (HTable)con<span class="hljs-preprocessor">.getTable</span>(TableName<span class="hljs-preprocessor">.valueOf</span>(name))<span class="hljs-comment">;</span></code></pre><ul class="pre-numbering"><li>1</li><li>2</li><li>3</li></ul><p>后续的数据库的增删改查操作可以在获得的table对象上进行。</p>
<h2 id="mapreduce-java-apiversion-273"><a name="t2"></a>MAPREDUCE JAVA API(version 2.7.3)</h2>
<p>MR 的JAVA API</p>
<table><thead><tr><th align="left">Name</th>
<th align="left">Description</th>
</tr></thead><tbody><tr><td align="left">org.apache.hadoop.mapreduce.Mapper</td>
<td align="left">Mapper类，编写hadoop mr程序的Map过程时候需要继承这一个类实现其中的map方法。</td>
</tr><tr><td align="left">org.apache.hadoop.mapreduce.Reducer</td>
<td align="left">Reducer类，编写Hadoop mr程序的Reduce过程时候需继承的一个类，需要实现其中的reduce方法</td>
</tr><tr><td align="left">org.apache.hadoop.mapreduce.Mapper.Context</td>
<td align="left">用于将Map产生的结果当中间值输出,一般是输出key/value值</td>
</tr><tr><td align="left">org.apache.hadoop.mapreduce.Reducer.Context</td>
<td align="left">同Mapper.Context。将Reducer处理的结果进行输出</td>
</tr><tr><td align="left">org.apache.hadoop.io.Text</td>
<td align="left">可以理解为Hadoop中专门处理字符串的类型</td>
</tr><tr><td align="left">org.apache.hadoop.io.IntWritable</td>
<td align="left">可以理解为hadoop中专门处理整数的类型</td>
</tr><tr><td align="left">org.apache.hadoop.mapreduce.Job</td>
<td align="left">这个类也是Hadoop mr中非常重要的类型。用于定义任务以及提交任务到Hadoop mr集群处理。setJarClass()设置main函数所在的类，程序入口。setInputFormatClass()设置输入类型的类，setOutputFormatClass()设置输出类型的类。setMapOutputKeyClass()设置Map处理输出的键类型，setMapOutputValueClass()设置Map处理输出的值类型，setOutputKeyClass()设置reduce输出的key类型，setOutputValueClass()设置reduce输出的value类型。waitForCompletion()提交任务运行。</td>
</tr><tr><td align="left">org.apache.hadoop.hbase.mapreduce.TableReducer</td>
<td align="left">故名思意，当MR 的reduce处理结果需要放置在HBase中的时候，那么Reduce应该从这个类型继承</td>
</tr></tbody></table><p>核心是编写继承的Mapper类与Reducer类，以及Job实例完成类型设置，提交任务运行。</p>
            </div>
                </div>
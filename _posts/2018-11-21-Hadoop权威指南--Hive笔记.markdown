---
layout:     post
title:      Hadoop权威指南--Hive笔记
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>关于Hive</strong></span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">Hive是一个构建在Hadoop上的</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>数据仓库</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">框架</span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">SQL语句不适合开发复杂的机器学习算法</span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive把SQL查询转换为一系列在Hadoop集群上运行的MapReduce作业</strong></span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">Hive把数据组织为表，通过这种方式为存储在hdfs的数据赋予结构，元数据（如表模式）存储在metastore数据库中</span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">Hive能和哪些版本的Hadoop共同工作：</span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">Hive支持最新帆布的稳定版本以及之前的老版本，只要确保hadoop可执行文件在相应的路径下或设置HADOOP_HOME环境变量，就不必另行告诉Hive正在使用的哪个版本的hadoop</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive的外壳环境</strong></span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive的外壳环境是我们与Hive交互，发出HiveQL命令的主要方式</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>命令必须以分号结束</strong></span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">HiveQL一般是</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>大小写不敏感的</strong></span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive可以使用-f选项运行在指定文件中的命令</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，比如：hive -f script.q,duiyu；</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>对于较短的脚本，可用-e选项在行内嵌入命令，此时不需要表示结束的分号</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，如：hive
 -e 'select * from dumy'</span></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">hive shell有用的特性：</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>使用dfs命令来访问Hadoop文件系统，使用！前缀来运行宿主操作系统的命令</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="text-indent:28px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;"><strong>在Hive的仓库目录中，表存储为目录，仓库的目录由选项hive.metastore.warehouse.dir指定，默认值为usr/hive/warehouse/records</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>配置Hive</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">Hive使用XML配置文件进行设置，配置文件为hive-site.xml，也可以使用如下方法：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>传递到--config选项参数给hive命令</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，可以通过这种方式</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>重新定义Hive查找hive-site.xml文件</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，如：hive
 --config /user/tom/dev/conf，这个选项指定的是包含配置文件的目录</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>设置HIVE_CONF_DIR环境变量来指定配置文件的目录</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">这对于有（对应于多个集群）多个站点文件时很有用</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">Hive允许向hive命令传递-hiveconf选项来为单个会话设置属性，还可以在一个会话中</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>使用SET命令更改设置，这对于为某个特定的查询修改Hive或MapReduce作业设置非常有用</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>不带参数的SET命令会列出Hive所设置的所有属性及其取值（不包含hadoop属性），使用SET
 -v可以列出系统中所有属性</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>设置属性有个优先级层次：</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive SET &gt; -hiveconf &gt;hive-site.xml &gt; hive-default-xml &gt; hadoop-site.xml(等价于core-site.xml、hdfs-site.xml与mapred-site.xml)</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>日志记录</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>可以在/tmp/$USER/hive.log找到Hive的错误日志</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>日志的配置放在conf/hive-log4j.properties中，或者使用命令：hive -hiveconf hive.root.logger=DEBUG,console</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive服务</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>可以在运行时使用--service选项指明要使用哪种服务</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">一些有用的服务：</span></div>
<ul><li style="text-align:left;line-height:1.75;font-size:16px;list-style-position:inside;list-style-type:disc;font-family:SimSun, STSong;">
cli Hive的命令窗口（shell环境）</li><li style="text-align:left;line-height:1.75;font-size:16px;list-style-position:inside;list-style-type:disc;font-family:SimSun, STSong;">
hiveserver 让Hive以提供Thrift服务的服务器形式运行，允许用不同的语言编写的客户端进行访问</li><li style="text-align:left;line-height:1.75;font-size:16px;list-style-position:inside;list-style-type:disc;font-family:SimSun, STSong;">
hwi Hive的web接口（<span style="font-weight:bold;">待测试</span>）</li><li style="text-align:left;line-height:1.75;font-size:16px;list-style-position:inside;list-style-type:disc;font-family:SimSun, STSong;">
jar 与hadoop jar等价，这只运行类路径中同时包含hadoop和hive类java程序的简便方法</li><li style="text-align:left;line-height:1.75;font-size:16px;list-style-position:inside;list-style-type:disc;font-family:SimSun, STSong;">
metastore 让metastore作为一个单独的进程运行</li></ul><div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Metastore</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>metastore是Hive元数据的存放地</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，包括两部分：</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>服务和后台数据的存储</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>默认情况下，metastore服务和Hive服务运行在一个JVM中</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，它包含一个内嵌的以本地磁盘作为存储的Derby数据库实例</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>使用内嵌的metastore，一次只能为每个metastore打开一个服务，如果要支持多会话需要使用一个独立的数据库</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，如mysql</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive三种模型</strong></span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）local模式</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">连接到In-memory的数据库Derby，一般用于Unit Test</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2）单用户模式</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">通过网络连接到一个数据库中，是最常用的模式</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">3）远程服务器模式</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">在服务端启动MetaStoreServer，客户端利用Thrift协议通过MetaStoreServer访问元数据库</span></div>
<div><img src="http://note.youdao.com/yws/res/2426/WEBRESOURCE668ce149b840cf10186f44cb64bb1b09" alt="" style="width:593.393px;"></div>
<div style="line-height:1.875;font-size:14px;"><br></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive和传统数据相比</strong></span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">写时模式：数据在写入时对照模式进行检查</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">读时模式：</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>对数据的验证并不在加载数据时验证，而在查询时验证</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive被设计为用MapReduce操作hdfs数据，全表扫描是常态操作，而表更新则是通过把数据变换后放入新表实现的，hive不支持数据更新，但是支持insert into</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive的索引分为两类：紧凑（compact）索引和位图（bitmap）索引</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>紧凑索引存储每个值的hdfs块号</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，因此存储不会占用很多空间，且值被聚集存储于相近行的情况，索引仍然有效</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>位图索引使用压缩的位集合来高效存储具有某个特征值的行</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，这种索引适用于具有较少可能值的列</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>HiveQL</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>sql与HiveQl比较</strong></span></div>
<div style="overflow:auto;">
<table style="border-collapse:collapse;table-layout:fixed;width:0px;"><colgroup><col style="width:112px;"><col style="width:316px;"><col style="width:411px;"></colgroup><tbody><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">特性</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">sql</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">HiveQL</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">更新</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">insert update delete</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">insert</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">事务</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持（表级和分区级）</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">索引</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">延迟</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">亚秒级</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">分钟级</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">数据类型</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">整数、浮点数、定点数、文本和二进制串，时间</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">整数，浮点数，布尔型，文本和二进制串，时间戳，数组，map，struct</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">函数</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">多表插入</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">不支持</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">create table as select *</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">有些支持</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">支持</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">选择</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">SQL-92</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">FRom子句中只能有一个表或视图</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">连接</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">SQL-92</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">内连接，外链接，半连接，映射连接</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">子查询</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">任何子句</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">只能在FROM子句中</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">视图</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">可更新</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">只读</span></div>
</td>
</tr><tr><td colspan="1" rowspan="2" style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">扩展点</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">用户定义函数</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">用户定义函数</span></div>
</td>
</tr><tr><td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">存储过程</span></div>
</td>
<td style="font-size:14px;color:rgb(57,57,57);border:1px solid rgb(167,167,167);overflow:hidden;">
<div class="table-cell-line"><span style="font-family:'Microsoft YaHei', STXihei;color:rgb(0,0,0);background-color:transparent;">MapReduce脚本</span></div>
</td>
</tr></tbody></table></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">数据类型：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">简单类型：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>tinyint，smallint，int，bigint</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>float，double</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>boolean，string，binary，timestamp</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">复杂类型：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>array、map、struct</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">Hive的内置函数：数学与统计函数，字符串函数，日期函数，条件函数，聚集函数，处理XML和JSON函数</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>查询函数列表命令：show functions</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">要</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>了解某个特定函数的使用帮助，可以使用describe命令</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">：</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>describe
 function lengt</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">h;</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>类型转换：Hive进行隐式类型转换，不会进行反向转换，除非用cast（）操作</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>隐式类型转换规则</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>所有整形可以转换为范围更广的类型</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>所有整数类型，float和string类型都能隐式转换double类型；</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">3）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>tinyint，smallint，int都可以转换为float类型</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">4）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Boolean类型不能转换为任何类型，timestamp可以隐式转换为string类型</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>表（托管表和外部表）</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive的表在逻辑上由存储的数据和元数据组成</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>数据存储在hdfs上，元数据放在关系型数据库中</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>加载操作就是文件的移动或文件的重命名</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，因此他的速度很快，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive不检查表目录中的文件是否符合表所声明的模式，我们通常通过查询为缺失字段返回的空值null才知道存在不匹配的行</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>外部表使用关键字external声明</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive不会将文件移到自己得仓库目录</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>在定义时也不检查这个外部表的位置是否存在</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">如何选择使用哪种表：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>如果所有的处理都由Hive完成，应该使用托管表</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">；</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>如果要用hive和其他工具来共同处理同一个数据集，则应该使用外部表</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">普通的用法：</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>把存储在hdfs（由其他进程创建）的初始数据集用作外部表进行使用，然后用hive的变换功能把数据移到托管的hive表</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>分区（partition）</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">Hive把表组织成分区，是一种</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>根据分区列的值对表进行粗略划分的机制，使用分区可以加快数据分片的查询速度</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">使用分区并不会影响大范围查询的执行：我们仍然可以查询跨多个分区的整个数据集</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>分区是在创建表的时候使用partitioned by子句定义的，partitioned by子句中定义的列是表中正式的列，成为分区列，但是数据文件并不包含这些列的值，因为它们源于目录</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>加载数据到分区表时，要显示的指定分区值</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">在文件系统级别，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>分区只是表目录下嵌套的子目录</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">使用</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>show partitions tableName;命令可以查看tableName的分区</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>桶（bucket）</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">把表（分区）组织成桶的理由：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>获得更高的处理效率。桶为表加上了额外结构</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，具体而言，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>连接两个在相同列上划分了桶的表，可以使用map端连接（map-site
 join）高效实现</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2）使取样更高效</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>使用cluster by子句来指定划分桶所用的列和桶的个数</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">对于</span><span style="font-size:16px;font-family:SimSun, STSong;color:rgb(255,0,0);">map端连接连接，</span><span style="font-size:16px;font-family:SimSun, STSong;">首先</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>两个表以相同的方式划分桶</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，其次是</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>两个表桶的个数是倍数关系或相同</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">把Hive外生成的表加载到划分成桶的表中，则可以保证表中的数据已经划分成桶</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">要</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>想分成桶的表中填充数据，需要将hive.enforce.bucketing属性设置为true</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">物理上每个桶就是表（或分区）目录里的一个文件，事实上桶对应于MapReduce的输出文件分区：一个作业产生的桶和reduce任务个数相同</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>存储格式：</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">Hive从两个维度对表的存储进行管理，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>行格式和文件格式</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>行格式的定义使用SerDe定义，Serde是序列化和反序列化的工具</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">默认的存储格式：分隔的文本</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">默认的行内分隔符是ASCII集合中的Control-A，集合元素默认的分隔符是Control-B，默认的map分隔符是Control-C</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">如果不确认Hive使用哪个字符作为某个嵌套结构的分隔符，可以使用如下命令：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">create table nested as select array(array(1,2),array(3,4)) from dummy</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>二进制的存储格式</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">：顺序文件、Avro数据文件，RCFile</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hadoop的</strong></span><span style="font-size:16px;font-family:SimSun, STSong;color:rgb(255,0,0);"><strong>顺序文件</strong></span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>格式是一种针对顺序和记录（键值对）的通用二进制格式，使用stored
 as sequencefile</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>使用顺序文件的一个主要优点就是它们支持可分割的压缩</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，如果你有一系列在Hive外产生的序列文件，则无需额外设置，Hive能读取它们</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">如果你想</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>用压缩顺序文件来存储Hive产生的表</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，需要设置几个相应的属性来使用压缩</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">hive.exec.compress.output=true;</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">mapred.output.compress=true;</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">mared.output.compression.codec=org.apache.hadoop.io.compress.GzipCodec</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>RCFile表示按列记录文件</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>面向列的存储布局方式可以使一个查询跳过那些不必访问的列，对于那些只访问表中小部分行的查询比较有效</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">；</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>面向行的存储格式适合同时处理一行中很多列的情况</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">。如果空间足够，可以使用create
 table as select子句复制一个表，创建它的另一种存储格式，从而直观的比较负载两种存储格式在性能的差异</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>表的创建</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– CREATE [EXTERNAL] TABLE [IF NOT EXISTS] [db_name.]table_name
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [(col_name data_type [COMMENT col_comment], ...)]
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [COMMENT table_comment]
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [PARTITIONED BY (col_name data_type [COMMENT col_comment], ...)]
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [CLUSTERED BY (col_name, col_name, ...) [SORTED BY (col_name [ASC|DESC], ...)] INTO num_buckets BUCKETS]
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [SKEWED BY (col_name, col_name, ...) ON ([(col_value, col_value, ...), ...|col_value, col_value, ...]) (Note: only available starting
 with 0.10.0)] </span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [ [ROW FORMAT row_format] [STORED AS file_format]
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– | STORED BY 'storage.handler.class.name' [WITH SERDEPROPERTIES (...)] (Note: only available starting with 0.6.0) ]
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [LOCATION hdfs_path]
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [TBLPROPERTIES (property_name=property_value, ...)] (Note: only available starting with 0.6.0)
</span></div>
<div style="line-height:1.875;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">– [AS select_statement] (Note: this feature is only available starting with 0.5.0, and is not supported when creating external tables.)</span></div>
<div style="line-height:1.875;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">示例：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">create table stations (user string,nameint)</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">row format serde 'org.apache.hadoop.contrib.serde2.RegexSerde'</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">with serdeproperties( 'input.regex'='(\\d{6}) (\\d{5}) (.{29}) .*');</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">上面示例中用serde关键字和实现serde的java类完整类名来指定哪个SerDe，SerDe可以使用with SerdeProperties子句来设置额外的属性。</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>导入数据</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1。load data [local] inpath 'path' into table table_name
</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2.</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">单表插入：insert into(overwrite) table_name select * from table_name2</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">多表插入：from source
</span></div>
<div style="text-indent:84px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">insert into target_table_name1</span></div>
<div style="text-indent:84px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">select * group by ... </span>
</div>
<div style="text-indent:84px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">insert into target_table_name2</span></div>
<div style="text-indent:84px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">select * group by ... </span>
</div>
<div style="text-indent:84px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">... ...</span></div>
<div style="text-indent:84px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">insert into target_table_namen</span></div>
<div style="text-indent:84px;line-height:1.75;font-size:14px;">
<span style="font-size:16px;font-family:SimSun, STSong;">select * group by ... </span>
</div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">3.create table ... as select * from table_name</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>表的修改</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">重命名：alter table source rename to target</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">添加列：alter table target add columns （col string）</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">注：</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive不允许更新已有的记录，常用的做法是常见一个定义新列的新表，然后使用select语句把数据填充进去</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>表的删除：</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">drop table tablename；</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">如果要删除数据，保留表的定义（如mysql的delete或truncate），删除数据文件即可</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">如果该表为托管表，则它的数据和元数据都会删除；对于外部表而言，Hive不会碰数据，只删除元数据</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>MapReduce脚本：</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>使用transform、map和reduce子句可以在hive中调用外部脚本</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">示例：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）使用Python脚本</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">add file /root/is_good_quality.py</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">from record2 select transform(col1,col2) using 'is_good_quality.py' as year,temperture</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>连接：</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）内连接</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">QL示例：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">select sales.*,things.* from sales join things on (sales.id = things.id)</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive只支持等值连接，意味着连接谓词中只能使用等号，不能在where子句中指定连接条件</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">在Hive中可以在连接谓词中使用and关键字分隔一系列表达式连接多个列</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>单个连接使用一个MapReduce作业实现，如果多个连接的连接条件使用了相同的列，那么平均每个连接可以用少于一个MapReduce作业来实现，在查询前可以使用explain关键字来查看Hive将为某个查询使用多少个MapReduce作业</strong></span><span style="font-size:16px;font-family:SimSun, STSong;">，如果要查询更详细的信息，可以在查询前使用explain
 extend </span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2）外连接（左外连接、右外连接、全连接）</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">left outer join 和right outer join和full outer join子句实现左外连接、右外连接、全连接</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">3）半连接</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>Hive并不支持In子查询，但可以用left semi join来达到相同的效果</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">示例：select * from things left semi join sales on (sales.id=things.id)</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">等价于select * from things where things.id in(select id from sales)</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>写left semi join查询时必须遵循一个限制：右表（sales）只能在on子句中出现，不能在select中引用右表</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">4）map连接</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">如果有一个连接表小到足以放入内存，Hive就可以把较小的表放入每个mapper的内存来指定操作，如果要使用map连接，需要在sql中使用C语言风格的注释，以给出提示：select /* mapjoin(things) */ sales.*,things.* from
 sales join things on (sales.id=things.id)</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>子查询</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">Hive对子查询的支持很有限，它</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>只允许子查询出现在select语句中的FRom子句中</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">示例：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">select station,year, avg(max_temperture)</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">from (select station,year,max(temperture) max_temperture from records) mt group by station,year</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>视图</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">使用create view as select ...语句创建视图，</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">创建视图时并不执行查询，查询只是存储在metastore中，show tables命令的输出结果中包括视图，可以</span><span style="font-size:16px;font-family:SimSun, STSong;"><strong>使用describe
 extended view_name命令来查看某个视图的详细信息</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;"><strong>UDF：</strong></span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">Hive中有三类UDF：</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">1）（普通）UDF：作用于单个数据行，且产生一个数据行作为输出</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">2）UDAF接收多个输入数据行，并产生一个输出数据行（像count和max函数）</span></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-size:16px;font-family:SimSun, STSong;">3）UDTF用于单个输入数据行，且产生多个数据行</span></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><br></div>
<div style="line-height:1.75;font-size:14px;"><span style="font-weight:bold;">UDF实现和调用过程：
</span></div>
<div style="line-height:1.875;font-size:14px;">1.创建一个项目，编写一个Java类，继承org.apache.hadoop.hive.ql.exec.UDF类，类中实现evaluate</div>
<div style="line-height:1.875;font-size:14px;">2.将项目导出为jar包，上传到服务器</div>
<div style="line-height:1.875;font-size:14px;">3.添加jar包（在hive命令行里面执行）:</div>
<div style="line-height:1.875;font-size:14px;">add jar /root/NUDF.jar;</div>
<div style="line-height:1.875;font-size:14px;">4.创建临时函数:</div>
<div style="line-height:1.875;font-size:14px;">create temporary function getNation as 'cn.itcast.hive.udf.NationUDF';</div>
<div style="line-height:1.875;font-size:14px;">5.SQL语句中调用：select id, name, getNation(nation) from beauty;</div>
<div style="line-height:1.875;font-size:14px;"><span style="font-weight:bold;">UDAF 自定义集函数
</span></div>
<div style="line-height:1.875;font-size:14px;">1）函数类继承org.apache.hadoop.hive.ql.exec.UDAF</div>
<div style="line-height:1.875;font-size:14px;">2）org.apache.hadoop.hive.ql.exec.UDAFEvaluator(内部类 Evaluator</div>
<div style="line-height:1.875;font-size:14px;">实现 UDAFEvaluator 接口)
</div>
<div style="line-height:1.875;font-size:14px;">3）.Evaluator 需要实现 init、iterate、terminatePartial、merge、t</div>
<div style="line-height:1.875;font-size:14px;">erminate 这几个函数</div>
<ul><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;">
init():类似于构造函数，用于 UDAF 的初始化 </li><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;">
iterate():接收传入的参数，并进行内部的轮转，返回 boolean </li><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;">
terminatePartial():无参数，其为 iterate 函数轮转结束后，返回轮转数据，类似于 hadoop 的 Combiner</li><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;">
merge():接收 terminatePartial 的返回结果，进行数据 merge 操作，其返回类型为 boolean </li><li style="text-align:left;line-height:1.75;font-size:14px;list-style-position:inside;list-style-type:disc;font-family:'Microsoft YaHei', STXihei;">
terminate():返回最终的聚集函数结果 </li></ul>            </div>
                </div>
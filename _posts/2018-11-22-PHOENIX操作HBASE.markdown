---
layout:     post
title:      PHOENIX操作HBASE
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">HBase</span>，一个NoSQL数据库，可存储大量非关系型数据。</span></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
HBase，可以用HBase shell进行操作，也可以用HBase Java api进行操作。HBase虽然是一个数据库，但是它的查询语句，很不太好用。要是能像使用Mysql等关系型数据库一样用sql语句操作HBase，那就很Perfect了。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
现有工具有很多Hive，Tez，Impala，Shark/Spark，Phoenix等。今天主要记录Phoenix。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
phoenix，由saleforce.com开源的一个项目，后又捐给了Apache。它相当于一个Java中间件，帮助开发者，像使用jdbc访问关系型数据库一些，访问NoSql数据库HBase。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span id="more-111" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></span></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix</span></span>，操作的表及数据，存储在hbase上。phoenix只是需要和Hbase进行表关联起来。然后再用工具进行一些读或写操作。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
其实，可以把Phoenix只看成一种代替HBase的语法的一个工具。虽然可以用java可以用jdbc来连接phoenix，然后操作HBase，但是在生产环境中，不可以用在OLTP(在线事务处理)中。在线事务处理的环境中，需要低延迟，而Phoenix在查询HBase时，虽然做了一些优化，但延迟还是不小。所以依然是用在OLAP(联机分析处理)中，再将结果返回存储下来。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<a href="http://phoenix.apache.org/" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(232,71,71);text-decoration:none;">Phoenix官网</a>上，对Phoenix讲解已经很屌了。如果英语好，可以看官网，更正式一些。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix能干嘛</span>：官网很多描述，总结一句，就是方便操作了。其他的，没有多少优势。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix不能干嘛</span>：</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
①不支持事务处理</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
②不支持复杂的条件</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
③表之间的关联操作</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h1 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:3em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
Phoenix安装</h1>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
1、<a href="http://phoenix.apache.org/download.html" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(232,71,71);text-decoration:none;">下载phoenix</a>。</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix与HBase版本对应关系</span></p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
Phoenix 2.x – HBase 0.94.x</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
Phoenix 3.x – HBase 0.94.x</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
Phoenix 4.x – HBase 0.98.1+</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
我目前测试使用版本概况：</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
Hadoop1.0.4</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
HBase0.94.18</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
所以我可以用<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix2.x</span>，<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix3.x</span>。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
官网download页面有</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<img class="alignnone wp-image-502" src="http://www.weixuehao.com/wp-content/uploads/2014/11/1111111111111111111111.png" alt="1111111111111111111111" width="694" height="285" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
我选用的是<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix3.1.0</span>版本。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
2、上传到主节点linux就ok了，解压缩</h2>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
tar –zxvf phoenix.tar.gz</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
pwd</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
/root/phoenix</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
ll phoenix</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<img class="alignnone wp-image-503" src="http://www.weixuehao.com/wp-content/uploads/2014/11/201503111800.png" alt="201503111800" width="609" height="216" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
phoenix目录结构可能会有点不同，主要是bin目录的位置，可能在hadoop1下，也可能直接在 /root/phoenix下。没关系，都差不多。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
3、拷贝一些文件</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
既然用的hadoop1.x集群，那么我们使用phoenix目录下，hadoop1目录下的内容。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">①</span>将hadoop1下，phoenix-core-3.x.jar拷贝到hadoop集群各个节点HBase的lib目录下。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">②</span>重启一下HBase （<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">在这里可以看出，用phoenix需要重启hbase，所以选择hbase查询工具时，需要考虑一下</span>）</p>
<h6 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:1em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
</h6>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
4、验证是否安成功</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
在主节点上，切换到/root/phoenix/hadoop1/bin目录下</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
输入：</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
./sqlline.py master:2181</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<img class="alignnone wp-image-504" src="http://www.weixuehao.com/wp-content/uploads/2014/11/201503111803.png" alt="201503111803" width="705" height="169" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
如果出现这个画面，那就是成功了。如果不成功，可能是zookeeper配置的有一些问题吧。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
好吧，先退出此界面，输入<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">!quit</span>回车然后就可以退出了。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
这个phoenix挺有意思，有一些命令需要输入叹号的！</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h1 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:3em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
phoenix的使用</h1>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
mysql的话，可以CLI命令行的方式操作；可以通过用jdbc，在Java代码中访问；可以通过用SQLyog进行访问管理；</p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">1、phoenix</span>，怎么用呢？~可以看成是mysql。</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
①Phoenix可以在CLI下操作；</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
②可以用jdbc操作；</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
③可以用phoenix的一个客户端工具Squirrel 访问；</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
2、先说Squirrel吧，这个简单一些。</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"><u><a title="squirrelsql官网下载" href="http://www.squirrelsql.org/" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">Squirrel
 SQL Client</a></u></span></span></span>，是一个连接数据库的客户端工具。一般支持JDBC的数据库都可以用它来连接。（如Squirrel连接Mysql）</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
下载<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;"><a title="squirrelsql官网下载" href="http://www.squirrelsql.org/" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(232,71,71);text-decoration:none;">Squirrel
 SQL Client</a></span>，解压缩就可以了。运行</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
squirrel-sql.bat</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
就出现了图形界面。</p>
<h6 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:1em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
</h6>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
3、这肯定要说怎样连Phoenix？</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">①</span>在Squirrel安装目录的lib下，添加几个jar包</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
a,  phoenix-core-xxx.jar</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
b,  phoenix-3.0-client.jar</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
c,  hbase-0.94.18.jar</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
d,  hadoop1.0.4.jar</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
e,  hadoop-common-xxx.jar</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">②</span></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<a href="http://www.weixuehao.com/archives/111/attachment/20140905150155281" rel="nofollow" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(232,71,71);text-decoration:none;"><img class="alignnone wp-image-505" src="http://www.weixuehao.com/wp-content/uploads/2014/11/20140905150155281.png" alt="20140905150155281" width="773" height="477" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></a></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
需要点击“Drivers”，将phoenix的驱动添加进去。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">③</span>点击左上角 蓝色的 “ <span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">+</span> ”
 加号，添加</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<img class="alignnone wp-image-506 size-full" src="http://www.weixuehao.com/wp-content/uploads/2014/11/201503111810.png" alt="201503111810" width="502" height="480" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
按上面的顺序，依次填写。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">第一步，</span>Name：随便写个名字，标记连接；</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">第二步，</span>Example URL：相当于mysql的jdbc连接串，这里的alias写zookeeper的主机名称，端口号，可以写，可以不写，我一般不写；</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">第三步，</span>选择Phoenix-core的jar包；</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">第四步，</span>就是手动输入org.apache.phoenix.jdbc.PhoenixDriver。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
然后点击OK。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">④</span>配置连接</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<img class="alignnone wp-image-507" src="http://www.weixuehao.com/wp-content/uploads/2014/11/201503111812.png" alt="201503111812" width="519" height="440" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">Name</span>：为随便起的名称。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">Driver</span>：选中③中添加的phoenix驱动。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">URL</span>：写如上内容，jdbc:phoenix:node1,node2,master等这里主要是zookeeper主机名。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">User Name</span>：要连接的主机的用户名</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">Password</span>：要连接的主机的密码</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
点击Test可以进行测试，或点OK连接。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
连接完毕，启动后，就可以看到如下的效果了。这里我已经创建了几个表了，这些表都是存在于HBase上的。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<img class="alignnone size-full wp-image-510" src="http://www.weixuehao.com/wp-content/uploads/2014/11/QQ%E6%88%AA%E5%9B%BE20150311191101.png" alt="QQ截图20150311191101" width="1005" height="659" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
4、Squirrel的一些布局简介（看上面这个图）：</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
1，用squirrel建立的一些连接</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
2，当前连接下，所有对象，包括主见系统表，普通表，视图。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
3，为表，这些表都是实际存在于zookeeper所管理的HBase上的。右键此表，可以对表进行管理。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
4，为视图。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
5，编写sql脚本的地方，可以输入脚本执行。脚本执行方式，在5上面有一个小人，选中sql，点击小人就可以执行了。或者按ctrl + enter键，执行。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
6，为选中的对象的一些基本信息，列信息，行数等。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
7，为sql执行的一些状态。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
5、在Squirrel中建表或视图</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
在Squirrel中创建表的过程主要是编写sql，进行执行。sql该怎么写，需要看phoenix驱动都支持什么效果。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
这需要看phoenix的官网了。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
需要注意的是<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix是区分大小写</span></span>的；</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">自己定义的HBase中的 HTableName，ColumnFamily，以及字段Column，需要和Phoenix中保持一致。（最好都用大写）</span></p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">phoenix操作hbase，我们有两种方式，创建表，创建视图。</span></p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
这两种方式，有<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">区别。</span></span></p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">创建表的话</span>，可读可写，就可以对HBase进行插入，查询，删除操作。</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">视图的话</span>，是只读的，一般就只可以进行查询操作</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
虽然看起来，表的功能，比视图更强大一些。但是就像是mysql等关系型数据库一样，删除表操作，会将表删掉。但是删除视图操作，却不会影响原始表的结构。</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
因为使用phoenix，创建表后，会自动和hbase建立关联映射。当你使用phoenix删除和hbase之间的关系时，就会将hbase中的表也删掉了</p>
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
所以用视图，会对原始的HBase表影响小一些。</p>
</blockquote>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
</h2>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
6、用phoenix可以创建表</h2>
<h3 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:1.5em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
（1）若hbase中，不存在名为<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">htablename或htablename2的</span>HTable：</h3>
<div style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<div id="highlighter_509185" class="syntaxhighlighter php" style="border:0px;font-family:inherit;font-size:1em !important;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;background-color:#000000 !important;">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="php plain">create htablename(</code></div>
<div class="line number2 index1 alt1"><code class="php spaces">    </code><code class="php plain">pk VARCHAR primary key not null,</code></div>
<div class="line number3 index2 alt2"><code class="php spaces">    </code><code class="php plain">col1 VARCHAR null,</code></div>
<div class="line number4 index3 alt1"><code class="php spaces">    </code><code class="php plain">col2 VARCHAR null,</code></div>
<div class="line number5 index4 alt2"><code class="php spaces">    </code><code class="php plain">col3 VARCHAR null</code></div>
<div class="line number6 index5 alt1"><code class="php plain">)</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<div style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<div id="highlighter_558411" class="syntaxhighlighter php" style="border:0px;font-family:inherit;font-size:1em !important;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;background-color:#000000 !important;">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="php plain">create htablename2(</code></div>
<div class="line number2 index1 alt1"><code class="php spaces">    </code><code class="php plain">pk VARCHAR primary key null,</code></div>
<div class="line number3 index2 alt2"><code class="php spaces">    </code><code class="php string">"cf"</code><code class="php plain">.</code><code class="php string">"col1"</code>
<code class="php plain">VARCHAR null,</code></div>
<div class="line number4 index3 alt1"><code class="php spaces">    </code><code class="php string">"cf"</code><code class="php plain">.</code><code class="php string">"col2"</code>
<code class="php plain">VARCHAR null,</code></div>
<div class="line number5 index4 alt2"><code class="php spaces">    </code><code class="php string">"cf2"</code><code class="php plain">.</code><code class="php string">"col3"</code>
<code class="php plain">VARCHAR null,</code></div>
<div class="line number6 index5 alt1"><code class="php spaces">    </code><code class="php string">"cf2"</code><code class="php plain">.</code><code class="php string">"col4"</code>
<code class="php plain">VARCHAR null</code></div>
<div class="line number7 index6 alt2"><code class="php plain">)</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
在SQuirreL中执行上面的SQL脚本，执行过程中，如果出现错误，会在工具的下面进行提示。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
若成功后，就可以在HBase中看到这个表了，而且在SQuirrel中，看到此表。</p>
<h3 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:1.5em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
（2）若hbase存在htablename3，但SQuirrel中看不到时</h3>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
当安装好SQuirrel时，本以为连上hbase后，就可以在SQuirrel中看到hbase中的表了。但是并没有看到，也不会显示。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
如果要想看到hbase中已存在的表，还需要一些操作。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
也就是如果想要在SQuirrel中，看到htablename3，且想要对htablename3进行操作。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
也还是需要在SQuirreL中进行执行创建表的操作，执行完毕后，就会将HBase的htablename3，映射到SQuirreL中。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
这样我们就可以在Java api中进行操作了。否则是不可以的。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
那么需要哪些具体操作呢？其实很简单，我当时没想到</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">就像创建表一样，使用Create table就可以了。就这样简单。</span></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">不过</span>这个语句怎么写呢？怎样对应呢？</p>
<div style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<div id="highlighter_284001" class="syntaxhighlighter php" style="border:0px;font-family:inherit;font-size:1em !important;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;background-color:#000000 !important;">
<table border="0" cellpadding="0" cellspacing="0"><tbody><tr><td class="gutter">
<div class="line number1 index0 alt2">1</div>
<div class="line number2 index1 alt1">2</div>
<div class="line number3 index2 alt2">3</div>
<div class="line number4 index3 alt1">4</div>
<div class="line number5 index4 alt2">5</div>
<div class="line number6 index5 alt1">6</div>
<div class="line number7 index6 alt2">7</div>
<div class="line number8 index7 alt1">8</div>
<div class="line number9 index8 alt2">9</div>
<div class="line number10 index9 alt1">10</div>
<div class="line number11 index10 alt2">11</div>
<div class="line number12 index11 alt1">12</div>
<div class="line number13 index12 alt2">13</div>
<div class="line number14 index13 alt1">14</div>
</td>
<td class="code">
<div>
<div class="line number1 index0 alt2"><code class="php plain">create htablename3(</code></div>
<div class="line number2 index1 alt1"> </div>
<div class="line number3 index2 alt2"><code class="php spaces">    </code><code class="php plain">--这句话直接写就可以了，这样的话，HBase中的RowKey转换成phoenix中的主键，列名就叫 pk。</code></div>
<div class="line number4 index3 alt1"><code class="php spaces">    </code><code class="php plain">--rowkey自动会和primary key进行对应。</code></div>
<div class="line number5 index4 alt2"><code class="php spaces">    </code><code class="php plain">pk VARCHAR primary key null,</code></div>
<div class="line number6 index5 alt1"> </div>
<div class="line number7 index6 alt2"><code class="php spaces">    </code><code class="php plain">--将名为cf的columnFamily下，字段名为col1的字段，写在这里。</code></div>
<div class="line number8 index7 alt1"><code class="php spaces">    </code><code class="php string">"cf"</code><code class="php plain">.</code><code class="php string">"col1"</code>
<code class="php plain">VARCHAR null,</code></div>
<div class="line number9 index8 alt2"> </div>
<div class="line number10 index9 alt1"><code class="php spaces">    </code><code class="php plain">--将名为cf的columnFamily下，字段名为col2的字段，写在这里。。。下面就以此类推</code></div>
<div class="line number11 index10 alt2"><code class="php spaces">    </code><code class="php string">"cf"</code><code class="php plain">.</code><code class="php string">"col2"</code>
<code class="php plain">VARCHAR null,</code></div>
<div class="line number12 index11 alt1"><code class="php spaces">    </code><code class="php string">"cf2"</code><code class="php plain">.</code><code class="php string">"col3"</code>
<code class="php plain">VARCHAR null,</code></div>
<div class="line number13 index12 alt2"><code class="php spaces">    </code><code class="php string">"cf2"</code><code class="php plain">.</code><code class="php string">"col4"</code>
<code class="php plain">VARCHAR null</code></div>
<div class="line number14 index13 alt1"><code class="php plain">)</code></div>
</div>
</td>
</tr></tbody></table></div>
</div>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
然后在SQuirreL中执行，然后就可以看到数据了。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
不过此时，可能还会有问题，<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">乱码</span></span>。
 在SQuirrel中，主键以及一些包含汉字的字段，都是方块等乱码了。这个怎么解决？？？(<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">暂未解决</span></span>)</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h2 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:2.25em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
7、用phoenix可以创建视图</h2>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
假如，要对刚刚创建的表<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">htablenameX</span></span>创建视图操作</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
CREATE  VIEW  “htablenameX”   (pk VARCHAR primary key)   default_column_family = ‘FM’</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
创建完成后，这里的“htablenameX” 是HBase中table “htablenameX”的名称。然后定义一个主键，就可以了。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
可能还不太清楚，看下面</p>
<h3 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:1.5em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
（1）第一种视图，假如HBase中存在表”DAMAI”，需要以视图的方式，操作它，那么开始创建视图</h3>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
CREATE VIEW “DAMAI” ( PK VARCHAR PRIMARY KEY) DEFAULT_COLUMN_FAMILY=’FM’</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
这里双引号内的 “DAMAI” 和HBase中的表名是一样的，所以会自动关联。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
会自动对应表名，自动关联字段。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
这里需要注意：<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">创建视图时，要保证在SQuirrel工具中，没有相同名称的表存在。</span></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<img class="alignnone wp-image-509" src="http://www.weixuehao.com/wp-content/uploads/2014/11/201503111847.png" alt="201503111847" width="600" height="259" style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
那么，如果想针对HBase中的一个表，建多个视图呢？慢慢来</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h3 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:1.5em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
（2）第二种视图，可以在Phoenix table的基础上创建</h3>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
CREATE VIEW my_VIEW (new_col VARCHAR,new_col2 VARCHAR) AS SELECT * FROM <span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">phoenix_Table</span>WHERE
 ……</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
也就是，原来在phoenix中，已经创建了表了。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">例如</span>：htablename2。在htablename2上建立一个视图，叫”mingzisuibian”  (名字随便)</p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
CREATE VIEW mingzisuibian ( new_col VARCHAR, new_col2  VARCHAR )  AS SELECT * FROM<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">htablename2 </span>WHERE
 ….(针对htablename2的查询条件)</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<h3 style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:1.5em;line-height:1em;vertical-align:baseline;color:rgb(34,34,34);">
（3）第三种建视图的方式，是在视图之上建立视图</h3>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
CREATE VIEW my_VIEW_ON_VIEW AS SELECT * FROM <span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;color:rgb(255,0,0);">MY_VIEW</span> WHERE
 ……</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
在（1）中，我们创建了一个视图 “DAMAI”。这里面包括了全部的字段。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
所以说，在创建DAMAI视图的时候，可以将全部字段都包括进来。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
所以，我们可以在DAMAI这个视图之上，创建其它视图，这样视图的名称，就可以随便定了。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">例如：</span></p>
<blockquote style="border-width:0px 0px 0px 10px;border-left-style:solid;border-left-color:rgb(204,204,204);font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);background:rgb(245,245,245);">
<p style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;">
CREATE VIEW BIGBIG AS SELECT * FROM DAMAI WHERE ……</p>
</blockquote>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
不断往hbase中，添加数据，随着数据的增长，在phoenix建的视图中也可以看到的数据的条数，在同步增加。</p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;color:rgb(17,17,17);">
 </p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">综上所述，从上面可以看出，为了避免影响hbase的表信息，最好是创建一个视图。然后在视图的基础上，再创建其它视图。</span></span></p>
<p style="border:0px;font-family:'Droid Sans', Arial, Tahoma;font-size:16.0016002655029px;line-height:25.602560043335px;vertical-align:baseline;">
<span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;font-weight:inherit;line-height:inherit;vertical-align:baseline;"><span style="border:0px;font-family:inherit;font-style:inherit;font-variant:inherit;line-height:inherit;vertical-align:baseline;">也就是，先执行视图里面的（1），再执行视图里面的（3）。</span></span></p>
            </div>
                </div>
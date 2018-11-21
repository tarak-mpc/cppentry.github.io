---
layout:     post
title:      Hadoop HBase入门学习
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/lan12334321234/article/details/70049914				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                <span style="font-size:x-large;"><strong>HBase是什么？ </strong></span>
<br>HBase是Apache Hadoop中的一个子项目，<span style="color:#FF0000;"><strong>Hbase依托于Hadoop的HDFS作为最基本存储基础单元</strong></span>，通过使用hadoop的DFS工具就可以看到这些这些数据 存储文件夹的结构,还可以通过Map/Reduce的框架(算法)对HBase进行操作。
<br>HBase在产品中还包含了Jetty，<span style="color:#FF0000;"><strong>在HBase启动时采用嵌入式的方式来启动Jetty</strong></span>，因此<span style="color:#FF0000;"><strong>可以通过web界面对HBase进行管理和查看当前运行的一些状态</strong></span>，非常轻巧。
<br><br><img src="http://dl2.iteye.com/upload/attachment/0123/8373/43b2563b-aacc-3f32-aee9-f3cf7b27036d.png" alt="43b2563b-aacc-3f32-aee9-f3cf7b27036d.png"><br><br><span style="font-size:x-large;"><strong>为什么采用HBase？</strong></span>
<br>HBase不同于一般的关系数据库,<span style="color:#FF0000;"><strong>它是一个适合于非结构化数据存储的数据库。</strong></span>所谓非结构化数据存储就是说<span style="color:#0000FF;"><strong>HBase是基于列的而不是基于行的模式</strong></span>，这样方便读写你的大数据内容。
<br><br><span style="font-size:large;"><span style="color:#0000FF;"><strong>HBase是面向列的数据库</strong></span></span>
<br><br><span style="font-size:medium;"><span style="color:#FF0000;"><strong>HBase是介于Map Entry(key &amp; value)和DB Row之间的一种数据存储方式。</strong></span></span>就点有点类似于现在流行的Memcache，但不仅仅是简单的一个key对应一个 value，你很可能需要存储多个属性的数据结构，但没有传统数据库表中那么多的关联关系，这就是<span style="color:#0000FF;"><strong>所谓的松散数据</strong></span>。
<br><br>简单来说，你在HBase中的表创建的可以看做是一张很大的表，而这个表的属性可以根据需求去动态增加，<span style="color:#FF0000;"><strong>在HBase中没有表与表之间关联查询。</strong></span>你只需要 告诉你的数据存储到Hbase的那个column families 就可以了，<span style="color:#FF0000;"><strong>不需要指定它的具体类型：char,varchar,int,tinyint,text等等。</strong></span>但是你<span style="font-size:medium;"><strong>需要注意HBase中不包含事务此类的功能。</strong></span>
<br><br>Apache HBase 和Google Bigtable 有非常相似的地方，一个数据行拥有一个可选择的键和任意数量的列。<span style="color:#FF0000;"><strong>表是疏松的存储的，因此用户可以给行定义各种不同的列</strong></span>，对于这样的功能在大项目中非常实用，可以简化设计和升级的成本。
<br><br><span style="font-size:x-large;"><strong>如何运行HBase？</strong></span>
<br>从 Apache的HBase的镜像网站上下载一个稳定版本的HBase http://mirrors.devlib.org/apache/hbase/stable/hbase-0.20.6.tar.gz， 下载完成后，对其进行解压缩。确定你的机器中已经正确的安装了Java SDK、SSH，否则将无法正常运行。
<br><br>进入此目录
<br><pre class="command">$ cd /work/hbase</pre>
<br><br>编辑 conf/hbase-env.sh 文件,将JAVA_HOME修改为你的JDK安装目录
<br><pre class="command">$ vim conf/hbase-env.sh
export JAVA_HOME=/JDK_PATH</pre>
<br><br>输入你的所有HBase服务器名,localhost,或者是ip地址
<br><pre class="command">$ vim conf/regionservers</pre>
<br><br>启动hbase, 中间需要你输入两次密码，也可以进行设置不需要输入密码，启动成功。
<br><pre class="command">$ bin/start-hbase.sh</pre>
<br><br>启动hbase REST服务后就可以通过对uri: http://localhost:60050/api/ 的<span style="color:#0000FF;"><strong>通用REST操作(GET/POST/PUT/DELETE)实现对hbase的REST形式数据操作.</strong></span>
<br><pre class="command">$ bin/hbase rest start</pre>
<br><br>也可以输入以下指令进入HQL指令模式
<br><pre class="command">$ bin/hbase shell</pre>
<br><br>关闭HBase服务
<br><pre class="command">$ bin/stop-hbase.sh</pre>
<br><br>

  
  <div class="attachments">
    
      
        <ul style="display:none;"><li><a href="http://dl2.iteye.com/upload/attachment/0123/8373/43b2563b-aacc-3f32-aee9-f3cf7b27036d.png" rel="nofollow"><img src="http://dl2.iteye.com/upload/attachment/0123/8373/43b2563b-aacc-3f32-aee9-f3cf7b27036d-thumb.png" class="magplus" title="点击查看原始大小图片" alt="43b2563b-aacc-3f32-aee9-f3cf7b27036d-thumb.png"></a></li>
          
          <li>大小: 95.2 KB</li>
        </ul><ul><li><a href="#" rel="nofollow">查看图片附件</a></li>
      </ul></div>            </div>
                </div>
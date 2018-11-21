---
layout:     post
title:      完全分布式Hadoop集群安装Phoenix
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                原文地址为：<a href="http://www.itdaan.com/blog/2017/11/29/eadd5e8b0414460c4d09707599a8ec19.html" rel="nofollow">完全分布式Hadoop集群安装Phoenix</a><br><br><div class="markdown_views">
 <h1 id="应用场景">应用场景</h1>
 <blockquote> 
  <p>当我们按照之前的博文，安装完Hadoop分布式集群之后，再安装了Hbase，当准备通过hbase shell命令开始使用Hbase的时候，发现hbase非常的难用，都是一些scan，status，describe命令等，无法像mysql，oracle，hive等通过一些简单的SQL语句来操作数据，但是通过Phoenix，它可以让Hbase可以通过SQL语句来进行操作。并且Phoenix只针对Hbase，所以它的效率比起Impala，HQL有过之而无不及！</p>
 </blockquote>
 <h1 id="操作步骤">操作步骤</h1>
 <h3 id="1-phoenix介绍">1. Phoenix介绍</h3>
 <blockquote> 
  <p><font color="#FF0000">可以把Phoenix理解为Hbase的查询引擎，phoenix，由saleforce.com开源的一个项目，后又捐给了Apache。它相当于一个Java中间件，帮助开发者，像使用jdbc访问关系型数据库一些，访问NoSql数据库HBase。</font></p> 
  <p>phoenix，操作的表及数据，存储在hbase上。phoenix只是需要和Hbase进行表关联起来。然后再用工具进行一些读或写操作。</p> 
  <p><font color="#FF0000"> 其实，可以把Phoenix只看成一种代替HBase的语法的一个工具。虽然可以用java可以用jdbc来连接phoenix，然后操作HBase，但是在生产环境中，不可以用在OLTP中。在线事务处理的环境中，需要低延迟，而Phoenix在查询HBase时，虽然做了一些优化，但延迟还是不小。所以依然是用在OLAT中，再将结果返回存储下来。</font></p>
 </blockquote>
 <h3 id="2-phoenix安装包下载">2. Phoenix安装包下载</h3>
 <blockquote> 
  <p><a href="https://pan.baidu.com/s/1nuLOFqh" rel="nofollow">phoenix安装包下载地址</a>，下载后上传到主节点的opt目录下！</p>
 </blockquote>
 <p><font color="#FF0000"><strong>注：phoenix安装包的下载非常讲究，如果和hbase的版本不匹配，那么可能导致hbase也会失败，导致HRegionServer开启后，1分钟之内自动关闭，很麻烦！上面的安装包针对hbase1.2.X，亲测好用！</strong></font></p>
 <blockquote> 
  <p><a href="http://archive.apache.org/dist/phoenix/" rel="nofollow">phoenix的其他版本下载地址</a></p>
 </blockquote>
 <h3 id="3-phoenix安装包解压缩更换目录">3. phoenix安装包解压缩更换目录</h3>
 <pre class="prettyprint"><code class="hljs bash"> <span class="hljs-comment"># cd /opt</span><br><span class="hljs-comment"># tar -xzvf apache-phoenix-4.10.0-HBase-1.2-bin.tar.gz</span><br><span class="hljs-comment"># mv apache-phoenix-4.10.0-HBase-1.2-bin phoenix4.10.0</span><br><br><span class="hljs-comment"># chmod 777 -R /opt/phoenix4.10.0 #给phoenix目录授权</span></code></pre>
 <h3 id="4-修改配置文件">4. 修改配置文件</h3>
 <pre class="prettyprint"><code class="hljs bash"> <span class="hljs-comment"># vim /etc/profile</span><br><br><span class="hljs-keyword">export</span> PHOENIX_HOME=/opt/phoenix4.<span class="hljs-number">10.0</span>      <span class="hljs-comment">#在最后两行加上如下phoenix配置</span><br><span class="hljs-keyword">export</span> PATH=<span class="hljs-variable">$PATH</span>:<span class="hljs-variable">$PHOENIX_HOME</span>/bin</code></pre>
 <pre class="prettyprint"><code class="hljs bash"> <span class="hljs-comment"># source /etc/profile #使环境变量配置生效</span></code></pre>
 <h3 id="5-将主节点的phoenix包传到从节点">5. 将主节点的phoenix包传到从节点</h3>
 <pre class="prettyprint"><code class="hljs ruby"> <span class="hljs-comment"># scp -r phoenix4.10.0 root<span class="hljs-yardoctag">@hadoop</span>1:/opt/</span><br><span class="hljs-comment"># scp -r phoenix4.10.0 root<span class="hljs-yardoctag">@hadoop</span>2:/opt/</span></code></pre>
 <blockquote> 
  <p><font color="#FF0000"><strong>并且在从节点上将phoenix目录进行授权，添加环境变量！</strong></font></p>
 </blockquote>
 <h3 id="6-将hbase-sitexml配置文件拷贝到phoenix的bin目录下主从节点都需要">6. 将hbase-site.xml配置文件拷贝到phoenix的bin目录下【主从节点都需要】</h3>
 <pre class="prettyprint"><code class="hljs bash"> <span class="hljs-comment"># cp /opt/hbase1.2.6/conf/hbase-site.xml /opt/phoenix4.10.0/bin/</span></code></pre>
 <h3 id="7-将phoenix安装包下的包放到hbase的lib目录下主从节点都需要">7. 将phoenix安装包下的包放到hbase的lib目录下【主从节点都需要】</h3>
 <blockquote> 
  <p><font color="#FF0000"><strong>将如下两个jar包，目录在/opt/phoenix4.10.0/下，拷贝到hbase的lib目录，目录在/opt/hbase1.2.6/lib/</strong></font></p>
 </blockquote>
 <pre class="prettyprint"><code class="hljs lasso"> phoenix<span class="hljs-subst">-</span><span class="hljs-number">4.10</span><span class="hljs-number">.0</span><span class="hljs-attribute">-HBase</span><span class="hljs-subst">-</span><span class="hljs-number">1.2</span><span class="hljs-attribute">-server</span><span class="hljs-built_in">.</span>jar<br> phoenix<span class="hljs-attribute">-core</span><span class="hljs-subst">-</span><span class="hljs-number">4.10</span><span class="hljs-number">.0</span><span class="hljs-attribute">-HBase</span><span class="hljs-subst">-</span><span class="hljs-number">1.2</span><span class="hljs-built_in">.</span>jar</code></pre>
 <blockquote> 
  <p><font color="#FF0000"><strong>注：全部配置完后需要重启Hbase！</strong></font></p>
 </blockquote>
 <h3 id="8-启动phoenix">8. 启动phoenix</h3>
 <pre class="prettyprint"><code class="hljs bash"> <span class="hljs-comment"># sqlline.py hadoop0,hadoop1,hadoop2:2181</span></code></pre>
 <p><img src="https://img-blog.csdn.net/20171129115024802?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvYmluZ294dWJpbg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="这里写图片描述" title=""></p>
 <pre class="prettyprint"><code class="hljs bash"> &gt; !tables                 <span class="hljs-comment">#测试命令</span></code></pre>
</div> 
<br>转载请注明本文地址：<a href="http://www.javaxxz.com/thread-366266-1-1.html" rel="nofollow">完全分布式Hadoop集群安装Phoenix</a><br>            </div>
                </div>
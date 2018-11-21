---
layout:     post
title:      Hadoop之——重新格式化HDFS的方案
---
<div id="article_content" class="article_content clearfix csdn-tracking-statistics" data-pid="blog" data-mod="popu_307" data-dsm="post">
								<div class="article-copyright">
					版权声明：本文为博主原创文章，未经博主允许不得转载。					https://blog.csdn.net/l1028386804/article/details/72857449				</div>
								            <link rel="stylesheet" href="https://csdnimg.cn/release/phoenix/template/css/ck_htmledit_views-f76675cdea.css">
						<div class="htmledit_views" id="content_views">
                
<p><a href="http://blog.csdn.net/l1028386804/article/details/72857449" rel="nofollow">转载请注明出处：http://blog.csdn.net/l1028386804/article/details/72857449</a><br></p>
<p>了解Hadoop的同学都知道，Hadoop有两个核心的组成部分，一个是HDFS，另一个则是MapReduce，HDFS作为Hadoop的数据存储方案，MapReduce则提供计算服务；同时，HDFS作为一种分布式文件系统，它的安装也是需要相应的格式化操作的，如果安装失败或者我们需要重新安装的时候，那我们就需要对HDFS重新进行格式化，这篇文章就和大家一起讨论下如何进行HDFS的重新格式化。<br>
重新格式化hdfs系统的方法：<br></p>
<h3>1、打开hdfs-site.xml<br></h3>
<p>我们打开Hadoop的hdfs-site.xml，基本配置如下所示：<br></p>
<p></p>
<pre><code class="language-html">&lt;property&gt;
   &lt;name&gt;dfs.name.dir&lt;/name&gt;
   &lt;value&gt;/usr/local/hadoop/hdfs/name&lt;/value&gt;
&lt;/property&gt;
&lt;property&gt;
   &lt;name&gt;dfs.data.dir&lt;/name&gt;
   &lt;value&gt;/usr/local/hadoop/hdsf/data&lt;/value&gt;
&lt;/property&gt;</code></pre> 将 dfs.name.dir所指定的目录删除、dfs.data.dir所指定的目录删除，及我们需要删除hdfs-site.xml中配置的存储信息路径。<br><p></p>
<h3> 2、删除core-site.xml配置文件指定的相关目录</h3>
<p></p>
<pre><code class="language-html"> &lt;property&gt;
 	&lt;name&gt;hadoop.tmp.dir&lt;/name&gt;
 	&lt;value&gt;/usr/local/hadoop/hadooptmp&lt;/value&gt;
 &lt;/property&gt;</code></pre> 将hadoop.tmp.dir所指定的目录删除，即删除Hadoop的临时文件路径。<br><p></p>
<h3>3、重新执行命令：hadoop namenode -format</h3>
<p>格式化完毕。<br>
这种格式化HDFS的方式是需要把原来HDFS中的数据全部清空，然后再格式化并安装一个全新的HDFS。<br>
注：这种格式化方式需要将HDFS中的数据全部清空，以后我们探讨不需删除数据即可重新格式化的方法。<br><br></p>
            </div>
                </div>